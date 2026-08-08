# Skarabäus – Website-Dokumentation

Dieses Dokument beschreibt die Website von **Skarabäus – Ska. Punk. Rock'n'Roll.**
Es erklärt, welche Dateien in die Hauptseite eingebunden werden, wie die
Tourneedaten aktualisiert werden und was die einzelnen Abschnitte der Seite
enthalten und bezwecken.

---

## 1. Überblick und Projektstruktur

Die Website ist eine **einzige HTML-Datei** (`index.html`). Es gibt kein
Build-System und kein Framework – die Seite funktioniert rein statisch. Alle
Styles (CSS) und das gesamte JavaScript (JS) sind direkt in `index.html`
eingebettet. Externe Ressourcen (Bilder, Logos, Daten, Schriften) liegen in
Unterordnern und werden von der HTML-Datei aus referenziert.

Die Ordnerstruktur im Überblick:

```
index.html              → Die komplette Website (Struktur, CSS, JS)
res/
  tour.data.js          → Tourneedaten (Terminliste)
  all.json              → Mitgliederdaten (Standalone-Kopie, wird nicht eingebunden)
  images/
    members/            → Porträtfotos und Video der Bandmitglieder
    bugs/               → „Skarabäus“-Maskottchen-Bilder
    events/             → Flyer/Plakate zu einzelnen Events
    Impressionen/       → Ordner für Impressionen (aktuell leer)
    mobil_*.JPG         → Hintergrundbilder für den Hero-Bereich (mobile)
    h_bridge*.JPG       → Weitere Hintergrundbilder
  logo/                 → Band-Logos (mit/ohne Hintergrund, Kurzlogo)
  symbols/              → Icons der Sozialen Medien (YouTube, WhatsApp, Facebook, Instagram)
  links_SM/             → Lesezeichen-Dateien zu den Sozialen Medien (nicht eingebunden)
font/                   → Lokale Schriftdateien (nicht aktiv eingebunden)
doc/                    → Downloads für Besucher (Presse-/Technik-ZIP) und diese Dokumentation
```

---

## 2. Wie die Dateien in die Hauptseite eingebunden werden

`index.html` bindet **keine** externen CSS- oder JS-Dateien ein, mit **einer**
Ausnahme. Alles wird über relative Pfade von der `index.html` aus geladen.

