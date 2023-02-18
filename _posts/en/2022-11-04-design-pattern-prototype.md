---
layout: post
title:  "Design Patterns - Prototype"
date:   2022-11-04T08:00:00-00:00
author: Graciele Damasceno
categories: technology
tags:    tutorial design-pattern java prototype
lang: en
---

Following our series of design patterns, the second one that we will revisit is Prototype!

This pattern aims to have a “blueprint” to generate instances. This is very handy in situations where exists multiple objects with different properties, it is expensive to create new resources and when we want to specify the class instantiation during run-time.

Basically, to implement a Prototype, we have to add to the desired original class a method that clones an instance based on the original. The next step is to create a “registry” that maintains a cache of the prototypical objects; followed by a factory class that clones the original and returns the result.

The clone process is a main aspect of prototype, so, to better understand the nuances hidden in cloning an object, let's talk about deep cloning and shallow cloning.

### Deep Cloning versus Shallow Cloning

**Deep cloning** means when cloning, each copy will have their own reference. If we change an attribute of the copy, the original will remain intact.

The same doesn't happen when **shallow cloning**: both copy and original shares the same reference. This means that changes made to copy attributes will also be reflected in the original attributes object.

<a href="/dev-on-track/assets/posts/2022-11-04/diagram-shallow-deep-cloning.png" data-lightbox="prototype" data-title="Deep Cloning vs Shallow Cloning">
  <img src="/dev-on-track/assets/posts/2022-11-04/diagram-shallow-deep-cloning.png" title="Deep Cloning vs Shallow Cloning">
</a>

### Implementing Prototype Pattern in Java

There's a few “*gotchas*” while implementing Prototype in Java: The obvious choice, Java's `Cloneable` interface, is commonly known to be flawed. There's simply no built mechanism in Java to create deep copies, which means that implementing `Cloneable` and calling `super.clone()` will always create a shallow copy either way. Even worse, the interface `Cloneable` is a marker class that just marks the class that implements it as suitable for cloning, and doesn't have itself a `clone()` method (this method being presented in the Object class instead).

Those factors argue to be a better choice to use copy constructors to create a clone instead of Java's Cloneable.

With that in mind, let's attempt to use Prototype to solve a problem. The concept is as follows: We want to keep track of some Cryptocurrencies that were bought on January 1st, 2022. The snapshot of the values at this period of time populates the original instance when the application starts. The user sends their choice of Cryptocurrency and the application calls an endpoint to get real-time information about that specific Crypto, calculates the difference between then and now in a report.

This is the application architecture diagram:

<a href="/dev-on-track/assets/posts/2022-11-04/diagram-prototype.png" data-lightbox="prototype" data-title="Prototype Architecture">
  <img src="/dev-on-track/assets/posts/2022-11-04/diagram-prototype.png" title="Prototype Architecture">
</a>

The application controller is as follows:

```java
@RestController
@AllArgsConstructor
public class CryprocurrencyController {
  private final CryptocurrencyService cryptocurrencyService;

  @GetMapping(value = "/crypto/{cryptocurrencyType}")
  public CryptocurrencyReport cryptocurrencyController(@PathVariable CryptocurrencyTypeEnum cryptocurrencyType) {
    return cryptocurrencyService.getCryptocurrencyReport(cryptocurrencyType);
  }
}
```

The controller will then call the service:

```java
@Service
@AllArgsConstructor
public class CryptocurrencyService {

  private final CryptocurrencyClient cryptocurrencyClient;
  private final CryptocurrencyLoader cryptocurrencyLoader;

  public CryptocurrencyReport getCryptocurrencyReport(CryptocurrencyTypeEnum cryptocurrencyTypeEnum){

    //Get original Cryptocurrency instance
    Cryptocurrency cryptocurrencyAtPurchase = cryptocurrencyLoader.getCryptocurrencyLoaded(cryptocurrencyTypeEnum);

    //Get Cryptocurrency cloned with updated values
    Cryptocurrency cryptocurrencyNow = getUpdatedCryptocurrencyValues(cryptocurrencyTypeEnum);

    //Returns the report calculating variation between purshased value and current value
    return CryptocurrencyReport.builder()
        .cryptocurrencyCurrentValues(cryptocurrencyNow)
        .cryptocurrencyPurchaseValues(cryptocurrencyAtPurchase)
        .rankVariation(calculateVariation(cryptocurrencyAtPurchase.getRank(), cryptocurrencyNow.getRank()))
        .priceUSDVariation(calculateVariation(cryptocurrencyAtPurchase.getPriceUSD(), cryptocurrencyNow.getPriceUSD()))
        .volume24Variation(calculateVariation(cryptocurrencyAtPurchase.getVolume24(), cryptocurrencyNow.getVolume24()))
        .marketCapUSDVariation(calculateVariation(cryptocurrencyAtPurchase.getMarketCapUSD(), cryptocurrencyNow.getMarketCapUSD()))
        .build();
  }

  private Cryptocurrency getUpdatedCryptocurrencyValues(CryptocurrencyTypeEnum cryptocurrencyTypeEnum){
    //Requests current Crypto's data 
    final CryptocurrencyDTO cryptocurrencyDTO =
        cryptocurrencyClient.getCryptocurrency(cryptocurrencyTypeEnum.getId()).iterator().next();
    //Loads original cripto values
    Cryptocurrency cryptocurrencyLoaded = cryptocurrencyLoader.getCryptocurrencyLoaded(cryptocurrencyTypeEnum);
    //Maps the current values to the original Criptocurrency object
    return updateLoadedPrototypeWithCurrentValues(cryptocurrencyDTO, cryptocurrencyLoaded);
  }

  private BigDecimal calculateVariation(BigDecimal valueBefore, BigDecimal valueNow){
    return valueNow.subtract(valueBefore);
  }

  private Integer calculateVariation(Integer valueBefore, Integer valueNow){
    return valueNow - valueBefore;
  }
}
```

