<properties title="Abfragen mit DocumentDB-SQL" pageTitle="Abfragen mit DocumentDB-SQL | Azure" description="DocumentDB unterst&uuml;tzt die Abfrage von Dokumenten mit SQL-&auml;hnlicher Grammatik &uuml;ber hierarchische JSON-Dokumente, ohne dass ein explizites Schema oder die Erstellung sekund&auml;ren Indizes erforderlich ist." metaKeywords="" services="documentdb"  documentationCenter="" solutions="data-management" authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="spelluru" />

# Abfragen von DocumentDB

Azure DocumentDB unterstützt Dokumentabfragen mit vertrautem SQL (Structured Query Language) über hierarchische JSON-Dokumente. DocumentDB funktioniert ohne Schema. Die direkte Integration des JSON-Datenmodells in das Datenbankmodul ermöglicht eine automatische Indexierung von JSON-Dokumenten ohne explizite Schemas oder die Erstellung sekundärer Indizes.
Bei der Entwicklung der Abfragesprache für DocumentDB hatten wir zwei Hauptziele:

-   **SQL-Nutzung** – Wir wollten die SQL-Sprache verwenden, anstatt eine neue Abfragesprache zu erfinden. Immerhin ist SQL eine der meistverwendeten und beliebtesten Abfragesprachen. Die SQL-Sprache von Azure DocumentDB bietet ein formelles Programmiermodell für umfassende Abfragen auf JSON-Dokumente.
-   **SQL-Erweiterung** – Als JSON-Dokumentdatenbank, die JavaScript direkt im Datenbankmodul ausführen kann, wollten wir das JavaScript-Programmiermodell als Fundament für unsere SQL-Abfragesprache verwenden. Die SQL-Abfragesprache von Azure DocumentDB verwendet Typsystem, Ausdrucksauswertung und Funktionsaufrufe von JavaScript. Dies wiederum bietet ein natürliches Programmiermodell für relationale Projektionen, hierarchische Navigation in JSON-Dokumenten, Selbstverknüpfungen, Aufrufe komplett in JavaScript geschriebener benutzerdefinierter Funktionen (User Defined Function UDF) sowie weitere Features.

Diese Funktionen sind unserer Ansicht nach der Schlüssel zur Minimierung der Reibung zwischen Anwendung und Datenbank und sind entscheidend für die Produktivität von Entwicklern.

Dieses Lernprogramm enthält eine Einführung in die Funktionalitäten und Grammatik der DocumentDB-Abfragesprache anhand von Beispielen. Außerdem betrachten wir DocumentDB-Abfragen mithilfe der REST-API und -SDKs (inkl. LINQ).

# Erste Schritte

Um die DocumentDB-SQL-Sprache in Aktion zu sehen, beginnen wir mit einigen einfachen JSON-Dokumenten und betrachten einige einfache Abfragen für diese Dokumente. Betrachten Sie diese beiden JSON-Dokumente über zwei Familien. Beachten Sie, dass wir in DocumentDB keine Schemas oder sekundäre Indizes explizit erstellen müssen. Wir fügen einfach die JSON-Dokumente in eine DocumentDB-Sammlung ein und führen anschließend Abfragen aus.
Hier haben wir ein einfaches JSON-Dokument für die Familie Andersen mit Eltern, Kindern (und deren Haustieren), Adresse und Registrierungsinformationen. Das Dokument enthält Zeichenfolgen, Zahlen, boolesche Werte, Arrays und verschachtelte Eigenschaften.

**Dokument**

    {
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
    }

Hier ist ein zweites Dokument mit einem kleinen Unterschied – `givenName` und `familyName` werden anstelle von `firstName` und `lastName` verwendet.

**Dokument**

    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam", 
                 "givenName": "Jesse", 
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
                "familyName": "Miller", 
                 "givenName": "Lisa", 
                 "gender": "female", 
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    }

Wir werden nun einige Abfragen an die Daten ausführen, um einige der Schlüsselaspekte von DocumentDB-SQL besser zu verstehen. Die folgende Abfrage gibt z. B. die Dokumente zurück, in denen das ID-Feld `AndersenFamily` enthält. Da es sich um `SELECT *` handelt, ist die Rückgabe der Abfrage das komplette JSON-Dokument:

**Query**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Ergebnisse**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
    }]

Betrachten wir nun einen Fall, in dem wir die JSON-Ausgabe nach unseren Wünschen formatieren möchten. Diese Abfrage projiziert ein neues JSON-Objekt mit 2 ausgewählten Feldern (Name und Stadt), wenn die Stadt in der Adresse denselben Namen wie der Staat hat. In diesem Fall stimmt "NY, NY" überein.

**Query**

    SELECT {"Name":f.id, "City":f.address.city} AS Family 
    FROM Families f 
    WHERE f.address.city = f.address.state

**Ergebnisse**

    [{
        "Family": {
            "Name": "WakefieldFamily", 
            "City": "NY"
        }
    }]

Die nächste Abfrage gibt alle Vornamen von Kindern der Familie zurück, deren ID `WakefieldFamily` entspricht.

**Query**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'

**Ergebnisse**

    [
      { "givenName": "Jesse" }, 
      { "givenName": "Lisa"}
    ]

Beachten Sie einige der bemerkenswerten Aspekte der DocumentDB-Abfragesprache, die wir in den bisherigen Beispielen gesehen haben:

