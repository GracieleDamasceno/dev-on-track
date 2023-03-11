---
layout: post
title:  "Design Patterns - Bridge"
date:   2023-03-03T08:00:00-00:00
author: Graciele Damasceno
categories: technology
tags:    tutorial design-pattern java bridge
lang: en
---

***Bridge*** is a structural design pattern that aims to decouple an abstraction of its implementation, in a way that allows both to evolve in an independent way.

This pattern usage is recommended when dealing with classes have many different implementations and use inheritance to implement the abstraction. In those cases, the inheritance connects an implementation to the abstraction permanently, in a way that it's hard to modify, increase and re-utilize the abstractions in an independent fashion.

To give an illustrated example, let's pretend that we have a scenario where we want to create different types of bank accounts: `CheckingAccount` and `SavingsAccount`. We want our system to be flexible to the creation of new accounts types, so we create an abstract class called `BankAccount` and extend this class with `CheckingAccount` and `SavingsAccount` classes.

Great! Later, we want to give our account types their own personalized credit card type. We create `BasicCreditCard`, `SilverCreditCard` and `GoldCreditCard` classes. When binding those credit card to each bank account type, the problem gets clear:

<br>
<a href="/dev-on-track/assets/posts/2023-03-03/bridge.png" data-lightbox="bridge" data-title="Implementation without bridge pattern">
  <img src="/dev-on-track/assets/posts/2023-03-03/bridge.png" title="Implementation without bridge pattern">
</a>
<br>

The naive approach would be to create a combination for each account type, a solution which is costly and grows exponentially (imagine how much work if we add a new account type!). The code is also highly coupled, since different states and behaviors would be contained in a single class.

The Bridge pattern solves this issue by switching from inheritance to composition, and relocating all `CreditCard` classes to their own hierarchy. By doing that, we allow both contexts to evolve and change independently. 

<br>
<a href="/dev-on-track/assets/posts/2023-03-03/bridge-success.png" data-lightbox="bridge" data-title="Implementation with bridge pattern">
  <img src="/dev-on-track/assets/posts/2023-03-03/bridge-success.png" title="Implementation with bridge pattern">
</a>
<br>

With that design in mind, let's then create the `CreditCard` interface:

<br>
<a href="/dev-on-track/assets/posts/2023-03-03/CreditCard.png" data-lightbox="bridge" data-title="CreditCard.java class">
  <img src="/dev-on-track/assets/posts/2023-03-03/CreditCard.png" title="CreditCard.java class">
</a>
<br>

Now, we'll create our types: Basic, Silver and Gold, all implementing the methods specified by the interface `CreditCard`.

<br>
<a href="/dev-on-track/assets/posts/2023-03-03/BasicCreditCard.png" data-lightbox="bridge" data-title="BasicCreditCard.java class">
  <img src="/dev-on-track/assets/posts/2023-03-03/BasicCreditCard.png" title="BasicCreditCard.java class">
</a>
<br>
<a href="/dev-on-track/assets/posts/2023-03-03/SilverCreditCard.png" data-lightbox="bridge" data-title="SilverCreditCard.java class">
  <img src="/dev-on-track/assets/posts/2023-03-03/SilverCreditCard.png" title="SilverCreditCard.java class">
</a>
<br>
<a href="/dev-on-track/assets/posts/2023-03-03/GoldCreditCard.png" data-lightbox="bridge" data-title="GoldCreditCard.java class">
  <img src="/dev-on-track/assets/posts/2023-03-03/GoldCreditCard.png" title="GoldCreditCard.java class">
</a>
<br>


Let's create the abstraction `BankAccount`, the base class for our account types. This class will have an object of `CreditCard`, as well as a method to get the card information.

<br>
<a href="/dev-on-track/assets/posts/2023-03-03/BankAccount.png" data-lightbox="bridge" data-title="BankAccount.java class">
  <img src="/dev-on-track/assets/posts/2023-03-03/BankAccount.png" title="BankAccount.java class">
</a>
<br>


With all created, we can write our account types `CheckingAccount` and `SavingsAccount`:

<br>
<a href="/dev-on-track/assets/posts/2023-03-03/CheckingAccount.png" data-lightbox="bridge" data-title="CheckingAccount.java class">
  <img src="/dev-on-track/assets/posts/2023-03-03/CheckingAccount.png" title="CheckingAccount.java class">
</a>
<br>
<a href="/dev-on-track/assets/posts/2023-03-03/SavingsAccount.png" data-lightbox="bridge" data-title="SavingsAccount.java class">
  <img src="/dev-on-track/assets/posts/2023-03-03/SavingsAccount.png" title="SavingsAccount.java class">
</a>
<br>


To test our design, we'll create a main class initializing each account with their respective credit card types. 

<br>
<a href="/dev-on-track/assets/posts/2023-03-03/BridgeApplication.png" data-lightbox="bridge" data-title="BridgeApplication.java class">
  <img src="/dev-on-track/assets/posts/2023-03-03/BridgeApplication.png" title="BridgeApplication.java class">
</a>
<br>

... which outputs the following content:

<br>
<a href="/dev-on-track/assets/posts/2023-03-03/Output.png" data-lightbox="bridge" data-title="Output.java class">
  <img src="/dev-on-track/assets/posts/2023-03-03/Output.png" title="Output.java class">
</a>
<br>

In the end, Bridge pattern is a great tool to be used when we want to divide and refactor classes with a lot of variants, and also when we need to extend a class in independent dimensions. A great benefit in using this pattern is that it follows the **open/closed principle** (since we can add abstractions and implementations without impacting existing ones) and the **single responsibility principle.**

You can check the full implementation of this example [here @ my Github: GracieleDamasceno/design-patterns](https://github.com/GracieleDamasceno/design-patterns)!

#### References:

[Bridge Pattern - Refactoring Guru](https://refactoring.guru/design-patterns/bridge)

[Design Patterns](https://www.google.com.br/books/edition/Valuepack_Design_Patterns_Elements_of_Re/VDRePgAACAAJ) (2005), By [Erich Gamma](https://www.google.com.br/search?hl=pt-BR&q=inauthor:%22Erich+Gamma%22&tbm=bks), [Richard Helm](https://www.google.com.br/search?hl=pt-BR&q=inauthor:%22Richard+Helm%22&tbm=bks), [Ralph Johnson](https://www.google.com.br/search?hl=pt-BR&q=inauthor:%22Ralph+Johnson%22&tbm=bks), [John Vlissides](https://www.google.com.br/search?hl=pt-BR&q=inauthor:%22John+Vlissides%22&tbm=bks), [Craig Larman](https://www.google.com.br/search?hl=pt-BR&q=inauthor:%22Craig+Larman%22&tbm=bks)

 
