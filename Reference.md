Language reference
==================

Snark language reference.

## Literal

Snark exposes basic literals for primitive values

### Keyword literal

These keywords are reserved as a representation of primitive values. They are identical with matching JS literal.

- `null`

- `true`

- `false`

### Number literal

You can freely insert `_` characters between numbers to improve readability. Hexadecimal, octal, binary and scientific representation are also supported.

- `42`

- `0xFF`

- `0o72`

- `0b1001`

- `1_000_000_000`

- `5.3e12`

- `0x1D_2E_3F`

### String literal

`\n` will be replaced with space and indentations will be removed.

`'''` for multi-line string.

```
'foobar'
// == 'foobar'
```

```
'foo
bar
baz'
// == 'foo bar baz'
```

```
if true {
  'foo
  bar
  baz'
}
// == 'foo bar baz'
```

```
if true {
  '''
    foo
    bar
    baz
  '''
}
// == 'foo\nbar\nbaz'
```

### Template string literal

String literal with double-quote (`"`) makes template string. Template string can include any expressions, to make string from data. Expressions are placed inside of `${}`.

Note that concatenating strings with `+` operator is highly discouraged. Use template string instead.

```
let name = 'Snark'

let greeting = "Hello, ${name}!"
// greeting == 'Hello, Snark!'

let longGreeting = """
  Hello, ${name}!
  It's nice to see you!
"""
// longGreeting == 'Hello, Snark!\nIt\'s nice to see you!'
```

### Object literal

Object is a basic building block of the program. Like JS, Snark object is a collection of named properties.

Note that line break(`\n` character in source code) has lexical meaning in Snark. They can replace separator tokens like `,` or `;`. so you can omit `,` in multi-line object literal.

```
let obj = {foo = 42, bar = 'quux'}
// same as
let obj = {
  foo = 42
  bar = 'quux'
}
// also same
let foo = 42
let bar = quux
let obj = {foo, bar}
```

### Array literal

Array is synchronous in-memory sequence object. Elements can be accessed by its index value.

```
let array = [foo, bar, quux]
// same as
let array = [
  foo
  bar
  quux
]
```

There's a syntactic sugar for array of key-value pairs.

```
let pairs = [
  foo -> 42
  bar -> 443
  quux -> 80
]
// same as
let pairs = [
  [foo, 42]
  [bar, 443]
  [quux, 80]
]
```

## Symbol

Like string, Symbol can be used as a variable name or a property key. But it's guaranteed to be unique across all modules.

```
let #foo = 42
obj.#bar = 'quux'

let obj2 = {
  #foo
  #bar = 84
}

obj2.#foo == 42 // true
```

ECMAScript provides some well-known symbols to utilize language-level features such as for-of statements. In this flavor, Snark adds some its own well-known symbols to provide new features.

## Module

### Import from environment

While all variables must be defined before use(except a few language-defined objects), using global variables is very common in JS environment. To do this, you should declare it first.

```
use navigator, document as doc
// now you can use navigator and document(as variable 'doc') object
```

If you want to use some well-known symbols in your module, declare it.

```
use #iterator as #itr, #get

myList.#itr = myIteratorGenerator
// myList[Symbol.iterator] = myIteratorGenerator
```

If you want to shorten some weird-and-unnecessarily-long-property-name(TM), declare it.

```
use 'weird-and-unnecessarily-long-property-name' as #wn

obj.#wn = 0
// obj['weird-and-unnecessarily-long-property-name'] = 0
```

## Expression

### Arithmetic operator

### Logical operator

### Function call

- `func(arg)`

- `func(arg1, arg2)`

- `func(arg1, ...restArgs)`

### Object property

- `obj.propName`

- `obj.prop = 42`

- `obj.#symbolProp`

Syntaxes below are just for legacy JS interoperability.

- `obj.#0` - same as `obj[0]` in JS

- `obj.#[expr]` - same as `obj[expr]` in JS

### Existential operator

Like one in CoffeeScript, this operator advancing operation only if it will not throw `TypeError`.

```
obj?.prop // "obj == null ? null : obj.prop" in JS

func?()   // "typeof func !== 'function' ? null : func()" in JS

map?[key] // "typeof map.#get !== 'function' ? null : map.#get(key)" in JS-ish

map?[key -> value] // "typeof map.#set !== 'function' ? null : map.#set(key)" in JS-ish
```

### Collection getter/setter

Using Map should be easier then traditional object-as-dictionary approach. That's why Snark adds such a syntax. To use these, first you should declare some well-known symbols. And boom!

```
use #get, #set

let innerMap = Map::new()

let map = {
  #get = key => innerMap.get(key)
  #set = (key, value) => innerMap.set(key, value)
}

map['foo' -> 42]
map['foo'] // 42
```

### In operator

## Statement

### Variable declaration

Declare variable. All variables MUST be declared before use. Initialization is required.

Note that variables cannot be re-assigned except explicitly declared as mutable.

- `let foo = 42`
- `let mut bar = 443`

### Assignment statement

Assign new value to the given place. In-place operation is supported, but prefix/postfix increment/decrement operators are not.

- `mut bar = 80`
- `obj.quux = foo`
- `mut bar += 1`
- `obj.quux *= 2`

### If statement

Execute following block when given condition is truthy. Otherwise execute else block if provided.

`{}` block is required, not optional.

Note that `else` keyword MUST be in the same line as `}` character of `if` block, as line change has lexical meaning in Snark.

```
let newcommer = true

if newcomer {
  greeting()
}
```

```
let isLeft = true

if isLeft {
  goLeft()
} else {
  goRight()
}
```

### While statement

Loop until given condition is fulfilled.

```
let mut count = 0

while count < 10 {
  doSomething()
  count += 1
}
```

### For of statement

Iterate over given object.

```
for name of ['Alex', 'Amy', 'Anna'] {
  addFriend(name)
}
```

You can iterate over AsyncIterable or Iterable<Promise> in async context.

```
for await name of fetchFriends() {
  addFriend(name)
}
```

## Function and do statement

### Function

Functions takes arguments and returns output. They may has optional name, for recursive call and easy debugging.
Named function in statement-level will be treated as function declaration. Unlike JS, they will not be hosted.

```
arr.map(el => "${el} is nice")

let random = (min, max) => min + (Math.random() * (max - min))
// same effect as following
fn random (min, max) => min + (Math.random() * (max - min))
// well this also possible but..
let random = fn random (min, max) => min + (Math.random() * (max - min))
```

### Do statement

Snark does not support traditional function syntax. To implement this, you must combine function AND do statement.

Do statement has same effect as IIEF, a well-known pattern in JS.

```
let result = do {
  let number = 42
  return number / 2
}
// result == 21
```

```
fn bumpAll (a, b, c) => do {
  bump(a)
  bump(b)
  bump(c)
}
```

Async and/or Generator functions are also possible.

```
do* {
  yield 1
  yield 2
}

async {
  let foo = await someTask()
  let bar = await anotherTask()
  return {foo, bar}
}

async* {
  yield await moreTask()
  yield* getEventStream()
}
```

### Method

In some case, we should access function's execution context called `this`. Unlike JS, you cannot do this unless you explicitly declare the function as a method. To write method, just include keyword `this` as a first argument of the function.

```
let obj = {
  size = 0
  grow = (this, amount) => do {
    this.size += amount
  }
}
```

## Pattern matching

## Range expression

## Decorator
