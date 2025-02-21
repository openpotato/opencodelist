# OpenCodeList-Spezifikation

#### Version 0.3.0

Die Schlüsselwörter "MUSS/MÜSSEN/DARF/DÜRFEN" (*Englisch: "MUST"*), "MUSS/MÜSSEN/DARF/DÜRFEN NICHT" (*Englisch: "MUST NOT"*), "SOLLTE/SOLLTEN" (*Englisch: "SHOULD"*), "SOLLTE/SOLLTEN NICHT" (*Englisch: "SHOULD NOT"*), "KANN/KÖNNEN" (*Englisch: "MAY"*) und "ERFORDERLICH" (*Englisch: "REQUIRED"*) in diesem Dokument sind so zu interpretieren, wie sie in ihrer englischen Übersetzung in [RFC2119 und RFC8174](https://tools.ietf.org/html/bcp14) spezifiziert sind, und nur dann, wenn sie, wie hier, in Großbuchstaben geschrieben sind.

Dieses Spezifikation ist lizenziert unter der [Apache License, Version 2.0](https://opensource.org/license/apache-2-0/).

## Einführung

OpenCodeList definiert ein generisches Standard-Datenformat zur Repräsentation von Code-Listen bzw. Schlüsselverzeichnissen. Basierend auf dem [JSON-Standard](https://datatracker.ietf.org/doc/html/rfc8259) kann dieses Format mit nahezu jeder Programmiersprache leicht erzeugt und gelesen werden. Mit Hilfe des [OpenCodeList Document Schema](https://github.com/openpotato/opencodelist/tree/main/schemas/v0.3/schema.json) können Dokumente im OpenCodeList-Format auf ihre syntaktische Korrektheit hin validiert werden.

OpenCodeList kann zum Austausch von Code-Listen zwischen Diensten oder Anwendungen genutzt werden, als Repräsentationsformat für ofizielle Code-Listen oder als Antwortformat für API-Anfragen (z.B. für RESTful Web-Services).

### Was sind Code-Listen?

Code-Listen bzw. Schlüsselverzeichnisse sind strukturierte Sammlungen von Codes bzw. Schlüsseln, die zur Identifikation und Klassifikation von Daten verwendet werden. Diese Verzeichnisse sind essenziell in zahlreichen Bereichen wie Datenbanken, Verwaltungssystemen, wissenschaftlichen Forschungen und industriellen Anwendungen. Sie dienen dazu, Daten konsistent und effizient zu organisieren, zu speichern und abzurufen.

Code-Listen spielen eine zentrale Rolle bei der Standardisierung und Harmonisierung von Daten. Durch die Verwendung standardisierter Codes können unterschiedliche Systeme und Organisationen Daten einheitlich interpretieren und austauschen. 

Beispiele für Code-Listen sind:

+ Internationale Codes für Staaten, Sprachen und Währungen der International Organization for Standardization (ISO).

+ Nationale Gebietsschlüssel (z.B. Gemeindeschlüssel der Bundesrepublik Deutschland)

+ Nationale und subnationale Schlüssel im öffentlichen Bereich (z.B. Statistikschlüssel der Statistikbehörden)

Im Prinzip lässt sich jede Datenauswahl auf eine Code-Liste abbilden, selbst ein banaler boolescher Wert kann durch die Codes *Nein* und *Ja* repräsentiert werden. 

### Wie sind Code-Listen aufgebaut?

Es gibt kein Naturgesetz, das die Struktur von Code-Listen vorschreibt. In den meisten Fällen kann man sich aber sehr schnell auf eine tabellarische Darstellung einigen. 

Die einfachste Form einer solchen tabellarischen Code-Liste besteht aus zwei Spalten: *Schlüssel* und *Name*.

Hier ein Beispiel für ein Länderverzeichnis:

Schlüssel | Name
--------- | ----
AT        | Österreich
CH        | Schweiz
DE        | Deutschland

Code-Listen können potentiell beliebig viele Spalten enthalten. Hier ein Beispiel für ein Länderverzeichnis mit drei Spalten:

Schlüssel | Kurzname        | Langname
--------- | --------------- | --------
AT        | Österreich      | Republik Österreich
CH        | Schweiz         | Schweizerische Eidgenossenschaft
DE        | Deutschland     | Bundesrepublik Deutschland

Code-Listen können aufeinander verweisen. Hier ein Beispiel für ein Länderverzeichnis mit Verweis auf ein separates Kontinente-Verzeichnis:

Schlüssel | Kurzname        | Langname                         | Kontinent
--------- | --------------- | -------------------------------- | ---------
DE        | Deutschland     | Bundesrepublik Deutschland       | EU 
MA        | Marokko         | Königreich Marokko               | AF
AU        | Australien      | Commonwealth of Australia        | OC

Das passende Kontinente-Verzeichnis könnte dann so aussehen:

Schlüssel | Name
--------- | ----
AF        | Afrika
AM        | Amerikas
AS        | Asien
EU        | Europa
OC        | Ozeanien

Auch können Code-Listen mehr als einen Schlüssel besitzen. Hier ein Beispiel für ein Länderverzeichnis mit verschiedenen ISO3166-Codes:

Alpha2Code | Alpha3Code | NumericCode | Name
---------- | ---------- | ----------- | ----
AT         | AUT        | 040         | Österreich
CH         | CHE        | 756         | Schweiz
DE         | DEU        | 276         | Deutschland

Gehen wir noch einen Schritt weiter mit diesem mehrsprachigen Länderverzeichnis. Hier ist es die Kombination aus Schlüssel und Sprache (ebenfalls durch einen Sprachschlüssel repräsentiert), welche Eindeutigkeit ergibt: 

Schlüssel | Sprache | Name
--------- | ------- | ----
AT        | de      | Österreich
AT        | en      | Austria
CH        | de      | Schweiz
CH        | en      | Switzerland
DE        | de      | Deutschland
DE        | en      | Germany

Neben Text und Zahlen können Code-Listen auch komplexe Spaltentypen definieren, die beispielsweise mit XML, JSON oder HTML gefüllt sind.

### Was macht Code-Listen so komplex?

#### Format und Semantik

Wenn man an Code-Listen denkt, dann erscheint vor dem eigenen geistigen Auge schnell eine Excel-Tabelle mit ein paar Spalten und Zeilen. Aber was bedeuten die Spalten eigentlich? Steht der Code immer in der ersten Spalte? Ist der Inhalt einer Spalte immer als reiner Text zu interpretieren? Und wieso eigentlich Excel? Das ist immerhin ein ziemlich komplexes Datenformat, was nicht gerade gut geeignet ist für eine automatisierte Verarbeitung von Code-Listen. CSV wäre definitiv einfacher zu handhaben, beantwortet die ersten drei Fragen aber leider auch nicht.

#### Versionierung

Code-Listen können sich im Laufe der Zeit ändern. In diesen Fällen gibt es sowohl eine aktuelle Version einer Code-Liste als auch ältere Versionen einer Code-Liste. Im Zuge dieser Änderungen können sich die Anzahl der Codes wie auch die Bedeutung einzelner Codes ändern.

#### Abhängigkeiten

Code-Listen können Abhängigkeiten untereinander haben, d.h. Werte aus einer Code-Liste können auf Codes in einer anderen Code-Liste verweisen. Diese Verweise müssen auch eine mögliche Versionierung der jeweils anderen Code-Liste berücksichtigen.

#### Mehrsprachigkeit

Wenn Code-Listen über Sprachgrenzen hinweg verwendet werden, müssen sie oft für mehrere Sprachen angepasst werden. Ein und derselbe Code hat dann beispielsweise in verschiedenen Sprachen unterschiedliche Bezeichnungen.

#### Benutzerdefinierte Code-Listen

Wenn wir von Code-Listen sprechen, geht es nicht immer nur um standardisierte Codes, die von einem Gremium, einer Organisation oder Institution festgelegt werden, sondern oft auch um Codes, die eine kleine Benutzergruppe für einen bestimmten Bereich oder sogar eine bestimmte Anwendung verwenden möchte. Diese müssen konfliktfrei mit etablierten Standard-Codes koexistieren können.

### Gibt es nicht schon Standards für Code-Listen?

Ja, gibt es: Die [Organization for the Advancement of Structured Information Standards (OASIS)](https://www.oasis-open.org) hat mit [Code List Representation (genericode)](https://docs.oasis-open.org/codelist/genericode/v1.0/genericode-v1.0.html) einen XML-basierten Standard für Code-Listen definiert.

> The OASIS Code List Representation format, “genericode”, is a single model and XML format (with a W3C XML Schema) that can encode a broad range of code list information. The XML format is designed to support interchange or distribution of machine-readable code list information between systems.  Note that genericode is not designed as a run-time format for accessing code list information, and is not optimized for such usage.  Rather, it is designed as an interchange format that can be transformed into formats suitable for run-time usage, or loaded into systems that perform run-time processing using code list information.

Klingt gut, hat aber einen Haken. Es existiert keine standardisierte JSON-Repräsentation von "genericode". 

> Recognizing the custom use of JSON in a tight binding between user-defined processes, the committee sees no purpose served by standardizing a JSON syntax for the genericode vocabulary.

Das bedeutet, es gibt keine offizielle Unterstützung für JSON als Datenformat und somit auch kein offizielles JSON-Schema.

Wer also Code-Listen im XML-Format repräsentieren möchte, dem sei der OASIS-Standard ans Herz gelegt. Wer aber mit JSON arbeiten möchte, der hat mit OpenCodeList eine passende Alternative parat. Natürlich ist OpenCodeList erheblich vom *OASIS Code List Representation Format* beeinflusst und versucht semantisch weitestgehend kompatibel zu sein.

### Warum JSON? XML ist doch gut!

Selbstverständlich ist XML ein tolles, standardisiertes Format, das kaum Wünsche offen lässt. Der Grund für eine JSON-Darstellung von Code-Listen liegt jedoch in dem ausdrücklichen Wunsch, JSON zu verwenden:

+ In der Welt cloud-basierter Dienste hat sich JSON als Payload (zu Deutsch: Nutzdaten) für RESTful-APIs weitestgehend durchgesetzt. Natürlich kann eine solche API auch XML als Payload zurückliefern, generiert damit aber eine zusätzliche Abhängigkeit, sowohl auf Serverseite (Generieren von XML) als auch auf Clientseite (Konsumieren von XML). Statt mit einem Format (JSON) muss beim Einsatz von Code-Listen mit einem zusätzlichen Format (XML) hantiert werden. Das erhöht den Aufwand.

+ JSON ist kompakter, da der Syntax weniger wortreich daherkommt. Bei umfangreichen Code-Listen wirkt sich das positiv auf die Performanz aus. Im Allgemeinen ist JSON schneller zu parsen als XML, da es weniger syntaktischen Ballast mit sich bringt und die JSON-Parser in den meisten modernen Programmiersprachen inzwischen stark optimiert sind.

## Definitionen

### OpenCodeList-Dokument

Ein OpenCodeList-Dokument ist eine in sich geschlossene Ressource, die entweder eine Code-Liste oder eine Code-Listensammlung definiert und beschreibt. Es MÜSSEN mindestens die Eigenschaften `opencodelist` sowie im gegenseitigen Ausschluss `codeList` oder `codeListSet` enthalten sein. Ein OpenCodeList-Dokument verwendet die OpenCodeList-Spezifikation und ist mit ihr konform.

### Code-Listen

Eine Code-Liste ist eine klassische relationale Tabelle mit Spalten und Datenzeilen, wobei mindestens eine Spalte als Schlüssel (Code) dienen sollte. OpenCodeList erlaubt es, generische Code-Listen zu definieren.

Ein OpenCodeList-Dokument mit gesetzter Eigenschaft `codeList` wird auch **CodeList-Dokument** genannt. 

Ein OpenCodeList-Dokument mit gesetzter Eigenschaft `codeList`, aber ohne Daten (also ohne die Untereigenschaft `dataSet`) wird auch **CodeList-Metadokument** genannt, weil es nur Metainformationen enthält.

### Code-Listensammlungen

Eine Code-Listensammlung ist eine Liste von Verweisen auf externe Code-Listen oder externe Code-Listensammlung. Mit einer Code-Listensammlung kann Folgendes abgebildet werden:

+ Zusammenfassen unterschiedlicher Versionen einer Code-Liste in einer Sammlung.

+ Erstellung einer Hierachie von Code-Listensammlungen.

+ Erstellung eines Indexes aller nutzbaren Code-Listen.

Ein OpenCodeList-Dokument mit gesetzter Eigenschaft `codeListSet` wird auch **CodeListSet-Dokument** genannt. 

Ein OpenCodeList-Dokument mit gesetzter Eigenschaft `codeListSet`, aber ohne Verweise (also ohne die Untereigenschaft `referenceSet`) wird auch **CodeListSet-Metadokument** genannt, weil es nur Metainformationen enthält.

## Spezifikation

### Versionierung

Die OpenCodeList-Spezifikation wird nach dem Schema `major.minor.patch` versioniert. Der Major-Minor-Teil der Versionsnummer (z. B. `0.3`) MUSS den Funktionssatz der Spezifikation bezeichnen. Die Patch-Versionen betreffen Fehler in diesem Dokument oder stellen Klarstellungen zu diesem Dokument bereit, nicht zum Funktionsumfang. Werkzeuge, die OpenCodeList in der Version `0.3` unterstützen, MÜSSEN mit allen `0.3.*` Versionen von OpenCodeList kompatibel sein. Die Patch-Version SOLLTE von den Werkzeugen NICHT berücksichtigt werden, so dass zum Beispiel kein Unterschied zwischen `0.3.0` und `0.3.1` gemacht wird.

Ein OpenCodeList-Dokument enthält stets ein obligatorisches Eigenschaft `opencodelist`, das die verwendete Version der OpenCodeList-Spezifikation angibt.

### Format

Ein OpenCodeList-Dokument, das mit der OpenCodeList-Spezifikation konform ist, ist selbst ein JSON-Objekt, das im JSON-Format dargestellt werden kann.

Bei allen Namen von Eigenschaften in der Spezifikation wird zwischen Groß- und Kleinschreibung unterschieden. Das Schema sieht zwei Arten von Eigenschaften vor: Fest definierte Eigenschaften, die einen deklarierten Namen haben, und freie Eigenschaften, deren Namen aber einem bestimmten Muster (*Englisch: pattern*) entsprechen MÜSSEN. Zusätzliche Eigenschaften MÜSSEN innerhalb des enthaltenen JSON-Objekts eindeutige Namen haben.

#### JSON Schema

[JSON Schema](https://json-schema.org/) ist eine Spezifikation zur Definition von JSON-Datenstrukturen. Ein JSON-Schema wird selbst deklarativ durch [JSON](https://www.json.org/) ausgedrückt. Das [OpenCodeList Document Schema](https://github.com/openpotato/opencodelist/tree/main/schemas/v0.3/schema.json) ist ein JSON-Schema für OpenCodeList-Dokumente.

#### Mehrsprachigkeit

OpenCodeList unterstützt Mehrsprachigkeit, d.h. Textspalten in einer Code-Liste können optional mit einem [IETF BCP 47-Sprachtag](https://datatracker.ietf.org/doc/html/rfc5646) versehen werden.   

Beispiel:

``` json
"columns": [
  {
    "id": "col-1",
    "name": "Beschreibung",
    "description": "Column with German description",
    "type": "string",
    "language": "de"
  },
  {
    "id": "col-2",
    "name": "Description",
    "description": "Column with English description",
    "type": "string",
    "language": "en"
  }
],
```

#### Sprachtags

IETF BCP 47 (Best Current Practice 47) ist ein Dokument, das die Regeln und Verfahren zur Erstellung und Nutzung von Sprachkennzeichnungen definiert. Diese Kennzeichnungen werden verwendet, um die Sprache von Inhalten im Internet zu identifizieren. BCP 47 wird von der Internet Engineering Task Force (IETF) verwaltet und besteht aus zwei RFCs (Request for Comments): [RFC 5646](https://datatracker.ietf.org/doc/html/rfc5646) und [RFC 4647](https://datatracker.ietf.org/doc/html/rfc4647).

Sprachtags sind notwendig, um die richtige Lokalisierung und Internationalisierung von Webinhalten und Software zu gewährleisten. Sie ermöglichen es Anwendungen und Diensten, den richtigen Sprachinhalt für Benutzer bereitzustellen und die korrekte Darstellung von sprachspezifischen Daten wie Datumsformaten, Zahlen und Textrichtung zu unterstützen.

Ein BCP 47-Sprachtag besteht aus einer Reihe von Untertags, die durch Bindestriche getrennt sind. Diese Untertags definieren verschiedene Aspekte der Sprache und ihrer Varianten. Die grundlegendsten Komponenten sind:

+ **Primärsprachen-Subtag**: Dies ist ein obligatorischer Subtag und besteht aus einem Zwei- oder Drei-Buchstaben-Code, der die Hauptsprache bezeichnet (z.B. `en` für Englisch, `de` für Deutsch).

+ **Region-Subtag**: Ein optionaler Subtag, der ein Land oder eine Region spezifiziert (z.B. `US` für die Vereinigten Staaten in `en-US`).

+ **Skript-Subtag**: Ein optionaler Subtag, der das Schriftsystem angibt (z.B. `Latn` für das lateinische Alphabet in `zh-Latn`).

+ **Variante-Subtag**: Ein optionaler Subtag, der Sprachvarianten oder Dialekte beschreibt (z.B. `1901` für die traditionelle deutsche Rechtschreibung in `de-1901`).

+ **Extension-Subtag**: Erweiterungen zur Angabe zusätzlicher Informationen.

+ **Privatgebrauch-Subtag**: Ein Bereich, der für die private Nutzung reserviert und nicht standardisiert ist.

#### Datums- und Zeitangaben

Die Formatierung der Datums- und Zeitangaben in OpenCodeList sind, wie von [JSON Schema](https://json-schema.org/understanding-json-schema/reference/string.html#dates-and-times) vorgegeben, durch [RFC 3339, Abschnitt 5.6](https://tools.ietf.org/html/rfc3339#section-5.6) spezifiziert.

Beispiele:

+ **`date-time`** : Datum und Zeit zusammen, z.B. `2024-11-13T20:20:39` oder `2024-11-13T20:20:39+00:00`.
+ **`time`** : Nur Uhrzeit, z.B. `20:20:39` oder `20:20:39+00:00`.
+ **`date`** : Nur Datum, z.B. `2024-11-13`.

#### URIs

Wird ein [Uniform Resource Identifier (URI)](https://tools.ietf.org/html/rfc3986) verlangt, muss je nach Kontext zwischen zwei JSON-String-Formaten unterscheiden werden:

+ Das Format `uri` erwartet, dass der JSON-String ein absoluter URI (Uniform Resource Identifier) ist. Ein absoluter URI enthält alle notwendigen Informationen, um die Ressource unabhängig von ihrem Kontext zu identifizieren. Das bedeutet, dass ein `uri`

    + mit einem Schemas (wie http, https, etc.) beginnt,
    + einen Hostnamen oder eine IP-Adresse enthalten kann,
    + und optional einen Pfad, eine Abfrage und ein Fragment enthalten kann.

+ Das Format `uri-reference` ist flexibler und erlaubt sowohl absolute als auch relative URIs. `uri-reference` kann also eine vollständige URI wie oben beschrieben sein, oder es kann ein relativer Verweis sein, der in einem bestimmten Kontext interpretiert werden muss. Ein relativer URI könnte beispielsweise nur einen Pfad oder sogar nur ein Fragment enthalten.

Beispiele:

+ **`uri`** : 

    + `https://example.com/path/to/resource?query=param#fragment`

+ **`uri-reference`** : 

    + `https://example.com/path/to/resource?query=param#fragment`
	+ `/path/to/resource`
	+ `#fragment`

### Schema

#### OpenCodeList-Dokument

Ein OpenCodeList-Dokument enthält folgende Eigenschaften:

**`$opencodelist`**

:   Ein JSON-String mit der Versionsnummer der OpenCodeList-Spezifikation. **Diese Eigenschaft ist ERFORDERLICH**.

**`$comments`**

:   Eine Liste von Kommentaren. Es MUSS ein JSON-String-Array sein.

**`codeList`**

:   Ein [`codeList`](#codelist-objekt)-Objekt, das die Spaltendefinitionen und den Dateninhalt einer Code-Liste beinhaltet. 

**`codeListSet`**

:   Ein [`codeListSet`](#codelistset-objekt)-Objekt, das eine Sammlung von Verweisen auf externe OpenCodeList-Dokumente definiert. 

Die Eigenschaften `codeList` und `codeListSet` schließen sich gegenseitig aus. **Eine von beiden Eigenschaften ist ERFORDERLICH**. 

Ist die Eigenschaft `codelist` gesetzt, gilt:

+ Ist die Untereigenschaft `dataSet` ebenfalls gesetzt, handelt es sich um ein CodeList-Dokument. 

+ Anderfalls handelt es sich um ein CodeList-Metadokument.

Ist die Eigenschaft `codelistSet` gesetzt, gilt:

+ Ist die Untereigenschaft `referenceSet` ebenfalls gesetzt, handelt es sich um ein CodeListSet-Dokument. 

+ Anderfalls handelt es sich um ein CodeListSet-Metadokument.

#### codeList-Objekt

Das `codeList`-Objekt definiert eine komplette Code-Liste samt Daten:

**`annotation`**

:   Ein [`annotation`](#annotation-objekt)-Objekt mit begleitenden Anmerkungen jeglicher Art.

**`identification`**

:   Ein [`identification`](#identification-objekt)-Objekt mit Metainformationen zur Code-Liste. **Diese Eigenschaft ist ERFORDERLICH**.

**`columnSet`**

:   Ein [`columnSet`](#columnset-objekt)-Objekt, das die Spalten und eindeutigen Schlüssel der Code-Liste definiert. **Diese Eigenschaft ist ERFORDERLICH**.

**`dataSet`**

:   Ein [`dataSet`](#dataset-objekt)-Objekt, das die Datenzeilen der Code-Liste enthält. 

#### codeListSet-Objekt

Das `codeListSet`-Objekt definiert eine Sammlung von Verweisen auf externe OpenCodeList-Dokumente:

**`annotation`**

:   Ein [`annotation`](#annotation-objekt)-Objekt mit begleitenden Anmerkungen jeglicher Art.

**`identification`**

:   Ein [`identification`](#identification-objekt)-Objekt mit Metainformationen zur Verweis-Listensammlung. **Diese Eigenschaft ist ERFORDERLICH**.

**`referenceSet`**

:   Eine Liste von Verweisen. Es MUSS ein JSON-Array mit [`documentRef`](#documentref-objekt)-Objekten sein. **Diese Eigenschaft ist ERFORDERLICH**.

#### annotation-Objekt

Das `annotation`-Objekt ist ein Platz für Anmerkungen jeglicher Art:

**`descriptions`**

:   Ein Liste von ausformulierten Anmerkungen. Es MUSS ein JSON-Array mit [`markup`](#markup-objekt)-Objekten sein. 

**`appInfo`**

:   Ein JSON-Objekt mit maschinell verarbeitbaren Metadaten. Der Inhalt ist frei wählbar und muss lediglich dem JSON-Syntax genügen.

Eine von beiden Eigenschaften ist **ERFORDERLICH**. 

#### markup-Objekt

Das `markup`-Objekt ist ein in einer Auszeichnungssprache (z.B. Markdown) formatierter Textblock:

**`language`**

:   Ein JSON-String mit der Sprache des Textblocks. Dies MUSS ein [IETF BCP 47-Sprachtag](https://datatracker.ietf.org/doc/html/rfc5646) sein.

**`format`**

:   Die Auszeichnungssprache des Textblocks. **Diese Eigenschaft ist ERFORDERLICH**. Es MUSS ein JSON-String mit einem dieser Werte sein:

    + `text`
    + `markdown`
    + `html`

**`content`**

:   Ein JSON-String, der den eigentlichen Inhalt des Textblocks repräsentiert. **Diese Eigenschaft ist ERFORDERLICH**.

#### identification-Objekt

Das `identification`-Objekt enthält Metainformationen zu einem OpenCodeList-Dokument:

**`language`**

:   Ein JSON-String mit der Sprache dieses Dokuments. Dies MUSS ein [IETF BCP 47-Sprachtag](https://datatracker.ietf.org/doc/html/rfc5646) sein.

**`shortName`**

:   Ein JSON-String mit dem Kurznamen des Dokuments. **Diese Eigenschaft ist ERFORDERLICH**.

**`longName`**

:   Ein JSON-String mit dem Langnamen des Dokuments. 

**`description`**

:   Ein JSON-String mit einer kurzen Beschreibung des Dokuments. 

**`tags`**

:   Ein JSON-String-Array mit Tags bzw. Schlüsselwörtern, die den Inhalt des Dokuments bestimmen.

**`version`**

:   Ein JSON-String mit der Version des Dokuments.

**`changeLog`**

:   Erlaubt es, die Änderungen gegenüber vorherigen Versionen dieses Dokuments zu dokumentieren. Es MUSS ein JSON-Array mit JSON-Strings sein.

**`publishedAt`**

:   Ein JSON-String im Format `date-time` mit dem Zeitpunkt der Veröffentlichung dieses Dokuments.

**`publisher`**

:   Ein [`publisher`](#publisher-objekt)-Objekt mit Informationen über die Stelle, die für die Veröffentlichung und/oder Pflege des Dokuments zuständig ist.

**`validFrom`**

:   Ein JSON-String im Format `date-time`, der den Zeitpunkt definiert, ab dem dieses Dokument gültig ist.

**`validTo`**

:   Ein JSON-String im Format `date-time`, der den Zeitpunkt definiert, bis zu dem dieses Dokument noch gültig ist.

**`canonicalUri`**

:   Ein JSON-String im Format `uri`. Diese URI identifiziert alle Versionen (zusammen) dieses Dokuments eindeutig. **Diese Eigenschaft ist ERFORDERLICH**.

**`canonicalVersionUri`**

:   Ein JSON-String im Format `uri`. Diese URI identifiziert eine bestimmte Version dieses Dokuments. **Diese Eigenschaft ist ERFORDERLICH**.

**`locationUrls`**

:   Ein JSON-Array mit JSON-String-Werten im Format `uri`. Diese URIs sind vorgeschlagene Abruforte für dieses Dokument, im OpenCodeList-Format.

**`alternateLanguageLocations`**

:   Ein JSON-Array mit [`localizedUri`](#localizeduri-objekt)-Objekten, welches übersetzte Versionen dieses OpenCodeList-Dokument und deren vorgeschlagene Speicherorte auflistet.

**`alternateFormatLocations`**

:   Ein JSON-Array mit [`mimeTypedUri`](#mimetypeduri-objekt)-Objekten, welches andere Formate als OpenCodeList (z.B. CSV) und deren vorgeschlagene Speicherorte auflistet.

Dieses Objekt KANN erweitert werden.

Beispiel:

``` json
"identification": {
  "language": "en",
  "shortName": "GermanFederalStateCodes",
  "longName": "ISO 3166-2 Codes for Germany",
  "description": "ISO 3166-2 Codes for the federal states of Germany",
  "publishedAt": "2017-11-24T12:00:00",
  "publisher": {
    "shortName": "ISO",
    "longName": "International Organization for Standardization",
    "url": "https://www.iso.org/"
  },
  "version": "2017-11-23",
  "canonicalUri": "urn:iso:std:iso:3166-2:de",
  "canonicalVersionUri": "urn:iso:std:iso:3166-2:de:2017-11-23",
  "locationUrls": [
    "https://iso.example.com/germany.federal-state-codes-2017-11-23.json"
  ]
}
```

#### publisher-Objekt

Das `publisher`-Objekt definiert den Herausgeber (Behörde, Institution, Personenkreis, etc.), welcher für die Veröffentlichung und/oder Pflege des Dokuments verantwortlich ist:

**`shortName`**

:   Ein JSON-String mit dem Kurznamen des Herausgebers. **Diese Eigenschaft ist ERFORDERLICH**.

**`longName`**

:   Ein JSON-String mit dem Langnamen des Herausgebers. 

**`identifier`**

:   Ein [`identifier`](#identifier-objekt)-Objekt mit zusätzlichen Informationen (z.B. Eintrag in ein Register) zur Identifikation des Herausgebers.

**`url`**

:   Ein JSON-String im Format `uri`. Diese URI ist ein Verweis auf zusätzliche externe Informationen (z.B. eine Webseite) zum des Herausgeber.

#### localizedUri-Objekt

Das `localizedUri`-Objekt definiert eine Referenz auf eine lokalisierte Ressource:

**`language`**

:   Ein JSON-String mit der Sprache der referenzierten Ressource. Dies MUSS ein [IETF BCP 47-Sprachtag](https://datatracker.ietf.org/doc/html/rfc5646) sein. **Diese Eigenschaft ist ERFORDERLICH**.

**`url`**

:   Ein JSON-String im Format `uri`. Diese URI ist der Abrufort der referenzierten Ressource. **Diese Eigenschaft ist ERFORDERLICH**.

#### mimeTypedUri-Objekt

Das `mimeTypedUri`-Objekt definiert eine Referenz auf eine Ressource in einem vorgegebenen Format:

**`mimeType`**

:   Ein JSON-String mit einem standardisierten [MIME-Typ (Multipurpose Internet Mail Extensions)](https://developer.mozilla.org/en-US/docs/Web/HTTP/MIME_types). **Diese Eigenschaft ist ERFORDERLICH**.

**`url`**

:   Ein JSON-String im Format `uri`. Diese URI ist der Abrufort der referenzierten Ressource. **Diese Eigenschaft ist ERFORDERLICH**.

#### identifier-Objekt

Das `identifier`-Objekt repräsentiert einen allgemeinen Identifikator:

**`value`**

:   Ein JSON-String mit einem Schlüssel, Code oder einer ID. **Diese Eigenschaft ist ERFORDERLICH**.

**`source`**

:   Ein [`identifierSource`](#identifiersource-objekt)-Objekt mit Quellangaben zu einem Identifikator.

#### identifierSource-Objekt

Das `identifierSource`-Objekt ist eine Quellangabe für einen allgemeinen Identifikator:

**`shortName`**

:   Ein JSON-String mit dem Kurznamen der Quelle. **Diese Eigenschaft ist ERFORDERLICH**.

**`longName`**

:   Ein JSON-String mit dem Langnamen der Quelle. 

**`url`**

:   Ein JSON-String im Format `uri`. Diese URI ist ein Verweis auf zusätzliche externe Informationen (z.B. eine Webseite) zur Quelle.

#### columnSet-Objekt

Das `columnSet`-Objekt definiert Spalten und eindeutige Schlüssel einer Code-Liste:

**`columns`**

:   Definiert die Spalten der Code-Liste. Es MUSS ein JSON-Array mit [`column`](#column-objekt)-Objekten sein. **Diese Eigenschaft ist ERFORDERLICH**.

**`keys`**

:   Definiert die eindeutigen Schlüssel der Code-Liste. Es MUSS ein JSON-Array mit [`key`](#key-objekt)-Objekten sein. **Diese Eigenschaft ist ERFORDERLICH**.

**`defaultKey`**

:   Definiert bei mehreren Schlüsseln den Standardschlüssel, also jenen Schlüssel aus `keys`, der bevorzugt als Code-Quelle genutzt werden soll. Es MUSS ein [`defaultKey`](#defaultkey-objekt)-Objekt sein.

**`foreignKeys`**

:   Definiert interne Referenzen sowie externe Referenzen zu anderen Code-Listen. Es MUSS ein JSON-Array mit [`foreignKey`](#foreignkey-objekt)-Objekten sein.

#### column-Objekt

Das `column`-Objekt definiert eine Spalte für einer Code-Liste:

**`id`**

:   Ein JSON-String mit der ID der Spalte. **Diese Eigenschaft ist ERFORDERLICH**.

**`name`**

:   Ein JSON-String mit dem dem Namen der Spalte. **Diese Eigenschaft ist ERFORDERLICH**.

**`description`**

:   Ein JSON-String mit einer kurzen Beschreibung der Spalte. 

**`type`**

:   Definiert den Datentyp der Spalte. **Diese Eigenschaft ist ERFORDERLICH**. Es MUSS ein JSON-String mit einem dieser Werte sein:

    + `string`
    + `enum`
    + `enum-set`
    + `integer`
    + `number`
    + `bool`
    + `time`
    + `date`
    + `date-time`
    + `object`

**`nullable`**

:   Eine JSON-Boolean, der definiert, ob diese Spalte auch JSON-NULLs enthalten darf.

**`optional`**

:   Eine JSON-Boolean, der definiert, ob diese Spalte optional ist, sie in einer Datenzeile also auch weggelassen werden kann.

Abhängig vom Wert in `type` sind weitere Eigenschaften verfügbar.

##### string

Ein JSON-String. Die folgenden zusätzlichen Schema-Eigenschaften sind verfügbar:

+ **`minLength`** : Eine JSON-Nummer im Format `integer` mit der minimalen zulässigen Anzahl an Zeichen
+ **`maxLength`** : Eine JSON-Nummer im Format `integer` mit der maximalen zulässigen Anzahl an Zeichen
+ **`pattern`** : Eine JSON-String mit einem regulären Ausdruck, der stets zu den Werten in dieser Spalte passen muss. Der verwendete Syntax für reguläre Ausdrücke entspricht dem Syntax aus JavaScript ([ECMAScript 2024 language specification](https://ecma-international.org/publications-and-standards/standards/ecma-262/)), so wie er in [JSON SChmewa](https://json-schema.org/understanding-json-schema/reference/regular_expressions) beschrieben und genutzt wird.
+ **`language`** : Ein JSON-String mit der Sprache für die Inhalte dieser Spalte. Dies MUSS ein [IETF BCP 47-Sprachtag](https://datatracker.ietf.org/doc/html/rfc5646) sein.

##### enum

Einen JSON-String, der ein Aufzählung repräsentiert. Die folgenden zusätzlichen Schema-Eigenschaften sind verfügbar:

+ **`members`** : Definiert die möglichen Werte der Aufzählung. Es MUSS ein JSON-Array mit [`enumMember`](#enummember-objekt)-Objekten sein. **Diese Eigenschaft ist ERFORDERLICH**.
+ **`language`** : Ein JSON-String mit der Sprache für die Inhalte dieser Spalte. Dies MUSS ein [IETF BCP 47-Sprachtag](https://datatracker.ietf.org/doc/html/rfc5646) sein.

##### enum-set

Einen JSON-Array, der eine Aufzählungmenge repräsentiert. Die folgenden zusätzlichen Schema-Eigenschaften sind verfügbar:

+ **`members`** : Definiert die möglichen Werte der Aufzählungmenge. Es MUSS ein JSON-Array mit [`enumMember`](#enummember-objekt)-Objekten sein. **Diese Eigenschaft ist ERFORDERLICH**.
+ **`language`** : Ein JSON-String mit der Sprache für die Inhalte dieser Spalte. Dies MUSS ein [IETF BCP 47-Sprachtag](https://datatracker.ietf.org/doc/html/rfc5646) sein.

##### integer

Repräsentiert eine JSON-Nummer im Format `integer`. Die folgenden zusätzlichen Schema-Eigenschaften sind verfügbar:

+ **`minValue`** : Eine JSON-Nummer im Format `integer` mit dem zulässigen Minimalwert.
+ **`maxValue`** : Eine JSON-Nummer im Format `integer` mit dem zulässigen Minimalwert.

##### number

Repräsentiert eine JSON-Nummer im Format `number`. Die folgenden zusätzlichen Schema-Eigenschaften sind verfügbar:

+ **`minValue`** : Eine JSON-Nummer im Format `number` mit dem zulässigen Minimalwert.
+ **`exclusiveMinValue`** : Eine JSON-Nummer im Format `number`, welche die exklusive Untergrenze definiert.
+ **`maxValue`** : Eine JSON-Nummer im Format `number` mit dem zulässigen Minimalwert.
+ **`exclusiveMaxValue`** : Eine JSON-Nummer im Format `number`, welche die exklusive Obergrenze definiert.

##### boolean

Repräsentiert eine JSON-Nummer im Format `boolean`. Es sind keine zusätzlichen Schema-Eigenschaften verfügbar.

##### date

Repräsentiert ein JSON-String im Format `date`. Die folgenden zusätzlichen Schema-Eigenschaften sind verfügbar:

+ **`minValue`** : Ein JSON-String im Format `date` mit dem zulässigen Minimalwert.
+ **`maxValue`** : Ein JSON-String im Format `date` mit dem zulässigen Minimalwert.

##### date-time

Repräsentiert ein JSON-String im Format `date-time`. Die folgenden zusätzlichen Schema-Eigenschaften sind verfügbar:

+ **`minValue`** : Ein JSON-String im Format `date-time` mit dem zulässigen Minimalwert.
+ **`maxValue`** : Ein JSON-String im Format `date-time` mit dem zulässigen Minimalwert.

##### time

Repräsentiert ein JSON-String im Format `time`. Die folgenden zusätzlichen Schema-Eigenschaften sind verfügbar:

+ **`minValue`** : Ein JSON-String im Format `time` mit dem zulässigen Minimalwert.
+ **`maxValue`** : Ein JSON-String im Format `time` mit dem zulässigen Minimalwert.

##### object

Repräsentiert ein eingebettetes JSON-Objekt. Die folgenden zusätzlichen Schema-Eigenschaften sind verfügbar:

+ **`schema`** : Entweder ein JSON-String im Format `uri`, das den Abrufort des JSON-Schemas für diese Spalte definiert, oder ein JSON-Objekt mit einem eingebetteten JSON-Schema.

#### enumMember-Objekt

Das `enumMember`-Objekt definiert einen Wert in einer Aufzählung:

**`value`**

:   Ein JSON-String mit dem Aufzählungswert. **Diese Eigenschaft ist ERFORDERLICH**.

**`description`**

:   Ein JSON-String mit der Beschreibung des Aufzählungswertes.

#### key-Objekt

Das `key`-Objekt definiert einen eindeutigen Schlüssel für die Code-Liste:

**`id`**

:   Ein JSON-String mit der eindeutigen ID des Schlüssels. **Diese Eigenschaft ist ERFORDERLICH**.

**`name`**

:   Ein JSON-String mit dem Namen des Schlüssels.

**`description`**

:   Ein JSON-String mit einer kurzen Beschreibung des Schlüssels.

**`columnIds`**

:   Ein JSON-String-Array mit IDs, die jeweils auf ein [`column`](#column-objekt)-Objekt verweisen. **Diese Eigenschaft ist ERFORDERLICH**.

Beispiel:	

``` json
"keys": [
  {
    "id": "codeKey",
    "name": "My primary key",
    "columnIds": [
      "code"
    ]
  }
]
```

#### defaultKey-Objekt

Das `defaultKey`-Objekt definiert den Standardschlüssel für die Code-Liste:

**`keyId`**

:   Ein JSON-String mit einer ID, die auf ein [`key`](#key-objekt)-Objekt in dieser Code-Liste verweist. **Diese Eigenschaft ist ERFORDERLICH**.

Beispiel:	

``` json
"defaultKey": {
  "keyId": "codeKey"
}
```

#### foreignKey-Objekt

Das `foreignKey`-Objekt definiert einen Fremdschlüssel zur einer externen Code-Liste:

**`id`**

:   Ein JSON-String mit der eindeutigen ID des Fremdschlüssels. **Diese Eigenschaft ist ERFORDERLICH**.

**`name`**

:   Ein JSON-String mit dem Namen des Fremdschlüssels.

**`description`**

:   Ein JSON-String mit einer kurzen Beschreibung des Fremdschlüssels.

**`columnIds`**

:   Ein JSON-String-Array mit IDs, die jeweils auf ein [`column`](#column-objekt)-Objekt verweisen. **Diese Eigenschaft ist ERFORDERLICH**.

**`keyRef`**

:   Ein `keyRef`-Objekt, das auf einen Schlüssel in einer externe Code-Liste verweist. **Diese Eigenschaft ist ERFORDERLICH**.

Beispiel:	

``` json
"foreignKeys": [
  {
    "id": "foreignKey",
    "name": "My foreign key",
    "columnIds": [
	  "federalState"
    ],
    "keyRef": {
	  "codeListRef": {
	    "canonicalUri": "urn:iso:std:iso:3166-2:de",
	    "canonicalVersionUri": "urn:iso:std:iso:3166-2:de:2017-11-23",
  	    "locationUrls": [
	      "https://iso.example.com/germany.federal-state-codes-2017-11-23.json"
		]
	  },
	  "keyId": "codeKey"
    }
  }
]
```

#### keyRef-Objekt

Das `keyRef`-Objekt definiert eine Referenz auf einen Schlüssel in einer externen Code-Liste:

**`codeListRef`**

:   Ein [`codeListRef`](#codelistref-objekt)-Objekt, das auf eine externe Code-Liste verweist. **Diese Eigenschaft ist ERFORDERLICH**.

**`keyId`**

:   Ein JSON-String mit einer ID, die auf ein [`key`](#key-objekt)-Objekt in der unter `codeListRef` definierten externen Code-Liste verweist. **Diese Eigenschaft ist ERFORDERLICH**.

#### codeListRef-Objekt

Das `codeListRef`-Objekt definiert einen Verweis auf eine externe CodeList-Dokument:

**`canonicalUri`**

:   Ein JSON-String im Format `uri`. Diese URI identifiziert alle Versionen (zusammen) der referenzierten Code-Liste eindeutig. **Diese Eigenschaft ist ERFORDERLICH**.

**`canonicalVersionUri`**

:   Ein JSON-String im Format `uri`. Diese URI identifiziert eine bestimmte Version der referenzierten Code-Liste. 

**`locationUrls`**

:   Ein JSON-Array mit JSON-String-Werten im Format `uri`. Diese URIs sind vorgeschlagene Abruforte für die referenzierte Code-Liste, im OpenCodeList-Format.

#### dataSet-Objekt

Das `dataSet`-Objekt enthält die Daten einer Code-Liste:

**`rows`**

:   Definiert die Datenzeilen der Code-Liste. Es MUSS ein JSON-Array mit [`row`](#row-objekt)-Objekten sein. **Diese Eigenschaft ist ERFORDERLICH**.

#### row-Objekt

Das `row`-Objekt definiert eine Datenzeile in einer Code-Liste. Ein `row`-Objekt ist ein JSON-Objekt, dessen Eigenschaften sich aus den Spaltendefinitionen der [`column`](#column-objekt)-Objekte ergeben:

+ Es MÜSSEN mindestens soviele Eigenschaften vorhanden sein wie es [`column`](#column-objekt)-Objekte mit der Eigenschaft `"optional": false` gibt.

+ Es DÜRFEN höchstens soviele Eigenschaften vorhanden sein wie es [`column`](#column-objekt)-Objekte ingesamt gibt.

+ Für jede Eigenschaft gilt:

    + Der Name enspricht einer ID, die auf ein [`column`](#column-objekt)-Objekt verweist
	
	+ Der Wert kann ein JSON-Null-Wert, ein JSON-String, ein JSON-Numeric, ein JSON-Objekt oder ein JSON-Array sein, muss aber mit dem Spaltentyp des [`column`](#column-objekt)-Objekts korrespondieren.
	
Beispiel:	

``` json
"codeList": {
  "identification": { ... },
  "columnSet": {
    "columns": [
      {
        "id": "code",
        "name": "Code",
        "type": "string"
      },
      {
        "id": "name",
        "name": "Name",
        "type": "string"
      },
      {
        "id": "population",
        "name": "Population",
        "type": "intger"
      }
    }
  },
  "dataSet": {
    "rows": [
      {
        "code": "BW",
        "name": "Baden-Württemberg"
		"population": 11280000
      },
      {
        "code": "BY",
        "name": "Bavaria"
		"population": 7450000
      }
    ]
  }
}
```

#### documentRef-Objekt

Das `documentRef`-Objekt definiert einen Verweis auf ein externes OpenCodeList-Dokument:

**`type`**

:   Definiert den Dokumententyp des Verweises. **Diese Eigenschaft ist ERFORDERLICH**. Es MUSS ein JSON-String mit einem dieser Werte sein:

    + `codeListRef`: Verweis auf CodeList-Dokument
    + `codeListSetRef`: Verweis auf CodeListSet-Dokument

**`annotation`**

:   Ein [`annotation`](#annotation-objekt)-Objekt mit benutzerdefinierten Anmerkungen jeglicher Art.

**`canonicalUri`**

:   Ein JSON-String im Format `uri`. Diese URI identifiziert alle Versionen (zusammen) des referenzierten Dokumentes eindeutig. **Diese Eigenschaft ist ERFORDERLICH**.

**`canonicalVersionUri`**

:   Ein JSON-String im Format `uri`. Diese URI identifiziert eine bestimmte Version der referenzierten Dokumentes.

**`locationUrls`**

:   Ein JSON-Array mit JSON-String-Werten im Format `uri`. Diese URIs sind vorgeschlagene Abruforte für das referenzierte Dokument, im OpenCodeList-Format.

### Erweiterung der Spezifikation

Die OpenCodeList-Spezifikation kann an bestimmten Stellen um zusätzliche Daten erweitert werden.

Die Eigenschaften der Erweiterungen sind als freie Eigenschaften implementiert, denen immer ein `x-` vorangestellt werden MUSS (z.B. `x-external-id`). Der Wert kann eine Zeichenfolge, eine Zahl, ein boolescher Wert, Null, ein Objekt oder ein Array sein.

Die Erweiterungen können von den verfügbaren Werkzeugen unterstützt werden oder auch nicht. Idealerweise können diese Werkzeuge erweitert werden, um die gewünschte Unterstützung hinzuzufügen (z.B. bei Open Source-Projekten).

Beispiel:

``` json
"identification": {
  "shortName": "GermanFederalStateCodes",
  "publisher": {
	"shortName": "ISO",
	"longName": "International Organization for Standardization",
    "x-contact-name": "ISO Central Secretariat",
    "x-contact-address": "Chemin de Blandonnet 8, 1214 Geneva, Switzerland",
    "x-contact-email": "central@iso.org "
  }
}
```
