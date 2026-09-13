### Gecoördineerde updates

- Een Server Action wijzigt data
- `revalidatePath` / `revalidateTag` ruimt gericht op
- Server cache én browser, in één keer in sync

Notes:

- Dit vervangt het handmatig bijhouden van "wat moet nu opnieuw ophalen" uit de SPA-wereld
- Eén regel in de Server Action raakt Data Cache, Full Route Cache en Router Cache tegelijk
- Geen aparte invalidatielaag die jij zelf moet optuigen en onderhouden

---

### Het resultaat

- Snel aanvoelen, ook als de app groeit
- Verse data, zonder het zelf te orkestreren
- Sterke Core Web Vitals, structureel, niet toevallig

<!-- .slide: class="is-fancy1" -->

Notes:

- Dit zijn precies de punten uit de opening: instant UX, streaming, verse data, gecoördineerde updates
- Het verschil is dat je er als developer geen datalaag voor hoeft te bouwen
- Terug naar het startpunt: je schrijft nog steeds gewoon components, het framework regelt de rest
