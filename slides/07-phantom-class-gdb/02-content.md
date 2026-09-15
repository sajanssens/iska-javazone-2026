### Wie of wat laadde een bepaalde class?

Een `LinkageError` debuggen

Notes:

- LinkageError is een fout waarin een class een dependency heeft op een andere class that in de tussentijd dusdanig is aangepast nadat de class was gecompiled.
- Gebeurd vaak als er tegenstrijdige versies zijn van een class die meegenomen wordt door andere classes.

---

### De situatie

- Een platform waarop klanten eigen plugins draaien
- Elke plugin in zijn eigen classloader
- Spring bootstrapt alles bij het opstarten

Notes:

- Stel je een platform voor waarop klanten eigen "rules"-plugins kunnen draaien, bijvoorbeeld om kortingen te berekenen
- Elke plugin komt binnen als losse JAR, met een eigen classloader, zodat plugins elkaar niet in de weg zitten
- Spring scant en bootstrapt dit allemaal bij het opstarten
- Dus: veel classloaders, veel dynamisch geladen code, en veel plekken waar het mis kan gaan

---

### Wat is class loading?

- Classes beginnen als `.class` bytecode op de schijf
- De JVM laadt er pas één in het geheugen op het moment dat het echt nodig is

Notes:

- Niemand "importeert" handmatig tijdens runtime, de JVM bepaalt zelf het moment
- Die luiheid is expres, en meestal totaal geen probleem
- Met meerdere classloaders door elkaar, zoals bij onze plugins, wordt dat een stuk minder onschuldig
- Precies dat maakt dit soort bugs zo lastig te vinden, en de reden dat we straks met GDB naar binnen moeten

---

### De plaats delict

> **java.lang.LinkageError**
>
> **DiscountRule**
>
> Waarom werd deze klasse geladen?

Notes:

- LinkageError: de JVM vond deze klasse inconsistent met wat er al geladen was, bijvoorbeeld via de verkeerde classloader, of in twee versies tegelijk
- De error zelf was niet het interessante deel
- Deze specifieke klasse hoorde op dit pad helemaal niet geladen te worden, het was een plugin-only klasse
- De JVM besloot hem toch nodig te hebben, maar wie vroeg erom?

---

### Zoveel manieren naar binnen

- Direct gebruik (`new`)
- Reflectie
- Annotaties
- Proxies & method handles
- Opstarten van frameworks
- Static initialisatie

Notes:

- Elk van deze kan een class load triggeren
- Niets in de klasse zelf registreert welke van deze het deze keer deed
- Er is geen ingebouwde logregel die zegt "geladen door X"

---

### De verdachten

<div class="kc-grid kc-gap2">

- **Reflectie**

- **Spring**

- **Static initialisatie**

- **Iets anders**

</div>

Notes:

- Eerste vermoeden was reflectie, `Class.forName()`, een expliciete runtime lookup
- Daarna misschien Spring, bean scanning loopt het classpath af en laadt wat het tegenkomt
- Daarna misschien static initialisatie, het aanraken van een static field laadt de klasse al
- Of iets heel anders: agents, bytecode transformers, de JIT
- Eerlijk gezegd was er nog geen bewijs, alleen vermoedens

---

### Doodlopende wegen

- Logging
  - Te laat
- Stack traces
  - Te laat
- JVMTI tracing
  - Geen oorzaak

Notes:

- Loggen laat zien DAT het gebeurde, niet wie het startte
- Een stack trace bestaat pas zodra er al iets is misgegaan, tegen die tijd is de echte aanroeper al teruggekeerd en afgewikkeld
- JVMTI class-load hooks vuren betrouwbaar af, maar geven je het event, niet "waarom nu"
- Genoeg logging om een klein datacenter mee te verwarmen, nog steeds geen antwoord