-   Da DocumentDB-SQL mit JSON-Werten arbeitet, werden baumförmige Entitäten anstelle von Spalten und Zeilen verarbeitet. Daher können Sie auf Knoten in der Baumstruktur in beliebiger Tiefe verweisen, z. B. `Node1.Node2.Node3…..Nodem`, ähnlich wie relationales SQL mit einem zweiteiligen Verweis auf `<table>.<column>`.
-   Die Sprache arbeitet mit schemalosen Daten. Daher muss das Typsystem dynamisch gebunden werden. Derselbe Ausdruck kann unterschiedliche Typen in unterschiedlichen Dokumenten ergeben. Das Ergebnis einer Abfrage ist ein gültiger JSON-Wert, aber nicht garantiert innerhalb eines festen Schemas.
-   DocumentDB unterstützt nur strikte JSON-Dokumente. Typsystem und Ausdrücke sind also auf JSON-Typen beschränkt. In der [JSON-Spezifikation] (http://www.json.org/) finden Sie weitere Informationen.
-   Eine DocumentDB-Sammlung ist ein schemaloser Container mit JSON-Dokumenten. Die Beziehungen in Datenentitäten innerhalb und zwischen Dokumenten in einer Sammlung werden implizit durch Einschluss erfasst, und nicht durch PS-FS-Beziehungen. Dieser Aspekt ist wichtig angesichts der später in diesem Dokument besprochenen dokumentinternen Verknüpfungen.

# DocumentDB-Indexierung

Bevor wir die DocumentDB-SQL-Sprache besser kennenlernen, sollten Sie sich mit dem Indexierungsdesign von DocumentDB vertraut machen.

Datenbankindizes dienen zur Ausführung von Abfragen verschiedenster Arten und Formen mit minimaler Ressourcennutzung (CPU, E/A) sowie mit gutem Durchsatz und niedriger Latenz. Die Auswahl der richtigen Indizes für Datenbankabfragen erfordert oft viel Planungs- und Testaufwand. Dieser Ansatz ist eine Herausforderung für schemalose Datenbanken, in denen die Daten nicht einem strikten Schema folgen und sich laufend verändern.

Daher haben wir uns für die Entwicklung des Indexierungs-Untersystems von DocumentDB die folgenden Ziele gesetzt:

-   Indexierung von Dokumenten ohne Schema: Das Indexierungs-Untersystem benötigt keine Schemainformationen und stellt keinerlei Annahmen über das Schema der Dokumente vorab an.

-   Unterstützung für effiziente, umfassende hierarchische und relationale Abfragen: Der Index unterstützt die DocumentDB-Abfragesprache auf effiziente Weise und bietet Unterstützung für hierarchische und relationale Projektionen.

-   Unterstützung für konsistente Abfragen auch bei steigendem Volumen an Schreibvorgängen: Bei Arbeitslasten mit hohem Schreibaufwand und konsistenten Abfragen wird der Index inkrementell, effizient und online aktualisiert, um mit dem hohen Aufkommen an Schreibvorgängen Schritt zu halten. Die konsistente Indexaktualisierung ist entscheidend, um Abfragen auf der Konsistenzebene zu bieten, mit der die Benutzer den Dokumentdienst konfiguriert haben.

-   Mehrinstanzenfähigkeit: Beim reservierungsbasierten Modell für mandantenübergreifende Ressourcenverwaltung werden Indexaktualisierungen innerhalb des Budgets der pro Replikat verfügbaren Systemressourcen (CPU, Speicher, IOPS) ausgeführt.

-   Speichereffizienz: Aus Kostengründen ist der zusätzlich benötigte Speicherplatz für den Index beschränkt und vorhersagbar. Dies ist entscheidend, da Entwickler mit DocumentDB kostenbasierte Kompromisse zwischen Index-Mehraufwand und Abfrageleistung treffen können.

Weitere Informationen zum Konfigurieren der Indexierungsrichtlinie für Sammlungen finden Sie in den [DocumentDB-Beispielen] (http://code.msdn.microsoft.com/Azure-DocumentDB-NET-Code-6b3da8af\#content) auf MSDN. Wenden wir uns nun den Details der DocumentDB-SQL-Sprache zu.

# Grundlagen von DocumentDB-Abfragen

Jede Abfrage besteht aus einer **SELECT**-Klausel und optionalen **FROM**- und **WHERE**-Klauseln nach ANSI-SQL-Standards. Normalerweise wird in jeder Abfrage die jeweilige Quelle in der From-Klausel aufgelistet. Anschließend wird in der WHERE-Klausel ein Filter auf die Quelle angewendet, um eine Teilmenge der JSON-Dokumente zurückzugeben. Zuletzt wird die SELECT-Klausel verwendet, um die abgefragten JSON-Werte in die ausgewählte Liste zu projizieren.

    SELECT <select_list> 
    [FROM <from_specification>] 
    [WHERE <filter_condition>]    

# Die FROM-Klausel

Die `FROM <from_specification>`-Klausel ist optional, es sei denn, die Quelle wird später in der Abfrage gefiltert/projiziert. Mit dieser Klausel wird die Datenquelle angegeben, auf der die Abfrage operiert. Normalerweise dient die gesamte Sammlung als Quelle, stattdessen kann aber auch eine Teilmenge der Sammlung angegeben werden.

Abfragen wie `SELECT * FROM Families` geben an, dass die gesamte Families-Sammlung als Quelle dient, die durchlaufen werden soll. Der Sonderbezeichner **ROOT** kann anstelle des Sammlungsnamens verwendet werden, um die Sammlung darzustellen.
Pro Abfrage gelten die folgenden Bindungsregeln:

-   Die Sammlung kann Aliase enthalten, wie z. B. in `SELECT f.id FROM Families AS f`, oder einfach `SELECT f.id FROM Families f`. Hier ist `f` das Äquivalent zu `Families`. `AS` ist ein optionales Schlüsselwort als Alias für den Bezeichner.

-   Nach der Aliasverwendung kann die Originalquelle nicht mehr gebunden werden. `SELECT Familes.id FROM Families f` ist beispielsweise syntaktisch ungültig, da der Bezeichner "Families" nicht mehr aufgelöst werden kann.

-   Alle referenzierten Eigenschaften müssen **vollqualifiziert** sein. Dies ist ohne strikte Schemaverwendung notwendig, um mehrdeutige Bindungen zu vermeiden. Daher ist `SELECT id FROM Families f` syntaktisch ungültig, da die Eigenschaft `id` nicht gebunden ist.

## Unterdokumente

Die Quelle kann auch auf eine kleinere Teilmenge reduziert werden. Wenn Sie z. B. nur eine Teilstruktur in jedem Dokument auflisten möchten, kann der Stammknoten der Teilstruktur zur Quelle werden, wie im folgenden Beispiel gezeigt.

**Query**

    SELECT * 
    FROM Families.children

**Ergebnisse**

    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]

Das obige Beispiel verwendet ein Array als Quelle. Sie können jedoch auch ein Objekt als Quelle verwenden, wie im folgenden Beispiel gezeigt. Jeder gültige JSON-Wert (mit Ausnahme von Undefined), der in der Quelle gefunden werden kann, wird für die Integration in das Abfrageergebnis herangezogen. Wenn manche Familien keinen `address.state`-Wert haben, werden sie aus dem Abfrageergebnis ausgeschlossen.

**Query**

    SELECT * 
    FROM Families.address.state

**Ergebnisse**

    [
      "WA", 
      "NY"
    ]

# Die WHERE-Klausel

Die WHERE-Klausel (**`WHERE <filter_condition>`**) ist optional. Sie gibt die Bedingungen an, die die in der Quelle angegebenen JSON-Dokumente erfüllen müssen, um als Teil des Ergebnisses zurückgegeben zu werden. Jedes JSON-Dokument muss die angegebenen Bedingungen erfüllen, um in das Ergebnis einbezogen zu werden. Die WHERE-Klausel wird von der Indexebene verwendet, um die absolut kleinste Teilmenge von Quelldokumenten zu bestimmen, die Teil des Ergebnisses sein können.

Die folgende Abfrage fordert Dokumente an, die eine name-Eigenschaft haben, deren Eigenschaftswert gleich `AndersenFamily` ist. Alle anderen Dokumente, die keine name-Eigenschaft haben oder deren Wert nicht gleich `AndersenFamily` ist, werden ausgeschlossen.

**Query**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Ergebnisse**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]

Das vorstehende Beispiel zeigt eine einfache Gleichheitsabfrage. DocumentDB-SQL unterstützt außerdem eine Vielzahl skalarer Ausdrücke. Die am häufigsten verwendeten Ausdrücke sind binäre und unäre Ausdrücke. Eigenschaftsverweise aus dem JSON-Quellobjekt sind ebenfalls gültige Ausdrücke.

Die folgenden binären Operatoren werden momentan unterstützt und können in Abfragen verwendet werden, wie in den folgenden Beispielen gezeigt:

