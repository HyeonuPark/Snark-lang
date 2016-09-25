Language reference
==================

Snark language specification version 0.1

## Line break semantics

Snark is not a free form language like C, nor formatting with indentation like python. The only whitespace character specially treated by Snark is [line terminators](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Lexical_grammar#Line_terminators).

In most situations, Snark treat line break as a separator token(`,` or `;`). So it can be used to separate statements or elements of array.

```
let array = [
  'foo'
  'bar'
  'baz'
]
array[0] // 'foo'
```

If some tokens(only `.` and `::`) lead the new line, line break right before this token is invalidated. This is the only exception of this rule.

```
let array = ['foo', 'bar', 'baz']
  .map(mapFn)
  .filter(filterFn)
```

## Scope

Unlike traditional namespace-lacking JS, every syntactic element in Snark has its own scope. Every identifier MUST be declared in its own or any ancestor scope before use.

## Literal

Snark exposes basic literals to represent values

### Keyword literal

These keywords are reserved as a representation of primitive values. You cannot make variables with same name as one of these.

Singleton literal

- `null`

- `true`

- `false`

### Placeholder literal

`_` keyword is used for placeholder literal. Mostly you can read it as "Ignore this place". This literal is adopted mostly for pattern matching.

When used as assignment pattern, values that targets this literal will simply be ignored.

```
let [a, _, c] = 1 ~ 5
// a == 1, c == 3

for [_, data] of someMap {
  console.log(data)
}
```

When used as expression, it represents a value same as `undefined` or `void 0` in JS.

```
let obj = {}

obj.foo == _ // true
```

### Number literal

You can freely insert `_` characters between numbers to improve readability. Hexadecimal, octal, binary and scientific representation are also supported.

Note that `_` prefixed numbers are treated as normal identifier.

- `42`

- `0xFF`

- `0o72`

- `0b1001`

- `1_000_000_000`

- `5.3e12`

- `0x1D_2E_3F`

Not this

- `_123`

### String literal

String literal inject text data into the program.

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

Object is a basic building block of the program. In JS, objects are just collection of named properties.

Object literal is composed of zero or more properties between curly braces(`{}`). Property follows the `name = value` form. `name` is the key to access this property. This can be either `Identifier` like `foo` or `Symbol identifier` like `#foo`. `value` is the initializer value of this property.

Property can be written as a shorthand form. For example, `{foo}` has the same meaning of `{foo = foo}`.

Note that line break(`\n` character in source code) has lexical meaning in Snark. So you can omit `,` in multi-line object literal.

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

You can copy another object into newly created object, using `Object.assign()` under the hood.

```
let obj1 = {foo = 0, bar = 1}
let obj2 = {...obj1, bar = 3, baz = 4} // {foo = 0, bar = 3, baz = 4}
```

### Array literal

Array is synchronous in-memory sequence object. Elements can be accessed by its index value.

Array literal is composed of zero or more elements between square brackets(`[]`).

```
let array = ['foo', 'bar', 'quux']
// same as
let array = [
  'foo'
  'bar'
  'quux'
]

array[0] // 'foo'
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

## Private identifier(Symbol)

Like normal identifiers, private identifiers can be used as a variable name or a property key. But it's guaranteed to be unique across all modules.

```
let #foo = 42
obj.#bar = 'quux'

let obj2 = {
  #foo
  #bar = 84
}

obj2.#foo == 42 // true
```

Under the hood, each private identifiers are ES2015 Symbol object.

```
// Snark

obj.#prop

// JavaScript

const __symbol_prop = Symbol('prop')

obj[__symbol_prop]
```

## Module

Snark module has same semantics as ES2015 module spec, to provide complete interoperability with JS.

Check [here](https://blogs.windows.com/msedgedev/2016/05/17/es6-modules-and-beyond) for more details.

### Export

Export variables to other module. Almost identical with ES2015 export spec, with a few extras.

Export statement can only be used within source code's top level scope.

- `export default <any expression here>`

- `export {member1, member2 as alias}`

- `export <any declaration here>`

- `export * from 'mylib'`

- `export {member1, member2 as alias} from 'mylib'`

- `export defaultMember from 'mylib'`

- `export * as namespace from 'mylib'`

Note that variables with `Symbol identifier` like `#foo` cannot be exported, as symbol identifier is not meant for sharing.

### Import

Import variables from other module. Just identical with ES2015 import spec.

- `import defaultMember from 'mylib'`

- `import * as namespace from 'mylib'`

- `import {member1, member2} from 'mylib'`

- `import {member as alias} from 'mylib'`

- `import defaultMember, * as namespace, {member1, member2 as alias} from 'mylib'`

- `import 'mylib'`

Note that you can't use `Symbol identifier` in normal import statement.

### Import from environment

`import` statement without `from` clause is used for importing things from runtime environment. Like normal `import` statement, they must be placed before any other statements.

Snark provides easy way to get global object. In fact, it's pseudo-global object that acts as read-only global object. Because Snark runtime specially treats real global object to handle some JS hacks like `this` unwrapping.

```
import window
// or
import global
```

While all variables must be defined before use(except a few language-defined objects), using global variables is very common in JS environment. To do this, you should declare it first.

```
import {navigator, document as doc}
// now you can use navigator and document(as variable 'doc') object
```

If you want to use some well-known symbols in your module, declare it.

```
import {#iterator as #itr, #get}

myList.#itr = myIteratorGenerator
// myList[Symbol.iterator] = myIteratorGenerator
```

If you want to shorten some `weird-and-unnecessarily-long-property-name(TM)`, declare it.

```
import {'weird-and-unnecessarily-long-property-name(TM)' as #wn}

obj.#wn = 0
// obj['weird-and-unnecessarily-long-property-name(TM)'] = 0
```

Note that you can only shorten property name to symbol identifiers, to prevent namespace conflict. For example, `import {'long' as l}` is not allowed.

### List of well-known symbols

ECMAScript provides some well-known symbols to allow customizing language-level features such as for-of statements. In this flavor, Snark adds some its own well-known symbols to provide new features.

  - JS well-known symbols

    `#iterator`, `#match`, `#replace`, `#search`, `#split`, `#hasInstance`, `#isConcatSpreadable`, `#unscopable`, `#species`, `#toPrimitive`, `#toStringTag`

    `#asyncIterator` - not in ES2015 spec, but will be added to ES soon. Polyfilled by Snark runtime.

  - Snark well-known symbols

    `#get`, `#set`, `#exec`

    `#proto` - alias for `'__proto__'`.

## Pattern matching

Pattern matching is a technique to determine a given value to ensure that it meets the specific pattern. Commonly pattern matching also provides functionality to extract properties of interest from given value, only if value is matching with pattern.

You can use this feature in two forms. To perform one-time pattern matching, use `in` operator. To match against multiple patterns, use `switch-case` expression.

```
if guest in Friend with {community, face} {
  community.uploadSelfie(face)
}
```

```
fn processMessage message => switch message {
  case Join    with {id}      -> createSession(id)
  case Ping    with {id}      -> sendPong(id)
  case Message with {id, msg} -> sessions[id].handleMessage(msg)
  case Quit    with {id}      -> removeSession(id)
}
```

At the base, pattern matching in Snark is just a method call of the pattern object. A well-known symbol `#exec` is provided for it.

```
import {#exec}

let Friend = {
  place = Map::new()
  addFriend = (this, person, comm) => this.place.set(person, comm)
  #exec = (this, person) => do {
    if this.place.has(person) {
      return {
        match = true
        value = {
          name = person.name
          community = this.communityMap.get(person)
          face = person.takePicture()
        }
      }
    }

    return {match = false}
  }
}
```

As shown above, a method `#exec` returns `{match, value}` form. `match` is a boolean value to determine this pattern matching is successed. `value` is an extracted value to be provided.

## Expression

Expressions can be evaluated as a value.

### Arithmetic operator

Perform arithmetic operation with given numbers.

`<number> <operator> <number>` -> `number`

- `1 + 2 // 3`

- `1 - 2 // -1`

- `1 * 2 // 2`

- `1 / 2 // 0.5`

Note that `+` operator should be used only with numbers. Though it's possible to concat strings with this operator as JS supports it, it's highly discouraged. See [template string](#template-string-literal) for details.

### Logical operator

Logical operators are used for boolean arithmetic.

`<expression> <operator> <expression>` -> `boolean`

- `a and b` - both `a` and `b` are truthy.

- `a or b` - either `a` or `b` is truthy.

- `not a` - `a` is not truthy.

### Comparison operator

Compare given values.

`<expression> <operator> <expression>` -> `boolean`

- `a == b` - `a` and `b` are strictly equal.

- `a != b` - `a` and `b` are not strictly equal.

Note that non-strict equality operators(`===` and `!==` in JS) is not exist in Snark.

Operators below are only able to be used with numbers.

`<number> <operator> <number>` -> `boolean`

- `a > b` - `a` is greater then `b`

- `a < b` - `a` is lesser then `b`

- `a >= b` - `a` is not lesser then `b`

- `a <= b` - `a` is not greater then `b`

Note that `=>` keyword is used for function, not for comparison.

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

### Virtual method

Also known as `Function bind syntax`. This syntax binds given object as a call context to the following function. For detail, see [method](#method) section.

```
myIterable::map(mapFn)::filter(filterFn)
// in JS, filter.call(map.call(myIterable, mapFn), filterFn)

passCallback(obj::handler)
// in JS, passCallback(handler.bind(obj))
```

As `new Class()` syntax is not exist, Snark provides `new` keyword as a meta virtual method. Use this to instantiate your classes.

```
let map = Map::new([
  'foo' -> 0
  'bar' -> 2
  'baz' -> 7
])
```

### Collection getter/setter

Using Map should be easier then traditional object-as-dictionary approach. That's why Snark adds such a syntax. To use these, first you should declare some well-known symbols.

```
import {#get, #set}

let innerMap = Map::new()

let map = {
  #get = key => innerMap.get(key)
  #set = (key, value) => innerMap.set(key, value)
}

map['foo' -> 42]
map['foo'] // 42
```

This can makes some syntactic inconsistency with plain ol' JS object like `Array` or `Map`. To solve it, Snark runtime monkey-patch some built-in objects for it.

```
let map = Map::new()

map['foo' -> 'bar']
map['foo'] // 'bar'

let array = ['foo', 'bar', 'baz']

array[2] // 'baz'
array[3 -> 'quux'] // returns modified array itself.
```

### Existential operator

Like one in CoffeeScript, this operator advancing operation only if it will not throw `TypeError`.

```
obj?.prop // "obj == null ? null : obj.prop" in JS

func?()   // "typeof func !== 'function' ? null : func()" in JS

map?[key] // "typeof map.#get !== 'function' ? null : map.#get(key)" in JS-ish

map?[key -> value] // "typeof map.#set !== 'function' ? null : map.#set(key)" in JS-ish

obj?.prop?[key]?()
```

### Switch expression

`switch` is expression in Snark. It checks given value with applied conditions and return evaluated expression of matching one.

Basically, condition matching in switch expression is strict equality check, same as `==` operator.

```
switch <expression> {
  <expression> -> <expression>
}
```

```
console.log(switch 'bar' {
  'foo' -> 'found "foo"'
  'bar' -> 'found "bar"'
  'baz' -> 'found "baz"'
  else as unknown -> "found unknown value <${unknown}>"
})
// found "bar"
```

If condition clause starts with `case` keyword, it performs pattern matching instead. See [here](#pattern-matching) for more detail. `case` conditions and normal conditions can be mixed within same `switch` block.

```
switch <expression> {
  case <expression> -> <expression>
  case <expression> with <assignment pattern> -> <expression>
}
```

```
console.log(switch myBreakfast {
  case Pizza with {topping, dough} -> "I ate pizza with ${topping} over ${dough}"
  case Pancake -> 'I ate pancake'
  case Donut with {isRing} with isRing -> "I ate ring-shaped Donut"
  else -> 'I ate ...something'
})
```

If you omit the argument of `switch` expression, it only check that given condition is truthy, just like an `else-if` chain. In this case, `case` condition is not allowed.

```
switch {
  <expression> -> <expression>
}
```

```
switch {
  truth == 0 -> console.log('nope')
  truth == 1 -> console.log('maybe not')
  truth == 42 -> console.log('best answer until more valid question is found')
}
```

### In operator

Perform one time pattern matching. For detail, see [pattern matching](#pattern-matching).

`<expression> in <expression>` -> `boolean`

`<expression> in <expression> with <assignment pattern>` -> `boolean`

```
if myBreakfast in Pizza with {topping, dough} {
  console.log("I ate pizza with ${topping} over ${dough}")
}
```

### As operator

Declare variables to it's following scope and assign given value to it. Evaluated as given value itself.

```
if getChild() as john {
  john.runAway()
}
```

## Statement

Statements are instruction to define how program is executed. They cannot be evaluated as a value.

#### Assignment pattern

1. Identifier pattern

  `<identifier>`

  Assign value to the variable with given identifier.

1. Object pattern

  `{ <identifier>, <identifier> as <assignment pattern>, ... }`

  Destructure given value as given pattern and assign them.

1. Array pattern

  `[ <assignment pattern>, ... ]`

  Extract contents from given iterable object and assign them.

### Assignment statement

Assign new value to the given place.

- `<assignment pattern> = <expression>`

  Destruct given value and assign them to the variables.

  ```
  foo = 'bar'
  {name, age} = john
  ```

- `<expression>.<identifier> = <expression>`

  Assign given value to object's property.

- `<identifier> <operator>= <expression>`

- `<expression>.<identifier> <operator>= <expression>`

Perform operation with given variable/property and re-assign it back.

Note that assignment is statement, not a expression. So you can't use this inside of `if` condition.

### Variable declaration

Declare variable to the current scope. All variables MUST be declared before use. Initialization is required, not optional.

Note that variables cannot be re-assigned except explicitly declared as mutable.

- `let foo = 42`

- `let mut bar = 443`

Generally variable declaration statement follows `let <assignment pattern> = <expression>` form. Identifiers inside assignment pattern are declared as variable. To make it mutable, place `mut` before it.

- `let {age, parents as [father, mother]} = john`

Snark does not allow double-underscore(`__`) prefixed variable name like `__foo`. It's not a technical limitation, but just a policy to improve readability of compiled output JS code.

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

Iterate over given iterable object.

```
for name of ['Alex', 'Amy', 'Anna'] {
  addFriend(name)
}
```

To avoid common pitfall of JS, Every variables are newly created per iteration. And just like normal variables, it's not mutable except explicitly declared as mutable.

```
for {name, mut age} of getPeople() {
  setTimeout(#(console.log(name)), 1000) // Just nice Snark code
  age += 1
  console.log(age)
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

## Function and do expression

Snark does not support traditional function syntax, like `function (arguments) {function body}`. To implement this, you must combine function AND do expression.

### Function

Functions takes arguments and returns output value. They may has optional name, for recursive call and easy debugging.

Named function in statement-level will be treated as function declaration. Unlike JS, they will not be hosted.

Note that non-named function cannot call itself in it's body.

```
arr.map(el => "${el} is nice")

let random = (min, max) => min + (Math.random() * (max - min))
// same effect as following
fn random (min, max) => min + (Math.random() * (max - min))
// well this also same but..
let random = fn random (min, max) => min + (Math.random() * (max - min))
```

#### Shorthand function expression

Even more shortened function expression.

```
arr.reduce( #(#0 + #1) )
// same as
arr.reduce((arg0, arg1) => arg1 + arg2)

let logSomething = #(console.log('something')) // () => console.log('something')
logSomething() // something
```

### Do expression

Do expression has same effect as IIEF, a well-known pattern in JS.

```
let result = do {
  let number = 42
  return number / 2
}
// result == 21
```

```
fn bumpThree (a, b, c) => do {
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

In some case, we should access function's call context which known as `this`. Unlike JS, you cannot do this unless you explicitly declare the function as a method. To write method, just include keyword `this` as a first argument of the function.

```
let obj = {
  size = 2
  grow = (this, amount) => do {
    this.size += amount
  }
}

obj.grow(3)
obj.size // 5
```

## Class

Snark's class syntax is not same as ES2015 class syntax. Check [class document](./Class.md) for details.

## Range expression

It's infinitely common pattern to use numeric sequence in programming. Snark provide syntactic shortcut for such patterns.

Basic syntax for range expression is `a ~ b`. It generate `Range` object with range from a to b, inclusive. Value of `a` and `b` must be a number and follows `a <= b` or it throws error. To mark some endpoint as exclusive, add `<` at that side. ex) `a ~< b` or `a <~ b`, or both side.

Range object can be used as match pattern or iterable.

### As match pattern

Match if value is number and within given range. Extracts given number itself.

```
3 in 2~4  // true
5 in 5~<7 // true
7 in 5~<7 // false
```

### As iterable

Iterate from left number to right number with interval 1. Bottom-exclusive range is not valid for this usage.

```
for i of 1~<5 {
  console.log(i)
}
// 1 2 3 4
```
