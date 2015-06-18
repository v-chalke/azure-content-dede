<properties 
    title="Elastic Scale Security Configurations" 
    pageTitle="Elastic Scale - Sicherheitskonfigurationen" 
    description="Sicherheit für Split-Merge-Dienste mithilfe von Elastic Scale für Azure SQL-Datenbank" 
    metaKeywords="Elastic Scale Security Configurations, Azure SQL Database sharding, elastic scale " 
    services="sql-database" documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng@microsoft.com"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" ms.topic="article" ms.date="03/20/2015" 
    ms.author="sidneyh" />

# Split-Merge-Sicherheitskonfiguration  

Zur Verwendung des Split-Merge-Diensts müssen Sie die Sicherheit ordnungsgemäß konfigurieren. Der Dienst ist Teil der Funktion Elastic Scale von Microsoft Azure SQL-Datenbank. Weitere Informationen finden Sie im [Lernprogramm zu Elastic Scale - Split-Merge-Dienst](sql-database-elastic-scale-configure-deploy-split-and-merge.md)

## Konfigurieren von Zertifikaten

Zertifikate werden auf zwei Arten konfiguriert. 

1. [So konfigurieren Sie das SSL-Zertifikat][]
2. [So konfigurieren Sie Clientzertifikate][] 

## So rufen Sie Zertifikate ab

