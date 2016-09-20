Planned features
================

These are planned features for the future version of Snark language.

## Decorator

## Macro

Macro is a compile-time-function which takes source code and replace it with another source code. This is useful for library author who want to embed custom DSL to library.

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
fn factorial num => switch num {
  case _~1 -> 1
  else as n -> n * factorial(n - 1)
}
```
