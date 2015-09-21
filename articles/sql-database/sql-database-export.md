<properties
	pageTitle="Erstellen und Exportieren der BACPAC-Datei einer Azure SQL-Datenbank"
	description="Erstellen und Exportieren der BACPAC-Datei einer Azure SQL-Datenbank in Azure Storage"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="09/05/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Erstellen und Exportieren der BACPAC-Datei einer SQL-Datenbank

**Einzeldatenbank**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)

In diesem Artikel wird veranschaulicht, wie Sie eine BACPAC-Datei Ihrer SQL-Datenbank mit dem [Azure-Vorschauportal](https://portal.azure.com) manuell exportieren.

Ein „BACPAC“ ist eine BACPAC-Datei, die ein Datenbankschema und Daten enthält. Weitere Informationen finden Sie unter „Sicherungspaket (.bacpac)“ in [Datenebenenanwendungen](https://msdn.microsoft.com/library/ee210546.aspx).

> [AZURE.NOTE]Azure SQL-Datenbank erstellt für jede Benutzerdatenbank automatisch Sicherungen. Weitere Informationen finden Sie unter [Übersicht über die Geschäftskontinuität](sql-database-business-continuity.md).


Das BACPAC wird in einen Azure-Speicherblobcontainer exportiert, den Sie nach dem erfolgreichen Abschluss des Vorgangs herunterladen können.

Damit Sie die Anweisungen in diesem Artikel ausführen können, benötigen Sie Folgendes:

- Ein Azure-Abonnement. Wenn Sie ein Azure-Abonnement benötigen, müssen Sie lediglich oben auf dieser Seite auf den Link **Kostenlose Testversion** klicken. Lesen Sie anschließend den Artikel weiter.
- Azure SQL-Datenbank. Wenn Sie nicht über eine SQL-Datenbank Server der Version 12 verfügen, erstellen Sie ihn anhand der Schritte in dem Artikel [Erstellen der ersten Azure SQL-Datenbank](sql-database-get-started.md).
- Ein [Azure Storage-Konto](storage-create-storage-account.md) mit einem Blobcontainer zum Speichern der Datenbanksicherung Derzeit muss für das Speicherkonto das klassische Bereitstellungsmodell verwendet werden. Wählen Sie also **Klassisch**, wenn Sie ein Speicherkonto erstellen. 


## Exportieren der Datenbank

Öffnen Sie das Blatt „SQL-Datenbank“ für die Datenbank, die Sie als BACPAC-Datei exportieren möchten:

1.	Öffnen Sie das [Azure-Vorschauportal](https//:portal.azure.com).
2.	Klicken Sie auf **ALLE DURCHSUCHEN**.
3.	Klicken Sie auf **SQL-Datenbanken**.
2.	Klicken Sie auf die Datenbank, die Sie als BACPAC-Datei exportieren möchten.
3.	Klicken Sie im Blatt „SQL-Datenbank“ auf **Exportieren**, um das Blatt **Datenbank exportieren** zu öffnen:

    ![Schaltfläche „Exportieren“][1]

1.  Klicken Sie auf **Storage**, und wählen Sie Ihr Speicherkonto und den Blobcontainer aus, in dem die BACPAC-Datei gespeichert wird:

    ![Datenbank exportieren][2]

1.  Geben Sie die **Serveradministratoranmeldung** und das **Kennwort** für den SQL Azure-Server mit der Datenbank ein, die Sie sichern.
1.  Klicken Sie auf **Erstellen**, um die Datenbank zu exportieren.

Durch Klicken auf **Erstellen** wird eine Anforderung zum Exportieren der Datenbank erstellt und an den Dienst gesendet. Je nach Größe Ihrer Datenbank kann es einige Zeit dauern, bis der Exportvorgang abgeschlossen ist.

## Überwachen des Fortschritts des Exportvorgangs

2.	Klicken Sie auf **ALLE DURCHSUCHEN**.
3.	Klicken Sie auf **SQL-Server**.
2.	Klicken Sie auf dem Server mit der ursprünglichen (Quell)-Datenbank, die Sie gerade exportiert haben.
3.	Klicken Sie im Blatt „SQL Server“ auf **Import/Export-Verlauf**:

    ![Import/Export-Verlauf][3] ![Import/Export-Verlauf][4]

## Sicherstellen, dass sich die BACPAC-Datei im Speichercontainer befindet

2.	Klicken Sie auf **ALLE DURCHSUCHEN**.
3.	Klicken Sie auf **Speicherkonten (klassisch)**.
2.	Klicken Sie auf das Speicherkonto, in dem Sie die BACPAC-Datei gespeichert haben.
3.	Klicken Sie auf **Container**, und wählen Sie den Container, in den Sie die Datenbank exportiert haben, um Details zur Sicherung anzuzeigen (Sie können die BACPAC-Datei hier herunterladen und speichern).

    ![Details der BACPAC-Datei][5]


## Nächste Schritte

- [Importieren einer Azure SQL-Datenbank](sql-database-import.md)



## Zusätzliche Ressourcen

- [Übersicht über die Geschäftskontinuität](sql-database-business-continuity.md)
- [Warnungen zur Notfallwiederherstellung](sql-database-disaster-recovery-drills.md)
- [SQL-Datenbankdokumentation](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-history.png
[4]: ./media/sql-database-export/export-status.png
[5]: ./media/sql-database-export/bacpac-details.png

<!---HONumber=Sept15_HO2-->