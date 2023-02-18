---
layout: post
title:  "Padrões de Projeto - Prototype"
date:   2022-11-04T08:00:00-00:00
author: Graciele Damasceno
categories: technology
tags:    tutorial padroes-projeto java prototype
lang: pt_BR
---

Continuando nossa série de padrões de projeto, o segundo que iremos revisitar é o Prototype!

Este padrão visa ter um “planta” para gerar instâncias. Isso é muito útil em situações onde existem vários objetos com propriedades diferentes, onde seja custoso criar novos recursos e quando queremos especificar a instanciação da classe em tempo de execução.

Basicamente, para implementar um Prototype, temos que adicionar à classe original desejada um método que clona uma instância baseada na original. O próximo passo é criar uma espécie de “registro” que mantenha um cache dos objetos prototipados; seguido por uma classe de fábrica que clona o original e retorna o resultado.

O processo de clonagem é um dos aspecto mais importantes do Prototype, então, para entender melhor as nuances escondidas na clonagem de um objeto, vamos falar sobre Deep Cloning e Shallow Cloning.

### Deep Cloning versus Shallow Cloning

**Deep Cloning** significa que, ao clonar, cada cópia terá sua própria referência. Se alterarmos um atributo da cópia, o original permanecerá intacto.

O mesmo não acontece com o **Shallow Cloning:** tanto a cópia quanto o original compartilham a mesma referência. Isso significa que as alterações feitas nos atributos de cópia também serão refletidas nos atributos do objeto original.

<a href="/dev-on-track/assets/posts/2022-11-04/diagram-shallow-deep-cloning.png" data-lightbox="prototype" data-title="Deep Cloning vs Shallow Cloning">
  <img src="/dev-on-track/assets/posts/2022-11-04/diagram-shallow-deep-cloning.png" title="Deep Cloning vs Shallow Cloning">
</a>

### Implementando o Padrão Prototype em Java

Existem algumas “*pegadinhas*” ao implementar o Prototype em Java: A escolha óbvia, a interface `Cloneable` do Java, é comumente conhecida por ser defeituosa. Simplesmente não existe um mecanismo construído em Java para criar "deep clones" nativamente, o que significa que implementar `Cloneable` e chamar `super.clone()` sempre criará uma cópia superficial. Pior ainda, a interface `Cloneable` é uma classe *marker* que apenas marca a classe que a implementa como adequada para clonagem, e não possui um método `clone()` próprio (este método existe na classe Object).

Esses fatores argumentam por ser uma escolha melhor usar cópia via construtores para criar clones ao invés do `Cloneable`  do Java.

Com isso em mente, vamos tentar usar o Prototype para resolver um problema. O conceito é o seguinte: Queremos acompanhar algumas Criptomoedas que foram compradas em 1º de janeiro de 2022. Os valores nesse período preenchem a instância original quando o aplicativo é iniciado. O usuário envia sua escolha de criptomoeda e o aplicativo chama um endpoint para obter informações em tempo real sobre essa criptomoeda específica, calcula a diferença entre o antes e o depois e envia de volta em um relatório.

Esse é o diagrama de arquitetura do aplicativo:

<a href="/dev-on-track/assets/posts/2022-11-04/diagram-prototype.png" data-lightbox="prototype" data-title="Prototype Architecture">
  <img src="/dev-on-track/assets/posts/2022-11-04/diagram-prototype.png" title="Prototype Architecture">
</a>

A classe de controller da aplicação é a seguinte:

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

Então, o controller invoca a service:

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

Essa é a classe que realiza o mapeamento do objeto recebido para o clone:

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

A classe que carrega a instância de Cryptocurrency:

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

Finalmente, a classe de Cryptocurrency que realiza a clonagem através do construtor com um parâmetro:

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

Você pode checar a implementação completa do projeto [clicando aqui.](https://github.com/GracieleDamasceno/design-patterns/tree/main/prototype)

##### Referências:

Discussões interessantes sobre o Cloneable do Java:

[Java: Clone and Cloneable > Programming.Guide](https://programming.guide/java/clone-and-cloneable.html)

[Java Cloning Problems > Vojtech Ruzicka's Programming Blog](https://www.vojtechruzicka.com/java-cloning-problems/)

[Copy constructors > Java Practices](http://www.javapractices.com/topic/TopicAction.do?Id=12)
