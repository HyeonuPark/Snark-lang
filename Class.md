Class
=====

Unlike many other languages, class is not a only way(not even most common way) to create new object in JS. But, class is still a useful syntax to create several objects that share behaviors.

Class consists of methods, properties, static properties, and optional superclass. This is an example of the Snark class. Let's explore this code line by line.

```
class Programmer extends Human {

  static let mut hype = 65535
  static let raiseHype = moreHype => do {hype += moreHype}

  let editor
  let mut balance = 0

  new (name, age, this.editor, initBalance) => do {
    super(name, age)

    if initBalance > 0 {
      balance = initBalance
    }
  }

  writeCode (requirement) => async {
    await coffee()
    balance += await salary()
    return editor.someBlackMagic(hype, requirement)
  }

  get isTired () => true
  set isTired (value) => do {
    if value != true {
      throw new RealityError()
    }
  }
}
```

1. Class and superclass

```
class Programmer extends Human {
```

First we declared the class. It's name is `Programmer` and it inherits from `Human` class. So we can expect that our programmer just acts as other humans if not explicitly override that behavior.

1. Static properties

```
static let mut hype = 65535
static let raiseHype = moreHype => do {hype += moreHype}
```

Static properties are just a property of the class object itself. For example, you can access `hype` field by `Programmer.hype` expression.

Note that `raiseHype` function uses `hype` *variable*. This is because class block acts similar to do expression so you can access variable from outer scope.

Just like normal variables, you can't modify `raiseHype` property as it's not declared as `mut`.

You can't access non-static things from inside of static properties.

1. Class properties

```
let editor
let mut balance = 0
```

Class properties are properties of class' each instance. For example, programmers from this class can have various editors like `vim` or `emacs`.

Unlike variables and static properties, you can omit initializer on class properties. If omitted, they must be initialized in class constructor.

1. Class methods

```
writeCode (requirement) => async {
  await coffee()
  balance += await salary()
  return editor.someBlackMagic(hype, requirement)
}

get isTired () => true
```

Class method can be called on instance of this class.

In this example, `writeCode` is a method function that takes single argument `requirement`. It's async function and using class property `balance` and static property `hype` as a variable.

1. Class constructor

```
new (name, age, this.editor, initBalance) => do {
  super(name, age)

  if initBalance > 0 {
    balance = initBalance
  }
}
```

Class constructor is a function that automatically called on instantiating class. Though it looks similar to class method named with `new`, its syntax is a bit different.

  1. Constructor's body must be a do expression. And it can't return any value.

  1. Direct assigned argument(`this.editor` for this example) is only allowed in constructor.

  1. All class properties must be initialized until the constructor is returned. If this property is not `mut`, it will be read-only property since constructor call is end.
