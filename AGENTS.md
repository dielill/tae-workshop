# Anweisungen an den Agenten

## Vorschau (Technik, bitte stehen lassen)
- Die App ist index.html im Hauptordner und lädt ihre Daten per fetch aus daten/ (relativer Pfad).
- htmlpreview.github.io führt nur Skripte aus, die in der Seite selbst stehen. Bibliotheken (SheetJS, three.js, rhino3dm.js) per document.createElement('script') nachladen und erst nach onload benutzen. Kein <script src>, kein type="module", kein window.onload.
- Nach jedem Schritt committen, pushen und den Link mit Commit-Hash geben:
  https://htmlpreview.github.io/?https://github.com/BENUTZER/REPO/blob/COMMIT/index.html

<!-- Alles ab hier schreibt ihr selbst, gern zusammen mit dem Agenten:
     „Frag mich nacheinander, was in diese Datei gehört.“
     Was hinein gehört, steht auf https://tae-workshop.netlify.app
     im Abschnitt „Anweisungen an den Agenten“. -->

## Ziel
Eine App für Bauingenieure: Ökobilanz (GWP) von Tragwerksmodellen.
Ablauf: Modell einlesen → Funktion jedes Stabs erkennen → Material der
ÖKOBAUDAT-Kategorie zuordnen → GWP je Funktion berechnen → im 3D-Viewer
anzeigen. Jeder Stab ist anklickbar und zeigt seine Daten, die Quelle
(Datei, Blatt/Zeile bzw. Objekt), den Faktor mit UUID und wie die
Zuordnung entschieden wurde.
- Eine einzige HTML-Datei (index.html), kein Server.

## Arbeitsweise
- Kleine Schritte. Nach jedem Schritt das Ergebnis zeigen (Link, siehe Vorschau).
- Nachfragen statt raten.
- Neue Datenquellen: erst die Struktur zeigen, dann parsen.

## Zahlen
- Einheiten immer mitführen (in Variablennamen, Tabellen und Anzeige).
- Emissionsfaktoren nie schätzen. Nur Werte aus daten/gemeinsam/faktoren_oekobaudat.csv,
  jeder Faktor mit Quelle (UUID, Name, Version, deklarierte Einheit) anzeigen.
- Alle Module getrennt ausweisen: A1-A3, C3, C4, D.
- Menge: Beton und Holz Volumen V = A × L [m³]; Stahl Masse m = A × L × ρ [kg],
  ρ = Wichte / 9,81 m/s² (aus dem Modell). gwp_bezug der CSV beachten (je kg / je m³).
- Beton ohne Bewehrung (keine Menge im Modell, BETONSTAHL wird nicht angesetzt).

## Entscheidungsleiter
Gilt für Funktion und Materialkategorie jedes Stabs.
1. Algorithmus (Regeln, zuordnung.csv, Geometrie) mit Konfidenz 0 bis 1.
2. KI, wenn Konfidenz < 0,8.
3. Mensch, wenn auch die KI < 0,8 liefert oder die Kategorie MENSCH ist.
- Jede Stufe speichert: Ergebnis, Konfidenz (0 bis 1), Begründung.
- Entscheidungen des Menschen in localStorage speichern und beim Laden wieder anwenden.

## KI-Stufe
- Vorerst ohne API. Die Stufe ist im Code als eigene Funktion vorgesehen
  (gibt im Moment „nicht verfügbar“ zurück, Fall geht an den Menschen),
  damit später ein API-Aufruf eingesetzt werden kann.

## Fakten zu den Daten
Siehe daten/DATEN.md.

## Unsere Entscheidungen
- Quellen: beide (Excel-Export und Rhino .3dm), alle drei Modelle fest aus daten/tragwerksmodell/.
- Funktionen: Stütze, Träger/Riegel, Nebenträger/Pfette, Verband, Zugstab (Liste kann wachsen).
- Ökobilanz nur nach Funktion gruppieren (nicht nach Material).
- 3D: echte Querschnitte als Körper, nicht nur Linien.
- Bei Abweichungen zwischen Excel und Rhino gilt Rhino (Abweichung anzeigen).
- Z-Achse: im Excel-Export nach unten (RFEM), in den .3dm nach oben. Anzeige: Z nach oben.
