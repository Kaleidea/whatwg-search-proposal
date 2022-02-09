---
title: Standardization errors (in search element standardization)
---

### Standardization errors

1. [Inappropriate procedure](#inappropriate-procedure)
1. [Speculation instead of research](#speculation-instead-of-research)
1. [Contempt towards developers](#contempt-towards-developers)
1. [W3C Design Principle violation](#w3c-design-principle-violation)


#### Inappropriate procedure

The WHATWG's [new feature guideline]( https://whatwg.org/faq#adding-new-features ) was not followed. Crucial steps were skipped that would ensure that the proposal best meets the web developer community's needs. This is detailed in the [appeal according to the WHATWG workstream policy](  ). Briefly:
- The lead editor enforced his predetermined solution (["we will add zero new elements with any form-like functionality"]( https://archive.ph/bjOzS#issuecomment-962488118 )).
- Described the alternative as ["makes no sense"]( https://twitter.com/domenic/status/1438229658324111360 ) instead of evaluating it.
- Described developer feedback requesting form functionality as ["confusion"]( https://github.com/whatwg/html/issues/5811#issuecomment-956454034 ).
- [Hastily suppressed]( https://github.com/whatwg/html/issues/7323#issuecomment-965145881 ) the alternative solution and ignored its contents.


#### Speculation instead of research

I am familiar with the relevant codebases in 3 browsers, with that knowledge I can tell that other participants are not familiar. The lead editor did not consult with implementers to evaluate complexity. A damaging consequence has been that Domenic and Scott believe that form functionality would be implemented by duplication ([1]( https://twitter.com/domenic/status/1437931771346268165 ), [2]( https://github.com/whatwg/html/issues/5811#issuecomment-958169793 ). That would be a terrible solution.

That's obviously not the case: browser DOM implementations are very well designed, code reuse is easily achieved. In fact, I've implemented the search element specifically **to prove that adding form functionality is trivial**: instead of mapping `<search>` to `HTMLElement`, it is mapped to `HTMLFormElement`, and a few nuances.

Unfortunately, this confusion about "huge implementation costs" repeated by Domenic and Scott [proliferates]( https://github.com/whatwg/html/issues/5811#issuecomment-962469704 ) to this day. They have ignored all my efforts to prove the minimal complexity. There is no interest, no discussion about it. I feel that weeks of research and implementation were for nothing.


#### Contempt towards developers

"a lot of people expressed *confusion* about when they would use this versus using `<form role="search">`."
[That]( https://github.com/whatwg/html/issues/5811#issuecomment-956454034 ) is a response to [developers requesting form functionality]( https://kaleidea.github.io/whatwg-search-proposal/#:~:text=Optional%3A%20Clarification%20notes-,Requests%20for%20this%20feature,-tweet%20%2D%20%E2%80%9CUsing%20a ).

This attitude characterizes the entire process that led to this proposal. Such attitude was also present in the [`alert()` debacle]( https://groups.google.com/a/chromium.org/g/blink-dev/c/hTOXiBj3D6A/m/QnvjjvCSAwAJ ) and previous cases of developers having a different POV. One wonders how long the developer community tolerates this.


#### W3C Design Principle violation

The proposal violates the [first principle]( https://www.w3.org/TR/design-principles/#priority-of-constituencies ) ("Priority of Constituencies")
by putting implementors' and specification writers' [needs ahead of authors]( https://github.com/whatwg/html/issues/5811#issuecomment-958169793 ): "huge implementation and spec lift that isn't worth it" (Fact correction: the implementation is trivial, the spec requires 3 days' worth of editorializing [that I've already done]( https://github.com/whatwg/html/pull/7382/commits )).

Domenic decided against form functionality to avoid the need to research implementation risks and complexities. As an initial proposal that was an understandable trade-off, but once a volunteer has done that work it is inappropriate to take the path easier for spec writers.

