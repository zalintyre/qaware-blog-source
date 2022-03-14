---
title: "Launching Codineers Meetup Rosenheim"
date: 2022-03-09T10:19:21+01:00
draft: true
author: "Franz Wimmer"
tags: [Meetup, Software Engineering, Quarkus, Flamewar]
image: "codineers-meetup-rosenheim/office-rosenheim.jpg"
aliases:
- /posts/2022-03-09-launching-codineers-meetup/
summary: We finally launched out long planned meetup series about software engineering in Rosenheim.
---

In 2019, QAware opened a new office in Rosenheim, Southern Bavaria. We moved into the beautiful top floor of the old paper factory at Rosenheim. From the very beginning we had plans to set up a new meetup in our town. 

But then, the Coronavirus striked. After two and a half years of planning, sweating and hoping that the coronavirus would allow a meetup in presence, we finally decided to stage the first episode of "Codineers" as a virtual event!
As a quantum of solace, every attendee received a voucher to order themselves a delicious dinner.

## Quarkus Quickstart

The first talk was "Quarkus Quickstart" - a short introduction into the fantastic world of asynchronous and reactive development for backend systems. 
[Quarkus](https://quarkus.io/), a fairly new but by now with version 2.7 more than stable Java Web Application Framework, promises a whole lot of fun while developing systems.

Just imagine you could query three Rest APIs at the same time, setup some processing in the meantime, and automatically get the end result when all the responses have arrived. With Smallrye Mutiny included in Quarkus, you can! 
With the `Uni<T>` type, you have a simple way to request, process and pass objects or queries that might take some time to execute.  

The following example shows three queries to the RKIs official Covid statistics:

```java
@Route(path = "/coviddata")
Uni<CovidData> retrieveCovidData(RoutingExchange rc) {
    Uni<VaccinationsResponse> vaccinationsResponse = vaccionationsClient.getVaccinations();
    Uni<CountryStatisticsResponse> germanStatisticsResponse = statisticsClient.getStatisticsForGermany();
    Uni<DistrictsStatistcsResponse> districtsStatisticsResponse = statisticsClient.getDistrictStatistics();

    Uni<VaccinationsData> vaccinationsData = getVaccinationsData(vaccinationsResponse);
    Uni<StatisticsData> statisticsData = getStatisticsData(germanStatisticsResponse);
    Uni<DistrictStatisticsData> districtStatisticsData = getDistrictStatisticsData(districtsStatisticsResponse);

    return Uni.combine()
        .all()
        .unis(vaccinationsData, statisticsData, districtStatisticsData)
        .combinedWith((vaccinations, statistics, districtStatistics) -> CovidData.builder()
            .vaccinationsData(vaccinations)
            .statisticsData(statistics)
            .districtStatisticsData(districtStatistics)
            .build());
}
```

Neat, isn't it? There is absolutely no hassle with asynchronous callbacks or promises. You can just return a `Uni<T>` in Rest controllers and Quarkus will take care of the rest.

You might think of some corner cases regarding error handling - this is also no problem. Quarkus offers a convenient API to handle errors in asynchronous code:

```java
Uni<String> uni = invokePickyService(client)
    .onFailure()
        .retry()
        .withBackOff(Duration.ofSeconds(1))
        .withJitter(0.2)
        .atMost(10);
```

## X is better than Y! Or is it? The Battle of Java Microservice Frameworks

... TBD

## Stay in touch

Slides for Quarkus Quickstart: https://de.slideshare.net/QAware/quarkus-quickstart

To keep yourself posted about our Meetup series, simply follow us on [meetup.com](https://www.meetup.com/de-DE/Codineers-Meetup/).

{{< img src="/images/codineers-meetup-rosenheim/codineers-header.png" alt="Codineers Meetup Rosenheim" >}}