Zertifikate können von öffentlichen Zertifizierungsstellen (CAs) oder vom [Windows-Zertifikatdienst](http://msdn.microsoft.com/library/windows/desktop/aa376539.aspx) abgerufen werden. Dies sind die bevorzugten Methoden für den Erhalt von Zertifikaten.

Wenn diese Optionen nicht verfügbar sind, können Sie **selbstsignierte Zertifikate** generieren.
 
## Tools zum Erstellen von Zertifikaten

* [makecert.exe](http://msdn.microsoft.com/library/bfsktky3.aspx)
* [pvk2pfx.exe](http://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### So führen Sie die Tools aus

* Von einer Developer-Eingabeaufforderung für Visual Studio; weitere Einzelheiten finden Sie unter [Visual Studio-Eingabeaufforderung](http://msdn.microsoft.com/library/ms229859.aspx) 

    Falls installiert, gehen Sie zu:

        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 

* Laden Sie das WDK über folgende Seite herunter: [Windows 8.1: Herunterladen von Kits und Tools](http://msdn.microsoft.com/windows/hardware/gg454513#drivers)

##    <a name="to-configure-ssl-cert"></a>So konfigurieren Sie das SSL-Zertifikat
Ein SSL-Zertifikat ist zum Verschlüsseln der Kommunikation und Authentifizieren des Servers erforderlich. Wählen Sie das zutreffende unter den folgenden drei Szenarien, und führen Sie alle zugehörigen Schritte aus:

### Erstellen eines neuen selbstsignierten Zertifikats

1.    [Erstellen eines selbstsignierten Zertifikats][]
2.    [Erstellen einer PFX-Datei für ein selbstsigniertes SSL-Zertifikat][]
3.    [Hochladen des SSL-Zertifikats zum Cloud-Dienst][]
4.    [Aktualisieren des SSL-Zertifikats in der Dienstkonfigurationsdatei][]
5.    [Importieren der SSL-Zertifizierungsstelle][]

### So verwenden Sie ein vorhandenes Zertifikat aus dem Zertifikatspeicher
1. [Exportieren des SSL-Zertifikats aus dem Zertifikatspeicher][]
2. [Hochladen des SSL-Zertifikats zum Cloud-Dienst][]
3. [Aktualisieren des SSL-Zertifikats in der Dienstkonfigurationsdatei][]

### So verwenden Sie ein vorhandenes Zertifikat in einer PFX-Datei

1. [Hochladen des SSL-Zertifikats zum Cloud-Dienst][]
2. [Aktualisieren des SSL-Zertifikats in der Dienstkonfigurationsdatei][]

## <a name="configuring-client-certs"></a>So konfigurieren Sie Clientzertifikate
Clientzertifikate sind erforderlich, um Anforderungen an den Dienst zu authentifizieren. Wählen Sie das zutreffende unter den folgenden drei Szenarien, und führen Sie alle zugehörigen Schritte aus:

### Deaktivieren von Clientzertifikaten
1.    [Deaktivieren der zertifikatbasierten Clientauthentifizierung][]

### Ausstellen eines neuen selbstsignierten Clientzertifikats
1.    [Erstellen einer selbstsignierten Zertifizierungsstelle][]
2.    [Hochladen eines CA-Zertifikats zum Cloud-Dienst][]
3.    [Aktualisieren des CA-Zertifikats in der Dienstkonfigurationsdatei][]
4.    [Ausstellen von Clientzertifikaten][]
5.    [Erstellen von PFX-Dateien für Clientzertifikate][]
6.    [Importieren des Clientzertifikats][]
7.    [Kopieren der Clientfingerabdrücke][]
8.    [Konfigurieren der zulässigen Clients in der Dienstkonfigurationsdatei][]

### So verwenden Sie vorhandene Clientzertifikate
1.    [Suchen des öffentlichen Schlüssels der Zertifizierungsstelle][]
2.    [Hochladen eines CA-Zertifikats zum Cloud-Dienst][]
3.    [Aktualisieren des CA-Zertifikats in der Dienstkonfigurationsdatei][]
4.    [Kopieren der Clientfingerabdrücke][]
5.    [Konfigurieren der zulässigen Clients in der Dienstkonfigurationsdatei][]
6.    [Konfigurieren der Sperrprüfung für Clientzertifikate][]

## <a name="allowed-ip-addresses"></a>Zulässige IP-Adressen

Der Zugriff auf die Dienstendpunkte kann auf bestimmte Bereiche von IP-Adressen beschränkt werden.

## So konfigurieren Sie die Verschlüsselung für den Speicher

Ein Zertifikat ist erforderlich, um die Anmeldeinformationen zu verschlüsseln, die im Metadatenspeicher gespeichert sind. Wählen Sie das zutreffende unter den folgenden drei Szenarien, und führen Sie alle zugehörigen Schritte aus:

### Verwenden eines neuen selbstsignierten Zertifikats

1.     [Erstellen eines selbstsignierten Zertifikats][]
2.     [Erstellen einer PFX-Datei für ein selbstsigniertes Verschlüsselungszertifikat][]
3.     [Hochladen eines Verschlüsselungszertifikats zum Cloud-Dienst][]
4.     [Aktualisieren des Verschlüsselungszertifikats in der Dienstkonfigurationsdatei][]

### Verwenden eines vorhandenen Zertifikats aus dem Zertifikatspeicher

1.     [Exportieren des Verschlüsselungszertifikats aus dem Zertifikatspeicher][]
2.     [Hochladen eines Verschlüsselungszertifikats zum Cloud-Dienst][]
3.     [Aktualisieren des Verschlüsselungszertifikats in der Dienstkonfigurationsdatei][]

### Verwenden eines vorhandenen Zertifikats in einer PFX-Datei

1.     [Hochladen eines Verschlüsselungszertifikats zum Cloud-Dienst][]
2.     [Aktualisieren des Verschlüsselungszertifikats in der Dienstkonfigurationsdatei][]

## Standardkonfiguration

Die Standardkonfiguration verweigert den gesamten Zugriff auf den HTTP-Endpunkt. Dies ist die empfohlene Einstellung, da die Anforderungen an diese Endpunkte vertrauliche Informationen wie Datenbankanmeldeinformationen enthalten können.
In der Standardkonfiguration ist der gesamte Zugriff auf den HTTP-Endpunkt zugelassen. Diese Einstellung kann weiter eingeschränkt werden.

### Ändern der Konfiguration

Die Gruppe von Zugriffssteuerungsregeln, die auf den Endpunkt angewendet werden, werden im Abschnitt **<EndpointAcls>** der **Dienstkonfigurationsdatei** konfiguriert.

    <EndpointAcls>
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
    </EndpointAcls>

Die Regeln einer Zugriffssteuerungsgruppe werden im Abschnitt <AccessControl-Name = ""> der Dienstkonfigurationsdatei konfiguriert. 

Das Format wird in der Dokumentation über Netzwerk-Zugriffsteuerungslisten erläutert.
Damit nur IP-Adressen im Bereich von 100.100.0.0 bis 100.100.255.255 auf den HTTPS-Endpunkt zugreifen können, würden die Regeln z. B. wie folgt aussehen:

    <AccessControl name="Retricted">
      <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
      <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
    </AccessControl>
    <EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />

## <a name = "denial-of-service-prevention"></a>Dienstverweigerungsschutz

Es werden zwei unterschiedliche Mechanismen zum Erkennen und Verhindern von Dienstverweigerungsangriffen unterstützt:

*    Beschränken der Anzahl gleichzeitiger Anforderungen pro Remotehost (standardmäßig deaktiviert)
*    Beschränken der Zugriffsrate pro Remotehost (standardmäßig aktiviert)

Sie basieren auf den Funktionen, die unter der dynamischen IP-Sicherheit in IIS eingehender dokumentiert sind. Wenn Sie diese Konfiguration ändern, beachten Sie die folgenden Faktoren:

* Das Verhalten der Proxys und Netzwerkadressübersetzungs-Geräte über die Remotehostinformationen
* Jede Anforderung an eine Ressource in der Web-Rolle wird berücksichtigt(z. B. das Laden von Skripts, Images usw.).

## Einschränken der Anzahl gleichzeitiger Zugriffe

Dieses Verhalten wird über folgende Einstellungen konfiguriert:

    <Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
    <Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />

Ändern Sie DynamicIpRestrictionDenyByConcurrentRequests in "true", um diesen Schutz zu aktivieren.

## Beschränken der Zugriffsrate

Dieses Verhalten wird über folgende Einstellungen konfiguriert:

    <Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
    <Setting name="DynamicIpRestrictionMaxRequests" value="100" />
    <Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />

## Konfigurieren der Antwort auf eine verweigerte Anforderung

Die folgende Einstellung konfiguriert die Antwort auf eine verweigerte Anforderung:

    <Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
Weitere unterstützte Werte entnehmen Sie der Dokumentation für dynamische IP-Sicherheit in IIS.

## Vorgänge für das Konfigurieren von Dienstzertifikaten
Dieses Thema dient nur zu Informationszwecken. Führen Sie die Konfigurationsschritte der folgenden Themen aus:

* Konfigurieren des SSL-Zertifikats
* Konfigurieren von Clientzertifikaten

## <a name = "create-self-signed-cert"></a>Erstellen eines selbstsignierten Zertifikats
Führen Sie folgende Befehle aus:

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha1 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

Zur Anpassung:

*    -n mit der Dienst-URL. Platzhalter ("CN=*.cloudapp.net") und alternative Namen ("CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net") werden unterstützt.
*    -e mit dem Ablaufdatum des Zertifikats
Erstellen Sie ein sicheres Kennwort, und geben Sie es bei Aufforderung an.

## <a name="create-pfx-for-self-signed-cert"></a>Erstellen einer PFX-Datei für ein selbstsigniertes SSL-Zertifikat

Führen Sie folgende Befehle aus:

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

Geben Sie das Kennwort ein, und exportieren Sie dann das Zertifikat mit den folgenden Optionen:
* Ja, privaten Schlüssel exportieren
* Alle erweiterten Eigenschaften exportieren

## <a name="export-ssl-from-store"></a>Exportieren des SSL-Zertifikats aus dem Zertifikatspeicher

* Suchen des Zertifikats
* Klicken Sie auf "Aktionen -> Alle Aufgaben -> Exportieren..."
* Exportieren Sie das Zertifikats mit den folgenden Optionen in eine.PFX-Datei:
    * Ja, privaten Schlüssel exportieren
    * Wenn möglich, alle Zertifikate im Zertifizierungspfad einbeziehen
    *Alle erweiterten Eigenschaften exportieren

## <a name="upload-ssl"></a>Hochladen des SSL-Zertifikats in den Cloud-Dienst

Laden Sie das Zertifikat mit der vorhandenen oder generierten .PFX-Datei mit dem SSL-Schlüsselpaar hoch:

* Geben Sie das Kennwort zum Schützen der Informationen des private Schlüssels ein.

## <a name="update-ssl-in-csfg"></a>Aktualisieren des SSL-Zertifikats in der Dienstkonfigurationsdatei

Aktualisieren Sie den Fingerabdruckwert der folgenden Einstellung in der Dienstkonfigurationsdatei mit dem Fingerabdruck des in den Cloud-Dienst hochgeladenen Zertifikats:

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="import-ssl-ca"></a>Importieren der SSL-Zertifizierungsstelle

Führen Sie die folgenden Schritten für alle Konto/Computer aus, die mit dem Dienst kommunizieren werden:

* Doppelklicken Sie auf die .CER-Datei in Windows-Explorer.
* Klicken Sie im Dialogfeld "Zertifikat" auf "Zertifikat installieren...".
* Importieren Sie das Zertifikat in den Speicher für vertrauenswürdige Stammzertifizierungsstellen.

## <a name="turn-off-client-cert"></a>Deaktivieren der zertifikatbasierten Clientauthentifizierung

Nur zertifikatbasierte Clientauthentifizierung wird unterstützt, und deren Deaktivierung lässt den öffentlichen Zugriff auf die Dienstendpunkte zu, sofern nicht andere Mechanismen eingerichtet wurden (z. B. Microsoft Azure Virtual Network).

Ändern Sie diese Einstellungen in der Dienstkonfigurationsdatei zu "false", um die Funktion zu deaktivieren:

    <Setting name="SetupWebAppForClientCertificates" value="false" />
    <Setting name="SetupWebserverForClientCertificates" value="false" />

Kopieren Sie dann denselben Fingerabdruck wie für das SSL-Zertifikat in die Einstellung des CA-Zertifikats:

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="create-self-signed-ca"></a>Erstellen einer selbstsignierten Zertifizierungsstelle
Führen Sie die folgenden Schritte aus, um ein selbstsigniertes Zertifikat zu erstellen, das als Zertifizierungsstelle fungiert:

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha1 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

Zur Anpassung:

*    -e mit dem Ablaufdatum des Zertifikats


## <a name="find-ca-public-key"></a>Suchen des öffentlichen Schlüssels der Zertifizierungsstelle

Alle Clientzertifikate müssen von einer Zertifizierungsstelle ausgestellt werden, der der Dienst vertraut. Suchen Sie den öffentlichen Schlüssel der Zertifizierungsstelle, die die Clientzertifikate für die Authentifizierung ausgestellt hat, um sie in den Cloud-Dienst hochzuladen.

Wenn die Datei mit dem öffentlichen Schlüssel nicht verfügbar ist, exportieren Sie sie aus dem Zertifikatspeicher:

* Suchen des Zertifikats
    * Suchen Sie ein Clientzertifikat, das von derselben Zertifizierungsstelle ausgestellt wurde.
* Doppelklicken Sie auf das Zertifikat.
* Wählen Sie die Registerkarte "Zertifizierungspfad" im Dialogfeld "Zertifikat" aus.
* Doppelklicken Sie auf den CA-Eintrag im Pfad. 
* Notieren Sie die Zertifikateigenschaften.
* Schließen Sie das Dialogfeld "Zertifikat".
* Suchen des Zertifikats
    * Suchen Sie nach der oben aufgeführten Zertifizierungsstelle.
* Klicken Sie auf "Aktionen -> Alle Aufgaben -> Exportieren..."
* Exportieren Sie das Zertifikat mit den folgenden Optionen in eine .CER-Datei:
    * Nein, privaten Schlüssel nicht exportieren
    * Wenn möglich, alle Zertifikate im Zertifizierungspfad einbeziehen
    * Alle erweiterten Eigenschaften exportieren

## <a name="upload-ca-cert"></a>Hochladen eines CA-Zertifikats in den Cloud-Dienst

Laden Sie das Zertifikat mit der vorhandenen oder generierten .CER-Datei mit dem öffentlichen Schlüssel der Zertifizierungsstelle hoch.

## <a name="update-ca-in-csft"></a>Aktualisieren des CA-Zertifikats in der Dienstkonfigurationsdatei

Aktualisieren Sie den Fingerabdruckwert der folgenden Einstellung in der Dienstkonfigurationsdatei mit dem Fingerabdruck des in den Cloud-Dienst hochgeladenen Zertifikats:

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

Aktualisieren Sie den Wert der folgenden Einstellung mit dem gleichen Fingerabdruck:

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />

## <a name="issue-client-certs"></a>Ausstellen von Clientzertifikaten

Für jede Person, die für den Zugriff auf den Dienst autorisiert ist, sollte ein Clientzertifikat für ihre exklusive Nutzung ausgestellt werden, und sie sollte ein eigenes sicheres Kennwort zum Schutz des privaten Schlüssels einrichten. 

Die folgenden Schritte müssen auf demselben Computer ausgeführt werden, auf dem das selbstsignierte Zertifikat der Zertifizierungsstelle generiert und gespeichert wurde:

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha1 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

Anpassen:

* -n, mit einer ID für den Client, der mit diesem Zertifikat authentifiziert wird
* -e mit dem Ablaufdatum des Zertifikats
* MyID.pvk und MyID.cer mit eindeutigen Dateinamen für dieses Clientzertifikat

Dieser Befehl fordert dazu auf, ein Kennwort zu erstellen und anschließend einmal zu verwenden. Verwenden Sie ein sicheres Kennwort.

## <a name="create-pfx-files"></a>Erstellen von PFX-Dateien für Clientzertifikate

Führen Sie für jedes generiertes Clientzertifikat folgende Befehle aus:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Anpassen:

    "MyID.pvk" und "MyID.cer" mit dem Dateinamen für das Clientzertifikat

Geben Sie das Kennwort ein, und exportieren Sie dann das Zertifikat mit den folgenden Optionen:

* Ja, privaten Schlüssel exportieren
* Alle erweiterten Eigenschaften exportieren
* Die Person, für die dieses Zertifikat ausgestellt wird, muss das Exportkennwort auswählen.

## <a name="import-client-cert"></a>Importieren des Clientzertifikats

Jede Person, für die ein Clientzertifikat ausgestellt wurde, sollte das Schlüsselpaar auf den Computer importieren, den sie zur Kommunikation mit dem Dienst verwenden möchte:

* Doppelklicken Sie auf die.PFX-Datei in Windows-Explorer.
* Importieren Sie das Zertifikat mit mindestens dieser Option in den persönlichen Zertifikatspeicher:
    * "Alle erweiterten Eigenschaften mit einbeziehen" aktiviert

## <a name="copy-client-cert"> </a> Kopieren der Clientzertifikatfingerabdrücke
Jede Person, für die ein Clientzertifikat ausgegeben wurde, muss folgende Schritte zum Abrufen des Fingerabdrucks ihres Zertifikats ausführen, das der Dienstkonfigurationsdatei hinzugefügt wird:
* Führen Sie "certmgr.exe" aus.
* Wählen Sie die Registerkarte "Persönlich".
* Doppelklicken Sie auf das Clientzertifikat, das für die Authentifizierung verwendet werden soll.
* Wählen Sie im daraufhin geöffneten Dialogfeld "Zertifikat" die Registerkarte "Details".
* Stellen Sie sicher, dass unter "Anzeigen" die Option "Alle" angezeigt wird.
* Wählen Sie das Feld mit dem Namen "Fingerabdruck" in der Liste aus.
* Kopieren Sie den Wert des Fingerabdrucks.
** Löschen Sie nicht sichtbare Unicode-Zeichen vor der ersten Ziffer.
** Löschen Sie alle Leerzeichen.

## <a name="configure-allowed-client"></a>Konfigurieren der zulässigen Clients in der Dienstkonfigurationsdatei

Aktualisieren Sie den Wert für die folgende Einstellung in der Dienstkonfigurationsdatei mit einer kommagetrennten Liste von Fingerabdrücken der Clientzertifikate, die Zugriff auf den Dienst haben:

    <Setting name="AllowedClientCertificateThumbprints" value="" />

## <a name="configure-client-revocation"></a>Konfigurieren der Sperrprüfung für Clientzertifikate

In der Standardeinstellung wird der Sperrstatus des Clientzertifikats bei der Zertifizierungsstelle nicht überprüft. Um die Überprüfungen zu aktivieren, falls die Zertifizierungsstelle, welche die Clientzertifikate ausgestellt hat, derartige Überprüfungen unterstützt, ändern Sie die folgende Einstellung mit einem der Werte, die in der X509RevocationMode-Enumeration definiert sind:

    <Setting name="ClientCertificateRevocationCheck" value="NoCheck" />

## <a name="create-pfx-files-encryption"></a>Erstellen einer PFX-Datei für selbstsignierte Verschlüsselungszertifikate

Führen Sie für ein Verschlüsselungszertifikat Folgendes aus:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Anpassen:

    "MyID.pvk" und "MyID.cer" mit dem Dateinamen für das Verschlüsselungszertifikat

Geben Sie das Kennwort ein, und exportieren Sie dann das Zertifikat mit den folgenden Optionen:
*    Ja, privaten Schlüssel exportieren
*    Alle erweiterten Eigenschaften exportieren
*    Sie benötigen das Kennwort zum Hochladen des Zertifikats in den Cloud-Dienst.

## <a name="export-encryption-from-store"></a>Exportieren des Verschlüsselungszertifikats aus dem Zertifikatspeicher

*    Suchen des Zertifikats
*    Klicken Sie auf "Aktionen -> Alle Aufgaben -> Exportieren..."
*    Exportieren Sie das Zertifikats mit den folgenden Optionen in eine.PFX-Datei: 
  *    Ja, privaten Schlüssel exportieren
  *    Wenn möglich, alle Zertifikate im Zertifizierungspfad einbeziehen 
*    Alle erweiterten Eigenschaften exportieren

## <a name="upload-encryption-cert"></a>Hochladen eines Verschlüsselungszertifikats in den Cloud-Dienst

Laden Sie das Zertifikat mit der vorhandenen oder generierten .PFX-Datei mit dem Verschlüsselungsschlüsselpaar hoch:

* Geben Sie das Kennwort zum Schützen der Informationen des private Schlüssels ein.

## <a name="update-encryption-in-csft"></a>Aktualisieren des Verschlüsselungszertifikats in der Dienstkonfigurationsdatei

Aktualisieren Sie den Fingerabdruckwert der folgenden Einstellungen in der Dienstkonfigurationsdatei mit dem Fingerabdruck des in den Cloud-Dienst hochgeladenen Zertifikats:

    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />

## Allgemeine Zertifikatvorgänge

* Konfigurieren des SSL-Zertifikats
* Konfigurieren von Clientzertifikaten

## Suchen des Zertifikats

Folgen Sie diesen Schritten:

1. Führen Sie "mmc.exe" aus.
2. Datei -> Snap-In hinzufügen/entfernen...
3. Wählen Sie "Zertifikate" aus.
4. Klicken Sie auf "Hinzufügen".
5. Wählen Sie den Speicherort des Zertifikats.
6. Klicken Sie auf "Fertig stellen".
7. OK klicken
8. Erweitern Sie "Zertifikate".
9. Erweitern Sie den Zertifikatspeicher.
10. Erweitern Sie den untergeordneten Knoten "Zertifikat".
11. Wählen Sie ein Zertifikat in der Liste auf der rechten Seite aus.

## Exportieren des Zertifikats
Gehen Sie im Zertifikatsexport-Assistenten wie folgt vor:

1. Klicken Sie auf "Weiter".
2. Wählen Sie "Ja, privaten Schlüssel exportieren".
3. Klicken Sie auf "Weiter".
4. Wählen Sie das gewünschte Ausgabeformat für die Datei.
5. Aktivieren Sie die gewünschten Optionen.
6. Aktivieren Sie "Kennwort".
7. Geben Sie ein sicheres Kennwort ein, und bestätigen Sie es.
8. Klicken Sie auf "Weiter".
9. Geben einen Dateinamen ein oder suchen Sie danach, um das Zertifikat dort zu speichern (verwenden Sie die Dateierweiterung .PFX).
10. Klicken Sie auf "Weiter".
11. Klicken Sie auf "Fertig stellen".
12. OK klicken 

## Importieren des Zertifikats

Gehen Sie im Zertifikatimport-Assistenten wie folgt vor:

1. Wählen Sie den Speicherort aus.
    1.     "Aktueller Benutzer", wenn nur Prozesse, die unter dem aktuellen Benutzer ausgeführt werden, auf den Dienst zugreifen.
    2.     "Lokaler Computer", wenn andere Prozesse auf diesem Computer auf den Dienst zugreifen.
2. Klicken Sie auf "Weiter".
3. Wenn Sie Daten aus einer Datei importieren, bestätigen Sie den Dateipfad.
4. Beim Importieren einer.PFX-Datei:
    1.     Geben Sie das Kennwort ein, das den privaten Schlüssel schützt.
    2.     Wählen Sie die Importoptionen aus.
5.     Wählen Sie "Zertifikate im folgenden Speicher ablegen" aus.
6.     Klicken Sie auf "Durchsuchen".
7.     Wählen Sie den gewünschten Speicher.
8.     Klicken Sie auf "Fertig stellen".
    1.     Wenn der Speicher vertrauenswürdiger Stammzertifizierungsstellen ausgewählt wurde, klicken Sie auf "Ja".
9.     Klicken Sie in allen Dialogfeldern auf "OK".

## <a name="upload-certificate"></a>Hochladen des Zertifikats

Im [Azure-Verwaltungsportal](http://manage.windowsazure.com/):

1. Wählen Sie "Cloud-Dienste".
2. Wählen Sie den Cloud-Dienst.
3. Klicken Sie im oberen Menü auf "Zertifikate".
4. Klicken Sie in der unteren Leiste auf "Hochladen".
5. Wählen Sie die Zertifikatdatei aus.
6. Wenn es sich um eine .PFX-Datei handelt, geben Sie das Kennwort für den privaten Schlüssel ein.
7. Kopieren Sie dann den Fingerabdruck des Zertifikats aus dem neuen Eintrag in der Liste.

# <a name="other-security"></a> Weitere Überlegungen zur Sicherheit
 
Die in diesem Dokument beschriebenen SSL-Einstellungen verschlüsseln die Kommunikation zwischen dem Dienst und seinen Clients, wenn der HTTPS-Endpunkt verwendet wird. Dies ist wichtig, da die Anmeldeinformationen für den Datenbankzugriff und andere vertrauliche Informationen in der Kommunikation enthalten sind. Beachten Sie jedoch, dass der Dienst den internen Status, einschließlich der Anmeldeinformationen, in den internen Tabellen in der Microsoft Azure SQL-Datenbank, die Sie in Ihrem Microsoft Azure-Abonnement für Metadatenspeicher bereitgestellt haben, beibehält. Diese Datenbank wurde im Rahmen der folgenden Einstellung in der Dienstkonfigurationsdatei (.CSCFG-Datei) definiert: 

    <Setting name="ElasticScaleMetadata" value="Server=..." />

In dieser Datenbank gespeicherte Anmeldeinformationen werden verschlüsselt. Stellen Sie als bewährte Methode außerdem sicher, dass die Web- und Worker-Rollen der Dienstbereitstellungen immer aktualisiert und sicher sind, da beide Zugriff auf die Metadaten-Datenbank und das Zertifikat haben, das für die Verschlüsselung und Entschlüsselung von gespeicherten Anmeldeinformationen verwendet wird. 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

[Konfigurieren von Zertifikaten]:#configuring-certificates
[Zulässige IP-Adressen]:#allowed-ip-addresses
[So konfigurieren Sie Clientzertifikate]:#configuring-client-certs
[Erstellen eines selbstsignierten Zertifikats]:#create-self-signed-cert
[Erstellen einer PFX-Datei für ein selbstsigniertes SSL-Zertifikat]:#create-pfx-for-self-signed-cert
[Hochladen des SSL-Zertifikats zum Cloud-Dienst]: #upload-ssl
[Aktualisieren des SSL-Zertifikats in der Dienstkonfigurationsdatei]: #update-ssl-in-csfg
[Importieren der SSL-Zertifizierungsstelle]: "import-ssl-ca"
[Exportieren des SSL-Zertifikats aus dem Zertifikatspeicher]: #export-ssl-from-store
[Deaktivieren der zertifikatbasierten Clientauthentifizierung]: #turn-off-client-cert
[Erstellen einer selbstsignierten Zertifizierungsstelle]:#create-self-signed-ca
[Kopieren der Clientfingerabdrücke]:#copy-client-cert
[Hochladen eines CA-Zertifikats zum Cloud-Dienst]:#upload-ca-cert
[Aktualisieren des CA-Zertifikats in der Dienstkonfigurationsdatei]:#update-ca-in-csft
[Ausstellen von Clientzertifikaten]:#issue-client-certs
[Erstellen von PFX-Dateien für Clientzertifikate]:#create-pfx-files
[Importieren des Clientzertifikats]:#import-client-cert
[Konfigurieren der zulässigen Clients in der Dienstkonfigurationsdatei]:#configure-allowed-client
[Suchen des öffentlichen Schlüssels der Zertifizierungsstelle]:#find-ca-public-key
[Konfigurieren der Sperrprüfung für Clientzertifikate]:#configure-client-revocation
[Dienstverweigerungsschutz]:#denial-of-service-prevention
[So rufen Sie Zertifikate ab]:#obtain-certificates
[Tools zum Erstellen von Zertifikaten]:#tools
[So konfigurieren Sie das SSL-Zertifikat]:#to-configure-ssl-cert
[Weitere Überlegungen zur Sicherheit]:#other-security 
[Hochladen des Zertifikats]:#upload-certificate
[Erstellen einer PFX-Datei für ein selbstsigniertes Verschlüsselungszertifikat]:#create-pfx-files-encryption
[Hochladen eines Verschlüsselungszertifikats zum Cloud-Dienst]:#upload-encryption-cert 
[Aktualisieren des Verschlüsselungszertifikats in der Dienstkonfigurationsdatei]:#update-encryption-in-csft
[Exportieren des Verschlüsselungszertifikats aus dem Zertifikatspeicher]:#export-encryption-from-store

<!--HONumber=49--> 