### Gecoördineerde Mutaties & Invalidatie

- <!-- .element: class="fragment" -->
  `revalidateTag(tag)` & `revalidatePath(path)` zorgen voor **on-demand invalidatie**
- <!-- .element: class="fragment" -->
  Invalideerd de server-side **Data Cache** en de **Full Route Cache**
- <!-- .element: class="fragment" -->
  Stuurt een nieuwe **RSC Payload** naar de browser
- <!-- .element: class="fragment" -->
  De **Router Cache** in de client wordt in dezelfde roundtrip gesynchroniseerd

Notes:

- Server Actions elimineren handmatige REST API route handlers en client-side fetch glue code
- Bij een mutatie hoef je niet handmatig client caches te patchen (`queryClient.setQueryData`)
- De server voert de mutatie uit, markeert tags als dirty, re-evalueert de benodigde subtrees en streamt de nieuwe RSC payload direct terug in de response

---

### Architecturale Conclusie

- <!-- .element: class="fragment" -->
  **Co-locatie van compute & data**: Geen netwerklatency tussen UI logic en databases
- <!-- .element: class="fragment" -->
  **Drastische bundle-reductie**: Zware business logic en dependencies blijven 0 KB client-side
- <!-- .element: class="fragment" -->
  **Multi-tier Caching**: Automatische request caching

<!-- .slide: class="is-fancy1" -->

Notes:

- Next.js en RSC lossen de fundamentele frictie van de klassieke SPA op zonder terug te vallen op 'domme' SSR
- Het framework regelt HTTP streaming, bundle split boundaries en multi-tier cache coherentie
- Voor de developer blijft het mentale model zuiver declaratief: React components en gestructureerde data flow
