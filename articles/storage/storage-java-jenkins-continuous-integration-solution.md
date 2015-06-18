<properties 
	urlDisplayName="Jenkins Continuous Integration" 
	pageTitle="Verwenden von Azure-Speicher mit einer Jenkins-Lösung für die fortlaufende Integration | Microsoft Azure" 
	metaKeywords="" 
	description="Dieses Lernprogramm zeigt, wie Sie den Azure-Blobdienst als Repository für Buildartefakte verwenden, die von einer Jenkins-Lösung für die fortlaufende Integration erstellt wurden." 
	metaCanonical="" 
	services="storage" 
	documentationCenter="java" 
	title="" 
	authors="rmcmurray" 
	solutions="" 
	manager="wpickett" 
	editor="mollybos" 
	scriptId="" 
	videoId=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="robmcm"/>

#Verwenden von Azure-Speicher mit einer Jenkins-Lösung für die fortlaufende Integration

*Von [Microsoft Open Technologies Inc..][ms-open-tech]*

Die folgenden Informationen zeigen die Verwendung des Azure-Blobdiensts als Repository von Buildartefakten, die durch eine Jenkins Continuous Integration-Lösung (CI) erstellt wurden, oder als eine Quelle von herunterladbaren Dateien, die in einem Buildvorgang verwendet werden. Dies ist zum Beispiel dann hilfreich, wenn Sie in einer agilen Entwicklungsumgebung (in Java oder anderen Sprachen) programmieren, Builds auf Basis der fortlaufenden Integration ausgeführt werden, und Sie ein Repository für Ihre Buildartefakte benötigen, sodass Sie sie beispielsweise mit anderen Mitgliedern der Organisation oder Ihren Kunden gemeinsam nutzen oder ein Archiv pflegen können. Ein anderes Szenario liegt vor, wenn für Ihren Buildauftrag an sich andere Dateien erforderlich sind, beispielsweise als Teil der Buildeingabe herunterzuladende Abhängigkeiten.

In diesem Lernprogramm verwenden Sie das Azure-Speicher-Plug-In für Jenkins CI, das von Microsoft Open Technologies, Inc. zur Verfügung gestellt wird.

## Inhaltsverzeichnis

-   [Überblick über Jenkins][]
-   [Vorteile der Verwendung des Blob-Diensts][]
-   [Voraussetzungen][]
-   [Verwenden des Blob-Diensts mit Jenkins CI][]
-   [Installieren des Azure-Speicher-Plug-Ins][]
-   [Konfigurieren des Azure-Speicher-Plug-Ins für die Verwendung Ihres Speicherkontos][]
-   [Erstellen einer Postbuildaktion, die Ihre Buildartefakte in Ihr Speicherkonto hochlädt][]
-   [Erstellen eines Buildschritts für das Herunterladen des Azure-Blobspeichers][]
-   [Vom Blob-Dienst verwendete Komponenten][]

## <a name="overview"></a>Überblick über Jenkins ##

Jenkins ermöglicht die fortlaufende Integration (Continuous Integration, CI) eines Softwareprojekts, da Entwickler ihre Codeänderungen auf einfache Weise einbinden und Builds automatisch und häufig erstellen lassen können. Dadurch wird die Produktivität der Entwickler gesteigert. Builds werden mit Versionsangaben versehen, und Buildartefakte können in verschiedene Repositorys hochgeladen werden. In diesem Thema wird gezeigt, wie Sie Azure-Blob-Speicher als Repository für die Buildartefakte verwenden. Es zeigt hoch, wie Abhängigkeiten aus dem Azure-Blobspeicher heruntergeladen werden.

Weitere Informationen zu Jenkins finden Sie unter [Meet Jenkins][].

## <a name="benefits"></a>Vorteile der Verwendung des Blob-Diensts ##

Die Verwendung des Blob-Diensts zum Hosten Ihrer Buildartefakte aus der agilen Entwicklung hat folgende Vorteile:

