---
title: Why is there disagreement? (in search element standardization)
---

### Why is there disagreement?

1. [Lack of research](#lack-of-research)
1. [Miscommunication](#miscommunication)
1. [Ignorance](#ignorance)
1. [What led to this outcome?](#what-led-to-this-outcome)


#### Lack of research

There was no research done to understand the implementation constraints and alternatives, risks and trade-offs. This led to the mistaken speculation about huge risks and complexities. No one could present evidence for those expected risks.

I have done that research and demonstrated that the [implementation]( https://kaleidea.github.io/whatwg-search-proposal/#implementations ) is trivial, pages won't just [randomly break](  ) and the [impact on the ecosystem]( https://kaleidea.github.io/whatwg-search-proposal/migration#how-to-update-js-code-and-libraries ) is small. I've spent 2 months to firmly disprove those speculations.


#### Miscommunication

I said reuse (inheritance, aliasing), the response was "duplication". [Again and again]( https://github.com/whatwg/html/issues/5811#issuecomment-964879335 ). Fundamentally different concepts that have to be clearly distinguished in the context of software engineering.

[Vague, unclear comments]( https://github.com/whatwg/html/issues/5811#issuecomment-979156406 ) and lack of evidence made communication even more ineffective.


#### Ignorance

Nobody has discussed or acknowledged the facts that I've presented in these documents. I was talking to a wall, while the speculations about fantom risks and complexities continued to repeat.


#### What led to this outcome?

It is Domenic's predetermined decision to avoid form functionality. Very few people have the privilege to disagree with him and be able to continue working with him. I am not one of them.

