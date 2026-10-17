# HA_public

Öffentliche Home-Assistant-Bausteine von Max: einfache Blueprints und ähnliche
kleine, in sich geschlossene Dinge, die ich teilen oder bequem per URL importieren
möchte.

## Was hier liegt, was nicht

| Gehört hierher | Gehört woanders hin |
|---|---|
| Blueprints (Automationen, Skripte) | Vollständige Integrationen: eigene Repos (z. B. CardDAV Birthday) |
| Kleine Snippets (Templates, Dashboard-Karten, Skripte) | Rein private Setups (z. B. Lichtsteuerung): privates Repo |

## Struktur

```
blueprints/automation/<name>/   Automations-Blueprints, eine Datei plus ggf. Begleitdateien
blueprints/script/<name>/       Skript-Blueprints (bei Bedarf)
snippets/<name>/                Templates, Dashboard-Karten u. ä. (bei Bedarf)
docs/<name>.md                  Dokumentation pro Baustein
```

Der Aufbau von `blueprints/` entspricht dem Ordner `config/blueprints/` in Home
Assistant. Eine Datei lässt sich daher direkt dorthin kopieren.

## Inhalt

| Baustein | Beschreibung | Import |
|---|---|---|
| [AWTRIX Events](docs/awtrix-events.md) | Kalendertermine nach Regelliste (Icon, Text, Zeitfenster) auf eine AWTRIX-Matrix | [![Blueprint importieren](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fmax-t-d%2FHA_public%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fawtrix_events%2Fawtrix_events.yaml) |

## Blueprint importieren

Einstellungen > Automationen & Szenen > Blueprints > Blueprint importieren und die
GitHub-URL der Blueprint-Datei einfügen (der "Blob"-Link aus dem Browser genügt).
Home Assistant importiert einmalig. Änderungen kommen über "Blueprint erneut
importieren" im Menü des Blueprints an.

## Hinweise

- Lizenz: [MIT](LICENSE).
- Alles hier ist ohne Gewähr, Beispielwerte (Icons, MQTT-Prefix, Regeln) sind an
  das eigene Setup anzupassen.
- Keine Zugangsdaten, Tokens oder private Entity-Listen in dieses Repo.
