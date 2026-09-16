### De nadelen van een SPA

- <!-- .element: class="fragment" -->
  Client-side data fetching & state management overhead
- <!-- .element: class="fragment" -->
  Overbodige data stores (Redux / React Query cache in browser memory)
- <!-- .element: class="fragment" -->
  Complexe cache synchronisatie

Notes:

- In traditionele SPA's duwen we de hele runtime, data fetching en caching naar de browser
- De browser downloadt eerst zware bundles voordat hij data kan fetchen: initiële TTFB en FCP lijden hieronder
- Elke client moet zijn eigen cachebeheer en loading states orkestreren
- Dit schaalt slecht qua client memory footprint en netwerkwaste

---

### Het RSC paradigma

> Compute kosten verplaatsen naar de server. UI via het web streamen.

Notes:

- React Server Components draaien uitsluitend op de Node.js / Edge runtime
- Directe toegang tot databases, backend microservices, caches en filesystems zonder netwerklatency naar de client
- Geen bundelkosten voor backend libraries of dependencies (zoals ORM's of markdown parsers)

---

### Wat is een Server Component?

- <!-- .element: class="fragment" -->
  Rendert naar **RSC Payload** (compacte JSON-achtige representatie van de virtuele DOM)
- <!-- .element: class="fragment" -->
  0 KB client-side JavaScript in de bundle
- <!-- .element: class="fragment" -->
  `"use client"` markeert de **serialisatiegrens** naar de browser

Notes:

- Een Server Component produceert geen klassieke HTML-only output, maar een gestreamde RSC Payload
- Client components worden tijdens SSR nog steeds op de server gepre-rendered, maar worden in de browser gehydrateerd
- `"use client"` is geen compiler directive voor 'alleen client', maar definieert de hydratatie- en bundelgrens