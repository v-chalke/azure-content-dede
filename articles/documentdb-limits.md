﻿<properties title="DocumentDB limits for the preview release" pageTitle="DocumentDB-Einschränkungen für die Vorschauversion | Azure" description="Learn about the limits and quota enforcements of DocumentDB for the preview release." metaKeywords="" services="documentdb" solutions="data-management"  authors="bradsev" manager="jhubbard" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="spelluru" />


#DocumentDB-Einschränkungen für die Vorschauversion
	In der folgenden Tabelle werden die Einschränkungen und Kontingenterzwingungen von DocumentDB bei der Vorschauversion beschrieben. In den meisten Fällen werden die Einschränkungen mit der Absicht erzwungen, Ihr Feedback einzuholen, oder basierend auf den aktuellen Kapazitätseinschränkungen. Wenn Sie in Ihrem Unternehmen die Einschränkungen lockern möchten, rufen Sie uns an. Wir werden uns bemühen, Ihre Wünsche im Rahmen der Einschränkungen des öffentlichen Angebots zu erfüllen.    

|Entität |Kontingent (Standardangebot für die Vorschauversion)|
|-------|--------|
|Datenbankkonten     |5
|Anzahl von Datenbanken pro Datenbankkonto     |100
|Anzahl von Benutzern pro Datenbankkonto - über alle Datenbanken hinweg |500.000
|Anzahl von Berechtigungen pro Datenbankkonto - über alle Datenbanken hinweg   |2.000.000
|Anhangspeicher pro Datenbankkonto      |2 GB
|Maximale Anzahl von Kapazitätseinheiten pro Datenbankkonto       |5
|Anzahl von Auflistungen pro Kapazitätseinheit      |3
|Mindestens zugewiesener Speicher pro Auflistung mit mindestens 1 Dokument    |3,3 GB
|Mindestens zugewiesener Durchsatz pro Auflistung mit mindestens 1 Dokument |667 RUs
|Flexibilität einer Auflistung    |0-10 GB
|Maximale Anforderungseinheiten/Sek. pro Auflistung   |2000
|Anzahl gespeicherter Prozeduren, Trigger und UDFs pro Auflistung       |jeweils 25
|Maximale Ausführungszeit für gespeicherte Prozedur und Auslöser     |5 Sekunden
|Bereitgestellter Dokumentspeicher/Kapazitätseinheit |10 GB
|Bereitgestellte Anforderungseinheiten/Sek. pro Auflistung     |2000
|Maximaler Dokumentspeicher pro Datenbank (5 Kapazitätseinheiten)    |50 GB
|Maximale Länge der ID-Eigenschaft    |255 Zeichen
|Standardanzahl von Elementen pro Seite     |100
|Maximale Elemente pro Seite        |1000
|Maximale Anforderungsgröße von Dokumenten und Anlagen       |256 KB
|Maximale Anforderungsgröße von gespeicherter Prozedur, Trigger und UDF        |256 KB
|Maximale Antwortgröße |1 MB
|Maximale Anzahl von eindeutigen Pfaden pro Auflistung       |100
|Zeichenfolge |Alle Zeichenfolgen müssen der UTF-8-Codierung entsprechen. Da es sich bei UTF-8 um eine Codierung mit variabler Breite handelt, werden die Zeichenfolgengrößen mithilfe der UTF-8-Bytes ermittelt.
|Maximale Länge der Eigenschaft oder des Werts  |Keine praktische Einschränkung
|Maximale Anzahl von UDFs pro Abfrage     |1
|Maximale Anzahl von JOINs pro Abfrage    |2
|Maximale Anzahl von UND-Klauseln pro Abfrage      |5
|Maximale Anzahl von ODER-Klauseln pro Abfrage       |5