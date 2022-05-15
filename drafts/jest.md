# JSON Encoded Syntax Tree

Jest is a portable format for representing syntax trees that encodes into a simple JSON structure. 

Jest does not define types or custom operators or loop constructs.
Instead, Jest provides a runtime selection mechanism which would allow different runtimes to provide custom functionality.

Jest does not define semantics of operations (such as overflows, errors etc).
Instead, Jest leaves these to the runtime mechanism to be defined separately.

Goals:

1. Provide a simple, portable, fixed intermediate representation for a variety of use cases: SQL-like data operators, Strongly-typed serialization, Build and Task pipelines, Configuration, Unified Document formats etc.
2. Provide a platform for building debuggers, type checkers, static and dynamic optimizers, compilers/interpreters without tightly coupling these to any specific architecture.
3. Provide a platform for implementing convergent/stream processing at the language level.

## Encoding Literals

Only the following atomic literal types are supported direcftly:  strings, numbers, booleans and `null`. 

These are encoded into JSON directly.

## Encoding Names/Identifiers

Names (such as for variables) are encoded into a JSON array with name as the single element of this array.

For example, the variable `x` would be encoded as `["x"]`

Note that any arbitrary JSON string may be present as the identifier name even though most runtimes would
restrict the character set allowed in a name.  This is useful to define system identifiers
that are not accessible from the higher level language directly (such as `"runtime.foo"`).

## Encoding Operator Expressions

Operator expressions are represented via a simple array: the first element specifies the operator
and the rest of the element are arbitrary Jest expressinos encoded into their corresponding JSON
forms.

For example, the expression `x + 2` would be encoded as `["+", ["x"], 2]`.

The following are the standard operators:

1. Arithmetic operators: `"+" "-" "*" "/"` (all binary except minus which can be binary or unary).
2. Logical Operators: `"&" "|" "!"` (all binary except not which is unary).
3. Comparision Operator: `"=" "!=" "<=" "<" ">" ">="` (all binary)
4. Field select operator: `"."`
5. Pair operator: `":"` (used for identifying named arguments or object key/value pairs)
6. Call operator: `"()"` (used for function calls).
7. List operator: `"[]"` (used to create list of items with operands specifying the items).
8. Object operator: `"{}"` (used to create objects with operands specifying key and value in sequence).
9. Identity/Group operator: `""` (used to indicate special grouping, see example below)

Exmaples:

1. `x.y < 33` should be encoded as `["<", [".", ["x"], ["y"]], 33]`.  Note that the field `y` is
encoded as `["y"]` to distinguish from langauges which may support `x."y"` which would be encoded
as `[".",["x"],"y"]`.

2. The identity/group operator is a special case for languages that support expressions like `x.(y)`
which would be encoded as `[".",["x"],["",["y"]]]`.  This is also useful if the group structure is
useful for debugging/annotations etc.  The empty string operator effectively just means that the
actual value is the first operand.

3. The call operator expects the first operand to be the function: `f(x)` is encoded as `["()", ["f"], ["x"]]`.
This also allows the operand to be an expression itself.  For example: `f.g(x)` is encodeded as
`["()", [".",["f"],["g"]], ["x"]]`

4. The list operator allows creating a list of items: `[1, 2, 3]` is encoded as `["[]",1, 2, 3]`

5. The object operator is like the list operator, allowing creating an object from the key value pairs.
So `{x: 5, y: 3}` is encoded as `["{}", "x", 5, "y", 3]`.  Note that this form allows non-string keys or
even whole expressions as keys.

6. The pair operator is meant to capture named arguments.  So, `f(x, y: 42)` is encoded as 
`["()", ["f"], ["x"], [":",["y"],42]]`.  Note that the pair expression allows arbitrary expressions
for keys.  In particular, if a language supported `f(x, (y + 1): 5)`, this would be encoded as:
`["()", ["f"], [":", ["+", ["y"], 1], 5]]`

### Alternate simpler forms

Several operator expressions have simpler forms:

1. The function call operator can be omitted altogether if the first operand is not a string.  So, `f(x)` 
can be encoded as `[["f"], ["x"]]`.  Since all operator forms require the first element to be a string,
if the first element is not a string, it unambiguously can be treated as a function call expression without
the call operator.  But note that `f()` cannot be encoded this way as this short form requries atleast one
non-string operand.

2. The list expression can be expressed via a double array: `[1, 2, 3]` can be encoded as `[[1, 2, 3]]`.  The
only other single element array encoding used in Jest is for identifiers for which the first element is a string.
So, an array with the a single array element within unambiguously represents a list.

3. The object expression can be simplified in the case where all the keys are unique strings: `{"x": y}` can be
encoded as `[{"x": ["y"]}]`.  Note that this is just the regualr JSON object but this is still wrapped with an
array to keep this consistent with the list expression format above.

4. Pair expressions can be simplified by using regular JSON objects.  So, `f(x, y: 5)` can be encoded as
`["f", ["x"], {"y": 5}]`.  Note that this is only possible for the cases where the key in the expression is a
simplie identifier.  So, `f(x, (y + 1): 5)` (assuming a language had such a construct) cannot be expressed
using this short form.

## Control flow

Control flows in general are expected to be implemented using functions:

1. If/then/else is expressed as a `if` function with two or more arguemnts: `if(condition, then, else)` or 
`if(condition, then)`.  So, `if(x < 5) then y` would be encoded as `[["runtime.if"], ["<", ["x"], 5], ["y"]]`.

2. Multiple if statements can be combined into one if with more pairs of condition/then operands with the 
optional last operand specifying the final else.

3. Switch statements are encoded into a `case` function: `[["runtime.case"], primary_operand, value1, then1, value2, then2... else]`.
So, switch statements are like `if` except that the then part includes the `value` + the `then` expression.

There are no loop constructs - runtimes may expose those via functions though.

## Scope

A scope generally defines a set of name/value expressions and a primary sub expression (with all sub expressions allowed
to refer to other names defined in this scope or a higher lexical scope).  This is encoded via a scope function
which expects one parameter and a set of named arguments for each of the name/value pairs.

That is, `{ let x = 52, y = 23; x + y }` can be treated like `scope(x + y, x: 52, y: 23)` and encoded as:
`[["runtime.scope"], ["+",["x"],["y"]], {"x": 52}, {"y": 23}]`

## Runtime Checks

It is useful to be able to check if the runtime has specific support.  For instance, if the runtime has support
for loops.  All these checks are handled via a simple `runtime.check` function which works syntactically like an
`if` expression except that:

1. These are expected to be evaluated during compliation and as such cannot use anything not available during
compilation.

2. The condition sub-expression can use a set of names exposed by the runtime.  If the condition accesses a 
name not defined by the runtime, the whole condition is treated as a false (instead of raising errors) for 
forward compatiblity.

For example, to check if a runtime supports loops, something like `runtime.check(has_loops, code_with_loops, code_with_map_reduce)` 
can be used.  Runtimes which support loops can expose a `has_loops` boolean variable.