<table>

<tr>

<td>
Arithmetik

</td>

<td>
+,-,\*,/,%

</td>

</tr>

<tr>

<td>
Bitweise

</td>

<td>
|, &, ^

</td>

</tr>

<tr>

<td>
Logisch

</td>

<td>
AND, OR

</td>

</tr>

<tr>

<td>
Vergleich

</td>

<td>
=, !=, \>, \>=, \<, \<=, \<\>

</td>

</tr>

<tr>

<td>
String

</td>

<td>
|| (Verkettung)

</td>

</tr>

</table>
</p>
Betrachten wir nun einige Abfragen mit binären Operatoren.

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5     -- matching grades == 5

Die unären Operatoren **+,-, ~ und NOT** werden ebenfalls unterstützt und können in Abfragen verwendet werden, wie in den folgenden Beispielen gezeigt:

    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5

Neben binären und unären Operatoren sind auch Eigenschaftsverweise zulässig. Zum Beispiel gibt `SELECT * FROM Families f WHERE f.isRegistered` alle JSON-Dokumente zurück, die die Eigenschaft `isRegistered` haben und deren Wert gleich dem JSON-Wert `true` ist. Alle anderen Werte (false, null, Undefined, <number>, <string>, <object>, <array> usw.) führen dazu, dass das Quelldokument aus dem Ergebnis ausgeschlossen wird.

## Gleichheits- und Vergleichsoperatoren

Die folgende Tabelle zeigt die Ergebnisse für Gleichheitsvergleiche in DocumentDB-SQL zwischen den einzelnen JSON-Typen.

<table style="width:300px">

<tbody>

<tr>

<td valign="top">
 **Op**

</td>

<td valign="top">
 **Undefined**

</td>

<td valign="top">
 **Null**

</td>

<td valign="top">
 **Boolean**

</td>

<td valign="top">
 **Number**

</td>

<td valign="top">
 **String**

</td>

<td valign="top">
 **Object**

</td>

<td valign="top">
 **Array**

</td>

</tr>

<tr>

<td valign="top">
 <strong>Undefined<strong>

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 Undefined

</td>

</tr>

<tr>

<td valign="top">
 <strong>Null<strong>

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 **OK**

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 Undefined

</td>

</tr>

<tr>

<td valign="top">
 <strong>Boolean<strong>

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 **OK**

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 Undefined

</td>

</tr>

<tr>

<td valign="top">
 <strong>Number<strong>

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 **OK**

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 Undefined

</td>

</tr>

<tr>

<td valign="top">
 <strong>String<strong>

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 **OK**

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 Undefined

</td>

</tr>

<tr>

<td valign="top">
 <strong>Object<strong>

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 **OK**

</td>

<td valign="top">
 Undefined

</td>

</tr>

<tr>

<td valign="top">
 <strong>Array<strong>

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 Undefined

</td>

<td valign="top">
 **OK**

</td>

</tr>

</tbody>

</table>
</p>
Für andere Vergleichsoperatoren wie \>, \>=, !=, \< und \<= gilt:

-   Vergleiche zwischen zwei unterschiedlichen Typen ergeben Undefined.
-   Vergleiche zwischen zwei Objekten oder zwei Arrays ergeben Undefined.

Wenn das Ergebnis eines skalaren Ausdrucks im Filter Undefined ist, wird das entsprechende Dokument aus dem Ergebnis ausgeschlossen, da Undefined logisch nicht gleich "true" ist.

## Logische Operatoren (AND, OR und NOT)

Diese Operatoren verwenden boolesche Werte. Es folgt eine Liste der logischen Wahrheitstabellen für diese Operatoren.

<table>
<colgroup>
<col width="25%" />
<col width="25%" />
<col width="25%" />
<col width="25%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><p><strong>OR</strong></p></td>
<td align="left"><p><strong>True</strong></p></td>
<td align="left"><p><strong>False</strong></p></td>
<td align="left"><p><strong>Undefined</strong></p></td>
</tr>
<tr class="even">
<td align="left"><p><strong>True</strong></p></td>
<td align="left"><p>True</p></td>
<td align="left"><p>True</p></td>
<td align="left"><p>True</p></td>
</tr>
<tr class="odd">
<td align="left"><p><strong>False</strong></p></td>
<td align="left"><p>True</p></td>
<td align="left"><p>False</p></td>
<td align="left"><p>Undefined</p></td>
</tr>
<tr class="even">
<td align="left"><p><strong>Undefined</strong></p></td>
<td align="left"><p>True</p></td>
<td align="left"><p>Undefined</p></td>
<td align="left"><p>Undefined</p></td>
</tr>
</tbody>
</table>

<table>
<colgroup>
<col width="25%" />
<col width="25%" />
<col width="25%" />
<col width="25%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><p><strong>AND</strong></p></td>
<td align="left"><p><strong>True</strong></p></td>
<td align="left"><p><strong>False</strong></p></td>
<td align="left"><p><strong>Undefined</strong></p></td>
</tr>
<tr class="even">
<td align="left"><p><strong>True</strong></p></td>
<td align="left"><p>True</p></td>
<td align="left"><p>False</p></td>
<td align="left"><p>Undefined</p></td>
</tr>
<tr class="odd">
<td align="left"><p><strong>False</strong></p></td>
<td align="left"><p>False</p></td>
<td align="left"><p>False</p></td>
<td align="left"><p>False</p></td>
</tr>
<tr class="even">
<td align="left"><p><strong>Undefined</strong></p></td>
<td align="left"><p>Undefined</p></td>
<td align="left"><p>False</p></td>
<td align="left"><p>Undefined</p></td>
</tr>
</tbody>
</table>

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><p><strong>NOT</strong></p></td>
<td align="left"><p></p></td>
</tr>
<tr class="even">
<td align="left"><p><strong>True</strong></p></td>
<td align="left"><p>False</p></td>
</tr>
<tr class="odd">
<td align="left"><p><strong>False</strong></p></td>
<td align="left"><p>True</p></td>
</tr>
<tr class="even">
<td align="left"><p><strong>Undefined</strong></p></td>
<td align="left"><p>Undefined</p></td>
</tr>
</tbody>
</table>

# Die SELECT-Klausel

Die SELECT-Klausel (**`SELECT <select_list>`**) ist obligatorisch und gibt an, welche Werte die Abfrage zurückgeben soll, wie auch in ANSI-SQL. Die gefilterte Teilmenge aus den Quelldokumenten wird an die Projektionsphase übergeben, in der die angegebenen JSON-Werte abgerufen werden und ein neues JSON-Objekt für jede übergebene Eingabe erstellt wird.

Das folgende Beispiel zeigt eine typische SELECT-Abfrage:

**Query**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Ergebnisse**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]

## Verschachtelte Eigenschaften

Im folgenden Beispiel werden zwei verschachtelte Eigenschaften projiziert, `f.address.state` und `f.address.city`:

**Query**

    SELECT f.address.state, f.address.city
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Ergebnisse**

    [{
      "state": "WA", 
      "city": "seattle"
    }]

