<properties 
   pageTitle="Bereitstellen von Hadoop-Clustern in HDInsight | Azure" 
   description="Erfahren Sie, wie Sie Cluster für Azure HDInsight mithilfe von Azure-Portal, Azure PowerShell, eine Befehlszeile oder das HDInsight .NET SDK bereitstellen." 
   services="hdinsight" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="04/21/2015"
   ms.author="nitinme"/>

#Benutzerdefiniertes Bereitstellen von Hadoop-Clustern in HDInsight

Lernen Sie die verschiedenen Möglichkeiten für die benutzerdefinierte Bereitstellung des Hadoop-Clusters in Azure HDInsight kennen: Azure-Portal, Azure PowerShell, Befehlszeilentools oder das HDInsight .NET SDK. Anleitungen zur Bereitstellung von HBase-Clustern und Storm-Clustern finden Sie unter [Bereitstellen von HBase-Clustern in HDInsight](../hdinsight-hbase-get-started.md) und [Erste Schritte mit Storm in HDInsight](../hdinsight-storm-getting-started.md). Unter <a href="http://go.microsoft.com/fwlink/?LinkId=510237">What's the difference between Hadoop and HBase?</a> (Worin besteht der Unterschied zwischen Hadoop und HBase?, in englischer Sprache) finden Sie einen Vergleich der beiden Technologien.

## Was ist ein HDInsight-Cluster?

Vielleicht haben Sie sich schon gefragt, warum wir jedes Mal Cluster erwähnen, wenn wir über Hadoop oder Big Data sprechen. Das liegt daran, dass Hadoop die verteilte Verarbeitung großer Datenmengen auf mehreren Knoten eines Clusters ermöglicht. Cluster haben eine Master/Slave-Architektur mit einem Master (oder Stamm- bzw. Namensknoten) und einer beliebigen Anzahl von Slaves (oder Datenknoten). Weitere Informationen finden Sie unter [Apache Hadoop][apache-hadoop].

![HDInsight-Cluster][img-hdi-cluster]

HDInsight-Cluster abstrahieren die Hadoop-Implementierungsdetails, sodass Sie sich keine Gedanken über die Kommunikation zwischen den einzelnen Clusterknoten machen müssen. Beim Bereitstellen eines HDInsight-Clusters werden Azure-Serverressourcen bereitgestellt, die Hadoop und verwandte Anwendungen enthalten. Weitere Informationen finden Sie unter [Einführung in Hadoop in HDInsight](hdinsight-hadoop-introduction.md). Die zu verarbeitenden Daten werden in Azure-Blob-Speicher gespeichert. Weitere Informationen finden Sie unter [Use Azure Blob Storage with HDInsight](../hdinsight-use-blob-storage.md) (Verwenden von Azure Blob-Speicher mit HDInsight, in englischer Sprache).

Dieser Artikel beschreibt die verschiedenen Möglichkeiten bei der Einrichtung eines Clusters. Falls Sie auf der Suche nach einem Schnelleinstieg in die Clusterbereitstellung sind, lesen Sie [Erste Schritte mit Azure HDInsight](../hdinsight-get-started.md).

**Voraussetzungen:**

Bevor Sie mit den Anweisungen in diesem Artikel beginnen können, benötigen Sie Folgendes:

- Ein Azure-Abonnement. Azure ist eine abonnementbasierte Plattform. Mit den Windows PowerShell-Cmdlets für HDInsight werden die Aufgaben anhand Ihres Abonnements ausgeführt. Weitere Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen][azure-purchase-options], [Spezielle Angebote][azure-member-offers] oder [Kostenlose Testversion][azure-free-trial].

##<a id="configuration"></a>Konfigurationsoptionen

###Cluster unter Linux

HDInsight bietet die Möglichkeit, Linux-Cluster unter Azure zu konfigurieren. Konfigurieren Sie einen Linux-Cluster, wenn Sie mit Linux oder Unix und der Migration von einer vorhandenen Linux-basierten Hadoop-Lösung vertraut sind oder eine einfache Integration mit Komponenten des Hadoop-Systems wünschen, die für Linux konzipiert sind. Weitere Informationen finden Sie unter [Erste Schritte mit Hadoop unter Linux in HDInsight](../hdinsight-hadoop-linux-get-started.md).

###Zusätzlicher Speicher

Bei der Konfiguration müssen Sie ein Azure-Blob-Speicherkonto und einen Standardcontainer angeben. Das Cluster verwendet diese Option als Standard-Speicherort. Sie können optional ein zusätzliches Azure-Speicherkonto angeben, das ebenfalls Ihrem Cluster zugeordnet wird.

>[AZURE.NOTE]Verwenden Sie nicht einen Blob-Speichercontainer für mehrere Cluster. Dies wird nicht unterstützt.

Weitere Informationen zu sekundären Blob-Speichern finden Sie unter [Verwenden von Azure-Blob-Speicher mit HDInsight](../hdinsight-use-blob-storage.md).

###Metastore

Metastore enthält Hive- und Oozie-Metadaten, wie z. B. Hive-Tabellen, Partitionen, Schemas und Spalten. Mit Metastore können Sie Ihre Hive- und Oozie-Metadaten speichern, damit Sie nicht Hive-Tabellen oder Oozie-Aufträge neu erstellen müssen, wenn Sie einen neuen Cluster bereitstellen. Standardmäßig speichert Hive diese Informationen in einer eingebetteten Datenbank. Die eingebettete Datenbank kann die Metadaten beim Löschen des Clusters nicht speichern.

### Clusteranpassung

Sie können während der Bereitstellung mithilfe von Skripts weitere Komponenten installieren oder die Konfiguration des Clusters anpassen. Diese Skripts werden mithilfe der Konfigurationsoption **Skriptaktion** aufgerufen, die vom Azure-Portal, von HDInsight PowerShell-Cmdlets oder dem HDInsight .NET SDK verwendet werden kann. Weitere Informationen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster.md).


###Virtuelle Netzwerke

