---
layout: post
title:  "Introduction to Concurrency(Part 3)[4]"
date:   2019-04-11 09:00:00
categories: LINK(OICW)
permalink: /archivers/concurrency_introduction_part_3_4
---

# C++ Multithreading (Part 3)[4]
## C++ Memory Model

> 필자는 공군 작전정보통신단 체계개발실에서 복무('17~'19)하였습니다. 이 포스트는 병사 **프로그래밍 동아리(LINK)** 에서의 활동을 바탕으로 작성한 내용입니다.


## 3.6. Introducing Low-Level Atomic to the C++ Memory Model

C++11로 오면서 명시적으로 sequential consistency 보장(guarantee)을 느슨하게 적용하는 방법이 생겼습니다. Figure 7에서의 각종 옵션이 원자적 연산이 명시적으로 로우 레벨(low-level)에서 동작하게끔 만들어줍니다. 우선 sequential consistent ordring이 표준(standard)으로 정의되어 있는 모델임을 기억하면서 다른 느슨한 모델을 봅시다.

## 3.6.1. Ordering Options

아래는 정렬 옵션(ordering options)의 의미를 나타내고 있습니다. 

- memory_order_relaxed: no operation orders memory.
- memory_order_release, memory_order_acq_rel, and memory_order_seq_cst: a store operation performs a release operation on the affected memory location.
- memory_order_consume: a load operation performs a consume operation on the affected memory location.
- memory_order_acquire, memory_order_acq_rel, and memory_order_seq_cst: a load operation performs an acquire operation on the affected memory location.


여러 용어가 등장하니 복잡하지만 단순하게 생각해봅시다. memory_order_relaxed는 메모리를 정렬하지 않습니다. memory_order_release, memory_order_acq_rel, memory_order_seq_cst의 쓰기 연산(store operation)은 release operation을 수행하고, memory_order_acquire, memory_order_acq_rel, memory_order_seq_cst의 읽기 연산(load operation)은 acquire operation을 수행합니다. 


## 3.6.2. Sequential Consistent Ordering