Die Projektion unterstützt auch JSON-Ausdrücke, wie im folgenden Beispiel gezeigt.

**Query**

    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Ergebnisse**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle", 
        "name": "AndersenFamily"
      }
    }]

Beachten Sie die Rolle von `$1`. Die `SELECT`-Klausel muss ein JSON-Objekt erstellen. Da kein Schlüssel angegeben ist, verwenden wir implizite Argument-Variablennamen beginnend mit `$1`. Diese Abfrage gibt z. B. zwei implizite Argumentvariablen mit den Bezeichnungen `$1` und `$2` zurück.

**Query**

    SELECT { "state": f.address.state, "city": f.address.city }, 
           { "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Ergebnisse**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]

## Aliase

Wir werden das obige Beispiel nun erweitern und explizite Aliase für Werte verwenden. **AS** ist das Schlüsselwort für die Aliasvergabe. Dieses Schlüsselwort ist optional, wie Sie bei der Projektion des zweiten Werts als `NameInfo` sehen.

Wenn eine Abfrage zwei Eigenschaften mit demselben Namen hat, müssen Aliase verwendet werden, um mindestens eine der Eigenschaften umzubenennen, sodass diese im projizierten Ergebnis eindeutig sind.

**Query**

    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo, 
           { "name": f.id } NameInfo
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Ergebnisse**

    [{
      "AddressInfo": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]

## Skalare Ausdrücke

Neben Eigenschaftsverweisen unterstützt die **SELECT**-Klausel auch skalare Ausdrücke wie z. B. Konstanten, arithmetische Ausdrücke, logische Ausdrücke usw. Hier sehen Sie z. B. eine einfache "Hallo Welt"-Abfrage.

**Query**

    SELECT "Hello World"

**Ergebnisse**

    [{
      "$1": "Hello World"
    }]

Hier ist ein komplexeres Beispiel mit einem skalaren Ausdruck:

**Query**

    SELECT ((2 + 11 % 7)-2)/3   

**Ergebnisse**

    [{
      "$1": 1.33333
    }]

Das Ergebnis des skalaren Ausdrucks im folgenden Beispiel ist ein Boolean-Wert.

**Query**

    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f 

**Ergebnisse**

    [
      {
        "AreFromSameCityState": false
      }, 
      {
        "AreFromSameCityState": true
      }
    ]

## Objekt- und Arrayerstellung

Eine weitere Schlüsselfunktion von DocumentDB-SQL ist die Objekt- und Arrayerstellung. Im vorherigen Beispiel haben wir ein neues JSON-Objekt erstellt. Auf dieselbe Weise können Sie Arrays erstellen, wie hier gezeigt.

**Query**

    SELECT [f.address.city, f.address.state] AS CityState 
    FROM Families f 

**Ergebnisse**

    [
      {
        "CityState": [
          "seattle", 
          "WA"
        ]
      }, 
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]

## VALUE-Schlüsselwort

Mit dem **VALUE**-Schlüsselwort können Sie JSON-Werte zurückgeben. Die folgende Abfrage gibt z. B. den skalaren Wert `"Hello World"` anstelle von `{$1: "Hello World"}` zurück.

**Query**

    SELECT VALUE "Hello World"

**Ergebnisse**

    [
      "Hello World"
    ]

Die folgende Abfrage gibt den JSON-Wert ohne die `"address"`-Bezeichnung in den Ergebnissen zurück.

**Query**

    SELECT VALUE f.address
    FROM Families f 

**Ergebnisse**

    [
      {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan", 
        "city": "NY"
      }
    ]

Das folgende Beispiel zeigt, wie Sie primitive JSON-Werte zurückgeben können, d. h. auf der Blattebene der JSON-Baumstruktur.

**Query**

    SELECT VALUE f.address.state
    FROM Families f 

**Ergebnisse**

    [
      "WA",
      "NY"
    ]

## \* Operator

Wir unterstützen den Sonderoperator (\*), um das Dokument unverändert zu projizieren. Wenn dieser Operator verwendet wird, dürfen keine weiteren projizierten Felder existieren. Abfragen wie z. B. `SELECT * FROM Families f` sind gültig, `SELECT VALUE * FROM Families f` und `SELECT *, f.id FROM Families f` hingegen nicht.

**Query**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Ergebnisse**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
    }]

# Erweiterte Konzepte

## Iteration

Wir haben ein neues Konstrukt mit dem **IN**-Schlüsselwort in DocumentDB-SQL eingeführt, mit dem Sie JSON-Arrays durchlaufen können. Die FROM-Quelle bietet Unterstützung für Iterationen. Beginnen wir mit dem folgenden Beispiel:

**Query**

    SELECT * 
    FROM Families.children

**Ergebnisse**

    [
      [
        {
          "firstName": "Henriette Thaulow", 
          "gender": "female", 
          "grade": 5, 
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam", 
            "givenName": "Jesse", 
            "gender": "female", 
            "grade": 1
        }, 
        {
            "familyName": "Miller", 
            "givenName": "Lisa", 
            "gender": "female", 
            "grade": 8
        }
      ]
    ]

Betrachten wir nun eine andere Abfrage, die untergeordnete Elemente in der Sammlung durchläuft. Beachten Sie den Unterschied im Ausgabe-Array. Dieses Beispiel teilt `children` und fügt das Ergebnis in ein einziges Array ein.

**Query**

    SELECT * 
    FROM c IN Families.children

**Ergebnisse**

    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]

Mit dieser Funktion können Sie über einzelne Einträge im Array filtern, wie im folgenden Beispiel gezeigt.

**Query**

    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8

**Ergebnisse**

    [{
      "givenName": "Lisa"
    }]

## Joins

Die Möglichkeit, Tabellen zu verknüpfen, ist sehr wichtig in relationalen Datenbanken. Es ist die logische Grundvoraussetzung für die Planung normalisierter Schemas. Im Gegensatz dazu arbeitet DocumentDB mit denormalisierten Datenmodellen schemafreier Dokumente. Dies ist das logische Äquivalent zu "Selbstverknüpfungen".

Die Sprache unterstützt die Syntax <from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourcen>. Dabei wird ein Satz von **N**-Tupeln (Tupel mit **N** Werten) zurückgegeben. Jedes Tupel enthält Werte, die durch Iteration aller Sammlungs-Aliase über deren jeweilige Sätze entstanden sind. In anderen Worten, dies ist ein komplettes Kreuzungsprodukt der an der Verknüpfung beteiligten Sätze.

Die folgenden Beispiele veranschaulichen die Funktionsweise von JOINs. Das Ergebnis im folgenden Beispiel ist leer, da das Kreuzprodukt der einzelnen Quelldokumente und einem leeren Satz leer ist.

**Query**

    SELECT f.id
    FROM Families f
    JOIN f.NonExistent

**Ergebnisse**

    [{
    }]

Im folgenden Beispiel findet die Verknüpfung zwischen dem Dokumentstamm und dem `children`-Unterstamm statt. Es handelt sich um ein Kreuzprodukt zweier JSON-Objekte. Die Tatsache, dass die untergeordnete Struktur ein Array ist, macht für die Verknüpfung keinen Unterschied, da wir auf einem einzigen Stammknoten arbeiten, der das untergeordnete Array darstellt. Das Ergebnis enthält also nur zwei Ergebnisse, da das Kreuzprodukt der einzelnen Dokumente mit dem Array jeweils genau ein Dokument ergibt.

