---
layout: post
title:  "[OJT]Data Structure(4)"
date:   2019-06-12 09:00:00
categories: "ROKAF"
nocomments: true
permalink: /archivers/snippet_ojt_4
---

# '17년 OJT

### CQueue.hpp
<!--more-->

```cpp

/*
* 2017.11.07. 
*		
* Author: Air Force Information & Command Group, SukJoon Oh
* Data Structure Algorithm Sample
*
* Last update: 2017.11.07.
*
* Stable
*
*/

#define DEBUG_ON
#define EXCEPTION_ON // default

#pragma once

#include "CNode(DLL_Based).hpp"

#ifdef DEBUG_ON

#include <iostream>
#include <cstdio>
using namespace std;

#endif

#define U_CHAR unsigned char

template <typename TYPE = U_CHAR>
class CQueue {
private:
	CNode<TYPE>* mHead_;
	// CNode<TYPE>* mTail_;
	unsigned mNodeCounter_;

	/* Exception Class */
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
	};

	/* Unnecessary functions will be declared as private */
	CQueue(CQueue&);
	CQueue(CQueue&&);

	CQueue& operator =(CQueue&);

protected:

public:
	CQueue() throw() : mHead_(nullptr), mNodeCounter_(0x00) {
#ifdef DEBUG_ON
		cout << "CQueue::CQueue(): Constructor successfully called " << endl;
		cout << "mHead_: " << mHead_ << endl;
		cout << endl;
#endif
	};

	virtual ~CQueue() { 
#ifdef DEBUG_ON
		cout << "CQueue::~CQueue(): Destructor successfully called " << endl;
		cout << endl;
#endif	
	};

	const bool enqueue(TYPE = 0);
	const TYPE dequeue();

#ifdef DEBUG_ON	
	/* Reads all data and counter */
	void currentStatus() const;
#endif

};





// CurrentStatus()
template <class TYPE>
void CQueue<TYPE>::currentStatus() const {

	CNode<TYPE>* Iterator_ = mHead_;

#ifdef DEBUG_ON
	cout << "CQueue::CurrentStatus(): Successfully called" << endl;
	cout << "	Current mNodeCounter_: " << mNodeCounter_ << endl;
#endif	

	try {

		if(mNodeCounter_ != 0) {
		
			while(Iterator_->mRight_ != nullptr) {
#ifdef DEBUG_ON
				cout << "	Loop engaged, Iterator_->mRight_: " << Iterator_->mRight_;
				cout << ", Iterator_->mData_: " << Iterator_->mData_ << endl;
#endif	
				Iterator_ = Iterator_->mRight_;
			}

#ifdef DEBUG_ON
			cout << "	Final loop, Iterator_->mRight_: " << Iterator_->mRight_;
			cout << ", Iterator_->mData_: " << Iterator_->mData_ << endl;
			cout << endl;
#endif	
		
		} else {
			
			throw CException(0x01);
		}	
	
	} catch(CException& argException_) {
	
		argException_.reportError();
	}
};


/* Enqueue(TYPE) */
template <class TYPE>
const bool CQueue<TYPE>::enqueue(TYPE argData_) {

#ifdef DEBUG_ON
	cout << "CQueue::Enqueue(TYPE): Successfully called" << endl;
	cout << "	Current mNodeCounter_: " << mNodeCounter_ << endl;
#endif	

	CNode<TYPE>* NewNode_ = new CNode<TYPE>(argData_);

	try {
		
		if(NewNode_ == nullptr)
			throw CException(0x00);

		if(mNodeCounter_ != 0) {
		
			NewNode_->mLeft_ = mHead_->mLeft_;
			NewNode_->mRight_ = mHead_;
			//(mHead_->mLeft_)->mRight_ = NewNode_;
			mHead_->mLeft_ = NewNode_;

			mHead_ = NewNode_;

			mNodeCounter_++;

		} else {
			
			NewNode_->mLeft_ = NewNode_;
			mHead_ = NewNode_;

			mNodeCounter_++;

		}
		
	} catch(CException& argException_) {
	
		argException_.reportError();
	}

#ifdef DEBUG_ON
	cout << "	After Enqueue() mNodeCounter_: " << mNodeCounter_ << endl;
	cout <<  endl;
#endif	

	return true;
};


/* Dequeue(TYPE) */
template <class TYPE>
const TYPE CQueue<TYPE>::dequeue() {

#ifdef DEBUG_ON
	cout << "CQueue::Dequeue(): Successfully called" << endl;
	cout << "	Current mNodeCounter_: " << mNodeCounter_ << endl;
#endif	

	CNode<TYPE>* Temporary_;

	try {
		
		if(mNodeCounter_ == 0) {

			throw CException(0x01);

		} else if(mNodeCounter_ == 1) {
		
			delete mHead_;
			mHead_ = nullptr;

			mNodeCounter_--;

		} else {
			
			mHead_->mLeft_->mLeft_->mRight_ = nullptr;
			Temporary_ = mHead_->mLeft_->mLeft_;

			delete mHead_->mLeft_;

			mHead_->mLeft_ = Temporary_;

			mNodeCounter_--;

		}
		
	} catch(CException& argException_) {
	
		argException_.reportError();
	}

#ifdef DEBUG_ON
	cout << "	After Dequeue() mNodeCounter_: " << mNodeCounter_ << endl;
	cout <<  endl;
#endif	

	return true;
};

```

