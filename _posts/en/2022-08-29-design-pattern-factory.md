---
layout: post
title:  "Design Patterns - Factory"
date:   2022-08-29T08:00:00-00:00
author: Graciele Damasceno
categories: technology
tags:    tutorial design-pattern java factory
lang: en
---

Today we are going to take a close look at Factory Design Pattern.

Imagine that you have a requirement to build an application feature that sends a message specified by a user through different delivery methods. For example, they can send their message using SMS, push notification to a mobile phone, email... 

It would be such a hassle to build different classes to receive the user message to deliver it! I mean, imagine if the user has to memorize different endpoints/arguments to send their message. Looking at the code, we would have to create a method that would call each of the classes that would send a message using a specific way. But what if I want to develop another message delivery type? I would have to touch the logic that controls each message to their respective type, and if I'm not careful enough, I could delete or modify important pieces of that logic. My code would have lots of conditions and could be highly coupled.

The Factory Patterns suggests a pretty neat way to solve that problem. Let's see how!

### Show me the code!

First, let's take a look at our architecture diagram.

<a href="/dev-on-track/assets/posts/2022-08-29/factory-diagram.png" data-lightbox="factory" data-title="Notification Factory Diagram">
  <img src="/dev-on-track/assets/posts/2022-08-29/factory-diagram.png" title="Notification Factory Diagram">
</a>

The main idea is to create a notification interface that will have a delivery method defined, so that each of the classes will implement their own specifics of the delivery. There's five delivery method defined, and those methods will be called based on a parameter sent by the user through a service. We will get more details about it in a bit. 

The project uses Java with Spring and a `REST` call to send a message. Let's start by creating a `REST` controller where the user can post their message and the selected delivery type.

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

We have created a `MessageRequestDTO` that encapsulates a String attribute message, and an enum class called  `NotificationTypeEnum` with all of our delivery options.

The interface that specifies our delivery method is as follows:

```java
public interface Notification {

  DeliveryResponseDTO deliverMessage(MessageRequestDTO message);
}
```

And this is an example of our delivery class implementing `Notification` interface:

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

Great, now, let's create the class `NotificationService` that will handle the dispatch of our message request from the controller.

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

Hang on... *There's not much happening here.* We created a method to redirect the message by receiving the message itself and the method of delivery, which is an enum. Apparently, there's a method inside our enum to get the instance of the `NotificationType`... Let's take a closer look at how the enum class was created.

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

This is the most interesting class in the project, in my opinion. We are applying a cool concept here - creating a `getInstance` method which redirects returns the correct class of delivery based on the enum sent by the user. That way, we avoid if statements and our code becomes more clean and efficient.

It's possible to notice that we didn't have to call any of our delivery classes - neither any of the service nor controller classes had to. If we wanted to add a new delivery type, we wouldn't have to touch any of the other delivery classes, they wouldn't even "notice" the addition or removal of such delivery method. This happens because our logic of constructing a class is held by only one "factory" class, in this case, `NotificationTypeEnum`.

So, to put it simply, Factory works by creating an interface that creates families of objects that have the similar function but different behavior, without the need of specifying the concrete class of those objects. The Factory-part lies in creating mechanisms that deal with selecting the correct implementation for each case needed. 

That's it! You can check the full project implementation [clicking here.](https://github.com/GracieleDamasceno/design-patterns/tree/main/factory)
