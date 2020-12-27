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

## struct proc

리스트로 프로세스를 관리하려면 다음 proc 구조체를 가리키는 포인터 변수가 필요합니다. 문제라면 이걸 어디에 끼워넣느냐가 될겁니다. 어차피 최대 개수가 NPROC으로 정해져 있는 요소를 관리하면 되는 문제이므로 다음과 같은 방법이 있을 수 있을 겁니다.

- struct proc* 타입의 배열을 추가 생성하여 circular queue 형식으로 관리하는 방법
- proc 구조체를 직접 수정하여 안에 struct proc* 타입의 멤버를 선언하고 linked list 형태로 구현하는 방법

첫 번째 방법은 proc 구조체를 건드리지 않고 외부에서 포인터 형식으로 각 proc 구조체를 독립적으로 관리하는 방법입니다. 이는 기존 코드를 고치는 작업이 많이 필요하지 않고 프로세스 정보만 포인터로 잘 관리하면 되기 때문이죠. 고정 길이 배열로 struct proc*을 관리하기 때문에 우선적으로 비용이 많이 줄어듭니다. 

두 번째 방법은 구조체 안에 직접 struct proc* 타입의 포인터 멤버 변수를 선언하는 것입니다. 각 Level에 해당하는 Queue 관리를 위한 방법이라고 하면 될 것 같습니다. 데이터구조 수업에서 배우는 가장 일반적인 방법이고 배열로 관리하는 방법과는 달리 추가적인 배열을 사용하지 않는다는 장점이 있습니다. 물론, 멤버를 추가하는 것이기 때문에 proc 구조체 자체의 크기가 늘어난다는 점에서 space 측면에서는 큰 차이가 없을 것이라 예상됩니다.

이 과제에서는 일반적인 Queue를 구현하는 것을 목적으로 두고 있기 때문에 두 번째 방법을 사용하도록 하겠습니다. 첫 번째 방법을 시도해보니 충분히 구현 가능합니다.

## struct proc


```cpp
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
// Author: SukJoon Oh, 2018142216, acoustikue@yonsei.ac.kr
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

(작성중)
