# Compact JSON

Compact JSON is an encoding of valid JSON into a more human readable and compact version of JSON.  

# JSON to Compact JSON

The following rules specify how to convert from regular JSON to compact JSON:

1. Remove all whitespaces unless they are within a JSON string.

2. Replace all commas that are used to separate items in an array or object with a single space.

3. Remove quotes from JSON strings if the contents of the strings meet all the following conditions:
     1. Does contain any whitespace characters.
     2. Does not contain square brackets or curly braces or a comma or a colon.
     3. Is not `true` or `false` or `null`.
     4. Is not a JSON number.
     5. Does not require escaping when encoded as JSON.


# Compact JSON to JSON

The following algorithm specifies how to convert Compact JSON to regular JSON:

1. Tokenize the input using only the following special characters `[]{}:`.  Quotes work like in JSON (i.e. they start a string).  White spaces are not special for the tokenizer.
2. If the token is a special character, just emit it without change.
3. If the token is a whitespace, emit a comma instead.
4. If the token is a valid JSON number, boolean or null -- emit it without change.
5. Add quotes before and after all other tokens.

# Examples

| Regular JSON     |  Compact JSON  |
| ---------------- | -------------- |
| `{"hello": "world", "foo": 42}` | `{hello:world foo:42}` |
| `[1, 2.3, "foo"] | `[1 2.3 foo]` |

# Content Encoding

The compact json format is not a full format of its own but more of a content-encoding.  
To use this encoding, applications should use the Content-Encoding or Accept-Encoding HTTP headers
or their equivalent.

The value of `x-cjson` represents the compact json encoding for such purposes.

