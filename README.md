# sbuff
A separate repository for the FreeRADIUS sbuff string manipulation library.

## Introduction

Sbuffs are used internally within FreeRADIUS for safe string printing and parsing.

When used correctly sbuffs avoid many of the pitfalls of C string handling including buffer 
overflows and reads into uninitialised memory.

## sbuff variants

There are four sbuff variants.

- `I/O` sbuffs which read from and write to network sockets or files.  These sbuffs create a
  'window' into the stream when reading, and (optionally) buffer data when writing.
- `transform` sbuffs, which apply transformations to data.  This transformation is usually
  escaping or unescaping data.  Transform sbuffs create a new window and stream, and all
  positions downstream of this sbuff are relative to the new stream.
- `terminal` sbuffs prevent reading past a sequence of characters in the stream.
- `marker` sbuffs which prevent the window of a `transform` or `I/O` sbuff going out of range.
  i.e. pinning a certain range of data within a parent's window.

## memory usage

Sbuffs which create new windows, necessarily need 

## Reading/Parsing

Sbuffs can have an infinite number of children.

When parsing, each sbuff can control how much of a string are available to decendent sbuffs
and perform transformations using intermediary buffers.  The shallowest (top level) sbuff 
can also read/write data to/from streaming sources such as sockets and files.

These operations are driven by demands for data.  When one of the `sbuff_read_*` functions is
called, it will attempt to read however many bytes it needs to complete the operation from
the sbuff that was passed into it, which may in turn attempt to read data from the previous
sbuff in the chain.

## Error indication

sbuff printing and parsing functions always return a `sbuff_slen_t` (typedef of `ssize_t`).

### Reading

- `>=0` the number of bytes consumed from the `sbuff_t`.
- `<0` negative offset of where a parse error occurred.  Given the string `foo`, and a return
  value of `-1`, a parse error would be indicated at offset `0` (`f`).

### Writing

- `>=0` the number of bytes written to the `sbuff_t`.
- `<0` negative offset indicating how many additional bytes would be required in the sbuff
  to write the complete string.  A return value of `-5` would mean `5` more bytes would be
  required.

