# Pflegekräfte-Rentenwelle-Rechner — Spec

## Kontext

medicruiter ist ein 2020 gegründetes, international zertifiziertes Recruiting- und
Trainingsunternehmen mit Sitz in Düsseldorf, das Pflegefachkräfte aus Lateinamerika,
Asien und Osteuropa nach Deutschland vermittelt. Website: https://www.medicruiter.com/de

Gebaut werden soll ein interaktives Lead-Gen-Tool für die medicruiter-Website: ein
Rechner, der Kliniken und Pflegeeinrichtungen zeigt, wie groß ihre Personallücke durch
Renteneintritte in den nächsten Jahren wird — und wie viel schneller sich diese Lücke
über medicruiter schließen lässt als über klassische Recruiting-Wege.

## Auftrag

1. **Design-System der echten Seite extrahieren.** Lies https://www.medicruiter.com/de
   (und bei Bedarf verlinkte Unterseiten wie /de/nursing-school) und leite daraus das
   tatsächliche Design-System ab: Farbpalette, Typografie, Logo/Header-Aufbau,
   Button- und Card-Stile, Abstände, Tonalität der Texte.
2. **Referenz-Prototyp als funktionale Vorlage nutzen.** Im selben Repo liegt
   `medicruiter-rentenwelle-rechner.html` — ein voll funktionsfähiger Prototyp, der
   alle unten beschriebenen Features bereits korrekt implementiert (inkl. der
   Berechnungslogik, die mehrfach iteriert und gegen Modellfehler geprüft wurde).
   **Wichtig:** Die Optik dieses Prototyps (Farben `#5B4B8A`/`#1B6B57`/`#B08A2E`,
   Schriften Fraunces/IBM Plex) ist ein Platzhalter und soll NICHT übernommen werden.
   Die Logik, die Copy und die Struktur schon — bitte 1:1 in Funktion, aber neu
   gestylt nach Schritt 1.
3. **Technischen Rahmen der Zielseite berücksichtigen.** Vor der Umsetzung prüfen,
   mit welchem Stack medicruiter.com gebaut ist (statisch, CMS, Framework), und das
   Tool so integrieren, wie es zu diesem Stack passt (eigene Seite, einbettbare
   Sektion/Komponente o. Ä.). Kurz dokumentieren, wofür man sich entschieden hat.

## Feature-Anforderungen

### 1. Eingaben (Rechner)
- Pflegekräfte gesamt (Zahl, Default 250)
- Anteil 55 Jahre oder älter (Slider 0–50 %, Default 50 %)
- Planungshorizont (Auswahl 3 / 5 / 10 Jahre, Default 5 Jahre)

### 2. Kernergebnis
- Anzahl Pflegekräfte, die im gewählten Horizont voraussichtlich in Rente gehen
  (Formel siehe unten), plus Zieljahr (aktuelles Jahr + Horizont).
- Visualisierung als Punkteraster (z. B. 10×5 = 50 Punkte), anteilig eingefärbt
  entsprechend dem Anteil der Belegschaft, der in Rente geht.

### 3. Aufschlüsselung Plus vs. reguläres Programm
Direkt unter dem Kernergebnis: wie viele der insgesamt betroffenen Pflegekräfte über
medicruiter Plus (schnell, teurer) und wie viele über das reguläre Programm gedeckt
werden sollten (Formel siehe unten).

### 4. Ablauf-Diagramm: Personalbestand über die Zeit
Liniendiagramm, X-Achse Monate (in Jahren beschriftet), Y-Achse Pflegekräfte-Bestand,
startend beim eingegebenen Gesamtbestand. Fünf Kurven:

1. **Gar nix tun** — Bestand sinkt mit den Renteneintritten, erholt sich nie von selbst.
2. **Eigene Ausbildung** — Vorlaufzeit 36 Monate.
3. **Klassische Personalvermittlung** — Vorlaufzeit 15 Monate.
4. **medicruiter** (regulär) — Vorlaufzeit 7,5 Monate.
5. **medicruiter Plus** — Hybrid: deckt alles bis zur Reife der regulären Pipeline
   (7,5 Monate) mit eigener kurzer Vorlaufzeit (1,5 Monate), danach übernimmt das
   reguläre Programm neue Renteneintritte ohne erneute Verzögerung.

Referenzlinie (gepunktet) beim Ausgangsbestand. Eine betonte vertikale Linie markiert
Jahr 3 (Monat 36) als Vergleichspunkt.

### 5. Jahr-3-Vergleich
An der Jahr-3-Marke: Punkte + Delta-Beschriftung (zum Ausgangsbestand) für "Gar nix
tun", "Klassische Personalvermittlung", "medicruiter" und "medicruiter Plus".
Beschriftungen dürfen sich nicht überlappen (bei Kollision vertikal versetzen).

### 6. Kennzahlen-Karten (4)
- **Gar nix tun** — Delta nach 3 Jahren, Hinweis "erholt sich nicht von selbst"
- **Klassische Personalvermittlung** — Delta nach 3 Jahren, vollständig aufgeholt nach
  X Jahren/Monaten (Formel: Horizont + 15 Monate)
- **medicruiter** — Delta nach 3 Jahren, vollständig aufgeholt nach X (Horizont + 7,5
  Monate)
