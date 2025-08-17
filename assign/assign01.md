---
layout: mathjax
title: "Assignment 1: Fixed-Point Arithmetic"
---

Milestone 1: Due Wednesday, Sep 3 by 11 pm

Milestone 2: Due Wednesday, Sep 10 by 11 pm

Assignment type: **Pair**, you may work with one partner

# Overview

In this assignment, you will implement a simple C library providing arithmetic operations
on a 64-bit fixed-point data type.

This is a substantial assignment! We strongly recommend that you start working
on it as early as possible, and plan to make steady progress rather than waiting
until the last minute to complete it.

To complete the assignment, you will implement a number of C functions,
and you will also write unit tests to test these functions.

## Mileestones, Grading Criteria

Milestone 1 (15% of the assignment grade):

* Implementation of `fixpoint_init`, `fixpoint_get_whole`, `fixpoint_get_frac`,
  `fixpoint_is_negative`, and `fixpoint_negate` functions (15%)

Milestone 2 (85% of the assignment grade):

* Implementation of `fixpoint_add`, `fixpoint_sub`, `fixpoint_mul`,
  and `fixpoint_compare` functions (65%)
* Comprehensiveness and quality of your unit tests (10%)
* Design and coding style (10%)

<div class='admonition caution'>
  <div class='title'>Important!</div>
  <div class='content' markdown='1'>
Milestone 1 is intended as a warm-up, since you might not have
written C code in a while. For that reason, it is a very lightweight milestone.
Milestone 2 will require significantly more work.
  </div>
</div>

## Getting started

To get started, download [csf\_assign01.zip](csf_assign01.zip), which contains the
skeleton code for the assignment, and then unzip it.

Note that you can download the zipfile from the command line using the `curl` program:

```bash
curl -O https://jhucsf.github.io/fall2025/assign/csf_assign01.zip
```

Note that in the `-O` option, it is the letter "O", not the numeral "0".

# Unsigned integers, fixed point numbers

You should be familiar with unsigned integer data types from your previous
experience programming in C and C++. (And, of course, we are covering the properties
of machine-level integer data types in the course.)

In C, the `uint32_t` data type is an unsigned 32-bit integer data type,
and it can represent integer values in the range $$0$$ to $$2^{32}-1$$, inclusive.

A *fixed-point* numeric representation is one that is internally represented as
an integer — a sequence of bits — but for which we "pretend" that there is a
decimal point at a fixed position in the bit sequence.

For example, we could pretend that a `uint32_t` value is really a fixed point
representation with 16 bits to the left of the decimal point, and 16 bits to
the right of the decimal point. This could be called a "16.16" fixed-point
representation. The bits to the left of the decimal point represent the *whole part*
of the value, and would range from 0 to $$2^{16}-1=65,535$$ inclusive.
The bits to the right of the decimal point represent the *fractional part*
of the value. Just like the bits to the left of the decimal point, the
value of each bit represents a power of 2. However, to the right of the
decimal point, the bits represent *negative* powers of two. For example,
the bit immediately to the right of the decimal point represents the
value $$2^{-1}=\frac{1}{2}$$. For example, the value $$\frac{1}{2}$$
in 16.16 fixed point would be written

> 0000000000000000.1000000000000000

## The `fixpoint_t` type

In this assignment, you will implement operations on the `fixpoint_t` data
type, which is defined like this:

```c
typedef struct {
  uint32_t whole;
  uint32_t frac;
  bool negative;
} fixpoint_t;
```

This is a "32.32" fixed point type. The `whole` field represents 32 bits to
the left of the decimal point, and the `frac` field represents 32 bits to the
right of the decimal point.

## Arithmetic Operations

The `fixpoint.h` header file declares the following public API functions
to perform operations on `fixpoint_t` instances:

* `fixpoint_init`
* `fixpoint_get_whole`
* `fixpoint_get_frac`
* `fixpoint_is_negative`
* `fixpoint_negate`
* `fixpoint_add`
* `fixpoint_sub`
* `fixpoint_mul`
* `fixpoint_compare`

Each public API function has a detailed documentation comment describing the
expected behavior of the function.

Your primary task in this assignment is to add code to the `fixpoint.c` source
file to implement each of the public API functions.

All of the operations that produce a result `fixpoint_t` value do
so by modifying a `fixpoint` instance passed as a pointer
(the `result` parameter.)

## `result_t`

Several of the operations aren't guaranteed to produce a mathematically
exact result. Specifically, the functions

* `fixpoint_add`
* `fixpoint_sub`
* `fixpoint_mul`

might yield a value that can't be exactly represented by the `fixpoint_t`
type. These functions return a `result_t` value to indicate whether the
computed result was represented exactly, or whether overflow or underflow
occurred.

Note that `result_t` values are a bitset. There are two possible members,
`RESULT_OVERFLOW` and `RESULT_UNDERFLOW`. If the `RESULT_OVERFLOW` member
is set it means that the whole part of the result's magnitude did not fit
in 32 bits.  If the `RESULT_UNDERFLOW` member is set, it means that the
fractional part of the result's magnitude did not fix in 32 bits.
Note that underflow is only possible in the `fixpoint_mul` operation.

There are four possible `result_t` values:

* `RESULT_OK`: the exact mathematical result was computed
* `RESULT_OVERFLOW`: overflow occurred
* `RESULT_UNDERFLOW`: underflow occurred
* `RESULT_OVERFLOW|RESULT_UNDERFLOW`: both overflow and underflow occurred

## Unit Tests

The source file `fixpoint_tests.c` contains unit tests for the `fixpoint_t`
data type and its operations.

A minimal but useful set of unit tests are provided for you. These tests
play an important role in defining how the public API functions should work,
since they provide explicit examples of how correct implementations of the
public functions are expected to behave.

To compile and run the unit test program:

```bash
# compile the unit test program
make clean
make depend
make -j

# run the unit test program
./fixpoint_tests
```

When you run the unit test program, you should see output something like
the following:

```text
test_init...passed!
test_get_whole...passed!
test_get_frac...passed!
test_is_negative...passed!
test_negate...passed!
test_add...passed!
test_sub...passed!
test_mul...passed!
All tests passed!
```

If a test assertion fails, you will see a message indiating the source
location of the failed assertion.
