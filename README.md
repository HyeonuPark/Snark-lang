Snark language document
=======================

This document explains about the Snark language.

## What is Snark?

Snark is a new syntax for modern JavaScript programs.

In detail, Snark is a programming language that compiles into JavaScript. Like CoffeeScript, Snark's main principle is _It's just JavaScript_, but a bit more aggressive.

Snark's main purpose is to integrate modern JavaScript's features with language syntax, without breaking most of idiomatic patterns.

## Overview

```
import {console}

let number = 10
let mut isNice = false

isNice = true

if isNice {
  let language = 'Snark'
  let description = "${language} is awesome!"
  let longDescription = """
    ${language} is awesome!
    It's nice, really!
    Please follow this document and you can feel it!
  """
} else {
  // Well, I don't think this block can be executed
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

bart.school = switch bart.age {
  case 7  ~ 11 -> "Elementary school"
  case 12 ~ 14 -> "Middle school"
  case 15 ~ 18 -> "High school"
  else -> "Hmm?"
}

let students = Map::new()

students[bart.name -> bart]

switch {
  bart.name not in students -> do {
    console.log('Isn\'t he a student?')
  }
  bart.age != 10 -> do {
    console.log('He should be 10')
  }
  bart.gender?.toUpperCase?() != 'MALE' -> do {
    console.log('Well.. maybe it\'s lisa, his sister')
  }
  else -> do {
    console.log('Everything has done right')
  }
}

let someTask = async {
  let [homer, marge] = await getParentsAsync(bart)

  let hairColor = do {
    let color = marge.hairColor

    if color == 'white' {
      return 'silver'
    }

    return color
  }

  return homer
}
```

Need more? There's a [reference!](./Reference.md)

## License

Contents of this repository are distributed under [Creative Commons Attribution-ShareAlike 3.0](http://creativecommons.org/licenses/by-sa/3.0/) license.