**Query**

    SELECT f.id
    FROM Families f
    JOIN f.children

**Ergebnisse**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]

Das folgende Beispiel zeigt eine gewöhnlichere Verknüpfung:

**Query**

    SELECT f.id
    FROM Families f
    JOIN c IN f.children 

**Ergebnisse**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]

Beachten Sie zunächst, dass `from_source` für das **JOIN** in diesem Fall ein Iterator ist. Der Fluss verläuft in diesem Fall also wie folgt

-   Alle untergeordneten Elemente **c** im Array erweitern.
-   Kreuzprodukt mit dem Stammknoten des Dokuments **f** mit den einzelnen untergeordneten Elementen **c** anwenden, die im ersten Schritt vereinfacht wurden.
-   Zuletzt wird die Namenseigenschaft des Stammobjekts **f** alleine projiziert.

Das erste Dokument (`AndersenFamily`) enthält nur ein untergeordnetes Element. Daher enthält der Ergebnissatz für dieses Dokument auch nur ein einzelnes Objekt. Das zweite Dokument (`WakefieldFamily`) enthält zwei untergeordnete Elemente. Daher ergibt das Kreuzprodukt ein separates Objekt für jedes untergeordnete Element und das Ergebnis enthält zwei Objekte, je eines pro untergeordnetem Element im Dokument. Die Stammfelder sind in beiden Dokumenten gleich, wie Sie es bei einem Kreuzprodukt erwarten würden.

Der wahre Nutzen von JOIN ist die Bildung von Tupeln aus dem Kreuzprodukt auf eine Weise, die auf anderen Wegen schwer zu projizieren ist. Außerdem könnten Sie Filter auf die Kombination von Tupeln anwenden, mit denen Benutzer eine Bedingung auswählen können, die von der Gesamtheit der Tupel erfüllt sein muss, wie im folgenden Beispiel gezeigt.

**Query**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets

**Ergebnisse**

    [
      {
        "familyName": "AndersenFamily", 
        "childFirstName": "Henriette Thaulow", 
        "petName": "Fluffy"
      }, 
      {
        "familyName": "WakefieldFamily", 
        "childGivenName": "Jesse", 
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]

Dieses Beispiel ist eine natürliche Erweiterung des vorherigen Beispiels und zeigt eine doppelte Verknüpfung. Sie sehen das Kreuzprodukt in Form des folgenden Pseudocodes.

    for-each(Family f in Families)
    {   
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName, 
                  c.givenName AS childGivenName, 
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }

`AndersenFamily` hat ein Kind mit einem Haustier. Das Kreuzprodukt ergibt also eine Zeile (1*1*1) für diese Familie. WakefieldFamily hat dagegen zwei Kinder, von denen allerdings nur "Jesse" Haustiere hat. Sie hat jedoch 2 Haustiere. Das Kreuzprodukt ergibt also 1*1*2 = 2 Zeilen für diese Familie.

Das nächste Beispiel verwendet einen zusätzlichen Filter auf `pet` an. Damit werden alle Tupel ausgeschlossen, bei denen der Haustiername nicht "Shadow" ist. Wir können also Tupel aus Arrays bilden, beliebige Elemente der Tupel filtern und beliebige Kombinationen aus Elementen projizieren.

**Query**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"

**Ergebnisse**

    [
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]

# JavaScript-Integration

DocumentDB bietet ein Programmiermodell zur Ausführung JavaScript-basierter Anwendungslogik direkt auf die Sammlungen über gespeicherte Prozeduren und Trigger. Damit ist Folgendes möglich:

-   Transaktionale CRUD-Operationen und Abfragen auf Dokumente in einer Sammlung mit hoher Leistung dank der tiefen Integration der JavaScript-Laufzeit direkt im Datenbankmodul.
-   Eine natürliche Modellierung von Kontrollfluss, Variablen-Bereichssteuerung, Zuweisung und Integration der Ausnahmebehandlung für Datenbanktransaktionen. Weitere Informationen zur DocumentDB-Unterstützung für die JavaScript-Integration finden Sie in der Dokumentation für serverseitige JavaScript-Programmierung.

## Benutzerdefinierte Funktionen (User Defined Functions UDFs)

Neben den oben angegebenen Typen bietet DocumentDB-SQL auch Unterstützung für benutzerdefinierte Funktionen (UDFs). Insbesondere skalare UDFs werden unterstützt, bei denen Entwickler null oder mehrere Argumente übergeben und ein einziges Argumentergebnis zurückerhalten können. Für dieser Argumente wird geprüft, ob es sich um gültige JSON-Werte handelt.

Die Grammatik von DocumentDB-SQL wurde erweitert und unterstützt benutzerdefinierte Anwendungslogik mithilfe dieser benutzerdefinierten Funktionen. UDFs können in Azure DocumentDB registriert und anschließend als Teil einer SQL-Abfrage referenziert werden. UDFs dienen in der Tat ausschließlich dazu, in Abfragen aufgerufen zu werden. Als Nebeneffekt haben UDFs im Gegensatz zu den anderen JavaScript-Typen (gespeicherte Prozeduren, Trigger) keinen Zugriff auf das Kontextobjekt. Da Abfragen schreibgeschützt ausgeführt werden, können sie entweder auf primären oder auf sekundären Replikaten ausgeführt werden. Daher werden UDFs normalerweise im Gegensatz zu anderen JavaScript-Typen für die Ausführung auf sekundären Replikaten entwickelt.

Hier sehen Sie ein Beispiel für die Registrierung einer UDF in der DocumentDB-Datenbank, speziell unterhalb einer Dokumentensammlung.

       UserDefinedFunction sqrtUdf = new UserDefinedFunction
       {
           Id = "SQRT",
           Body = @"function(number) { 
                       return Math.sqrt(number);
                   };",
       };
       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           collectionSelfLink/* link of the parent collection*/, 
           sqrtUdf).Result;  
                                                                             

Das obige Beispiel erstellt eine UDF mit dem Namen `SQRT`. Sie akzeptiert einen einzigen JSON-WErt `number` und berechnet die Quadratwurzel der Zahl mithilfe der Math-Bibliothek.

Wir können diese UDF nun in einer Abfrage in einer Projektion verwenden.

**Query**

    SELECT SQRT(c.grade)
    FROM c IN Families.children

**Ergebnisse**

    [
      {
        "$1": 2.23606797749979
      }, 
      {
        "$1": 1
      }, 
      {
        "$1": 2.8284271247461903
      }
    ]

Die UDF kann auch in einem Filter verwendet werden, wie im folgenden Beispiel gezeigt:

**Query**

    SELECT c.grade
    FROM c IN Familes.children
    WHERE SQRT(c.grade) = 1

**Ergebnisse**

    [{
        "grade": 1
    }]

UDFs sind gültige skalare Ausdrücke und können sowohl in Projektionen als auch in Filtern verwendet werden.

