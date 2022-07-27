---
layout: post
title:  "Criando uma skill de audio local na Alexa"
date:   2022-07-20T22:00:00-00:00
author: Graciele Damasceno
categories: technology
tags:    tech alexa tutorial
lang: pt_BR
---

# Skill de Audio da Alexa

Tutorial que mostra como criar uma Alexa Skill facilmente, chamando um arquivo de áudio externo para impressionar seus amigos e família.
O objetivo desse tutorial é ser o mais fácil e direto possível, permitindo a qualquer um - até aqueles que não sabem programar! - criar uma skill de áudio na Alexa. 
Para começar, vamos simplificar as coisas usando o Console da Alexa Development da Amazon.

## Primeiros passos

Para começar, você precisa de uma conta de desenvolvedor da Amazon. Você pode criar clicando [aqui][thislink] e fazendo login com sua conta Amazon já existente, se já tiver uma, ou criando uma nova conta. No entanto, é importante observar que para "testar" sua habilidade em seu dispositivo, você deve usar a mesma conta do seu dispositivo Alexa.

Após o login, você deve acessar o [Alexa Development Console][devconsole], onde criaremos a primeira skill.

## Criando uma skill

Para criar uma nova habilidade, basta clicar no botão `Create Skill`. Crie um nome para sua habilidade e selecione seu idioma padrão.
Nas primeiras configurações `Choose a model to add to your skill`, deixe marcado no padrão como está (Custom) e selecione  "Alexa-hosted (Node.js)" como opção na configuração `Choose a method to host your skill's backend resources`, se já não estiver selecionado.
Quando tudo estiver pronto, clique em  `Create Skill`. A próxima página pedirá que você escolher um novo template (`Choose a template to add to your skill`), e devemos selecionar a opção de começar do zero"Start from Scratch" e clicar em `Continue with template`. Voilà! Temos nossa habilidade criada e pronta para ser personalizada.

## Adicionando um nome de invocação e interface de áudio

Com a habilidade criada, a primeira etapa é criar um nome de invocação. Esta é a frase/duas palavras que o Alexa usará para invocar nosso código. Para configurar isso, precisamos clicar em `Invocation Name`  digitar a invocação desejada no campo abaixo de `Skill Invocation Name` e clicar em `Save Model`. Com isso configurado e pronto, há mais uma configuração que precisa ser feita: ativar nossa interface de áudio. Para fazer isso, clique na opção `Interfaces` que está localizada no menu à esquerda. Depois disso, marque o botão chamado `Audio Player` e salve a interface e o modelo de construção.
A mensagem `Quick build in progress` será exibida. Aguarde até que a compilação esteja pronta e vamos para a próxima etapa!

## Editando o código

O próximo passo é editar o código adicionando nosso arquivo de áudio! Precisamos especificar um URL que contém um áudio que se reproduz. Você pode auto-hospedar o áudio na AWS ou em qualquer host de sua escolha, ou pesquisar na web para encontrar um. No meu exemplo, usarei um áudio do [My Instants][instants].

Para editar o código, clique em `Code` no menu. Um editor de código no navegador aparecerá com vários arquivos. O que vamos editar é o `index.js`, e já deve estar aberto.

A próxima parte é fácil: substitua o seguinte código:

{% highlight javascript %}
const speakOutput = 'Welcome, you can say Hello or Help. Which would you like to try?';
    return handlerInput.responseBuilder
        .speak(speakOutput)
        .reprompt(speakOutput)
        .getResponse();
{% endhighlight %}

por esse aqui:

{% highlight javascript %}
const speakOutput = 'Its me, Mario!';
    return handlerInput.responseBuilder
        .speak(speakOutput)
        .addAudioPlayerPlayDirective('REPLACE_ALL', '*', 1, 0, null, null)
        .getResponse();
{% endhighlight %}

Explicando brevemente, o `speakOutput` corresponde à linha que o Alexa dirá para você quando nosso código for invocado. Você pode mudar o `It's me, Mario!` para o que você quiser, apenas certifique-se de escrever entre aspas simples.
A mágica acontece assim: na linha  `.addAudioPlayerPlayDirective('REPLACE_ALL', '*', 1, 0, null, null)`, o `'*'` é o espaço reservado para nosso link de áudio. Troquei para ficar assim:

{% highlight javascript %}
const speakOutput = 'Its me, Mario!';
    return handlerInput.responseBuilder
        .speak(speakOutput)
        .addAudioPlayerPlayDirective('REPLACE_ALL', 
            'https://www.myinstants.com/media/sounds/its-me-mario.mp3', 1, 0, null, null)
        .getResponse();
{% endhighlight %}

Depois de adicionar seu arquivo de áudio ao código, você precisa `Save` e `Deploy` seu código, espere até que a implantação seja feita e pronto! Pera, falta testar 🤔

## Ativando o modo de teste

Para finalmente chamar a habilidade em sua Alexa pessoal, há uma última etapa: ativar o modo de teste de habilidade. Para isso, basta clicar em  `Test` localizado no menu acima do seu código, e alterar a opção `Test is disabled for this skill.` para `Development`. Agora você pode chamar sua Alexa e dizer seu nome de invocação para testar sua habilidade!

## Notas finais

Esta é apenas uma opção para criar facilmente uma habilidade de uso pessoal, para ser usada localmente sempre no estágio de desenvolvimento. Não vamos aprofundar na publicação da habilidade para todo mundo neste tutorial.

[thislink]: https://developer.amazon.com/
[devconsole]: https://developer.amazon.com/alexa/console/ask
[instants]: https://www.myinstants.com