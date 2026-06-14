# WEBSITE-AUDIT.md
**Erstellt:** 2026-06-14  
**Geprüfte Dateien:** index.html, impressum.html, datenschutz.html, website_assets/  
**Website:** https://fertighaus-badstieber.de/  
**Status:** Phase 1 – Audit only. Keine Änderungen vorgenommen.

---

## 1. Kritische Probleme

### 1.1 Google Fonts lädt VOR der Einwilligung (DSGVO-Verstoß)
- **Datei:** index.html, Zeile 12–13
- **Code:**
  ```html
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link href="https://fonts.googleapis.com/css2?family=DM+Serif+Display..." rel="stylesheet" />
  ```
- **Problem:** Die Google Fonts (DM Serif Display, Plus Jakarta Sans) werden bei jedem Seitenaufruf sofort geladen – ohne Einwilligung. Dabei wird die IP-Adresse des Besuchers an Google LLC (USA) übertragen.
- **Widerspruch zur Datenschutzerklärung:** Die Datenschutzerklärung (Homepage-Datenschutz.txt, Abschnitt 4) behauptet, dass Google Fonts nur nach ausdrücklicher Einwilligung geladen wird: *„Die Verarbeitung... wird nur dann vollzogen, wenn Sie uns... Ihre ausdrückliche Einwilligung erteilt haben."* Das ist technisch falsch.
- **Kann Claude beheben:** Ja – Fonts lokal hosten oder per JavaScript nur nach Consent laden.
- **Manuelle Prüfung durch Inhaber:** Datenschutzerklärung muss angepasst werden, falls Fonts lokal gehostet werden.

### 1.2 Tally-Skript lädt VOR der Einwilligung
- **Datei:** index.html, Zeile 11
- **Code:**
  ```html
  <script async src="https://tally.so/widgets/embed.js"></script>
  ```
- **Problem:** Das Tally-Einbettungsskript (tally.so) lädt sofort beim Seitenaufruf, bevor der Nutzer einwilligt. Tally verarbeitet Metadaten (IP, Browser-Informationen).
- **Relevanz:** Tally ist ein Drittanbieter-Dienst. Auch wenn das Formular nur bei Klick öffnet, lädt das Einbettungsskript selbstständig und kann Cookies setzen oder Verbindungen herstellen.
- **Kann Claude beheben:** Ja – Skript kann bedingt geladen werden (nach Consent oder per `data-tally-*`-Attributen ohne separates Skript, falls möglich).
- **Manuelle Prüfung:** Tally-Datenschutzrichtlinie prüfen; ggf. Auftragsverarbeitungsvertrag (AVV) abschließen.

### 1.3 Tailwind CSS CDN lädt VOR der Einwilligung
- **Datei:** index.html, Zeile 10
- **Code:** `<script src="https://cdn.tailwindcss.com"></script>`
- **Problem:** Lediglich ein CSS-Framework, jedoch wird die IP-Adresse des Nutzers an den Tailwind/CDN-Server übertragen. Tailwind CDN ist für Produktionsseiten nicht empfohlen (Performance, Tracking-Risiko, Abhängigkeit von externem CDN).
- **Kann Claude beheben:** Ja – Tailwind kann lokal eingebaut werden (Build-Prozess) oder durch statische CSS-Datei ersetzt werden.
- **Manuelle Prüfung:** Nicht erforderlich, aber empfohlen.

### 1.4 E-Mail-Adresse im Impressum ist inkonsistent
- **Datei:** impressum.html (Zeile 173) vs. website_assets/Impressum (1).txt (Zeile 8)
- **Problem:**
  - impressum.html zeigt: `thomas.badstieber@wolfsystem.de`
  - Die Impressum-Textdatei enthält: `t.badstieber@gmx.de`
- **Rechtliche Relevanz:** §5 DDG erfordert eine korrekte, erreichbare Kontaktadresse. Eine falsche E-Mail kann eine Abmahnung auslösen.
- **Manuelle Prüfung durch Inhaber:** Welche E-Mail-Adresse ist korrekt und zuverlässig erreichbar?