[Azure Virtual Network](http://azure.microsoft.com/documentation/services/virtual-network/) ermöglicht das Erstellen eines sicheren, dauerhaften Netzwerks mit allen Ressourcen, die Sie für Ihre Lösung benötigen. Virtuelle Netzwerken ermöglichen Folgendes:

* Verbinden von Cloudressourcen in einem privaten Netzwerk (nur in der Cloud)

	![Diagramm der Nur-Cloud-Konfiguration](./media/hdinsight-provision-clusters/hdinsight-vnet-cloud-only.png)

* Verbinden Ihrer Cloudressourcen mit dem Netzwerk in Ihrem lokalen Datencenter (Standort-zu-Standort oder Punkt-zu-Standort) mithilfe eines virtuellen privaten Netzwerks (VPN)

	Mit einer Standort-zu-Standort-Konfiguration können Sie mehrere Ressourcen aus Ihrem Datencenter mit dem virtuellen Azure-Netzwerk über ein Hardware-VPN oder den Routing- und RAS-Dienst verbinden

	![Diagramm der Standort-zu-Standort-Konfiguration](./media/hdinsight-provision-clusters/hdinsight-vnet-site-to-site.png)

	Mit einer Punkt-zu-Standort-Konfiguration können Sie eine bestimmte Ressource über ein Software-VPN mit dem virtuellen Azure-Netzwerk verbinden.

	![Diagramm der Punkt-zu-Standort-Konfiguration](./media/hdinsight-provision-clusters/hdinsight-vnet-point-to-site.png)

Weitere Informationen zu Features, Vorteilen und Funktionen von virtuellen Netzwerken finden Sie unter [Überblick über virtuelle Azure-Netzwerke](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [AZURE.NOTE]Sie müssen das virtuelle Azure-Netzwerk erstellen, bevor Sie einen HDInsight-Cluster bereitstellen. Weitere Informationen finden Sie unter [Konfigurationsaufgaben für virtuelle Netzwerke](http://msdn.microsoft.com/library/azure/jj156206.aspx).
>
> Azure HDInsight unterstützt nur standortbasierte virtuelle Netzwerke und kann momentan nicht mit affinitätsgruppenbasierten virtuellen Netzwerken verwendet werden.
>
> Sie sollten unbedingt ein einziges Subnetz pro Cluster verwenden.

##<a id="portal"></a> Verwenden des Azure-Portals

HDInsight-Cluster verwenden als Standarddateisystem einen Azure-Blob-Speichercontainer. Sie benötigen ein Azure-Speicherkonto im entsprechenden Datencenter, um einen HDInsight-Cluster erstellen zu können. Weitere Informationen finden Sie unter [Use Azure Blob Storage with HDInsight](../hdinsight-use-blob-storage.md) (Verwenden von Azure Blob-Speicher mit HDInsight, in englischer Sprache). Weitere Informationen zum Erstellen von Azure-Speicherkonten finden Sie unter [Erstellen eines Speicherkontos](../storage-create-storage-account.md).


> [AZURE.NOTE]HDInsight-Cluster sind momentan nur in den Regionen **Ostasien**, **Südostasien**, **Nordeuropa**, **Westeuropa**, **Osten USA**, **Westen USA**, **USA Nord Mitte** und **USA Süd Mitte** verfügbar.

**So erstellen Sie einen HDInsight-Cluster mit der benutzerdefinierten Erstellungsoption**

1. Melden Sie sich beim [Azure-Portal][azure-management-portal] an.
2. Klicken Sie im unteren Seitenbereich auf **+ NEW**, anschließend auf **DATA SERVICES**, auf **HDINSIGHT**und zuletzt auf **CUSTOM CREATE**.
3. Geben Sie auf der Seite **Clusterdetails** die folgenden Daten ein:

	![Bereitstellen von Hadoop HDInsight-Clustern – Details][image-customprovision-page1]

    <table border='1'>
	<tr><th>Eigenschaft</th><th>Wert</th></tr>
	<tr><td>Clustername</td>
		<td><p>Der Name des Clusters. </p>
			<ul>
			<li>Der DNS-Name (Domain Name System) muss mit einem alphanumerischen Zeichen beginnen und enden und darf Bindestriche enthalten.</li>
			<li>Das Feld muss eine Zeichenfolge mit 3 bis 63&#160;Zeichen enthalten.</li>
			</ul></td></tr>
	<tr><td>Clustertyp</td>
		<td>Wählen Sie unter "Clustertyp" den Eintrag <strong>Hadoop</strong> aus.</td></tr>
	<tr><td>Betriebssystem</td>
		<td>Wählen Sie <b>Windows Server 2012 R2 Data Center</b> aus, um einen Windows-Cluster bereitzustellen. Informationen zum Bereitstellen eines Linux-Clusters finden Sie unter <a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-provision-linux-clusters/" target="_blank">Benutzerdefinierte Bereitstellung eines Hadoop-Linux-Clusters in HDInsight</a>.</td></tr>
	<tr><td>HDInsight-Version</td>
		<td>Wählen Sie die Version aus. HDInsight Version 3.1 ist die Standardversion für Hadoop und verwendet Hadoop Version 2.4.</td></tr>
	</table>Geben Sie die Werte wie in der Tabelle gezeigt ein und klicken Sie auf den Pfeil nach rechts.

4. Geben Sie auf der Seite **Cluster konfigurieren** die folgenden Daten ein:

	![Bereitstellen von Hadoop HDInsight-Clustern – Details](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page2.png)

	<table border="1">
<tr><th>Name</th><th>Wert</th></tr>
<tr><td>Datenknoten</td><td>Die Anzahl der Datenknoten, die Sie bereitstellen möchten. Erstellen Sie zu Testzwecken einen Cluster mit nur einem Knoten. <br />Die Größenbegrenzung für die Cluster variiert in Azure-Abonnements. Wenden Sie sich an das Azure-Abrechnungssupportteam, um diese Begrenzung zu erhöhen.</td></tr>
<tr><td>Region/virtuelles Netzwerk</td><td><p>Wählen Sie dieselbe Region aus wie für das Speicherkonto, das Sie im letzten Verfahren erstellt haben. Für HDInsight muss sich das Speicherkonto in derselben Region befinden. Später in dieser Konfiguration können Sie nur ein Speicherkonto wählen, das sich in der hier angegebenen Region befindet.</p><p>Die verfügbaren Regionen sind: <strong>Ostasien</strong>, <strong>Südostasien</strong>, <strong>Nordeuropa</strong>, <strong>Westeuropa</strong>, <strong>USA (Osten)</strong>, <strong>USA (Westen)</strong>, <strong>USA (Mitte/Norden)</strong>, <strong>USA (Mitte/Süden)</strong>.<br/>Wenn Sie ein virtuelles Azure-Netzwerk erstellt haben, können Sie das Netzwerk auswählen, für dessen Nutzung der HDInsight-Cluster konfiguriert wird.</p><p>Weitere Informationen zur Erstellung von virtuellen Azure-Netzwerken finden Sie unter <a href="http://msdn.microsoft.com/library/azure/jj156206.aspx">Konfigurationsaufgaben für virtuelle Netzwerke</a>.</p></td></tr>
<tr><td>Größe des Hauptknotens</td><td><p>Wählen Sie eine VM-Größe für den Hauptknoten aus.</p></td></tr>
<tr><td>Datenknotengröße</td><td><p>Wählen Sie eine VM-Größe für die Datenknoten aus.</p></td></tr>
</table>
>[AZURE.NOTE]Je nach Wahl der VMs können Ihre Kosten variieren. HDInsight verwendet für Clusterknoten VMs mit Standardtarif. Informationen über die Auswirkungen der VM-Größe auf Ihre Kosten finden Sie unter <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">HDInsight-Preise</a>.


5. Geben Sie auf der Seite **Clusterbenutzer konfigurieren** die folgenden Daten ein:

    ![Bereitstellen von Hadoop HDInsight-Clustern – Details zu Benutzern und Metastore](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png)

    <table border='1'>
	<tr><th>Eigenschaft</th><th>Wert</th></tr>
	<tr><td>HTTP User Name</td>
		<td>Geben Sie den Benutzernamen für das HDInsight-Cluster an.</td></tr>
	<tr><td>HTTP Password/Confirm Password</td>
		<td>Geben Sie das Passwort für das HDInsight-Cluster an.</td></tr>
	<tr><td>Enable remote desktop for cluster</td>
		<td>Aktivieren Sie dieses Kontrollkästchen, um den Benutzernamen, das Kennwort und ein Ablaufdatum für einen Remotedesktopbenutzer einzugeben, der nach der Bereitstellung per Remotezugriff auf die bereitgestellten Clusterknoten zugreifen kann. Sie können Remotedesktop auch noch nach der Bereitstellung des Clusters aktivieren. Anweisungen hierzu finden Sie unter <a href="hdinsight-administer-use-management-portal/#rdp" target="_blank">Herstellen einer Verbindung mit HDInsight-Clustern mit RDP</a>.</td></tr>
	<tr><td>Hive/Oozie Metastore öffnen</td>
		<td>Aktivieren Sie dieses Kontrollkästchen, um eine SQL-Datenbank im gleichen Datencenter wie der Cluster für die Verwendung als Hive-/Oozie-Metastore anzugeben. Wenn Sie dieses Kontrollkästchen aktivieren, müssen Sie auf den nachfolgenden Seiten des Assistenten Details zur Azure SQL-Datenbank angeben. Dies ist praktisch, wenn Sie die Metadaten von Hive-/Oozie-Jobs behalten möchten, nachdem der Cluster gelöscht wurde.</td></tr>
	</td></tr>		
</table>Klicken Sie auf den Pfeil nach rechts.

6. Geben Sie auf der Seite **Hive/Oozie-Metastore konfigurieren** die folgenden Daten ein:

    ![Bereitstellen von Hadoop HDInsight-Clustern – Benutzer](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png)

	Geben Sie eine Azure SQL-Datenbank an, die als Metastore für Hive/Oozie verwendet werden soll. Sie können dieselbe Datenbank für Hive- und Oozie-Metastores angeben. Die SQL-Datenbank muss sich im selben Rechenzentrum wie das HDInsight-Cluster befinden. In diesem Feld werden nur SQL-Datenbanken aufgelistet, die sich im gleichen Datencenter befinden, das Sie auf der Seite <strong>Clusterdetails</strong> angegeben haben. Geben Sie auch den Benutzernamen und das Kennwort zum Herstellen der Verbindung mit der Azure SQL-Datenbank an, die Sie ausgewählt haben.

    >[AZURE.NOTE]Die als Metastore verwendete Azure SQL-Datenbank muss für die Konnektivität mit anderen Azure-Diensten konfiguriert sein, inklusive Azure HDInsight. Klicken Sie im Dashboard der Azure SQL-Datenbank mit der rechten Maustaste auf den Servernamen. Dies ist der Server, auf dem die SQL-Datenbankinstanz läuft. Öffnen Sie die Serveransicht, klicken Sie auf **Konfigurieren**, wählen Sie unter **Azure-Dienste** den Wert **Ja** aus, und klicken Sie auf **Speichern**.

    Klicken Sie auf den Pfeil nach rechts.


7. Geben Sie auf der Seite **Speicherkonto** die folgenden Werte ein:

    ![Bereitstellen von Speicherkonten für Hadoop HDInsight-Cluster](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page5.png)

	<table border='1'>
	<tr><th>Eigenschaft</th><th>Wert</th></tr>
	<tr><td>Speicherkonto</td>
		<td>Geben Sie das Azure-Speicherkonto an, das als Standard-Dateisystem für das HDInsight-Cluster verwendet werden soll. Sie haben drei Möglichkeiten:
		<ul>
			<li><strong>Vorhandenen Speicher verwenden</strong></li>
			<li><strong>Neuen Speicher erstellen</strong></li>
			<li><strong>Speicher aus einem anderen Abonnement verwenden</strong></li>
		</ul>
		</td></tr>
	<tr><td>Kontoname</td>
		<td><ul>
			<li>Falls Sie vorhandenen Speicher verwenden, wählen Sie unter <strong>Kontoname</strong> ein vorhandenes Speicherkonto aus. Die Dropdownliste enthält nur die Speicherkonten in dem Datencenter, in dem Sie auch den Cluster eingerichtet haben.</li>
			<li>Falls Sie <strong>Neuen Speicher erstellen</strong> oder <strong>Speicher aus einem anderem Abonnement verwenden</strong> ausgewählt haben, müssen Sie den Namen des Speicherkontos angeben.</li>
		</ul></td></tr>
	<tr><td>Kontoschlüssel</td>
		<td>Geben Sie den Speicherschlüssel für das entsprechende Konto ein, falls Sie <strong>Speicher aus einem anderem Abonnement verwenden</strong> ausgewählt haben.</td></tr>
	<tr><td>Standardcontainer</td>
		<td><p>Geben Sie den Standardcontainer im Speicherkonto an, der als Standarddateisystem für den HDInsight-Cluster verwendet wird. Wenn Sie <strong>Vorhandenen Speicher verwenden</strong> für das Feld <strong>Speicherkonto</strong> wählen und in dem betreffenden Konto keine Container vorhanden sind, wird der Container standardmäßig mit demselben Namen wie der Cluster erstellt. Falls bereits ein Container mit dem Namen des Clusters existiert, wird eine Sequenznummer an den Containernamen angehängt. Zum Beispiel mycontainer1, mycontainer2 und so weiter. Sie können jedoch auch Container im vorhandenen Speicherkonto verwenden, die einen anderen Namen als der Cluster haben.</p>
        <p>Falls Sie einen neuen Speicher erstellen oder einen Speicher aus einem anderen Azure-Abonnement verwenden, müssen Sie den Namen des Standardcontainers angeben.</p>
    </td></tr>
	<tr><td>Zusätzliche Speicherkonten</td>
		<td>HDInsight unterstützt mehrere Speicherkonten. Es gibt keine Beschränkung in Bezug auf die zusätzlichen Speicherkonten, die von einem Cluster verwendet werden können. Wenn Sie den Cluster jedoch im Azure-Portal erstellen, können Sie aufgrund von Einschränkungen der Benutzeroberfläche maximal sieben Speicherkonten einrichten. Für jedes angegebene Speicherkonto wird dem Assistenten eine zusätzliche Seite **Speicherkonto** hinzugefügt, in der Sie die Kontoinformationen angeben können. Im obigen Bildschirmfoto wurde z.&#160;B. ein zusätzliches Speicherkonto ausgewählt, sodass dem Dialogfeld Seite&#160;5 hinzugefügt wurde.</td></tr>
</table>Klicken Sie auf den Pfeil nach rechts.

7. Wenn Sie für den Cluster zusätzlichen Speicher konfigurieren möchten, geben Sie auf der Seite **Speicherkonto** die Kontoinformationen für das zusätzliche Speicherkonto ein:

	![Angeben zusätzlicher Speicherdetails für HDInsight-Cluster](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page6.png)

    Hier haben Sie erneut die Auswahl zwischen einem vorhandenen Speicher, einem neu erstellten Speicher und einem Speicher aus einem anderen Azure-Abonnement. Die Angabe der Werte erfolgt auf dieselbe Weise wie im vorherigen Schritt.

    > [AZURE.NOTE]Nachdem Sie ein Azure-Speicherkonto für Ihren HDInsight-Cluster ausgewählt haben, können Sie das Konto nicht mehr löschen und auch kein anderes Konto auswählen.

8. Klicken Sie auf der Seite **Script Actions** auf **Add script action**, um Einzelheiten zum benutzerdefinierten Skript anzugeben, das Sie zum Anpassen eines Clusters bei seiner Erstellung ausführen möchten. Weitere Informationen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster.md).
	
	![Konfigurieren von Skriptaktionen zum Anpassen von HDInsight-Clustern](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page7.png)

	<table border='1'>
	<tr><th>Eigenschaft</th><th>Wert</th></tr>
	<tr><td>Name</td>
		<td>Geben Sie einen Namen für die Skriptaktion an.</td></tr>
	<tr><td>Skript-URI</td>
		<td>Geben Sie den Uniform Resource Identifier (URI) für das Skript an, das aufgerufen wird, um den Cluster anzupassen.</td></tr>
	<tr><td>Knotentyp</td>
		<td>Gibt die Knoten an, auf denen das Anpassungsskript ausgeführt wird. Sie können <b>All Nodes</b>, <b>Head nodes only</b> oder <b>Data nodes only</b> auswählen.
	<tr><td>Parameter</td>
		<td>Geben Sie die Parameter an, wenn dies für das Skript erforderlich ist.</td></tr>
</table>Sie können dem Cluster mehr als eine Skriptaktion zum Installieren von mehreren Komponenten hinzufügen. Nachdem Sie die Skripts hinzugefügt haben, klicken Sie auf das Häkchen, um die Bereitstellung des Clusters zu starten.

##<a id="powershell"></a> Verwenden von Azure PowerShell
Azure PowerShell ist eine leistungsstarke Skriptumgebung, mit der Sie die Bereitstellung und Verwaltung Ihrer Arbeitsauslastungen in Azure steuern und automatisieren können. Dieser Abschnitt enthält Anweisungen für die Bereitstellung eines HDInsight-Clusters mit Azure PowerShell. Weitere Informationen zum Konfigurieren einer Arbeitsstation für die Ausführung von Windows Powershell-Cmdlets für HDInsight finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md). Weitere Informationen zum Verwenden von Azure PowerShell mit HDInsight finden Sie unter [Verwalten von HDInsight mit PowerShell](hdinsight-administer-use-powershell.md). Eine Liste der Windows PowerShell-Cmdlets für HDInsight finden Sie unter [Referenzdokumentation zu den HDInsight PowerShell-Cmdlets][hdinsight-powershell-reference].

> [AZURE.NOTE]Die Skripts in diesem Abschnitt dienen zum Konfigurieren eines HDInsight-Clusters in einem virtuellen Azure-Netzwerk, jedoch nicht zum Erstellen des virtuellen Azure-Netzwerks. Weitere Informationen zum Erstellen von virtuellen Azure-Netzwerken finden Sie unter [Konfigurationsaufgaben für virtuelle Netzwerke](http://msdn.microsoft.com/library/azure/jj156206.aspx).

Die folgenden Verfahren müssen für die Bereitstellung eines HDInsight-Clusters mithilfe von Azure PowerShell ausgeführt werden:

- Erstellen eines Azure-Speicherkontos
- Erstellen eines Azure-Blob-Containers
- Erstellen eines HDInsight-Clusters

Sie können die Windows PowerShell-Konsole oder Windows PowerShell Integrated Scripting Environment (ISE) zum Ausführen der Skripts verwenden.
 
HDInsight verwendet Azure-Blob-Speichercontainer als Standarddateisystem. Sie benötigen ein Azure-Speicherkonto und einen Speichercontainer, um einen HDInsight-Cluster erstellen zu können. Das Speicherkonto muss sich im gleichen Datencenter wie der HDInsight-Cluster befinden.

**So stellen Sie eine Verbindung mit Ihrem Azure-Konto her**

		Add-AzureAccount 

Sie werden zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert.

**So erstellen Sie ein Azure-Speicherkonto**

		$storageAccountName = "<StorageAcccountName>"	# Provide a Storage account name
		$location = "<MicrosoftDataCenter>"				# For example, "West US"

		# Create an Azure Storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

Falls Sie bereits ein Speicherkonto haben und den Kontonamen und den Kontoschlüssel nicht kennen, können Sie diese Informationen mit den folgenden Windows PowerShell-Befehlen abrufen:

		# List Storage accounts for the current subscription
		Get-AzureStorageAccount

		# List the keys for a Storage account
		Get-AzureStorageKey "<StorageAccountName>"

**So erstellen Sie einen Azure-Blob-Speichercontainer**

		$storageAccountName = "<StorageAccountName>"	# Provide the Storage account name
		$containerName="<ContainerName>"				# Provide a container name

		# Create a storage context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName
		                                       -StorageAccountKey $storageAccountKey  

		# Create a Blob storage container
		New-AzureStorageContainer -Name $containerName -Context $destContext

Sobald Sie Ihr Speicherkonto und Ihren Blob-Container vorbereitet haben, können Sie einen Cluster erstellen.

**So stellen Sie einen HDInsight-Cluster bereit**

> [AZURE.NOTE]Die Azure PowerShell-Cmdlets sind der einzige empfohlene Weg zum Ändern von Konfigurationsvariablen in einem HDInsight-Cluster. Änderungen an Hadoop-Konfigurationsdateien, während Sie per Remotedesktop mit dem Cluster verbunden sind, können bei einem Patchvorgang des Clusters überschrieben werden. Die mit Azure PowerShell festgelegten Konfigurationswerte bleiben erhalten, wenn der Cluster gepatcht wird.

- Führen Sie die folgenden Befehle in einem Azure PowerShell-Konsolenfenster aus:

		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created

		$storageAccountName = "<AzureStorageAccountName>" # HDInsight cluster requires an existing Azure Storage account to be used as the default file system

		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster
        $hadoopUserName = "<HadoopUserName>"              # User name for the Hadoop user. You will use this account to connect to the cluster and run jobs.
        $hadoopUserPassword = "<HadoopUserPassword>"    

        $secPassword = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
        $credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$secPassword)            

		# Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$containerName = $clusterName				# Azure Blob container that is used as the default file system for the HDInsight cluster

        # The location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $location = Get-AzureStorageAccount -StorageAccountName $storageAccountName | %{$_.Location} 

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Credential $credential -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop

	>[AZURE.NOTE]Mit den Befehlen "$hadoopUserName" und "$hadoopUserPassword" wird das Hadoop-Benutzerkonto für den Cluster erstellt. Sie verwenden dieses Konto für die Verbindung mit dem Cluster und das Ausführen von Aufträgen. Wenn Sie im Azure-Portal die Option "Schnellerfassung" verwenden, um einen Cluster bereitzustellen, ist der Hadoop-Standardbenutzername "admin". Verwechseln Sie dieses Konto nicht mit dem RDP-Benutzerkonto (Remote Desktop Protocol). Das RDP-Benutzerkonto muss sich vom Hadoop-Benutzerkonto unterscheiden. Weitere Informationen finden Sie unter [Verwalten von Hadoop-Clustern in HDInsight mit dem Azure-Verwaltungsportal][hdinsight-admin-portal]

	Die Bereitstellung des Clusters kann einige Minuten dauern.

	![HDI.CLI.Provision][image-hdi-ps-provision]



**So stellen Sie einen HDInsight-Cluster mit benutzerdefinierten Konfigurationsoptionen bereit**

Bei der Bereitstellung eines Clusters können Sie weitere Konfigurationsoptionen verwenden, z. B. die Verbindung mit mehr als einem Azure-Blob-Speichercontainer oder die Verwendung eines virtuellen Netzwerks oder einer Azure SQL-Datenbank für Hive- und Oozie-Metastores. Auf diese Weise können Sie die Lebensdauer Ihrer Daten und Metadaten von der Lebensdauer des Clusters trennen.

> [AZURE.NOTE]Die Windows PowerShell-Cmdlets sind der einzige empfohlene Weg zum Ändern von Konfigurationsvariablen in einem HDInsight-Cluster. Änderungen an Hadoop-Konfigurationsdateien, während Sie per Remotedesktop mit dem Cluster verbunden sind, können bei einem Patchvorgang des Clusters überschrieben werden. Die mit Azure PowerShell festgelegten Konfigurationswerte bleiben erhalten, wenn der Cluster gepatcht wird.

- Führen Sie die folgenden Befehle in einem Windows PowerShell-Fenster aus:

		$subscriptionName = "<SubscriptionName>"
		$clusterName = "<ClusterName>"
		$location = "<MicrosoftDataCenter>"
		$clusterNodes = <ClusterSizeInNodes>

		$storageAccountName_Default = "<DefaultFileSystemStorageAccountName>"
		$containerName_Default = "<DefaultFileSystemContainerName>"

		$storageAccountName_Add1 = "<AdditionalStorageAccountName>"

		$hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
		$hiveSQLDatabaseName = "<SQLDatabaseDatabaseNameForHiveMetastore>"
		$oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>"
		$oozieSQLDatabaseName = "<SQLDatabaseDatabaseNameForOozieMetastore>"

		# Get the virtual network ID and subnet name
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"

		# Get the Storage account keys
		Select-AzureSubscription $subscriptionName
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }

		$oozieCreds = Get-Credential -Message "Oozie metastore"
		$hiveCreds = Get-Credential -Message "Hive metastore"

		# Create a Blob storage container
		$dest1Context = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default  
		New-AzureStorageContainer -Name $containerName_Default -Context $dest1Context

		# Create a new HDInsight cluster
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
		    Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
		    Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 |
		    Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore |
		    Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore |
		        New-AzureHDInsightCluster -Name $clusterName -Location $location -VirtualNetworkId $vnetID -SubnetName $subNetName

	>[AZURE.NOTE]Die als Metastore verwendete Azure SQL-Datenbank muss für die Konnektivität mit anderen Azure-Diensten konfiguriert sein, inklusive Azure HDInsight. Klicken Sie im Dashboard der Azure SQL-Datenbank mit der rechten Maustaste auf den Servernamen. Dies ist der Server, auf dem die SQL-Datenbankinstanz läuft. Öffnen Sie die Serveransicht, klicken Sie auf **Konfigurieren**, wählen Sie unter **Azure-Dienste** den Wert **Ja** aus, und klicken Sie auf **Speichern**.

