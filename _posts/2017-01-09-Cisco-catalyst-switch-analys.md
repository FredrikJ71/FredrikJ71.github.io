---
layout: post
title: Cisco Catalyst Switch - Analys
date: 2017-01-09 21:01:00
description: Analysera nätverkskonfigurationer på en Cisco switch
tags: nätverksteknik Cisco
categories: nätverksteknik
---


## MAC tabeller

En switch lär sig MAC-adresser för anslutna hostar genom att inspektera avsändaradressen i Ethernet-ramar. De adresser som en switch lär sig lagras i tabeller. För att få fram dessa tabeller i en Cisco Catalyst switch så kan vi skriva *show mac address-table*. Oftast är vi endast intresserade av adresser som switchen lärt sig genom inspektion av ramar. Dessa adresser kallas för dynamiska. Det slutliga kommandot vi oftast är intresserade av blir då:
*show mac address-table dynamic*

Ibland vill vi filtrera resultatet för att se en specifik MAC-adress, efter ett visst interface eller för ett specifikt vlan. Detta åstadkommer vi med följande tre alternativ:

- show mac address-table dynamic address *MAC-adress*
- show mac address-table dynamic interface *interfacebenämning*
- show mac address-table dynamic address *vlan id*

Adresser stannar i MAC-tabellen i en viss tid även om inga nya ramar skickas från en adress. Hur länge denna tid är beror på något som kallas för *aging-time*. Som default är denna satt till 300 sekunder. För att lista storleken på aging time används kommandot show mac address-table aging-time. Adresser kan även raderas innan tiden runnit ut för dem om minnet blir fullt. Då raderas de äldsta raderna ut tabellen. För att se storlek på tabellen fast inte tabellen i sig så kan vi skriva *show mac address-table count*.

Om vi vill kan vi radera alla dynamiska adresser från mac-tabellen med *clear mac address-table dynamic*.

## Interface

För att inspektera de olika interface (portar) som finns på switchen används *show interface status*. Med detta kommando så listas alla interface med deras namn. Dessutom får vi reda på om någon host är ansluten, vilket vlan som interfacet tillhör, samt duplex och hastighet.

Det finns flera andra alternativ som kan vara intressanta. Om vi inte tar med alternativet status så får vi betydligt mer information. Denna information kan vara intressant för att få fram för individuella interface men jag ser ingen större användning av att lista den kompletta informationen för alla interface.

Om vi vill ha information om ett specifikt interface, exempelvis Gi0/2 kan vi skriva *show interface Gi0/2 status* för kort information eller *show interface Gi0/2* för komplett information. Ibland vill vi ha statistik över trafiken som har passerat ett visst interface. Detta kan vi få genom att ange counters, exempelvis enligt följande *show interfaces Gi0/3 counters*. Med statistiken ser vi antal ingående och utgående ramar indelat i unicast, multicast och broadcast.