- Hohe Verfügbarkeit Ihrer Buildartefakte und/oder herunterladbare Abhängigkeiten.
- Schnelles Hochladen Ihrer Buildartefakte durch Jenkins CI
- Schnelles Herunterladen Ihrer Buildartefakte durch Kunden und Partner
- Kontrolle über Benutzerzugriffsrichtlinien, mit Wahlmöglichkeiten zwischen anonymer Zugriff, Zugriff per Shared Access Signature mit Ablaufdatum, privater Zugriff usw.

## <a name="prerequisites"></a>Voraussetzungen ##

Sie müssen folgende Voraussetzungen erfüllen, um den Blob-Dienst mit Ihrer Jenkins CI-Lösung zu verwenden:

- Eine Jenkins-Lösung für die fortlaufende Integration (Jenkins CI)

    Wenn Sie noch keine Jenkins CI-Lösung im Einsatz haben, können Sie eine Jenkins CI-Lösung mithilfe folgender Methode ausführen:

    1. Laden Sie für einen Java-fähigen Computer "jenkins.war" von <http://jenkins-ci.org> herunter.
    2. Führen Sie in einer Eingabeaufforderung, die für den Ordner, der "jenkins.war" enthält, geöffnet wird, folgenden Befehl aus:

        `java -jar jenkins.war`

    3. Öffnen Sie in Ihrem Browser `http://localhost:8080/`. Dadurch wird das Jenkins-Dashboard geöffnet, über das Sie das Azure-Speicher-Plug-In installieren und konfigurieren können.

        Eine typische Jenkins CI-Lösung würde zwar zur Ausführung als Service konfiguriert, die Ausführung von "jenkins.war" über die Befehlszeile reicht für dieses Lernprogramm jedoch aus.

- Ein Azure-Konto. Unter <http://www.windowsazure.com> können Sie ein Azure-Konto registrieren.

- Ein Azure-Speicherkonto. Wenn Sie noch kein Speicherkonto haben, können Sie eines erstellen, indem Sie die Schritte unter [Erstellen eines Speicherkontos][] befolgen.

- Vorkenntnisse der Jenkins CI-Lösung werden empfohlen, sind aber nicht zwingend erforderlich, da in den folgenden Abschnitten ein einfaches Beispiel verwendet wird, um zu zeigen, welche Schritte erforderlich sind, wenn Sie den Blob-Dienst als Repository für Jenking CI-Buildartefakte nutzen möchten.

## <a name="howtouse"></a>Verwenden des Blob-Diensts mit Jenkins CI ##

Um den Blob-Dienst mit Jenkins verwenden zu können, müssen Sie das Azure-Speicher-Plug-In installieren, das Plug-In für die Verwendung Ihres Speicherkontos konfigurieren und dann eine Postbuildaktion erstellen, die Ihre Buildartefakte in Ihr Speicherkonto hochlädt. Diese Schritte sind in den folgenden Abschnitten beschrieben.

## <a name="howtoinstall"></a>Installieren des Azure-Speicher-Plug-Ins ##

1. Klicken Sie im Jenkins-Dashboard auf **Manage Jenkins**.
2. Klicken Sie auf der Seite **Manage Jenkins** auf **Manage Plugins**.
3. Klicken Sie auf die Registerkarte **Available**.
4. Aktivieren Sie im Abschnitt **Artifact Uploaders** das Kontrollkästchen **Microsoft Azure Storage plugin**.
5. Klicken Sie entweder auf **Install without restart** oder auf **Download now and install after restart**.
6. Starten Sie Jenkins neu.

## <a name="howtoconfigure"></a>Konfigurieren des Azure-Speicher-Plug-Ins für die Verwendung Ihres Speicherkontos ##

