---
layout: post
title: Cisco Catalyst - Management och Säkerhet
date: 2017-01-10 21:01:00
description: Lösenordsskydd och grundläggande säkerhet
tags: nätverksteknik Cisco Säkerhet
categories: nätverksteknik
---

## Bakgrund

En switch eller router som kan konfigureras med hjälp av kommandotolk via konsolkabel eller via nätverk behöver konfigureras för att göra det svårare för en angripare att få access. Utan detta skydd så kan en angripare göra inställningar på enheten som påverkar säkerheten i hela nätet. Om vi exempelvis har separerat vårt nät i logiska subnät och VLAN så kan en angripare göra så nät som skulle vara åtskilda istället blir sammankopplade.

Extra viktigt är detta när man inte längre behöver fysisk access via konsol utan man kan konfigurera enheten via nätet. Naturligtvis bör man även se över den fysiska säkerheten för att göra det svårare med access.

## Lösenordsskydd

En av de första skydden man kan lägga till är att man måste logga in på enheten innan man får access. Detta kan göras på några olika sätt.

1. Gemensamt lösenord. I denna metod så har alla användare samma lösenord för access. Det som skyddas är access till ett visst gränssnitt. Det betyder att vi kan ha olika lösenord för konsolen och nätverksanslutningarna.För att sätta ett gemensamt lösenord så flyttar man till konfigurationsläget för aktuell anslutning. Därefter anger man *login* för att tala om att gemensamt lösenord ska användas. Efter detta steg anger man lösenord med *password lösenord*. Exempel för konsol:
- line console 0
- login
- password Passwd

    Observera följande! Denna metod skapar visserligen ett lösenord men lösenordet delas av alla användare. Dessutom så syns lösenordet i klartext om man listar running-config.
2. Skapa lokala användarnamn och lösenord. Denna teknik är något säkrare jämfört med bara ett enkelt lösenord. På enheten skapas en användarlista med användarnamn och ett lösenord. Förutom fördelen med att varje användare har sitt egna lösenord så lagras inte lösenordet i klartext. Vid lagring av lösenord så används en hash-funktion (md5). För att skapa en användardatabas så skriver vi följande i det globala konfigurationsläget: *username namn secret lösenord*.  När användarna sedan har skapats så talar vi om att vi vill använda användardatabasen genom att gå in på aktuell anslutning och skriva *login local*.

3. Centraliserade användardatabas. Med lokala användare så måste man skapa användardatabas på alla enheter. Detta är ett väldigt omständligt steg samt ger flera felkällor. Om en användare vill byta lösenord så måste man se till att byta på alla enheter var för sig. Ett modernare alternativ är att ha en centraliserad användarlista och sedan låta enheten autentisera användarna användarna med hjälp av RADIUS som ansluter till den centrala databasen.

## Säkra enable mode

Förutom att kräva att användarna måste logga in innan de får ansluta till enheterna så kan man även tvinga ett nytt lösenord när man vill gå från user till enable-mode. För att konfigurera ett enable-lösenord skriver man: *enable secret lösenord*.

Det finns även ett äldre sätt där man skriver *enable password lösenord*. Vid en första anblick ser det ut som om dessa metoder är samma sak men de har en viktig skillnad. Med enable secret så lagras inte lösenordet i klartext utan en hashfunktion används. Däremot så lagras det i klartext med enable password.

## Ge en switch en IP-adress

En switch har inget behov av en IP-adress i sin normala funktion. Det är faktiskt så att inte ens MAC-adressen är viktigt för hur en switch skickar vidare ramar. Anledningen till att man vill sätta en IP-adress är om man vill kunna konfigurera switchen via nätverket i stället för att hålla på med konsol-kabeln.

