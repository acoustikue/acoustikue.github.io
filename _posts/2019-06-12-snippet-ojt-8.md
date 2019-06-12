---
layout: post
title:  "[OJT]Data Structure(8)"
date:   2019-06-12 09:00:00
categories: "ROKAF"
nocomments: true
permalink: /archivers/snippet_ojt_8
---

# '17년 OJT

### CRedBlackTreeNode.hpp
<!--more-->

```cpp
/*
**	
** Theme: Red Black Tree Node
** Author(Developer): Air Force Operations Information & Command Wing, KTMO-Cell S/W Support, SukJoon Oh
** Version: Stable
** Since 2017.12.27.	Last update: 2017.12.28.
** File: CRedBlackTreeNode.hpp
** Notice: 
**	
*/

#pragma once

#define DEBUG_ON
#define EXCEPTION_ON

#if defined(DEBUG_ON)

#include <iostream>
using namespace std;

#endif

template <class TYPE = unsigned char>
class CRedBlackTreeNode {
private:
	/* Field */
	TYPE mData_;

	CRedBlackTreeNode<TYPE>* mPar_;
	CRedBlackTreeNode<TYPE>* mLeft_;
	CRedBlackTreeNode<TYPE>* mRight_;

	enum {
		RED = 0, 
		BLACK 
	} mColor_;

	/* Unnecessary functions will be declared as private */
	CRedBlackTreeNode(CRedBlackTreeNode&);
	CRedBlackTreeNode(CRedBlackTreeNode&&);

	CRedBlackTreeNode& operator =(CRedBlackTreeNode&);

protected:


public:
	/* Constructor & Destructor */
	explicit CRedBlackTreeNode(TYPE = 0x00);
	virtual ~CRedBlackTreeNode();

	/* Interface */
	void toRed();
	void toBlack();

	CRedBlackTreeNode<TYPE>* getPar() const;
	CRedBlackTreeNode<TYPE>* getNext() const;
	CRedBlackTreeNode<TYPE>* getPrev() const;

	void setPar(CRedBlackTreeNode<TYPE>*);
	void setNext(CRedBlackTreeNode<TYPE>*);
	void setPrev(CRedBlackTreeNode<TYPE>*);
};

/* 
	Method Definition 
*/

// Constructor & Destructor
template <typename TYPE>
explicit CRedBlackTreeNode<TYPE>::CRedBlackTreeNode(TYPE argData_) : mPar_(nullptr), mNext_(nullptr), mPrev_(nullptr), mColor_(RED), mData_(argData_) {

#ifdef DEBUG_ON
	cout << "CRedBlackTreeNode::CRedBlackTreeNode()" << endl;
#endif
};

template <typename TYPE>
CRedBlackTreeNode<TYPE>::~CRedBlackTreeNode() {

#ifdef DEBUG_ON
	cout << "CRedBlackTreeNode::~CRedBlackTreeNode()" << endl;
#endif
};

// Flag Setting
template <typename TYPE>
void CRedBlackTreeNode<TYPE>::toBlack() {
	mColor_ = BLACK;
};

template <typename TYPE>
void CRedBlackTreeNode<TYPE>::toRed() {
	mColor_ = RED;
};

// return val
template <typename TYPE>
CRedBlackTreeNode<TYPE>* CRedBlackTreeNode<TYPE>::getPar() const {
	return mPar_;
};

template <typename TYPE>
CRedBlackTreeNode<TYPE>* CRedBlackTreeNode<TYPE>::getNext() const {
	return mNext_;
};

template <typename TYPE>
CRedBlackTreeNode<TYPE>* CRedBlackTreeNode<TYPE>::getPrev() const {
	return mPrev_;
};

// set val
template <typename TYPE>
void CRedBlackTreeNode<TYPE>::setPar(CRedBlackTreeNode<TYPE>* argPar_) {
	mPar_ = argPar_;
};

template <typename TYPE>
void CRedBlackTreeNode<TYPE>::setNext(CRedBlackTreeNode<TYPE>* argNext_) {
	mNext_ = argNext_;
};

template <typename TYPE>
void CRedBlackTreeNode<TYPE>::setPrev(CRedBlackTreeNode<TYPE>* argPrev_) {
	mPrev_ = argPrev_;
};


```