### 1.5 Telefonummer fehlt im Impressum
- **Datei:** impressum.html
- **Problem:** Die Impressum-Textdatei enthält die Telefonnummer `0171 2 109 109`. Das Impressum-HTML zeigt keine Telefonnummer. Gemäß §5 DDG ist keine Telefonnummer zwingend erforderlich, aber ein „schneller elektronischer Kontakt" muss angegeben sein. Eine Telefonnummer ist empfehlenswert.
- **Kann Claude beheben:** Ja – Telefon einfügen.
- **Manuelle Prüfung:** Inhaber bestätigt, welche Nummer verwendet werden soll.

### 1.6 Unternehmensname fehlt im Impressum-HTML
- **Datei:** impressum.html, Karte "Betreiber dieser Website"
- **Problem:** Die Impressum-Textdatei nennt den Unternehmensnamen: „Thomas Badstieber Handelsvertretung". Dieser fehlt im HTML-Impressum komplett.
- **Kann Claude beheben:** Ja – Zeile ergänzen.
- **Manuelle Prüfung:** Inhaber bestätigt, dass dieser Unternehmensname korrekt und aktiv ist.

---

## 2. Datenschutz und Cookie-Fragen

### 2.1 Cookie-Banner blockiert keine optionalen Dienste
- **Datei:** index.html, Zeilen 10–13 und 3142–3162
- **Problem:** Das Cookie-Banner fragt nach Einwilligung, aber alle optionalen Dienste (Google Fonts, Tally) laden unabhängig davon. Das Banner speichert nur die Entscheidung des Nutzers in `localStorage` unter dem Schlüssel `cookieConsent`.
- **Was passiert VOR „Akzeptieren":** Google Fonts und Tally laden sofort.
- **Was passiert NACH „Akzeptieren":** Keine technische Änderung – dieselben Dienste liefen bereits.
- **Was passiert NACH „Ablehnen":** Ebenfalls keine Änderung. Die optionalen Dienste liefen schon.
- **Kann Einwilligung widerrufen werden?** Nein – es gibt kein UI, um die Entscheidung später zu ändern.
- **Kann Claude beheben:** Ja – Dienste nach Einwilligung laden (oder lokal hosten).

### 2.2 localStorage-Nutzung für Cookie-Entscheidung
- **Datei:** index.html, Zeilen 3147, 3155
- **Schlüssel:** `cookieConsent` (Wert: `"accepted"` oder `"declined"`)
- **Keine anderen localStorage-Einträge gefunden.**
- **Bewertung:** `localStorage` ist nach DSGVO keine klassische Cookie-Technologie, fällt aber unter die ePrivacy-Richtlinie (§25 TTDSG). Die Datenschutzerklärung beschreibt ein „Cookie-Consent-Tool" – das trifft technisch zu.
- **Manuelle Prüfung:** Datenschutzberater bestätigen, ob localStorage-Nutzung für Consent korrekt ist.

### 2.3 Keine Möglichkeit, Einwilligung zurückzuziehen
- **Datei:** index.html
- **Problem:** Sobald „Akzeptieren" oder „Ablehnen" geklickt wurde, verschwindet das Banner und kann nicht erneut aufgerufen werden. Der Nutzer kann seine Entscheidung nicht ändern.
- **Kann Claude beheben:** Ja – Link „Cookie-Einstellungen" im Footer ergänzen.
- **Manuelle Prüfung:** Juristische Anforderung bestätigen.

---

## 3. Externe Dienste und Datenübertragungen

