---
layout: mathjax
title: "Assignment 1: Fixed-Point Arithmetic"
---

Milestone 1: Due Wednesday, Sep 3 by 11 pm (note: at most 48 late hours may be used)

Milestone 2: Due Wednesday, Sep 10 by 11 pm

Assignment type: **Pair**, you may work with one partner

<div class='admonition caution'>
  <div class='title'>Caution!</div>
  <div class='content' markdown='1'>
This is a preliminary assignment description. Details are missing,
and the content could change.
  </div>
</div>

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
  `fixpoint_compare`, `fixpoint_format_hex`, and `fixpoint_parse_hex` functions (65%)
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

## Important Requirement

<div class='admonition caution'>
  <div class='title'>Important!</div>
  <div class='content' markdown='1'>
In your code for this assignment, you must do all integer arithmetic using
`uint32_t` and `uint64_t` values.  You may **not** use operations on integer values
with more than 64 bits, e.g., values belonging to the nonstandard `__uint128_t` type.
  </div>
</div>

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
so by modifying a `fixpoint_t` instance passed as a pointer
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

Note that a `result_t` values is a bitset. There are two possible members,
`RESULT_OVERFLOW` and `RESULT_UNDERFLOW`. If the `RESULT_OVERFLOW` member
is set, it means that the whole part of the result's magnitude did not fit
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
test_compare...passed!
test_format_hex...passed!
test_parse_hex...passed!
All tests passed!
```

If a test assertion fails, you will see a message indiating the source
location of the failed assertion.

## Writing Your Own Unit Tests

The provided unit tests are intended to be useful, but are not
comprehensive. You should add additional unit tests of your own
to cover cases that aren't adequately tested by the provided tests.

<div class='admonition info'>
  <div class='title'>Note</div>
  <div class='content' markdown='1'>
Rather than adding more test code to the existing test functions
in `fixpoint_tests.c`, you should add new test functions with additional
test code. I.e., don't modify the `test_add` function, but instead
add additional functions to test addition (e.g., `test_add_2`.)
  </div>
</div>

In Milestone 2, part of your grade will be based on the quality and
comprehensiveness of the unit tests you write. Your unit tests should
test all of the functions, not just the functions required for
Milestone 2.

## Running and Debugging Tests

By default running the test program with the invocation `./fixpoint_tests`
will run each test function in order. However, you can run just one
test function by naming it on the command line. For example, the invocation

```bash
./fixpoint_tests test_add
```

will execute only the `test_add` test function.  This is useful when
you are focusing on getting a specific test to pass. Also, because C
is a memory-unsafe language, it's possible for an earlier test to corrupt
the state of the program in a way that could affect the execution of
later tests, so in general running only one test function will produce
a more trustworthy result than running all of the test functions.

If a unit test function fails, you can use `gdb` to debug the test function.
For example, let's say that the unit test `test_add` is failing.
Start by invoking `gdb` on the test program:

```bash
gdb ./fixpoint_tests
```

At the `gdb` prompt, set a breakpoint at the beginning of
`test_add`, then run the program:

```
(gdb) break test_add
Breakpoint 1 at 0x3b69: file fixpoint_tests.c, line 187.
(gdb) run
Starting program: /space/daveho/git/csf-fall2025-private/src/csf_assign01_solution/fixpoint_tests
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
test_init...passed!
test_get_whole...passed!
test_get_frac...passed!
test_is_negative...passed!
test_negate...passed!
test_add...
Breakpoint 1, test_add (objs=0x55555555d2a0) at fixpoint_tests.c:187
187     void test_add( TestObjs *objs ) {
```

At this point, you can use the `next` and `step` commands to execute the
code.  By stepping into the function call associated with the assertion
failure, you can trace the execution and inspect data in order to pinpoint
the cause of the issue.

## Memory correctness

In all C and C++ code you write, we expect that there are no memory errors,
including

* invalid reads
* invalid writes
* uses of uninitialized values
* memory leaks

We expect you to use the [valgrind](https://www.valgrind.org/) memory trace tool
to check program execution for occurrences of memory errors. For this assignment,
run

```bash
valgrind --leak-check=full ./fixpoint_tests
```

There should be no dynamic memory errors, and assuming that all of
the unit tests pass, there should be no memory leaks.

## Hints and Suggestions

This section has some advice on implementing specific operations.

### `fixpoint_negate`, `fixpoint_is_negative`

Keep in mind that 0 is not negative. If the magnitude of a `fixpoint_t` value is 0,
then its `negative` field should be set to `false`.

Note that `fixpoint_init` may assume as a precondition that its `negative` parameter
will not be `true` if the `whole` and `frac` parameters are both 0.

### `fixpoint_add`

You can break this operation into two cases: adding values with the same sign, and
adding values with different signs.

If the values being added have the same sign, then the result's magnitude is the
sum of the magnitudes of the values being added, and the result's sign is the same
as the sign of the values being added. Note that overflow is possible.
If overflow occurs, the result value's whole part should be the truncation of
the correct whole part, i.e., just the low 32 bits of the correct whole part.

Addition of magnitudes can be implemented as follows:

1. Add the fractional parts to compute the result's fractional part
2. If the addition of the fractional parts overflowed, make a note to
   carry a 1 into the addition of the whole parts
3. Add the whole parts to compute the result's fractional part,
   carrying a 1 if needed (per Step 2)

If Step 3 overflowed, then the overall addition overflowed.

If the values being added have different signs, then the result's magnitude
is the difference computed by subtracting the magnitude of the addend with
the smaller magnitude from the magnitude of the addend with the larger
magnitude. The result's sign is the sign of the addend with the larger
magnitude. Note that overflow is not possible when adding `fixpoint_t` values
with different signs.

Subtraction of magnitudes can be implemented similarly to the approach
for adding magnitudes described above. You'll need to consider how to detect
whether a borrow of 1 from the whole part is needed.

### `fixpoint_sub`

Since `fixpoint_add` already handles negative values, `fixpoint_sub` can be
implemented by calling `fixpoint_negate` and `fixpoint_add`, with the idea being

$$a - b = a + -b$$

### `fixpoint_mul`

Multiplication can be approached in the following way. Think of the values being
multiplied (the factors) as being 64-bit integers, with the whole part being the high 32 bits
and the fractional part being the low 32 bits. The 64 bit factors should be multiplied
to yield a 128-bit product.

The following comment from the reference solution outlines a possible way to
implement the multiplication:

```c
// The idea here is that we'll multiply the two 64 bit magnitudes
// of left and right to produce a 128 bit product. The middle 64 bits
// of the product of the magnitudes becomes the magnitude of the
// result product. However, if any of the low 32 bits or high 32 bits of
// the 128-bit magnitude product are non-zero, it means the numeric
// product can't be represented exactly.
//
//   WX    left magnitude
// * YZ    right magnitude
// ----
//  PRS    PRS = WX * Z
// TUV     TUV = WX * Y
//
// Full (128-bit) magnitude of product is PRS + ( TUV << 32 )
```

### `fixpoint_format_hex`

You should be able to leverage the [`snprintf`](https://cplusplus.com/reference/cstdio/snprintf/)
function to do most of the work for `fixpoint_format_hex`. A couple things to
think about:

* You'll need to make sure that the hex digits of the fractional part
  are placed correctly. For example, if the `fraction` field of a
  `fixpoint_t` value is equal to 1, then the correct hex digits for
  the fraction part are `00000001`, not just `1`
* You will likely need to write some code to trim trailing zeroes
  from the fractional part

### `fixpoint_parse_hex`

You should be able to leverage the [`sscanf`](https://cplusplus.com/reference/cstdio/sscanf/)
function to do much of the work needed for `fixpoint_parse_hex`. A couple things
to think about

* You'll want to know how many hex digits were matched for the whole
  and fractional part; the `%n` conversion is useful for this purpose,
  since it lets you capture how many characters from the string have
  been used at a particular point in the format
* You'll need to make sure that the hex digits of the fractional part
  are incorporated into the fraction value at the correct place:
  for example, a string ending in `.8` means that the `fraction` field
  should be set to `0x80000000`, not `0x8`
* Keep in mind that the whole and fractional parts should be represented
  by at most 8 hex digits; if there are more than 8 hex digits in either
  the whole part or the fractional part, then the formatted string is
  invalid

## Submitting

Before you submit, edit the `README.txt` file so that it contains your
names, and briefly summarizes each of your contributions to the submission
(i.e., who worked on what functionality.) This may be very brief if you
did not work with a partner.

To submit your work:

Run the following command to create a `solution.zip` file:

```
make solution.zip
```

Upload `solution.zip` to [Gradescope](https://www.gradescope.com/)
as **Assignment 1 MS1** or **Assignment 1 MS2**, depending on which
milestone you are submitting.

Please check the files you uploaded to make sure they are the ones you
intended to submit.

## Autograder

When you upload your submission to Gradescope, it will be tested by
the autograder, which executes unit tests for each required function.
Please note the following:

* If your code does not compile successfully, all of the tests will fail
* The autograder runs `valgrind` on your code, but it does *not* report
  any information about the result of running `valgrind`: points will be
  deducted if your code has memory errors or memory leaks!