Wir erweitern den Funktionsumfang von UDFs im folgenden Beispiel um konditionale Logik:

       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                switch (city) {
                    case 'seattle':
                        return 520;
                    case 'NY':
                        return 410;
                    case 'Chicago':
                        return 673;
                    default:
                        return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(collection.SelfLink, seaLevelUdf);

Hier sehen Sie ein Verwendungsbeispiel für die UDF.

**Query**

    SELECT f.address.city, SEALEVEL(f.address.city) AS seaLevel
    FROM Families f 

**Ergebnisse**

     [
      {
        "city": "seattle", 
        "seaLevel": 520
      }, 
      {
        "city": "NY", 
        "seaLevel": 410
      }
    ]

Wie im obigen Beispiel gezeigt vereinen UDFs den Funktionsumfang der JavaScript-Sprache mit DocumentDB-SQL zu einer umfassenden, programmierbaren Schnittstelle für komplexe verfahrensorientierte und konditionale Logik mithilfe der Fähigkeiten der integrierten JavaScript-Laufzeit.

DocumentDB-SQL übergibt die Argumente an die UDFs für die einzelnen Quelldokumente für die aktuelle Phase (WHERE-Klausel oder SELECT-Klausel) der UDF-Ausführung. Das Ergebnis wird nahtlos in die Gesamt-Ausführungspipeline aufgenommen. Wenn die von den UDF-Parametern referenzierten Eigenschaften nicht im JSON-Wert verfügbar sind, wird der Parameter als Undefined betrachtet und der UDF-Aufruf daher komplett übersprungen. Wenn das Ergebnis der UDF Undefined ist, wird es ebenfalls nicht in das Ergebnis aufgenommen.

UDFs sind also hervorragende Werkzeuge, um komplexe Geschäftslogik als Teil der Abfragen zu integrieren.

## Operatorauswertung

DocumentDB ist eine JSON-Datenbank und enthält daher Parallelen zu JavaScript-Operatoren und deren Auswertungslogik. Obwohl DocumentDB versucht, die JavaScript-Logik für die JSON-Unterstützung zu erhalten, weicht die Auswertung von Operationen doch in manchen Fällen davon ab.

In der DocumentDB-SQL-Abfragesprache ist der Typ eines Werts im Gegensatz zu herkömmlichem SQL oft nicht bekannt, bevor der Wert tatsächlich aus der Datenbank abgerufen wird. Um effiziente Abfragen zu ermöglichen, haben die meisten Operatoren strikte Typanforderungen.

DocumentDB-SQL führt im Gegensatz zu JavaScript keine impliziten Konvertierungen durch. Eine Abfrage wie `SELECT * FROM Person p WHERE p.Age = 21` gibt z. B. Dokumente zurück, die eine Age-Eigenschaft enthalten, deren Wert 21 ist. Alle weiteren Dokumente, deren Age-Eigenschaft mit der
Zeichenfolge "21" übereinstimmt, oder sonstige Variationen wie z. B. "021", "21.0", "0021", "00021" usw. werden nicht zurückgegeben.
Bei JavaScript würden diese Zeichenfolgenwerte dagegen implizit in Zahlen umgewandelt (je nach Operator, z. B.: ==). Dieser Unterschied ist wichtig für die effiziente Indexierung in DocumentDB-SQL.

# LINQ zu DocumentDB-SQL

LINQ ist ein .NET-Programmiermodell, das Berechnungen als Abfragen auf Streams von Objekten darstellt. DocumentDB bietet eine clientseitige Bibliothek als Schnittstelle zu LINQ und erleichtert die Konvertierung zwischen JSON- und .NET-Objekten sowie die Zuordnung einer Teilmenge von LINQ-Abfragen zu DocumentDB-Abfragen.

Die folgende Abbildung zeigt die Architektur für die Unterstützung von LINQ-Abfragen in DocumentDB. Mit dem DocumentDB-Client können Entwickler ein **IQueryable**-Objekt erstellen, das die Abfrage an den DocumentDB-Abfrageanbieter weiterleitet, der die LINQ-Abfrage wiederum in eine DocumentDB-Abfrage übersetzt. Anschließend wird die Abfrage an den DocumentDB-Server übergeben, um einen Ergebnissatz im JSON-Format abzurufen. Die zurückgegebenen Ergebnisse werden clientseitig in einen Stream von .NET-Objekten deserialisiert.

![][0]

## .NET- und JSON-Zuordnung

Die Zuordnung zwischen .NET- und JSON-Objekten erfolgt auf natürliche Weise. Jedes Datenelementfeld wird einem JSON-Objekt zugeordnet. Dabei wird der Feldname in den "key"-Teil des Objekts übernommen, und der "value"-Teil wird rekursiv zur Wertkomponente des Objekts zugeordnet. Betrachten Sie das folgende Beispiel. Das erstellte Family-Objekt wird wie hier gezeigt zum JSON-Dokument zugeordnet. Umgekehrt wird das JSON-Dokument zurück zu einem .NET-Objekt zugeordnet.

**C\#-Klasse**

    public class Family
    {
        [JsonProperty(PropertyName="id")]
        public string Id;
        public Parent[] parents;
        public Child[] children;
        public bool isRegistered;
    };

    public struct Parent
    {
        public string familyName;
        public string givenName;
    };

    public class Child
    {
        public string familyName;
        public string givenName;
        public string gender;
        public int grade;
        public List<Pet> pets;
    };

    public class Pet
    {
        public string givenName;
    };

    public class Address
    {
        public string state;
        public string county;
        public string city;
    };

    // create a Family object
    Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
    Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
    Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
    Pet pet = new Pet { givenName = "Fluffy" };
    Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
    Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };

**JSON**

    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam", 
                "givenName": "Jesse", 
                "gender": "female", 
                "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
              "familyName": "Miller", 
              "givenName": "Lisa", 
              "gender": "female", 
              "grade": 8 
            }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    };

## LINQ zu SQL-Übersetzung

Der DocumentDB-Abfrageanbieter führt eine bestmögliche Zuordnung von LINQ-Abfragen in DocumentDB-SQL-Abfragen durch. Die folgende Beschreibung geht davon aus, das Sie mit den Grundsätzen von LINQ vertraut sind.

Für das Typsystem werden alle primitiven JSON-Typen unterstützt: numerische Typen, String und null. Andere JSON-Typen werden nicht unterstützt. Die folgenden skalaren Ausdrücke werden unterstützt.

-   Konstante Werte – dazu gehören konstante Werte der primitiven Datentypen zum Zeitpunkt der Ausführung der Abfrage.

-   Indexausdrücke auf Eigenschaften/Arrays – diese Ausdrücke beziehen sich auf die Eigenschaft eines Objekts oder eines Arrayelements.

        family.Id;
        family.children[0].familyName;
        family.children[0].grade;
        family.children[n].grade; //n is an int variable

-   Arithmetische Ausdrücke - dazu gehören arithmetische Ausdrücke auf numerische und boolesche Werte. Eine vollständige Liste finden Sie in der obigen SQL-Dokumentation.

        2 * family.children[0].grade;
        x + y;