1. Klicken Sie im Jenkins-Dashboard auf **Manage Jenkins**.
2. Klicken Sie auf der Seite **Manage Jenkins** auf **Configure System**.
3. Führen Sie im Bereich Microsoft **Microsoft Azure Storage Account Configuration** folgende Schritte aus:
    1. Geben Sie Ihren Speicherkontonamen ein, den Sie aus dem Azure-Portal <https://manage.windowsazure.com> abrufen können.
    2. Geben Sie Ihren Speicherkontoschlüssel ein, der ebenfalls über das Azure-Portal erhältlich ist.
    3. Verwenden Sie den Standardwert für **Blob Service Endpoint URL**, wenn Sie die öffentliche Azure-Cloud verwenden. Wenn Sie mit einer anderen Azure-Cloud arbeiten, verwenden Sie den Endpunkt, der im Azure-Verwaltungsportal für Ihr Speicherkonto angegeben ist. 
    4. Klicken Sie auf **Validate storage credentials**, um Ihr Speicherkonto zu validieren. 
    5. [Optional] Wenn Sie über weitere Speicherkonten verfügen, die Sie für Jenkins CI verfügbar machen möchten, klicken Sie auf **Add more Storage Accounts**.
    6. Klicken Sie auf **Save**, um Ihre Einstellungen zu speichern.

## <a name="howtocreatepostbuild"></a>Erstellen einer Postbuildaktion, die Ihre Buildartefakte in Ihr Speicherkonto hochlädt ##

Für das Lernprogramm müssen wir zunächst einen Auftrag erstellen, der mehrere Dateien erstellen wird, und ihn dann zur Postbuildaktion hinzufügen, um die Dateien in Ihr Speicherkonto hochzuladen.

1. Klicken Sie im Jenkins-Dashboard auf **New Item**.
2. Nennen Sie den Auftrag **MyJob**, klicken Sie auf **Build a free-style software project** und klicken Sie dann auf **OK**.
3. Klicken Sie in der Auftragskonfiguration im Bereich **Build** auf **Add build step**, und wählen Sie dann **Execute Windows batch command** aus.
4. Verwenden Sie in **Command** folgende Befehle:

        md text
        cd text
        echo Hello Azure Storage from Jenkins > hello.txt
        date /t > date.txt
        time /t >> date.txt
 
5. Klicken Sie in der Auftragskonfiguration im Bereich **Post-build Actions** auf **Add post-build action** und wählen Sie **Upload artifacts to Azure Blob storage** aus.
6. Wählen Sie unter **Storage account name** das zu verwendende Speicherkonto aus.
7. Geben Sie unter **Container name** den Containernamen ein. (Der Container wird erstellt, wenn er beim Hochladen der Buildartefakte noch nicht vorhanden ist.) Sie können Umgebungsvariablen verwenden. Geben Sie also für dieses Beispiel **${JOB_NAME}** als Containernamen ein.

    **Tipp**
    
    Unter dem Bereich **Command**, in dem Sie ein Skript für **Execute Windows batch command** eingegeben haben, befindet sich ein Link zu den von Jenkins erkannten Umgebungsvariablen. Klicken Sie auf diesen Link, um die Namen und Beschreibungen der Umgebungsvariablen anzuzeigen. Beachten Sie, dass Umgebungsvariablen, die Sonderzeichen enthalten, z. B. die Umgebungsvariable **BUILD_URL**, nicht als Containername oder gemeinsamer virtueller Pfad zulässig sind.

