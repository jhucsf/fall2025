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

* TODO: which functions are tested

Milestone 2 (85% of the assignment grade):

* TODO: which functions are tested
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
