---
layout: post
title:  "XV6 Scheduler Assignment Implementation 2"
date:   2020-12-18 09:00:00
categories: EEE
permalink: /archivers/xv6-scheduler-assign-impl-2
nocomments: false
use_math: true 
---

# [Operating System] XV6 Scheduler Assignment Implementation 2

## 구현하기에 앞서

우선 주요 목표는 다시 한 번, Round Robin의 디폴트 스케줄러를 MLFQ(Multi Level Feedback Queue) 스타일의 스케줄러로 변경하는 것입니다. 여기서 '스타일'이라고 함은, 완벽한 MLFQ가 아니라 과제를 위해 몇 가지 조건이 추가되었기 때문에 사용한 용어입니다. 이에 대한 조건은 이 [포스트](https://acoustikue.github.io/archivers/xv6-scheduler-assign-guide)에서 확인할 수 있습니다.

MLFQ는 거창하게 Multi Level이 붙었지만 단순히 여러 개의 Queue만 있으면 이 Queue 사이를 이동하는 행위만 정의해주면 됩니다. Queue는 일반적으로 Linked List를 이용하므로 저도 Linked List를 이용하는게 좋을 것 같습니다.

<!--more-->

일단 xv6는 머신 부팅 시 상위 메모리 주소에 proc table에 대한 정보를 쌓아두는데, 이 크기는 정해져 있습니다. 또 앞선 포스트에 언급되어 있는 코드 외에 다른 부분을 확인하면 알겠지만 부팅 시 trap table이나 필요한 부가 정보를 메모리에 차곡차곡 쌓아올립니다. 그 말인 즉슨, 예를 들어 NPROC으로 정의되어 있는 최대 64개의 프로세스를 임의적으로 개수를 늘리거나, proc table을 kernel domain의 다른 메모리 주소에 올려놓기가 힘들겁니다. 아마 그러려면 모든 소스를 뜯어고쳐야 할 지도 모를 일입니다. (일부 주요 함수는 어셈블리어로 작성되어 있으니 그것 마져 고쳐야 할 지도...) 가변적인 링크드 리스트를 이용한 직접적인 초기화는 힘듭니다.

따라서 가능한 접근법은 부팅 시의 각종 정보 (proc table 사이즈 등등)은 유지하되, 관리만 Queue를 사용해야 더 쉽게 구현이 가능하다는 결론이 나옵니다. xv6가 생성할 수 있는 최대 개수 NPROC과 proc table은 그대로 놔 두고, 추가적으로 Queue를 사용하여 scheduler() 만 고치는거죠.

## 변수 준비

리스트로 프로세스를 관리하려면 다음 proc 구조체를 가리키는 포인터 변수가 필요합니다. 문제라면 이걸 어디에 끼워넣느냐가 될겁니다. 어차피 최대 개수가 NPROC으로 정해져 있는 요소를 관리하면 되는 문제이므로 다음과 같은 방법이 있을 수 있을 겁니다.

- struct proc* 타입의 배열을 추가 생성하여 circular queue 형식으로 관리하는 방법
- proc 구조체를 직접 수정하여 안에 struct proc* 타입의 멤버를 선언하고 linked list 형태로 구현하는 방법

첫 번째 방법은 proc 구조체를 건드리지 않고 외부에서 포인터 형식으로 각 proc 구조체를 독립적으로 관리하는 방법입니다. 이는 기존 코드를 고치는 작업이 많이 필요하지 않고 프로세스 정보만 포인터로 잘 관리하면 되기 때문이죠. 고정 길이 배열로 struct proc*을 관리하기 때문에 우선적으로 비용이 많이 줄어듭니다. 

두 번째 방법은 구조체 안에 직접 struct proc* 타입의 포인터 멤버 변수를 선언하는 것입니다. 각 Level에 해당하는 Queue 관리를 위한 방법이라고 하면 될 것 같습니다. 데이터구조 수업에서 배우는 가장 일반적인 방법이고 배열로 관리하는 방법과는 달리 추가적인 배열을 사용하지 않는다는 장점이 있습니다. 물론, 멤버를 추가하는 것이기 때문에 proc 구조체 자체의 크기가 늘어난다는 점에서 space 측면에서는 큰 차이가 없을 것이라 예상됩니다.

첫 번째 방법의 경우 제가 제일 처음 시도한 방법이고 충분히 구현 가능합니다. 그러나 이 과제에서는 일반적인 Queue를 구현하는 것을 목적으로 두고 있기 때문에 본 포스트에서는 두 번째 방법을 사용하도록 하겠습니다. 

### struct proc

앞서 설명한 struct proc 구조체는 아래와 같습니다. 이전과 다음 포스트를 가리킬 변수가 필요하므로 struct proc* 타입의 p_prev, p_next의 변수를 선언해줍니다. 

```cpp
// proc.c
// Per-process state
struct proc {
  struct spinlock lock;

  // p->lock must be held when using these:
  enum procstate state;        // Process state
  struct proc *parent;         // Parent process
  void *chan;                  // If non-zero, sleeping on chan
  int killed;                  // If non-zero, have been killed
  int xstate;                  // Exit status to be returned to parent's wait
  int pid;                     // Process ID

  // these are private to the process, so p->lock need not be held.
  uint64 kstack;               // Virtual address of kernel stack
  uint64 sz;                   // Size of process memory (bytes)
  pagetable_t pagetable;       // User page table
  struct trapframe *trapframe; // data page for trampoline.S
  struct context context;      // swtch() here to run process
  struct file *ofile[NOFILE];  // Open files
  struct inode *cwd;           // Current directory
  char name[16];               // Process name (debugging)


// EEE3535 Operating System
// Assignment #4 Scheduling
// Author: SukJoon Oh, acoustikue@yonsei.ac.kr
#define SUKJOON
#ifdef SUKJOON
  int           p_id;           // This p_id holds which queue it is located in.
  uint          p_ticks[3];     // ticks.
  uint          p_stp;          // tick calculation starting point
  struct proc*  p_prev;         // Maybe unnecessary?
  struct proc*  p_next;         // Move to the next element.
  int           p_intr;
#endif
};
```

이 과제에서는 총 세 개의 Queue를 구현하는 것을 목적으로 두고 있습니다. 스케줄링 과정에서는 한 프로세스가 어느 Level의 Queue에 위치해있는지 알아야 하는 경우가 있습니다. 그래야 어떤 특정한 시점이 현재 프로세스를 어느 Queue에 올려두어야 하는지 알아야 하기 때문이죠. Queue는 Linked List로 구현되어 있기 때문에 프로세스를 옮겨야 하는 경우 그 Queue에 process가 상주해 있는지 찾을 필요가 있습니다. 최대 프로세스의 수가 64개로 정해져 있기 때문에 최악의 경우에도 64번만 리스트를 타고 검사하면 됩니다. 그렇지만 직관적이고 빠른 search를 위해 현재 프로세스가 저장되어 있는 Queue의 레벨을 저장하는 p_id 변수도 추가해줍니다.

p_ticks와 p_stp 등은 현재 프로세스가 CPU를 얼마나 점유하고 있는지를 나타내는 변수입니다. 이 변수들은 console 창으로 보여야하는 것들의 과제 목표 중 일부라 이에 대한 설명은 넘어가도록 하겠습니다.


### Queue

세 개의 Queue level이 존재하므로 Queue를 관리하는 변수도 각각 세 개면 충분합니다. 

```cpp
// proc.c
typedef struct __queue {
  int           q_id;           // Queue holds its ID. Q2 holds 2 for instance.
  int           q_cnt;          // Element counter.
  struct proc*  q_head;         // First element position.   
  struct proc*  q_tail;         // Last element position.
} _queue;
```

별 내용은 없습니다. q_id는 각 Queue의 레벨을 나타내는 확인용 변수이고, q_cnt는 queue 안에 몇 개의 요소를 가지고 있는지 카운트하는 변수입니다.


## 함수 준비

이 정도면 간단히 새로운 스케줄러를 구성하는 데는 충분합니다. 나머지는 구현하면서 추가하도록 합시다. 

이 과제에서는 어느 부분에서 action을 추가할지가 관건입니다. *main()* 함수에서 *procinit()* 부분에서 proc table을 제일 먼저 건드리고 있으니 그곳에서 queue를 초기화하도록 하죠. 

```cpp
// Initializes struct __queue. 
// This function should be called when the system was on booting.
// procinit(), for instance. Any scope in front of scheduler() function will do.
void init_queue(_queue*, int);
```

함수 원형은 아래와 같습니다.

```cpp
//
// Make it all zeros.
void init_queue(_queue* q, int id) { 
  q->q_id = id; 
  q->q_head = q->q_tail = 0; 
  q->q_cnt = 0;
};
```

이걸 아래 부분에다 추가하도록 합시다.

```cpp
// proc.c
// initialize the proc table at boot time.
void
procinit(void)
{
  struct proc *p;
  
  initlock(&pid_lock, "nextpid");
  for(p = proc; p < &proc[NPROC]; p++) {
      initlock(&p->lock, "proc");

      // Allocate a page for the process's kernel stack.
      // Map it high in memory, followed by an invalid
      // guard page.
      char *pa = kalloc();
      if(pa == 0)
        panic("kalloc");
      uint64 va = KSTACK((int) (p - proc));
      kvmmap(va, (uint64)pa, PGSIZE, PTE_R | PTE_W);
      p->kstack = va;
  }
  kvminithart();

// 추가!!
#ifdef SUKJOON
  // Set all values to zeros.
  init_queue(&q2, Q2);
  init_queue(&q1, Q1);
  init_queue(&q0, Q0);
#endif
}
```

코드의 내용은 별 것 없습니다. 각 Queue를 나타내는 구조체를 받아다가 모두 0으로 초기화시킵니다. 물론, 각 q_id는 Queue level 마다 맞게 설정해 주어야 하니 Q2, Q1, Q0를 인자로 넣어줍니다. 매크로 원형은 다음과 같아요.

```cpp
#define Q2    2
#define Q1    1
#define Q0    0
```

그리고 미리 enqueue와 dequeue 작업을 할 수 있도록 함수를 만들어주죠. 간단히 q_head와 q_tail, 그리고 각 프로세스안의 포인터 변수를 서로 연결해주는 작업입니다.

```cpp
// Queue controls
// 
int is_empty(_queue* q) { return (q->q_cnt == 0); } 

struct proc* get_head(_queue* q) { return q->q_head; }
struct proc* get_tail(_queue* q) { return q->q_tail; }
int get_cnt(_queue* q) { return q->q_cnt; }

// Simple enqueueing function.
struct proc* enqueue(_queue* q, struct proc* p) {
  color(p, q->q_id); // color it first
  
  if (is_empty(q)) { ground(p); q->q_head = q->q_tail = p; }
  else {
    q->q_tail->p_next = p;
    p->p_prev = q->q_tail;
    p->p_next = 0;
    q->q_tail = p;
  }

  q->q_cnt++;  
  return p;
}

// Simple dequeueing function.
struct proc* dequeue(_queue* q) {
  struct proc* p = q->q_head;
  if (is_empty(q)) return 0;

  // When single element exists
  if (q->q_cnt == 1) q->q_head = q->q_tail = 0;
  else {
    q->q_head = p->p_next;
    q->q_head->p_prev = 0;
  }
  uncolor(p); // remove color
  ground(p);

  q->q_cnt--;
  return p;
}
```

여기서 is_empty()는 queue가 비었는지 검사하는 함수입니다. 비었다면 1을 반환하기 때문에 미리 오류를 방지하도록 작성했습니다. 이 외에도 Queue 상태를 확인하도록 하는 함수들을 미리 만들어둡니다.

## To new scheduler!

다시 한 번 상기하자면, 목적은 scheduler() 함수를 교체하는 것입니다. 이전 포스트에서 보았듯, for 문을 돌면서 Round Robin 형태로 프로세스를 순차적으로 CPU에 제공하는 것을 볼 수 있습니다.

이 과제에서 요구하는 MLFQ에서는 프로세스가 제일 처음 만들어질 경우 가장 interactive 한 Queue, 즉 Q2에 먼저 집어넣도록 요구합니다. 따라서 프로세스가 생성되는 코드를 찾아 Q2에 밀어넣도록 합시다.

```cpp
// Look in the process table for an UNUSED proc.
// If found, initialize state required to run in the kernel,
// and return with p->lock held.
// If there are no free procs, or a memory allocation fails, return 0.
static struct proc*
allocproc(void)
{
  struct proc *p;

  for(p = proc; p < &proc[NPROC]; p++) {
    acquire(&p->lock);
    if(p->state == UNUSED) {
      goto found;
    } else {
      release(&p->lock);
    }
  }
  return 0;

found:
  p->pid = allocpid();

#ifdef SUKJOON
  enqueue(&q2, p);
  p->p_ticks[Q2] = p->p_ticks[Q1] = p->p_ticks[Q0] = 0;
  p->p_stp = ticks;
  p->p_intr = 0;
#endif

  // Allocate a trapframe page.
  if((p->trapframe = (struct trapframe *)kalloc()) == 0){
    release(&p->lock);
    return 0;
  }

  // An empty user page table.
  p->pagetable = proc_pagetable(p);
  if(p->pagetable == 0){
    freeproc(p);
    release(&p->lock);
    return 0;
  }

  // Set up new context to start executing at forkret,
  // which returns to user space.
  memset(&p->context, 0, sizeof(p->context));
  p->context.ra = (uint64)forkret;
  p->context.sp = p->kstack + PGSIZE;

  return p;
}
```

*allocproc()* 함수는 본래 UNUSED로 state되어 있는 struct proc을 proc table에서 찾아 프로세스를 할당합니다. 오랜 만에 goto 문을 볼 수 있네요. 여기서 프로세스가 만들어지므로 다른 건 볼 필요 없고, Q2에 넣어주는 작업만 하면 됩니다. 아까 작성한 enqueue 함수를 사용하도록 하죠. 

xv6를 다루다 보면 예상하지 못한 곳에서 panic이 일어나는 경우가 잦습니다. 대부분의 경우 access하면 안되는 메모리에 접근한 경우입니다. enqueue 함수는 단순히 새로 만들어진 struct proc 의 주소만 queue에 기록하는 것이므로 별 다른 에러를 발생하지 않지만 proc의 내부 변수에 접근하는 경우는 신중해야 합니다. 따라서 이 경우 acquire과 release 함수를 적극 사용하는 것이 좋습니다.

혹시 모를 경우를 대비해 enqueue도 acquire과 release 사이에 위치시켰습니다.





(작성중)
