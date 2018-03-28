## Rationale

Many procedures in many libraries accept arguments from a finite set (usually a fairly small one), or subsets of a finite set to describe a certain mode of operation, or several flags to describe a mode of operation. Offering a default policy for dealing with such values fosters portable and readable code, much as records do for compound values, or multiple values for procedures computing several values.

In Lisp-family languages, it is traditional to use symbols and lists of symbols for this purpose.  Symbols have at least two disadvantages: they are not "type-safe", in the sense that a single symbol may be used in more than one logically independent universe of flags; and in Scheme symbols do not have associated values.  In C-family languages, enumerations have names and numeric values, by default consecutive values, but often powers of two or something externally dictated.  However, the name is not accessible at runtime, and enumeration types are not really disjoint from integer types (in C++ they are statically distinct).

This SRFI instead provides something related to the *enums* of Java version 5 and later.  These are objects of a type disjoint from all others that are grouped into *enumeration types* (called *enum classes* in Java).  In Java, each enumeration type is allowed to declare the number and types of values associated with each object, but in this SRFI an enumeration object has exactly one value; this is useful when translating from C to record the numeric value, but probably has other uses as well.  The universes of R6RS correspond to enum types, but they are not reified.

In this SRFI, each enum has four properties:  the enum type to which it belongs, its name (a symbol), its ordinal (an exact integer), and its value (any object).  An enum type provides access to all the enums that belong to it by name or ordinal.

Specialized sets of enums and specialized mappings from enums to arbitrary values will be described in future SRFIs.  It is also possible to use general-purpose [SRFI 113](http://srfi.schemers.org/srfi-113/srfi-113.html) sets and bags, hash tables from [SRFI 125](http://srfi.schemers.org/srfi-125/srfi-125.html) or elsewhere, and [SRFI 146](http://srfi.schemers.org/srfi-146/srfi-146.html) mappings for the same purposes.

## Specification

### Constructor

`(make-enum-type `*list*`)`

Returns a newly allocated enum type containing a fixed set of newly allocated enums.  Both enums and enum types are immutable, and it is not possible to create an enum except as part of creating an enum type.

The elements of *list* are either symbols or two-element lists, where each list has a symbol as the first element and any value as the second element.  Each list element causes a single enum to be generated, whose name is specified by the symbol.  It is an error unless all the symbols are distinct within an enum type.  The position of the element in *list* is the ordinal of the corresponding enum, so ordinals within an enum type are also distinct.  If a value is given, it becomes the value of the enum; otherwise the enum's value is the same as the ordinal.

### Enum accessors

`(enum-type `*enum*`)`

Returns the enum type to which *enum* belongs.

`(enum-name `*enum*`)`

Returns the name (symbol) associated with *enum*.

`(enum-ordinal `*enum*`)`

Returns the ordinal (exact integer) associated with *enum*.

`(enum-value `*enum*`)`

Returns the value associated with *enum*.

### Property finders

`(enum-name->enum `*enum-type symbol*`)`

If there exists an enum belonging to *enum-type* named *symbol*, return it; otherwise return `#f`.

`(enum-ordinal->enum `*enum-type exact-integer*`)`

If there exists an enum belonging to *enum-type* whose ordinal is *exact-integer*, return it; otherwise return `#f`.

The following convenience procedures provide enum-finding followed by access to a property.

`(enum-name->ordinal `*enum-set symbol*`)`

Return the ordinal of the enum which belongs to *enum-type* and whose name is *symbol*.  It is an error if there is no such enum.

`(enum-name->value`*enum-set symbol*`)`

Return the value of the enum which belongs to *enum-type* and whose name is *symbol*.  It is an error if there is no such enum.

`(enum-ordinal->name `*enum-set exact-integer*`)`

Return the name of the enum which belongs to *enum-type* and whose ordinal is *exact-integer*.  It is an error if there is no such enum.

`(enum-ordinal->value `*enum-set exact-integer*`)`

Return the value of the enum which belongs to *enum-type* and whose ordinal is *exact-integer*.  It is an error if there is no such enum.

## Enumeration types

`(enum-type-size `*enum-type*`)`

Returns an exact integer equal to the number of enums in *enum-type*.

`(enum-min `*enum-type*`)`

Returns the enum belonging to *enum-type* whose ordinal is 0.

`(enum-max `*enum-type*`)`

Returns the enum belonging to *enum-type* whose ordinal is equal to the number of enums in the enum type minus 1.

`(enum-type-enums `*enum-type*`)`

Returns a list of the enums belonging to *enum-type* ordered by increasing ordinal.

`(enum-type-names `*enum-type*`)`

Returns a list of the names of the enums belonging to *enum-type* ordered by increasing ordinal.

`(enum-type-values `*enum-type*`)`

Returns a list of the values of the enums belonging to *enum-type* ordered by increasing ordinal.

### Enums

`(enum-next `*enum*`)`

Returns the enum that belongs to the same enum-type as *enum* and has an ordinal one greater than *enum*.  Returns `#f` if there is no such enum.

`(enum-prev `*enum*`)`

Returns the enum that belongs to the same enum-type as *enum* and has an ordinal one less than *enum*.  Returns `#f` if there is no such enum.

`(enum=? `*enum* ...`)`

Returns `#t` if all the arguments are the same enum, and `#f` otherwise.  It is an error to apply `enum=?` to enums belonging to different enum types.

`(enum<? `*enum* ...`)`

`(enum>? `*enum* ...`)`

`(enum<=? `*enum* ...`)`

`(enum>=? `*enum* ...`)`

These predicates return `#t` if their arguments are enums whose ordinals are in increasing, decreasing, non-decreasing, and non-increasing order respectively, and `#f` otherwise.  It is an error unless all of the arguments belong to the same enum type.

### Syntax

`(define-enum-type `<name> <symbol-or-list>`)`

Creates an enum type as if with `make-enum-type`, passing the <symbol-or-list> arguments to it.  In addition, the identifier <name> is defined as the enum type, and the <symbol> identifiers are defined as the individual enums.  So `(define-enum-type direction north south (other 100))` is equivalent to the following definitions:

```
(define direction (make-enum-type '(north south (other 100))))
(define north (enum-name->enum direction north))
(define south (enum-name->enum direction south))
(define other (enum-name->enum direction other))
```

### Comparators

`(make-enum-comparator `*enum-type*`)`

Returns a [SRFI 128](http://srfi.schemers.org/srfi-128/srfi-128.html) comparator suitable for comparing enums that belong to *enum-type*.  The comparator contains both an ordering predicate and a hash function, and orders enums based on their ordinal values.

