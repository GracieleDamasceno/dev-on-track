---
layout: post
title:  "Melhorando a comunicação assíncrona no ambiente de trabalho"
date:   2022-09-11T08:00:00-00:00
author: Graciele Damasceno
categories: Personal
tags:    communication tips
lang: pt_BR
---

Trabalhar remotamente pode resultar em alguns desafios inesperados. Quando precisamos de ajuda em um ambiente onde todos estão fisicamente compartilhando o mesmo espaço, podemos simplesmente nos chegar em um colega no intervalo do café e pedir ajuda, apontando freneticamente para a tela enquanto explicamos o que estávamos fazendo antes que não funciona mais agora.

No entanto, em um ambiente remoto, às vezes pedir ajuda ou tirar dúvidas pode ser um grande desafio. Primeiro, eu iria analisar minuciosamente o chat, procurando alguém que esteja disponível que *possa* com sorte saber a resposta para o meu problema, secretamente esperando que a pessoa não mude seu status para "ocupado" ou "não perturbe" nos próximos cinco segundos. Ótimo, a pessoa está disponível! Eu vou ligar... Não, é muito abrupto; melhor enviar uma mensagem.

> Eu: Bom dia Joe, tudo bem?

E... nada. Joe não me responde imediatamente. Talvez ele não tenha visto minha mensagem, isso pode acontecer de vez em quando. Vamos tentar chamar a atenção dele novamente:

> Eu: Você está ocupado?

Nada. Estou travada! Joe precisa me responder para que eu consiga seguir em frente. Ele é o único que pode. Cada segundo que ele leva para me responder é um tempo perdido que não posso recuperar.

> Joe: Oi Gracie. Eu estou bem. E você?

**AÍ SIM**! Demorou um pouco para me responder, só que ele só respondeu a minha primeira pergunta. Vamos tentar de novo:

> Eu: Estou bem, obrigado. Pode me ajudar com uma coisinha?

...

Joe sumiu de novo. Ele não me respondeu.

E ainda estou travada.

## Qual é o problema aqui?

Talvez Joe esteja super ocupado e não tenha atualizado seu status. Talvez ele esteja tentando resolver um problema próprio. Talvez ele esteja ajudando outra pessoa. Talvez ele esteja fazendo uma pausa para o café. Talvez sua conexão com a internet não esteja tão boa agora. Talvez ele se distraiu e esqueceu de responder. Talvez...

Nunca dá pra saber o que a outra pessoa está fazendo enquanto trabalhando remoto. Não dá pra dar aquela olhadinha de canto e ver a expressão super concentrada do colega, aquela cara de *nem-nem-tente-me-incomodar-que-o-bagulho-ta-feio*. Portanto, justamente por estarmos remoto, uma coisa que devemos ter em mente é que devemos aprender a trabalhar **de forma assíncrona**.

Nem sempre o outro estará disponível o tempo todo para você, e **está tudo bem** (o problema é quando eles nunca estão disponíveis para ninguém, mas isso é discussão para outro tópico...). Do mesmo jeito como nem você não está disponível para ajudar o tempo todo. Com isso em mente, podemos usar algumas técnicas para aproveitar ao máximo o tempo disponível de todos.

Voltando ao nosso cenário: Joe me responde três horas depois com:

> Joe: Sim, desculpe a demora. O que está acontecendo?

Aí eu nem sei nem mais por onde começar minha pergunta. Então eu mando:

> Eu: Posso te ligar para mostrar meu problema?

Aqui vêm algumas ramificações deste cenário:

1. Joe está disponível para conversar agora. Vou ligar para ele e explicar o que estava tentando fazer e o que não está funcionando. Depois de cinco minutos ouvindo, ele vai me perguntar se eu tentei fazer isso ou aquilo, eu vou dizer que não, ainda não tentei, e ele vai esperar na ligação enquanto eu tento aplicar sua sugestão. Funcionou! Excelente.

2. Joe está disponível para conversar agora. Vou ligar para ele e explicar o que estava tentando fazer, e enquanto explico, percebo que... Algo não parece certo. Eu me escuto explicando e percebo que agora posso chegar à raiz do meu problema. Percebo algum ponto de falha na minha lógica, ou que eu tinha entendido superficialmente o que estava fazendo. Enquanto falo, minha voz desaparece lentamente e exclamo com orgulho que entendi! Eu rapidamente resolvo o problema, agradeço a Joe por seu tempo e sigo em frente.

3. Joe não está disponível para conversar agora. Procuro outra pessoa para me ajudar.

Vamos examinar algumas técnicas para melhorar esses cenários.

## 1. Como escrever perguntas de forma eficaz para obter se comunicar de forma assíncrona e precisa

No primeiro cenário, liguei para Joe e expliquei desde o início o que eu estava tentando fazer. Dá pra notar que muito tempo foi gasto esperando Joe me responder antes de conseguir falar com ele. Existe uma maneira melhorar essas situações?