| Domain | Wann geladen | Automatisch | Cookies/localStorage | IP-Übertragung | Anmerkung |
|---|---|---|---|---|---|
| `cdn.tailwindcss.com` | Sofort beim Aufruf | Ja | Unbekannt | Ja | CDN-Dienst, kein Cookie bekannt |
| `tally.so` (Widget-Skript) | Sofort beim Aufruf | Ja | Möglich | Ja | Formular-Dienst, lädt vor Consent |
| `fonts.googleapis.com` | Sofort beim Aufruf | Ja | Nein direkt | Ja | Google LLC, USA |
| `fonts.gstatic.com` | Sofort beim Aufruf | Ja (via Google Fonts) | Nein | Ja | Google LLC, USA |
| `www.wolfhaus.de` | Nur bei Klick | Nein | Nein (fremde Site) | Nur nach Klick | Externer Link, rel="noopener noreferrer" |
| `google.com/search?...` | Nur bei Klick | Nein | Nein (fremde Site) | Nur nach Klick | Google-Profil-Link, fehlt `noreferrer` |
| `wa.me/491712109109` | Nur bei Klick | Nein | Nein | Nur nach Klick | WhatsApp-Link, kein eingebettetes Skript |
| `instagram.com` | Nur bei Klick | Nein | Nein | Nur nach Klick | Externer Link, rel="noopener noreferrer" |
| `facebook.com` | Nur bei Klick | Nein | Nein | Nur nach Klick | Externer Link, rel="noopener noreferrer" |
| `tally.so/r/Gxb2kO` | Nur bei Klick (Mobil) | Nein | Nein | Nur nach Klick | Mobile Formular-Weiterleitung |

**Wichtig:** Es wurden keine Analytics, Tracking-Pixel, Facebook Pixel, Google Analytics, Google Tag Manager oder ähnliche Tracking-Tools gefunden.

---

## 4. Impressum und rechtliche Seiten

### 4.1 impressum.html
| Punkt | Status | Anmerkung |
|---|---|---|
| Vollständiger Name | ✅ Thomas Badstieber | |
| Unternehmensname | ❌ Fehlt | Muss „Thomas Badstieber Handelsvertretung" enthalten |
| Postanschrift | ✅ Beim Kellerhaus 1, 95463 Bindlach | |
| E-Mail | ⚠️ Inkonsistenz | Seite zeigt wolfsystem.de, Textdatei zeigt gmx.de |
| Telefon | ❌ Fehlt | In Textdatei vorhanden (0171 2 109 109) |
| Umsatzsteuer-ID | ⚠️ Platzhalter | „DE ...." – muss ergänzt oder als Kleinunternehmer entfernt werden |
| Streitschlichtung | ✅ Korrekt | |
| Copyright-Jahr | ❌ 2025 | Sollte 2026 sein (wie in index.html) |
| Seitentitel (`<title>`) | ⚠️ | Enthält „Bayreuth" – ggf. anpassen |

### 4.2 datenschutz.html
| Punkt | Status | Anmerkung |
|---|---|---|
| Vollständige Datenschutzerklärung | ❌ Platzhalter | Seite zeigt nur „In Bearbeitung" |
| Fertige Datenschutzerklärung | ✅ Bereit | Homepage-Datenschutz.txt enthält vollständige IT-Recht Kanzlei-Version |
| Copyright-Jahr | ❌ 2025 | Sollte 2026 sein |
| Datenschutz stimmt mit Technik überein | ❌ Nein | Texte behaupten Consent-Gate für Google Fonts, technisch ist keins implementiert |

**→ Die fertige Datenschutzerklärung aus `website_assets/Homepage-Datenschutz.txt` muss in `datenschutz.html` eingebaut werden.**

---

## 5. Schriften und Icon-Bibliotheken

### 5.1 Google Fonts (extern geladen)
- **Fonts:** `DM Serif Display` (Gewichte: Normal, Italic) und `Plus Jakarta Sans` (Gewichte: 300, 400, 500, 600, 700, 800)
- **Laden über:** fonts.googleapis.com + fonts.gstatic.com
- **Lizenz:** Beide Schriften sind unter der SIL Open Font License 1.1 lizenziert – lokales Hosting ist ausdrücklich erlaubt.
- **Empfehlung:** Fonts lokal hosten (`@font-face` mit lokalen WOFF2-Dateien).
- **Kann Claude beheben:** Ja – `@font-face`-CSS generieren, aber Font-Dateien müssen separat heruntergeladen werden (Claude darf Fonts nicht herunterladen, da sie über Google Fonts ausgeliefert werden).

