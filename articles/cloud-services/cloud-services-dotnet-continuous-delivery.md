<properties
	pageTitle="Kontinuierliche Zustellung für Clouddienste mit TFS in Azure | Microsoft Azure"
	description="Hier erfahren Sie, wie Sie die kontinuierliche Zustellung für Azure-Cloud-Apps einrichten. Codebeispiele für MSBuild-Befehlszeilenanweisungen und PowerShell-Skripts."
	services="cloud-services"
	documentationCenter=""
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="tarcher"/>

# Kontinuierliche Zustellung für Cloud Services in Azure

Der in diesem Artikel beschriebene Prozess zeigt, wie die kontinuierliche Zustellung für Azure-Cloud-Apps eingerichtet wird. Dieser Prozess ermöglicht es Ihnen, Pakete automatisch zu erstellen und nach jedem Einchecken von Code in Azure bereitzustellen. Der in diesem Artikel beschriebene Prozess der Paketerstellung entspricht dem Befehl **Paket** in Visual Studio, die Veröffentlichungsschritte entsprechen dem Befehl **Veröffentlichen** in Visual Studio. Der Artikel befasst sich mit den Methoden, die üblicherweise dazu dienen, einen Buildserver mit MSBuild-Befehlszeilenanweisungen und Windows PowerShell-Skripts zu erstellen. Außerdem zeigt er, wie Visual Studio Team Foundation Server - Team Build-Definitionen optional für die Verwendung von MSBuild-Befehlen und PowerShell-Skripts konfiguriert werden. Der Prozess kann an Ihre Buildumgebung und Azure-Zielumgebungen angepasst werden.

Eine bequemere Möglichkeit hierzu bietet Visual Studio Team Services, eine in Azure gehostete Version von Team Foundation Server. Weitere Informationen finden Sie unter [Fortlaufende Bereitstellung für Azure mithilfe von Visual Studio Team Services][].

Bevor Sie beginnen, müssen Sie Ihre Anwendung über Visual Studio veröffentlichen. Damit wird sichergestellt, dass alle Ressourcen verfügbar und initialisiert sind, wenn Sie versuchen, den Veröffentlichungsprozess zu automatisieren.

## Schritt 1: Konfigurieren des Buildservers

Bevor Sie ein Azure-Paket mithilfe von MSBuild erstellen können, müssen Sie die erforderliche Software und Tools auf dem Buildserver installieren.

Visual Studio muss nicht auf dem Buildserver installiert werden. Wenn Sie Team Foundation-Builddienst zum Verwalten des Buildservers verwenden möchten, folgen Sie den Anweisungen in der Dokumentation [Einrichten des Team Foundation-Builddiensts][].

