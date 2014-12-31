<properties title="Build an HBase application using Maven" pageTitle="Build an HBase application using Maven" description="Learn how to use Apache Maven to build a Java-based Apache HBase application, then deploy it to Azure HDInsight" metaKeywords="Maven hbase hadoop, hbase hadoop, maven java hbase, maven java hbase hadoop, maven java hadoop, hbase hdinsight, hbase java hdinsight, maven hdinsight, maven java hdinsight, hadoop database, hdinsight database" services="hdinsight" solutions="big-data" documentationCenter="" authors="larryfr" videoId="" scriptId="" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="larryfr" />

## Verwenden von Maven zur Entwicklung von Java-Anwendungen, die HBase mit HDInsight (Hadoop) nutzen

Erfahren Sie, wie Sie eine [Apache HBase][Apache HBase]-Anwendung in Java mithilfe von Apache Maven erstellen und entwickeln. Setzen Sie anschließend die Anwendung mit Azure HDInsight (Hadoop) ein.

[Maven][Maven] ist ein Projektmanagement- und Verständnistool, mit dem Sie Software, Dokumentationen und Berichte für Java-Projekte erstellen können. In diesem Artikel erfahren Sie, wie Sie es für das Erstellen einer einfachen Java-Anwendung verwenden, die eine HBase-Tabelle auf einem Azure HDInsight-Cluster erstellt, abfragt und löscht.

## Anforderungen

-   [Java platform JDK][Java platform JDK] 7 oder höher

-   [Maven][Maven]

-   [Ein Azure HDInsight-Cluster mit HBase][Ein Azure HDInsight-Cluster mit HBase]

## Erstellen des Projekts

1.  Ändern Sie in der Befehlszeile Ihrer Entwicklungsumgebung die Verzeichnisse auf den Ort, an dem Sie das Projekt anlegen möchten. Beispiel: `cd code\hdinsight`

2.  Verwenden Sie den Befehl **mvn**, der mit Maven installiert wird, um das Gerüst für das Projekt zu erstellen.

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Damit wird ein neues Verzeichnis im aktuellen angelegt, das den Namen trägt, der vom Parameter **artifactID** (**hbaseapp** in diesem Beispiel) festgelegt wurde. Dieses Verzeichnis enthält die folgenden Objekte.

    -   **pom.xml**: Das Projekt-Objektmodell ([POM][POM]) enthält Informationen und Konfigurationsdetails für das Erstellen des Projekts.

    -   **src**: Das Verzeichnis, das das Verzeichnis **main\\java\\com\\microsoft\\examples** enthält; hier schreiben Sie die Anwendung.

3.  Löschen Sie die Datei **src\\test\\java\\com\\microsoft\\examples\\apptest.java**, die in diesem Beispiel nicht verwendet wird.

## Aktualisieren des Projekt-Objektmodells

1.  Bearbeiten Sie die Datei **pom.xml** und fügen Sie Folgendes im Abschnitt `<dependencies>` ein.

        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>0.98.4-hadoop2</version>
        </dependency>

    Damit wird Maven informiert, dass das Projekt den **hbase-Client** in der Version **0.98.4-hadoop2** benötigt. Beim Kompilieren wird er aus dem standardmäßigen Maven-Repository heruntergeladen. Sie können [Maven-Repositorysuche][Maven-Repositorysuche] verwenden, wenn Sie weitere Informationen zu dieser Abhängigkeit erhalten möchten.

