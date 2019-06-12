---
layout: post
title:  "[OJT]Data Structure(2)"
date:   2019-06-12 09:00:00
categories: "ROKAF"
nocomments: true
permalink: /archivers/snippet_ojt_2
---

# '17년 OJT

### CHeap.hpp
<!--more-->
```cpp
/*
**	
** Theme: NONE
** Author(Developer): Air Force Operations Information & Command Wing, KTMO-Cell S/W Support, SukJoon Oh
** Version: Experimental
** Since 2017.12.18.	Last update: 2017.12.27.
** File: CHeap.hpp
** Notice: 
**	
*/

#pragma once

#include <cstdlib>
// #include <cmath>

#define HEAP_VERSION "Stable"
#define DEBUG_ON
#define EXCEPTION_ON

#ifdef DEBUG_ON
#include <iostream>

using namespace std;
#endif

/* CHeap Class */

template <class TYPE = unsigned char>
class CHeap {
private:

	TYPE* mpData_;
	unsigned mCap_;
	unsigned mUsed_;
	
	/* Unneccessary overloaded operators, declared as private */
	CHeap(CHeap&);
	CHeap(CHeap&&);

	CHeap& operator =(CHeap&);

	/*  */

	/*  */

protected:
	class CException {
private:
	int mErrorCode_;

protected:

public:
	explicit CException(int argErrorCode_) : mErrorCode_(argErrorCode_) {
#ifdef DEBUG_ON
		cout << "CException::CException(): Constructor successfully called " << endl;
#endif
	};

	~CException() { 
#ifdef DEBUG_ON
		cout << "CException::~CException(): Destructor successfully called " << endl;
		cout << endl;
#endif
	};
	
	int reportError() {

		switch(mErrorCode_) {
		case 0x00:
#ifdef DEBUG_ON
			cout << "CException::ReportError(): Bad memory allocation" << endl;
			cout << "	Error Code: " << mErrorCode_ << endl;
#endif					
			return mErrorCode_;

		case 0x01:
#ifdef DEBUG_ON
			cout << "CException::ReportError(): Empty stack" << endl;
			cout << "	Error Code: " << mErrorCode_ << endl;
#endif					
			return mErrorCode_;


		default:
#ifdef DEBUG_ON
			cout << "CException::ReportError(): Unknown error occurred " << endl;
			cout << "	Error Code: " << mErrorCode_ << endl;
#endif
			return mErrorCode_;
		};
	};
};;

public:
	CHeap() : mpData_(nullptr), mUsed_(0), mCap_(0) {

#ifdef DEBUG_ON
		cout << "CHeap::CHeap() " << endl;
		cout << "CHeap > Version: " << HEAP_VERSION << endl;
#endif 

	};

	virtual ~CHeap() {

#ifdef DEBUG_ON
		cout << "CHeap::~CHeap() " << endl;
#endif

		if(mpData_ != nullptr) {
#ifdef DEBUG_ON
		cout << "STAT > Freeing memory" << endl;
#endif
			free(mpData_);
		};

	};

private:
	unsigned getLeftSib(unsigned) const;
	unsigned getRightSib(unsigned) const;
	unsigned getParent(unsigned) const;

	// void swapNode(TYPE**, TYPE**);
	void swapNode(TYPE*, TYPE*);

	unsigned getIncreasedCap(unsigned) const;
	unsigned getDecreasedCap(unsigned) const;

	const bool isFull() const;
	// const bool isLeftSib() const;
	// const bool isRightSib() const;
	
	void afterInsert();
	void afterRemove();

public:

	/* Interface */

	const bool insertData(TYPE);
	const TYPE removeData();

#ifdef DEBUG_ON
	void currentStatus() const;
#endif
};

/* 
	getParent(unsigned), getLeftSib(unsigned), getRightSib(unsigned) 
*/

template <class TYPE>
unsigned CHeap<TYPE>::getParent(unsigned argTar_) const {
	
	if(argTar_ == 0)
		return 0;

	return ((argTar_ - 1) / 2);
};

template <class TYPE>
unsigned CHeap<TYPE>::getLeftSib(unsigned argTar_) const {

	return (argTar_ * 2 + 1);
}; 

template <class TYPE>
unsigned CHeap<TYPE>::getRightSib(unsigned argTar_) const {

	return (argTar_ * 2 + 2);
};

/* 
	getIncreasedCap(unsigned), getDecreasedCap(unsigned)
*/

template <class TYPE>
unsigned CHeap<TYPE>::getIncreasedCap(unsigned argCap_) const {

	return ((2 * argCap_) + 1);
};

template <class TYPE>
unsigned CHeap<TYPE>::getDecreasedCap(unsigned argCap_) const {

	return ((argCap_ - 1) / 2);
};

/* 
	isFull()
*/

template <class TYPE>
const bool CHeap<TYPE>::isFull() const {

	if(mUsed_ == (mCap_ - 1))
		return true;
	else 
		false;
};

/* 
	swapNode() 
*/

template <class TYPE>
void CHeap<TYPE>::swapNode(TYPE* argA_, TYPE* argB_) {

#ifdef DEBUG_ON
	cout << "CHeap::swapNode(TYPE*, TYPE*)" << endl; 
	cout << "STAT > " << *argA_<< ", " << *argB_<< endl; 
#endif

	TYPE T_ = *argA_;

	*argA_ = *argB_;
	*argB_ = T_;

#ifdef DEBUG_ON
	cout << "STAT > " << *argA_<< ", " << *argB_<< endl; 
#endif
};


/* 
	currentStatus() 
*/
#ifdef DEBUG_ON
template <typename TYPE>
void CHeap<TYPE>::currentStatus() const {

	// unsigned Lev_ = 0;
	unsigned T_ = 0;

	cout << "CHeap::currentStatus()" << endl;
	 
	if(this->mUsed_ == 0) {

		cout << "STAT > The structure is empty" << endl;

	} else {

		cout << "STAT > Loop engaged" << endl;
		cout << "STAT > " << endl;

		for(register unsigned i = 0; i < mUsed_; i++) {
			
			cout << "   " << mpData_[i];

			if(T_ == i) {
			
				cout << endl;

				T_ = T_ * 2 + 2;
			}
			// cout << "STAT > " << i << " : " << mpData_[i] << endl; 
		}

		cout << endl;
	}
};
#endif

/* 
	insertData(TYPE) 
*/

template <class TYPE>
const bool CHeap<TYPE>::insertData(TYPE argData_) {

#ifdef DEBUG_ON
	cout << "CHeap::insertData(" << argData_ << ")" << endl;
#endif

	if(mUsed_ == 0) {
#ifdef DEBUG_ON
	cout << "STAT > Initializing array.. " << endl;
#endif	
		mCap_ = getIncreasedCap(mCap_);
		mpData_ = static_cast<TYPE*>(malloc(sizeof(TYPE) * mCap_));

		mpData_[mUsed_++] = argData_;

	} else {

#ifdef DEBUG_ON
	cout << "STAT > Array exists " << endl;
#endif
	
		if(!isFull()) {
			
			mpData_[mUsed_++] = argData_;

		} else {
			
			mCap_ = getIncreasedCap(mCap_);
			mpData_ = static_cast<TYPE*>(realloc(mpData_, sizeof(TYPE) * mCap_));

			mpData_[mUsed_++] = argData_;
		}

		afterInsert();
	}

	return true;
};

/* 
	afterInsert()
*/
template <class TYPE>
void CHeap<TYPE>::afterInsert() {

#ifdef DEBUG_ON
	cout << "CHeap::afterInsert()" << endl;
#endif

	unsigned It_ = mUsed_ - 1;

#ifdef DEBUG_ON
	cout << "Cur: " << mpData_[It_] << ", Par:" << mpData_[getParent(It_)] << endl;
#endif

	while(mpData_[It_] <= mpData_[getParent(It_)]) {
		
		swapNode(&mpData_[It_], &mpData_[getParent(It_)]);

		It_ = getParent(It_);

		if(It_ == 0)
			break;

	}
};

/* 
	removeData()
*/

template <class TYPE>
const TYPE CHeap<TYPE>::removeData() {
#ifdef DEBUG_ON
	cout << "CHeap::removeData() " << endl;
#endif

	if(mUsed_ == 0) {

#ifdef DEBUG_ON
		cout << "STAT > Structure is empty, unable to remove data " << endl;
#endif
		return 0;

	}
	
	TYPE returnVal_ = mpData_[0];

	TYPE* pT_ = static_cast<TYPE*>(malloc(sizeof(TYPE) * (--mUsed_)));
	
	memcpy(&pT_[1], &mpData_[1], sizeof(TYPE) * (mUsed_ - 1));



	pT_[0] = mpData_[mUsed_];

	if(mUsed_ == getDecreasedCap(mCap_)) {
		
		mCap_ = getDecreasedCap(mCap_);

	} else {
		
		mCap_--;
	}

	mpData_ = static_cast<TYPE*>(realloc(mpData_, sizeof(TYPE) * (mUsed_)));

	memcpy(mpData_, pT_, sizeof(TYPE) * (mUsed_));

	free(pT_);

	/* afterRemove() */
	afterRemove();

	return returnVal_;
};


/* 
	afterRemove()
*/

template <class TYPE>
void CHeap<TYPE>::afterRemove() {

#ifdef DEBUG_ON
	cout << "CHeap::afterRemove()" << endl;
#endif

	// unsigned Target_ = 0;
	unsigned It_ = 0;

	if(mUsed_ == 0) {

#ifdef DEBUG_ON
	cout << "STAT > No need afterRemove()" << endl;
#endif
		return;
	}

	while(1) {

		/* Comparin' */

		if(getLeftSib(It_) >= mUsed_)
			break;

		if(getRightSib(It_) >= mUsed_) {

			if(mpData_[getLeftSib(It_)] <= mpData_[It_])	{
				
				swapNode(&mpData_[It_], &mpData_[getLeftSib(It_)]);
				// currentStatus();
			}

			It_ = getLeftSib(It_);
		
		} else {
		
			if(mpData_[getLeftSib(It_)] >= mpData_[getRightSib(It_)]) {

				It_ = getRightSib(It_);

			} else {

				It_ = getLeftSib(It_);
			}

			swapNode(&mpData_[It_], &mpData_[getParent(It_)]);
			// currentStatus();
		}
	}
};

```
