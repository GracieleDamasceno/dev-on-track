---
layout: post
title:  "Padrões de Projeto - Bridge"
date:   2023-03-03T08:00:00-00:00
author: Graciele Damasceno
categories: technology
tags:    tutorial design-pattern java bridge
lang: pt_BR
---

***Bridge*** é um padrão de projeto estrutural que visa desacoplar uma abstração de sua implementação, de forma que permita que ambos evoluam de forma independente.

O uso desse padrão é recomendado ao lidar com classes que possuem muitas implementações diferentes e usam herança para implementar a abstração. Nesses casos, a herança conecta uma implementação à abstração permanentemente, de forma que é difícil modificar, aumentar e reutilizar as abstrações de forma independente.

Para dar um exemplo ilustrado, vamos fingir que temos um cenário onde queremos criar diferentes tipos de contas bancárias: `CheckingAccount` e `SavingsAccount`. Queremos que nosso sistema seja flexível para a criação de novos tipos de contas, então criamos uma classe abstrata chamada `BankAccount` e estendemos esta classe com as classes `CheckingAccount` e `SavingsAccount`.

Ótimo! Mais tarde, queremos dar aos nossos tipos de conta seu próprio tipo de cartão de crédito personalizado. Criamos as classes `BasicCreditCard`, `SilverCreditCard` e `GoldCreditCard`. Ao vincular esses cartões de crédito a cada tipo de conta bancária, o problema fica claro:

<br>
<a href="/dev-on-track/assets/posts/2023-03-03/bridge.png" data-lightbox="bridge" data-title="Implementation without bridge pattern">
  <img src="/dev-on-track/assets/posts/2023-03-03/bridge.png" title="Implementation without bridge pattern">
</a>
<br>

A abordagem ingênua seria criar uma combinação para cada tipo de conta, uma solução que é cara e cresce exponencialmente (imagine o tanto de trabalho se adicionarmos um novo tipo de conta!). O código também é altamente acoplado, pois estados e comportamentos diferentes estariam contidos em uma única classe.

O padrão Bridge resolve esse problema mudando de herança para composição e realocando todas as classes `CreditCard` para sua própria hierarquia. Ao fazer isso, permitimos que ambos os contextos evoluam e mudem independentemente.

<br>
<a href="/dev-on-track/assets/posts/2023-03-03/bridge-success.png" data-lightbox="bridge" data-title="Implementation with bridge pattern">
  <img src="/dev-on-track/assets/posts/2023-03-03/bridge-success.png" title="Implementation with bridge pattern">
</a>
<br>

Com esse design em mente, vamos então criar nossa interface `CreditCard`:

<br>
<a href="/dev-on-track/assets/posts/2023-03-03/CreditCard.png" data-lightbox="bridge" data-title="CreditCard.java class">
  <img src="/dev-on-track/assets/posts/2023-03-03/CreditCard.png" title="CreditCard.java class">
</a>
<br>

Agora, vamos criar nossos tipos: Basic, Silver e Gold, todos implementando os métodos especificados pela interface `CreditCard`.

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

Vamos criar a abstração `BankAccount`, a classe base para nossos tipos de conta. Esta classe terá um objeto `CreditCard`, bem como um método para obter as informações do cartão.

<br>
<a href="/dev-on-track/assets/posts/2023-03-03/BankAccount.png" data-lightbox="bridge" data-title="BankAccount.java class">
  <img src="/dev-on-track/assets/posts/2023-03-03/BankAccount.png" title="BankAccount.java class">
</a>
<br>


Com tudo criado, podemos escrever nossos tipos de conta `CheckingAccount` e `SavingsAccount`:

<br>
<a href="/dev-on-track/assets/posts/2023-03-03/CheckingAccount.png" data-lightbox="bridge" data-title="CheckingAccount.java class">
  <img src="/dev-on-track/assets/posts/2023-03-03/CheckingAccount.png" title="CheckingAccount.java class">
</a>
<br>
<a href="/dev-on-track/assets/posts/2023-03-03/SavingsAccount.png" data-lightbox="bridge" data-title="SavingsAccount.java class">
  <img src="/dev-on-track/assets/posts/2023-03-03/SavingsAccount.png" title="SavingsAccount.java class">
</a>
<br>


Para testar nosso projeto, criaremos uma classe principal inicializando cada conta com seus respectivos tipos de cartão de crédito.

<br>
<a href="/dev-on-track/assets/posts/2023-03-03/BridgeApplication.png" data-lightbox="bridge" data-title="BridgeApplication.java class">
  <img src="/dev-on-track/assets/posts/2023-03-03/BridgeApplication.png" title="BridgeApplication.java class">
</a>
<br>

...que gera o seguinte conteúdo:

<br>
<a href="/dev-on-track/assets/posts/2023-03-03/Output.png" data-lightbox="bridge" data-title="Output.java class">
  <img src="/dev-on-track/assets/posts/2023-03-03/Output.png" title="Output.java class">
</a>
<br>

No fim das contas, o padrão Bridge é uma ótima ferramenta para ser utilizada quando queremos dividir e refatorar classes com muitas variantes, e também quando precisamos estender uma classe em dimensões independentes. Um grande benefício em usar esse padrão é que ele segue o **princípio aberto/fechado** (já que podemos adicionar abstrações e implementações sem afetar as existentes) e o **princípio de responsabilidade única**.


Você pode verificar a implementação completa deste exemplo [aqui no meu Github: GracieleDamasceno/design-patterns](https://github.com/GracieleDamasceno/design-patterns)!

#### Referências:

[Bridge Pattern - Refactoring Guru](https://refactoring.guru/design-patterns/bridge)

[Design Patterns](https://www.google.com.br/books/edition/Valuepack_Design_Patterns_Elements_of_Re/VDRePgAACAAJ) (2005), By [Erich Gamma](https://www.google.com.br/search?hl=pt-BR&q=inauthor:%22Erich+Gamma%22&tbm=bks), [Richard Helm](https://www.google.com.br/search?hl=pt-BR&q=inauthor:%22Richard+Helm%22&tbm=bks), [Ralph Johnson](https://www.google.com.br/search?hl=pt-BR&q=inauthor:%22Ralph+Johnson%22&tbm=bks), [John Vlissides](https://www.google.com.br/search?hl=pt-BR&q=inauthor:%22John+Vlissides%22&tbm=bks), [Craig Larman](https://www.google.com.br/search?hl=pt-BR&q=inauthor:%22Craig+Larman%22&tbm=bks)

 
