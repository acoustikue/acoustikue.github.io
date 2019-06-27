---
layout: post
title:  "[OJT]Data Structure(1)"
date:   2019-06-12 09:00:00
categories: "OICW"
nocomments: true
permalink: /archivers/snippet_ojt_1
---

# '17년 OJT

### CDataStructure.hpp

<!--more-->

```cpp

#define DEBUG_ON
#define EXCEPTION_ON // default

#include "CNode.hpp"
#include "CStack.hpp"
#include "CQueue.hpp"

#include "CRB_Tree.hpp"

#include <conio.h>

```


### CDoublyLinkedList.hpp
```cpp
/*
**	
** Theme: NONE
** Author(Developer): Air Force Operations Information & Command Wing, KTMO-Cell S/W Support, SukJoon Oh
** Version: Stable
** Since 2017.12.14.	Last update: 2017.12.27.
** File: CDoubleLinkedListNode.hpp
** Notice: 
**	
*/

#define VERSION "Stable"
#define DEBUG_ON
#define EXCEPTION_ON

#define CDLL CDoubleLinkedList

/* CHeap Class */

template <class TYPE = unsigned char>
class CDoubleLinkedListNode { 
private:
	CDoubleLinkedListNode* mPrev_;
	CDoubleLinkedListNode* mNext_;
	TYPE mData_;

	/* Constructor & Destructor */
	explicit CDoubleLinkedListNode(TYPE argData_ = 0) throw() : mPrev_(nullptr), mNext_(nullptr), mData_(argData_) {

#ifdef DEBUG_ON
		cout << "CDoubleLinkedList::CDoubleLinkedList()" << endl;
#endif

	};

	virtual ~CDoubleLinkedListNode() {

#ifdef DEBUG_ON

		cout << "CDoubleLinkedList::~CDoubleLinkedList()" << endl;
#endif
	};

	/* Unnecessary functions will be declared as private */
	CDoubleLinkedListNode(CDoubleLinkedListNode&);
	CDoubleLinkedListNode(CDoubleLinkedListNode&&);

	CDoubleLinkedListNode& operator =(CDoubleLinkedListNode&);

public:
	CDoubleLinkedListNode<TYPE>* getPrev() const {
		
		return mPrev_;
	};

	CDoubleLinkedListNode<TYPE>* getNext() const {
		
		return mNext_;
	};

	CDoubleLinkedListNode<TYPE>* getThis() const {
		
		return this;
	};
	
	const TYPE getData() const {
	
		return mData_;
	};

	void setData(const TYPE argData_) {
		
		mData_ = argData_;
	};

	void setPrev(CDoubleLinkedListNode<TYPE>* argT_) {
		
		mPrev_ = argT_;
	};

	void setNext(CDoubleLinkedListNode<TYPE>* argT_) {
	
		mNext_ = argT_;
	};
};



```
