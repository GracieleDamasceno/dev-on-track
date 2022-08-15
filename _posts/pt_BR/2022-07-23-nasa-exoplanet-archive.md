---
layout: post
title:  "Arquivo de Exoplanetas da Nasa"
date:   2022-07-23T05:00:00-00:00
author: Graciele Damasceno
categories: technology
tags:    project python astronomy
lang: pt_BR
---

Este é um projetinho desenvolvido para aprender mais sobre APIs e gráficos em Python. O principal objetivo aqui foi coletar informações usando um endpoint que oferece dados de exoplanetas (que são planetas fora do nosso sistema solar). Os dados usados na aplicação são provenientes do site do Nasa Exoplanet Science Institute, por meio de uma chamada de API. 

O Nasa Exoplanet Archive é um catálogo de estrelas e exoplanetas que reúne e correlaciona dados e informações sobre exoplanetas. Você pode conferir mais sobre o site e a iniciativa [aqui](https://exoplanetarchive.ipac.caltech.edu/docs/intro.html).

#### Detalhes do Projeto

O projeto usa as seguintes bibliotecas externas:

- `FastApi` para criar endpoints e expôr dados,

- `Requests` para buscar informações do banco de dados do Nasa Exoplanet Archive,

- `MongoClient` para salvar tais dados localmente,

- `Pandas` + `MatPlotLib` para criar gráficos bacanas com os dados coletados.

#### Endpoints da API:

`/sync-database`: endpoint que chama o site do Exoplanets' Archive e coleta a resposta, salvando ou atualizando os dados existentes em um banco de dados local.

**Curl**:

```bash
curl -X 'GET' \
  'http://localhost:8000/sync-database' \
  -H 'accept: application/json'
```

**Resposta**:

```json
{
  "code": 200,
  "message": "Database successfully updated 32606 elements",
  "data": "",
  "total_elements": 32606
}
```

**Observações pessoais:** este endpoint *pode* demorar um pouco para carregar. Tentei pegar leve ao construir a solicitação da API para o site da Nasa, selecionando apenas mais alguns parâmetros mais "interessantes" para construir meu banco de dados local:

```python
file_url = "https://exoplanetarchive.ipac.caltech.edu/TAP/sync?" \
                "query=select+pl_name,sy_snum,sy_pnum,disc_year," \
                "discoverymethod,disc_facility,disc_locale," \    
                "disc_telescope,pl_orbper,pl_rade,pl_bmasse+" \
                "from+ps&format=json"
```

Tomei a decisão de salvar tudo em um banco de dados Mongo apenas para testar a integração do `MongoClient` com o Pyhton, sem nenhum outro motivo especial além disso. E olha, no momento que fiz essa requisição arquivo da Nasa possui 32606 elementos! Isso é bem legal!

---

`/planets`: Retorna todos os exoplanetas paginados com o uso de filtros (nome do exoplaneta, método de descoberta, onde foi descoberto, ano de descoberta).

**Curl**:

```bash
curl -X 'GET' \
  'http://localhost:8000/planets?size=3&page_num=1&ordered=desc' \
  -H 'accept: application/json'
```

**Resposta**:

```json
{
  "code": 200,
  "message": "Planets successfully retrieved",
  "data": [
    {
      "id": "62ddfacf34ec799ba78a342f",
      "planet_name": "TOI-1272 c",
      "number_of_stars": 1,
      "number_of_planets": 2,
      "discovery_year": 2022,
      "discovery_method": "Radial Velocity",
      "discovery_facility": "W. M. Keck Observatory",
      "discovery_locale": "Ground",
      "discovery_telescope": "10 m Keck I Telescope",
      "orbital_period": 8.689,
      "planet_earth_radius": null,
      "planet_earth_mass": 26.7,
      "link": "https://exoplanetarchive.ipac.caltech.edu/overview/TOI-1272 c"
    },
    {
      "id": "62ddfacf34ec799ba78a348b",
      "planet_name": "TOI-500 d",
      "number_of_stars": 1,
      "number_of_planets": 4,
      "discovery_year": 2022,
      "discovery_method": "Radial Velocity",
      "discovery_facility": "La Silla Observatory",
      "discovery_locale": "Ground",
      "discovery_telescope": "3.6 m ESO Telescope",
      "orbital_period": 26.233,
      "planet_earth_radius": null,
      "planet_earth_mass": 33.12,
      "link": "https://exoplanetarchive.ipac.caltech.edu/overview/TOI-500 d"
    },
    {
      "id": "62ddfacf34ec799ba78a348c",
      "planet_name": "TOI-500 e",
      "number_of_stars": 1,
      "number_of_planets": 4,
      "discovery_year": 2022,
      "discovery_method": "Radial Velocity",
      "discovery_facility": "La Silla Observatory",
      "discovery_locale": "Ground",
      "discovery_telescope": "3.6 m ESO Telescope",
      "orbital_period": 61.3,
      "planet_earth_radius": null,
      "planet_earth_mass": 15.05,
      "link": "https://exoplanetarchive.ipac.caltech.edu/overview/TOI-500 e"
    }
  ],
  "total_elements": 32606
}
```

**Notas Pessoais:** A resposta vem paginada e ordenada por ano de descoberta. Na resposta, todos os elementos possuem um link que leva ao site do Arquivo, para verificar mais informações sobre o exoplaneta em questão.

---

`/plot/planet/discovery-year`: Produz um gráfico com todos os planetas agrupados por ano de descoberta.

**Curl:**

```bash
curl -X 'GET' \
  'http://localhost:8000/plot/planet/discovery-year' \
  -H 'accept: application/json'
```

**Resposta:**

<a href="/dev-on-track/assets/posts/2022-07-23/plot-discovery-year.png" data-lightbox="charts" data-title="Chart with number planets by year">
  <img src="/dev-on-track/assets/posts/2022-07-23/plot-discovery-year.png" title="Chart with number planets by year">
</a>

**Notas pessoais:** Muito louco concluir que o número de planetas descobertos foi expressivamente maior em 2014 e 2016. Tive que ajustar o gráfico para exibir o número de planetas usando a notação científica, já que a discrepância era tão grande que os valores dos outros anos quase não apareceram!

---

`/plot/planet/discovery-method`: Produz um gráfico com todos os planetas agrupados por método de descoberta.

**Curl:**

```bash
curl -X 'GET' \
  'http://localhost:8000/plot/planet/discovery-method' \
  -H 'accept: application/json'
```

**Resposta:**

<a href="/dev-on-track/assets/posts/2022-07-23/plot-discovery-method.png" data-lightbox="charts" data-title="Chart with number planets by discovery method">
  <img src="/dev-on-track/assets/posts/2022-07-23/plot-discovery-method.png" title="Chart with number planets by discovery method">
</a>

**Notas pessoais:** Mais uma vez, descobrir planetas por trânsito (confira a [explicação desse método de descoberta](https://exoplanets.nasa.gov/faq/31/whats-a-transit/)) parece ser a maneira mais popular de se descobrir exoplanetas. A visualização também foi ajustada à notação científica.

---

`/plot/planet/discovery-facility`: Produz um gráfico com todos os planetas agrupados por estação de descoberta.

**Curl**:

```bash
curl -X 'GET' \
  'http://localhost:8000/plot/planet/discovery-facility' \
  -H 'accept: application/json'
```

**Resposta:**

<a href="/dev-on-track/assets/posts/2022-07-23/plot-discovery-facility.png" data-lightbox="charts" data-title="Chart with number planets by discovery facility">
  <img src="/dev-on-track/assets/posts/2022-07-23/plot-discovery-facility.png" title="Chart with number planets by discovery facility">
</a>

**Notas Pessoais:** [Kepler](https://exoplanets.nasa.gov/keplerscience/) ganha o prêmio como "estação" que mais descobriu exoplanetas! 

---

`/plot/planet/discovery-locale`: Produz um gráfico com todos os planetas agrupados por local de descoberta.

**Curl**:

```bash
curl -X 'GET' \
  'http://localhost:8000/plot/planet/discovery-locale' \
  -H 'accept: application/json'
```

**Resposta**:

<a href="/dev-on-track/assets/posts/2022-07-23/plot-discovery-locale.png" data-lightbox="charts" data-title="Chart with number planets by discovery locale">
  <img src="/dev-on-track/assets/posts/2022-07-23/plot-discovery-locale.png" title="Chart with number planets by discovery locale">
</a>

**Notas Pessoais:** O espaço é um ponto de vista bem privilegiado (Kepler que o diga!) para ver outros planetas. Há duas instâncias de "Multiple Locale" que poderiam ter sido mescladas, no entanto, achei divertido que uma possui S e a outra não e acabei deixando assim.

---

`/plot/planet/discovery-telescope`: Produz um gráfico com todos os planetas agrupados por telescópio usado na descoberta.

**Curl**:

```bash
curl -X 'GET' \
  'http://localhost:8000/plot/planet/discovery-telescope' \
  -H 'accept: application/json'
```

**Resposta**:

<a href="/dev-on-track/assets/posts/2022-07-23/plot-discovery-telescope.png" data-lightbox="charts" data-title="Chart with number planets by discovery telescope">
  <img src="/dev-on-track/assets/posts/2022-07-23/plot-discovery-telescope.png" title="Chart with number planets by discovery telescope">
</a>

**Notas pessoais:** Foi um choque descobrir que em segundo lugar atrás do Kepler em número de exoplanetas descobertos estava a Canon 200mm f1.8, uma câmera com mais de 30 anos!

--- 

## Conclusão

Esta foi uma das minhas primeiras oportunidades de trabalhar com `pandas` e construir gráficos em Python e eu não poderia estar mais feliz! Eu gostei bastante de construir uma API e me diverti muito descobrindo mais sobre exoplanetas, que é um dos meus interesses pessoais. Você pode verificar o [repositório com todo o código fonte aqui](https://github.com/GracieleDamasceno/nasa-exoplanet-archive).
