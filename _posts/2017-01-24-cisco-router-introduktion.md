---
layout: post
title: Cisco router - introduktion
date: 2017-01-24 21:01:00
description: Grunderna för att använda Cisco router 
tags: nätverksteknik Cisco
categories: nätverksteknik
---

## Skillnader mot switch

Precis som en switch så konfigureras Cisco routrar via kommandotolken. Mycket är samma som exempelvis de olika lägen user-, enable- och konfigurationsläget. Det finns också en del viktiga skillnader. En viktig skillnad är att efter fabriksåterställning så lägger en switch alla interface i VLAN 1 och startar dem. En router däremot gör tvärt om. Raderar alla inställningar och stänger ner interfacen. En router jobbar dessutom både på lager 2 och 3 vilket gör att vissa kommandon behöver justeras något.

## Grundläggande konfiguration

Bland det första man vill kunna göra är att läsa information om ett interface. Om det handlar om ett vanligt interface, exempelvis en Ethernetport, så används kommandot *show ip interface brief*. Detta kommando listar alla interface på lager 3 och visar dess status inklusive eventuella IP-adresser. Om vi vill ha mer utförlig information om ett interface så kan vi använda *show interface namn*. Observera att vi inte anger ip här. Man kan även skriva med ip och då får vi lite mer utförlig information om ip-inställningarna.

Nästa steg är att sätta en ip-adress på ett interface samt starta det. För att göra detta krävs två kommandon.

- ip address ip-adress mask
- no shutdown

Dessa kommandon förutsätter att vi har angett vilket interface vi vill konfigurera. När detta kommandon är utförda så kan routern kommunicera med det ip-nät som interfacet ansluter till. IP-paket som kommer in till routern på något interface och som har destination i nätet kommer att levereras till destinationen. 

## Routingtabeller

Centralt för en routers funktion är dess routingtabell. En routingtabell innehåller alla de ip-nät som en router känner till. Till varje nät finns även information om hur routern ska skicka vidare paketet. Om en destinations nät inte finns i routingtabellen så kommer paketet kastas. Vi kan även skriva in default-vägar, om det finns default-väg så kommer paket inta att kastas utan skickas vidare via default-väg. Detta liknar principen för hostar som arbetar med default gateway. Användning av default-vägar är väldigt praktiskt i en organisation som får internet via en ISP. Då sätter man default-väg till den router som tillhandahålls av ISP.

För att titta på en routingtabell så skriver vi *show ip route*. När vi har konfigurerat ip-adresser på ett interface så kommer ett antal vägar och adresser att synas i routingtabellen. Detta är de nät som är direkt anslutna via routerns interface. Benämningen för dessa nät är directly connected.

När en router skickar ett IP-paket så bakas detta paket in i ett lager 2 meddelande, exempelvis Ethernet-frame. För omvandling mellan adresser i lager 2 och lager 3 används protokollet arp. För att se denna omvandlingstabell så kan vi använda *show ip arp*.

För att skapa vägar till nät som ansluts via andra routrar så måste en väg till detta nät läggas in i routingtabellen. Det finns i princip två sätt att få in adresser i tabellen.

Den första principen bygger på att en administratör själv konfigurerar vägar. Dessa vägar kallas för statiska routes. För att skapa dessa vägar använder man kommandot *ip route nät-adress mask interface*, eller *ip route nätadress mask next-hop*. Skillnaderna mellan dessa är att man i det först talar om vilket interface som paketet ska använda och det andra talar om vilken ip-adress som ska vara nästa hopp.

Den andra principen bygger på att vi använder ett så kallat dynamiskt routingprotokoll. Det de här routingprotokollen gör är att de kommunicerar med andra routrar för att lära sig nya vägar som läggs in i routingtabellen.