1.  Installieren Sie auf dem Buildserver [.NET Framework 4.5.2][], das MSBuild einschließt.
2.  Installieren Sie die neuesten [Azure-Entwicklertools für .NET](https://azure.microsoft.com/develop/net/).
3.	Installieren Sie die [Microsoft Azure-Bibliotheken für .NET](http://go.microsoft.com/fwlink/?LinkId=623519).
4.  Kopieren Sie die Datei "Microsoft.WebApplication.targets" aus einer Visual Studio-Installation auf den Buildserver.

	Auf einem Computer, auf dem Visual Studio installiert ist, befindet sich diese Datei im Verzeichnis „C:\\Programme (x86) \\MSBuild\\Microsoft\\VisualStudio\\v14.0\\WebApplications“ Kopieren Sie die Datei in das gleiche Verzeichnis auf dem Buildserver.
5.  Installieren Sie die [Azure-Tools für Visual Studio](https://www.visualstudio.com/features/azure-tools-vs.aspx).

## Schritt 2: Erstellen eines Pakets mit MSBuild-Befehlen

In diesem Abschnitt wird erläutert, wie ein MSBuild-Befehl konstruiert wird, der ein Azure-Paket erstellt. Führen Sie diesen Schritt auf dem Buildserver aus, um sicherzustellen, dass alles richtig konfiguriert wurde und dass der MSBuild-Befehl ordnungsgemäß funktioniert. Sie können diese Befehlszeile vorhandenen Buildskripts auf dem Buildserver hinzufügen, oder Sie können die Befehlszeile in einer TFS-Builddefinition verwenden, wie im nächsten Abschnitt beschrieben. Weitere Informationen über Befehlszeilenparameter und MSBuild finden Sie unter [MSBuild-Befehlszeilenreferenz](https://msdn.microsoft.com/library/ms164311%28v=vs.140%29.aspx).

1.  Wenn Visual Studio auf dem Buildserver installiert ist, suchen Sie unter Windows im Ordner **Visual Studio Tools** nach **Visual Studio-Eingabeaufforderung**, und wählen Sie diese Option aus.

    Wenn Visual Studio auf dem Buildserver nicht installiert ist, öffnen Sie eine Eingabeaufforderung, und stellen Sie sicher, dass MSBuild.exe im Pfad verfügbar ist. MSBuild wird mit .NET Framework unter "%WINDIR%\\Microsoft.NET\\Framework\\*Version*" installiert. Um z. B. MSBuild.exe der Umgebungsvariable PATH hinzuzufügen, wenn .NET Framework 4 installiert ist, geben Sie an der Eingabeaufforderung den folgenden Befehl ein:

        set PATH=%PATH%;"C:\Windows\Microsoft.NET\Framework\v4.0.30319"

2.  Navigieren Sie an der Eingabeaufforderung zu dem Ordner, der die zu erstellende Azure-Projektdatei enthält.

3.  Führen Sie MSBuild wie im folgenden Beispiel mit der Option „/target:Publish“ aus:

        MSBuild /target:Publish

    Sie können auch eine abgekürzte Version der Option verwenden: /t:Publish. Die Option "/t:Publish" in MSBuild darf nicht mit den in Visual Studio verfügbaren Veröffentlichungsbefehlen verwechselt werden, wenn Sie das Azure-SDK installiert haben. Die Option "/t:Publish" dient nur zum Erstellen der Azure-Pakete. Die Pakete werden nicht bereitgestellt, wie dies mit den Veröffentlichungsbefehlen in Visual Studio geschieht.

    Den Projektnamen können Sie optional als MSBuild-Parameter angeben. Wird er nicht angegeben, wird das aktuelle Verzeichnis verwendet. Weitere Informationen über MSBuild-Befehlszeilenoptionen finden Sie unter [MSBuild-Befehlszeilenreferenz][1].

4.  Suchen Sie die Ausgabe. Standardmäßig erstellt dieser Befehl ein Verzeichnis unter dem Stammordner des Projekts, z. B. "*ProjectDir*\\bin\\*Configuration*\\app.publish\". Beim Erstellen eines Azure-Projekts werden zwei Dateien generiert, die Paketdatei selbst und die zugehörige Konfigurationsdatei:

    -   Project.cspkg
    -   ServiceConfiguration.*Zielprofil*.cscfg

    Standardmäßig umfasst jedes Azure-Projekt eine Dienstkonfigurationsdatei (.cscfg file) für lokale (Debugging-)Builds und eine weitere Datei für Cloudbuilds (Staging oderProduktion). Sie können Dienstkonfigurationsdateien jedoch je nach Bedarf hinzufügen oder entfernen. Wenn Sie ein Paket in Visual Studio erstellen, werden Sie gefragt, welche Dienstkonfigurationsdatei für das Paket eingeschlossen werden soll.

5.  Geben Sie die Dienstkonfigurationsdatei an. Wenn Sie ein Paket mit MSBuild erstellen, wird die lokale Dienstkonfigurationsdatei standardmäßig eingeschlossen. Wenn Sie eine andere Dienstkonfigurationsdatei einschließen möchten, legen Sie die Eigenschaft "TargetProfile" des MSBuild-Befehls wie im folgenden Beispiel fest:

        MSBuild /t:Publish /p:TargetProfile=Cloud

6.  Geben Sie den Speicherort der Ausgabe an. Legen Sie den Pfad mit der Option "/p:PublishDir=*Verzeichnis*\" fest, und geben Sie dabei wie im folgenden Beispiel auch den abschließenden umgekehrten Schrägstrich an:

        MSBuild /target:Publish /p:PublishDir=\\myserver\drops\

    Sobald Sie eine entsprechende MSBuild-Befehlszeile zum Erstellen Ihrer Projekte und zur Kombination dieser Projekte in einem Azure-Paket konstruiert und getestet haben, können Sie diese Befehlszeile Ihren Buildskripts hinzufügen. Wenn der Buildserver benutzerdefinierte Skripts verwendet, hängt dieser Prozess von den Besonderheiten Ihres benutzerdefinierten Buildprozesses ab. Wenn Sie TFS als Buildumgebung verwenden, können Sie den Anweisungen im nächsten Schritt folgen, um den Azure-Paketbuild zu Ihrem Buildprozess hinzuzufügen.

## Schritt 3: Erstellen eines Pakets mit TFS Team Build

Wenn Sie Team Foundation Server (TFS) als Buildcontroller und den Buildserver als TFS-Buildcomputer eingerichtet haben, können Sie optional für Ihr Azure-Paket einen automatischen Build einrichten. Informationen zum Einrichten und Verwenden von Team Foundation Server als Buildsystem finden Sie unter [Skalieren des Buildsystems][]. Beim folgenden Verfahren wird insbesondere vorausgesetzt, dass Sie Ihren Buildserver, wie unter [Bereitstellen und Konfigurieren eines Buildservers][] beschrieben, konfiguriert und ein Teamprojekt sowie ein Clouddienstprojekt in diesem Teamprojekt erstellt haben.

Um TFS zum Erstellen von Azure-Paketen zu konfigurieren, führen Sie die folgenden Schritte aus:

1.  Wählen Sie in Visual Studio auf dem Entwicklungscomputer im Menü "Ansicht" die Option **Team Explorer** aus, oder wählen Sie STRG + \\, STRG + M. Erweitern Sie in Team Explorer-Fenster den Knoten **Builds**, oder wählen Sie die Seite **Builds** und anschließend **Neue Builddefinition** aus.

    ![][0]

2.  Wählen Sie die Registerkarte **Trigger** aus, und geben Sie die gewünschten Bedingungen für die Erstellung des Pakets an. Geben Sie z. B. **Fortlaufende Integration** an, um das Paket zu erstellen, sobald ein Einchecken von Quellcode erfolgt.

3.	Wählen Sie die Registerkarte **Quelleinstellungen** aus, und vergewissern Sie sich, dass Ihr Projektordner in der Spalte **Quellcodeverwaltungsordner** aufgeführt ist und der Status **Aktiv** lautet.

4.  Wählen Sie die Registerkarte **Build-Standardwerte**, und prüfen Sie unter "Buildcontroller" den Namen des Buildservers. Wählen Sie außerdem die Option **Kopieren der Buildausgabe in den folgenden Ablageordner**, und geben Sie den gewünschten Ablageort an.

5.  Wählen Sie die **Prozessvorlage** aus. Wählen Sie auf der Registerkarte "Prozess" die Standardvorlage aus, und wählen Sie dann unter **Build** das Projekt aus, sofern es noch nicht ausgewählt ist. Erweitern Sie dann im Abschnitt **Build** des Rasters den Abschnitt **Erweitert**.

6.  Wählen Sie **MSBuild-Argumente**, und legen Sie die entsprechenden MSBuild-Befehlszeilenargumente wie in Schritt 2 oben erläutert fest. Geben Sie z. B. **/t:Publish /p:PublishDir=\\\myserver\\drops\** ein, um ein Paket zu erstellen und die Paketdateien in das Verzeichnis "\\\myserver\\drops\" zu kopieren:

    ![][2]

    **Hinweis:** Wenn Sie die Dateien in eine öffentliche Freigabe kopieren, lassen sich die Pakete einfacher manuell über Ihren Entwicklungscomputer bereitstellen.

5.  Prüfen Sie, ob der Buildschritt einwandfrei funktioniert, indem Sie eine Änderung am Projekt einchecken oder indem Sie einen neuen Build in die Warteschlange stellen. Klicken Sie zum Einstellen eines neuen Builds in die Warteschlange im Team Explorer mit der rechten Maustaste auf **Alle Builddefinitionen**, und wählen Sie dann **Neuen Build in Warteschlange**.

## Schritt 4: Veröffentlichen eines Pakets mit einem PowerShell-Skript

In diesem Abschnitt wird erläutert, wie ein Windows PowerShell-Skript erstellt wird, das die Ausgabe des Cloud-App-Pakets mithilfe optionaler Parameter in Azure veröffentlicht. Dieses Skript kann im benutzerdefinierten automatischen Build nach dem Buildschritt aufgerufen werden. Es kann auch über Prozessvorlagen-Workflowaktivitäten in Visual Studio TFS Team Build aufgerufen werden.

1.  Installieren Sie die [Azure PowerShell-Cmdlets][] (Version 0.6.1 oder höher). Wählen Sie während der Cmdlet-Einrichtung die Installation als Snap-In. Diese offiziell unterstützte Version ersetzt die ältere von CodePlex angebotene Version, auch wenn diese die Versionsnummer 2.x.x haben.

2.  Starten Sie Azure PowerShell über das Startmenü oder die Startseite. Auf diese Weise werden die Azure PowerShell-Cmdlets geladen.

3.  Prüfen Sie an der PowerShell-Eingabeaufforderung, ob die PowerShell-Cmdlets geladen sind. Geben Sie dazu den Teilbefehl `Get-Azure` ein, und drücken Sie anschließend die Tabulatortaste, um die Anweisung zu vervollständigen.

    Wenn Sie die Tabulatortaste mehrfach drücken, werden verschiedene Azure PowerShell-Befehle angezeigt.

4.  Prüfen Sie, ob Sie eine Verbindung mit dem Azure-Abonnement herstellen können, indem Sie die Abonnementinformationen aus der .publishsettings-Datei importieren.

    `Import-AzurePublishSettingsFile c:\scripts\WindowsAzure\default.publishsettings`

    Geben Sie anschließend folgenden Befehl ein:

    `Get-AzureSubscription`

    Damit werden Informationen über Ihr Abonnement angezeigt. Prüfen Sie, ob alles richtig ist.

4.  Speichern Sie die am Ende dieses Artikels bereitgestellte Skriptvorlage im Skriptordner unter „C:\\scripts\\WindowsAzure\**PublishCloudService.ps1**“.

5.  Prüfen Sie den Parameterabschnitt des Skripts. Fügen Sie Standardwerte hinzu, oder ändern Sie diese. Diese Werte können durch Übergabe expliziter Parameter stets überschrieben werden.

6.  Stellen Sie sicher, dass in Ihrem Abonnement gültige Clouddienst- und Speicherkonten erstellt sind, die vom Veröffentlichungsskript als Ziel angesprochen werden können. Das Speicherkonto (Blobspeicher) dient dazu, das Bereitstellungspaket und die Konfigurationsdatei hochzuladen und vorübergehend zu speichern, während die Bereitstellung erstellt wird.

    -   Wenn Sie einen neuen Clouddienst erstellen möchten, können Sie dieses Skript aufrufen oder das Azure-Verwaltungsportal verwenden. Der Name des Clouddiensts wird als Präfix in einem vollqualifizierten Domänennamen verwendet und muss daher eindeutig sein.

            New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

    -   Wenn Sie ein neues Speicherkonto erstellen möchten, können Sie dieses Skript aufrufen oder das Azure-Verwaltungsportal verwenden. Der Name des Speicherkontos wird als Präfix in einem vollqualifizierten Domänennamen verwendet und muss daher eindeutig sein. Sie können versuchen, den gleichen Namen wie für den Clouddienst zu verwenden.

            New-AzureStorageAccount -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

7.  Rufen Sie das Skript direkt in Azure PowerShell auf, oder reihen Sie es in der Host-Buildautomatisierung nach dem Paketbuild ein.

    >[AZURE.IMPORTANT] Standardmäßig werden vorhandene Bereitstellungen, falls sie erkannt werden, von dem Skript stets gelöscht oder ersetzt. Dies ist notwendig, um die kontinuierliche Zustellung bei der Automatisierung zu ermöglichen, bei der keine Eingabeaufforderungen möglich sind.

    **Beispielszenario 1:** kontinuierliche Bereitstellung in der Stagingumgebung eines Diensts:

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Staging -serviceName mycloudservice -storageAccountName mystoragesaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    In der Regel folgen danach eine Testlaufverifizierung und ein VIP-Swap. Der VIP-Swap kann über das Azure-Verwaltungsportal oder mit dem Move-Deployment-Cmdlet ausgeführt werden.

    **Beispielszenario 2:** kontinuierliche Bereitstellung in der Produktionsumgebung eines dedizierten Testdiensts

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Production -enableDeploymentUpgrade 1 -serviceName mycloudservice -storageAccountName mystorageaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    **Remotedesktop:**

    Wenn Remotedesktop in Ihrem Azure-Projekt aktiviert ist, müssen Sie zusätzlich einmalige Schritte ausführen, um sicherzustellen, dass das richtige Clouddienst-Zertifikat zu allen Clouddiensten hochgeladen wird, auf die dieses Skript zielt.

    Suchen Sie die von Ihren Rollen erwarteten Zertifikatfingerabdruckwerte. Die Fingerabdruckwerte sind im Abschnitt "Zertifikate" der Cloudkonfigurationsdatei (d. h. ServiceConfiguration.Cloud.cscfg) sichtbar. Sie werden außerdem im Dialogfeld "Remotedesktopkonfiguration" von Visual Studio angezeigt, wenn Sie "Optionen anzeigen" wählen und das ausgewählte Zertifikat anzeigen.

        <Certificates>
              <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="C33B6C432C25581601B84C80F86EC2809DC224E8" thumbprintAlgorithm="sha1" />
        </Certificates>

    Laden Sie Remotedesktopzertifikate in einem einmaligen Schritt während der Einrichtung mit dem folgenden Cmdlet-Skript hoch:

        Add-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MY<THUMBPRINT>)

    Zum Beispiel:

        Add-AzureCertificate -serviceName 'mytestcloudservice' -certToDeploy (get-item cert:\CurrentUser\MY\C33B6C432C25581601B84C80F86EC2809DC224E8

    Alternativ können Sie die PFX-Zertifikatdatei mit privatem Schlüssel exportieren und Zertifikate über das Azure-Verwaltungsportal zu jedem Zielclouddienst hochladen. Weitere Informationen hierzu finden Sie im folgenden Artikel: [http://msdn.microsoft.com/library/windowsazure/gg443832.aspx][].

    **Upgrade einer Bereitstellung im Vergleich zum Löschen einer Bereitstellung -> neue Bereitstellung**

    In der Standardeinstellung aktualisiert das Skript eine Bereitstellung ($enableDeploymentUpgrade = 1), wenn kein Parameter übergeben wird oder der Wert 1 explizit übergeben wird. Bei Einzelinstanzen bietet dies den Vorteil, dass die Bereitstellung weniger Zeit in Anspruch nimmt als eine vollständige Bereitstellung. Bei Instanzen, die hoch verfügbar sein müssen, bietet dies außerdem den Vorteil, dass einige Instanzen weiter aktiv sein können, während andere aktualisiert werden (wandernde Updatedomäne). Außerdem wird Ihr VIP nicht gelöscht.

    Die Bereitstellung des Upgrades kann im Skript deaktiviert werden ($enableDeploymentUpgrade = 0) oder durch Übergabe von *-enableDeploymentUpgrade 0* als Parameter. Dadurch wird das Skriptverhalten so geändert, dass zuerst vorhandene Bereitstellungen gelöscht werden und anschließend eine neue Bereitstellung erstellt wird.

    >[AZURE.IMPORTANT] Standardmäßig werden vorhandene Bereitstellungen, falls sie erkannt werden, von dem Skript stets gelöscht oder ersetzt. Dies ist notwendig, um die kontinuierliche Zustellung bei der Automatisierung zu ermöglichen, bei der keine Eingabeaufforderungen möglich sind.

## Schritt 5: Veröffentlichen eines Pakets mit TFS Team Build

Mit diesem optionalen Schritt wird TFS Team Build in das in Schritt 4 erstellte Skript eingereiht, das die Veröffentlichung des Paketbuilds in Azure übernimmt. Dies hat die Änderung der von der Builddefinition verwendeten Prozessvorlage zur Folge, sodass eine Veröffentlichungsaktivität am Ende des Workflows ausgeführt wird. Die Veröffentlichungsaktivität führt den PowerShell-Befehl aus und übergibt Parameter aus dem Build. Die Ausgabe der MSBuild-Ziele und des Veröffentlichungsskripts wird in die Standard-Buildausgabe geleitet.

1.  Bearbeiten Sie die für die kontinuierliche Bereitstellung verantwortliche Builddefinition.

2.  Wählen Sie die Registerkarte **Prozess**.

3.	Befolgen Sie [diese Anweisungen](http://msdn.microsoft.com/library/dd647551.aspx), um ein Aktivitätsprojekt für die Buildprozessvorlage hinzuzufügen, laden Sie die Standardvorlage herunter. fügen Sie die Vorlage dem Projekt hinzu, und checken Sie es ein. Geben Sie der Buildprozessvorlage einen neuen Namen, z. B. "AzureBuildProcessTemplate".

3.  Kehren Sie zur Registerkarte **Prozess** zurück, und zeigen Sie mithilfe von **Details anzeigen** eine Liste der verfügbaren Buildprozessvorlagen an. Klicken Sie auf die Schaltfläche **Neu...**, und navigieren Sie zu dem gerade hinzugefügten Projekt, und checken Sie es ein. Gehen Sie zur soeben erstellten Vorlage, und wählen Sie **OK** aus.

4.  Öffnen Sie die ausgewählte Prozessvorlage zur Bearbeitung. Sie können sie direkt im Workflow-Designer oder im XAML-Editor öffnen, um die XAML-Datei zu bearbeiten.

5.  Fügen Sie die folgende Liste neuer Argumente als separate Zeilenelemente auf der Registerkarte "Argumente" des Workflow-Designers ein. Für alle Argumente muss direction=In und type=String gelten. Mit diesen Einstellungen werden Parameter aus der Builddefinition in den Workflow übernommen und anschließend zum Aufrufen des Veröffentlichungsskripts verwendet.

        SubscriptionName
        StorageAccountName
        CloudConfigLocation
        PackageLocation
        Environment
        SubscriptionDataFileLocation
        PublishScriptLocation
        ServiceName

    ![][3]

    Die entsprechende XAML-Datei sieht folgendermaßen aus:

        <Activity  _ />
          <x:Members>
            <x:Property Name="BuildSettings" Type="InArgument(mtbwa:BuildSettings)" />
            <x:Property Name="TestSpecs" Type="InArgument(mtbwa:TestSpecList)" />
            <x:Property Name="BuildNumberFormat" Type="InArgument(x:String)" />
            <x:Property Name="CleanWorkspace" Type="InArgument(mtbwa:CleanWorkspaceOption)" />
            <x:Property Name="RunCodeAnalysis" Type="InArgument(mtbwa:CodeAnalysisOption)" />
            <x:Property Name="SourceAndSymbolServerSettings" Type="InArgument(mtbwa:SourceAndSymbolServerSettings)" />
            <x:Property Name="AgentSettings" Type="InArgument(mtbwa:AgentSettings)" />
            <x:Property Name="AssociateChangesetsAndWorkItems" Type="InArgument(x:Boolean)" />
            <x:Property Name="CreateWorkItem" Type="InArgument(x:Boolean)" />
            <x:Property Name="DropBuild" Type="InArgument(x:Boolean)" />
            <x:Property Name="MSBuildArguments" Type="InArgument(x:String)" />
            <x:Property Name="MSBuildPlatform" Type="InArgument(mtbwa:ToolPlatform)" />
            <x:Property Name="PerformTestImpactAnalysis" Type="InArgument(x:Boolean)" />
            <x:Property Name="CreateLabel" Type="InArgument(x:Boolean)" />
            <x:Property Name="DisableTests" Type="InArgument(x:Boolean)" />
            <x:Property Name="GetVersion" Type="InArgument(x:String)" />
            <x:Property Name="PrivateDropLocation" Type="InArgument(x:String)" />
            <x:Property Name="Verbosity" Type="InArgument(mtbw:BuildVerbosity)" />
            <x:Property Name="Metadata" Type="mtbw:ProcessParameterMetadataCollection" />
            <x:Property Name="SupportedReasons" Type="mtbc:BuildReason" />
            <x:Property Name="SubscriptionName" Type="InArgument(x:String)" />
            <x:Property Name="StorageAccountName" Type="InArgument(x:String)" />
            <x:Property Name="CloudConfigLocation" Type="InArgument(x:String)" />
            <x:Property Name="PackageLocation" Type="InArgument(x:String)" />
            <x:Property Name="Environment" Type="InArgument(x:String)" />
            <x:Property Name="SubscriptionDataFileLocation" Type="InArgument(x:String)" />
            <x:Property Name="PublishScriptLocation" Type="InArgument(x:String)" />
            <x:Property Name="ServiceName" Type="InArgument(x:String)" />
          </x:Members>

          <this:Process.MSBuildArguments>

6.  Fügen Sie am Ende von "Ausführung mit Agent" eine neue Sequenz hinzu:

    1.  Fügen Sie zunächst eine Aktivität vom Typ If-Anweisung hinzu, um zu prüfen, ob eine gültige Skriptdatei vorhanden ist. Legen Sie die Bedingung auf den folgenden Wert fest:

            Not String.IsNullOrEmpty(PublishScriptLocation)

    2.  Fügen Sie anschließend im Then-Fall der If-Anweisung eine neue Sequenzaktivität hinzu. Legen Sie den Anzeigenamen auf "Start publish" fest.

    3.  Fügen Sie bei weiterhin ausgewählter "Start publish"-Sequenz die folgende Liste neuer Variablen auf der Registerkarte "Variablen" im Workflow-Designer als separate Zeilenelemente hinzu. Für alle Variablen muss Folgendes gelten: Variable type=String und Scope=Start publish. Mit diesen Einstellungen werden Parameter aus der Builddefinition in den Workflow übernommen und anschließend zum Aufrufen des Veröffentlichungsskripts verwendet.

        -   SubscriptionDataFilePath, Typ String (Zeichenfolge)

        -   PublishScriptFilePath, Typ String (Zeichenfolge)

            ![][4]

    4.  Wenn Sie TFS 2012 oder früher verwenden, fügen Sie eine ConvertWorkspaceItem-Aktivität am Anfang der neuen Sequenz hinzu. Wenn Sie TFS 2013 oder höher verwenden, fügen Sie eine GetLocalPath-Aktivität am Anfang der neuen Sequenz hinzu. Legen Sie für ein ConvertWorkspaceItem die Eigenschaften folgendermaßen fest: Direction=ServerToLocal, DisplayName='Convert publish script filename', Input=' PublishScriptLocation', Result='PublishScriptFilePath', Workspace='Workspace'. Legen Sie für eine GetLocalPath-Aktivität die Eigenschaft "IncomingPath" auf 'PublishScriptLocation' und "Result" auf 'PublishScriptFilePath' fest. Mit dieser Aktivität wird der Pfad zum Veröffentlichungsskript von TFS-Serverspeicherorten (falls zutreffend) in einen standardmäßigen Pfad auf dem lokalen Datenträger konvertiert.

    5.  Wenn Sie TFS 2012 oder früher verwenden, fügen Sie eine weitere ConvertWorkspaceItem-Aktivität am Ende der neuen Sequenz hinzu. Direction=ServerToLocal, DisplayName='Convert subscription filename', Input=' SubscriptionDataFileLocation', Result= 'SubscriptionDataFilePath', Workspace='Workspace'. Wenn Sie TFS 2013 oder höher verwenden, fügen Sie eine weitere GetLocalPath-Aktivität hinzu. IncomingPath='SubscriptionDataFileLocation' und Result='SubscriptionDataFilePath'.

    6.  Fügen Sie am Ende der neuen Sequenz eine InvokeProcess-Aktivität hinzu. Mit dieser Aktivität wird PowerShell.exe mit den von der Builddefinition übergebenen Argumenten aufgerufen.

        1.  Arguments = String.Format(" -File ""{0}"" -serviceName {1} -storageAccountName {2} -packageLocation ""{3}"" -cloudConfigLocation ""{4}"" -subscriptionDataFile ""{5}"" -selectedSubscription {6} -environment ""{7}""", PublishScriptFilePath, ServiceName, StorageAccountName, PackageLocation, CloudConfigLocation, SubscriptionDataFilePath, SubscriptionName, Environment)

        2.  DisplayName = Execute publish script

        3.  FileName = "PowerShell" (einschließlich der Anführungszeichen)

        4.  OutputEncoding= System.Text.Encoding.GetEncoding(System.Globalization.CultureInfo.InstalledUICulture.TextInfo.OEMCodePage)

    7.  Legen Sie im Textfeld **Standardausgabe für Handle** von InvokeProcess den Textfeldwert auf "data" fest. Dies ist eine Variable zum Speichern der Standardausgabedaten.

    8.  Fügen Sie direkt unterhalb des Abschnitts **Standardausgabe für Handle** eine WriteBuildMessage-Aktivität hinzu. Legen Sie Folgendes fest: Importance='Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High' und Message='data'. Damit wird sichergestellt, dass die Standardausgabe des Skripts in die Buildausgabe geschrieben wird.

    9.  Legen Sie im Textfeld **Fehlerausgabe für Handle** von InvokeProcess den Textfeldwert auf "data" fest. Dies ist eine Variable zum Speichern der Standardfehlerdaten.

    10. Fügen Sie direkt unterhalb des Abschnitts **Fehlerausgabe für Handle** eine WriteBuildError-Aktivität hinzu. Legen Sie Message='data' fest. Damit wird sichergestellt, dass die Standardfehler des Skripts in die Buildfehlerausgabe geschrieben werden.

	11. Korrigieren Sie alle durch blaue Ausrufungszeichen angezeigten Fehler. Zeigen Sie auf das Ausrufezeichen, um einen Hinweis zum Fehler zu erhalten. Speichern Sie den Workflow, um die Fehler zu löschen.

    Das Endergebnis der Aktivitäten zum Veröffentlichen des Workflows sieht im Designer folgendermaßen aus:

    ![][5]

    Das Endergebnis der Aktivitäten zum Veröffentlichen des Workflows sieht in der XAML-Datei folgendermaßen aus:

		<If Condition="[Not String.IsNullOrEmpty(PublishScriptLocation)]" sap2010:WorkflowViewState.IdRef="If_1">
	        <If.Then>
	          <Sequence DisplayName="Start Publish" sap2010:WorkflowViewState.IdRef="Sequence_4">
	            <Sequence.Variables>
	              <Variable x:TypeArguments="x:String" Name="SubscriptionDataFilePath" />
	              <Variable x:TypeArguments="x:String" Name="PublishScriptFilePath" />
	            </Sequence.Variables>
	            <mtbwa:ConvertWorkspaceItem DisplayName="Convert publish script filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_1" Input="[PublishScriptLocation]" Result="[PublishScriptFilePath]" Workspace="[Workspace]" />
	            <mtbwa:ConvertWorkspaceItem DisplayName="Convert subscription filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_2" Input="[SubscriptionDataFileLocation]" Result="[SubscriptionDataFilePath]" Workspace="[Workspace]" />
	            <mtbwa:InvokeProcess Arguments="[String.Format("; -File ";";{0}";"; -serviceName {1}&#xD;&#xA;            -storageAccountName {2} -packageLocation ";";{3}";";&#xD;&#xA;            -cloudConfigLocation ";";{4}";"; -subscriptionDataFile ";";{5}";";&#xD;&#xA;            -selectedSubscription {6} -environment ";";{7}";";";,&#xD;&#xA;            PublishScriptFilePath, ServiceName, StorageAccountName,&#xD;&#xA;            PackageLocation, CloudConfigLocation,&#xD;&#xA;            SubscriptionDataFilePath, SubscriptionName, Environment)]" DisplayName="'Execute Publish Script'" FileName="[PowerShell]" sap2010:WorkflowViewState.IdRef="InvokeProcess_1">
	              <mtbwa:InvokeProcess.ErrorDataReceived>
	                <ActivityAction x:TypeArguments="x:String">
	                  <ActivityAction.Argument>
	                    <DelegateInArgument x:TypeArguments="x:String" Name="data" />
	                  </ActivityAction.Argument>
	                  <mtbwa:WriteBuildError Message="{x:Null}" sap2010:WorkflowViewState.IdRef="WriteBuildError_1" />
	                </ActivityAction>
	              </mtbwa:InvokeProcess.ErrorDataReceived>
	              <mtbwa:InvokeProcess.OutputDataReceived>
	                <ActivityAction x:TypeArguments="x:String">
	                  <ActivityAction.Argument>
	                    <DelegateInArgument x:TypeArguments="x:String" Name="data" />
	                  </ActivityAction.Argument>
	                  <mtbwa:WriteBuildMessage sap2010:WorkflowViewState.IdRef="WriteBuildMessage_2" Importance="[Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High]" Message="[data]" mva:VisualBasic.Settings="Assembly references and imported namespaces serialized as XML namespaces" />
	                </ActivityAction>
	              </mtbwa:InvokeProcess.OutputDataReceived>
	            </mtbwa:InvokeProcess>
	          </Sequence>
	        </If.Then>
	      </If>
	    </Sequence>


7.  Speichern Sie den Workflow für die Buildprozessvorlage, und checken Sie diese Datei ein.

8.  Bearbeiten Sie die Builddefinition (schließen Sie sie, wenn sie bereits geöffnet ist), und klicken Sie auf die Schaltfläche **Neu**, wenn die neue Vorlage noch nicht in der Liste der Prozessvorlagen angezeigt wird.

9.  Legen Sie Parametereigenschaftenwerte im Abschnitt "Sonstiges" wie folgt fest:

    1.  CloudConfigLocation ='c:\\drops\\app.publish\\ServiceConfiguration.Cloud.cscfg' *Dieser Wert wird abgeleitet von: ($PublishDir)ServiceConfiguration.Cloud.cscfg*

    2.  PackageLocation = 'c:\\drops\\app.publish\\ContactManager.Azure.cspkg' *Dieser Wert wird abgeleitet von: ($PublishDir)($ProjectName).cspkg*

    3.  PublishScriptLocation = 'c:\\scripts\\WindowsAzure\\PublishCloudService.ps1'

    4.  ServiceName = 'mycloudservicename' *Verwenden Sie hier den Namen des entsprechenden Clouddiensts*

    5.  Environment = 'Staging'

    6.  StorageAccountName = 'mystorageaccountname' *Verwenden Sie hier den Namen des entsprechenden Speicherkontos*

    7.  SubscriptionDataFileLocation = 'c:\\scripts\\WindowsAzure\\Subscription.xml'

    8.  SubscriptionName = 'default'

    ![][6]

10. Speichern Sie die Änderungen in der Builddefinition.

11. Stellen Sie einen Build in die Warteschlange, um sowohl den Paketbuild als auch die Paketveröffentlichung auszuführen. Wenn Sie einen Auslöser auf "Fortlaufende Integration" eingestellt haben, wird dieses Verhalten bei jedem Einchecken ausgeführt.

### Skriptvorlage PublishCloudService.ps1

```
Param(  $serviceName = "",
        $storageAccountName = "",
        $packageLocation = "",
        $cloudConfigLocation = "",
        $environment = "Staging",
        $deploymentLabel = "ContinuousDeploy to $servicename",
        $timeStampFormat = "g",
        $alwaysDeleteExistingDeployments = 1,
        $enableDeploymentUpgrade = 1,
        $selectedsubscription = "default",
        $subscriptionDataFile = ""
     )


function Publish()
{
	$deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot -ErrorVariable a -ErrorAction silentlycontinue
    if ($a[0] -ne $null)
    {
        Write-Output "$(Get-Date -f $timeStampFormat) - No deployment is detected. Creating a new deployment. "
    }
    #check for existing deployment and then either upgrade, delete + deploy, or cancel according to $alwaysDeleteExistingDeployments and $enableDeploymentUpgrade boolean variables
	if ($deployment.Name -ne $null)
	{
		switch ($alwaysDeleteExistingDeployments)
	    {
	        1
			{
                switch ($enableDeploymentUpgrade)
                {
                    1  #Update deployment inplace (usually faster, cheaper, won't destroy VIP)
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Upgrading deployment."
				        UpgradeDeployment
                    }
                    0  #Delete then create new deployment
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Deleting deployment."
				        DeleteDeployment
                        CreateNewDeployment

                    }
                } # switch ($enableDeploymentUpgrade)
			}
	        0
			{
				Write-Output "$(Get-Date -f $timeStampFormat) - ERROR: Deployment exists in $servicename.  Script execution cancelled."
				exit
			}
	    } #switch ($alwaysDeleteExistingDeployments)
	} else {
            CreateNewDeployment
    }
}

function CreateNewDeployment()
{
	write-progress -id 3 -activity "Creating New Deployment" -Status "In progress"
	Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: In progress"

	$opstat = New-AzureDeployment -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName

    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid

    write-progress -id 3 -activity "Creating New Deployment" -completed -Status "Complete"
	Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: Complete, Deployment ID: $completeDeploymentID"

	StartInstances
}

function UpgradeDeployment()
{
	write-progress -id 3 -activity "Upgrading Deployment" -Status "In progress"
	Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: In progress"

    # perform Update-Deployment
	$setdeployment = Set-AzureDeployment -Upgrade -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName -Force

    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid

    write-progress -id 3 -activity "Upgrading Deployment" -completed -Status "Complete"
	Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: Complete, Deployment ID: $completeDeploymentID"
}

function DeleteDeployment()
{

	write-progress -id 2 -activity "Deleting Deployment" -Status "In progress"
	Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: In progress"

    #WARNING - always deletes with force
	$removeDeployment = Remove-AzureDeployment -Slot $slot -ServiceName $serviceName -Force

	write-progress -id 2 -activity "Deleting Deployment: Complete" -completed -Status $removeDeployment
	Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: Complete"

}

function StartInstances()
{
	write-progress -id 4 -activity "Starting Instances" -status "In progress"
	Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: In progress"

    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $runstatus = $deployment.Status

    if ($runstatus -ne 'Running')
    {
	    $run = Set-AzureDeployment -Slot $slot -ServiceName $serviceName -Status Running
    }
	$deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
	$oldStatusStr = @("") * $deployment.RoleInstanceList.Count

	while (-not(AllInstancesRunning($deployment.RoleInstanceList)))
	{
		$i = 1
		foreach ($roleInstance in $deployment.RoleInstanceList)
		{
			$instanceName = $roleInstance.InstanceName
			$instanceStatus = $roleInstance.InstanceStatus

			if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
			{
				$oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
				Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
			}

			write-progress -id (4 + $i) -activity "Starting Instance '$instanceName'" -status "$instanceStatus"
			$i = $i + 1
		}

		sleep -Seconds 1

		$deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
	}

	$i = 1
	foreach ($roleInstance in $deployment.RoleInstanceList)
	{
		$instanceName = $roleInstance.InstanceName
		$instanceStatus = $roleInstance.InstanceStatus

		if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
		{
			$oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
			Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
		}

		$i = $i + 1
	}

    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
	$opstat = $deployment.Status

	write-progress -id 4 -activity "Starting Instances" -completed -status $opstat
	Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: $opstat"
}

function AllInstancesRunning($roleInstanceList)
{
	foreach ($roleInstance in $roleInstanceList)
	{
		if ($roleInstance.InstanceStatus -ne "ReadyRole")
		{
			return $false
		}
	}

	return $true
}

#configure powershell with Azure 1.7 modules
Import-Module Azure

#configure powershell with publishsettings for your subscription
$pubsettings = $subscriptionDataFile
Import-AzurePublishSettingsFile $pubsettings
Set-AzureSubscription -CurrentStorageAccountName $storageAccountName -SubscriptionName $selectedsubscription
Select-AzureSubscription $selectedsubscription

#set remaining environment variables for Azure cmdlets
$subscription = Get-AzureSubscription $selectedsubscription
$subscriptionname = $subscription.subscriptionname
$subscriptionid = $subscription.subscriptionid
$slot = $environment

#main driver - publish & write progress to activity log
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script started."
Write-Output "$(Get-Date -f $timeStampFormat) - Preparing deployment of $deploymentLabel for $subscriptionname with Subscription ID $subscriptionid."

Publish

$deployment = Get-AzureDeployment -slot $slot -serviceName $servicename
$deploymentUrl = $deployment.Url

Write-Output "$(Get-Date -f $timeStampFormat) - Created Cloud Service with URL $deploymentUrl."
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script finished."
```

## Nächste Schritte

Informationen zum Aktivieren des Remotedebuggens bei Verwendung der kontinuierlichen Zustellung finden Sie unter [Aktivieren von Remotedebuggen, wenn kontinuierliche Zustellung für die Veröffentlichung in Azure verwendet wird](cloud-services-virtual-machines-dotnet-continuous-delivery-remote-debugging.md).

  [Fortlaufende Bereitstellung für Azure mithilfe von Visual Studio Team Services]: cloud-services-continuous-delivery-use-vso.md
  [Einrichten des Team Foundation-Builddiensts]: https://msdn.microsoft.com/library/ee259687.aspx
  [.NET Framework 4]: https://www.microsoft.com/download/details.aspx?id=17851
  [.NET Framework 4.5]: https://www.microsoft.com/download/details.aspx?id=30653
  [.NET Framework 4.5.2]: https://www.microsoft.com/download/details.aspx?id=42643
  [Skalieren des Buildsystems]: https://msdn.microsoft.com/library/dd793166.aspx
  [Bereitstellen und Konfigurieren eines Buildservers]: https://msdn.microsoft.com/library/ms181712.aspx
  [Azure PowerShell-Cmdlets]: powershell-install-configure.md
  [the .publishsettings file]: https://manage.windowsazure.com/download/publishprofile.aspx?wa=wsignin1.0
  [0]: ./media/cloud-services-dotnet-continuous-delivery/tfs-01bc.png
  [2]: ./media/cloud-services-dotnet-continuous-delivery/tfs-02.png
  [3]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-03.png
  [4]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-04.png
  [5]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-05.png
  [6]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-06.png

<!---HONumber=AcomDC_0204_2016-->