# freeradius-sbuff
A separate repository for the sbuff string manipulation library

## Introduction

Sbuffs are used internally within FreeRADIUS for safe string printing and parsing.

When used correctly sbuffs avoid many of the pitfalls of C string handling including buffer 
overflows and buffer overruns.

## Parsing

Sbuffs can have an infinite number of children.

When parsing, each sbuff can control how much of a string are available to decendent sbuffs
and perform transformations using intermediary buffers.  The shallowest (top level) sbuff 
can also pull data from streaming sources such as sockets and files.

These operations are driven by demands for data.  When one of the `sbuff_out_*` functions is
called, it will attempt to read however many bytes it needs to complete the operation from
the sbuff that was passed into it.