This is the class that mappers the object received by the request to the instance clone:

```java
@NoArgsConstructor
public class CryptocurrencyDTOMapper {

  public static Cryptocurrency updateLoadedPrototypeWithCurrentValues(CryptocurrencyDTO cryptocurrencyDTO, Cryptocurrency cryptocurrencyLoaded){
    cryptocurrencyLoaded.setRank(cryptocurrencyDTO.getRank());
    cryptocurrencyLoaded.setPriceUSD(cryptocurrencyDTO.getPriceUSD());
    cryptocurrencyLoaded.setVolume24(cryptocurrencyDTO.getVolume24());
    cryptocurrencyLoaded.setMarketCapUSD(cryptocurrencyDTO.getMarketCapUSD());
    return cryptocurrencyLoaded;
  }
}
```

The class that loads the original instance of Cryptocurrency:

```java
@Component
public class CryptocurrencyLoader {

  HashMap<String, Cryptocurrency> cryptocurrencyTable = new HashMap<>();
  //Returns crypto object based on the enum requested by the user
  public Cryptocurrency getCryptocurrencyLoaded(CryptocurrencyTypeEnum crypto){
    Cryptocurrency loadedCryptocurrency = cryptocurrencyTable.get(crypto.getId());
    //Here the cloning happens!
    return new Cryptocurrency(loadedCryptocurrency);
  }

  //Loads the crypto information on application startup
  @PostConstruct
  private void loadCryptoInfo1stJanuary2022(){
    Cryptocurrency bitcoin = Cryptocurrency.builder()
        .id(CryptocurrencyTypeEnum.BTC.getId())
        .name("BITCOIN")
        .symbol(CryptocurrencyTypeEnum.BTC)
        .rank(1)
        .volume24(new BigDecimal("24582667004"))
        .priceUSD(new BigDecimal("47686.81"))
        .marketCapUSD(new BigDecimal("902104193385"))
        .build();

    Cryptocurrency ethereum = Cryptocurrency.builder()
        .id(CryptocurrencyTypeEnum.ETH.getId())
        .name("ETHEREUM")
        .symbol(CryptocurrencyTypeEnum.ETH)
        .rank(1)
        .volume24(new BigDecimal("9776191466"))
        .priceUSD(new BigDecimal("3769.70"))
        .marketCapUSD(new BigDecimal("448537615143"))
        .build();

    cryptocurrencyTable.put(CryptocurrencyTypeEnum.BTC.getId(), bitcoin);
    cryptocurrencyTable.put(CryptocurrencyTypeEnum.ETH.getId(), ethereum);
  }
}
```

Finally, the Cryptocurrency class that performs the cloning in the constructor:

```java
@Getter
@Setter
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class Cryptocurrency {

  private String id;

  private CryptocurrencyTypeEnum symbol;

  private String name;

  private Integer rank;

  @JsonProperty("price_usd")
  private BigDecimal priceUSD;

  @JsonProperty("volume_24")
  private BigDecimal volume24;

  @JsonProperty("market_cap_usd")
  private BigDecimal marketCapUSD;

  /*
  * Since the clone method from Cloneable class is broken, we use a copy constructor.
  * */
  public Cryptocurrency(Cryptocurrency cryptocurrency) {
    this(cryptocurrency.getId(),
        cryptocurrency.getSymbol(),
        cryptocurrency.getName(),
        cryptocurrency.getRank(),
        cryptocurrency.getPriceUSD(),
        cryptocurrency.getVolume24(),
        cryptocurrency.getMarketCapUSD());
  }
}
```

You can check the full project implementation [clicking here.](https://github.com/GracieleDamasceno/design-patterns/tree/main/prototype) 

##### References:

Interesting conversations about Java's Cloneable:

[Java: Clone and Cloneable > Programming.Guide](https://programming.guide/java/clone-and-cloneable.html)

[Java Cloning Problems >  Vojtech Ruzicka's Programming Blog](https://www.vojtechruzicka.com/java-cloning-problems/)

[Copy constructors > Java Practices](http://www.javapractices.com/topic/TopicAction.do?Id=12)
