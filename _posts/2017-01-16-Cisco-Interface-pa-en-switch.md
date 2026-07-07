---
layout: post
title: Cisco - Interface på en switch
date: 2017-01-16 21:01:00
description: Konfigurationera interface på en Cisco switch 
tags: nätverksteknik Cisco
categories: nätverksteknik
---

## Grundläggande inställningar på ett interface

Från det globala konfigurationsläget som vi kan nå via configure terminal så kan vi hoppa direkt till ett interface genom att ge dess namn, exempelvis *interface Gi0/2*. Väl där så kan vi göra ett antal inställningar. Några exempel är följande:

- shutdown – stänger av ett interface. När vi listar status på interfacet så anges status till disabled. I vissa listningar ser vi också benämningen administratively down.
- no shutdown – aktiverar ett interface. (OBS! ordet no används frekvent för att utföra motsatsen till ett kommando.)
- duplex full respektive duplex half – sätter ett önskat duplexläge på interfacet. Detta ändrar de inställningar som förhandlats fram vid anslutning till switchen. För att använda den förhandlade inställningar använder man duplex auto eller no duplex.
- speed hastighet – sätter hastigheten. Hastigheten anges i Mbps (exempelvis 10/100/1000.) Även här kan man använda den förhandlade hastigheten med speed auto eller no speed.
- description – Används för att ge en beskrivning av interfacet. Exempelvis description filserver, vilket kan användas för att visa att vår filserver ska anslutas till detta interface. Med bra beskrivningar så underlättas felsökning.

## Arbeta med flera interface samtidigt

Ofta så vill man ha samma inställningar för flera interface. Som exempel antag att vi inte har några hostar eller andra switchar anslutna till interface Gi0/7 och Gi0/8. Det man då vill göra är att stänga dessa interface så att en eventuell angripare som lyckas ansluta inte ska få tillgång till nätet.

Det kommando som då används är att ange *range* som ger ett intervall. För vårt exempel ovan så skriver vi *interface range Gi0/7 – 8*. (Ofta anger vi det kortare skrivsättet, *int ran Gi0/7 – 8*.) Sedan kan vi stänga ner interfacen med shutdown.

## Port security

Ett problem vi har i dagens nätverk är att angripare försöker ansluta till vårt nät. För att försvåra angrepp så kan vi använda något som kallas port security. Med port security så kan vi sätta regler för vilka hostar som får ansluta. En sak som är viktigt att notera är att ett krav för att få sätta port security är att porten måste vara antingen access- eller trunk-interface. (Används för VLAN) Vi startar därför med *switchport mode access* eller *switchport mode trunk*.

För att tala om för en switch att vi vill ha port security för ett interface skriver vi *switchport port-security*. Därefter har vi ett antal alternativ beroende på vad vi vill åstadkomma.

- switchport port-security maximum antal – sätter begränsningar så att maximalt antal stycken MAC-adress tillåts skicka data Ethernet-ramar via detta interface. Observera att detta inte anger vilka MAC-adress utan bara hur många olika.
- switchport port-security violation åtgärd – talar om för switchen vilken åtgärd som ska utföras när den upptäcker ett brott mot villkoren. De åtgärder som används är protect, restrict eller shutdown. Protect betyder att man kastar de ramar som kommer från okänt MAC-adress. Restrict loggar dessutom överträdelsen genom att räkna upp en räknare för överträdelser samt skickar logg och SNMP-meddelande. Shutdown som är default-inställningen stänger ner porten vilket får till följd att även legitim trafik blockeras. Observera att vid överträdelse så kommer status inte att visas som shutdown utan som error disabled (err-disabled). För att återställa ett interface så måste man först stänga ner det med shutdown och därefter starta det igen.
- switchport port-security  mac-address adress – talar om att MAC-adressen tillåts för denna port. Vi kan upprepa kommandot om vi vill tillåta flera MAC-adresser till en port. Detta är en bra inställning om vi på förhand vet vilken host som är ansluten till en viss port. Om vi byter dator eller byter nätverkskort måste vi gå in och konfigurera om inställningarna. Detta kanske går att göra i en hemmiljö men i en större organisation är detta inte hanterbart i praktiken. Vi kan då använda följande alternativ.
- switchport port-security  mac-address sticky – här lär sig switchen själv vilken MAC-adress som ska få tillgång. När en host ansluter så ser switchen MAC-adressen. Under förutsättningen att maximalt antal hostar inte är nådda så lagras MAC-adressen. Denna inställning lagras bara i running-config vilket gör att sticky försvinner om switchen startas om. Detta utnyttjas både för administration då man startar om switchen om man har en ny dator ansluten till ett interface. Då lagras den nya adressen i stället för den gamla. Tyvärr kan detta utnyttjas av en angripare om hen lyckas tvinga switchen till en omstart.
- show port-security interface namn – visar status för port-security för ett interface.

## Listning av MAC-adresser och port security

Ett knep som används för att filtrera statiska default adresser som finns lagrade i switchens MAC-tabell så skriver man ofta show mac address-table dynamic. Detta fungerar inte när vi använder port security. En port som använder port security är inte dynamisk utan statisk. Men vi vill inte se den tillsammans med  övriga statiska default-adresser. För att kontrollera MAC-tabellen för portar med port security används: show mac address-table secure