2.  Fügen Sie der Datei **pom.xml** folgenden Code hinzu: Dieser muss sich in den `<project>...</project>`-Tags in der Datei befinden, z. B. zwischen `</dependencies>` und `</project>`.

        <build>
          <sourceDirectory>src</sourceDirectory>
          <resources>
            <resource>
              <directory>${basedir}/conf</directory>
              <filtering>false</filtering>
              <includes>
                <include>hbase-site.xml</include>
              </includes>
            </resource>
          </resources>
          <plugins>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-shade-plugin</artifactId>
              <version>2.3</version>
              <configuration>
                <transformers>
                  <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                  </transformer>
                </transformers>
              </configuration>
              <executions>
                <execution>
                  <phase>package</phase>
                  <goals>
                    <goal>shade</goal>
                  </goals>
                </execution>
              </executions>
            </plugin>       
          </plugins>
        </build>

    Damit wird eine Ressource konfiguriert (**conf\\hbase-site.xml**), die die Konfigurationsinformationen für HBase enthält.

    > [WACOM.NOTE] Sie können die Konfigurationswerte auch per Code festlegen. Wie Sie das tun, erfahren Sie in den Kommentaren im Beispiel **CreateTable** unten.

    Damit wird auch [maven-shade-plugin][maven-shade-plugin] konfiguriert, das die Lizenzduplizierung im JAR verhindert, das von Maven erstellt wird. Es wird deshalb verwendet, weil die doppelten Lizenzdateien bei der Laufzeit einen Fehler auf dem HDInsight-Cluster verursachen. Wird maven-shade-plugin mit der `ApacheLicenseResourceTransformer`-Implementierung verwendet, so wird dieser Fehler verhindert.

    Das maven-shade-plugin produziert außerdem ein uberjar (oder fatjar), das alle Abhängigkeiten enthält, die von der Anwendung benötigt werden.

3.  Speichern Sie die Datei **pom.xml**.

4.  Erstellen Sie ein neues Verzeichnis namens **conf** im Verzeichnis **hbaseapp**. Erstellen Sie im Verzeichnis **conf** eine neue Datei namens **hbase-site.xml** und verwenden Sie Folgendes als Inhalt.

        <?xml version="1.0"?>
        <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
        <!--
        /**
         * Copyright 2010 The Apache Software Foundation
         *
         * Licensed to the Apache Software Foundation (ASF) under one
         * or more contributor license agreements.  See the NOTICE file
         * distributed with this work for additional information
         * regarding copyright ownership.  The ASF licenses this file
         * to you under the Apache License, Version 2.0 (the
         * "License"); you may not use this file except in compliance
         * with the License.  You may obtain a copy of the License at
         *
         *     http://www.apache.org/licenses/LICENSE-2.0
         *
         * Unless required by applicable law or agreed to in writing, software
         * distributed under the License is distributed on an "AS IS" BASIS,
         * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
         * See the License for the specific language governing permissions and
         * limitations under the License.
         */
        -->
        <configuration>
            <name>hbase.cluster.distributed</name>
            <value>true</value>
          </property>
          <property>
            <name>hbase.zookeeper.quorum</name>
            <value>zookeepernode0:2181 zookeepernode1:2181 zookeepernode2:2181</value>
          </property>

        </configuration>

    Diese Datei wird zum Laden der HBase-Konfiguration für einen HDInsight-Cluster verwendet.

    > [WACOM.NOTE] Das ist eine sehr minimale hbase-site.xml-Datei, die die Mindesteinstellungen für den HDInsight-Cluster enthält. Eine Vollversion der von HDInsight, verwendeten Konfigurationsdatei hbase-site.xml verbinden Sie sich per [Remotedesktop mit dem HDInsight-Cluster][Remotedesktop mit dem HDInsight-Cluster]. Dort finden Sie die Datei hbase-site.xml im Verzeichnis C:\\apps\\dist\\hbase-\<version number\>-hadoop2\\conf. Der Dateiteil mit der Versionsnummer des Dateipfads ändert sich, wenn HBase auf dem Cluster aktualisiert wird.

5.  Speichern Sie die Datei **hbase-site.xml**.

## Erstellen der Anwendung

1.  Gehen Sie zum Verzeichnis **hbaseapp\\src\\com\\microsoft\\examples** und benennen Sie die Datei app.java\_\_ um in **CreateTable.java**.

