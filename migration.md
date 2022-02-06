---
title: Migration guide
---

## Migration guide

#### Contents

1. [How to migrate current applications of `role=search`](#how-to-migrate-current-applications-of-rolesearch)
    1. [Simple cases](#simple-cases)
    1. [Nesting](#nesting)
    1. [Not `<div>` or `<form>`](#not-div-or-form)
    1. [Usage patterns](#usage-patterns)
1. [How to update JS code and libraries](#how-to-update-js-code-and-libraries)
    1. [Tagname conditions](#tagname-conditions)
    1. [Ancestor selector](#ancestor-selector)
    1. [Switch-case](#switch-case)

<br>

### How to migrate current applications of `role=search`

#### Simple cases

- `<div role=search>` -> `<search>`
- `<form role=search>` -> `<search action="">`
- `<form role=search action="...">` -> `<search action="...">`

#### Nesting
- `<div role=search> ... <form>` -> `<search> ... <div>`
- If there are additional form controls in the `<div>` that shouldn't be submitted, those conceptually do not belong in `search` and should be refactored.
- `<form> ... <div role=search>` -> `<search> ... <div>`
- `<form> ... <div role=search>` where the form contains additional elements outside search is very rare:  [`<form> <... role=search></...> + <...>`]( https://sourcegraph.com/search?q=context:global+%3Cform%5B%5E%3C%5D*%3F%3C%5B%5E/%5D%5B%5E%3E%5D*%3Frole%3D%5B%27%22%5D%3Fsearch%5B%27%22+%3E%5D.*%3F%3C/.*%3F%3E%3C%5B%5E/%5D.*%3F%3C/form%3E&patternType=regexp ). Anno ASP Forms resulted in a full-page `<form>`, where `role=search` would have been just one part of the `form`. A new element is not expected to be used with such old technology, therefore it is a better trade-off to not support this pattern.

#### Not `<div>` or `<form>`
- [other elements used with `role=search` (ignore the custom components)](https://sourcegraph.com/search?q=context:global+%3C%28%5B%5Edf/%5C%3F%5D..%7Cd.%5B%5Ev%5D%7Cf.%5B%5Er%5D%29%5B%5E%3E%5D*role%3D%5B%27%22%5D%3Fsearch%5B%27%22+%3E%5D&patternType=regexp) are mostly anti-patterns that will benefit from refactoring to adopt the `<search>` element.

#### Usage patterns

Note: Sourcegraph indexes open-source projects only. It does not cover the World Wide Web, but there are many public git scraping projects that mirror a piece of the Web.

- [`<div role=search> <form>`]( https://sourcegraph.com/search?q=context:global+%3Cdiv%5B%5E%3E%5D%2Brole%3D%5B%27%22%5D%3Fsearch%5B%27%22+%5D%5B%5E%3C%5D*%3Cform.*%3F%3C/form%3E%5B%5E%3C%5D*%3F%3C/div%3E++-file:/%5C.cache/++-file:/_debug_&patternType=regexp ) is rare, 79 results. All other patterns are 500+.
- [`<form> <... role=search>`]( https://sourcegraph.com/search?q=context:global+%3Cform%5B%5E%3C%5D*%3F%3C%5B%5E%3E%5D%2B%28%5Cs%29role%3D%5B%27%22%5D%3Fsearch%5B%27%22+%3E%5D%5B%5E%3C%5D*%28%3C%28%5B%5E/%5D%7C/%5B%5Ef%5D%7C/..%5B%5Er%5D%29%5B%5E%3C%5D*%29*%3C/form%3E&patternType=regexp )
- [`<form>, single <div role=search>`]( https://sourcegraph.com/search?q=context:global+%3Cform%5B%5E%3C%5D*%3F%3Cdiv%5B%5E%3E%5D%2Brole%3D%5B%27%22%5D%3Fsearch%5B%27%22+%3E%5D%5B%5E%3C%5D*%28%3C%28%5B%5E/%5D%7C/%5B%5Edf%5D%7C/..%5B%5Evr%5D%29%5B%5E%3C%5D*%29*%3C/div%3E%5Cs*%3C/form%3E&patternType=regexp )
- [`<div role=search>, no <form> within`]( https://sourcegraph.com/search?q=context:global+%3Cform%5B%5E%3E%5D%2Brole%3D%5B%27%22%5D%3Fsearch%5B%27%22+%5D%5B%5E%3C%5D*%28%3C%28%5B%5Ed/%5D..%7Cd.%5B%5Ev%5D%29%5B%5E%3C%5D*%29*%3C/form%3E&patternType=regexp )
- [`<form role=search>, no <div> within`]( https://sourcegraph.com/search?q=context:global+%3Cform%5B%5E%3E%5D%2Brole%3D%5B%27%22%5D%3Fsearch%5B%27%22+%5D%5B%5E%3C%5D*%28%3C%28%5B%5Ed/%5D..%7Cd.%5B%5Ev%5D%29%5B%5E%3C%5D*%29*%3C/form%3E&patternType=regexp ).
- [`<form role=search>, single <div>`]( https://sourcegraph.com/search?q=context:global+%3Cform%5B%5E%3E%5D%2Brole%3D%5B%27%22%5D%3Fsearch%5B%27%22+%3E%5D%5B%5E%3C%5D*%3Cdiv%5B%5E%3C%5D*%28%3C%28%5B%5E/%5D%7C/%5B%5Edf%5D%7C/..%5B%5Evr%5D%29%5B%5E%3C%5D*%29*%3C/div%3E%5Cs*%3C/form%3E&patternType=regexp ).
- [`<form role=search>, multiple <div>`]( https://sourcegraph.com/search?q=context:global+%3Cform%5B%5E%3E%5D%2Brole%3D%5B%27%22%5D%3Fsearch%5B%27%22+%3E%5D%5B%5E%3C%5D*%28%3C%28%5B%5E/%5D%7C/%5B%5Ef%5D%7C/..%5B%5Er%5D%29%5B%5E%3C%5D*%29*%3C/div%3E%5Cs*%3Cdiv%5B%5E%3C%5D*%28%3C%28%5B%5E/%5D%7C/%5B%5Ef%5D%7C/..%5B%5Er%5D%29%5B%5E%3C%5D*%29*%3C/form%3E&patternType=regexp ) - complex trees.
- Exotic search landmarks - [`not <form>, not <div>`]( https://sourcegraph.com/search?q=context:global+%3C%28%5B%5Edf/%5C%3F%5D..%7Cd.%5B%5Ev%5D%7Cf.%5B%5Er%5D%29%5B%5E%3E%5D*role%3D%5B%27%22%5D%3Fsearch%5B%27%22+%3E%5D&patternType=regexp ):
-> input, ul, li, components, aside, table, span, section??



### How to update JS code and libraries

Common use-cases:

#### Tagname conditions

- `element.tagName.toLowerCase() === 'form'`
- `element.tagName.toUpperCase() === 'FORM'`
- `element.tagName === 'FORM'`
- Variations with double quotes, `element.nodeName`, `element.localName`

-> `element instanceof HTMLFormElement`

#### Ancestor selector

- `element.closest('form')` -> `element.closest('form,search')`

#### Switch-case

- `case 'FORM'` ->
```
case 'FORM':
case 'SEARCH':
```

