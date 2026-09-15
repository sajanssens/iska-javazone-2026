### Nieuwe strategie

> Stop de JVM op het exacte moment dat hij besluit de klasse te laden.

Notes:

- Achteraf onderzoeken werkte niet
- Een breakpoint bevriest de executie op het moment dat de beslissing wordt genomen, niet erna
- In plaats van het resultaat onderzoeken, het moment zelf onderscheppen

---

### Onder de motorkap

:::mermaid
graph TD
Applicatie --> Spring --> Runtime["Java Runtime"] --> HotSpot["HotSpot JVM"] --> OS["Besturingssysteem"]
:::

<!-- .slide: class="is-empty" -->

Notes:

- Een gewone Java-debugger ziet alleen Java-frames, hij stopt waar onze code stopt
- Hij ziet niet het moment waarop de interne JVM-machinerie besluit dat een klasse nodig is
- Om dat te zien moeten we naar binnen in HotSpot zelf, het C++-programma dat de JVM is
- Op dit punt debuggen we niet meer onze applicatie, we debuggen de JVM

---

### Wat is een slowdebug JVM?

- Gebouwd vanaf de bron, niet gedownload
- Debug symbols aan, optimalisaties uit
- Ruilt snelheid in voor inzicht

Notes:

- Een normale "product" JVM-build is gestript en geoptimaliseerd, functienamen zijn weg, variabelen zijn weggeoptimaliseerd
- Een slowdebug build houdt dat allemaal in stand, zodat een native debugger er iets van kan maken
- Trager om te draaien, maar leesbaar, en dat is precies het punt hier

---

### Wat is GDB?

**GDB — de GNU Project Debugger**

Een native, C-level debugger. Geen Java-debugger.

Notes:

- HotSpot is zelf grotendeels geschreven in C++
- Omdat de JVM een native programma is, kan een native debugger er zo naar binnen kijken
- De meeste Java-developers komen hier nooit aan, dit is normaal C/C++/Rust-terrein

---

### De hinderlaag

<div class="kc-columns kc-gap2">

<div>

**Aanhaken**

```
gdb java
```

</div>

<div>

**De val zetten**

```
break SystemDictionary::resolve_instance_class_or_null
```

</div>

</div>

Notes:

- `SystemDictionary::resolve_instance_class_or_null` is de receptie van de JVM voor class loading
- Elke verdachte van een paar slides terug, reflectie, Spring, static init, komt via precies deze functie binnen
- Draai de applicatie, en wacht, de JVM belt zelf naar GDB op het moment dat hij besluit een klasse te laden

---

### De backtrace lezen

- Onderaan: de trigger
- Klim omhoog: het Java-frame dat naar binnen belde
- Blijf klimmen: naar de echte oorzaak

Notes:

- `bt` in GDB print de call stack op het breakpoint
- Die lezen legt precies vast wie wie aanriep, in volgorde
- Dit is wat loggen en stack traces niet konden geven: de echte keten, vastgelegd op het moment van de misdaad

---

### Vanaf hier

De backtrace noemt de verdachte.

<!-- .slide: class="is-fancy1" -->

Notes:

- Hier pakt de live demo het over: breakpoint raken, backtrace printen, terugklimmen naar de echte aanroeper
- Het mysterie eindigt niet met nog meer logging, maar met de JVM die zichzelf op heterdaad betrapt
