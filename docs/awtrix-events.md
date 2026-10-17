# AWTRIX Events

Zeigt Kalendertermine (Geburtstage, Sport, Feiertage, sonstige Termine) auf der
AWTRIX-Matrix. Welcher Termin welches Icon, welchen Text und welches
Zeitfenster bekommt, steht in einer Regelliste direkt in den Blueprint-Eingaben.

Blueprint: [`blueprints/automation/awtrix_events/awtrix_events.yaml`](../blueprints/automation/awtrix_events/awtrix_events.yaml)
Icon-Liste: [`blueprints/automation/awtrix_events/icons.md`](../blueprints/automation/awtrix_events/icons.md)

Voraussetzungen: eine AWTRIX 3 mit MQTT und die MQTT-Integration in Home Assistant.

## Funktionsweise

Die Automation rechnet im eingestellten Intervall (Standard 5 min) und beim
HA-Start den Soll-Zustand neu:

1. `calendar.get_events` für alle ausgewählten Kalender (heute und morgen).
2. Termine, deren Titel einen Begriff aus **Ignorieren** enthält, werden komplett
   übersprungen (z. B. Abfalltermine, die zusätzlich in einem Stadtkalender stehen). Pro Termin gewinnt
   danach die **erste** passende Regel von oben nach unten (`match` = "enthält",
   ohne Groß-/Kleinschreibung). Ohne Treffer greift die Standard-App (`termine`,
   Icon 2231), sofern "Termine ohne Regel anzeigen" an ist.
3. Es zählt nur, was jetzt im Zeitfenster liegt:
   - Termine mit Uhrzeit: `Start + von` bis `Ende + bis` (Minuten).
   - Ganztägige Termine: von `tag_von` bis `tag_bis` an jedem Tag des Termins.
     Mehrtägige ganztägige Termine laufen damit automatisch über alle Tage.
4. Pro App werden alle aktiven Treffer zu **einem** Text mit ", " verkettet
   (z. B. `Geburtstag Oma, Geburtstag Opa`). Das Icon stammt vom ersten Treffer,
   ganztägige vor zeitgebundenen, dann nach Startzeit.
5. Pro App wird per `mqtt.publish` auf `<Prefix>/custom/<app>` gepusht oder, wenn
   nichts aktiv ist, mit leerem Payload entfernt.

Die Lifetime ist maximal 15 min, bei Terminen mit Uhrzeit endet sie genau zum
Fensterende. Dadurch heilt sich das System nach AWTRIX- oder HA-Neustarts, nach
Terminänderungen und bei Kalenderausfällen selbst.

## Eingaben des Blueprints

| Eingabe | Bedeutung |
|---|---|
| Kalender | Mehrfachauswahl der Kalender-Entitäten |
| AWTRIX MQTT-Prefix | z. B. `homeassistant/awtrix_ed5528` |
| Ignorieren | Ein Begriff pro Zeile, "enthält", ohne Groß-/Kleinschreibung |
| Regeln | Liste, ein Eintrag pro Regel (siehe unten) |
| Erweitert | Termine ohne Regel anzeigen, deren App und Icon, Abgleich-Intervall |

Die Voreinstellungen im Blueprint sind Beispielregeln (Geburtstage, Sport,
Feiertage). Die Icon-IDs sind LaMetric-IDs, Namen wie `ChristmasSleigh` müssen als
Datei auf der Uhr liegen. Die Ignorieren-Liste ist leer.

### Spalten einer Regel

| Feld | Bedeutung |
|---|---|
| `app` | AWTRIX-App-Name, darf mehrfach vorkommen (z. B. `sport`) |
| `match` | Suchbegriffe kommagetrennt, "enthält", ohne Groß-/Kleinschreibung |
| `icon` | Icon-ID oder -Name (als Text) |
| `text` | `{summary}` = Titel (Standard, auch bei leerem Feld), fester Text möglich |
| `nur_icon` | Kein Text senden, nur das Icon (z. B. große Animation). Im Formular ein Schalter |
| `von` / `bis` | Minuten relativ zu Start / Ende, nur Termine mit Uhrzeit (z. B. `-90`) |
| `tag_von` / `tag_bis` | Anzeigefenster ganztägiger Termine (Standard 00:00 bis 24:00) |
| `uhrzeit` | Startzeit an den Text hängen (`Schwimmen 17:30`), Standard an |
| `duration` | Sekunden pro Durchlauf, Standard 10 |
| `color` | Optional, Textfarbe (z. B. `#FF0000`) |
| `sound` | Optional, RTTTL-Name, läuft bei jedem Durchlauf der App |

Neuen Termintyp ergänzen: in der Instanz unter "Regeln" einen Eintrag hinzufügen,
z. B. im YAML-Editor

```yaml
- {app: sport, match: "handball", icon: "12345", von: -90, bis: 0}
```

Danach das Icon auf die Uhr laden.

## Blueprint aktualisieren

HA importiert einen Blueprint nur einmal. Änderungen im Repo kommen so an:

- Schnell testen: Datei direkt in `config/blueprints/automation/awtrix_events/`
  ersetzen und Automationen neu laden, bestehende Instanzen übernehmen die Änderung
  sofort.
- Über die Quelle: unter Einstellungen > Automationen > Blueprints beim Blueprint
  "Blueprint erneut importieren". Das lädt die `source_url` neu und überschreibt die
  lokale Kopie. Neue Pflichteingaben müssen in der Instanz nachgetragen werden.

## Einrichtung

1. Blueprint importieren:
   [![Blueprint importieren](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fmax-t-d%2FHA_public%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fawtrix_events%2Fawtrix_events.yaml)
   Oder unter Einstellungen > Automationen & Szenen > Blueprints > Blueprint
   importieren die URL der Datei einfügen, oder die Datei nach
   `config/blueprints/automation/awtrix_events/awtrix_events.yaml` kopieren.
2. Aus dem Blueprint eine Automation erstellen, Kalender und MQTT-Prefix eintragen,
   Regeln bei Bedarf anpassen.
3. Alle Icons aus `icons.md` auf der Uhr abgleichen.

## Bekannte Grenzen

- Ein neuer Termin erscheint bis zu einem Intervall verzögert. Kürzere Intervalle
  lösen bei CalDAV (iCloud) bei jedem Lauf einen Abruf aus.
- Der Abruf läuft für alle Kalender in einem Aufruf. Fällt einer aus, bricht der
  Lauf ab, bestehende Apps laufen über ihre Lifetime aus.
- Zwei Termine in derselben App mit unterschiedlichem Fensterende verkürzen die
  gemeinsame Lifetime auf das frühere Ende. Nach dem nächsten Lauf passt es wieder.
- Ein leeres Textfeld heißt "Titel anzeigen". Nur das Icon ohne Text geht über
  `nur_icon`. Ein 32x8-GIF ohne Text läuft auf der Uhr über das volle Display (getestet). Als Ausweg bei anderem Verhalten dient ein Leerzeichen als `text` (Anleitung im
  Kommentar über den Regeln im Blueprint).
