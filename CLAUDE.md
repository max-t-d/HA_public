# Arbeitsanweisung für Claude in diesem Repo

Dieses Repo (`HA_public`) ist **öffentlich**. Es enthält einfache Home-Assistant-
Blueprints und ähnliche kleine Bausteine. Vollständige Integrationen leben in eigenen
Repos, rein private Setups im privaten Repo `HA_automations`.

## Regeln

1. **Nichts Privates.** Keine Secrets, Tokens, URLs interner Dienste, Personen- oder
   Adressdaten, keine Geräte-IDs und keine privaten Entity-Listen. Beispielwerte in
   Blueprints (Regeln, Ignorier-Listen, Icons) bleiben neutral. Vor jedem Commit
   kurz prüfen, ob etwas Privates mit hineingerutscht ist.
2. **Struktur:** `blueprints/automation/<name>/<name>.yaml` (Begleitdateien wie
   Icon-Listen daneben), `blueprints/script/<name>/`, `snippets/<name>/`,
   `docs/<name>.md`. Neue Bausteine bekommen eine Zeile in der Tabelle der README.
3. **Blueprints:** `source_url` zeigt auf die Blob-URL der Datei in diesem Repo,
   damit der Import und "erneut importieren" funktionieren. Kommentare im Blueprint
   erklären Stolperstellen direkt an der betreffenden Eingabe.
4. **YAML validieren:** Vor dem Commit per Python-`yaml.safe_load` mit tolerantem
   Constructor für `!input` prüfen. Templates, wenn möglich, offline mit
   Testdaten rendern.
5. **Doku aktuell halten:** Ändert sich Verhalten oder eine Eingabe, die
   `docs/<name>.md` im selben Commit anpassen.
6. **Commits:** klein, auf Deutsch, kurz und auf das "Warum" fokussiert.