### 5.2 Icons
- Alle Icons sind inline SVG – keine externe Icon-Bibliothek (kein Font Awesome, kein Heroicons CDN). Kein Problem.

---

## 6. Bilder, Logos, PDFs und mögliche Copyright-Risiken

### 6.1 WOLF-Haus Logo
- **Datei:** website_assets/Wolf.jpg
- **Verwendung:** Nav, Hero, Footer, Kontakt-Karte
- **Risiko:** Das WOLF-Haus-Logo ist eine eingetragene Marke von WOLF System GmbH. Die Verwendung durch einen autorisierten Vertriebspartner ist üblicherweise vertraglich geregelt.
- **Manuelle Prüfung:** Liegt eine schriftliche Genehmigung von WOLF System GmbH vor?

### 6.2 WOLF-Produktionsbilder (Lagerhalle)
- **Dateien:** Wolf lagerhalle.jpeg, Wolf lagerhalle 2.jpeg, Wolf Lagerhalle 3.jpeg, Wolf Lagerhalle 4.jpeg, Wolf lagerhalle 5.jpeg
- **Verwendung:** Wolf-Haus-Partner-Sektion
- **Risiko:** Wahrscheinlich offizielle WOLF-Produktionsbilder. Urheberrecht liegt bei WOLF System GmbH oder Fotografen.
- **Manuelle Prüfung:** Woher stammen diese Bilder? Schriftliche Freigabe?

### 6.3 WOLF-Haus PDF-Broschüren
- **Dateien:** Zuhause_Sein_im_Wolf_Haus.pdf, Wolf_Musterhaeuser.pdf, Wolf_Kompaktmodul.pdf, Wolf_Bungalow.pdf, Bauherrenleitfaden_2024.pdf, BuL_26_web (1).pdf, SYSTEM.pdf
- **Verwendung:** Downloadbereich, Service-Karte
- **Risiko:** Alle PDFs sind offizielle WOLF-Marketingmaterialien. Die Veröffentlichung auf einer Drittanbieter-Website (auch von Vertriebspartnern) bedarf der Genehmigung von WOLF System GmbH.
- **Manuelle Prüfung:** Sind diese PDFs für die öffentliche Veröffentlichung durch Partner freigegeben?

### 6.4 Fotos mit Thomas Badstieber
- **Dateien:** vadder 3.jpg, vadder 4.jpg, Vadder gute qualität.jpg, vadder 5.jpg, Vadder.jpeg, Holz vadder.jpeg, vadder zuhause sein.jpeg, vadderr laptop.jpeg, vadder in küche.jpeg, Vadder wohnhaus.jpeg, vadder vor Haus.jpeg, Beratung.jpeg
- **Verwendung:** Hero, Über-mich-Sektion, Benefits-Bild
- **Risiko:** Sofern Fotos von einem professionellen Fotografen erstellt wurden, liegt das Urheberrecht beim Fotografen.
- **Manuelle Prüfung:** Fotos selbst gemacht oder von Fotograf? Liegt schriftliche Nutzungsrechtsübertragung vor?

### 6.5 KI-generierte Visualisierungen
- **Dateien:** KI 1.jpg, KI2.jpg, KI 3.jpg, KI 4.jpg, KI 5.jpg, KI 6.jpg, KI 7.jpg, KI8.jpeg
- **Verwendung:** KI-Visualisierungs-Sektion
- **Hinweis:** Auf der Website gibt es bereits einen korrekt formulierten Hinweis: „Die gezeigten Hausbilder sind KI-generierte Visualisierungen..." – gut.
- **Risiko:** Abhängig vom verwendeten KI-Tool – manche Tools untersagen kommerzielle Nutzung oder beanspruchen bestimmte Rechte.
- **Manuelle Prüfung:** Welches KI-Tool wurde verwendet? Sind kommerzielle Nutzungsrechte gegeben?

