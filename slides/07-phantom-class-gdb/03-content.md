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

- Locally build OpenJDK, niet gedownload
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

### De backtrace ontleed

```text
#0 SystemDictionary::resolve_instance_class_or_null(...)
#1 JVM_FindLoadedClass / ClassLoader.loadClass(...)
#2 com.example.plugin.RuleScanner.scanAnnotations()
#3 com.example.app.Application.init()
```

- **JVM/GDB**: Onderschept de class-load aanvraag
- **Java / JNI grens**: Verbindt de runtime met HotSpot
- **De dader (Java)**: De specifieke scanner die te vroeg zocht

Notes:

- JNI Java Native Interface
- Zodra het breakpoint raakt, toont `bt` in GDB de volledige gemengde stack
- We zien precies waar de native JVM-laag overgaat in de aanroepende Java-code
- We hoeven niet te gokken: frame #2 wijst de boosdoener direct aan

---

### De ontknoping

De dader: een overijverige annotation scanner.

<!-- .slide: class="is-fancy2" -->

Notes:

- Het bleek geen obscure static initializer of runtime reflectie te zijn
- Een scanner inspecteerde annotaties op plugin-interfaces en forceerde daarmee een vroege class load
- Het mysterie is opgelost door het moment van beslissen te bevriezen, niet door achteraf logs te analyseren

---

### Wat we hiervan leren

- **Onderscheppen > Achteraf loggen**: Bevries het moment van de beslissing
- **Slowdebug maakt transparant**: Geeft HotSpot zijn debug symbols terug
- **Native tools voor Java-raadsels**: Als JVM-tools blind zijn, kijk onder de motorkap

Notes:

- Wanneer Java-level tools blind zijn omdat het gedrag in de JVM-machinerie zit, brengt native debugging uitkomst
- GDB met een slowdebug JVM maakt het onzichtbare zichtbaar
- Soms moet je één abstractielaag dieper durven kijken om het raadsel bovenin op te lossen
