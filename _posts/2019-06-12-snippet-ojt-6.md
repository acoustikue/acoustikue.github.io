---
layout: post
title:  "[OJT]Data Structure(6)"
date:   2019-06-12 09:00:00
categories: "OICW"
nocomments: true
permalink: /archivers/snippet_ojt_6
---

# '17년 OJT

### CNode(General).hpp
<!--more-->

```cpp

/*
**	
** Theme: General Purpose Node
** Author(Developer): Air Force Operations Information & Command Wing, KTMO-Cell S/W Support, SukJoon Oh
** Version: Stable
** Since 2017.12.28.	Last update: 2017.12.28.
** File: CNode(General).hpp
** Notice: Triple-modes supported
**	
*/

#pragma once

// #define VERSION "Stable"
#define DEBUG_ON
#define EXCEPTION_ON

#undef GP_NODE
// #define SLL_NODE
// #define DLL_NODE
// #define RBT_NODE

#if defined(DEBUG_ON)

#include <iostream>
using namespace std;

#endif

template <class TYPE = unsigned char>
class CNode {
private:
	/* Common Field */
	TYPE mData_

	/* GP_BINARY_NODE Field */
#if defined(GP_NODE)
	/*
	CNode<TYPE>* mPrev_;
	CNode<TYPE>* mNext_;
	*/
#endif

	/* SLL_NODE Field */
#if defined(SLL_NODE)
	CNode<TYPE>* mNext_;
#endif

	/* DLL_NODE Field */
#if defined(DLL_NODE)
	CNode<TYPE>* mPrev_;
	CNode<TYPE>* mNext_;
#endif

	/* RBT_NODE Field */
#if defined(RBT_NODE)
	CNode<TYPE>* mPar_;
	CNode<TYPE>* mLeft_;
	CNode<TYPE>* mRight_;

	enum {
		RED = 0, 
		BLACK 
	} mColor_;
#endif

	/* Unnecessary functions will be declared as 'private'. */
	CNode(CNode&);
	CNode(CNode&&);

	CNode& operator =(CNode&);

protected:


public:
	/* Constructor & Destructor */
	explicit CNode(TYPE = 0x00);  // Constructors are defined differently according to each modes.
	virtual ~CNode();

	/* Interface */
#if defined(SLL_NODE)
	CNode<TYPE>* getNext() const;

	void setNext(CNode<TYPE>*);
#endif

#if defined(DLL_NODE) 
	CNode<TYPE>* getNext() const;
	CNode<TYPE>* getPrev() const;

	void setNext(CNode<TYPE>*);
	void setPrev(CNode<TYPE>*);
#endif

#if defined(RBT_NODE)
	CNode<TYPE>* getPar() const;
	CNode<TYPE>* getNext() const;
	CNode<TYPE>* getPrev() const;

	void setPar(CNode<TYPE>*);
	void setNext(CNode<TYPE>*);
	void setPrev(CNode<TYPE>*);

	void toRed();
	void toBlack();
#endif

};

/* 
	Method Definition 
*/

#if defined(GP_NODE) // UNDEFINED
// Constructor & Destructor

#endif

#if defined(SLL_NODE) // STABLE
// Constructor & Destructor
template <typename TYPE>
explicit CNode<TYPE>::CNode(TYPE argData_) : mNext_(nullptr), mData_(argData_) {
#ifdef DEBUG_ON
	cout << "CNode::CNode(TYPE)" << endl;
#endif
};

template <typename TYPE>
CNode<TYPE>::~CNode() {
#ifdef DEBUG_ON
	cout << "CNode::~CNode()" << endl;
#endif
};

// set val
template <typename TYPE>
void CNode<TYPE>::setNext(CNode<TYPE>* argNext_) {
	mNext_ = argNext_;
};

// return val
template <typename TYPE>
CNode<TYPE>* CNode<TYPE>::getNext() const {
	return mNext_;
};

#endif


#if defined(DLL_NODE) // STABLE
// Constructor & Destructor
template <typename TYPE>
explicit CNode<TYPE>::CNode(TYPE argData_) : mNext_(nullptr), mPrev_(nullptr), mData_(argData_) {
#ifdef DEBUG_ON
	cout << "CNode::CNode(TYPE)" << endl;
#endif
};

template <typename TYPE>
CNode<TYPE>::~CNode() {
#ifdef DEBUG_ON
	cout << "CNode::~CNode()" << endl;
#endif
};

// set val
template <typename TYPE>
void CNode<TYPE>::setNext(CNode<TYPE>* argNext_) {
	mNext_ = argNext_;
};

template <typename TYPE>
void CNode<TYPE>::setPrev(CNode<TYPE>* argPrev_) {
	mPrev_ = argPrev_;
};

// return val
template <typename TYPE>
CNode<TYPE>* CNode<TYPE>::getNext() const {
	return mNext_;
};

template <typename TYPE>
CNode<TYPE>* CNode<TYPE>::getPrev() const {
	return mPrev_;
};

#endif

#if defined(RBT_NODE) // STABLE
// Constructor & Destructor
template <typename TYPE>
explicit CNode<TYPE>::CNode(TYPE argData_) : mPar_(nullptr), mNext_(nullptr), mPrev_(nullptr), mColor_(RED), mData_(argData_) {
#ifdef DEBUG_ON
	cout << "CNode::CNode(TYPE)" << endl;
#endif
};

template <typename TYPE>
CNode<TYPE>::~CNode() {
#ifdef DEBUG_ON
	cout << "CNode::~CNode()" << endl;
#endif
};

// return val
template <typename TYPE>
CNode<TYPE>* CNode<TYPE>::getPar() const {
	return mPar_;
};

template <typename TYPE>
CNode<TYPE>* CNode<TYPE>::getNext() const {
	return mNext_;
};

template <typename TYPE>
CNode<TYPE>* CNode<TYPE>::getPrev() const {
	return mPrev_;
};

// set val
template <typename TYPE>
void CNode<TYPE>::setPar(CNode<TYPE>* argPar_) {
	mPar_ = argPar_;
};

template <typename TYPE>
void CNode<TYPE>::setNext(CNode<TYPE>* argNext_) {
	mNext_ = argNext_;
};

template <typename TYPE>
void CNode<TYPE>::setPrev(CNode<TYPE>* argPrev_) {
	mPrev_ = argPrev_;
};

// Flag Setting
template <typename TYPE>
void CNode<TYPE>::toBlack() {
	mColor_ = BLACK;
};

template <typename TYPE>
void CNode<TYPE>::toRed() {
	mColor_ = RED;
};

#endif






```