**So listen Sie HDInsight-Cluster auf**

- Führen Sie die folgenden Befehle in einer Azure PowerShell-Konsole aus, um den HDInsight-Cluster aufzulisten und zu überprüfen, ob der Cluster erfolgreich eingerichtet wurde:

		Get-AzureHDInsightCluster -Name <ClusterName>


##<a id="cli"></a> Verwenden einer plattformübergreifenden Befehlszeile

> [AZURE.NOTE]Mit Stand vom 29.08.2014 kann die plattformübergreifende Befehlszeilenschnittstelle (CLI) nicht verwendet werden, um einen Cluster einem virtuellen Azure-Netzwerk zuzuordnen.

Eine weitere Möglichkeit zur Bereitstellung von HDInsight-Clustern ist die plattformübergreifende Befehlszeile. Das Befehlszeilentool ist in Node.js implementiert. Das Tool kann auf allen Plattformen verwendet werden, die Node.js unterstützen, einschließlich Windows, Mac und Linux. Sie können die Befehlszeilenschnittstelle an den folgenden Speicherorten installieren:

- **Node.js SDK** – <a href="https://www.npmjs.com/package/azure-mgmt-hdinsight" target="_blank">https://www.npmjs.com/package/azure-mgmt-hdinsight</a>
- **Plattformübergreifende Befehlszeilenschnittstelle** – <a href="https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz" target="_blank">https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz</a>  

