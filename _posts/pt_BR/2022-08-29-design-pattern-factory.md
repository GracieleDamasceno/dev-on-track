---
layout: post
title:  "Padrões de Projeto - Factory"
date:   2022-08-29T08:00:00-00:00
author: Graciele Damasceno
categories: technology
tags:    tutorial padroes-projeto java factory
lang: pt_BR
---

Hoje vamos dar uma olhada no Padrão de Projeto "Factory".

Imagine que você tenha que construir uma applicação que envie uma mensagem especificada pelo usuário usando diferentes métodos de entrega. Por exemplo, o usuário poderia enviar sua mensagem usando SMS, uma notificação push para um celular, e-mail...

Seria um incômodo construir muitas classes diferentes para receber a mensagem do usuário e entregá-la! Quero dizer, imagine se o usuário tiver que utilizar diferentes endpoints/argumentos para enviar cada tipo de mensagem. Pensando em código, teríamos que criar um método que chamasse cada uma das classes que enviariam uma mensagem de cada maneira específica. Mas e se eu quiser desenvolver outro tipo de método de entrega? Eu teria que alterar a lógica que envia cada mensagem para seu respectivo tipo e, se não tomar cuidado, poderia excluir ou modificar partes importantes dessa lógica. Meu código teria muitas condições e poderia ser altamente acoplado.

Os Padrões de Projeto sugerem uma maneira bem legal de resolver esse problema. Vamos ver como!

### Mãos a obra!

Primeiro, vamos dar uma olhadinha no nosso diagrama de arquitetura.

<a href="/dev-on-track/assets/posts/2022-08-29/factory-diagram.png" data-lightbox="factory" data-title="Notification Factory Diagram">
  <img src="/dev-on-track/assets/posts/2022-08-29/factory-diagram.png" title="Notification Factory Diagram">
</a>

A ideia principal é criar uma interface de notificação que terá um método de entrega definido, para que cada uma das classes implemente suas próprias especificidades da entrega. Há cinco métodos de entrega definidos, e esses métodos serão chamados com base em um parâmetro enviado pelo usuário por meio de uma classe de service. Daqui a pouco vamos ver mais detalhes sobre isso.

Esse projeto usa Java com Spring, e uma chamada `REST` para receber a mensagem desejada. Vamos começar criando um controller `REST` onde o usuário pode postar sua mensagem e o tipo de entrega selecionado.

```java
@Slf4j
@RestController
@AllArgsConstructor
public class NotificationController {

  private NotificationService notificationService;

  @PostMapping(value = "/message")
  public DeliveryResponseDTO sendMessage(@RequestBody MessageRequestDTO message, @RequestParam NotificationTypeEnum delivery) {
    log.info("Message received. Method of delivery: " + delivery + ". Payload: " + message);

    return notificationService.redirectMessage(message, delivery);
  }
}
```

Criamos uma classe `MessageRequestDTO` que encapsula uma mensagem de tipo String e uma classe enum chamada `NotificationTypeEnum` com todas as nossas opções de entrega.

A interface que especifica nosso método de entrega padrão para todas as opções de entrega é a seguinte:

```java
public interface Notification {

  DeliveryResponseDTO deliverMessage(MessageRequestDTO message);
}
```

E este é um exemplo de nossa classe de entrega implementando a interface `Notification`:

```java
@Slf4j
public class CarrierPigeonNotification implements Notification {

  @Override
  public DeliveryResponseDTO deliverMessage(MessageRequestDTO message) {
    log.info("Carrier Pigeon is the selected choice of delivery! The message to send is: " + message);

    return DeliveryResponseDTO.builder()
        .delivered(false)
        .deliveryStatus("Delivery method not yet supported!")
        .build();
  }
}
```

Maravilha, agora vamos criar a classe `NotificationService` que tratará o envio da nossa requisição de mensagem vinda do controller.

```java
@NoArgsConstructor
@Service
public class NotificationService {

  public DeliveryResponseDTO redirectMessage(MessageRequestDTO message, NotificationTypeEnum methodOfDelivery){
    Notification notification = methodOfDelivery.getInstance();
    return notification.deliverMessage(message);
  }
}
```

Pera aí... *Não tem muita coisa acontecendo aqui.* Criamos um método para redirecionar a mensagem recebendo o próprio DTO de mensagem e o método de entrega, que é um enum. Aparentemente, existe um método dentro do nosso enum para obter a instância do `NotificationType`... Vamos dar uma olhada em como a classe enum foi criada.

```java
public enum NotificationTypeEnum {
  CARRIER_PIGEON {
    public CarrierPigeonNotification getInstance(){
      return new CarrierPigeonNotification();
    }
  },
  EMAIL {
    public EmailNotification getInstance(){
      return new EmailNotification();
    }
  },
  MAIL_LETTER {
    public MailLetterNotification getInstance(){
      return new MailLetterNotification();
    }
  },
  MOBILE_PUSH {
    public MobilePushNotification getInstance(){
      return new MobilePushNotification();
    }
  },
  SMS{
    public SMSNotification getInstance(){
      return new SMSNotification();
    }
  };

  public abstract Notification getInstance();
}
```

Essa é a classe mais interessante de todo o projeto, na minha opinião. Estamos aplicando um conceito bem legal aqui - criando um método `getInstance` que retorna a classe correta de entrega com base no enum enviado pelo usuário. Dessa forma, evitamos blocos *if* e nosso código se torna mais limpo e eficiente.

É possível notar que não tivemos que chamar nenhuma de nossas classes de entrega - nem as classes de service nem de controller precisaram chamar. Se quiséssemos adicionar um novo tipo de entrega, não teríamos que mexer em nenhuma das outras classes de tipo de entrega, elas nem "perceberiam" a adição ou remoção de tal método de entrega. Isso acontece porque nossa lógica de construção das instancias das classes é mantida por apenas uma classe usada como "fábrica", neste caso, a `NotificationTypeEnum`.

Então, para simplificar, o Factory funciona criando uma interface que é responsável por criar *famílias* de objetos que possuem a mesma "função", mas com comportamento diferente, sem a necessidade de especificar qual é a classe concreta desses objetos. A parte de ser uma fábrica está na criação de mecanismos que tratam de selecionar a implementação correta para cada caso necessário.

É isso! Você pode conferir a implementação completa desse exemplo de factory [clicando aqui.](https://github.com/GracieleDamasceno/design-patterns/tree/main/factory)
