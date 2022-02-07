---
title: Implementation details
---

### Implementation details

Implementations:
- [Chromium]( https://github.com/chromium/chromium/pull/100/commits ) (prototype, commits on GitHub)
- [WebKit]( https://github.com/Kaleidea/WebKit/commits/search-element ) (proof-of-concept, commits on GitHub)
- Firefox in progress (commits in Mercurial)

<br>


#### Implementation complexity

In Chromium:
- Checks for `kSearchTag` when aliasing `div`: 5 = 2 (parser) + 1 (aria role) + 2 (`<p>` closing)
- Checks for `kSearchTag` when aliasing `form`: 10 = 3 (parser) + 1 (aria role) + 2 (`<p>` closing) + 1 (form) + 3 (collections)

<br>


#### DOM building
- Constraint: The HTMLFormElement class in the 3 major browsers is `final` for performance.
- `HTMLTreeBuilder` (Blink, WebKit): `kSearchTag` instantiates an HTMLFormElement.
- `html_tag_names.json5` (Blink): `name: "search", interfaceName: "HTMLFormElement"` generates `HTMLSearchConstructor()` -> `HTMLElementFactory::Create()`
- `HTMLTagNames.in` (WebKit): `search interfaceName=HTMLFormElement` generates `searchConstructor()` -> `HTMLElementFactory::createKnownElement()` (WebKit)


#### ARIA
- `role=search` if the element tag is `search`, otherwise unchanged (`role=`[`form` with an accessible name](https://w3c.github.io/html-aam/#el-form-accessible-name), `role=none` without).


#### Disabling form submission
- HTMLFormElement's form submit functionality is disabled if the element tag is `search` and the `action` attribute is unspecified.
- Empty / whitespace-only `action=""` attribute resolves to the current page's address.
- `HTMLFormElement::ScheduleFormSubmission()` method returns after handling `method=dialog`.

Sidenote: if `<form>` wouldn't be standardized yet, I'd suggest the same functionality.
<br>


#### Form behavior that's not disabled: *no change*
- `<input type=reset>` and form validation remains functional. It these features are used, this is what would be expected without `action`.
- `autocomplete` is on by default:
	- "If there is no form owner, then the value "on" is used." ([HTML spec](https://html.spec.whatwg.org/multipage/form-control-infrastructure.html#attr-fe-autocomplete))
	- "The *missing value default* and the *invalid value default* are the `on` state." ([HTML spec](https://html.spec.whatwg.org/multipage/forms.html#attr-form-autocomplete))
- The search element is registered in `document.forms`. This is different from `<div role=search>`, but documented behavior for the `<search>` element, therefore developers would be aware. This is more intuitive, even.
- Form associated elements (form controls) associate with the closest `<form>` or `<search>` ancestor. The presence or absence of `action` has no effect on association.
<br>


#### Nesting: *no change*
- For implementation simplicity nesting of `<search>` and `<form>` elements is invalid in any order.
- `<search><search>` - it's semantically not meaningful to put a search component within a search component.
<br>


#### User-agent stylesheet
- `form, search { display: block; margin-top: 0; }` in the user agent stylesheet (Blink: `third_party/blink/renderer/core/html/resources/html.css`).


#### Shadow DOM
- `<search>` cannot be a shadow root: `.attachShadow()` is not allowed (like `<form>`, but unlike `<div>`). ([MDN]( https://developer.mozilla.org/en-US/docs/Web/API/Element/attachShadow#elements_you_can_attach_a_shadow_to ))


#### Further considerations
- Any affected functionality missing?
- Any layout inconsistencies with `<div>`?