Eu poderia tentar explicar meu problema via texto para Joe, para que quando ele veja minha mensagem, ele possa passar algum tempo pensando e me dar a melhor resposta possível. Existem algumas técnicas que podem ser usadas para melhorar a qualidade de nossa comunicação escrita; vamos dar uma olhada no método Pirâmide Minto (você pode ler mais sobre esse método de escrita visitando o site [untools](https://untools.co/minto-pyramid).)

**A Pirâmide Minto** é uma técnica para escrever melhor e comunicar de forma mais eficiente e clara. Ela se concentra principalmente em compartilhar a essência do problema imediatamente, elaborando os detalhes posteriormente seguindo três etapas:

<a href="/dev-on-track/assets/posts/2022-09-11/minto-pyramid.png" data-lightbox="menti" data-title="Minto Pyramid">
  <img src="/dev-on-track/assets/posts/2022-09-11/minto-pyramid.png" title="Minto Pyramid">
</a>

1. Para começar, capture a atenção do espectador imediatamente, contando-lhe a essência do problema logo no início. É um começo bastante eficiente, pois pode envolver o leitor a pensar e realmente focar no problema principal.

2. Em seguida, apoie sua pergunta fornecendo argumentos e pontos-chave breves, explicando os principais detalhes sobre o problema.

3. Agora é a hora de dar todos os pequenos detalhes que você tiver: o que você tentou, os detalhes mais finos do problema, coisas que podem estar interferindo no seu resultado final, alguns palpites da sua parte... Se a pessoa está muito ocupada e já sabem a resposta, podem pular essa parte inconscientemente, mas às vezes ainda é necessário enriquecer o contexto.

Vamos reescrever nossa primeira mensagem para Joe com tudo isso em mente:

> Eu: Oi Joe! Estou tentando acessar nosso banco de dados, porém, a conexão está sendo recusada.
> 
> Preciso me conectar ao banco de dados para poder verificar os atributos da nossa tabela para finalizar outra tarefa.
> 
> Tentei verificar se meu username e senha estão corretos e também minhas configurações de proxy, e tudo parece estar funcionando bem. Também troquei entre a linha de comando e uma ferramenta GUI, mas ainda não tive sorte.
> Isso pode ser um problema de acesso? Talvez eu não esteja autorizada a acessar o banco?

Depois de um tempo, Joe responde minha mensagem:

> Joe: Olá! Acabei de dar uma olhadinha no sistema principal e sim, foi devido a um problema de autorização. Você não tinha uma permissão válida para acessar o banco de dados, mas acabei de corrigir. Agora vai dar bom!

Dá pra ver como nossa interação foi bem mais rápida? Com apenas duas trocas de mensagens meu problema foi resolvido. Escrevi todas as partes importantes em apenas uma mensagem, como se fosse enviar um  e-mail, onde a outra pessoa poderia demorar um pouco para receber, ler e me responder.

Essa técnica de escrita também nos leva a...

## 2. A arte de obter respostas sem ao menos precisar perguntar para alguém, também conhecida como: a teoria do pato de borracha

Na maioria das vezes, apenas o simples ato de organizar nossos pensamentos pode ser muito benéfico para a resolução de problemas. Como um experimento escrevendo a pergunta do cenário acima em um bloco de notas, sem nem ao menos enviar para ninguém, eu poderia facilmente chegar à mesma conclusão que Joe.

Isso tem correlação direta com o fato de alguém explicar algo complexo para outra pessoa pode aumentar bastante o seu conhecimento próprio sobre a questão em si, as vezes levando diretamente para perceber a solução.

Tentar analisar o problema com os olhos de outra pessoa também pode melhorar a qualidade de nossas perguntas, eliminando redundância e indo direto ao ponto com maior facilidade.

No entanto, às vezes, só analisar o nosso problema não é suficiente. Às vezes, não conseguimos encontrar uma resposta nós mesmos e nosso primeiro instinto pode ser perguntar a outra pessoa, mas por que não perguntar a nós mesmos:

## 3. Você sabe Google-Fu?

O último recurso antes de pedir ajuda pode ser pesquisar no Google. A esse ponto, já isolamos o problema e pensamos um pouco sobre ele e o contexto, então por que não procurá-lo na internet? Pode esclarecer ou solidificar alguns pontos de nossa compreensão da situação.

A experiência de pesquisar no Google pode ser melhorada por:

- [Usando operadores de pesquisa avançada do Google](https://support.google.com/websearch/answer/2466433?hl=en), como aspas duplas;

- Estar atento às palavras-chave escolhidas para pesquisar, procurando focar no objetivo principal da pesquisa;

- Se for algo muito específico, tentar pesquisar em vários idiomas, se possível;

- Não tenha medo de olhar para vários links e fontes em sua pesquisa! A resposta pode estar em um deles;

- Tentando outros motores de busca.

Com tudo isso dito, há também mais uma habilidade a desenvolver - essa sendo mais difícil de equilibrar: em qual ocasião devemos conversar pessoalmente. Às vezes, é melhor ligar para outra pessoa para compartilhar notícias, pedir conselhos ou apenas conversar; isso aumenta o vínculo na equipe, o que é muito importante para melhorar a sinergia geral do time. Outro ponto é que nem todos se sentem confortáveis em se espressar por texto. Nesse caso, a dica de tentar comunicar mais por mensagens no dia a dia pode ser um excelente modo de melhorar as habilidades de comunicação escrita e até falada.