---
layout: post
title:  "[OJT]Data Structure(5)"
date:   2019-06-12 09:00:00
categories: "ROKAF"
nocomments: true
permalink: /archivers/snippet_ojt_5
---

# '17년 OJT

### CNode.hpp
<!--more-->

```cpp
/*
* 2017.11.06. 
*		
* Author: Air Force Information & Command Group, SukJoon Oh
* Quick Sort Algorithm Sample
*
* Last update: 2017.11.07.
*
* Stable
*
*/

#pragma once

#ifdef DEBUG_ON

#include <iostream>
using namespace std;

#endif

#define U_CHAR unsigned char

template <typename TYPE = U_CHAR>
class CNode {
public:
	CNode* mRight_;
	CNode* mLeft_;
	TYPE mData_;

	/* Constructor & Destructor */
	explicit CNode(TYPE argData_ = 0) throw() : mRight_(nullptr), mLeft_(nullptr), mData_(argData_) {
/*
#ifdef DEBUG_ON
		cout << "CNode::CNode(): Constructor successfully called " << endl;
		cout << "mData_: " << mData_ << endl;
		cout << endl;
#endif
*/

	};

	virtual ~CNode() {
/*
#ifdef DEBUG_ON

		cout << "CNode::~CNode(): Destructor successfully called " << endl;
		cout << endl;
#endif
		*/
	};

private:
	/* Unnecessary functions will be declared as private */
	CNode(CNode&);
	CNode(CNode&&);

	CNode& operator =(CNode&);
};

```


