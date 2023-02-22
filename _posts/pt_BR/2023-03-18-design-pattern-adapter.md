---
layout: post
title:  "Padrões de Projeto - Adapter"
date:   2023-02-18T08:00:00-00:00
author: Graciele Damasceno
categories: technology
tags:    tutorial design-pattern java adapter
lang: pt_BR
---

***Adapter*** é um padrão de projeto com a finalidade de - como o próprio nome sugere - adaptar um contexto à outro, permitindo que duas interfaces incompatíveis sejam conectadas. O principal benefício de aplicar esse padrão é permitir trocar de uma interface existente para outra sem alterar o comportamento original da interface existente. o Adapter também é usado quando há a necessidade de criar uma classe reutilizável que interaja com interfaces incompatíveis. Vamos ver esse padrão em ação!

### Adaptando a saída existente para uma nova

Em nosso cenário, temos uma POJO de pessoa que contém alguns atributos.

<br>

<a href="/dev-on-track/assets/posts/2023-02-18/Person.png" data-lightbox="adapter" data-title="Person.java">
  <img src="/dev-on-track/assets/posts/2023-02-18/Person.png" title="Person.java">
</a>

<br>

Existe também uma interface que dita qual será o contrato de negócio do objeto Pessoa. Em nosso caso, o contrato existente estabelece que a resposta da classe `Person` deve ser uma string formatada em XML.

<br>

<a href="/dev-on-track/assets/posts/2023-02-18/PersonOutput.png" data-lightbox="adapter" data-title="PersonOutput.java">
  <img src="/dev-on-track/assets/posts/2023-02-18/PersonOutput.png" title="PersonOutput.java">
</a>

<br>E a classe `LegacyPersonService` implementa esse contrato, construindo um objeto da classe `Person` e retornando-o em string, formatada em XML.

<br>

<a href="/dev-on-track/assets/posts/2023-02-18/LegacyPersonService.png" data-lightbox="adapter" data-title="LegacyPersonService.java">
  <img src="/dev-on-track/assets/posts/2023-02-18/LegacyPersonService.png" title="LegacyPersonService.java">
</a>

<br>

Essa arquitetura funciona bem por um bom tempo, até que seja necessário redesenhar nosso sistema. Como nesse cenário temos muitas dependências externas e cruciais, a resposta em XML tem que coexistir com a nova solução, que tem como objetivo ter a  resposta em string no formato JSON.

É exatamente aí que o padrão do adaptador é muito útil!

Vamos adaptar nossa solução anterior para gerar um JSON, seguindo os novos requisitos. Primeiro, vamos implementar uma interface que dite esse novo contrato.

<br>

<a href="/dev-on-track/assets/posts/2023-02-18/PersonOutputAdapter.png" data-lightbox="adapter" data-title="PersonOutputAdapter.java">
  <img src="/dev-on-track/assets/posts/2023-02-18/PersonOutputAdapter.png" title="PersonOutputAdapter.java">
</a>

<br>Agora, criamos o adapter: implementando a interface criada anteriormente, instanciamos a classe `LegacyPersonService` que contém a lógica do contrato antigo e implementamos a nova lógica para transformar a resposta em uma string em JSON.
<br>

<a href="/dev-on-track/assets/posts/2023-02-18/ModernizedPersonAdapter.png" data-lightbox="adapter" data-title="ModernizedPersonAdapter.java">
  <img src="/dev-on-track/assets/posts/2023-02-18/ModernizedPersonAdapter.png" title="ModernizedPersonAdapter.java">
</a>

<br>

Com a lógica criada, podemos criar tranquilamente uma nova versão do nosso endpoint que atenda à nova especificação, sem alterar a que já existia.

<br>

<a href="/dev-on-track/assets/posts/2023-02-18/PersonController.png" data-lightbox="adapter" data-title="PersonController.java">
  <img src="/dev-on-track/assets/posts/2023-02-18/PersonController.png" title="PersonController.java">
</a>

<br>

Então, para resumir:

- O padrão de Adapter segue o **princípio aberto-fechado**. Podemos adicionar novos adaptadores sem alterar o código existente.

- O padrão de Adapter também segue o princípio da **responsabilidade única**, pois separamos a interface/contrato da lógica de negócios existente.

- No nosso caso, foi utilizada composição na classe `ModernizedPersonAdapter` para encapsular o alvo do adaptador (`LegacyPersonService`). Esta forma de lidar com a classe *adaptada* é chamada de **Object Adapter**.
  
  - A outra forma de implementar um adaptador seria usando o estilo **Class Adapter**, onde ao invés de criar um objeto `LegacyPersonService` em `ModernizedPersonAdapter`, estenderíamos `LegacyPersonService` e implementaríamos `PersonOutputAdapter` simultaneamente. Ambas são abordagens válidas, no entanto, é melhor favorecer a composição em detrimento da herança, pois permite que o sistema seja mais fácil de testar, menos complexo e desacoplado.

- A principal desvantagem que esse padrão oferece é o aumento da complexidade ao adicionar novos componentes à aplicação. Dependendo da situação e das restrições externas, é melhor apenas refatorar o código existente para corresponder ao novo contrato.

<br>

Você pode dar uma olhada no código [aqui no meu Github: GracieleDamasceno/design-patterns](https://github.com/GracieleDamasceno/design-patterns)! Até a próxima!

<br>

#### Referências:

[Composition over inheritance - Wikipedia](https://en.wikipedia.org/wiki/Composition_over_inheritance)

[Adapter Pattern - Refactoring Guru](https://refactoring.guru/design-patterns/adapter)
