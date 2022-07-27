---
layout: post
title:  "Nasa Exoplanet Archive"
date:   2022-07-23T05:00:00-00:00
author: Graciele Damasceno
categories: technology
tags:    project python astronomy
lang: en
---

## Nasa Exoplanet Archive

This is a pet project developed to learn more about Python APIs. The main objective here was to gather information using a endpoint that offers planet's data. The data used in the application comes from Nasa Exoplanet Science Institute website, through an API call. 

Nasa Exoplanet Archive is an stellar and astronomical exoplanet catalog that collates and cross-correlates data and information about exoplanets. You can check more about the website and the initiative [here][aboutnea].

#### Project Details

The project used the following external libs:

- `FastApi` to create endpoints to expose data,

- `Requests` to fetch data of Nasa Exoplanet Archive database,

- `MongoClient` to save data  locally,

- `Pandas` + `MatPlotLib` to create meaningful charts using the data collected.

##### API Endpoints:

`/sync-database`: endpoint that calls Exoplanets' Archive website and collect the response, saving or updating existing data into a local database. 

**Curl**:

```bash
curl -X 'GET' \
  'http://localhost:8000/sync-database' \
  -H 'accept: application/json'
```

**Response**:

```json
{
  "code": 200,
  "message": "Database successfully updated 32606 elements",
  "data": "",
  "total_elements": 32606
}
```

**Personal Notes:** This endpoint *may* take a while to load. I tried to be gentle while building the API request to the archive, selecting only a few more "interesting" parameters in order to build my local database:

```python
file_url = "https://exoplanetarchive.ipac.caltech.edu/TAP/sync?query=select+pl_name,sy_snum,sy_pnum,disc_year," \
           "discoverymethod,disc_facility,disc_locale,disc_telescope,pl_orbper,pl_rade,pl_bmasse+from+ps&format=json"
```

I made the decision to save it all in a Mongo database just to test `MongoClient`'s integration with Pyhton, no other special reason. 😁 And look, at the time of the requisition the archive has 32606 elements! That's pretty cool!

---

`/planets`: Get all planets paginated with usage of filters (planet name, discovery method, discovery facility, discovery year).

**Curl**:

```bash
curl -X 'GET' \
  'http://localhost:8000/planets?size=3&page_num=1&ordered=desc' \
  -H 'accept: application/json'
```

**Response**:

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

**Personal Notes:** I've paginated the response and order it by discovery year. There's also a link that leads to Archive's website, to check more info about the planet in question 🌎

---

`/plot/planet/discovery-year`: Produces a chart using the number of planets discovered grouped by year. 

**Curl:**

```bash
curl -X 'GET' \
  'http://localhost:8000/plot/planet/discovery-year' \
  -H 'accept: application/json'
```

**Response:**

<a href="/dev-on-track/assets/posts/2022-07-23/plot-discovery-year.png" data-lightbox="charts" data-title="Chart with number planets by year">
  <img src="/dev-on-track/assets/posts/2022-07-23/plot-discovery-year.png" title="Chart with number planets by year">
</a>

**Personal Notes:** Pretty wild to conclude that the number of planets discovered was expressively higher at 2014 and 2016. I had to adjust the chart to display the number of planets using scientific notation, since the discrepancy was so overbearing that the other years almost didn't show up!

---

`/plot/planet/discovery-method`: Produces a chart using the number of planets discovered grouped by discovery method.

**Curl:**

```bash
curl -X 'GET' \
  'http://localhost:8000/plot/planet/discovery-method' \
  -H 'accept: application/json'
```

**Response:**

<a href="/dev-on-track/assets/posts/2022-07-23/plot-discovery-method.png" data-lightbox="charts" data-title="Chart with number planets by discovery method">
  <img src="/dev-on-track/assets/posts/2022-07-23/plot-discovery-method.png" title="Chart with number planets by discovery method">
</a>

**Personal Notes:** Again, discovering planets by transit (check out Nasa's [explanation of the method!][transit]) seems to be the most popular way to go. The visualization was also adjusted to scientific notation.

---

`/plot/planet/discovery-facility`: Produces a chart using the number of planets discovered grouped by discovery facility.

**Curl**:

```bash
curl -X 'GET' \
  'http://localhost:8000/plot/planet/discovery-facility' \
  -H 'accept: application/json'
```

**Response:**

<a href="/dev-on-track/assets/posts/2022-07-23/plot-discovery-facility.png" data-lightbox="charts" data-title="Chart with number planets by discovery facility">
  <img src="/dev-on-track/assets/posts/2022-07-23/plot-discovery-facility.png" title="Chart with number planets by discovery facility">
</a>

**Personal Notes:** [Kepler][kepler] takes the cake as the "facility" that discovered more planets! Way to go! 🚀

---

`/plot/planet/discovery-locale`: Produces a chart using the number of planets discovered grouped by discovery locale.

**Curl**:

```bash
curl -X 'GET' \
  'http://localhost:8000/plot/planet/discovery-locale' \
  -H 'accept: application/json'
```

**Response**:

<a href="/dev-on-track/assets/posts/2022-07-23/plot-discovery-locale.png" data-lightbox="charts" data-title="Chart with number planets by discovery locale">
  <img src="/dev-on-track/assets/posts/2022-07-23/plot-discovery-locale.png" title="Chart with number planets by discovery locale">
</a>

**Personal Notes:** Space is a privileged point of view to see other planets. There's two instances of "Multiple Locale" that could've been merged, however, I thought that it was amusing and let it that way. 😬

---

`/plot/planet/discovery-telescope`: Produces a chart using the number of planets discovered grouped by discovery telescope.

**Curl**:

```bash
curl -X 'GET' \
  'http://localhost:8000/plot/planet/discovery-telescope' \
  -H 'accept: application/json'
```

**Response**:

<a href="/dev-on-track/assets/posts/2022-07-23/plot-discovery-telescope.png" data-lightbox="charts" data-title="Chart with number planets by discovery telescope">
  <img src="/dev-on-track/assets/posts/2022-07-23/plot-discovery-telescope.png" title="Chart with number planets by discovery telescope">
</a>

**Personal Notes:** It was a shock to discover that in second place to Kepler in number of planets discovered was the Canon 200mm f1.8, a camera over 30 years old! 🤯

---

## Final Thoughts

This was one of my first opportunities to work with `pandas` and building charts in Python and I couldn't be happier! I really enjoyed building this tiny API and has lots of fun discovering more about planets, which is one of my personal interests. You can check the [repository with all source code here][repo].



[aboutnea]: https://exoplanetarchive.ipac.caltech.edu/docs/intro.html

[transit]: https://exoplanets.nasa.gov/faq/31/whats-a-transit/

[kepler]: https://exoplanets.nasa.gov/keplerscience/

[repo]: https://github.com/GracieleDamasceno/nasa-exoplanet-archive