- **medicruiter Plus** (hervorgehoben) — Delta nach 3 Jahren (rechnerisch immer 0,
  sobald Horizont ≥ 3 Jahre), maximale Delle während der Anlaufphase, komplett
  aufgeholt nach 9 Monaten (konstant, unabhängig von den Eingaben)

### 7. Vertrauens-Elemente
- Gegründet 2020 in Deutschland
- Talentpool: 3 Kontinente (Lateinamerika, Asien, Osteuropa)
- 6–9 Monate bis zur Festanstellung
- Zertifiziert durch die Gütegemeinschaft Anwerbung und Vermittlung von
  Pflegefachkräften aus dem Ausland e. V.

### 8. Lead-Formular
Felder: Name, E-Mail, Klinik/Einrichtung, Telefon (optional). Bei Absenden aktuell
nur clientseitige Erfolgsmeldung — **Anbindung an ein echtes CRM- oder
E-Mail-Backend ist offen** und muss vor Live-Schaltung ergänzt werden (im Prototyp
mit `TODO` markiert).

## Mathematisches Modell

Alle Zeiten in Monaten. `total` = Eingabe Pflegekräfte gesamt, `R` = Anzahl
Renteneintritte im Horizont, `H` = Horizont in Monaten.

```js
// Anteil, der im gewählten Horizont überhaupt in Rente geht (Modellannahme:
// die Gruppe 55+ verteilt sich gleichmäßig auf die nächsten 10 Jahre)
const fraction = Math.min(1, horizonYears / 10);
const R = Math.round(total * (sharePct / 100) * fraction);
const H = horizonYears * 12;

// Vorlaufzeiten (Monate)
const LAG_AUSBILDUNG = 36;
const LAG_KLASSISCH = 15;
const LAG_MEDICRUITER = 7.5;
const LAG_PLUS = 1.5;     // 4–8 Wochen, Mittelwert
const YEAR3_MONTHS = 36;

function clamp01(v) { return Math.max(0, Math.min(1, v)); }
function clampRange(v, lo, hi) { return Math.max(lo, Math.min(hi, v)); }

// Reaktives Modell: eine Stelle wird erst nachbesetzt, sobald sie frei wird, und
// braucht dann die Vorlaufzeit des jeweiligen Wegs. "Gar nix tun" = lag = Infinity.
function bestandAt(t, total, r, h, lag) {
  if (h <= 0) return total;
  const retFrac = clamp01(t / h);
  const hireFrac = isFinite(lag) ? clamp01((t - lag) / h) : 0;
  return total - r * retFrac + r * hireFrac;
}

// Hybrid für medicruiter Plus: Plus deckt ALLE Renteneintritte bis zum Reifepunkt
// der regulären Pipeline (lagSlow) mit ihrer eigenen kurzen Vorlaufzeit (lagPlus).
// Ab dem Reifepunkt deckt das reguläre Programm neue Renteneintritte SOFORT ab
// (keine erneute Verzögerung) — nur der Rückstand aus der Anlaufphase wird noch
// nachgezogen, bis er nach (lagSlow + lagPlus) Monaten vollständig getilgt ist.
function bestandAtPlus(t, total, r, h, lagPlus, lagSlow) {
  if (h <= 0) return total;
  const retFrac = clamp01(t / h);
  const earlyBound = Math.min(lagSlow, h);
  const earlyHires = clampRange(t - lagPlus, 0, earlyBound);
  const lateWidth = Math.max(0, h - earlyBound);
  const lateHires = lateWidth > 0 ? clampRange(t - earlyBound, 0, lateWidth) : 0;
  return total - r * retFrac + (r / h) * (earlyHires + lateHires);
}

// Aufhol-Zeitpunkt (wann Bestand wieder auf Ausgangsniveau)
function closureMonths(H, lag) { return H + lag; } // für Klassisch/medicruiter regulär
// Für Plus ist das immer LAG_MEDICRUITER + LAG_PLUS = 9 Monate, unabhängig von H.

// Aufschlüsselung Plus- vs. regulär-Bedarf
const earlyBound = Math.min(LAG_MEDICRUITER, H);
const plusNeeded = Math.round(R * earlyBound / H);
const regularNeeded = R - plusNeeded;
```

Wichtige Eigenschaft, die beim Testen geprüft werden sollte: `bestandAtPlus` liefert
für `t >= LAG_MEDICRUITER + LAG_PLUS` (=9) und `t <= H` exakt `total` zurück — die
Lücke schließt sich vollständig und bleibt geschlossen, unabhängig von `total`, `R`
oder `H` (solange `H >= 9`, was bei allen drei Horizont-Optionen der Fall ist). Wenn
eine Neuimplementierung das nicht liefert, ist ein Modellfehler wahrscheinlicher als
eine gewollte Abweichung — im Zweifel gegen die Formel oben prüfen, nicht dagegen
optimieren.

## Nicht-Ziele

- Keine echte Anbindung an Personaldaten einer konkreten Klinik (Rechner arbeitet nur
  mit manuell eingegebenen Werten).
- Keine Zahlungs- oder Vertragsabwicklung.
- Backend für das Lead-Formular ist bewusst offen gelassen (siehe oben).
