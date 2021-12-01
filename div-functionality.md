---
title: Div functionality
---

### Div functionality

This solution satisfies the original use-case [defined](https://github.com/whatwg/html/issues/5811#issuecomment-958169793) as "people who don't want form behavior": `<search>` without the `action` attribute behaves as `<div role=search>`, the same as the original proposal.

Breakdown of individual form features:
- Form submission is disabled if the `action` attribute is unspecified.
- Autocomplete is not affected (it is already enabled by default for `<input type=search>`).
- There is no form validation unless the developer explicitly sets it up.
- There is no reset button unless the developer adds one.
- `<search>` is registered in `document.forms`, this has no effect unless the developer iterates `forms`, in which case this is expected.
- Form controls within `<search>` are associated to it. This has no unwanted effect. In fact, it is a reasonable expectation to wire up the form controls within a `search` element [since](https://github.com/whatwg/html/pull/7320/commits/c61cd6e884efde1c126922d145f00ce9fec1d1cb#diff-41cf6794ba4200b839c53531555f0f3998df4cbb01a4d5cb0b94e3ca5e23947dR19961) `<search>` "contains a set of form controls [...] **related** to performing a search".

The above features are available to the developer *optionally*, if needed. The original proposal lacks these features and developers have to work around with a nesting pattern that contradicts the common sense expectation, leading to a steeper learning curve, errors and a different a11y tree, that [brings up long-dormant bugs](https://github.com/whatwg/html/issues/5811#issuecomment-961781730).

With these considerations it is unreasonable to restrict the `<search>` element to `<div>` semantics and exclude probably the most common usage. Doing so would have negative effects in the long term.
