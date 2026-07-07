---
layout: post
title: Cisco Catalyst - Konfigurera VLAN
date: 2017-01-19 21:01:00
description: Dela in ditt nät i mindre delar med VLAN 
tags: nätverksteknik Cisco
categories: nätverksteknik
---

## Introduktion

VLAN är ett bra verktyg för att logiskt dela in ett större LAN i mindre delar. Ett interface i VLAN-sammanhang kan befinna sig i två olika lägen. Det kan vara ett access-interface som befinner sig i ett VLAN, eller också så kan interfacet vara i läge trunking för att skicka data från flera VLAN. Trunking används för förbindelser mellan swicthar eller för anslutning till router och andra enheter. För att skilja meddelanden från olika VLAN från varandra används taggar. En vanlig standard för detta är IEEE 802.1q.

## Exempel

För att konfigurera VLAN så behöver man utföra ett antal steg. Jag har valt att göra detta som ett exempel enligt nedanstående figur.

![Principskiss Switch VLAN](/assets/img/Switch_VLAN.png)

Vi har en switch med 10 portar. I mitt exempel är det en Catalyst 2960. De fyra första portarna, Gi0/1-Gi0/4, ska konfigureras som accessinterface för VLAN 10. Nästa fyra portar, Gi0/5-Gi0/8, ska vara accessportar för VLAN 20. Slutligen ska de två sista portarna, Gi0/9 och Gi0/10, konfigureras för trunking.

## Kommandon

Det första som måste göras är att från det globala konfigurationsläget tala om att vi vill aktivera ett VLAN. Samtidigt går vi in i konfigurationsläget. Kommandot för detta är vlan nummer.

I mitt exempel startar jag då med VLAN 10. I konfigurationsläget för VLAN 10 kan jag ge mitt VLAN en beskrivning eller namn med kommandot name.

När jag skapat mina VLAN och gett dem namn så är det dags att placera interface i ett VLAN eller konfigurera dem för trunking.

För att få konfigurationen för accessportarna som beskrivits i exemplet så utförs följande kommandon:

    interface range Gi0/1-4
    switchport access vlan 10
    swicthport mode access
    interface range Gi0/5-8
    switchport access vlan 20
    switchport mode access

För att konfigurera trunking så går vi in i de interface som ska använda trunking. Där skriver vi switchport mode trunk.

Förutom de två lägen som vi har använt (access och trunk) så finns det två andra lägen som kan användas. Dessa är dynamic auto respektive dynamic desirable. Båda dessa sätter ett interface till trunk eller access beroende på vad som läget är på andra sidan av anslutningen. Skillnaden är att med dynamic desirable så kommer interfacet att föredra trunk och om vi då ansluter till ett interface med dynamic auto så kommer vi att få trunking. Detta händer inte om vi har dynamic auto på båda anslutningarna.

## VLAN Trunking Protocol

Cisco har ett äldre eget trunkingprotokoll som heter VTP. Detta bör inte användas. Man kan se om VTP fortfarande är aktivt med show vtp status. Om det är aktivt stäng av det eller sätt det i transparent läge. Detta görs med vtp mode off, respektive vtp mode transparent. 


