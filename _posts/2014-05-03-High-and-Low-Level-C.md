---
layout: post
title: 高级C语言特性
date: 2014-05-03
categories:
  - 技术
tags:
  - C
---

__注意__：

>  本文由`Jim Larson`的[High and Low-Level C](http://www.jetcafe.org/jim/highlowc.html)改编而来。


## Introduction

Tower of languages. High-level languages can (mostly) compile to lower-level ones.
Might want to write in low-level language for access to hardware level. Can write high-level code in low-level language by compiling in your head.

Might want to write in high-level language for flexibility and power. Can write low-level code in high-level language by "writing through the compiler".

## C Features


### Recursive Functions

C has a stack used for the call stack, activation records, and local variables.
Note that functions are not nested, as in Pascal. This affords greater freedoms for using function pointers.

```c

/*
 * Simple example of a recursive function.
 */
unsigned
fib(unsigned n)
{
    if (n == 0 || n == 1) {
        return 1;
    }

    return fib(n - 1) + fib(n - 2);
}
```

This simple example is a little contrived, as well as a lousy way to compute Fibonacci numbers. A better example shows the natural relation between recursive functions and recursive data structures - those that have references to other objects of the same type.

```c

/*
 * Recursive functions work well with recursive data structures.
 */
typedef struct Expr Expr;
struct Expr {
    enum { Number, Plus, Minus, Times, Divide } op;
    union {
        double number;
        struct {
            Expr *left, *right;
        } child;
    } u;
};

double
eval(Expr *e)
{
    switch (e->op) {
        case Number:	return e->u.number;
        case Plus:	return eval(e->u.child.left) + eval(e->u.child.right);
        case Minus:	return eval(e->u.child.left) - eval(e->u.child.right);
        case Times:	return eval(e->u.child.left) * eval(e->u.child.right);
        case Divide:	return eval(e->u.child.left) / eval(e->u.child.right);
    }
}
```

## Dynamic memory allocation

Stack-allocation - local variables.
Heap-allocation. Library only, but pervasively used. (Actually, this is our first example of a high-level feature 
implemented entirely in Standard C.)

### Abstract Data Types

C Type theory is kind of confusing: Types where you know the size of the object Types where you don't know the size of the object void *

Tricks: You can declare and use a pointer to an incomplete type. You can complete an incomplete type later. Pointers to structures can be coerced to and from pointers to their first element, if it also is a structure.

```c

/* In widget.h */
typedef struct Widget Widget;

extern Widget *widget_new(double length, enum Color color);
extern double widget_mass(Widget *w);
extern int widget_fitsin(Widget *w_inner, Widget *w_outer);
extern void widget_delete(Widget *w);
```

The implementation gets to hide information about the representation, as well as guarantee invariants.

```c

/* In widget.c */
#include <stdlib.h>
#include "colors.h"
#include "widget.h"

/*
 * Non-public definition of Widget structure, declared in "widget.h".
 */
struct Widget {
	Widget *next;		/* widgets are stored on a linked list */
	int id;			/* identification stamp */
	double length;		/* length in centimeters */
	double mass;		/* mass in grams */
	enum Color color;	/* see "colors.h" for definitions */
};

static const double widget_height = 2.54;	/* in centimeters */
static const double widget_density = 1.435;	/* in g/cm^3 */
static Widget *widget_list = 0;
static int widget_next_id = 0;

/*
 * Create a new widget.  Calculate widget mass.  Keep track of
 * bookkeeping with id number and store it on linked list of widgets.
 */
Widget *
widget_new(double length, enum Color color)
{
	Widget *w = malloc(sizeof (Widget));

	if (!w)
		return 0;
	w->next = widget_list;
	widget_list = w;
	w->id = widget_next_id++;
	w->length = length;
	w->mass = 0.5 * length * length * widget_height * widget_density;
	w->color = color;
	return w;
}
```

## Nonlocal exits

Setjmp/longjmp work like a bunch of immediate returns from functions. Intermediate functions don't need to make provisions for this - modular way to raise error conditions.
Viewing function call/return sequences (aka procedure activations) as a tree, longjump can only work on a saved jmp_buf from a parent in the tree.

```c

#include <signal.h>
#include <setjmp.h>

static jmp_buf begin;

static void
fpecatch(int sig)
{
	warning("floating point exception");
	longjmp(begin, 0);
}

void
command_loop(void)
{
	for (;;) {
		if (setjmp(begin)) {
			printf("Command failed to execute!\n");
		}
		signal(SIGFPE, &fpecatch);
		prompt();
		do_command(read_command());
	}
}
```

## High-Level C


### Classes and objects

The core of OO is "Dynamic Dispatch" - you don't know which function you're calling. Function pointers also provide this kind of indirection.
Structure coercion and nesting provide for single-inheritance of classes.

Method calls can be masked by functions or macros. Can "crack open" the abstraction to cache methods.

```c

/* In shape.h */
typedef struct Point Point;
struct Point {
	double x, y;
};

typedef struct Shape Shape;
struct Shape {
	void (*move)(Shape *self, Point p);
	void (*scale)(Shape *self, double factor);
	void (*rotate)(Shape *self, double degrees);
	void (*redraw)(Shape *self);
};

extern Shape *make_triangle(Point center, double size);
```

In the implementation:

```c

/* In triangle.c */
#include <stdlib.h>
#include "shape.h"

typedef struct Triangle Triangle;
struct Triangle {
	Shape ops;
	Point center;
	Point voffset[3];
};

static void
tri_move(Shape *self, Point p)
{
	Triangle *t = (Triangle *) self;

	t->center = p;
}

static void
tri_scale(Shape *self, double factor)
{
	Triangle *t = (Triangle *) self;
	int i;

	for (i = 0; i < 3; ++i) {
		t->voffset[i].x *= factor;
		t->voffset[i].y *= factor;
	}
}

static void
tri_redraw(Shape *self)
{
	Triangle *t = (Triangle *) self;
	Point c = t->center;
	Point v0 = addpoint(c, t->voffset[0]);
	Point v1 = addpoint(c, t->voffset[1]);
	Point v2 = addpoint(c, t->voffset[2]);

	drawline(v0, v1);
	drawline(v1, v2);
	drawline(v2, v0);
}

Shape triangle_ops = { &tri_move, &tri_redraw, &tri_scale, &tri_rotate };

Shape *
make_triangle(Point center, double size)
{
	Triangle *t = malloc(sizeof (Triangle));

	if (!t)
		return 0;
	t->ops = triangle_ops;
	t->center = center;
	t->voffset[0].x = size * V0X;
	t->voffset[0].y = size * V0Y;
	t->voffset[1].x = size * V1X;
	t->voffset[1].y = size * V1Y;
	t->voffset[2].x = size * V2X;
	t->voffset[2].y = size * V2Y;
	return &t->ops;
}
```

In a client module that uses the interface:

```c

/* In animate.c */
void
pulsate(Shape *s, double period)
{
	double factor = 1.0, step = 0.1;
	int i;
	void (*scale)(Shape *, double) = s->scale;	/* cache method */
	void (*redraw)(Shape *) = s->redraw;

	for (;;) {
		for (i = 0; i < 10; ++i) {
			factor += step;
			(*scale)(s, factor);
			(*redraw)(s);
			sleep(1);
		}
		step *= -1.0;
	}
}
```

### Closures

In Scheme, abstractions carry their environment with them. This is like bundling a function pointer and some data to work with. The data acts like "configuration" data. Can either make it an explicit argument, or create ADT for closure and make "apply_closure" function - breaks from ordinary function syntax.
Much like objects, implemented above, but less constrained in use.

```c

/* In closure.h */
typedef struct Closure Closure;
struct Closure {
	void *(*fn)(void *);
	void *data;
}

inline void *
appclosure(Closure *t)
{
	return (*t->fn)(t->data);
}
```

### Exception handling

Want to be able to raise a certain kind of error to by handled by a designated handler, but with the "linkage" established dynamically.

```c

#include "exception.h"

void
logcommands(char *filename)
{
	if (!(f = fopen(filename)))
		THROW_ERROR(Err_filename);

	CATCH_ERROR(ALL_ERRS) {
		fflush(f);
		fclose(f);
		THROW_ERROR(throwerror);
	} ERRORS_IN {
		while ((x = read_input(stdin)) != EOF) {
			a = compute_result(x);
			print_result(a, f);
		}
	} END_ERROR;
}
```

The implementation is kind of tricky - use of ternary expression to make complicated series of tests into a syntactic expression:

```c

/* In exception.h */

const int maxcatchers = 100;
extern jmp_buf catchers[maxcatchers];
extern volatile int nextcatch;
extern volatile int throwerror;

#define ALL_ERRS 0
#define CATCH_ERROR(E) \
	if ((nextcatch == maxcatchers) \
	? error("too many exception handlers") \
	: (setjmp(catchers[nextcatch++]) == 0) \
		? 0 \
		: ((E) != ALL_ERRS && throwerror != (E)) \
			? longjmp(catchers[--nextcatch]) \
			: 1)
#define ERRORS_IN else
#define END_ERROR do { --nextcatch; } while (0)

#define THROW_ERROR(E) \
	do { \
		throwerr = (E); \
		longjmp(catchers[--nextcatch]); \
	} while (0)
```

### Continuations

Scheme's general continuations will let you resume at any previous location in call tree - cool for escapes, coroutines, backtracking, etc.

Setjmp/longjmp will let you jump up to a known location. Disciplined use can result in catch/throw.

By making stacks explicit, you can do coroutines.

By using a continuation-passing style, you can do arbitrarily cool things using Cheney on the MTA. See paper by Henry Baker.

```c

typedef void *(*Genfun)(void);

void
trampoline(Genfun f)
{
	while (f)
		f = (Genfun) (*f)();
}
```

### Garbage-collected memory

Through explicit maintenance of roots into the GC'd heap, and the types of objects, you can safely ignore free() and the associate bookkeeping.

"Conservative GC" implementations allow GC behavior for standard programs. See work by Hans Boehm, or the commercial product by Geodesic Systems.

Cheney on the MTA gives simple generational capabilities.


## Low-level C


### Bounded memory usage

Ted Drain's idea: have a malloc() substitute profile allocation for a sample run, then can build a dedicated allocator for a heap whose size is known at compile-time.

```c

#include <assert.h>
#include <stdio.h>
#include <stdlib.h>

static size_t allocbytes = 0;

void
printalloc(void)
{
	fprintf(stderr, "Allocated %lu bytes\n", (unsigned long) allocbytes);
}

void
ememinit(void)
{
	int err;

	err = atexit(&printalloc);
	assert(err == 0);
}

void *
emalloc(size_t size)
{
	void *p = malloc(size);

	assert(p);
	allocbytes += size;
	return p;
}
```

### Bounded call stack depth

Trampoline back and manage stack explicitly.
Can also implement a state-machine within a function.

### Data type representation

Structure ordering and offsets - make explicit with arrays.
Byte-level representation - use arrays of characters. Portable binary representations of output.

```c

typedef char ipv4hdr[20];

struct {
	char *fieldname;
	int byteoffset;
	int bitoffset;
	int bitlength;
} ipv4fields[] = {
	{ "vers", 0, 0, 4 },
	{ "hlen", 0, 4, 4 },
	{ "service type", 1, 0, 8 },
	{ "total length", 2, 0, 16 },
	{ "identification", 4, 0, 16 },
	{ "flags", 6, 0, 3 },
	{ "fragment offset", 6, 3, 13 },
	{ "time to live", 8, 0, 8 },
	{ "protocol", 9, 0, 8 },
	{ "header checksum", 10, 0, 16 },
	{ "source ip address", 12, 0, 32 },
	{ "desination ip address", 16, 0, 32}
};
```

## 扩展阅读


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

