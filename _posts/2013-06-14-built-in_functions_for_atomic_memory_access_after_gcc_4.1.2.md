---
layout: post
title: gcc 4.1.2版本内建的原子内存访问函数
date: 2013-06-14
categories:
  - 技术
tags:
  - atomic memory access
---

The following builtins are intended to be compatible with those described in the Intel Itanium Processor-specific Application Binary Interface, section 7.4. As such, they depart from the normal GCC practice of using the “\_\_builtin\_” prefix, and further that they are overloaded such that they work on multiple types.

The definition given in the Intel documentation allows only for the use of the types int, long, long long as well as their unsigned counterparts. GCC will allow any integral scalar or pointer type that is 1, 2, 4 or 8 bytes in length.

Not all operations are supported by all target processors. If a particular operation cannot be implemented on the target processor, a warning will be generated and a call an external function will be generated. The external function will carry the same name as the builtin, with an additional suffix `\_n' where n is the size of the data type.

In most cases, these builtins are considered a full barrier. That is, no memory operand will be moved across the operation, either forward or backward. Further, instructions will be issued as necessary to prevent the processor from speculating loads across the operation and from queuing stores after the operation.

All of the routines are are described in the Intel documentation to take “an optional list of variables protected by the memory barrier”. It's not clear what is meant by that; it could mean that only the following variables are protected, or it could mean that these variables should in addition be protected. At present GCC ignores this list and protects all variables which are globally accessible. If in the future we make some use of this list, an empty list will continue to mean all globally accessible variables.

type \_\_sync\_fetch\_and\_add (type *ptr, type value, ...)
type \_\_sync\_fetch\_and\_sub (type *ptr, type value, ...)
type \_\_sync\_fetch\_and\_or (type *ptr, type value, ...)
type \_\_sync\_fetch\_and\_and (type *ptr, type value, ...)
type \_\_sync\_fetch\_and\_xor (type *ptr, type value, ...)
type \_\_sync\_fetch\_and\_nand (type *ptr, type value, ...)
These builtins perform the operation suggested by the name, and returns the value that had previously been in memory. That is,
          { tmp = *ptr; *ptr op= value; return tmp; }
          { tmp = *ptr; *ptr = ~tmp & value; return tmp; }   // nand
     

type \_\_sync\_add\_and\_fetch (type *ptr, type value, ...)
type \_\_sync\_sub\_and\_fetch (type *ptr, type value, ...)
type \_\_sync\_or\_and\_fetch (type *ptr, type value, ...)
type \_\_sync\_and\_and\_fetch (type *ptr, type value, ...)
type \_\_sync\_xor\_and\_fetch (type *ptr, type value, ...)
type \_\_sync\_nand\_and\_fetch (type *ptr, type value, ...)
These builtins perform the operation suggested by the name, and return the new value. That is,
          { *ptr op= value; return *ptr; }
          { *ptr = ~*ptr & value; return *ptr; }   // nand
     

bool \_\_sync\_bool\_compare\_and\_swap (type *ptr, type oldval type newval, ...)
type \_\_sync\_val\_compare\_and\_swap (type *ptr, type oldval type newval, ...)
These builtins perform an atomic compare and swap. That is, if the current value of *ptr is oldval, then write newval into *ptr.
The “bool” version returns true if the comparison is successful and newval was written. The “val” version returns the contents of *ptr before the operation. 

\_\_sync\_synchronize (...)
This builtin issues a full memory barrier. 
type \_\_sync\_lock\_test\_and\_set (type *ptr, type value, ...)
This builtin, as described by Intel, is not a traditional test-and-set operation, but rather an atomic exchange operation. It writes value into *ptr, and returns the previous contents of *ptr.
Many targets have only minimal support for such locks, and do not support a full exchange operation. In this case, a target may support reduced functionality here by which the only valid value to store is the immediate constant 1. The exact value actually stored in *ptr is implementation defined.

This builtin is not a full barrier, but rather an acquire barrier. This means that references after the builtin cannot move to (or be speculated to) before the builtin, but previous memory stores may not be globally visible yet, and previous memory loads may not yet be satisfied. 

void \_\_sync\_lock\_release (type *ptr, ...)
This builtin releases the lock acquired by \_\_sync\_lock\_test\_and\_set. Normally this means writing the constant 0 to *ptr.
This builtin is not a full barrier, but rather a release barrier. This means that all previous memory stores are globally visible, and all previous memory loads have been satisfied, but following memory reads are not prevented from being speculated to before the barrier.


## 应用示例

以下是，源于`云风`写的[skynet](https://github.com/cloudwu/skynet/blob/master/skynet-src/rwlock.h)游戏服务器框架的一个示例代码（`读写锁的实现`）。

<pre class="prettyprint linenums">
#ifndef _RWLOCK_H_
#define _RWLOCK_H_

struct rwlock {
	int write;
	int read;
};

static inline void
rwlock_init(struct rwlock *lock) {
	lock->write = 0;
	lock->read = 0;
}

static inline void
rwlock_rlock(struct rwlock *lock) {
	for (;;) {
		while(lock->write) {
			__sync_synchronize();
		}
		__sync_add_and_fetch(&lock->read,1);
		if (lock->write) {
			__sync_sub_and_fetch(&lock->read,1);
		} else {
			break;
		}
	}
}

static inline void
rwlock_wlock(struct rwlock *lock) {
	while (__sync_lock_test_and_set(&lock->write,1)) {}
	while(lock->read) {
		__sync_synchronize();
	}
}

static inline void
rwlock_wunlock(struct rwlock *lock) {
	__sync_lock_release(&lock->write);
}

static inline void
rwlock_runlock(struct rwlock *lock) {
	__sync_sub_and_fetch(&lock->read,1);
}

#endif
</pre>


## 扩展阅读


## 祝大家玩的开心

