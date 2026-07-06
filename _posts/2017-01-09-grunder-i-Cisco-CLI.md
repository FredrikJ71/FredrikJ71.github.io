---
layout: post
title: Grunder i Cisco CLI
date: 2017-01-09 21:01:00
description: introduktion till konfiguration av Cisco
tags: nätverksteknik Cisco
categories: nätverksteknik
---



## Anslutning

För att administrera Ciscos nätverksutrustning på en grundläggande nivå är via deras kommandogränssnitt (CLI) via seriell konsol-anslutning. För att göra detta används följande steg:

1. Koppla en Cisco-konsolkabel till uttaget. Observera att uttaget ser ut som en vanlig RJ45-ethernetport men det är en seriell anslutning. Andra änden ansluts till seriellt uttag eller via dongel till USB.
2. Anslut med terminalprogram. Lämpliga program är putty eller Tera Term. Följande inställningar ska användas för den seriella anslutningen: 9600 bps, ingen flödeskontroll, 8-bitars ASCII, ingen paritet och 1 stoppbit. Om allt fungerar som de ska får du följande prompt i terminalprogrammet: namn>
    namn är namnet på enheten, exempelvis switch1

Förutom att ansluta med konsolkabel så kan man konfigurera enheten för anslutning via nätverk med Telnet (osäkert) eller SSH (säkrare). Principer för att konfigurera detta kommer senare.

## Mode (lägen)

En Cisco-enhet använder sig av olika modes (lägen). För varje mode kan man utföra olika kommandon. Tanken bakom detta är att minimera påverkan om man skriver fel i ett kommando. Som exempel: Om man vill stänga av ett visst interface så måste man först ange att man vill göra inställningar till just detta interface och därefter stänga interface. På detta sätt undviker man att stänga ner fel interface. (Under förutsättning att man inte angett fel interface när man väljer läge.) De olika mode kan beskrivas enligt följande.

1. user mode. Detta är det första läge man kommer till och kännetecknas av att prompten ser som följande: namn>Denna mode som kallas för user mode heter egentligen user EXEC mode. I detta läge kan man göra väldigt begränsade saker, såsom att pinga en annan enhet och inte minst hoppa vidare till nästa kraftfullare steg.
2. enable mode, som också kallas privileged EXEC mode. Detta läge nås genom kommandot enable och kännetecknas av att prompten byter utseende till namn#
3. configuration mode, är ett grundläge för att göra inställningar. För att nå detta läge skriver man configure terminal. Man ser att man hamnat i konfigurationsläget genom att prompten ändrats till namn(config)#
För att lämna detta läge och gå tillbaks till enable mode så skriver man end eller Ctrl+Z. Från det grundläggande konfigurationsläget kan man sedan gå vidare till mer specifika lägen. Dessa är olika beroende på enhet men för en switch används typiskt följande; interface, vlan, line console 0 och line vty. Från dessa mer specifika lägen går vi tillbaks till grundläggande konfigurationsläge med exit.

## Hjälp

Eftersom de flesta personer inte dagligen arbetar med alla kommandon så är det svårt att komma ihåg exakt hur ett kommando ser ut. Cisco har därför utvecklat hjälpfunktioner i gränssnittet. För att få hjälp skriver vi ?, alltså ett frågetecken.

Vi kan dessutom få hjälp med ett visst kommando genom att skriva kommando?, där kommando är namnet på ett kommando. Om man då inte kommer ihåg hela namnet på ett kommando kan man skriva början på ett kommando och sedan ?.

För eventuella parametrar till ett kommando så anger vi kommando param?

I Cisco CLI så kan vi komplettera ett kommando med <TAB> precis som för många andra OS. Det fungerar också att ange kortkommando för ett kommando om man vill spara tangenttryckningar. En sak som jag upptäckt är att tekniker anser sig mer kunniga och duktiga genom att aldrig använda hela namnet utan bara kortkommandot.

## Minne och Konfigurationsfiler

Ciscoenheter är normalt utrustande med följande minnestyper.
### RAM
Arbetsminne samt nuvarande konfiguration, running config

### Flash
Cisco IOS

### ROM
Används vid bootning av enheten. I princip enkel uppstart samt laddning av OS i Flash-minnet. Jämför med BIOS

### NVRAM
Här lagras den konfigurationsfil som enheten startar med efter uppstart, startup config.

Vid uppstart laddas startup config från NVRAM till RAM och blir därefter running config. De ändringar och konfigurationer som vi gör därefter sparas i running config. En konsekvens av detta är att konfigurationerna försvinner vid en omstart.

För att lagra de ändringar vi gjort så kan vi skriva över startup config med kommandot copy running-config startup-config. 

Ibland så vill man rensa enheten och starta om från början. För att göra detta kan vi använda något av följande tre sätt för att radera startup-config: write erase, erase startup-config, erase nvram. Därefterstartar vi om enheten med reload.

 
## Kommandon

- enable

    Byter från user mode till enable mode.
- disable

    Byter från enable mode till user mode.
- reload

    Bootar om (omstart) enheter.
- configure terminal

    Går från enable till konfigurationsläget.
- end

    Går från konfigurations- till enable-läget.
- exit

    Lämnar ett specifikt konfigurationsläget och går tillbaks till grundläggande konfigurationsläget.
- enable secret *password*

    Gör så att användaren måste ange ett lösenord (som anges av password) för att gå till enable mode. Detta är ett krav för att kunna logga in med telnet eller SSH.
- login

    Anges i konfigurationsläget för line eller consol för att ange att det krävs ett lösenord för att ansluta till enheten
    password password
    Skrivs efter login och sätter lösenord.
- ?

    Hjälp
- show

    Kommandot show används med olika parametrar för att visa olika typer av data.
- debug

    Spelar in data under användning. Data kan sedan presenteras för användaren.
- hostname *namn*

    Ger enheten ett nytt namn som presenteras i prompt.
- copy running-config startup-config

    Kopierar nuvarande inställningar till NVRAM så att de behålls efter omstart.
- write erase, erase startup-config, erase nvram

    Tre kommandon för att radera uppstartskonfigurationen för enheten.
- delete vlan.dat

    Raderar information om VLAN