### 6.6 Hausfotos (Haus 1–7, Innenansichten)
- **Dateien:** Haus 1.jpeg bis Haus 7.jpeg, Haus innen.jpeg, Haus innen 2.jpeg, haus 2.jpeg
- **Verwendung:** Einblicke-Galerie
- **Risiko:** Unbekannte Quelle. Können von WOLF bereitgestellt, aus dem Internet entnommen oder eigene Fotos sein.
- **Manuelle Prüfung:** Woher stammen diese Bilder?

### 6.7 Maskottchen / sonstige Bilder
- **Dateien:** Maskottchen.jpeg, wolf 5.jpg, Regionen.PNG, vadder flyer ausgebessert.png, Holz nah.jpeg
- **Verwendung:** Teils ungenutzt oder im Regionen-Bereich
- **Manuelle Prüfung:** Quellen klären.

### 6.8 Nicht verwendete Assets
- **Dateien (im HTML nicht referenziert):** vadder.webp, vadder 4.jpg, vadder 5.jpg, wolf 5.jpg (wird in Regionen verwendet, aber ohne Quellenangabe), KI 7.jpg, KI bilder rendering.PNG, KI visualisierung.png, KI Bilder.PNG, Regionen.PNG, Bewertungen.png, Bewertung 1.PNG, bewertung 2.PNG, bewerttung 3.PNG, bewertung 4 & 5.PNG, bewertung 6.PNG, bewertung 7.PNG, Rezensionen.png, Gelb.PNG, wolf logo schgwarz.PNG, Maskottchen.jpeg, broschüren+.PNG, Startseite.PNG, inspo start.PNG, KI selbe höhe.PNG, replace.PNG, Ausbessern.PNG, KI section.PNG, 10 Jahre erfahrung.PNG, ändern.PNG, Vadder schlechte qualität.PNG, **WhatsApp Video 2026-06-10 at 22.43.46.mp4**
- **Anmerkung:** Das MP4-Video ist nicht in den HTML-Seiten eingebunden, liegt aber öffentlich im Assets-Ordner. Wenn die Website öffentlich deployt ist, könnte es direkt aufgerufen werden.

---

## 7. Google-Bewertungen

### 7.1 Erwähnung einer anderen Marke (Danwood)
- **Datei:** index.html, Zeile 2042 (erste Bewertung: Christianja_on_tour)
- **Zitat:** *„...dass wir mit ihm ein Danwood Haus bauen."*
- **Problem:** Diese Bewertung nennt eine Konkurrenzmarke (Danwood). Die Bewertung stammt offenbar aus einer Zeit, bevor Thomas Badstieber WOLF-Haus beriet. Das kann für Besucher irreführend sein und wirft die Frage auf, ob dieser Kunde tatsächlich ein WOLF-Haus gebaut hat.
- **Manuelle Prüfung:** Wurde diese Bewertung als WOLF-Haus-Beratung erhalten? Soll sie auf der Website verbleiben?
- **Kann Claude beheben:** Nein – Entscheidung liegt beim Inhaber.

### 7.2 Bewertungsklone und Barrierefreiheit
- Die Original-Reviews (SET 1) sind korrekt für Screenreader zugänglich.
- Die Klone (SET 2) haben bereits `aria-hidden="true"` – korrekt implementiert.
- **Problem:** Die Datumsspalte bei allen Bewertungen zeigt nur „Google Bewertung" ohne Datum. Für Glaubwürdigkeit und DSGVO (Nennung echter Kundennamen) empfehlenswert.

### 7.3 Angezeigtes Rating vs. Google-Profil
- Seite zeigt: **5,0 Sterne**
- **Manuelle Prüfung:** Stimmt diese Bewertung aktuell mit dem Google Business Profil überein?

