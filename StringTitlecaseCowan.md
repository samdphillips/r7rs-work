## Rationale

This SRFI defines R7RS-style Unicodely correct `char-title-case?`, `char-titlecase`, and `string-titlecase` procedures similar to those specified in [R6RS](http://www.r6rs.org/final/html/r6rs-lib/r6rs-lib-Z-H-2.html#node_sec_1.2) and [SRFI 13](http://srfi.schemers.org/srfi-13/srfi-13.html).  These didn't seem useful enough to include in the small language, but are a useful building block for future SRFIs.  The algorithm does not depend on the availability of full Unicode, however, and will work just as well with a partial or even purely ASCII repertoire.

Consider the string `ﬂoo bar`, which begins with a ligature of the characters `fl`.  The Unicode way of titlecasing this string is to treat the ligature the same as the two characters `fl`, in which case the result is `Floo Bar`.  However, by the strict letter of R6RS, the `ﬂ` character must be passed to `char-titlecase`, which in this case will return its argument unchanged, and the result is `ﬂoo Bar`.  What is more, if the `ﬂ` character is not even seen as a casing letter, then the result will be `ﬂOo Bar`.  Different Schemes show all of these behaviors.

## Specification

`(char-title-case? `*char*`)`

Returns `#t` if *char* is a character with the Unicode property Titlecase, and `#f` otherwise.  (Not in R6RS.)

`(char-titlecase `*char*`)`

Returns the titlecase equivalent of *char*, if that character exists in the implementation, and *char* otherwise.  Note that the titlecase equivalent is not typically a titlecase character: for most values of *char*, it is either the uppercase equivalent of *char* or else *char* itself.  Note that language-sensitive mappings are not used.  (The same as the R6RS equivalent.)

`(string-titlecase `*string*`)`

This procedure applies the Unicode full string lowercasing algorithm to its argument.  However, any character preceded by a non-cased character, or which is the first character of *string*, is processed by a different algorithm.  If such a character has a multi-character titlecase mapping specified by Unicode, and all the characters of the mapping are supported by the implementation, then it is replaced by that mapping.  Otherwise, it is replaced by its single-character titlecase mapping as if by `char-titlecase`.  The result of the application of these algorithms is returned.

In certain cases, the result differs in length from the argument. If the result is equal to the argument in the sense of `string=?`, the argument may be returned. Note that language-sensitive mappings are not used.  (The R6RS version does not make use of multi-character titlecase mappings.)