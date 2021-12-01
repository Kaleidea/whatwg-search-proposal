---
title: Alternatives
---

### Alternatives

Should the implementation of a [hybrid element as defined](  ) fall through, an alternative is to define 2 elements (`<search>` and `<searchform>`) for the 2 use-cases:

- `<search>` === `<div role=search>`
- `<searchform action="...">` === `<form role=search action="...">`

Implementing this is trivial by aliasing `HTMLElement` -> `<search>` and `HTMLFormElement` -> `<searchform>`. The specification still needs rewording to incorporate `<searchform>` as a `<form>`. The only benefit would be that the `action` attribute's specification is not changed.

Should 2 elements find opposition, the proposal would be to favor the best practice:

- `<search action="...">` === `<form role=search action="...">`
- `<search>` can be used with form submission dissabled by the `submit` event handler.
- `<div role=search>` remains the suggested usage if form functionality is to be avoided.

Implementing this is trivial by aliasing `HTMLFormElement` -> `<search>`. he specification still needs rewording to incorporate `<search>` as a `<form>`. The drawback is that a few use-cases of the original request is not treated as first-class. The reasoning above implies that the best practice is more important to be treated as first-class, thus if a choice has to be made it should be to favor `form` semantics. The hybrid element proposal makes a strong effort to avoid such trade-off.