### 7.4 Echte Kundennamen ohne Datum
- Die Namen (Josef Kynast, Pauline V, Hans Bauer, Lutz Boeske, Hubert Hiltner, Karin Schlegel) sind vollständige oder teilweise echte Namen aus Google Reviews.
- **Manuelle Prüfung:** Sind diese Personen damit einverstanden, namentlich auf der Website erwähnt zu werden? Google Reviews sind öffentlich, aber die Weiterverwendung auf einer eigenen Website liegt in einer rechtlichen Grauzone.

---

## 8. Werbeaussagen – Manuelle Prüfung erforderlich

Jede der folgenden Aussagen erfordert manuelle Bestätigung durch den Inhaber. Keine darf ohne Prüfung auf der Website verbleiben.

| Aussage | Fundort | Status |
|---|---|---|
| „7+ Jahre Erfahrung" | Stats-Bar, index.html:1780; Über-mich-Sektion | MANUAL VERIFICATION REQUIRED |
| „500+ Beratungsgespräche" | Stats-Bar, index.html:1784 | MANUAL VERIFICATION REQUIRED |
| „100% Persönliche Betreuung" | Stats-Bar, index.html:1792 | MANUAL VERIFICATION REQUIRED |
| „12 Regionen in Oberfranken" | Stats-Bar, index.html:1788 | MANUAL VERIFICATION REQUIRED |
| „60+ Jahre WOLF-Erfahrung" | WOLF-Sektion, index.html:2345 | MANUAL VERIFICATION REQUIRED – WOLF-Gründungsjahr prüfen |
| „KfW 40 Energiestandard" | WOLF-Sektion, index.html:2349 | MANUAL VERIFICATION REQUIRED – aktuelle WOLF-Spezifikation prüfen |
| „100% Individuell geplant" | WOLF-Sektion, index.html:2353 | MANUAL VERIFICATION REQUIRED |
| „Festpreisgarantie" | Benefits, index.html:2771 | MANUAL VERIFICATION REQUIRED – gilt für alle WOLF-Häuser? |
| „Feste Preise, klare Abläufe" | WOLF-Features, index.html:2338 | MANUAL VERIFICATION REQUIRED |
| „oft in wenigen Wochen aufgestellt" | Benefits, index.html:2775 | MANUAL VERIFICATION REQUIRED – Montagezeitraum bestätigen |
| „Antwort innerhalb von 24 Stunden" | Kontakt-Sektion, index.html:2943 | MANUAL VERIFICATION REQUIRED |
| „Ich melde mich innerhalb von 24 Stunden" | Kontakt, index.html:2976 | MANUAL VERIFICATION REQUIRED |
| „Kostenlos beraten lassen" | Hero, index.html:1762 | MANUAL VERIFICATION REQUIRED – ist Erstberatung dauerhaft kostenlos? |
| „Exklusiver Service" | KI-Badge, index.html:1865 | MANUAL VERIFICATION REQUIRED – was macht den Dienst „exklusiv"? |
| „autorisierter Partner" | Leistungen, index.html:1848 | MANUAL VERIFICATION REQUIRED – Partnerstatus belegen |
| „zertifizierter WOLF-Haus Fachberater" | Nav, Hero, Über-mich | MANUAL VERIFICATION REQUIRED – Zertifikat vorlegen |
| „Persönlich, transparent und ohne Verkaufsdruck" | Zitat-Karte, index.html:2749 | MANUAL VERIFICATION REQUIRED – Eigenzitat, kein Kundenzitat |

---

## 9. Barrierefreiheit

### 9.1 Positiv
- FAQ-Akkordeon: `aria-expanded` korrekt implementiert
- Lightbox: `role="dialog"`, `aria-modal="true"`, `aria-label` vorhanden
- Galerie-Klone: `aria-hidden="true"` auf Duplikaten
- Hamburger-Button: `aria-label="Menü öffnen"`
- Alt-Texte: Großteils sinnvoll befüllt

