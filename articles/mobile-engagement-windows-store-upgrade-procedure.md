﻿<properties 
	pageTitle="Windows Store-SDK für Azure Mobile Engagement - Upgrade" 
	description="Neueste Updates und Verfahren für Windows Store-SDK für Azure Mobile Engagement" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

# Upgrade-Verfahren

Wenn Sie bereits eine ältere Version von Engagement in Ihrer Anwendung integriert haben, müssen Sie die folgenden Punkte beim Aktualisieren des SDK beachten.

Möglicherweise müssen Sie mehrere Verfahren befolgen, wenn Sie mehrere Versionen des SDK verpasst haben. Wenn Sie beispielsweise von 0.10.1 zu 0.11.0 migrieren, müssen Sie zunächst den Vorgang "von 0.9.0 zu 0.10.1" ausführen und anschließend den Vorgang "0.10.1 zu 0.11.0".

## Von 1.1.1 zu 2.0.0

Im Folgenden wird beschrieben, wie Sie die Migration einer SDK-Integration vom Capptain-Dienst, der von Capptain-SAS angeboten wird, in eine App von Azure Mobile Engagement durchführen. 

>[Azure.IMPORTANT] Capptain und Mobile Engagement sind nicht dieselben Dienste, und die unten beschriebene Vorgehensweise hebt nur hervor, wie die Migration der Clientapp durchzuführen ist. Bei der Migration des SDK in die App werden Ihre Daten NICHT von den Capptain-Servern zu den Mobile Engagement-Servern migriert

Wenn Sie von einer früheren Version migrieren, ziehen Sie zunächst die Capptain-Website zurate, um zu 1.1.1 zu migrieren und wenden Sie dann das folgende Verfahren an

### NuGet-Paket

Ersetzen Sie Capptain.WindowsPhone.nupkg durch azuresdk-mobileengagement-windows-VERSION.nupkg im Paket Lib-Archivordner.

### Anwenden von Mobile Engagement

Das SDK verwendet den Begriff `Engagement`. Sie müssen Ihr Projekt entsprechend dieser Änderung aktualisieren.

Sie müssen Ihr aktuelles Capptain Nuget-Paket deinstallieren. Bedenken Sie, dass alle Änderungen im Capptain-Ressourcenordner entfernt werden. Wenn Sie diese Dateien beibehalten möchten, erstellen Sie eine Kopie davon.

Danach installieren Sie das neue Microsoft Azure Engagement Nuget-Paket für Ihr Projekt. Sie finden es direkt auf der [Nuget-Website]. oder im Index. Diese Aktion ersetzt alle Ressourcendateien, die von Engagement verwendet werden, und fügt die neue Engagement-DLL Ihren Projektverweisen hinzu.

Sie müssen Ihre Projektverweise bereinigen, indem Sie Capptain-DLL-Verweise löschen. Wenn Sie dies nicht tun, kommt es zu Konflikten mit der Capptain-Version und Fehler treten auf.

Wenn Sie Capptain-Ressourcen angepasst haben, kopieren Sie den Inhalt Ihrer alten Dateien, und fügen Sie ihn in die neuen Engagement-Dateien ein. Bitte beachten Sie, dass XAML- und CS-Dateien aktualisiert werden müssen.

Nach Abschluss dieser Schritte müssen Sie nur die alten Capptain-Verweise durch die neuen Engagement-Verweise ersetzen.

Alle Capptain-Namespaces müssen aktualisiert werden.

Vor der Migration:

			using Capptain.Agent;
			using Capptain.Reach;

Nach der Migration:

			using Microsoft.Azure.Engagement;

Alle Capptain-Klassen, die "Capptain" enthalten, sollten "Engagement" enthalten.

Vor der Migration:

			public sealed partial class MainPage : CapptainPage
			{
			  protected override string GetCapptainPageName()
			  {
			    return "Capptain Demo";
			  }
			  ...
			}

Nach der Migration:

			public sealed partial class MainPage : EngagementPage
			{
			  protected override string GetEngagementPageName()
			  {
			    return "Engagement Demo";
			  }
			  ...
			}

Für XAML-Dateien ändern sich ebenfalls Capptain-Namespace und Attribute.

Vor der Migration:

			<capptain:CapptainPage
			...
			xmlns:capptain="using:Capptain.Agent"
			...
			</capptain:CapptainPage>

Nach der Migration:

			<engagement:EngagementPage
			...
			xmlns:engagement="using:Microsoft.Azure.Engagement"
			...
			</engagement:EngagementPage>

> [AZURE.IMPORTANT] Die Überlagerung ändert sich ebenfalls. Der neue Namespace ist `Microsoft.Azure.Engagement.Overlay`. Er muss in XAML- und CS-Dateien verwendet werden. Darüber hinaus wird `CapptainGrid` in  `EngagementGrid` umbenannt, `capptain_notification_content` und `capptain_announcement_content` werden in  `engagement_notification_content` und `engagement_announcement_content` umbenannt.

Für die Überlagerung:

			<capptain:CapptainPageOverlay
			  xmlns:capptain="using:Capptain.Overlay"
			  ...
			</capptain:CapptainPageOverlay>

Wird zu:

			<EngagementPageOverlay
			  engagement="using:Microsoft.Azure.Engagement.Overlay"
			  ...
			</engagement:EngagementPageOverlay>

Beachten Sie, dass andere Ressourcen wie Capptain-Bilder und HTML-Dateien auch für die Verwendung von "Engagement" umbenannt wurden.

### Projekt-Deklaration

Auf "Package.appxmanifest" `File Type Associations` wurde aktualisiert aus:

> -   capptain\_reach\_content to engagement\_reach\_content
> -   capptain\_log\_file to engagement\_log\_file

### Anwendungs-ID/SDK-Schlüssel

Engagement verwendet eine Verbindungszeichenfolge. Sie müssen bei Mobile Engagement keine Anwendungs-ID und keinen SDK-Schlüssel angeben, sie müssen nur eine Verbindungszeichenfolge angeben. Sie können es auf der EngagementConfiguration-Datei einrichten.

Die Konfiguration von Engagement kann in der `Resources\EngagementConfiguration.xml`-Datei des Projekts festgelegt werden.

Bearbeiten Sie diese Datei, um Folgendes anzugeben:

-   Die Verbindungszeichenfolge der Anwendung zwischen den Tags `<connectionString>` und `<\connectionString>`.

Wenn Sie sie stattdessen zur Laufzeit angeben möchten, können Sie die folgende Methode vor der Engagement Agent-Initialisierung aufrufen:

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			
			/* Initialize Engagement agent with above configuration. */
			EngagementAgent.Instance.Init(args, engagementConfiguration);

Die Verbindungszeichenfolge für die Anwendung wird im Azure-Portal angezeigt.

### Änderung des Namens von Objekten

Alle Elemente mit dem Namen... Capptain... wurden umbenannt in ...Engagement... Für Capptain und Engagement gilt dasselbe.

Beispiele für häufig verwendete Capptain-Elemente:

> -   CapptainConfiguration trägt nun den Namen EngagementConfiguration
> -   CapptainAgent trägt nun den Namen EngagementAgent
> -   CapptainReach trägt nun den Namen EngagementReach
> -   CapptainHttpConfig trägt nun den Namen EngagementHttpConfig
> -   GetCapptainPageName trägt nun den Namen GetEngagementPageName

Beachten Sie, dass sich das Umbenennen auch auf überschriebene Methoden auswirkt.

[NuGet-Website]:http://www.nuget.org/packages/Capptain.WindowsCS


<!--HONumber=47-->