2.  Öffnen Sie die Datei **CreateTable.java** und ersetzen Sie die vorhandenen Inhalte mit Folgendem:

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;
        import org.apache.hadoop.hbase.HTableDescriptor;
        import org.apache.hadoop.hbase.TableName;
        import org.apache.hadoop.hbase.HColumnDescriptor;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Put;
        import org.apache.hadoop.hbase.util.Bytes;

        public class CreateTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Example of setting zookeeper values for HDInsight
            //   in code instead of an hbase-site.xml file
            //
            // config.set("hbase.zookeeper.quorum",
            //            "zookeepernode0,zookeepernode1,zookeepernode2");
            //config.set("hbase.zookeeper.property.clientPort", "2181");
            //config.set("hbase.cluster.distributed", "true");

            // create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);

            // create the table...
            HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
            // ... with two column families
            tableDescriptor.addFamily(new HColumnDescriptor("name"));
            tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
            admin.createTable(tableDescriptor);

            // define some people
            String[][] people = {
                { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
                { "2", "Franklin", "Holtz", "franklin@contoso.com" },
                { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
                { "4", "Rae", "Schroeder", "rae@contoso.com" },
                { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
                { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

            HTable table = new HTable(config, "people");

            // Add each person to the table
            //   Use the `name` column family for the name
            //   Use the `contactinfo` column family for the email
            for (int i = 0; i< people.length; i++) {
              Put person = new Put(Bytes.toBytes(people[i][0]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
              person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
              table.put(person);
            }
            // flush commits and close the table
            table.flushCommits();
            table.close();
          }
        }

    Das ist die Klasse **CreateTable** die eine Tabelle namens **people** erzeugt und sie mit ein paar vordefinierten Benutzern füllt.

3.  Speichern Sie die Datei **CreateTable.java**.

4.  Erstellen Sie im Verzeichnis **hbaseapp\\com\\microsoft\\examples** eine neue Datei namens **SearchByEmail.java**. Fügen Sie Folgendes als Inhalt der Datei hinzu:

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Scan;
        import org.apache.hadoop.hbase.client.ResultScanner;
        import org.apache.hadoop.hbase.client.Result;
        import org.apache.hadoop.hbase.filter.RegexStringComparator;
        import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
        import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
        import org.apache.hadoop.hbase.util.Bytes;
        import org.apache.hadoop.util.GenericOptionsParser;

        public class SearchByEmail {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Use GenericOptionsParser to get only the parameters to the class
            // and not all the parameters passed (when using WebHCat for example)
            String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
            if (otherArgs.length != 1) {
              System.out.println("usage: [regular expression]");
              System.exit(-1);
            }

            // Open the table
            HTable table = new HTable(config, "people");

            // Define the family and qualifiers to be used
            byte[] contactFamily = Bytes.toBytes("contactinfo");
            byte[] emailQualifier = Bytes.toBytes("email");
            byte[] nameFamily = Bytes.toBytes("name");
            byte[] firstNameQualifier = Bytes.toBytes("first");
            byte[] lastNameQualifier = Bytes.toBytes("last");

            // Create a new regex filter
            RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
            // Attach the regex filter to a filter
            //   for the email column
            SingleColumnValueFilter filter = new SingleColumnValueFilter(
              contactFamily,
              emailQualifier,
              CompareOp.EQUAL,
              emailFilter
            );

            // Create a scan and set the filter
            Scan scan = new Scan();
            scan.setFilter(filter);

            // Get the results
            ResultScanner results = table.getScanner(scan);
            // Iterate over results and print  values
            for (Result result : results ) {
              String id = new String(result.getRow());
              byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
              String firstName = new String(firstNameObj);
              byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
              String lastName = new String(lastNameObj);
              System.out.println(firstName + " " + lastName + " - ID: " + id);
              byte[] emailObj = result.getValue(contactFamily, emailQualifier);
              String email = new String(emailObj);
              System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
            }
            results.close();
            table.close();
          }
        }

    Die Klasse **SearchByEmail** kann zur Abfrage von Zeilen nach E-Mail-Adresse verwendet werden. Da sie einen Filter für reguläre Ausdrücke verwendet, können Sie beim Einsatz der Klasse entweder eine Zeichenfolge angeben oder einen regulären Ausdruck.

5.  Speichern Sie die Datei **SearchByEmail.java**.

6.  Erstellen Sie im Verzeichnis **hbaseapp\\com\\microsoft\\examples** eine neue Datei namens **DeleteTable.java**. Fügen Sie Folgendes als Inhalt der Datei hinzu:

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;

        public class DeleteTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);

            // Disable, and then delete the table
            admin.disableTable("people");
            admin.deleteTable("people");
          }
        }

    Diese Klasse dient nur zum Aufräumen dieses Beispiels. Sie deaktiviert zunächst die von der Klasse **CreateTable** erstellte Tabelle und legt diese dann ab.

7.  Speichern Sie die Datei **CreateTable.java**.

## Erstellen und Packen der Anwendung

1.  Öffnen Sie eine Eingabeaufforderung, und ändern Sie das Verzeichnis in **hbaseapp**.

2.  Verwenden Sie den folgenden Befehl, um ein JAR mit der Anwendung zu erstellen.

        mvn clean package

    Damit werden etwaige frühere Erstellungsartefakte entfernt, alle noch nicht installierten Abhängigkeiten heruntergeladen und dann die Anwendung erstellen und gepackt.

3.  Nachdem dieser Befehl ausgeführt wurde, enthält das Verzeichnis **hbaseapp\\target** eine Datei namens **hbaseapp-1.0-SNAPSHOT.jar**.

    > [WACOM.NOTE] Die Datei **hbaseapp-1.0-SNAPSHOT.jar** ist ein Uberjar (manchmal auch Fatjar genannt), das alle Abhängigkeiten enthält, die für das Ausführen der Datei erforderlich sind.

## Hochladen der JAR-Datei und Starten des Auftrags

> [WACOM.NOTE] Es gibt viele Möglichkeiten, eine Datei auf einen HDInsight-Cluster hochzuladen. Sie sind beschrieben unter [Hochladen von Daten für Hadoop-Aufträge in HDInsight][Hochladen von Daten für Hadoop-Aufträge in HDInsight]. Bei den Schritten unten kommt [Azure PowerShell][Azure PowerShell] zum Einsatz.

1.  Nachdem Sie [Azure PowerShell][Azure PowerShell] installiert und konfiguriert haben, erstellen Sie eine neue Datei namens **hbase-runner.psm1**. Fügen Sie Folgendes als Inhalt der Datei hinzu:

        <#
        .SYNOPSIS
            Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
            Copies a file from a local directory to the blob container for
            the HDInsight cluster.
        .EXAMPLE
            Start-HBaseExample -className "com.microsoft.examples.CreateTable"
                -clusterName "MyHDInsightCluster"

        .EXAMPLE
            Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
                -clusterName "MyHDInsightCluster"
                -emailRegex "contoso.com"

        .EXAMPLE
            Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
                -clusterName "MyHDInsightCluster"
                -emailRegex "^r" -showErr
        #>

        function Start-HBaseExample {
            [CmdletBinding(SupportsShouldProcess = $true)]
            param(
                #The class to run
                [Parameter(Mandatory = $true)]
                [String]$className,

                #The name of the HDInsight cluster
                [Parameter(Mandatory = $true)]
                [String]$clusterName,

                #Only used when using SearchByEmail
                [Parameter(Mandatory = $false)]
                [String]$emailRegex,

                #Use if you want to see stderr output
                [Parameter(Mandatory = $false)]
                [Switch]$showErr
            )

            Set-StrictMode -Version 3

            # Is the Azure module installed?
            FindAzure

            # The JAR
            $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"

            # The job definition
            $jobDefinition = New-AzureHDInsightMapReduceJobDefinition `
              -JarFile $jarFile `
              -ClassName $className `
              -Arguments $emailRegex

            # Get the job output
            $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
            Write-Host "Wait for the job to complete ..." -ForegroundColor Green
            Wait-AzureHDInsightJob -Job $job
            if($showErr)
            {
                Write-Host "STDERR"
                Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
            }
            Write-Host "Display the standard output ..." -ForegroundColor Green
            Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput
        }

        <#
        .SYNOPSIS
            Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
            Copies a file from a local directory to the blob container for
            the HDInsight cluster.
        .EXAMPLE
            Add-HDInsightFile -localPath "C:\temp\data.txt"
                -destinationPath "example/data/data.txt"
                -ClusterName "MyHDInsightCluster"
        .EXAMPLE
            Add-HDInsightFile -localPath "C:\temp\data.txt"
                -destinationPath "example/data/data.txt"
                -ClusterName "MyHDInsightCluster"
                -Container "MyContainer"
        #>

        function Add-HDInsightFile {
            [CmdletBinding(SupportsShouldProcess = $true)]
            param(
                #The path to the local file.
                [Parameter(Mandatory = $true)]
                [String]$localPath,

                #The destination path and file name, relative to the root of the container.
                [Parameter(Mandatory = $true)]
                [String]$destinationPath,

                #The name of the HDInsight cluster
                [Parameter(Mandatory = $true)]
                [String]$clusterName,

                #If specified, overwrites existing files without prompting
                [Parameter(Mandatory = $false)]
                [Switch]$force
            )

            Set-StrictMode -Version 3

            # Is the Azure module installed?
            FindAzure

            # Does the local path exist?
            if (-not (Test-Path $localPath))
            {
                throw "Source path '$localPath' does not exist."
            }

            # Get the primary storage container
            $storage = GetStorage -clusterName $clusterName

            # Upload file to storage, overwriting existing files if -force was used.
            Set-AzureStorageBlobContent -File $localPath -Blob $destinationPath -force:$force `
                                        -Container $storage.container `
                                        -Context $storage.context
        }

        function FindAzure {
            # Is the Azure module installed?
            if (-not(Get-Module -ListAvailable Azure))
            {
                throw "Windows Azure PowerShell not found! For help, see http://www.windowsazure.com/de-de/documentation/articles/install-configure-powershell/"
            }

            # Is there an active Azure subscription?
            $sub = Get-AzureSubscription -ErrorAction SilentlyContinue
            if(-not($sub))
            {
                throw "No active Azure subscription found! If you have a subscription, use the Add-AzureAccount or Import-PublishSettingsFile cmdlets to make the Azure account available to Windows PowerShell."
            }
        }

        function GetStorage {
            param(
                [Parameter(Mandatory = $true)]
                [String]$clusterName
            )
            $hdi = Get-AzureHDInsightCluster -name $clusterName
            # Does the cluster exist?
            if (!$hdi)
            {
                throw "HDInsight cluster '$clusterName' does not exist."
            }
            # Create a return object for context & container
            $return = @{}
            $storageAccounts = @{}
            # Get the primary storage account information
            $storageAccountName = $hdi.DefaultStorageAccount.StorageAccountName.Split(".",2)[0]
            $storageAccountKey = $hdi.DefaultStorageAccount.StorageAccountKey
            # Build the hash of storage account name/keys
            $storageAccounts.Add($hdi.DefaultStorageAccount.StorageAccountName, $storageAccountKey)
            foreach($account in $hdi.StorageAccounts)
            {
                $storageAccounts.Add($account.StorageAccountName, $account.StorageAccountKey)
            }
            # Get the storage context, as we can't depend
            # on using the default storage context
            $return.context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
            # Get the container, so we know where to
            # find/store blobs
            $return.container = $hdi.DefaultStorageAccount.StorageContainerName
            # Return storage accounts to support finding all accounts for
            # a cluster
            $return.storageAccounts = $storageAccounts

            return $return
        }
        # Only export the verb-phrase things
        export-modulemember *-*

    Diese Datei enthält zwei Module:

    -   **Add-HDInsightFile** – wird für das Hochladen von Dateien zu HDInsight verwendet

    -   **Start-HBaseExample** – wird für das Ausführen der bereits erstellter Klassen verwendet

2.  Speichern Sie die Datei **hbase-runner.psm1**.

3.  Öffnen Sie ein neues Azure PowerShell-Fenster, ändern Sie die Verzeichnis auf **hbaseapp** und führen Sie dann den folgenden Befehl aus.

        PS C:\ Import-Module c:\path\to\hbase-runner.psm1

    Ändern Sie den Pfad auf den Speicherort der früher erstellten Datei **hbase-runner.psm1**. Damit wird das Modul für diese PowerShell-Sitzung registriert.

4.  Verwenden Sie den folgenden Befehl zu Hochladen von **hbaseapp-1.0-SNAPSHOT.jar** zum HDInsight-Cluster.

        Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername

    Ersetzen Sie **hdinsightclustername** durch den Namen Ihres HDInsight-Clusters. Der Befehl lädt dann **hbaseapp-1.0-SNAPSHOT.jar** in **example/jars** im Hauptspeicher Ihres HDInsight-Clusters hoch.

5.  Verwenden Sie nach dem Hochladen der Dateien Folgendes, um eine neue Tabelle mithilfe von **hbaseapp** zu erstellen.

        Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername

    Ersetzen Sie **hdinsightclustername** durch den Namen Ihres HDInsight-Clusters.

    Dieser Befehl erstellt eine neue Tabelle namens **people** auf dem HDInsight-Cluster.

6.  Verwenden Sie den folgenden Befehl, um nach Einträgen in die Tabelle zu suchen.

        Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com

    Ersetzen Sie **hdinsightclustername** durch den Namen Ihres HDInsight-Clusters.

    Damit wird die Klasse SearchByEmail für die Suche nach etwaigen Zeilen verwendet, in denen sie Spaltenfamilie **contactinformation**, column **und email** die Zeichenfolge **contoso.com** enthält. Daraufhin sollte Sie folgende Ergebnisse erhalten:

        Rae Schroeder - rae@contoso.com - ID: 032439814
        Franklin Holtz - franklin@contoso.com - ID: 229772110
        Gabriela Ingram - gabriela@contoso.com - ID: 655336201

    Wird **fabrikam.com** als `-emailRegex`-Wert verwendet, werden Benutzer zurückgegeben, die **fabrikam.com** im Feld E-Mail haben. Da diese Suche mithilfe eines auf regulären Ausdrücken basierenden Filters implementiert wurde, können Sie auch reguläre Ausdrücke wie etwa **^r** eingeben, die Einträge zurückgibt, deren E-Mail-Adresse mit dem Buchstaben „r“ beginnt.

## Löschen der Tabelle

Wenn Sie mit dem Beispiel fertig sind, verwenden Sie den folgenden Befehl aus der PowerShell-Sitzung zum Löschen der Tabelle **people**, die darin verwendet wurde.

    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

Ersetzen Sie **hdinsightclustername** durch den Namen Ihres HDInsight-Clusters.

## Problembehandlung

### Keine oder unerwartete Ergebnisse beim Einsatz von **Start-HBaseExample**

Verwenden Sie den Parameter `-showErr`, wenn Sie das STDERR sehen möchten, die während der Ausführung des Auftrags produziert wurde.

  [Apache HBase]: http://hbase.apache.org/
  [Maven]: http://maven.apache.org/
  [Java platform JDK]: http://www.oracle.com/technetwork/java/javase/downloads/index.html
  [Ein Azure HDInsight-Cluster mit HBase]: /de-de/documentation/articles/hdinsight-hbase-get-started/#create-hbase-cluster
  [POM]: http://maven.apache.org/guides/introduction/introduction-to-the-pom.html
  [Maven-Repositorysuche]: http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar
  [maven-shade-plugin]: http://maven.apache.org/plugins/maven-shade-plugin/
  [Remotedesktop mit dem HDInsight-Cluster]: http://azure.microsoft.com/de-de/documentation/articles/hdinsight-administer-use-management-portal/#rdp
  [Hochladen von Daten für Hadoop-Aufträge in HDInsight]: /de-de/documentation/articles/hdinsight-upload-data/
  [Azure PowerShell]: /de-de/documentation/articles/install-configure-powershell/