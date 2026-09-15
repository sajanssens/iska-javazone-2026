### De rekening van de SPA

- Data layer naar de browser sturen
- Een eigen client cache bijhouden
- Loading states jongleren
- Mutaties met de hand coördineren

Notes:

- Zo bouwden we lang app-achtige UX op het web: een SPA
- Elk van deze punten is op zichzelf te doen
- Bij elkaar is het veel code, alleen om de UI snel en vers te laten aanvoelen
- En die kost groeit mee met de app, niet ernaast

---

### Een ander idee

> Elk stukje draait waar het hoort. Het mentale model blijft hetzelfde: components.

Notes:

- Sinds React Server Components is Next.js dit stap voor stap aan het uitbouwen
- Server-werk blijft op de server, client-werk in de browser
- Je schrijft in beide gevallen gewoon een component, geen twee werelden om te leren

---

### Wat is een Server Component?

- Rendert op de server, stuurt geen JS mee naar de browser
- `"use client"` schakelt over naar een Client Component
- Beide zijn, en blijven, gewoon components

Notes:

- Een Server Component levert alleen het resultaat, geen bundel, geen hydratie-kosten
- Zodra iets interactief moet zijn, een klik, een input, wordt het een Client Component
- Dat onderscheid zit in één regel bovenaan het bestand, niet in een aparte architectuur

---

### Eén boom, twee plekken

:::mermaid
graph TD
Page["Server Component"] --> List["Server Component"]
Page --> Client["Client Component - use client"]
List --> Item["Server Component"]
:::

<!-- .slide: class="is-empty" -->

Notes:

- Nog steeds één componentenboom, zoals je gewend bent
- Alleen: sommige takken renderen op de server, andere in de browser
- De grens ligt waar jij hem zet, niet waar het framework hem afdwingt
