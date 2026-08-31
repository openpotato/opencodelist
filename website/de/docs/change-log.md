# Änderungslog

Wir halten uns dabei weitesgehend an die Empfehlungen aus dem Community-Projekt [Keep a Changelog](https://keepachangelog.com/de).

## OpenCodeList-Spezifikation

### 0.4.0 <small>_ 31. August 2026</small>

**Hinzugefügt:**

+ Bessere Unterstützung für lokalisierte Texte:

    + Namen und Beschreibungen von Spalten, Schlüsseln und Fremdschlüsseln sowie Beschreibungen von Aufzählungswerten können neben einfachen JSON-Strings nun auch als sprachabhängige Werte mit BCP-47-Sprachtags angegeben werden.
    + Werte von Spalten des Typs `string` können ebenfalls als lokalisierte Werte mit BCP-47-Sprachtags angegeben werden.

+ Für `identification.language` wurde die Semantik einer Standardsprache für die Inhalte des Dokuments definiert. Spalten vom Typ `string`, `enum` und `enum-set` können diese über ihre Eigenschaft `language` überschreiben.

**Geändert:**

+ Breaking Change: Für Spalten vom Typ `document` wurde die Eigenschaft `schema` durch `schemaUri` ersetzt. Eingebettete JSON-Schemas als Spaltendefinition werden nicht mehr unterstützt.
+ Breaking Change: Werte des Spaltentyps `time` repräsentieren nun ausdrücklich eine lokale Uhrzeit ohne Datum und UTC-Offset im Format `HH:mm:ss` mit optionalen Sekundenbruchteilen von bis zu sieben Stellen.
+ Breaking Change: Spalten, die Bestandteil eines Schlüssels sind, MÜSSEN `optional: false` und `nullable: false` sein. Die Kombination der Schlüsselwerte MUSS innerhalb des Datensatzes eindeutig sein.
+ Die Regeln für Fremdschlüssel wurden präzisiert. Anzahl und Datentypen der lokalen Fremdschlüsselspalten MÜSSEN mit den Spalten des referenzierten Schlüssels übereinstimmen; die Zuordnung erfolgt anhand der Reihenfolge.
+ Die Regeln für Datenzeilen wurden präzisiert. Nicht optionale Spalten MÜSSEN vorhanden sein, optionale Spalten DÜRFEN fehlen und Werte MÜSSEN dem jeweiligen Spaltentyp und dessen Einschränkungen entsprechen.
+ Das `annotation`-Objekt wurde dahingehend präzisiert, dass `descriptions` und `appInfo` gemeinsam angegeben werden DÜRFEN.
+ Die zulässigen Erweiterungspunkte mit benutzerdefinierten `x-`-Eigenschaften wurden auf `identification` und `publisher` konkretisiert.

**Entfernt:**

+ Die Eigenschaft `referenceSet` eines `codeListSet`-Objekts ist nicht mehr erforderlich. Dadurch kann eine Code-Listensammlung auch als reines CodeListSet-Metadokument ohne Referenzen veröffentlicht werden.

Außerdem wurde eine Vielzahl kleiner (und großer) Fehler auf der Webseite bereinigt.

### 0.3.0 <small>_ 19. Februar 2025</small>

**Geändert:**

+ Breaking Change: Die Eigenschaft `canonicalVersionUri` wurde für das Teilschema `identification` als erforderlich markiert.
+ Breaking Change: Für die Teilschemata `documentRef` und `keyRef` wurde jeweils die erforderliche Eigenschaft `canonicalVersionUri` in `canonicalUri` geändert.

### 0.2.0 <small>_ 25. Januar 2025</small>

+ Erste Veröffentlichung
