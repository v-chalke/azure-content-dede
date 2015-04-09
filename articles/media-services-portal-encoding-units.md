﻿<properties 
	pageTitle="Skalieren von für die Codierung reservierten Einheiten" 
	description="Erfahren Sie, wie Sie Mediendienste durch Angabe der Anzahl von On-Demand Streaming Reserved Units und Encoding Reserved Units für Ihr Konto skalieren können." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/15/2015" 
	ms.author="juliako"/>


# Skalieren der Codierung

Dieser Artikel gehört zur Reihe [Media Services: Video-on-Demand-Workflow](../media-services-video-on-demand-workflow) .

## Übersicht

Ein Media Services-Konto ist dem Typ einer reservierten Einheit zugeordnet, der die Geschwindigkeit bestimmt, mit der Codierungsaufträge verarbeitet werden. Sie können zwischen den folgenden Typen reservierter Einheiten auswählen: **Basic**, **Standard** oder **Premium**. Derselbe Codierungsauftrag wird bei Verwendung der reservierten Einheit **Standard** beispielsweise schneller ausgeführt als mit dem Typ **Basic**. Weitere Informationen finden Sie unter der [Typen reservierter Einheiten für die Codierung](http://azure.microsoft.com/blog/author/milanga).

Zusätzlich zum Typ reservierter Einheiten können Sie angeben, dass für Ihr Konto reservierte Einheiten für die Codierung bereitgestellt werden sollen. Die Anzahl der bereitgestellten reservierten Einheiten für die Codierung bestimmt die Anzahl der Medienaufgaben, die gleichzeitig unter einem angegebenen Konto verarbeitet werden können. Wenn Ihr Konto beispielsweise über fünf reservierte Einheiten verfügt, werden fünf Medienaufgaben gleichzeitig ausgeführt, so lange es auszuführende Aufgaben gibt. Die restlichen Aufgaben bleiben in der Warteschlange und werden nacheinander für die Verarbeitung aufgerufen, wenn eine aktive Aufgabe abgeschlossen wird. Wenn für ein Konto keine reservierten Einheiten bereitgestellt wurden, werden die Aufgaben nacheinander aufgerufen. In diesem Fall hängt die Wartezeit zwischen dem Abschließen einer Aufgabe und dem Start der nächsten Aufgabe von der Verfügbarkeit von Ressourcen im System ab.

Führen Sie folgende Schritte aus, um den Typ reservierter Einheiten und die Anzahl reservierter Einheiten für die Codierung zu ändern:

1. Klicken Sie im [Verwaltungsportal](https://manage.windowsazure.com/) auf **Media Services**. Klicken Sie anschließend auf den Namen des Mediendiensts.

2. Wählen Sie die Seite **CODIERUNG** aus. 

	Um den **TYP RESERVIERTER EINHEITEN** zu ändern, drücken Sie BASIC, STANDARD oder PREMIUM. 

	Verwenden Sie zum Ändern der Anzahl reservierter Einheiten für den ausgewählten Typ reservierter Einheiten den Schieberegler unter **CODIERUNG**. 
	
	
	![Processors page](./media/media-services-how-to-scale/media-services-encoding-scale.png)

	  
	>[Azure.Note] Der Premium-Typ für reservierte Einheiten ist in den folgenden Rechenzentren nicht verfügbar: Singapur, Hongkong, Osaka, Peking, Shanghai.

3. Klicken Sie auf die Schaltfläche "Speichern", um die Änderungen zu speichern.

	Die neuen für das Codieren reservierten Einheiten werden zugewiesen, wenn Sie auf SPEICHERN klicken.

	>[Azure.Note] Die höchste für den 24-Stunden-Zeitraum angegebene Anzahl an Einheiten wird zum Berechnen der Kosten verwendet.

## Kontingente und Einschränkungen

Informationen zu Kontingenten und Einschränkungen sowie zum Öffnen von Supporttickets finden Sie unter [Kontingente und Einschränkungen](../media-services-quotas-and-limitations).





<!--HONumber=47-->