-   Zeichenfolgenvergleiche - dazu gehören Vergleiche von Zeichenfolgenwerten mit konstanten Zeichenfolgen.

        mother.familyName == "Smith";
        child.givenName == s; //s is a string variable

-   Erstellungsausdrücke für Objekte/Arrays - diese Ausdrücke geben ein Objekt mit einem Verbundwerttyp oder anonymen Typ bzw. ein Array solcher Objekte zurück. Diese Werte können verschachtelt sein.

        new Parent { familyName = "Smith", givenName = "Joe" };
        new { first = 1, second = 2 }; //an anonymous type with 2 fields              
        new int[] { 3, child.grade, 5 };

## Abfrageoperatoren

Die folgenden Beispiele zeigen, wie einige der Standard-LINQ-Abfrageoperatoren in DocumentDB-Abfragen übersetzt werden.

### Select-Operator

Die Syntax ist `input.Select(x => f(x))`, wobei `f` ein skalarer Ausdruck ist.

**LINQ-Lambdaausdruck**

    input.Select(family => family.parents[0].familyName);

**SQL**

    SELECT VALUE f.parents[0].familyName
    FROM Families f

**LINQ-Lambdaausdruck**

    input.Select(family => family.children[0].grade + c); // c is an int variable

**SQL**

    SELECT VALUE f.children[0].grade + c
    FROM Families f 

**LINQ-Lambdaausdruck**

    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });

**SQL**

    SELECT VALUE {"name":f.children[0].familyName, 
                  "grade": f.children[0].grade + 3 }
    FROM Families f

### SelectMany-Operator

Die Syntax ist `input.SelectMany(x => f(x))`, wobei `f` ein skalarer Ausdruck ist, der einen Sammlungstyp zurückgibt.

**LINQ-Lambdaausdruck**

    input.SelectMany(family => family.children);

**SQL**

    SELECT VALUE child
    FROM child IN Families.children

### Where-Operator

Die Syntax ist `input.Where(x => f(x))`, wobei `f` ein skalarer Ausdruck ist, der einen booleschen Wert zurückgibt.

**LINQ-Lambdaausdruck**

    input.Where(family=> family.parents[0].familyName == "Smith");

**SQL**

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith" 

**LINQ-Lambdaausdruck**

    input.Where(
        family => family.parents[0].familyName == "Smith" && 
        family.children[0].grade < 3);

**SQL**

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
    AND f.children[0].grade < 3

## Zusammengesetzte Abfragen

Die obigen Operatoren können zu komplexeren Abfragen zusammengesetzt werden. Da DocumentDB verschachtelte Sammlungen unterstützt, können solche Zusammensetzungen entweder verkettet oder verschachtelt werden.

### Verkettung

Die Syntax ist `input(.|.SelectMany())(.Select()|.Where())*`. Eine verkettete Abfrage kann mit einer optionalen `SelectMany`-Abfrage beginnen, gefolgt von mehreren `Select`- oder `Where`-Operatoren.

**LINQ-Lambdaausdruck**

    input.Select(family=>family.parents[0])
        .Where(familyName == "Smith");

**SQL**

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"

**LINQ-Lambdaausdruck**

    input.Where(family => family.children[0].grade > 3)
        .Select(family => family.parents[0].familyName);

**SQL**

    SELECT VALUE f.parents[0].familyName
    FROM Families f
    WHERE f.children[0].grade > 3

**LINQ-Lambdaausdruck**

    input.Select(family => new { grade=family.children[0].grade}).
        Where(anon=> anon.grade < 3);
            

**SQL**

    SELECT *
    FROM Families f
    WHERE ({grade: f.children[0].grade}.grade > 3)

**LINQ-Lambdaausdruck**

    input.SelectMany(family => family.parents)
        .Where(parent => parents.familyName == "Smith");

**SQL**

    SELECT *
    FROM p IN Families.parents
    WHERE p.familyName = "Smith"

### Verschachtelung

Die Syntax ist `input.SelectMany(x=>x.Q())`, wobei Q ein `Select`-, `SelectMany`- oder `Where`-Operator ist.

In verschachtelten Abfragen wird die innere Abfrage auf jedes Element der äußeren Abfrage angewendet. Hierbei ist wichtig, dass die innere Abfrage auf die Felder der Elemente in der äußeren Abfrage wie bei Selbstverknüpfungen verweisen kann.

**LINQ-Lambdaausdruck**

    input.SelectMany(family=> 
        family.parents.Select(p => p.familyName));

**SQL**

    SELECT VALUE p.familyName
    FROM Families f
    JOIN p IN f.parents

**LINQ-Lambdaausdruck**

    input.SelectMany(family => 
        family.children.Where(child => child.familyName == "Jeff"));
            

**SQL**

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = "Jeff"

**LINQ-Lambdaausdruck**

    input.SelectMany(family => family.children.Where(
        child => child.familyName == family.parents[0].familyName));

**SQL**

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = f.parents[0].familyName

# Ausführen von Abfragen

Azure DocumentDB stellt Ressourcen über eine REST-API zur Verfügung, die in jeder Sprache aufgerufen werden kann, die HTTP/HTTPS-Anfragen unterstützt. Zusätzlich bietet Azure DocumentDB Programmierbibliotheken für einige beliebte Sprachen an, wie z. B. .NET, Node.js, JavaScript und Python. Die REST-API und die verschiedenen Bibliotheken unterstützen allesamt SQL-Abfragen. Das .NET SDK unterstützt LINQ-Abfragen zusätzlich zu SQL.

Die folgenden Beispiele zeigen, wie Sie eine Abfrage erstellen und auf ein DocumentDB-Datenbankkonto ausführen können.

## REST-API

DocumentDB bietet ein RESTful-Programmiermodell über HTTP. Datenbankkonten können im Rahmen eines Azure-Abonnements erstellt werden. Das Ressourcenmodell von DocumentDB besteht aus einem Satz von Ressourcen in einem Datenbankkonto, die jeweils über eine logische und beständige URI adressiert werden können. Ein Ressourcensatz wird in diesem Dokument als Feed bezeichnet. Ein Datenbankkonto besteht aus einem Satz von Datenbanken, die je mehrere Sammlungen enthalten, die wiederum Dokumente, UDFs und andere Ressourcentypen enthalten.

Die Interaktion mit diesen Ressourcen erfolgt über die HTTP-Verben GET, PUT, POST und DELETE mit deren Standardinterpretation. Das POST-Verb dient zur Erstellung neuer Ressourcen, zur Ausführung gespeicherter Prozeduren oder zum Ausführen von DocumentDB-Abfragen. Abfragen werden immer als schreibgeschützte Operationen ohne Nebeneffekte ausgeführt.

Die folgenden Beispiele zeigen POST für eine DocumentDB-Abfrage an eine Sammlung, die die beiden bisher behandelten Beispieldokumente enthält. Die Abfrage enthält einen einfachen Filter auf die JSON-Eigenschaft name. Beachten Sie die Verwendung der `x-ms-documentdb-isquery`- und Content-Type: `application/sql`-Header, um anzugeben, dass es sich bei dieser Operation um eine Abfrage handelt.

**Request**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/sql

    SELECT * FROM Families f WHERE f.id = "AndersenFamily"

