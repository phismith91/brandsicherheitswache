# Brandsicherheitswache – Feature-Ideen

**Erstellt:** 2026-03-24
**Kontext:** Kryptographisch sichere Zufallsziehung fur Feuerwehr-Dienste
**Basis:** Audit v2 (Score 88/100) + Analyse des bestehenden Funktionsumfangs

---

## Prioritat 1 – Hoher Mehrwert, kleiner Aufwand

### 1. Teilnehmer-Liste speichern (localStorage)
**Was:** Namen und Veranstaltungsname werden beim Verlassen der Seite automatisch gespeichert
und beim nachsten Offnen wiederhergestellt.
**Warum:** Aktuell gehen alle Eingaben beim Reload verloren. Bei wiederholten Veranstaltungen
(z.B. jedes Jahr Silvester) muss die Liste neu eingegeben werden.
**Wie:** `localStorage.setItem('bsw_names', ...)` beim Input-Event; Restore on load.
**Aufwand:** ~30 Minuten

### 2. Ausgeschlossene Personen ("Bereits gezogen")
**Was:** Einmal gezogene Personen werden aus der Teilnehmerliste fur nachfolgende Ziehungen
automatisch ausgeschlossen. Toggle: "Ohne Wiederholung" aktivieren.
**Warum:** Bei mehreren Ziehungen in einer Saison (z.B. mehrere Veranstaltungen) sollen
Personen, die zuletzt dran waren, nicht erneut bevorzugt werden.
**Wie:** `Set` der bereits gezogenen Namen; wird zusammen mit `localStorage` persistiert.
**Aufwand:** ~1 Stunde

### 3. Mindest-Abstand-Regel ("Nicht zweimal hintereinander")
**Was:** Personen, die bei der letzten Ziehung ausgewahlt wurden, werden in dieser Ziehung
mit einer niedrigeren Wahrscheinlichkeit oder gar nicht berucksichtigt.
**Warum:** Fairness-Garant. Typisch fur Feuerwehr-Dienstplane.
**Wie:** Separate Liste "Letzte Ziehung" speichern; Flag in der Validierung nutzen.
**Aufwand:** ~1 Stunde

### 4. PWA / Offline-Fahigkeit
**Was:** `manifest.json` + Service Worker → App kann auf Tablet-Startbildschirm installiert werden,
funktioniert ohne Internetverbindung (Fonts lokal cachen).
**Warum:** Feuerwehr-Einsatz findet haufig in Hallen ohne stabiles WLAN statt.
**Wie:** Workbox oder minimal eigener SW fur Fonts + JS-Libs.
**Aufwand:** ~2 Stunden

### 5. Druck-Ansicht (Druckbarer Aushang)
**Was:** `window.print()`-optimiertes Layout: einseitiger Aushang mit Gewinnern, Datum und
QR-Code zur Verifizierung (Entropie-Hash als QR).
**Warum:** Alternativ zum PDF-Export, direkt aus dem Browser druckbar ohne jsPDF.
**Wie:** `@media print` CSS + dediziertes Print-Layout.
**Aufwand:** ~1.5 Stunden

---

## Prioritat 2 – Mittlerer Mehrwert, mittlerer Aufwand

### 6. Ziehungshistorie
**Was:** Liste der letzten N Ziehungen (Datum, Veranstaltung, Gewinner) lokal gespeichert.
Erreichbar uber einen "Verlauf"-Button.
**Warum:** Nachvollziehbarkeit uber mehrere Veranstaltungen hinweg. Prufer konnen alte
Ziehungen einsehen.
**Wie:** `localStorage` mit Array von Ziehungs-Objekten; Anzeige als Tabelle.
**Aufwand:** ~3 Stunden

### 7. Gewichtete Ziehung (Bonus-Punkte-System)
**Was:** Personen, die lange keinen Dienst hatten, erhalten ein hoheres Gewicht.
Interface: Neben jedem Namen ein Gewichtungs-Wert (z.B. Anzahl verpasster Dienste).
**Warum:** Fairness bei unterschiedlicher Verfugbarkeit. Manche Personen sind seltener einplanbar.
**Wie:** Gewichtetes Fisher-Yates (Alias-Methode oder einfaches Wiederholungs-Sampling).
**Aufwand:** ~4 Stunden

### 8. Import aus Datei (CSV/TXT)
**Was:** Teilnehmerliste per Drag & Drop oder Dateiauswahl aus einer CSV oder TXT importieren.
**Warum:** Bei großen Wehren wird die Mitgliederliste oft extern gepflegt.
**Wie:** `FileReader` API + CSV-Parser (kein Dependency notig).
**Aufwand:** ~2 Stunden

### 9. Mehrstufige Ziehung (Haupt- und Ersatz)
**Was:** Ziehe X Hauptdienste + Y Reserve-Personen in einer Runde. Ergebnis zeigt beide Gruppen
deutlich getrennt.
**Warum:** Praxis: 2 Hauptwachen + 1 Reserve ist ein typischer Modus.
**Wie:** Zwei separate `count`-Eingaben; Shuffle einmal, dann splitten.
**Aufwand:** ~2 Stunden

