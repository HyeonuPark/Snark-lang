Planned features
================

These are planned features for the future version of Snark language.

## Decorator

Waiting [this ES proposal](https://tc39.github.io/proposal-decorators/) stabilized to provide perfect interoperability with vanilla JS decorators.

## Macro

Macro is a compile-time-function which takes source code and replace it with another source code. This is useful for library author who want to embed custom DSL to their library.

Some language-defined macros can be implemented before the custom macro syntax/api.

```
regexp!("^Hello, ([A-Za-z]+)!$", 'i')

passCallback(bind!(console.log))
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
fn factorial num => switch {
  num <= 1 -> 1
  else -> num * factorial(num - 1)
}
```
