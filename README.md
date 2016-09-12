Snark language document
=======================

This document explains about the Snark language.

## What is Snark?

Snark is a new syntax for modern JavaScript programs.

In detail, Snark is a programming language that compiles into JavaScript. Like CoffeeScript, Snark's main principle is _It's just JavaScript_, but a bit more aggressive.

Snark's main purpose is to integrate modern JavaScript's features with language syntax, without breaking most of idiomatic patterns.

Additionally, Snark adds bunch of cool features which makes it easy to use such modern techniques.

## Overview

```
let number = 10
let mut isNice = false

mut isNice = true

if isNice {
  let language = 'Snark'
  let description = "${language} is awesome!"
  let longDescription = """
    ${language} is awesome!
    It's nice, really!
    Please follow this document and you can feel it!
  """
} else {
  // Well, I don't think this block will be executed
}

let name = 'Bart Simpson'
let bart = {
  name
  age = 10
  write = (this, phrase) => do {
    console.log("${this.name}> ${phrase}")
  }
}

bart.gender = 'Male'

for i of 1~100 {
  bart.write("I WILL DO ENJOY PROGRAMMING - ${i}")
}

console.log(
  match student.age {
    _ ~< 14 -> "${student.name} cannot get driver's license"
    14~  _  -> "${student.name} can get his/her driver's license"
  }
)

let students = Map::new()

students[bart.name -> bart]

switch {
  bart.name not in students {
    console.log('Isn\'t he a student?')
  }
  bart.age != 10 {
    console.log('He should be 10')
  }
  bart.gender?.toUpperCase?() == 'MALE' {
    console.log('Well.. maybe it\'s lisa, his sister')
  }
  else {
    console.log('Everything has done right')
  }
}
```

Need more? There's a [reference!](./Reference.md)

## License

Contents of this repository are distributed under [Creative Commons Attribution-ShareAlike 3.0](http://creativecommons.org/licenses/by-sa/3.0/) license.