### 10. Ergebnis teilen (URL-Hash oder QR-Code)
**Was:** Ziehungsergebnis + Entropie-Proof als URL-Hash kodieren oder als QR-Code anzeigen.
Jemand mit dem QR-Code kann das Ergebnis auf derselben Seite nachvollziehen.
**Warum:** Transparenz fur abwesende Teilnehmer; leicht teilbar per WhatsApp.
**Wie:** Ergebnis-Objekt → JSON → Base64 → URL-Hash; QR via Library (qrcode.js, ~5KB).
**Aufwand:** ~3 Stunden

---

## Prioritat 3 – Langer Aufwand / Nice-to-have

### 11. Dienstplan-Ansicht
**Was:** Nach mehreren Ziehungen wird ein vollstandiger Dienstplan fur die Saison angezeigt:
wer war wann dran, wer noch nicht, wer muss als nachstes.
**Warum:** Uberblick statt Einzelziehungen; Planungshilfe fur Fuhrungskrafte.
**Wie:** Kombination aus Ziehungshistorie + Visualisierung (einfache Tabelle).
**Aufwand:** ~1 Tag

### 12. Gruppen-Verwaltung
**Was:** Teilnehmer konnen in Gruppen eingeteilt werden (z.B. "Zugfuhrer", "Truppfuhrer",
"Truppmann"). Ziehung kann gruppenspezifisch erfolgen (1 aus Gruppe A, 2 aus Gruppe B).
**Warum:** Komplexere Anforderungen an Dienstplanen (z.B. immer ein Fuhrungskraft dabei).
**Wie:** Gruppen-Syntax in der Textarea (`[Gruppe] Name`) oder separate Eingabemasken.
**Aufwand:** ~1 Tag

### 13. Animations-Modi
**Was:** Alternativer Ziehungsmodus mit anderen Animationen:
- "Stille Ziehung" (sofort, kein Drum-Roll) – fur ernste Situationen
- "Feuerwerk-Modus" – Konfetti bei Ergebnis
- "Countdown-Modus" – 3..2..1 vor der Enthullung
**Warum:** Verschiedene Veranstaltungstypen brauchen verschiedene Atmospharen.
**Aufwand:** ~4 Stunden

### 14. Mehrsprachigkeit (DE/EN)
**Was:** Alle UI-Texte in DE und EN verfugbar; automatische Spracherkennung via `navigator.language`.
**Warum:** Internationale Veranstaltungen, Partnerorganisationen.
**Wie:** Einfaches i18n-Objekt in JS; kein Framework notig.
**Aufwand:** ~3 Stunden

### 15. Barrierefreier Ton-Feedback (optional)
**Was:** Opt-in Ton-Signal wenn der Gewinner enthullt wird (Web Audio API, kein Asset notig).
Kurzes Glockchen oder Sirenen-Ton.
**Warum:** Zuganglichkeit fur sehbehinderte Nutzer; Aufmerksamkeit im Raum ohne auf den Screen zu schauen.
**Wie:** Web Audio API `OscillatorNode` oder kurze AudioBuffer.
**Aufwand:** ~2 Stunden

---

## Technische Verbesserungen (kein Feature, aber wertvoll)

| Verbesserung | Aufwand | Benefit |
|--------------|---------|---------|
| Screen-Reader Announcer (`role="status"` immer im DOM) | 15 min | Accessibility fix aus Audit v2 |
| `easeOutQuart` statt `easeOutCubic` | 5 min | Bessere Drum-Roll-Abbremsung |
| `.msg.warn` Token-Fix (`--amber-warn`) | 5 min | Theming-Konsistenz |
| `outline: none` → `:focus-visible` auf Inputs | 15 min | WCAG-Konsistenz |
| Leeres `label` → `aria-hidden` | 5 min | Accessibility |

---

## Priorisierungs-Empfehlung

Fur den nachsten Sprint empfohlen (groste Wirkung, kleiner Aufwand):

1. **Technische Fixes aus Audit v2** (~40 Minuten) – Score → 93/100
2. **Teilnehmer-Liste speichern** (localStorage) – grostes UX-Delta
3. **Ausgeschlossene Personen** – unmittelbar nutzlicher fur Feuerwehr-Praxis
4. **PWA-Manifest** – fur Tablet-Einsatz vor Ort

**Feature mit hochstem strategischem Wert:** Ziehungshistorie + Mindest-Abstand-Regel zusammen
implementieren – das macht das Tool von einer "einmaligen Spielerei" zu einem echten Planungstool
fur den jahrelangen Feuerwehr-Betrieb.

---

**Erstellt von:** Claude Code – Impeccable Audit Skill + Superpowers
**Datum:** 2026-03-24
