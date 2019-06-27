---
layout: post
title:  "[OJT]Data Structure(3)"
date:   2019-06-12 09:00:00
categories: "OICW"
nocomments: true
permalink: /archivers/snippet_ojt_3
---

# '17년 OJT

### CStack.hpp
<!--more-->

```cpp
/*
* 2017.11.06. 
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

#pragma warning(disable:4715)
#pragma once

#include "CNode(DLL_Based).hpp"

#ifdef DEBUG_ON

#include <iostream>
#include <cstdio>
using namespace std;

#endif

#define U_CHAR unsigned char

template <typename TYPE = U_CHAR>
class CStack {
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
	
		int ReportError() {
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
	CStack(CStack&);
	CStack(CStack&&);

	CStack& operator =(CStack&);

protected:

public:
	CStack() throw() : mHead_(nullptr), mNodeCounter_(0x00) {
#ifdef DEBUG_ON
		cout << "CStack::CStack(): Constructor successfully called " << endl;
		cout << "mHead_: " << mHead_ << endl;
		cout << endl;
#endif
	};

	virtual ~CStack() { 
#ifdef DEBUG_ON
		cout << "CStack::~CStack(): Destructor successfully called " << endl;
		cout << endl;
#endif	
	};

	const bool pushNode(TYPE = 0);
	const TYPE popNode();

#ifdef DEBUG_ON	
	/* Reads all data and counter */
	void currentStatus() const;
#endif

};


/* Push() */
template <class TYPE>
const bool CStack<TYPE>::pushNode(TYPE argData_) {

#ifdef DEBUG_ON
	cout << "CStack::Push(TYPE): Successfully called" << endl;
	cout << "	Current mNodeCounter_: " << mNodeCounter_ << endl;
#endif	

	CNode<TYPE>* NewNode_ = new CNode<TYPE>(argData_);
	CNode<TYPE>* Iterator_ = mHead_;

	try {
		
		if(NewNode_ == nullptr)
			throw CException(0x00);

		if(mNodeCounter_ != 0) {
		
			while(Iterator_->mRight_ != nullptr) {
#ifdef DEBUG_ON
				cout << "	Loop engaged, Iterator_->mRight_: " << Iterator_->mRight_ << endl;
#endif	
				Iterator_ = Iterator_->mRight_;
			}

#ifdef DEBUG_ON
			cout << "	Loop engaged, Iterator_->mRight_: " << Iterator_->mRight_ << endl;
#endif

			NewNode_->mLeft_ = Iterator_;
			Iterator_->mRight_ = NewNode_;

			mNodeCounter_++;

		} else {
			
			mHead_ = NewNode_;

			mNodeCounter_++;

		}
		
	} catch(CException& argException_) {
	
		argException_.ReportError();
	}

#ifdef DEBUG_ON
	cout << "	After Push() mNodeCounter_: " << mNodeCounter_ << endl;
	cout <<  endl;
#endif	

	return true;
};


// CurrentStatus()
template <class TYPE>
void CStack<TYPE>::currentStatus() const {

	CNode<TYPE>* Iterator_ = mHead_;

#ifdef DEBUG_ON
	cout << "CStack::Push(TYPE): Successfully called" << endl;
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
	
		argException_.ReportError();
	}
};

/* Pop() */
template <typename TYPE>
const TYPE CStack<TYPE>::popNode() {
#ifdef DEBUG_ON
	cout << "CStack::Pop(): Successfully called" << endl;
	cout << "	Current mNodeCounter_: " << mNodeCounter_ << endl;
#endif	

	CNode<TYPE>* Iterator_ = mHead_;

	try {

		if(mNodeCounter_ == 1) {

#ifdef DEBUG_ON
			cout << "	Will be deleted, Iterator_->mRight_: " << Iterator_->mRight_;
			cout << ", Iterator_->mData_: " << Iterator_->mData_ << endl;
			cout << endl;
#endif
		
			delete Iterator_;

			mHead_ = nullptr;

			mNodeCounter_--;

			return static_cast<TYPE>(0);

		} else if(mNodeCounter_ != 0) {
		
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
			Iterator_->mLeft_->mRight_ = nullptr;

			delete Iterator_;	

			mNodeCounter_--;

#ifdef DEBUG_ON
			cout << endl;
#endif
		
		} else {
			
			throw CException(0x01);
		}	
	
	} catch(CException& argException_) {
	
		argException_.ReportError();
	}


};

```
