Language reference
==================

Snark language reference.

## Literal

Snark exposes basic literals to declare values

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

You can include another object into newly created object, using `Object.assign()`.

```
let obj1 = {bar = 1, baz = 2}
let obj2 = {foo = 0, ...obj1} // {foo = 0, bar = 1, baz = 2}
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

You can include another iterable into newly created array.

```
let array1 = [3, 4, 5]
let array2 = [1, 2, ...array1] // [1, 2, 3, 4, 5]
```

There's a syntactic shortcut for array of key-value pairs.

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

### List of well-known symbols

  - JS well-known symbols

    `#iterator`, `#match`, `#replace`, `#search`, `#split`, `#hasInstance`, `#isConcatSpreadable`, `#unscopable`, `#species`, `#toPrimitive`

    `#asyncIterator` - not in ES2015 spec, but will be added to ES soon. Polyfilled by Snark runtime.

  - Snark well-known symbols

    `#get`, `#set`, `#in`, `#case`

    `#proto` - alias for `__proto__`.

## Module

Snark module has same semantics as ES2015 module spec, to provide complete interoperability with JS.

Check [here](https://blogs.windows.com/msedgedev/2016/05/17/es6-modules-and-beyond/#6i0OmkY8euSIiFd2.97) for more details.

### Import

Import variables from other module. Just identical with ES2015 import spec.

- `import defaultMember from 'mylib'`

- `import * as namespace from 'mylib'`

- `import {member1, member2} from 'mylib'`

- `import {member as alias} from 'mylib'`

- `import defaultMember, * as namespace, {member1, member2 as alias} from 'mylib'`

- `import 'mylib'`

### Export

Export variables to other module. Almost identical with ES2015 export spec, with a few extras.

Unlike import statement, export statement can be used anywhere of source code's top level scope.

- `export default <any expression here>`

- `export {member1, member2 as alias}`

- `export <variable/function/class declaration here>`

- `export * from 'mylib'`

- `export {member1, member2 as alias} from 'mylib'`

- `export defaultMember from 'mylib'`

- `export * as namespace from 'mylib'`

### Import from environment

`use` statement is used for importing things from runtime environment. Like `import` statement, `use` statement must be placed before any other statements.

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

Expressions can be evaluated as a value.

### Arithmetic operator

- `1 + 2 == 3`

- `1 - 2 == -1`

- `1 * 2 == 2`

- `1 / 2 == 0.5`

Note that `+` operator should be used only with numbers. Though it's possible to concat strings with this operator as JS supports it, it's highly discouraged. See [template string](#template-string-literal) for details.

### Logical operator

- `and`

- `or`

- `not`

### Function call

- `func()`

- `func(arg1, arg2)`

- `func(arg1, ...restArgs)`

### Object property

- `obj.propName`

- `obj.prop = 42`

- `obj.#symbolProp`

Syntaxes below are just for legacy JS interoperability.

- `obj.#0` - same as `obj[0]` in JS

- `obj.#[expr]` - same as `obj[expr]` in JS

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

### Existential operator

Like one in CoffeeScript, this operator advancing operation only if it will not throw `TypeError`.

```
obj?.prop // "obj == null ? null : obj.prop" in JS

func?()   // "typeof func !== 'function' ? null : func()" in JS

map?[key] // "typeof map.#get !== 'function' ? null : map.#get(key)" in JS-ish

map?[key -> value] // "typeof map.#set !== 'function' ? null : map.#set(key)" in JS-ish
```

### In operator

Check given value is a member of the given collection or not.

```
let foo = 'foo'

if foo in ['foo', 'bar', 'baz'] {
  console.log('yup')
}

if foo not in someListWithoutFoo {
  console.log('nope')
}
```

### Virtual method

Also known as `Function bind syntax`. This syntax binds given object as a execution context to the following function. For detail, see [method](#Method) section.

```
myIterable::map(mapFn)::filter(filterFn)

passCallback(obj::handler)
```

## Statement

Statements are instruction to define how program is executed. They cannot be evaluated as a value.

### Variable declaration

Declare variable. All variables MUST be declared before use. Initialization is required, not optional.

Snark does not allow underscore(`_`) postfixed variable identifier, like `foo_`. It's not a technical limitation, but just a policy to improve readability of compiled output JS code.

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
  mut count += 1
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

### Enum statement

Shortcut syntax for declaring constants.

```
enum PossibleConditions {
  COND_A, COND_B, COND_C
}

console.log(COND_A) // 'COND_A'

console.log(COND_B in PossibleConditions) // true
console.log('NOT_VALID' in PossibleConditions) // false
```

## Function and do statement

Snark does not support traditional function syntax, Like `function (arguments) {function body}`. To implement this, you must combine function AND do statement.

### Function

Functions takes arguments and returns output value. They may has optional name, for recursive call and easy debugging.
Named function in statement-level will be treated as function declaration. Unlike JS, they will not be hosted.

```
arr.map(el => "${el} is nice")

let random = (min, max) => min + (Math.random() * (max - min))
// same effect as following
fn random (min, max) => min + (Math.random() * (max - min))
// well this also same but..
let random = fn random (min, max) => min + (Math.random() * (max - min))
```

### Do statement

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

## Class

Snark's class syntax is not same as ES2015 class syntax. Check [class document](./Class.md) for details.

## Pattern matching

Pattern matching is a common pattern in functional programming language to wrap values with additional information. Check [pattern matching document](./PatternMatching.md) for details.

## Range expression

It's infinitely common pattern to use numeric sequence in programming. Snark provide syntactic shortcut for such patterns.

Basic syntax for range expression is `a ~ b`. It generate `Range` object with range from a to b, inclusive. Value of `a` and `b` must be a number and follows `a <= b` or it throws error. To mark some endpoint as exclusive, add `<` at that side. ex) `a ~< b` or `a <~ b`, or both side.

### In operator

Evaluated as true if value is number and within given range.

```
3 in 2~4  // true
5 in 5~<7 // true
7 in 5~<7 // false
```

### Case operator

Same as `in` operator. Extracts value itself.

```
switch getNum() {
  case 1~4 as i -> doSomething(i)
  else -> doNothing()
}
```

### For of statement

Iterate from left number to right number with interval 1. Bottom-exclusive range is not valid for this usage.

```
for i of 1~<5 {
  console.log(i)
}
// 1 2 3 4
```

## Decorator

## Macro

Macro is a compile-time-function which takes source code and replace it with another source code. This is useful for library author who want to embed custom DSL to library.

```
regexp!("^Hello, ([A-Za-z]+)!$", 'i')
```

```
jsx!(
  [div
    [HeaderComponent, {title = 'most awesome page in the web'}]

    this.prop.children

    [FooterComponent
      {
        name = 'rob.co'
        tel = '123-456-7890'
        logoUrl = 'logo.example.com'
      }
    ]
  ]
)
```

```
@!stackless
fn factorial num => switch num {
  case _~1 -> 1
  else as n -> n * factorial(n - 1)
}
```