### 9.2 Probleme

| Problem | Datei:Zeile | Schwere |
|---|---|---|
| Kein Skip-to-Content-Link | index.html | Mittel |
| Lightbox: Fokus wird beim Öffnen nicht auf das Modal verschoben | index.html:3103 | Hoch |
| Lightbox: Kein Fokus-Trap (Tab verlässt Modal) | index.html:3103–3112 | Hoch |
| Einblicke-Gallery: Duplicate Cards haben `aria-hidden="true"` aber enthaltene Bilder können noch focussiert werden (falls tabindex nicht gesetzt) | index.html:2642+ | Mittel |
| Review-Carousel Original-Track: Kein `aria-label` zur Unterscheidung von Duplikaten | index.html:2031 | Niedrig |
| `prefers-reduced-motion` nicht berücksichtigt – Gallery-Animationen laufen unabhängig davon | index.html | Mittel |
| Lightbox-Bild `alt=""` ist leer (bleibt leer nach Öffnen) – Screenreader bekommt keinen Bildinhalt | index.html:3079 | Mittel |
| Hamburger-Menü: `aria-expanded` auf Button fehlt | index.html:1738 | Niedrig |
| FAQ-Panels nutzen `max-height`-Transition – keine `hidden`-Attribute – Screenreader können Inhalt lesen bevor Panel geöffnet ist | index.html:1703 | Niedrig |
| Kontaktform (Tally-Modal): Barrierefreiheit des Tally-Formulars selbst nicht prüfbar | Tally | Unbekannt |

---

## 10. SEO und Metadaten

| Problem | Datei:Zeile | Priorität |
|---|---|---|
| Seitentitel enthält „Bayreuth" statt Nordbayern/Region | index.html:6 | Hoch |
| Meta-Description enthält „Bayreuth" | index.html:7 | Hoch |
| Keine Open Graph Tags (`og:title`, `og:description`, `og:image`) | index.html Head | Hoch |
| Kein `<link rel="canonical">` | index.html Head | Mittel |
| Kein Favicon | alle HTML-Dateien | Mittel |
| Kein robots.txt | Projekt-Root | Niedrig |
| Kein sitemap.xml | Projekt-Root | Niedrig |
| Keine strukturierten Daten (Schema.org `Person`, `LocalBusiness`) | index.html | Mittel |
| Meta-Keywords enthält mehrfach „Bayreuth" und „Bindlach" | index.html:8 | Niedrig (Keywords werden von Google ignoriert) |
| impressum.html und datenschutz.html: `<meta name="robots" content="noindex">` – korrekt | beide Dateien | ✅ Korrekt |
| Alt-Texte in einblicke-Galerie enthalten „Bayreuth" | index.html:2611, 2629, 2688, 2700 | Mittel |
| Hero-Untertitel: „Ihr zertifizierter WOLF-Haus Berater in Bayreuth" | index.html:1758 | Hoch (Text) |
| Bewertungseinleitung: „Bauherren aus Bayreuth und Oberfranken" | index.html:1993 | Mittel |
| Einblicke-Leitung: „in Bayreuth und Oberfranken" | index.html:2598 | Mittel |

---

## 11. Mobile und technische Probleme

### 11.1 Sicherheit
| Problem | Datei:Zeile | Schwere |
|---|---|---|
| WhatsApp-Link fehlt `noreferrer` (nur `rel="noopener"`) | index.html:3011 | Niedrig |
| Google-Profil-Link fehlt `noreferrer` (nur `rel="noopener"`) | index.html:2278 | Niedrig |
| Keine Content-Security-Policy | alle Seiten | Mittel |
| Keine API-Schlüssel im Code gefunden | – | ✅ |
| Keine Secrets im HTML/JS gefunden | – | ✅ |

