elixir-bson
===========

BSON implementation for Elixir Language

elixir-bson on GitHub [source repo](https://github.com/checkiz/elixir-bson) - 
[documentation](https://checkiz.github.io/elixir-bson)


BSON is a binary format in which zero or more key/value pairs are stored as a single entity, called a document. It is a data type with a standard binary representation defined at <http://www.bsonspec.org>.

This implements version 1.0 of that spec.

This project is used by [elixir-mongo](https://github.com/checkiz/elixir-mongo), a [MongoDB](http://www.mongodb.org) driver in Elixir.

This implementation maps the Bson grammar with Elixir terms in the following way:

  - document: Keyword List
  - int32 and int64: Integer
  - double: Float
  - string: String
  - Array: List (non-keyword)
  - binary: Bson.Bin (record)
  - ObjectId: Bson.ObjectId (record)
  - Boolean: true or false (Atom)
  - UTC datetime: triple Atom
  - Null value: nil (Atom)
  - Regular expression: Bson.Regex (record)
  - JavaScript: Bson.JS (record)
  - Timestamp: Bson.Timestamp (record)
  - Min and Max key: `MIN_KEY` or `MAX_KEY` (Atom)

This is how to encode a sample Elixir Keyword into a Bson Documentation:

```elixir
bson = Bson.encode a: 1, b: "2", c: [1,2,3], d: [d1: 10, d2: 11]
```
In this case, `bson` would be a document with 4 elements (an Integer, a String, an Array and an embeded document). This document would correspond in Javascript to:
```javascript
{a: 1, b: "2", c: [1,2,3], d: {d1: 10, d2: 11}}
```

Conversly, to decode a bson document:
```elixir
[a: 1] == Bson.decode <<12, 0, 0, 0, 16, 97, 0, 1, 0, 0, 0, 0>>
```

Special Bson element that do not have obvious corresponding type in Elixir are represented with Record, for example:

```elixir
jsbson = Bson.encode js: Bson.JS.new code:"function(a) return a+b;", scope: [b: 2]
rebson = Bson.encode re: Bson.Regex.new pattern: "\d*", opts: "g"
```

Implementation of encoders and decoders is done using `Protocol`, so, it is possible to redefine them (for instance, encoding end decoding of Bson.Bin could be redefined for the user defined subtype)