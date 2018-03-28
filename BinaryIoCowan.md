## Input and output

The default value of *port* is the current input or output port, as appropriate.  It is an error if the ports passed to these procedures are not binary ports.  The [type] values are defined in [BytevectorsCowan](BytevectorsCowan.md).

`(read-[type] ` [[|*port* ]]`)`

Reads the appropriate number of bytes from *port* in native byte order and returns a number of the appropriate type (exact integer or inexact real or complex number).

`(read-[type]le ` [[|*port* ]]`)`

Reads the appropriate number of bytes from *port* in little-endian byte order and returns a number of the appropriate type (exact integer or inexact real or complex number).

`(read-[type]be ` [[|*port* ]]`)`

Reads the appropriate number of bytes from *port* in big-endian ("network") byte order and returns a number of the appropriate type (exact integer or inexact real or complex number).

`(write-[type] `*number* [[|*port* ]]`)`

Writes *number* to *port* in the appropriate format using native byte order.

`(write-[type]le `*number* [[|*port* ]]`)`

Writes *number* to *port* in the appropriate format using little-endian byte order.

`(write-[type]be `*number* [[|*port* ]]`)`

Writes *number* to *port* in the appropriate format using big-endian ("network") byte order.

`(read-ber-integer ` [[|*port* ]]`)`

Reads a [BER](https://en.wikipedia.org/wiki/X.690#BER_encoding)-encoded integer of arbitrary size from *port* and returns it as an exact integer.

`(write-ber-integer ` *exact-integer* [[|*port* ]]`)`

Writes *exact-integer* using [BER](https://en.wikipedia.org/wiki/X.690#BER_encoding) encoding to *port*.

`(read-utf8-string ` *k* [[|*port* ]]`)`

`(read-utf16-le-string ` *k* [[|*port* ]]`)`

`(read-utf16-be-string ` *k* [[|*port* ]]`)`

Read *k* bytes from *port*, interpreting them as a sequence of characters encoded in UTF-8, UTF-16LE, or UTF-16BE, and return the sequence as a string.  It is an error if the implementation forbids any of the characters in strings, except that if the implementation forbids null characters in strings and the final character is a null, then it is discarded.

`(write-utf8-string ` *string* [[|*port* ]]`)`

`(write-utf16-le-string ` *string* [[|*port* ]]`)`

`(write-utf16-be-string ` *string' [[|*port* ]]`)`

Convert the characters of *string* to UTF-8, UTF-16LE, or UTF-16BE, and write the bytes to *port*.  It is an error if the implementation forbids any of the characters in strings.

`(read-utf8-string-until `*byte* [[|*port* ]]`)`

Read bytes from *port* until a byte equal to *byte* is read.  Interpret all the other bytes as a sequence of characters encoded in UTF-8, and return two values, the character sequence as a string and the terminating byte as a character.

## Implementation

See [SRFI 56](http://srfi.schemers.org/srfi-56/srfi-56.html) for implementation.