### 2.1 Schriften (Google Fonts)

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Bebas+Neue&family=Boogaloo&family=Inter:wght@400;600;700;900&display=swap" rel="stylesheet">
```

Die drei Schriften **Bebas Neue** (Überschriften), **Boogaloo** (Datum/„Boogaloo“-Optik)
und **Inter** (Fließtext) werden von den Google-Servern geladen. Der lokale
Ordner `font/` wird dabei aktuell **nicht** verwendet.

### 2.2 Tourneedaten (die einzige externe JS-Datei)

```html
<script src="res/tour.data.js"></script>
```

Diese Datei definiert eine globale Variable `tourData` mit allen Terminen
(siehe Kapitel 3). Sie muss **vor** dem Hauptskript im `<body>` geladen werden,
damit die Variable beim Rendern bereits verfügbar ist. Alle übrigen
Programmabläufe stehen als Inline-Skript direkt in `index.html`.

### 2.3 Bilder, Logos und Icons

Bilder werden direkt über `src`- bzw. CSS-`url()`-Angaben relativ zur
`index.html` eingebunden. Die wichtigsten Beispiele:

| Ressource | Pfad | Verwendung |
|---|---|---|
| Logo im Hero | `res/logo/Logo_no_background.png` | großes Bandlogo auf der Startseite |
| Favicon | `res/logo/Ska_short_no_background_big.png` | Browser-Tab-Icon |
| Hintergrund Hero (Desktop) | `res/images/mobil_stairs.JPG` | CSS-Hintergrund des Hero |
| Hintergründe Hero (mobil) | `res/images/mobil_stairs.JPG`, `res/images/mobil_green.JPG` | per JS zufällig ausgewählt |
| Mitgliederfotos | `res/images/members/*.JPG` | Karussell und Modal „Die Band“ |
| Maskottchen | `res/images/bugs/*.png` | zufällig im About-Bereich |
| Event-Flyer | `res/images/events/*.jpg` | Detail-Popup der Termine |
| Social-Media-Icons | `res/symbols/logo_*.png` | Header, Kontakt-Popup |

### 2.4 Downloads für Besucher

```html
<a href="doc/skarabaeus_press.zip" ...>Presse</a>
<a href="doc/skarabaeus_technik.zip" ...>Technik</a>
```

Im Abschnitt „Downloads“ verlinken zwei Karten auf ZIP-Archive im `doc/`-Ordner.

### 2.5 Was NICHT eingebunden wird

- **`res/all.json`** – enthält dieselben Mitgliederdaten wie das Inline-Array in
  `index.html`, wird aber nirgends geladen. Pflege der Mitglieder erfolgt daher
  direkt im JS-Array `members` in `index.html`.
- **`res/links_SM/`** – Windows-Lesezeichen-Dateien, nur lokal gedacht.
- **`font/`** – lokale Schriften, werden nicht referenziert.

> **Fazit:** Um ein Element der Seite zu ändern, bearbeitet man fast immer
> direkt `index.html` (Struktur, CSS, JS, Mitgliederdaten, Hörproben-Links,
> Kontaktdaten). Nur die Tourneedaten pflegt man in `res/tour.data.js`.

---

## 3. Wie die Tourneedaten aktualisiert werden

Alle Termine stehen in der Datei **`res/tour.data.js`**. Sie enthält eine
Variable `tourData`, die einen **CSV-artigen Text** mit Semikolon als Trenner
enthält:

```js
var tourData = `Date;Location;Info;Link;Flyer
22.08.2026;Bischofsheim;Chiller-Festival;;
29.08.2026;Höchstadt a. d. Aisch;Altstadtfest;https://www.hoechstadt.de/...;Altstadtfest_Hös.jpg
06.02.2027;Bamberg;Live Club;https://www.live-club.de/events;;`;
```

### 3.1 Spaltenbedeutung

| Spalte | Bedeutung | Pflicht |
|---|---|---|
| `Date` | Datum im Format **TT.MM.JJJJ** (deutsch) | ja |
| `Location` | Ort/Stadt | ja |
| `Info` | Veranstaltungsname (z. B. „Altstadtfest“) | ja |
| `Link` | URL zur Veranstaltung / zum Veranstalter | nein |
| `Flyer` | Dateiname eines Flyers in `res/images/events/` | nein |

### 3.2 So pflegt man einen Termin

1. `res/tour.data.js` in einem Texteditor öffnen.
2. Eine neue Zeile am Ende des Template-Literal ergänzen, z. B.:
   ```
   14.03.2027;Erlangen;Skalapark;https://www.beispiel.de;Flyer_Erlangen.jpg
   ```
   Link und Flyer können leer bleiben (dann einfach zwei aufeinanderfolgende
   Semikolons setzen bzw. den Zeilenabschluss verwenden).
3. Falls ein Flyer angegeben wurde: die Bilddatei in `res/images/events/`
   ablegen (z. B. `Flyer_Erlangen.jpg`).
4. Speichern. Ein Reload der Seite genügt – es gibt keinen Build- oder
   Deploy-Schritt.

### 3.3 Verarbeitung im JavaScript

Die Termine werden beim Laden der Seite zweimal verarbeitet:

- **Karussell `#tour-list`:** `parseRows(tourData)` erzeugt pro Zeile ein
  `tour-item`-Element (Datum + Monat + Jahr links, Veranstaltung + Ort rechts).
- **Tabelle „Alle Termine“ (`#tour-table-body`):** Derselbe Text wird noch
  einmal in Tabellenzeilen umgewandelt, die der Button „Alle Termine →“ in
  einem Popup anzeigt.

Automatische Logik (Code in `index.html`):

- Die Kopfzeile (`Date;Location;...`) wird übersprungen, leere Zeilen ebenfalls.
- Zeilen ohne Datum, Ort oder Veranstaltung werden ignoriert.
- **Vergangene Termine werden automatisch ausgeblendet** (nur wenn ein Jahr
  angegeben ist und der Termin in der Vergangenheit liegt).
- Fehlt beim Link ein Protokoll (`http://`, `https://`), wird automatisch
  `https://` vorangestellt.
- Der Flyer wird aus `res/images/events/<Flyer>` geladen und im Event-Detail-
  Popup angezeigt (klickbar = Vollbild/„Zoom“).

> **Hinweis:** Monatsnamen werden im Karussell englisch (Jan, Feb …), in der
> Tabelle deutsch (Jan, Mär, Mai …) angezeigt. Das Format der Datumsangabe ist
> in beiden Fällen TT.MM.JJJJ.

---

## 4. Inhalt und Zweck der einzelnen Abschnitte

Die Seite besteht aus einem Header, mehreren Sections und diversen Popups.
Reihenfolge auf der Seite von oben nach unten:

### 4.1 Sticky Header + Navigation

- **Zweck:** Schnellzugriff auf die Seitenbereiche und auf die Sozialen Medien.
- Der Header bleibt beim Scrollen oben fixiert und erhält ab einer bestimmten
  Scroll-Position einen dunklen Hintergrund.
- Ein **Hamburger-Menü** öffnet die Vollbild-Navigation. Auf dem Desktop zeigt
  sie die Punkte *Home, Nächste Termine, Die Band, Kontakt*, auf Mobilgeräten
  zusätzlich *Hörproben* und *Downloads*.
- Rechts im Header liegen die Icons zu **YouTube, WhatsApp, Facebook und
  Instagram** (externer Link, neuer Tab).

### 4.2 Hero / Home (`#home`)

- **Zweck:** Visuelle Einstiegsfläche mit dem Bandlogo.
- Großes Logo in der Mitte; dahinter ein halbtransparentes Bandfoto als
  Hintergrund. Auf Mobilgeräten wird per JavaScript zufällig eines von zwei
  Bildern gewählt.
- Ein animierter „Scroll down“-Hinweis führt zur nächsten Sektion.

### 4.3 Nächste Termine / Tour (`#tour`)

- **Zweck:** Kommende Auftritte präsentieren.
- Ein horizontal scrollbares **Karussell** mit den kommenden Terminen
  (Pfeil-Buttons, Wischgeste, mobile Scroll-Hinweise).
- Der Button **„Alle Termine →“** öffnet ein Popup mit einer **Tabelle** aller
  zukünftigen Termine.
- Klick auf einen einzelnen Termin öffnet das **Event-Detail-Popup** mit Datum,
  Veranstaltung, Ort, optionalem Flyerbild und – falls vorhanden – einem Button
  „Zum Veranstalter“.
- Datenquelle: `res/tour.data.js` (siehe Kapitel 3).

### 4.4 Die Band / About (`#about`)

- **Zweck:** Die Band vorstellen und Hörproben anbieten.
- **Beschreibung:** Kurzer Slogan („Ska – Punk – Rock'n'Roll …“).
- **Maskottchen:** Zufällig wird eines von sechs „Skarabäus“-Bildern angezeigt.
- **Statistiken:** Animierte Zähler für *Shows played*, *Musiker* und *Spaß*
  (laufen an, sobald der Bereich sichtbar wird).
- **Mitglieder-Karussell:** Alle neun Musiker als automatisch laufendes,
  unendliches Karussell. Ein Klick auf ein Mitglied öffnet das **Modal** mit
  Foto, Name, Instrument und Biografie. (Video von Stocki ist vorhanden, wird
  aber aktuell nicht genutzt.)
- **Hörproben:** Setlist mit fünf Songs; jeder Eintrag trägt einen
  `data-yt`-Link. Ein Klick öffnet den **YouTube-Popup** und startet das Video
  automatisch (Einbettung als Iframe, Autoplay).

### 4.5 Für Veranstalter / Kontakt (`#contact`)

- **Zweck:** Buchungsanfragen erleichtern und Pressematerial bereitstellen.
- **Kontakt:** Der Button „Kontakt →“ öffnet ein Popup mit Name, Telefon,
  E-Mail und den Sozialen-Medien-Links.
- **Downloads:** Zwei Download-Karten – **Presse** (Fotos, Logo, Pressetext)
  und **Technik** (Bühnenplan, Tech Rider). Beide verlinken auf ZIP-Archive
  unter `doc/`.

### 4.6 Footer

- **Zweck:** Rechtliches.
- Enthält Links zu **Impressum** und **Haftungsausschluss**, die als Popup
  geöffnet werden, sowie den Copyright-Hinweis.

### 4.7 Popups im Überblick

| Popup | Auslöser | Inhalt |
|---|---|---|
| Tour-Popup (`#tour-popup-overlay`) | Button „Alle Termine →“ | Tabelle aller Termine |
| Event-Detail (`#event-detail-overlay`) | Klick auf Termin | Datum, Veranstaltung, Ort, Flyer, Veranstalter-Link |
| Mitglieder-Modal (`#modal-overlay`) | Klick auf Bandmitglied | Foto, Name, Instrument, Biografie |
| YouTube-Popup (`#yt-popup-overlay`) | Klick auf Hörprobe | eingebettetes YouTube-Video (Autoplay) |
| Kontakt-Popup (`#contact-popup-overlay`) | Button „Kontakt →“ | Kontaktdaten + Social Links |
| Impressum/Haftung (`#agb-popup-overlay`, `#haftung-popup-overlay`) | Links im Footer | Rechtstexte |

Alle Popups teilen sich einheitliche Ein-/Ausblend-Animationen, werden beim
Schließen aufgeräumt (z. B. wird die YouTube-URL geleert) und lassen sich mit
`Esc` schließen. Popups werden automatisch an die Bildschirmgröße angepasst
(„Zoom“, falls der Inhalt zu groß ist).

---

## 5. Unterschiede zwischen Mobile- und Desktop-Version

Die Seite ist **responsive**. Der Umschaltpunkt liegt bei einer Breite von
**768 px** (`@media (max-width: 768px)`). Inhalte sind auf beiden Versionen
identisch, es ändern sich aber Layout, Bedienung und teils die Inhalte der
Navigation. Die wichtigsten Unterschiede:

### 5.1 Navigation

| Aspekt | Desktop | Mobile |
|---|---|---|
| Menü | Vollbild-Overlay mit `#mobile-nav` | eigenes Overlay `#mobile-nav-extra` |
| Menüpunkte | Home, Nächste Termine, Die Band, Kontakt | zusätzlich **Hörproben** und **Downloads** |
| Öffnen | Hamburger-Button (wie mobile) | Hamburger-Button |

Die „richtige“ Navigation wird per JavaScript anhand der Bildschirmbreite
gewählt (`getActiveNav()`). Beim Klick auf einen Menüpunkt schließt sich das
Menü.

### 5.2 Hero / Hintergrundbild

| Aspekt | Desktop | Mobile |
|---|---|---|
| Hintergrund | fix `res/images/mobil_stairs.JPG` (per CSS) | **zufällig** eines von zwei Bildern (`mobil_stairs.JPG` oder `mobil_green.JPG`), per JavaScript gesetzt |

### 5.3 Tour-Karussell (Nächste Termine)

| Aspekt | Desktop | Mobile |
|---|---|---|
| Bedienung | Pfeil-Buttons `‹` / `›` | **Wischen** (Touch), Pfeile ausgeblendet |
| Scroll-Hinweise | ausgeblendet | seitliche Verlaufs-Gradienten mit Pfeil-Hinweis |
| Kartenbreite | min. **280 px**, feste Polsterung | min. **160 px**, ohne seitliche Polsterung |
| Datum/Darstellung | große Datumszahl (3,5rem) | verkleinert (2,2rem) |

### 5.4 Bandbereich / About

| Aspekt | Desktop | Mobile |
|---|---|---|
| Layout | CSS-Grid, Text und Setlist nebeneinander | alles **untereinander** (flex column) |
| Maskottchen-Bild | normal groß | auf **140 px** verkleinert |
| Beschreibungstext | 1,15rem | **1,35rem** (größer für bessere Lesbarkeit) |

### 5.5 Mitglieder-Karussell

| Aspekt | Desktop | Mobile |
|---|---|---|
| Kartenbreite | **20 %** des Containers (5 sichtbar) | **75 %** des Containers (ca. 1,3 sichtbar) |
| Bedienung | Pfeil-Buttons `‹` / `›`, Scroll beim Hovern stoppt das Auto-Play | **Wischen** per Touch (Drag), Pfeile ausgeblendet |
| Namen/Instrumente | 1,1rem | **1,6rem** / 0,95rem |
| Auto-Play | läuft, pausiert bei Mouse-Hover | läuft weiter (kein Hover möglich) |
| Position nach Modal | – | beim Schließen des Modals wird die Position zur angeklickten Karte zentriert (`bringSlideLeft`) |

### 5.6 Kontakt / Downloads

| Aspekt | Desktop | Mobile |
|---|---|---|
| Layout | zwei Spalten (Kontakt links, Downloads rechts) | **eine Spalte** untereinander |

### 5.7 Mitglieder-Modal (Popup)

| Aspekt | Desktop | Mobile |
|---|---|---|
| Aufbau | zwei Spalten (Foto links, Infos rechts), ab **600 px** | **eine Spalte** (Foto oben, Infos darunter) |

### 5.8 Allgemein

- Alle Popups werden automatisch an die Bildschirmgröße angepasst (per
  „Zoom“-Eigenschaft), damit sie nie über den Rand ragen.
- Bei `prefers-reduced-motion` werden sämtliche Ein-/Ausblend-Animationen
  deaktiviert (gilt für beide Versionen).
- Einzelne Ausgaben wurden im Desktop-CSS ausgeblendet, sind aber für beide
  Versionen vorgesehen: die `hero-links` (Stadion-Buttons „Tour/About/Kontakt“)
  und die `section-label`s.

---

## 6. Schnellreferenz: Wo was gepflegt wird

| Was möchte ich ändern? | Datei / Stelle |
|---|---|
| Termine (Auftritte) | `res/tour.data.js` (CSV-Block) |
| Flyer-Bilder zu Terminen | Datei nach `res/images/events/` kopieren, Namen in `tour.data.js` eintragen |
| Bandmitglieder (Name, Instrument, Bio) | Inline-Array `members` in `index.html` |
| Mitgliederfotos | Datei unter `res/images/members/`, Pfad im `members`-Array |
| Hörproben / YouTube-Links | `.setlist-item`-Elemente in `index.html` (`data-yt`-Attribut) |
| Kontaktdaten | Abschnitt „Contact Popup“ in `index.html` |
| Social-Media-Links | Header, Kontakt-Popup in `index.html` |
| Downloads (Presse/Technik) | ZIP-Archive in `doc/` austauschen (Dateinamen beibehalten) |
| Impressum / Haftungsausschluss | Footer-Popups in `index.html` |
| Farben, Abstände, Optik | `<style>`-Block in `index.html` |
