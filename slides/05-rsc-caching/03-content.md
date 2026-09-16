### Streaming & Suspense

- <!-- .element: class="fragment" -->
  HTTP `Transfer-Encoding: chunked` streamt direct de shell
- <!-- .element: class="fragment" -->
  React `<Suspense>` boundaries isoleren trage async server queries
- <!-- .element: class="fragment" -->
  Asynchrone RSC payload chunks worden inline geschreven en gestreamd
- <!-- .element: class="fragment" -->
  Voorkomt moeten wachten tot alles is ingeladen

Notes:

- In plaats van te wachten op de traagste backend query (`Promise.all`), streamt de server direct de statische layout
- `loading.tsx` vertaalt zich automatisch naar een Suspense boundary
- Zodra trage data resolved, stuurt de server een extra script/chunk die de fallback DOM naadloos vervangt zonder client-side rerender

---

### De 4 Caching Lagen

<div class="kc-grid kc-gap2">

**1. Request Memoization**</br>
React per-render deduplication
<!-- .element: class="pos-color1 fragment" -->

**2. Data Cache**</br>
Persistent across requests & builds
<!-- .element: class="pos-color2 fragment" -->

**3. Full Route Cache**</br>
Static HTML & RSC Payload
<!-- .element: class="pos-color3 fragment" -->

**4. Router Cache**</br>
In-memory Client-side Segment Cache
<!-- .element: class="pos-color4 fragment" -->

</div>

Notes:

- Next.js implementeert een meerlaagse caching-architectuur: van in-memory call deduplicatie tot globale persistente stores
- Twee lagen draaien op server runtime (Request Memoization & Data Cache)
- Eén laag zit op build/CDN niveau (Full Route Cache)
- Eén laag draait in client-side JavaScript memory (Router Cache)

---

### 1. Request Memoization

- <!-- .element: class="fragment" -->
  **Scope**: Eén server-side render lifecycle (per HTTP request)
- <!-- .element: class="fragment" -->
  **Locatie**: React Runtime Memory (`React.cache`)
- <!-- .element: class="fragment" -->
  Dedupliceert identieke `GET fetch()` URLs en request parameters

Notes:

- Meerdere componenten in dezelfde tree kunnen dezelfde data fetchen zonder netwerkkosten te verdubbelen
- Werkt alleen binnen de React render pass; het geheugen wordt direct na de response afgewikkeld
- Niet-fetch operaties (zoals DB calls) kunnen handmatig gewrapped worden met `React.cache()`

---

### 2. Data Cache

- <!-- .element: class="fragment" -->
  **Scope**: Persistent over requests, sessies en deployments
- <!-- .element: class="fragment" -->
  **Locatie**: Server Filesystem / KV Store / Redis
- <!-- .element: class="fragment" -->
  `fetch(url, { next: { revalidate: 3600, tags: ['products'] } })`
- <!-- .element: class="fragment" -->
  Biedt Time-based & On-Demand cache invalidatie

Notes:

- Overleeft server restarts en deployments (mits geconfigureerd met een gedeelde cache adapter)
- Isolateert backend APIs en databases tegen redundante load
- `tags` maken gerichte cache purging mogelijk via tags zonder hele paths weg te gooien

---

### 3. Full Route Cache

- <!-- .element: class="fragment" -->
  **Scope**: Persistent op de server / CDN
- <!-- .element: class="fragment" -->
  **Artifacts**: Statische HTML + gecompileerde RSC Payload
- <!-- .element: class="fragment" -->
  Rendert routes op build-time (SSG) of bij revalidatie (ISR)

Notes:

- Zodra een pagina statisch is, serveert Next.js de kant-en-klare RSC payload en HTML direct zonder Node.js compute
- Als een component dynamische context (`cookies()`, `headers()`) leest, valt Next.js automatisch terug naar dynamic rendering
- Maakt extreme Core Web Vitals (FCP, LCP) mogelijk

---

### 4. Router Cache

- <!-- .element: class="fragment" -->
  **Scope**: Client browser sessie (In-memory)
- <!-- .element: class="fragment" -->
  **Locatie**: Browser memory per route segment
- <!-- .element: class="fragment" -->
  Houdt eerder bezochte en via `<Link prefetch>` opgehaalde RSC payloads vast
- <!-- .element: class="fragment" -->
  Direct vooruit/achteruit navigatie zonder netwerkkosten

Notes:

- Slaat RSC Payloads op segment-niveau op in het geheugen van de browser
- Heeft een configurable stale time (bijv. 30s voor dynamische routes, 5 min voor statische routes)
- Zorgt voor de navigatiesnelheid van een SPA, maar met behoud van server-side data control