### 11.2 Performance
| Problem | Datei | Schwere |
|---|---|---|
| Tailwind via CDN (erzeugt mehrere Sekunden JS-Ausführung, Abhängigkeit von extern) | index.html:10 | Mittel |
| Brochure-Cover-Bilder fehlen (Bauherrenleitfaden_2024-cover.jpg, etc.) – onerror behandelt | index.html | Niedrig (behandelt) |
| MP4-Video liegt in website_assets (8+ MB möglich), nicht referenziert, aber öffentlich zugänglich | website_assets | Niedrig |
| Keine expliziten `width`/`height`-Attribute auf den meisten `<img>`-Tags – verhindert Layout-Stabilität (CLS) | index.html | Mittel |
| Alle Bilder ohne `loading="lazy"` im sichtbaren Bereich (Hero, Nav) – korrekt | index.html | ✅ |

### 11.3 Mobile
| Problem | Status |
|---|---|
| Cookie-Banner auf Mobile: Wurde in letztem Fix behoben (display:none) | ✅ Behoben |
| Hamburger-Menü funktioniert | ✅ |
| Tally-Modal auf Mobile: Redirect zu tally.so/r/Gxb2kO (verhindert abgeschnittenes Modal) | ✅ |
| Kein horizontales Overflow gefunden | ✅ |

### 11.4 JavaScript-Probleme
| Problem | Datei:Zeile | Schwere |
|---|---|---|
| Lightbox öffnet auch für `aria-hidden="true"` Gallery-Cards (Einblicke) | index.html:3128–3136 | Niedrig |
| Doppelter `click`-Listener (zwei separate `<script>`-Tags) – kein Konflikt, nur suboptimal | index.html:3205, 3239 | Niedrig |
| `smoothScrollTo` und Tally-Click-Listener auf `document` – könnten bei Tally-Links kollidieren | index.html:3205 | Niedrig |

---

## 12. Empfohlene Korrekturen

### Sofort möglich (Claude kann umsetzen, nach Freigabe):

1. **Impressum aktualisieren:** Unternehmensnamen „Thomas Badstieber Handelsvertretung", Telefonnummer ergänzen; E-Mail klären; Copyright-Jahr 2026
2. **Datenschutzerklärung einbauen:** IT-Recht Kanzlei-Text aus Homepage-Datenschutz.txt in datenschutz.html einfügen (sauber formatiert)
3. **Bayreuth in Marketingtexten ersetzen:** Seitentitel, Meta-Description, Hero-Untertitel, Bewertungseinleitung, Einblicke-Lead, Alt-Texte (die nicht im Rechtlichen verankert sind)
4. **Open Graph Tags ergänzen:** og:title, og:description, og:image, og:url
5. **Canonical Link ergänzen**
6. **Favicon einbinden** (sobald vorhanden)
7. **`rel="noreferrer"` auf WhatsApp- und Google-Links ergänzen**
8. **`aria-expanded` auf Hamburger-Button ergänzen**
9. **Lightbox: Fokus beim Öffnen setzen, Fokus-Trap implementieren**
10. **`prefers-reduced-motion` für Gallery-Animationen ergänzen**
11. **Copyright-Jahr in impressum.html und datenschutz.html auf 2026 setzen**

### Nur mit Inhaber-Entscheidung:

12. **Google Fonts lokal hosten** (Inhaber muss Font-Dateien herunterladen und bereitstellen)
13. **Tally: Consent-Gate implementieren** (oder bestätigen, dass kein Consent erforderlich ist)
14. **Danwood-Bewertung:** Belassen, entfernen oder Inhaber entscheidet
15. **VAT-ID-Platzhalter** im Impressum: Ausfüllen oder als Kleinunternehmer entfernen
16. **E-Mail im Impressum:** Richtige Adresse bestätigen
17. **KI-Visualisierungen Disclaimer:** Prüfen, welches Tool verwendet wurde
18. **WOLF-Bilder und PDFs:** Schriftliche Genehmigung einholen

---

*Dieses Dokument wurde am 2026-06-14 erstellt. Es handelt sich um eine technische Bestandsaufnahme, nicht um Rechtsberatung.*