**Ergebnisse**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    <indented for readability, results highlighted>

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }

Das zweite Beispiel zeigt eine komplexere Abfrage, die mehrere Ergebnisse der Verknüpfung zurückgibt.

**Request**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/sql

    SELECT 
         f.id AS familyName, 
         c.givenName AS childGivenName, 
         c.firstName AS childFirstName, 
         p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p in c.pets

**Ergebnisse**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    <indented for readability, results highlighted>

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }

Wenn die Abfrageergebnisse nicht in eine einzelne Ergebnisseite passen, gibt die REST-API ein Fortsetzungstoken im `x-ms-continuation-token`-Antwortheader zurück. Clients können einzelne Ergebnisseiten abfragen, indem sie den Header in nachfolgenden Abfragen angeben. Die Anzahl der Ergebnisse pro Seite kann über den `x-ms-max-item-count`-number-Header gesteuert werden.

Für die Verwaltung der Datenkonsistenzrichtlinien für Abfragen verwenden Sie den `x-ms-consistency-level`-Header, wie in allen REST-API-Anfragen. Für die Sitzungskonsistenz muss außerdem der letzte `x-ms-session-token`-Cookie-Header in jeder Anfrage wiederholt werden. Die Indexierungsrichtlinie der abgefragten Sammlung kann die Konsistenz der Abfrageergebnisse ebenfalls beeinflussen. Mit den Standardeinstellungen für Indexierungsrichtlinien stimmt der Index einer Sammlung immer mit dem aktuellen Dokumentinhalt überein, und Abfragen liefern die Daten in der gewünschten Konsistenz zurück. Wenn eine verzögerte Indexierungsrichtlinie verwendet wird, kann es passieren, dass veraltete Ergebnisse zurückgegeben werden. Weitere Informationen finden Sie unter [DocumentDB-Konsistenzebenen][DocumentDB-Konsistenzebenen].

Wenn die konfigurierte Indexierungsrichtlinie der Sammlung die angegebene Abfrage nicht unterstützen kann, gibt der DocumentDB-Server "400 Bad Request" zurück. Dies wird z. B. für Bereichsabfragen auf Pfade zurückgegeben, die für Hash (Gleichheits)-Suchvorgänge konfiguriert sind, oder für Pfade, die ausdrücklich von der Indexierung ausgeschlossen wurden. Mit dem `x-ms-documentdb-query-enable-scan`-Header kann angegeben werden, dass eine Abfrage eine Suche durchführen darf, wenn kein Index verfügbar ist.

## C\# (.NET) SDK

Das .NET SDK unterstützt Abfragen per LINQ und SQL. Das folgende Beispiel zeigt, wie Sie die weiter oben in diesem Dokument gezeigte einfache Filterabfrage ausführen können.

    foreach (var family in client.CreateDocumentQuery(collectionLink, 
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(collectionLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(collectionLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }

Das Beispiel vergleicht zwei Eigenschaften auf Gleichheit in den einzelnen Dokumenten und verwendet anonyme Projektionen.

    foreach (var family in client.CreateDocumentQuery(collectionLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family 
        FROM Families f 
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(collectionLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(collectionLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }

Das nächste Beispiel zeigt Verknüpfungen mithilfe von LINQ-SelectMany.

    foreach (var pet in client.CreateDocumentQuery(collectionLink,
          @"SELECT p
            FROM Families f 
                 JOIN c IN f.children 
                 JOIN p in c.pets 
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions
    foreach (var pet in
        client.CreateDocumentQuery<Family>(collectionLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }

Der .NET-Client durchläuft automatisch alle Seiten der Abfrageergebnisse in den foreach-Blöcken, wie oben gezeigt. Die im Abschnitt zur REST-API vorgestellten Abfrageoptionen sind auch im .NET SDK über die Klassen `FeedOptions` und `FeedResponse` in der CreateDocumentQuery-Methode verfügbar. Die Anzahl der Ergebnisse pro Seite kann über die `MaxItemCount`-Einstellung gesteuert werden.

Entwickler können die Seitenaufteilung steuern, indem sie ein `IDocumentQueryable` mit dem `IQueryable`-Objekt erstellen und anschließend die `ResponseContinuationToken`-Werte auslesen und als `RequestContinuationToken` in `FeedOptions` zurückgeben. `EnableScanInQuery` kann festgelegt werden, um Suchen zu aktivieren, wenn die Abfrage nicht von der konfigurierten Indexierungsrichtlinie unterstützt werden kann.

Weitere Abfragebeispiele finden Sie unter [DocumentDB .NET-Beispiele] (http://code.msdn.microsoft.com/Azure-DocumentDB-NET-Code-6b3da8af\#content).

## Serverseitige JavaScript-API

DocumentDB bietet ein Programmiermodell zur Ausführung JavaScript-basierter Anwendungslogik direkt auf die Sammlungen über gespeicherte Prozeduren und Trigger. Die auf Sammlungsebene registrierte JavaScript-Logik kann anschließend Datenbankoperationen auf die Dokumente der jeweiligen Sammlung ausführen. Diese Operationen werden in ACID-Transaktionen der jeweiligen Umgebung gekapselt.

Das folgende Beispiel zeigt, wie Sie mithilfe von queryDocuments in der serverseitigen JavaScript-API Abfragen aus gespeicherten Prozeduren und Triggers heraus ausführen können.

    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();
        var collectionLink = collectionManager.getSelfLink()

        // create a new document.
        collectionManager.createDocument(collectionLink,
            { name: name, author: author },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don't need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }

# Referenzen

1.  [Einführung in Azure DocumentDB][Einführung in Azure DocumentDB]
2.  [DocumentDB-SQL-Sprachspezifikation] (http://go.microsoft.com/fwlink/p/?LinkID=510612)
3.  [DocumentDB .NET-Beispiele] (http://code.msdn.microsoft.com/Azure-DocumentDB-NET-Code-6b3da8af\#content)
4.  [DocumentDB-Konsistenzebenen][DocumentDB-Konsistenzebenen]
5.  ANSI SQL 2011 - <http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681>
6.  JSON <http://json.org/>
7.  Javascript-Spezifikation <http://www.ecma-international.org/publications/standards/Ecma-262.htm>
8.  LINQ <http://msdn.microsoft.com/de-de/library/bb308959.aspx>
9.  Abfragetechniken für große Datenbanken <http://dl.acm.org/citation.cfm?id=152611>
10. Query Processing in Parallel Relational Database Systems, IEEE Computer Society Press, 1994
11. Lu, Ooi, Tan, Query Processing in Parallel Relational Database Systems, IEEE Computer Society Press, 1994.
12. Christopher Olston, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar, Andrew Tomkins: Pig Latin: A Not-So-Foreign Language for Data Processing, SIGMOD 2008.
13. G. Graefe. The Cascades framework for query optimization. IEEE Data Eng. Bull., 18(3): 1995.

  [0]: ./media/documentdb-sql-query/sql-query1.png
  [DocumentDB-Konsistenzebenen]: ../documentdb-consistency-levels
  [Einführung in Azure DocumentDB]: ../documentdb-introduction