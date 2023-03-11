---
layout: post
title:  "Design Patterns - Adapter"
date:   2023-02-18T08:00:00-00:00
author: Graciele Damasceno
categories: technology
tags:    tutorial design-pattern java adapter
lang: en
---

***Adapter*** is a design pattern with the purpose of - as the name suggests - adapt one context to another, allowing two incompatible interfaces to be connected.

The main benefit of applying this pattern is to allow switching from an existing interface to another without changing the original behavior of the existing interface. Adapter is also used when there's the necessity of creating a reusable class that interacts with incompatible interfaces.

Let's see this pattern in action!

### Adapting an existing output to a new one

In our scenario, we have a Person POJO that contains a few attributes.

<br>

<a href="/dev-on-track/assets/posts/2023-02-18/Person.png" data-lightbox="adapter" data-title="Person.java">
  <img src="/dev-on-track/assets/posts/2023-02-18/Person.png" title="Person.java">
</a>

<br>

There's also an interface that dictates the contract of a Person. In our case, the existing contract states that the `Person` output should be a string formatted in XML.

<br>

<a href="/dev-on-track/assets/posts/2023-02-18/PersonOutput.png" data-lightbox="adapter" data-title="PersonOutput.java">
  <img src="/dev-on-track/assets/posts/2023-02-18/PersonOutput.png" title="PersonOutput.java">
</a>

<br>And the `LegacyPersonService` implements that contract, building a `Person` object and returning it in XML written as string.

<br>

<a href="/dev-on-track/assets/posts/2023-02-18/LegacyPersonService.png" data-lightbox="adapter" data-title="LegacyPersonService.java">
  <img src="/dev-on-track/assets/posts/2023-02-18/LegacyPersonService.png" title="LegacyPersonService.java">
</a>

<br>

This architecture works well for quite a while, until we have to redesign our system. Since we have a lot of external and crucial dependencies, the XML output has to coexist with the new solution, which has the objective to output the Person object as a string in JSON format.

That's exactly when the adapter pattern comes in handy!

We are going to adapt our previous solution to generate a JSON, following the new requirements. First, let's implement an interface that carries this new contract.

<br>

<a href="/dev-on-track/assets/posts/2023-02-18/PersonOutputAdapter.png" data-lightbox="adapter" data-title="PersonOutputAdapter.java">
  <img src="/dev-on-track/assets/posts/2023-02-18/PersonOutputAdapter.png" title="PersonOutputAdapter.java">
</a>

<br>Now, we create the adapter: implementing the interface created earlier, we instantiate the `LegacyPersonService` class that contains the logic of the old contract and implement the new logic to transform the response into a string in JSON.
<br>

<a href="/dev-on-track/assets/posts/2023-02-18/ModernizedPersonAdapter.png" data-lightbox="adapter" data-title="ModernizedPersonAdapter.java">
  <img src="/dev-on-track/assets/posts/2023-02-18/ModernizedPersonAdapter.png" title="ModernizedPersonAdapter.java">
</a>

<br>

With that created, we can safely create a new version of our endpoint which serves the new specification, without changing the existing one.

<br>

<a href="/dev-on-track/assets/posts/2023-02-18/PersonController.png" data-lightbox="adapter" data-title="PersonController.java">
  <img src="/dev-on-track/assets/posts/2023-02-18/PersonController.png" title="PersonController.java">
</a>

<br>

So, to summarize:

- The adapter pattern follows the **open-closed principle**. We can add new adapters without altering the existing code.

- The adapter pattern also follows the **single responsibility** principle, since we separated the interface/contract from the existing business logic.

- In our case, it was used composition at  `ModernizedPersonAdapter` to wrap the target of the adapter (`LegacyPersonService`). This way of dealing with the *adaptee* class is called **Object Adapter**. 
  
  - The other way of implementing an adapter would be using the **Class Adapter** style, where instead of creating a `LegacyPersonService` object in `ModernizedPersonAdapter`, we would simultaneously extend `LegacyPersonService` and implement `PersonOutputAdapter`.  Both are valid approaches, however, it's best to favor composition over inheritance, since it allows the system to be easier to test, less complex and decoupled.

- The main disadvantage that this pattern offers is the increase in complexity when adding new components to the application. Depending on the situation and external constraints, it's better to just refactor the existing code to match the new contract.

<br>

You can check the full implementation displayed [here @ my Github: GracieleDamasceno/design-patterns](https://github.com/GracieleDamasceno/design-patterns)! Till next time! 

<br>

#### References:

[Composition over inheritance - Wikipedia](https://en.wikipedia.org/wiki/Composition_over_inheritance)

[Adapter Pattern - Refactoring Guru](https://refactoring.guru/design-patterns/adapter)