Eine allgemeine Anleitung für die Befehlszeilenschnittstelle finden Sie unter [Azure-CLI für Mac, Linux und Windows](../xplat-cli.md).

Im Folgenden finden Sie Anweisungen zum Installieren der plattformübergreifenden Befehlszeile unter Linux und Windows und zum Verwenden der Befehlszeile zum Bereitstellen eines Clusters.

- [Einrichten der Azure-Befehlszeilenschnittstelle für Linux](#clilin)
- [Einrichten der Azure-Befehlszeilenschnittstelle für Windows](#cliwin)
- [Bereitstellen von HDInsight-Clustern mithilfe der Azure-Befehlszeilenschnittstelle](#cliprovision)

#### <a id="clilin"></a>Einrichten der Azure-Befehlszeilenschnittstelle für Linux

Führen Sie die folgenden Verfahren zum Einrichten des Linux-Computers für das Verwenden von Azure-Befehlszeilentools aus:

- Installieren der plattformübergreifenden Befehlszeilenschnittstelle mithilfe von Node.js Package Manager (NPM)
- Verbinden mit Ihrem Azure-Abonnement

**So installieren Sie die Befehlszeilenschnittstelle mit dem NPM**

1.	Öffnen Sie auf dem Linux-Computer ein Terminalfenster, und führen Sie den folgenden Befehl aus:

		sudo npm install -g https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

2.	Führen Sie den folgenden Befehl aus, um die Installation zu überprüfen:

		azure hdinsight -h

	Sie können den Parameter *-h* auf verschiedenen Ebenen verwenden, um Hilfeinformationen anzuzeigen. Zum Beispiel:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**So stellen Sie eine Verbindung mit Ihrem Azure-Abonnement her**

Bevor Sie die Befehlszeilenschnittstelle verwenden können, müssen Sie die Konnektivität zwischen Ihrer Arbeitsstation und Azure konfigurieren. Die Befehlszeilenschnittstelle verwendet Ihre Azure-Kontoinformationen, um sich mit Ihrem Benutzerkonto zu verbinden. Sie finden diese Informationen in Azure in einer Einstellungsveröffentlichungsdatei. Die Einstellungsveröffentlichungsdatei kann anschließend als persistente lokale Einstellung importiert werden und wird von der Befehlszeilenschnittstelle in späteren Operationen verwendet. Sie müssen Ihre Veröffentlichungseinstellungen nur einmal importieren.

> [AZURE.NOTE]Die Einstellungsveröffentlichungsdatei enthält vertrauliche Daten. Microsoft empfiehlt, dass Sie die Datei löschen oder weitere Schritte unternehmen, um den Benutzerordner zu verschlüsseln, der die Datei enthält. Ändern Sie unter Windows die Ordnereigenschaften, oder verwenden Sie die BitLocker-Laufwerkverschlüsselung.


1.	Öffnen Sie ein Terminalfenster.
2.	Führen Sie den folgenden Befehl aus, um sich bei Ihrem Azure-Abonnement anzumelden:

		azure account download

	![HDI.Linux.CLIAccountDownloadImport](./media/hdinsight-provision-clusters/HDI.Linux.CLIAccountDownloadImport.png)

	Dieser Befehl öffnet die Webseite, von der Sie die Datei mit den Veröffentlichungseinstellungen herunterladen können. Wenn die Webseite nicht geöffnet wird, klicken Sie auf den Link im Terminalfenster, um die Browserseite zu öffnen und sich beim Portal anzumelden.

3.	Laden Sie die Datei mit den Veröffentlichungseinstellungen auf den Computer herunter.
5.	Führen Sie im Eingabeaufforderungsfenster den folgenden Befehl aus, um die Einstellungsveröffentlichungsdatei zu importieren:

		azure account import <path/to/the/file>


#### <a id="cliwin"></a>Einrichten der plattformübergreifenden Befehlszeile für Windows

Führen Sie die folgenden Verfahren zum Einrichten des Windows-Computers für das Verwenden von Azure-Befehlszeilentools aus:

- Installieren der plattformübergreifenden Befehlszeile (mithilfe von NPM oder Windows Installer)
- Download und Import der Veröffentlichungseinstellungen für das Azure-Konto


Sie können die Befehlszeilenschnittstelle entweder über NPM oder Windows Installer installieren. Microsoft empfiehlt, dass Sie nur eine der beiden Optionen für die Installation verwenden.

**So installieren Sie die Befehlszeilenschnittstelle mit dem NPM**

1.	Öffnen Sie die Webseite **www.nodejs.org**.
2.	Klicken Sie auf **INSTALL**, und befolgen Sie die Anweisungen unter Beibehaltung der Standardeinstellungen.
3.	Öffnen Sie die **Eingabeaufforderung** (bzw. *Azure-Eingabeaufforderung* oder *Developer-Eingabeaufforderung für VS2012*) auf Ihrer Arbeitsstation.
4.	Geben Sie im Eingabeaufforderungsfenster den folgenden Befehl ein.

		npm install -g https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

	> [AZURE.NOTE]Wenn die Fehlermeldung angezeigt wird, dass der NPM-Befehl nicht gefunden wurde, überprüfen Sie, ob sich die folgenden Pfade in der Umgebungsvariable PATH befinden: <i>C:\\Program Files (X 86)\\nodejs;C:\\Users\[Benutzername]\\AppData\\Roaming\\npm</i> oder <i>C:\\Program Files\\nodejs;C:\\Users\[Benutzername]\\AppData\\Roaming\\npm</i>

5.	Führen Sie den folgenden Befehl aus, um die Installation zu überprüfen:

		azure hdinsight -h

	Sie können den Parameter *-h* auf verschiedenen Ebenen verwenden, um Hilfeinformationen anzuzeigen. Zum Beispiel:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**So installieren Sie die Befehlszeilenschnittstelle mit dem Windows Installer**

1.	Navigieren Sie zu **http://azure.microsoft.com/downloads/**.2.	Blättern Sie nach unten zum Bereich **Befehlszeilentools**, klicken Sie auf **Plattformübergreifende Befehlszeilenschnittstelle** und führen Sie den Webplattform-Installer aus.

**So laden Sie die Veröffentlichungseinstellungen herunter und installieren sie**

Bevor Sie die Befehlszeilenschnittstelle verwenden können, müssen Sie die Konnektivität zwischen Ihrer Arbeitsstation und Azure konfigurieren. Die Befehlszeilenschnittstelle verwendet Ihre Azure-Kontoinformationen, um sich mit Ihrem Benutzerkonto zu verbinden. Sie finden diese Informationen in Azure in einer Einstellungsveröffentlichungsdatei. Die Einstellungsveröffentlichungsdatei kann anschließend als persistente lokale Einstellung importiert werden und wird von der Befehlszeilenschnittstelle in späteren Operationen verwendet. Sie müssen Ihre Veröffentlichungseinstellungen nur einmal importieren.

> [AZURE.NOTE]Die Einstellungsveröffentlichungsdatei enthält vertrauliche Daten. Microsoft empfiehlt, dass Sie die Datei löschen oder weitere Schritte unternehmen, um den Benutzerordner zu verschlüsseln, der die Datei enthält. Windows: Ändern Sie die Ordnereigenschaften, oder verwenden Sie BitLocker.


1.	Öffnen Sie eine **Eingabeaufforderung**.
2.	Führen Sie den folgenden Befehl aus, um die Datei mit den Veröffentlichungseinstellungen herunterzuladen:

		azure account download

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	Dieser Befehl öffnet die Webseite, von der Sie die Datei mit den Veröffentlichungseinstellungen herunterladen können.

3.	Klicken Sie in der Aufforderung auf **Speichern** und geben Sie einen Speicherort für die Datei an.
5.	Führen Sie im Eingabeaufforderungsfenster den folgenden Befehl aus, um die Einstellungsveröffentlichungsdatei zu importieren:

		azure account import <path/to/the/file>

#### <a id="cliprovision"></a>Bereitstellen von HDInsight-Clustern mithilfe der Azure-Befehlszeilenschnittstelle

Die folgenden Prozeduren müssen für die Bereitstellung eines HDInsight-Clusters mithilfe der Azure-Befehlszeilenschnittstelle ausgeführt werden:

- Erstellen eines Azure-Speicherkontos
- Bereitstellen eines Clusters

**So erstellen Sie ein Azure-Speicherkonto**

HDInsight verwendet Azure-Blob-Speichercontainer als Standarddateisystem. Sie benötigen ein Azure-Speicherkonto, um einen HDInsight-Cluster erstellen zu können. Das Speicherkonto muss sich im gleichen Datencenter befinden.

- Führen Sie den folgenden Befehl in einem Eingabeaufforderungsfenster aus, um ein Azure-Speicherkonto zu erstellen:

		azure storage account create [options] <StorageAccountName>

	Wählen Sie einen geeigneten Ort für die Bereitstellung des HDInsight-Clusters aus, wenn Sie dazu aufgefordert werden. Der Speicher muss sich am gleichen Standort befinden wie der HDInsight-Cluster. HDInsight-Cluster sind momentan nur in den Regionen **Ostasien**, **Südostasien**, **Nordeuropa**, **Westeuropa**, **Osten USA**, **Westen USA**, **USA Nord Mitte** und **USA Süd Mitte** verfügbar.

Informationen zum Erstellen von Azure-Speicherkonten im Azure-Portal finden Sie unter [Erstellen, Verwalten oder Löschen von Speicherkonten](../storage-create-storage-account.md).

Falls Sie bereits ein Speicherkonto haben, aber dessen Kontonamen und Kontoschlüssel nicht kennen, können Sie diese Daten mit den folgenden Befehlen abrufen:

	-- Lists Storage accounts
	azure storage account list

	-- Shows information for a Storage account
	azure storage account show <StorageAccountName>

	-- Lists the keys for a Storage account
	azure storage account keys list <StorageAccountName>

Ausführliche Informationen zum Abrufen dieser Informationen im Azure-Portal finden Sie unter [Erstellen, Verwalten oder Löschen von Speicherkonten](../storage-create-storage-account.md) im Abschnitt *Anzeigen, Kopieren und Neuerstellen von Speicherzugriffsschlüsseln*.

Für einen HDInsight-Cluster benötigen Sie außerdem einen Container innerhalb des Speicherkontos. Falls das angegebene Speicherkonto noch keinen Container enthält, werden Sie vom Befehl *azure hdinsight cluster create* zur Eingabe eines Containernamens aufgefordert und der Container wird erstellt. Sie können den folgenden Befehl verwenden, um den Container vorab zu erstellen:

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

Sobald Sie Ihr Speicherkonto und Ihren Blob-Container vorbereitet haben, können Sie einen Cluster erstellen.

**So stellen Sie einen HDInsight-Cluster bereit**

- Geben Sie im Eingabeaufforderungsfenster den folgenden Befehl ein:

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <StorageContainerName> --dataNodeCount <NumberOfNodes> --location <DataCenterLocation> --userName <HDInsightClusterUsername> --password <HDInsightClusterPassword> --osType windows

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**So stellen Sie einen HDInsight-Cluster mit einer Konfigurationsdatei bereit**

Normalerweise erstellen Sie einen HDInsight-Cluster, führen Ihre Jobs aus und löschen den Cluster anschließend, um Kosten zu sparen. In der Befehlszeilenschnittstelle können Sie die Konfigurationen in einer Datei speichern, um diese bei zukünftigen Bereitstellungen von Clustern wiederverwenden zu können.

- Führen Sie im Eingabeaufforderungsfenster die folgenden Befehle aus:


		#Create the config file
		azure hdinsight cluster config create <file>

		#Add commands to create a basic cluster
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --dataNodeCount <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --userName "<Username>" --password "<UserPassword>" --osType windows

		#If required, include commands to use additional Blob storage with the cluster
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
		       --storageAccountKey "<StorageAccountKey>"

		#If required, include commands to use a SQL database as a Hive metastore
		azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

		#If required, include commands to use a SQL database as an Oozie metastore
		azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"

		#Run this command to create a cluster by using the config file
		azure hdinsight cluster create --config <file>

>[AZURE.NOTE]Die als Metastore verwendete Azure SQL-Datenbank muss für die Konnektivität mit anderen Azure-Diensten konfiguriert sein, inklusive Azure HDInsight. Klicken Sie im Dashboard der Azure SQL-Datenbank mit der rechten Maustaste auf den Servernamen. Dies ist der Server, auf dem die SQL-Datenbankinstanz läuft. Öffnen Sie die Serveransicht, klicken Sie auf **Konfigurieren**, wählen Sie unter **Azure-Dienste** den Wert **Ja** aus, und klicken Sie auf **Speichern**.


	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**So listen Sie Clusterdetails auf und zeigen sie an**

- Mit den folgenden Befehlen können Sie Clusterdetails auflisten und anzeigen:

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**So löschen Sie Cluster**

- Mit dem folgenden Befehl können Sie ein Cluster löschen:

		azure hdinsight cluster delete <ClusterName>



##<a id="sdk"></a> Verwenden des HDInsight .NET SDK
Das HDInsight .NET SDK enthält .NET-Clientbibliotheken zur Vereinfachung der Arbeit mit HDInsight in einer .NET Framework-Anwendung.

Führen Sie die folgenden Schritte aus, um einen HDInsight-Cluster mithilfe des SDK bereitzustellen:

- Installieren des HDInsight .NET SDK
- Erstellen eines selbstsignierten Zertifikats
- Erstellen einer Konsolenanwendung
- Ausführen der Anwendung


**So installieren Sie das HDInsight .NET SDK**

Sie können die neueste veröffentlichte Version des SDK von [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) herunterladen und installieren. Die Anweisungen dazu werden im nächsten Verfahren erläutert.

**So erstellen Sie ein selbstsigniertes Zertifikat**

Erstellen Sie ein selbstsigniertes Zertifikat, installieren Sie es auf Ihrer Arbeitsstation und laden Sie es in Ihr Azure-Abonnement hoch. Weitere Hinweise hierzu finden Sie unter [Erstellen eines selbstsignierten Zertifikats](http://go.microsoft.com/fwlink/?LinkId=511138).


**So erstellen Sie eine Visual Studio-Konsolenanwendung**

1. Öffnen Sie Visual Studio 2013.

2. Klicken Sie im Menü **Datei** auf **Neu** und dann auf **Projekt**.

3. Geben Sie unter **Neues Projekt** die folgenden Werte ein, bzw. wählen Sie sie aus:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Eigenschaft</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Wert</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Kategorie</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Vorlagen/Visual C#/Windows</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Vorlage</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Konsolenanwendung</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateHDICluster</td></tr>
</table>

4. Klicken Sie auf **OK**, um das Projekt zu erstellen.

5. Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager** und dann auf **Paket-Manager-Konsole**.

6. Führen Sie den folgenden Befehl in der Konsole aus, um die Pakete zu installieren:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Mit diesem Befehl werden dem aktuellen Visual Studio-Projekt .NET-Bibliotheken und Verweise darauf hinzugefügt.

7. Doppelklicken Sie im Projektmappen-Explorer auf **Program.cs**, um die Datei zu öffnen.

8. Fügen Sie die folgenden Anweisungen am Anfang der Datei ein:

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;


9. Fügen Sie in der Main()-Funktion den folgenden Code ein:

        string certfriendlyname = "<CertificateFriendlyName>";     // Friendly name for the certificate you created earlier  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Get the certificate object from certificate store by using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);

        // Create the Storage account if it doesn't exist

        // Create the container if it doesn't exist.

		// Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply the cluster information
        ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
        {
            Name = clustername,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = containername,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize
        };

		// Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue.");
        Console.ReadKey();

10. Ersetzen Sie die Variablen am Anfang der "Main()"-Funktion.

**So führen Sie die Anwendung aus**

Öffnen Sie die Anwendung in Visual Studio und drücken Sie **F5**, um die Anwendung auszuführen. In einem Konsolenfenster wird der Status der Anwendung angezeigt. Die Erstellung eines HDInsight-Clusters kann mehrere Minuten in Anspruch nehmen.



##<a id="nextsteps"></a> Nächste Schritte
In diesem Artikel haben Sie verschiedene Methoden zum Bereitstellen eines HDInsight-Clusters kennen gelernt. Weitere Informationen finden Sie in den folgenden Artikeln:

* [Erste Schritte mit Azure HDInsight](../hdinsight-get-started.md) – Erfahren Sie, wie Sie die Arbeit mit Ihrem HDInsight-Cluster aufnehmen können.
* [Verwenden von Sqoop mit HDInsight](hdinsight-use-sqoop.md) – Erfahren Sie, wie Sie Daten zwischen HDInsight und der SQL-Datenbank oder SQL Server kopieren können.
* [Verwalten von HDInsight mit PowerShell](hdinsight-administer-use-powershell.md) – Erfahren Sie, wie Sie mit HDInsight unter Verwendung von PowerShell arbeiten können.
* [Programmgesteuerte Übermittlung von Hadoop-Aufträgen](hdinsight-submit-hadoop-jobs-programmatically.md) – Erfahren Sie, wie Sie Aufträge programmgesteuert an HDInsight übermitteln können
* [Dokumentation zum Azure HDInsight SDK][hdinsight-sdk-documentation] – Machen Sie sich mit dem HDInsight SDK vertraut.


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hadoop-hdinsight-intro]: hdinsight-hadoop-introduction.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-powershell-reference]: http://msdn.microsoft.com/library/windowsazure/dn479228.aspx
[hdinsight-storm-get-started]: ../hdinsight-storm-getting-started.md

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli.md

[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png

[image-customprovision-page1]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page1.png "Bereitstellen von Hadoop HDInsight-Clustern – Details"
[image-customprovision-page3]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png "Bereitstellen von Hadoop HDInsight-Clustern – Benutzer"
[image-customprovision-page4]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png "Bereitstellen von Informationen zu Speicherkonten für Hadoop HDInsight-Cluster"
[image-customprovision-page5]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page5.png "Bereitstellen von zusätzlichen Informationen zu Speicherkonten für Hadoop HDInsight-Cluster"
[image-customprovision-page6]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page6.png "Konfigurieren von Skriptaktionen zum Anpassen von HDInsight-Clustern"


[image-cli-account-download-import]: ./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "Auflisten und Anzeigen von Clustern"

[image-hdi-ps-provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png

[img-hdi-cluster]: ./media/hdinsight-provision-clusters/HDI.Cluster.png

[89e2276a]: hdinsight-use-sqoop.md "Verwenden von Sqoop mit HDInsight"

<!--HONumber=54--> 