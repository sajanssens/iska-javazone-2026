### Instant aanvoelen

- Navigatie toont meteen een skeleton
- De data stroomt binnen zodra hij klaar is
- Streaming UI, geen witte flits, geen wachten op alles tegelijk

Notes:

- Dit is Suspense en `loading.tsx` in actie
- De pagina hoeft niet te wachten op de traagste data om iets te tonen
- Het aanvoelen van snelheid komt niet van sneller laden, maar van eerder tonen

---

### Vier lagen cache

<div class="kc-grid kc-gap2">

**Request memoization**
<!-- .element: class="pos-color1" -->

**Data Cache**
<!-- .element: class="pos-color2" -->

**Full Route Cache**
<!-- .element: class="pos-color3" -->

**Router Cache**
<!-- .element: class="pos-color4" -->

</div>

Notes:

- Next.js cachet niet op één plek, maar op vier, elk voor een ander probleem
- Eén render pass, meerdere requests, meerdere gebruikers, en de browser zelf
- De rest van deze slides gaat er stuk voor stuk doorheen

---

### Request memoization

- Dezelfde `fetch` twee keer aangeroepen? Eén keer over de lijn
- Geldig binnen één render pass
- Automatisch, geen configuratie nodig

Notes:

- Verschillende components kunnen onafhankelijk dezelfde data opvragen
- React dedupliceert dat binnen dezelfde render, geen N keer hetzelfde endpoint raken
- Zodra de render klaar is, is dit geheugen weer weg

---

### Data Cache

- Fetch-resultaten blijven hangen op de server
- Overleeft requests, en zelfs deployments
- Jij bepaalt de houdbaarheid: `revalidate`, `tags`

Notes:

- Dit is de laag die "vers genoeg" configureerbaar maakt, van altijd opnieuw tot bijna nooit
- `tags` laat je straks gericht ongeldig maken, niet de hele cache legen
- Dit is server-side en gedeeld tussen gebruikers, niet per sessie

---

### Full Route Cache

- De gerenderde pagina zelf, niet alleen de onderliggende data
- Klaar voor gebruik, nog voordat er een request binnenkomt
- Blijft geldig tot iets hem ongeldig maakt

Notes:

- Statische routes worden vooraf gerenderd tot RSC payload plus HTML
- Een nieuwe request kost dan geen render meer, alleen het al klaarliggende resultaat serveren
- Dit is waar de winst in Core Web Vitals grotendeels vandaan komt

---

### Router Cache

- Leeft in de browser, niet op de server
- Bezochte en geprefetchte routes staan al klaar in het geheugen
- Terug- en vooruitknop: geen netwerk nodig

Notes:

- Dit is dezelfde soort snelheid als een SPA, maar zonder dat jij een client cache beheert
- Prefetching vult 'm al voordat je klikt
- Dit is de enige laag van de vier die client-side zit
