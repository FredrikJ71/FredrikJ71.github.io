---
layout: post
title: Cisco Routing - RIPv2
date: 2017-01-24 21:01:00
description: Hur man kan bygga upp ett enkelt nät med flera routrar 
tags: nätverksteknik Cisco
categories: nätverksteknik
---



Det finns flera olika routingprotokoll för att dynamisk skapa vägar och uppdatera routingtabellen. Ett av de enklaste och som fungerar i mindre miljöer är RIPv2. (Routing Information Protocol version 2) I denna laboration kommer vi att titta på hur man konfigurerar RIPv2 i Ciscos routrar. 

RIPv2 är ett distance vector-protokoll. Detta innebär att en router lär sig antal hopp till målet, samt nästa router på vägen till målet. RIPv2 minimerar antalet hopp till målet men tar ingen hänsyn till hastighet på länkarna. Även om RIPv2 tar hänsyn till nätmasken och fungerar med subnetting så bör man i flera fall välja ett moderna protokoll såsom OSPF. En fördel med RIP är att det är enkelt samt att utrustning från olika tillverkare stödjer det.

## Konfiguration

Eftersom det finns få inställningsmöjligheter med RIP så betyder det också att det är enkelt att konfigurera. Från det globala konfigurationsläget utförs följande kommandon:

- Hoppa till konfiguration av RIP. *router rip*
- Tala om att vi vill ha RIPv2. *version 2*
- Tala om vilka IP-nät som ska annonseras. *network nätverks-adress*

När kommandot körts på alla routrar i nätet så lär de sig vägarna till andra nät.

En annan funktion som kan vara bra att använda är att publicera default information med hjälp av RIP. Som exempel antag att en organisation vars router R0 ansluter till internet via en router från ISP. R0 kan då tala om för andra routrar att den har en väg till alla andra adresser än de som vi har lokalt i vår organisation. För att göra detta så börja med att skapa en default route med *ip route 0.0.0.0 0.0.0.0 ISP-IP*, där ISP-IP är den adress som vår ISPs router har. För att sedan sprida denna adress så skriver vi i konfigurationen för RIP, *default-information originate*.

Andra saker som kan vara bra att känna till är om vi använder subnetting så kan ibland nät slås samman med något som kallas auto-summarization. Om detta ställer till problem så kan detta stängas av med *no auto-summary*. Om det finns flera vägar till ett mål med samma avstånd så kan routern lagra dessa vägar och sedan använda lastbalansering och välja väg. För att ange hur många vägar som kan lagras för ett nät skriver vi *maximum-paths antal*.

## Felsökning

Ibland blir det fel i våra nät och då är det viktig att vi kan felsöka. För att göra detta finns ett antal kommandon som kan vara bra att känna till:

- show ip interface brief – Listar alla interface, en rad per interface. Visar bland annat IP-adress samt om interfacet är uppe eller nere.
- show ip route – Listar routingtabellen.
- show ip route adress – anger hur routern hanterar ett IP-paket som ska till en host med adressen adress.
- show ip protocols – ger en detaljerad lista över de protokoll som är aktiva.
- show ip rip database – visar en rad för varje subnät som finns i databasen. Bland annat visas next-hop eller om nätet är direktanslutet.