8. Klicken Sie für dieses Beispiel auf **Make new container public by default**. (Wenn Sie einen privaten Container verwenden möchten, müssen Sie eine Shared Access Signature erstellen, um den Zugriff zu ermöglichen. Dies geht jedoch über den Rahmen dieses Thema hinaus. Sie finden weitere Informationen zu Shared Access Signatures unter [Erstellen einer Shared Access Signature](http://go.microsoft.com/fwlink/?LinkId=279889).)
9. [Optional] Klicken Sie auf **Clean container before uploading**, wenn die Inhalte aus dem Container gelöscht werden sollen, bevor die Buildartefakte hochgeladen (lassen Sie die Option deaktiviert, wenn die Inhalte nicht aus dem Container gelöscht werden sollen) werden.
10. Geben Sie unter **List of Artifacts to upload** die Zeichenfolge **text/*.txt** ein.
11. Geben Sie **Common virtual path for uploaded artifacts** für die Zwecke dieses Lernprogramms unter **${BUILD_ID}/${BUILD_NUMBER}** ein.
12. Klicken Sie auf **Save**, um Ihre Einstellungen zu speichern.
13. Klicken Sie im Jenkins-Dashboard auf **Build Now**, um **MyJob** auszuführen. Prüfen Sie den Status in der Ausgabe der Konsole. Statusmeldungen für Azure-Speicher werden in die Ausgabe der Konsole aufgenommen, wenn die Postbuildaktion mit dem Hochladen von Buildartefakten beginnt.
14. Nach erfolgreichem Abschluss des Auftrags können Sie die Buildartefakte überprüfen, indem Sie den öffentlichen Blob öffnen.
    1. Melden Sie sich beim Azure-Verwaltungsportal <https://manage.windowsazure.com> an.
    2. Klicken Sie auf **Storage**.
    3. Klicken Sie auf den Speicherkontonamen, den Sie für Jenkins verwendet haben.
    4. Klicken Sie auf **Containers**.
    5. Klicken Sie auf den Container **myjob**. Dies ist die Version des Auftragsnamens, den Sie beim Erstellen des Jenkins-Auftrags zugewiesen haben, in Kleinbuchstaben. Containernamen und Blob-Namen bestehen im Azure-Speicher aus Kleinbuchstaben (es wird zwischen Groß- und Kleinschreibung unterschieden). In der Liste der Blobs für den Container **myjob** sollte **hello.txt** und **date.txt** angezeigt werden. Kopieren Sie die URL für beide Elemente, und öffnen Sie sie in Ihrem Browser. Sie sehen die Textdatei, die als Buildartefakt hochgeladen wurde.

Es kann nur eine "post-build"-Aktion pro Auftrag erstellt werden, die Artefakte in den Azure-Blobspeicher hochlädt. Beachten Sie, dass die einzelne "post-build"-Aktion zum Hochladen von Artefakten in den Azure-Blobspeicher mithilfe eines Semikolons als Trennzeichen verschiedene Dateien (einschließlich Platzhalter) und Pfade zu Dateien in **List of Artifacts to upload** angeben kann. Wenn Ihr Jenkins-Build beispielsweise JAR- und TXT-Dateien im Ordner **build** Ihres Arbeitsbereichs erstellt und beide in den Azure-Blobspeicher hochladen möchten, verwenden Sie Folgendes für den Wert **List of Artifacts to upload**: **build/*.jar;build/*.txt**. Sie können auch eine Doppel-Doppelpunktsyntax verwenden, um einen im Blobnamen zu verwendenden Pfad anzugeben. Wenn Sie beispielsweise möchten, dass die JAR-Dateien mithilfe von **binaries** im Blobpfad und die TXT-Dateien mithilfe von **notices** im Blobpfad hochgeladen werden, verwenden Sie Folgendes für den Wert **List of Artifacts to upload**: **build/*.jar::binaries;build/*.txt::notices**.

## <a name="howtocreatebuildstep"></a>Erstellen eines Buildschritts für das Herunterladen des Azure-Blobspeichers ##

Die folgenden Schritte zeigen, wie Sie einen Buildschritt konfigurieren, damit Elemente aus dem Azure-Blobspeicher heruntergeladen werden. Dies ist hilfreich, wenn Sie Elemente in Ihren Build einbeziehen möchten, beispielsweise JAR-Dateien, die im Azure-Blobspeicher gespeichert sind.

1. Klicken Sie in der Auftragskonfiguration im Abschnitt **Build** auf **Add build step**, und wählen Sie dann **Download from Azure Blob storage** aus.
2. Wählen Sie unter **Storage account name** das zu verwendende Speicherkonto aus.
3. Geben Sie für **Container name** den Namen des Containers an, der die herunterzuladenden Blobs enthält. Sie können Umgebungsvariablen verwenden.
4. Geben Sie unter **Blob name** den Blobnamen ein. Sie können Umgebungsvariablen verwenden. Sie können auch ein Sternchen als einen Platzhalter verwenden, nachdem Sie den bzw. die Anfangsbuchstaben des Blobnamens angeben. Beispielsweise würde **project*** alle Blobs angeben, deren Namen mit **project** beginnen.
5. [Optional] Geben Sie für **Download path** den Pfad auf dem Jenkins-Computer an, in den die Dateien aus dem Azure-Blobspeicher heruntergeladen werden sollen. Es können auch Umgebungsvariablen verwendet werden. (Wenn Sie keinen Wert für **Download path** angeben, werden die Dateien aus dem Azure-Blobspeicher in den Arbeitsbereich des Auftrags heruntergeladen.)

Wenn Sie zusätzliche Elemente aus dem Azure-Blobspeicher herunterladen möchten, können Sie zusätzliche Buildschritte erstellen.

Nach der Ausführung eines Builds können Sie die Build-Verlaufskonsolenausgabe prüfen oder den Downloadspeicherort aufrufen, um zu prüfen, ob die von Ihnen erwarteten Blobs erfolgreich heruntergeladen wurden.  

## <a name="components"></a>Vom Blob-Dienst verwendete Komponenten ##

Im Folgenden erhalten Sie einen Überblick über die Komponenten des Blob-Diensts.

- **Speicherkonto**: Alle Zugriffe auf den Azure-Speicher erfolgen über ein Speicherkonto. Dies ist die höchste Ebene des Namespaces für den Zugriff auf Blobs. Ein Konto kann eine beliebige Anzahl von Containern enthalten, solange deren Gesamtgröße 100TB nicht überschreitet.
- **Container**: Ein Container dient zur Gruppierung eines Satzes von Blobs. Alle BLOBs müssen sich in Containern befinden. Ein Konto kann eine beliebige Anzahl von Containern enthalten. In einem Container kann eine beliebige Anzahl von BLOBs gespeichert sein.
- **Blob**: Eine Datei von beliebiger Art und Größe. Es gibt zwei Arten von Blobs, die im Azure-Speicher gespeichert werden können: Block- und Seitenblobs. Die meisten Dateien sind Block-BLOBs. Ein einzelner Block-BLOB kann bis zu 200 GB groß sein. In diesem Tutorial werden Block-BLOBs verwendet. Die andere Art von BLOBs, Seiten-BLOBs, kann bis zu 1 TB groß sein und ist effizienter, wenn Byte-Bereiche in einer Datei häufig geändert werden. Weitere Informationen über Blobs finden Sie unter [Grundlegendes zu Block-Blobs und Seiten-Blobs](http://msdn.microsoft.com/library/windowsazure/ee691964.aspx).
- **URL-Format**: Blobs sind über das folgende URL-Format adressierbar:

    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
    
    (Das Format oben gilt für die öffentliche Azure-Cloud. Wenn Sie mit einer anderen Azure-Cloud arbeiten, verwenden Sie den Endpunkt im Azure-Verwaltungsportal, um Ihren URL-Endpunkt zu ermitteln.)

    Bei obigem Format steht  `storageaccount` für den Namen Ihres Speicherkontos,  `container_name` für den Namen des Containers und  `blob_name` für den Namen des Blobs. Der Containername kann mehrere Pfade umfassen, die durch einen Schrägstrich (**/**) getrennt sind. Der Beispielcontainername in diesem Lernprogramm war **MyJob**, and **${BUILD_ID}/${BUILD_NUMBER}** wurde für den gemeinsamen virtuellen Pfad verwendet. Der Blob hat also eine URL in folgendem Format:

    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

  [Überblick über Jenkins]: #overview
  [Vorteile der Verwendung des Blob-Diensts]: #benefits
  [Voraussetzungen]: #prerequisites
  [Verwenden des Blob-Diensts mit Jenkins CI]: #howtouse
  [Installieren des Azure-Speicher-Plug-Ins]: #howtoinstall
  [Konfigurieren des Azure-Speicher-Plug-Ins für die Verwendung Ihres Speicherkontos]: #howtoconfigure
  [Erstellen einer Postbuildaktion, die Ihre Buildartefakte in Ihr Speicherkonto hochlädt]: #howtocreatepostbuild
  [Erstellen eines Buildschritts für das Herunterladen des Azure-Blobspeichers]: #howtocreatebuildstep
  [Vom Blob-Dienst verwendete Komponenten]: #components
  [Vorgehensweise: Erstellen eines Speicherkontos]: http://go.microsoft.com/fwlink/?LinkId=279823
  [Meet Jenkins]: https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins
  [ms-open-tech]: http://msopentech.com

<!--HONumber=42-->
 