För att vara noggrann så är det inte switchen som har en ip-adress. I stället är det den inbyggda datorn som konfigurerar switchen som får en IP-adress. Den inbyggda datorn har ingen fysisk anslutning till nätet. I stället ansluter den via ett virtuellt interface som är anslutet till ett VLAN. Alltså måste man konfigurera VLAN för att därefter tilldela ett virtuellt interface anslutet till ett VLAN en IP-adress. De steg som utförs är följande:

- Gå till läget för det virtuella interfacet med *interface vlan id*.
- Sätt en IP-adress med *ip address ip-adress nätmask*. (Vi kan även få ip via DHCP med *ip address dhcp*)
- Starta det virtuella interfacet med *no shutdown*.
- Från det globala konfigurationsläget så kan vi ställa in default gateway och DNS-server med kommandona: *ip default-gateway ip-address*, respektive *ip name-server ip-address*.

## Telnet

För att ansluta via nätet så kan vi använda det gamla telnet-protokollet. Detta fungerar men rekommenderas inte då alla kommandon skickas i klartext. Om vi dessutom väljer att lista konfigurationen med så kommer även den att skickas i klartext. Men om man trots detta väljer att konfigurera Telnet så kommer här en kort guide. Man behöver i princip enbart utföra följande tre korta steg som är beskrivna tidigare.

1. Sätt IP-adress på ett virtuellt VLAN-interface.
2. Skapa ett lösenord som krävs för anslutning via nätverk. (line vty)
3. Sätt ett lösenord för att gå till enable-mode.

## SSH

Ett bättre och säkrare alternativ till telnet är SSH. Med SSH skapas en krypterad anslutning vilket gör det svårare för en angripare att avlyssna. Vi måste dessutom ansluta med användarnamn och lösenord till enheten.

För att konfigurera enheten för SSH så ska följande steg utföras:

1. Sätt IP-adress på ett virtuellt VLAN-interface.
2.  Skapa användare och lösenord.
3. Ställ in inlogg via nätverk (line vty) till att använda både användarnamn och lösenord. (login local)
4. Sätt ett lösenord (secret) för att nå enable-mode.
5. Skapa ett nyckelpar som ska användas för SSH. För att göra detta behöver vi utföra flera delsteg. Vi måste ge enheten ett namn i en DNS-zon (FQDN) med följande kommandon: *hostname namn* och *ip domain-name DNS-domän*.  När enheten har ett FQDN så kan vi skapa ett nyckelpar med *crypto key generate rsa*. (I skarpa miljöer så kan vi utnyttja riktiga certifikat i stället för självsignerade.)
6. Vi måste inte men det är bra från ett säkerhetsperspektiv att sätta SSH-versionen till 2 i stället för default 1.99. Detta görs med *ip ssh version 2*.
7. Om vi vill tillåta endast anslutning via SSH och inte via telnet så ställs detta in med: *transport input ssh* (Om man vill tillåta båda så gäller input all, medan om vi bara vill ha telnet input telnet.) Observera att för routrar så kan default vara input none vilket gör att man varken kan ansluta med SSH eller telnet.

## Loggning

Resultatet från kommandon ger ibland ett svar från enheten som listas i kommandoraden. Ett problem är att denna text har en förmåga att nästla sig in i nya kommandon vilket gör det svårläst. För att lösa detta kan vi antingen ta bort utmatningen helt med *no logging console*. Alternativt kan vi välja att utmatningen sker vid mer kontrollerade tillfällen med *logging synchronous*.

En annan sak som kan vara frustrerande är att om man skriver fel i namnet på ett kommando så verkar det som om enheten låser sig. Det som sker är att enheten tror att vi angett en nätverksplats och enheten kontaktar då DNS för att lösa IP-adressen. Detta kan vara bra om vi vill nå en nätverksenhet men oftast är det vi som råkat stava fel. För att stoppa enheten från att kolla med DNS så skriver man *no ip domain-lookup*.

Vi kan också ändra den tid av inaktivitet som krävs innan man blir automatiskt utloggad från enheten. Detta görs med kommandot *exec-timeout minuter sekunder*.


