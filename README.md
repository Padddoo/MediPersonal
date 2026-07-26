# medicruiter – Pflegekräfte-Rentenwelle-Rechner

Interaktives Lead-Gen-Tool für die medicruiter-Website: Kliniken und
Pflegeeinrichtungen sehen, wie groß ihre Personallücke durch Renteneintritte in den
nächsten Jahren wird – und wie viel schneller sich diese Lücke über medicruiter
schließen lässt als über klassische Recruiting-Wege.

Deliverable: **`index.html`** – eine vollständig eigenständige, statische Seite
(HTML/CSS/JS, keine Build-Schritte, eine Datei).

## Umsetzung des Auftrags

### 1. Design-System der echten Seite (extrahiert)

Quelle: Screenshot der Startseite + gespeichertes HTML (`medicruiter.html`), da
`medicruiter.de` aus der Build-Umgebung nicht direkt erreichbar war.

- **Farben**
  - Primär-Navy `#1D426E`, tiefes Navy `#16324F`
  - Brand-Blau (Akzentlinien, aktive Elemente) `#1E86C7`
  - Gold/Senf (CTA-Buttons, Highlights) `#E7B24A`, dunkles Gold für Text `#B98220`
  - Heller Blau-Tint (Hero/Flächen) `#E8F1FA`, Seitenhintergrund `#F5F8FC`
  - Dringlichkeit/Problem: Rot `#C0392B`
- **Typografie**
  - Headlines: **Baloo 2** (warme, runde Sans – entspricht dem Hero-Font-Charakter)
  - Body/UI: **Inter**
  - Logo-Wortmarke „medicruiter": **Zilla Slab** (fett, kursiv – Slab-Serif)
- **Komponenten**: Pill-Buttons in Gold mit Navy-Text, weich gerundete Cards mit
  dezentem Schatten, goldene Eyebrow mit Unterstrich, Navy-Header mit Logo links und
  Gold-CTA rechts.
- **Tonalität**: sachlich, seriös, an Entscheider in Kliniken/Pflege gerichtet.

Die Platzhalter-Optik des Referenz-Prototyps (`#5B4B8A`/`#1B6B57`/`#B08A2E`,
Fraunces/IBM Plex) wurde bewusst **nicht** übernommen.

### 2. Referenz-Prototyp als funktionale Vorlage

`medicruiter-rentenwelle-rechner_5.html` liefert die geprüfte Berechnungslogik, die
Copy und die Struktur. Diese wurden **1:1 in Funktion und Text** übernommen und
komplett nach dem Design-System aus Schritt 1 neu gestylt. Die Kern-Eigenschaft des
Modells (`bestandAtPlus` liefert für `t ∈ [9, H]` exakt `total`) wurde vor der
Umsetzung verifiziert.

### 3. Technischer Rahmen / Integration

Die Zielseite `medicruiter.de` läuft auf **WordPress + Elementor + WPML** (custom
Theme `medicruiter-theme`). Für diesen Stack ist das Tool als **eigenständige,
statische Single-File-Seite** umgesetzt:

- Als eigene Landingpage deploybar (z. B. Vercel – siehe unten).
- In WordPress/Elementor per **HTML-Widget** oder **iframe** einbettbar, ohne
  Theme- oder Plugin-Konflikte, da keine externen Abhängigkeiten außer Google Fonts
  bestehen.

## Offener Punkt vor Live-Schaltung

Das Lead-Formular zeigt aktuell nur eine clientseitige Erfolgsmeldung. Die Anbindung
an ein CRM-/E-Mail-Backend ist im Code mit `TODO` markiert und muss vor dem
Live-Gang ergänzt werden (z. B. Webhook, HubSpot, eigenes Formular-Handling).

## Deployment (Vercel)

Statisches Projekt ohne Framework:

1. Repo in Vercel importieren.
2. Framework Preset: **Other** (kein Build-Command / kein Output-Dir nötig).
3. `index.html` wird direkt aus dem Root ausgeliefert.
