---
layout: post
title: Router on a stick - Cisco
date: 2017-01-24 21:01:00
description: Routa mellan VLAN via ett interface 
tags: nätverksteknik Cisco
categories: nätverksteknik

---

## Exempel

Om vi har en miljö som arbetar med VLAN så behöver vi en router för att kunna kommunicera mellan VLAN. Principen är att för varje VLAN på lager 2 så behöver vi ett IP-nät på lager 3. För varje IP-nät så behöver en router ett interface. Detta skulle innebära att vi behöver väldigt många interface på en router. I praktiken är det inte så utan vi klarar oss med ett interface. För att se hur detta konfigureras så använder vi följande exempel.

![Principskiss router an a stick](/assets/img/router_on_a_stick.png)

Vi har en switch med två VLAN 10 och 20. Dessutom finns det interface på switchen som är konfigurerade för trunking. (Taggar Ethernet-paket med rätt VLAN-id.) Genom att ansluta ett trunkinginterface till routern, samt att därefter konfigurera routern korrekt så kan vi använda vår router för att skicka paket mellan VLAN 10 och VLAN 20.

Här förutsätter jag att switchen redan är konfigurerad. Hur detta görs har behandlats i ett tidigare inlägg, [Cisco Catalyst - Konfigurera VLAN.]({% link _posts/2017-01-19-Cisco-Catalyst_konfigurera-VLAN.md %}) 

De steg vi behöver göra på routern är följande.

- Gå till interfacet. (enable -> configure tetrminal -> interface Gi0/0)
- Starta interface. no shutdown
- Skapa ett subinterface för VLAN 10. interface Gi0/0.10 (.10 anger subinterface. Detta behöver inte vara samma som VLAN-id, men varför ändra?)
- Tala om att detta subinterface ska ta emot Ethernet-ramar som taggats för VLAN 10. encapsulation dot1q 10
- Sätt ip-adress på subinterfacet. ip address 192.168.10.1 255.255.255.0
- Repetera för VLAN 20.
    - interface Gi0/0.20
    - encapsulation dot1q 20
    - ip address 192.168.20.1 255.255.255.0

Detta är faktiskt allt som behöver för att kunna routa mellan våra VLAN. För att komma åt resten av nätet så gäller precis som vanligt att vi måste skapa vägar till övriga nät.


