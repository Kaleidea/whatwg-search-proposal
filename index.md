---
title: HTML search element explainer
---

## HTML \<search\> element - specification draft and implementation

The `<search>` element is a new semantic HTML element to mark the ARIA search landmark. It represents the common functionality of searching a website, contents on a webpage or filtering a dataset.

- `<search>` is a short form of `<div role=search>`,
- `<search action="...">` is a short form of `<form role=search action="...">`.

The benefit of this element is making the [best practice]( https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles/Search_role ) for accessibility simpler, more intuitive and semantically meaningful.


#### Contents
1. [Motivation](#motivation)
    1. [Developer experience]( developer-experience )
1. [Design decisions](#design-decisions)
    1. [Form submission](#form-submission)
    1. [Other form functionality](#other-form-functionality)
    1. [Nesting](#nesting)
    1. [Shadow DOM](#shadow-dom)
    1. [Layout](#layout)
1. [Use-cases](#use-cases)
1. [Specification changes](#specification-changes)
1. [Requests for this feature](#requests-for-this-feature)

#### Subpages
1. [Use-case without form functionality](div-functionality): Satisfying the original proposal.
1. [Migration guide, usage patterns](migration)
1. [Implementation explanation](implementation)
1. [Alternatives](alternatives)
1. [Test page](testpage)
1. [What went wrong in the standardization process](standardization-flaws/)

#### Specification draft
- [Readable form]( https://search-element-html-spec.netlify.app/multipage/forms.html#the-search-element ) (generated)
- [Specification changes](#specification-changes) (source)
- [Pull request]( https://github.com/whatwg/html/pull/7382 )

#### Implementations
- [Chromium]( https://github.com/chromium/chromium/pull/100/commits ) (prototype, commits on GitHub)
- [WebKit]( https://github.com/Kaleidea/WebKit/commits/search-element ) (proof-of-concept, commits on GitHub)
- Firefox in progress (commits in Mercurial)

<br>

### Motivation

The `<search>` element with `form` functionality is
- a [popular developer request]( #requests-for-this-feature )
- the best practice as demonstrated by the [MDN example]( https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles/Search_role ) and the [WAI-ARIA example]( https://www.w3.org/TR/wai-aria-practices/examples/landmarks/search.html ).
- the common sense [mental model]( https://search-element-html-spec.netlify.app/multipage/forms.html#the-search-element ): "The `search` element represents a part of a document or application that **contains a set of form controls** or other content related to performing a search or filtering operation." A set of form controls is a form.
- better [developer experience]( developer-experience )

This approach meets developers' expectations, guaranteeing the best API ergonomy. In the long term this will reduce the learning curve, increase developer satisfaction, make accessibility more intuitive and consequently improve the quality of the World Wide Web. These long-term benefits are worth the little extra effort up front, which is mostly a systematic, trivial rewording of the "Forms" section in the HTML specification to refer to "[form]( https://search-element-html-spec.netlify.app/multipage/forms.html#concept-form )" instead of "form element".

#### Developer experience

- KISS: Simplicity of use, learning: matches the [established practice]( https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles/Search_role ).
- No team debates about which element is outside: there's only one (simpler topology).
- Supports the best practice: the current trend is to render more server-side. Even client-side rendered solutions can benefit from a fallback to SSR when JS loading is unreliable, this is the best practice, superior to pure SPAs.
- Future-proofing: If you have a non-form `<search>` and one day decide to enable native form submission then all it takes is to add the `action` attribute. With the `<search><form>` solution a new level is added to the DOM tree, which can break CSS and JS selectors, traversal logic.


<br>

### Design decisions

1. `<div role=search>` is shortened to `<search>`, form submission is disabled (client-side rendering of search results)
1. `<form role=search action="...">` is shortened to `<search action="...">` (server-side rendering of search results)
1. `<form role=search>` becomes `<search action>` (server-side rendering of search results, target URL is the document's)

#### Form submission
Form submission is when the browser sends the contents of a form to a server and navigates to the webpage in the response. This includes implicit form submission which happens when pressing ENTER (or equivalent) or the first submit button.

In the first case (`div`) form submission is an unwanted feature, therefore disabled. The second case behaves equivalently to a `form` with `role=search`.
The `action` attribute's presence distinguishes between the two cases. A `form` without the `action` attribute becomes `<search action="">`, this slightly alters the [specification of the `action` attribute]( https://search-element-html-spec.netlify.app/multipage/form-control-infrastructure.html#attr-search-action ) on the `search` element.

Alternatives considered: `method=none` / `nosubmit` boolean attribute. To support the migration of the `div` usage without adding extra attributes the absence of  `action` must disable form submission, therefore the alternatives would be redundant.

#### Other form functionality
Other form functionality is opt-in and defined by the form controls.  if the developer makes no use of it then it does not change the application's behavior.
- Form validation is optional.
- Reset button is optional.
- Autocomplete is on by default, even w/o a form element, controlled by the individual form controls.
- Registering `<search>` in `document.forms` has no effect on behavior.
- Associating with descendant form controls has no effect on behavior unless the developer makes use of it, in which case it is a benefit.

#### Nesting
Nesting of `<search>` and `<form>` is invalid in any combination:
- `<search><search>` is semantically meaningless.
- `<div role=search>...<form>` and `<form>...<div role=search>` can often be [refactored]( https://kaleidea.github.io/whatwg-search-proposal/migration#nesting ) to a single `<search>`. Researching [usage patterns in the wild]( https://kaleidea.github.io/whatwg-search-proposal/migration#usage-patterns ) suggests this to be possible in most cases.

#### Shadow DOM
- `<search>` cannot be a shadow root: `.attachShadow()` is not allowed (like `<form>`, but unlike `<div>`). ([MDN]( https://developer.mozilla.org/en-US/docs/Web/API/Element/attachShadow#elements_you_can_attach_a_shadow_to ))

#### Layout
- `form, search { display: block; margin-top: 0; }` in the user agent [stylesheet]( https://github.com/Kaleidea/chromium/commit/30e21a0bc72f9df0bda67bc956093a1da0390be9#diff-039ad02aad25eca0593554285fa92cddc5a4964e934144a500802f40a2968838R367 ) (standards mode, no change to quirks mode)


<br>

### Use-cases

1. Site search: typically in the header/sidebar, providing a pop-up list of suggestions (client-side rendered), navigating to a results page (server-side rendered).
2. Table/dataset filtering: in the main content, typically client-side rendered.
3. Page search (eg. text editor): changes positioning and selection client-side.

The most common use-case for a `<search>` element is site search. The best practice for implementation is `<form role="search" action="/search">`. Exception to this is client-side rendered search results (use-cases without page navigation). In that case a site search page (also advertised in sitesearch.xml) can be used as a no-JS fallback. JS execution cannot be taken for granted, therefore a fallback improves availability in case of unreliable internet or disabled JS.

<br>

### Specification changes

- [Readable form]( https://search-element-html-spec.netlify.app/multipage/forms.html#the-search-element ) (generated)
- [Breakdown of changes - pull request]( https://github.com/whatwg/html/pull/7382/commits ): each commit is a different transformation pattern for quicker (batched) verification of the systematic editorial changes.
- [Categorised changes]( https://github.com/Kaleidea/whatwg-html/commits/search-form ) (source):
    - What developers will read: ["The search element" section]( https://github.com/Kaleidea/whatwg-html/commit/ffb121049e7593c8ee0fdd2024484d30d0c2b5db )
    - [Add the `<search>` element to tag lists]( https://github.com/Kaleidea/whatwg-html/commit/51bd17e8c4cb3e86938671eddf002f7d0c80bc34 )
    - Systematic changes: [Rewording "form elements" -> "forms", etc.]( https://github.com/Kaleidea/whatwg-html/commit/fbb1c7de652290c3ab455f5c8f1d83a598942fc9 )
    - Optional: [Clarification notes]( https://github.com/Kaleidea/whatwg-html/commit/4d85297a8217a047283c15d93bf056e20d56cf53 )

<br>

### Requests for this feature

- [tweet](https://twitter.com/_TooAndrew/status/1437930256208482304) - "\<search\> makes sense and would be great if [...] it could process its own form actions without a \<form\>. I would expect that feature would make (correct) usage of the element by devs more likely too"
- [tweet](https://twitter.com/AmeliasBrain/status/1437942737647509504) - "why would an author ever use this new element? Using a `<form role="search">` gives you significant functionality beyond the ARIA semantics."
- [tweet](https://twitter.com/kaleidealogy/status/1438220270242779146) - "The semantics of \<form role="search"\> is more motivating."
- [tweet](https://twitter.com/WestbrookJ/status/1437897675337347080) - "Should the `<search>` element be an extension of the `<form>` element such that it can process its own form actions without having to also be wrapped in a `<form>`?"
- [github](https://github.com/whatwg/html/issues/5811#issuecomment-957864625) - "I haven't run into a need for \<div role="search"\> instead of \<form role="search"\>"
- [tweet](https://twitter.com/devongovett/status/1438632248908005377) - "Aren't you [meant](https://www.w3.org/TR/wai-aria-practices/examples/landmarks/search.html) to add `role="search"` to the `<form>` element rather than a separate element?"
- [tweet](https://twitter.com/freddyb/status/1438033831093841921) - "Extra attribute on a \<form\>?"
- [github](https://github.com/whatwg/html/issues/5811#issuecomment-962438379) - "I would be unlikely to employ `<search>` without `<form>`"

<br>

### Subpages

Due to length these are split:
1. [Migration guide, usage patterns](migration)
1. [Implementation details](implementation)
1. [Alternatives](alternatives)
