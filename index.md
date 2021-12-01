---
title: Search element with form functionality: proposal
---

## HTML `<search>` element with `form` functionality - specification and implementation draft

This is the proposed specification and implementation of the `search` element following the best practice usage ([MDN]( https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles/Search_role ), [WAI-ARIA]( https://www.w3.org/TR/wai-aria-practices/examples/landmarks/search.html )), as [requested by developers]( #requests-for-this-feature ). It covers both usage patterns: server-side and client-side rendering of the search results.

- Specification draft:
    - [Readable form]( https://search-element-html-spec.netlify.app/multipage/forms.html#the-search-element ) (generated)
    - [Specification changes](#specification-changes) (source)
- Implementations (proof-of-concept):
    - [Chromium]( https://github.com/Kaleidea/chromium/commits/search-element ) (commits on GitHub)
    - [WebKit]( https://github.com/Kaleidea/WebKit/commits/search-element ) (commits on GitHub)
    - Firefox in progress (commits in Mercurial)
- [Implementation explanation](implementation)


#### Contents
1. [Specification changes](#specification-changes)
1. [Motivation](#motivation)
1. [Design decisions](#design-decisions)
1. [Use-cases](#use-cases)
1. [Requests for this feature](#requests-for-this-feature)
1. [Prior art](#prior-art)

#### Subpages
1. [Migration guide, usage patterns](migration)
1. [Implementation explanation](implementation)
1. [Including the original proposal](div-functionality): `<div>` functionality
1. [Alternatives](alternatives)

<br>

### Specification changes

- [Readable form]( https://search-element-html-spec.netlify.app/multipage/forms.html#the-search-element ) (generated)
- [Categorised changes]( https://github.com/Kaleidea/whatwg-html/commits/search-form ) (source):
    - What developers will read: ["The search element" section]( https://github.com/Kaleidea/whatwg-html/commit/ffb121049e7593c8ee0fdd2024484d30d0c2b5db )
    - [Add the `<search>` element to tag lists]( https://github.com/Kaleidea/whatwg-html/commit/51bd17e8c4cb3e86938671eddf002f7d0c80bc34 )
    - Systematic changes: [Rewording "form elements" -> "forms", etc.]( https://github.com/Kaleidea/whatwg-html/commit/fbb1c7de652290c3ab455f5c8f1d83a598942fc9 )
    - Optional: [Clarification notes]( https://github.com/Kaleidea/whatwg-html/commit/4d85297a8217a047283c15d93bf056e20d56cf53 )
- [Breakdown of changes - pull request]( https://github.com/whatwg/html/pull/7382/commits ): each commit is a different transformation pattern for quicker (batched) verification of the systematic editorial changes.

<br>

### Motivation

The `<search>` element with `form` functionality is
- a [popular developer request]( #requests-for-this-feature )
- the best practice as demonstrated by the [MDN example]( https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles/Search_role ) and the [WAI-ARIA example]( https://www.w3.org/TR/wai-aria-practices/examples/landmarks/search.html ).
- the common sense [mental model]( https://search-element-html-spec.netlify.app/multipage/forms.html#the-search-element ): "The `search` element represents a part of a document or application that **contains a set of form controls** or other content related to performing a search or filtering operation." A set of form controls is a form.

This approach meets developers' expectations, guaranteeing the best API ergonomy. In the long term this will reduce the learning curve, increase developer satisfaction, make accessibility more intuitive and consequently improve the quality of the World Wide Web. These long-term benefits are worth the little extra effort up front, which is mostly a systematic, trivial rewording of the "Forms" section in the HTML specification to refer to "[form]( https://search-element-html-spec.netlify.app/multipage/forms.html#concept-form )" instead of "form element".

<br>

### Design decisions

1. `<div role=search>` is shortened to `<search>`, form submission is disabled
1. `<form role=search action="...">` is shortened to `<search action="...">`

In the first case (`div`) form submission is an unwanted feature, therefore disabled. The second case behaves equivalently to a `form` with `role=search`.
The `action` attribute's presence distinguishes between the two cases. A `form` without the `action` attribute is migrated to `<search action="">`, this slightly alters the [specification of the `action` attribute]( https://search-element-html-spec.netlify.app/multipage/form-control-infrastructure.html#attr-search-action ) on the `search` element.

Alternatives considered: `method=none` / `nosubmit` boolean attribute. To support the migration of the `div` usage without adding extra attributes the absence of  `action` must disable form submission, therefore the alternatives would be redundant.

Other form functionality is opt-in, if the developer makes no use of it then it does not change the application's behavior:
- Form validation is optional.
- Reset button is optional.
- Autocomplete is on by default for `<input type=searchbox>` even without a form.
- Registering `<search>` in `document.forms` has no effect on behavior.
- Associating with descendant form controls has no effect on behavior unless the developer makes use of it in which case it is a benefit.

Nesting of `<search>` and `<form>` is invalid in any combination:
- `<search><search>` is semantically meaningless.
- `<div role=search>...<form>` and `<form>...<div role=search>` can often be [refactored](https://gist.github.com/Kaleidea/19851b737cf83cbf678504fbc643b08b#nesting) to a single `<search>`. Researching [usage patterns in the wild](https://gist.github.com/Kaleidea/19851b737cf83cbf678504fbc643b08b#usage-patterns) suggests this to be possible in most cases.

Shadow DOM:
- `<search>` cannot be a shadow root: `.attachShadow()` is not allowed (like `<form>`, but unlike `<div>`). ([MDN]( https://developer.mozilla.org/en-US/docs/Web/API/Element/attachShadow#elements_you_can_attach_a_shadow_to ))

<br>

### Use-cases

1. Site search: typically in the header/sidebar, providing a pop-up list of suggestions (client-side rendered), navigating to a results page (server-side rendered).
2. Table/dataset filtering: in the main content, typically client-side rendered.
3. Page search (eg. text editor): changes positioning and selection client-side.

The most common use-case for a `<search>` element is site search. The best practice for implementation is `<form role="search" action="/search">`. Exception to this is client-side rendered search results (use-cases without page navigation). In that case a site search page (also advertised in sitesearch.xml) can be used as a no-JS fallback. JS execution cannot be taken for granted, therefore a fallback improves availability in case of unreliable internet or disabled JS.

<br>

### Requests for this feature

- [tweet](https://twitter.com/AmeliasBrain/status/1437942737647509504) - "Using a `<form role="search">` gives you significant functionality beyond the ARIA semantics."
- [tweet](https://twitter.com/WestbrookJ/status/1437897675337347080) - "Should the `<search>` element be an extension of the `<form>` element such that it can process its own form actions without having to also be wrapped in a `<form>`?"
- [tweet](https://twitter.com/_TooAndrew/status/1437930256208482304) - "\<search\> makes sense and would be great if [...] it could process its own form actions without a \<form\>. I would expect that feature would make (correct) usage of the element by devs more likely too"
- [tweet](https://twitter.com/kaleidealogy/status/1438220270242779146) - "The semantics of \<form role="search"\> is more motivating."
- [tweet](https://twitter.com/devongovett/status/1438632248908005377) - "Aren't you [meant](https://www.w3.org/TR/wai-aria-practices/examples/landmarks/search.html) to add `role="search"` to the `<form>` element rather than a separate element?"
- [tweet](https://twitter.com/freddyb/status/1438033831093841921) - "Extra attribute on a \<form\>?"
- [github](https://github.com/whatwg/html/issues/5811#issuecomment-957864625) - "I haven't run into a need for \<div role="search"\> instead of \<form role="search"\>"
- [github](https://github.com/whatwg/html/issues/5811#issuecomment-962438379) - "I would be unlikely to employ `<search>` without `<form>`"

<br>

### Prior art

This proposal builds upon the request for the `<search>` element implementing `<div role=search>`. The decisions made there that don't affect form functionality also apply here:
- `search { display: block }` in the user agent [stylesheet](https://github.com/whatwg/html/issues/5811#issuecomment-961228338)
- Is it a [paragraph closing element]( https://github.com/whatwg/html/pull/7320#issuecomment-969281707 )? Implementation cost: [2 lines of code]( https://github.com/Kaleidea/chromium/commit/0a15a4c15bd952bc3b439d2607839a3942d42665 ).

<br>

### Subpages

Due to length these are split:
1. [Migration guide, usage patterns](migration)
1. [Implementation details](implementation)
1. [Including the original proposal](div-functionality): `<div>` functionality
1. [Alternatives](alternatives)
