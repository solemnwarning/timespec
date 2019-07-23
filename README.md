# Introduction

This library provides functions for working with timespec structures.

It aims to provide a comprehensive set of functions with well-defined behaviour
that handle all edge cases (e.g. negative values) in a sensible manner.

Negative values are allowed in the tv_sec and/or tv_usec field of timespec
structures, tv_usec is always relative to tv_sec, so mixing positive and
negative values will produce consistent results:

    { tv_sec = 1,  tv_nsec = 500000000  } ==  1.5 seconds
    { tv_sec = 1,  tv_nsec = 0          } ==  1.0 seconds
    { tv_sec = 1,  tv_nsec = -500000000 } ==  0.5 seconds
    { tv_sec = 0,  tv_nsec = 500000000  } ==  0.5 seconds
    { tv_sec = 0,  tv_nsec = 0          } ==  0.0 seconds
    { tv_sec = 0,  tv_nsec = -500000000 } == -0.5 seconds
    { tv_sec = -1, tv_nsec = 500000000  } == -0.5 seconds
    { tv_sec = -1, tv_nsec = 0          } == -1.0 seconds
    { tv_sec = -1, tv_nsec = -500000000 } == -1.5 seconds

Furthermore, any timespec structures processed or returned by library functions
are normalised according to the rules defined by `timespec_normalise()`.

A test program for your platform can be produced by compiling with `-DTEST`.

This is public domain. Feel free to embed it in your software if it meets your
needs.

# Functions

## Maths functions

`struct timespec timespec_add(struct timespec ts1, struct timespec ts2)`

Returns the result of adding two timespec structures.

`struct timespec timespec_sub(struct timespec ts1, struct timespec ts2)`

Returns the result of subtracting ts2 from ts1.

`struct timespec timespec_mod(struct timespec ts1, struct timespec ts2)`

Returns the remainder left over after dividing ts1 by ts2.

## Comparison functions

`bool timespec_eq(struct timespec ts1, struct timespec ts2)`

Returns true if the two timespec structures are equal.

`bool timespec_gt(struct timespec ts1, struct timespec ts2)`

Returns true if ts1 is greater than ts2.

`bool timespec_ge(struct timespec ts1, struct timespec ts2)`

Returns true if ts1 is greater than or equal to ts2.

`bool timespec_lt(struct timespec ts1, struct timespec ts2)`

Returns true if ts1 is less than ts2.

`bool timespec_le(struct timespec ts1, struct timespec ts2)`

Returns true if ts1 is less than or equal to ts2.

## Conversion funtions

`struct timespec timespec_from_double(double s)`

Converts a fractional number of seconds to a timespec.

`double timespec_to_double(struct timespec ts)`

Converts a timespec to a fractional number of seconds.

`struct timespec timespec_from_timeval(struct timeval tv)`

Converts a timeval to a timespec.

`struct timeval timespec_to_timeval(struct timespec ts)`

Converts a timespec to a timeval.

`struct timespec timespec_from_ms(long milliseconds)`

Converts an integer number of milliseconds to a timespec.

`long timespec_to_ms(struct timespec ts)`

Converts a timespec to an integer number of milliseconds.

## Normalisation

`struct timespec timespec_normalise(struct timespec ts)`

Returns a normalised version of a timespec structure, according to the following
rules:

1. If tv_nsec is >=1,000,000,000 or <=-1,000,000,000, flatten the surplus
   nanoseconds into the tv_sec field.

2. If tv_sec is >0 and tv_nsec is <0, decrement tv_sec and roll tv_nsec up to
   represent the same value on the positive side of the new tv_sec.

3. If tv_sec is <0 and tv_nsec is >0, increment tv_sec and roll tv_nsec down to
   represent the same value on the negative side of the new tv_sec.
