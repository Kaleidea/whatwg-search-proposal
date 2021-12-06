---
title: Alternatives
---

### Alternatives


#### To disable form submission

- `method=none` / `nosubmit` boolean attribute. To support the migration of the `div` usage without adding extra attributes the absence of  `action` must disable form submission, therefore the alternatives would be redundant.


#### No hybrid element

Define 2 elements (`<search>` and `<searchform>`) for the 2 use-cases:

- `<div role=search>` --> `<search>`
- `<form role=search action="...">` --> `<searchform action="...">`

Implementing this has the same cost as the hybrid solution, in which `HTMLFormElement` branches in one location in `ScheduleFormSubmission()` to differentiate between `<search>` and `<form>`. That condition would be replaced by another condition in `HTMLTreeBuilder` to match `<searchform>`.

The only benefit would be that `<search>` with an unset `action` attribute behaves consistently with `<form>`.


#### Disable form submission with JavaScript

- `<form role=search>` --> `<search>`
- `<div role=search>` --> `<search submit="return false;">`

Implementing this has almost the same cost as the hybrid solution: it saves one condition in `ScheduleFormSubmission()`.
The drawback is that use-cases for client-side rendering require the boilerplate of a submit event handler.
