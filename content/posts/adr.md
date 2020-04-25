---
title: "Architecture Decision Records"
date: 2020-04-25T18:32:59+05:30
draft: false
tags: [
    "Architecture", "Documentation"
]
---
Every software developer that ever worked on a project that has been under development for a considerable amount of time  or ever dealt with a mature codebase, would have criticised the way things are structured in the project or may have even cussed about the orchestration of the components and the design overall. 

This inevitably seems to happen in scenarios where a new developer is on-boarded to work on a project/product that is already in production and has real users using that product. But in the real world software development, it is always mostly the case that there is a specific motivation behind the way components talk to each other or the way each of the components are designed. Unless we are tying up a bunch of components in a haste to bring a product up and running, logical decisions always direct the way a project/product is designed. 

This design in a general sense is what constitutes the architecture of a project. To address and to ease the friction that is caused during the initial phases of getting used to a new project or a codebase, it is beneficial to understand the architecture. Also, in all practical cases, it is not possible for one an individual to remember all the decisions made along the lifecycle of software development. Particularly, having an understanding of why the product is architected in a certain manner, helps every developer working on a project to comprehend the product's architecture better. It leads to a healthy dev culture in a team where every developer is aware of the history of the product's architecture and this can help in keeping up the quality of the product in the longer-run of the product lifecycle. This is where **Architecture Decision Records** (ADR) come to the rescue. This blog post walks you through what ADRs are all about and how you can get started with using them.

## What are Architecture Decision Records anyway?
Architecture Desicion Records are not something new and have been around for quite some time. 

As per [Thoughtwork's Tech Radar](https://www.thoughtworks.com/radar/techniques/lightweight-architecture-decision-records), Architecture Decision Records is a technique for capturing important architectural decisions that are made along the way of development of a project along with the context and consequences. To put it simply, ADR is a timelined view of the architecture of a system that encapsulates the decisions made since the inception of the system together with the consequences of each of those decisions. Given that a system's design and architecture evolve with time, ADR feels like a natural fit that presents the evolution of the system.

## Are ADRs a substitute to my documentation?
To answer that in a single word, **No**. In general, documentation for a project may deal with the intricacies of the project at a much deeper level (aka source code level). However, in most cases of enterprise software development, there is a clear lack of well maintained documentation of the source code unless there is a strong emphasis for it. There is also a trend among the developers where there is preference to well-written, readable code over scores of long documentation and this makes total sense. Under all these circumstances, having *Architecture Decision Records* can serve as the first step towards documenting a system in cases where is there is no documentation in place and a *huge* addition to projects that have well-maintained documentation.

## How do I begin to use ADRs?
There are multiple ways of dealing with Architecture Decision Records and we are going to adopt a really simple way that is first described by [Michael Nygard](http://thinkrelevance.com/blog/2011/11/15/documenting-architecture-decisions). Each of the ADRs will be maintained as a simple markdown file. Before jumping into how we are going to leverage ADRs in our projects, let's understand the fundamental structure and semantics of an ADR.

### Structure of an ADR
An ADR contains the following sections:

* A concise *Title* for the ADR along with the timestamp of when that decision was taken
* *Status* of the ADR
* The *Context* under which the decision is being made
* The actual *Decision* being made
* *Consequences* of the decision

### **ADR Template**

### *1. Title of the decision*
Date: 2020-04-25

### *Status*
Status can be one among *Pending*, *Accepted*, *Deprecated* or *Superseded*

### *Context*
This section encapsulates all the factors due to which we are making this decision and the facts that are steering the decision. These include all the technological, business, logical and other every other facet of factors affecting this decision

### *Decision*
This is where we describe the actual decision that is being made

### *Consequences*
This sections lists all the possible consequences that may ensue after a decision is implemented. This should include every possible/conceivable consequence, positive, negative and neutral.

## How to include and maintain ADRs in my project?
Since we are adopting the markdown way of handling ADRs, we can include them within our project repository. One possible directory structure could be **/docs/architecture/decisions** (relative to root directory of our repository). It is always advised to follow a convention that allows for a sequence among the ADRs. An example could be **XXXX-Title-of-ADR**. Replace the placeholder with the title of your ADR and *XXXX* could be the sequence beginning from *0001* for your first ADR.

Always keep in mind that ADRs are living, breathing documents just like our source code. We never delete any ADR that is accepted, but only mark them *Deprecated* when a particular record is no longer valid. Make sure to review your ADRs over a period of time. This has to be done with the same discipline we perform our code reviews.

Additionally, architecture decisions that pertain to any cross-cutting concerns may be maintained separately in a different directory. It is not a hard and fast rule to do so, but this eliminates plausible clutter and provides a clean view of our architecture decisions.

## Are there any tools to adopt ADRs?
To include the markdown style of ADRs that we discussed above, we can make use of [*adr-tools*](https://github.com/npryce/adr-tools). This is an actively maintained project (at the time of writing) and provides a seamless way to begin with adopting ADRs. This also provides a way to maintain ADRs consistently across a team as the conventions for handling ADRs is solved at the tool level itself. Also, the best point of this tool is that there is no specific configuration required on the server side to get started.

## Further Steps
The Architecture Decision Records are just the beginning in the journey of documentation but can add huge value to a project. Especially, projects that lack documentation can begin with ADRs to bring all the developers and stakeholders involved in the project to come on to the same page in terms of what the stage of the project is. 

As a next step to this, to proceed with detailed documentation of a project, there are several methodologies that could be explored. I suggest taking a look at [***C4 Model***](https://www.infoq.com/articles/C4-architecture-model/). This is a natural transition if you're starting off with ADRs. 

## Suggested Reading

* https://www.thoughtworks.com/radar/techniques/lightweight-architecture-decision-records
* http://thinkrelevance.com/blog/2011/11/15/documenting-architecture-decisions
* https://github.com/npryce/adr-tools
* https://c4model.com
* https://www.infoq.com/articles/C4-architecture-model
* https://github.com/structurizr
