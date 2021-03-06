<properties
	pageTitle="Rollenbasierte Zugriffssteuerung in Azure Active Directory | Microsoft Azure"
	description="In diesem Artikel wird die rollenbasierte Zugriffssteuerung in Azure beschrieben."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="02/10/2016"
	ms.author="kgremban"/>

# Rollenbasierte Access Control in Azure

## Rollenbasierte Access Control
Die rollenbasierte Access Control in Azure (RBAC) ermöglicht eine präzise Zugriffsverwaltung für Azure. Mithilfe von RBAC können Sie Aufgaben in Ihrem DevOps-Team verteilen und Benutzern nur den Zugriff gewähren, den sie zur Ausführung ihrer Aufgaben benötigen.

### Grundlagen zur Zugriffsverwaltung in Azure
Jedes Azure-Abonnement ist mit einem Azure Active Directory verknüpft. Nur Benutzern, Gruppen und Anwendungen aus diesem Verzeichnis kann Verwaltungszugriff auf Ressourcen im zugehörigen Azure-Abonnement gewährt werden. Die Verwaltung kann über das Azure-Portal, Azure-Befehlszeilentools und Azure-Verwaltungs-APIs erfolgen.

Der Zugriff wird gewährt, indem Benutzern, Gruppen und Anwendungen die jeweils geeignete RBAC-Rolle auf der richtigen Ebene zugewiesen wird. Um Zugriff auf das gesamten Abonnement zu gewähren, weisen Sie eine Rolle auf Abonnementebene zu. Um Zugriff auf eine bestimmte Ressourcengruppe innerhalb eines Abonnements zu gewähren, weisen Sie eine Rolle auf Ressourcengruppenebene zu. Sie können Rollen auch für bestimmte Ressourcen zuweisen, wie z. B.für Websites, virtuelle Computer und Subnetze, um nur Zugriff auf eine Ressource zu gewähren.

![Azure Active Directory-Ressourcen und Tools für die Ressourcenverwaltung – Diagramm](./media/role-based-access-control-configure/overview.png)

Die RBAC-Rolle, die Sie Benutzern, Gruppen und Anwendungen zuweisen, legt fest, welche Ressourcen im jeweiligen Bereich verwaltet werden können.

### Integrierte RBAC-Rollen in Azure
Die rollenbasierte Access Control in Azure verfügt über drei grundlegende Rollen, die für alle Ressourcentypen gelten: Besitzer, Mitwirkender und Leser. Besitzer verfügen über vollständigen Zugriff auf alle Ressourcen, einschließlich des Rechts, den Zugriff an andere Personen zu delegieren. Mitwirkende können alle Arten von Azure-Ressourcen erstellen und verwalten, aber keinen anderen Personen Zugriff gewähren. Leser können nur vorhandene Azure-Ressourcen anzeigen. Die verbleibenden RBAC-Rollen in Azure ermöglichen die Verwaltung von bestimmten Azure-Ressourcen. Beispielsweise ermöglicht die Rolle "Mitwirkender für virtuelle Computer" die Erstellung und Verwaltung von virtuellen Computern, nicht jedoch des virtuellen Netzwerks oder des Subnetzes, mit dem der virtuelle Computer eine Verbindung herstellt.

Unter [Integrierte RBAC-Rollen](role-based-access-built-in-roles.md) werden die in Azure verfügbaren integrierten RBAC-Rollen aufgeführt. Für jede Rolle werden die Vorgänge angegeben, für die eine integrierte Rolle den Zugriff gewährt.

### Ressourcenhierarchie und Zugriffsvererbung in Azure
Jedes Abonnement in Azure gehört zu einem einzigen Verzeichnis, jede Ressourcengruppe gehört zu einem einzigen Abonnement, und jede Ressource gehört zu einer einzigen Ressourcengruppe. Der Zugriff, den Sie auf übergeordneter Ebene gewähren, wird auf untergeordneter Ebene geerbt. Wenn Sie einer Azure AD-Gruppe die Rolle „Leser“ auf Abonnementebene zuweisen, können die Mitglieder dieser Rolle alle Ressourcengruppen und alle Ressourcen in diesem Abonnement anzeigen. Wenn Sie einer Anwendung auf Ressourcengruppenebene die Rolle "Mitwirkender" zuweisen, kann diese Anwendung alle Ressourcentypen in dieser Ressourcengruppe verwalten. Sie kann jedoch keine anderen Ressourcengruppen im Abonnement verwalten.

### Azure RBAC im Vergleich zu klassischen Administratoren und Co-Admins für Abonnements
Klassische Administratoren und Co-Admins für Abonnements verfügen über vollständigen Zugriff auf das Azure-Abonnement. Sie können Ressourcen über das [Azure-Portal](https://portal.azure.com) mit Azure-Ressourcen-Manager-APIs oder über das [klassische Azure-Portal](https://manage.windowsazure.com) und Azure Service Management-APIs verwalten. Im RBAC-Modell wird klassischen Administratoren die Besitzerrolle auf Abonnementebene zugewiesen.

Das präzisere Autorisierungsmodell (Azure RBAC) wird nur vom neuen Azure-Portal und den neuen Azure-Ressourcen-Manager-APIs unterstützt. Benutzer und Anwendungen, denen eine RBAC-Rolle zugewiesen ist (auf Abonnement-/Ressourcengruppen-/Ressourcenebene), können das klassische Verwaltungsportal und die Azure Service Management-APIs nicht verwenden.

### Autorisierung für Verwaltungsvorgänge im Vergleich zu Datenvorgängen
Das präzisere Autorisierungsmodell (Azure RBAC) wird nur für Verwaltungsvorgänge für die Azure-Ressourcen im Azure-Portal und in den Azure-Ressourcen-Manager-APIs unterstützt. Über RBAC können nicht alle Vorgänge auf Datenebene für Azure-Ressourcen autorisiert werden. Während das Erstellen/Lesen/Aktualisieren/Löschen von Speicherkonten über RBAC gesteuert werden kann, lassen sich dieselben Vorgänge für Blobs oder Tabellen in einem Speicherkonto noch nicht über RBAC steuern. Ebenso kann das Erstellen/Lesen/Aktualisieren/Löschen einer SQL-Datenbank über RBAC gesteuert werden, während sich dieselben Vorgänge noch nicht für SQL-Tabellen in einer Datenbank über RBAC steuern lassen.

## Verwalten des Zugriffs über das Azure-Portal
### Zugriff anzeigen
Wählen Sie die Zugriffseinstellungen auf dem Blatt **Ressourcengruppe** im Abschnitt mit den grundlegenden Informationen aus. Auf dem Blatt **Benutzer** werden alle Benutzer, Gruppen und Anwendungen aufgeführt, denen Zugriff auf die Ressourcengruppe gewährt wurde. Der Zugriff wird entweder in der Ressourcengruppe zugewiesen oder aus einer Zuweisung im übergeordneten Abonnement geerbt.

![Blatt „Benutzer“ – geerbter und zugewiesener Zugriff – Screenshot](./media/role-based-access-control-configure/view-access.png)

> [AZURE.NOTE] Klassische Administratoren und Co-Admins für Abonnements sind im neuen RBAC-Modell de facto Besitzer des Abonnements.


### Zugriff hinzufügen
1. Klicken Sie auf dem Blatt **Benutzer** auf das Symbol **Hinzufügen**. ![Blatt „Zugriff hinzufügen“ – Rolle auswählen – Screenshot](./media/role-based-access-control-configure/grant-access1.png)
2. Wählen Sie die Rolle aus, die Sie zuweisen möchten.
3. Suchen Sie nach dem Benutzer, der Gruppe oder der Anwendung, dem bzw. der Sie Zugriff gewähren möchten, und wählen Sie das entsprechende Element aus.
4. Durchsuchen Sie das Verzeichnis anhand von Anzeigenamen, E-Mail-Adressen und Objektbezeichnern nach Benutzern, Gruppen und Anwendungen. ![Blatt „Benutzer hinzufügen“ – Durchsuchen – Screenshot](./media/role-based-access-control-configure/grant-access2.png)

### Zugriff entfernen
1. Wählen Sie auf dem Blatt **Benutzer** die Rollenzuweisung aus, die Sie entfernen möchten.
2. Klicken Sie auf dem Blatt mit den Zuweisungsdetails auf das Symbol **Entfernen**.
3. Klicken Sie auf **Ja**, um die Entfernung zu bestätigen.

![Blatt „Benutzer“ – Aus Rolle entfernen – Screenshot](./media/role-based-access-control-configure/remove-access1.png)


> [AZURE.NOTE] Geerbte Zuweisungen können auf untergeordneter Ebene nicht entfernt werden. Wechseln Sie auf die übergeordnete Ebene, und entfernen Sie die entsprechenden Zuweisungen.

![Blatt „Benutzer“ – Schaltfläche „Entfernen“ bei geerbtem Zugriff deaktiviert – Screenshot](./media/role-based-access-control-configure/remove-access2.png)

## Verwalten des Zugriffs mit Azure PowerShell
Der Zugriff kann mithilfe von Azure RBAC-Befehlen in den Azure PowerShell-Tools verwaltet werden.

-	Verwenden Sie `Get-AzureRmRoleDefinition` zum Auflisten von RBAC-Rollen für die Zuweisung und zum Überprüfen der Vorgänge, auf die sie Zugriff gewähren.

-	Verwenden Sie `Get-AzureRmRoleAssignment` zum Auflisten der RBAC-Zugriffszuweisungen, die im angegebenen Abonnement, der Ressourcengruppe oder der Ressource gelten. Verwenden Sie `ExpandPrincipalGroups` zum Auflisten der Zugriffszuweisungen zum angegebenen Benutzer sowie zu den Gruppen, denen der Benutzer angehört. Verwenden Sie den `IncludeClassicAdministrators`-Parameter, um auch die klassischen Administratoren und Co-Admins für Abonnements aufzulisten.

-	Verwenden Sie `New-AzureRmRoleAssignment` zum Gewähren des Zugriffs für Benutzer, Gruppen und Anwendungen.

-	Verwenden Sie `Remove-AzureRmRoleAssignment` zum Entfernen des Zugriffs.

Detaillierte Beispiele zur Verwaltung des Zugriffs mithilfe von Azure PowerShell finden Sie unter [Verwalten des Zugriffs mit Azure PowerShell](role-based-access-control-manage-access-powershell.md).

## Verwalten des Zugriffs über die Azure-Befehlszeilenschnittstelle
Der Zugriff kann mithilfe von Azure RBAC-Befehlen in der Azure-Befehlszeilenschnittstelle verwaltet werden.

-	Verwenden Sie `azure role list` zum Auflisten der für die Zuweisung verfügbaren RBAC-Rollen. Verwenden Sie `azure role show` zum Überprüfen der Vorgänge, auf die die Rollen Zugriff gewähren.

-	Verwenden Sie `azure role assignment list` zum Auflisten der RBAC-Zugriffszuweisungen, die im angegebenen Abonnement, der Ressourcengruppe oder der Ressource gelten. Verwenden Sie die `expandPrincipalGroups`-Option zum Auflisten der Zugriffszuweisungen zum angegebenen Benutzer sowie zu den Gruppen, denen der Benutzer angehört. Verwenden Sie den `includeClassicAdministrators`-Parameter, um auch die klassischen Administratoren und Co-Admins für Abonnements aufzulisten.

-	Verwenden Sie `azure role assignment create` zum Gewähren des Zugriffs für Benutzer, Gruppen und Anwendungen.

-	Verwenden Sie `azure role assignment delete` zum Entfernen des Zugriffs.

Detaillierte Beispiele zur Verwaltung des Zugriffs mithilfe der Azure-Befehlszeilenschnittstelle finden Sie unter [Verwalten des Zugriffs über die Azure-Befehlszeilenschnittstelle](role-based-access-control-manage-access-azure-cli.md).

## Verwalten des Zugriffs mithilfe der REST-API
Ausführlichere Beispiele für die Verwaltung des Zugriffs mit der REST-API finden Sie unter [Verwalten der rollenbasierten Zugriffssteuerung mit der REST-API](role-based-access-control-manage-access-rest.md).

## Verwenden des Verlaufsberichts zu Zugriffsänderungen
Alle Zugriffsänderungen, die in Ihrem Azure-Abonnement vorgenommen werden, werden in Azure-Ereignissen protokolliert.

### Erstellen eines Berichts mit Azure PowerShell
Verwenden Sie folgenden PowerShell-Befehl, um einen Bericht mit Informationen darüber zu erstellen, wer in Ihren Azure-Abonnements welche Art von Zugriff auf welcher Ebene gewährt oder widerrufen hat:

    `Get-AzureRMAuthorizationChangeLog`

### Erstellen eines Berichts über die Azure-Befehlszeilenschnittstelle
Verwenden Sie folgenden Befehl in der Azure-Befehlszeilenschnittstelle, um einen Bericht mit Informationen darüber zu erstellen, wer in Ihren Azure-Abonnements welche Art von Zugriff auf welcher Ebene gewährt oder widerrufen hat:

    `azure authorization changelog`

> [AZURE.NOTE] Zugriffsänderungen können für die letzten 90 Tage abgefragt werden (in Zeiträumen von jeweils 15 Tagen).

Unten sind alle Zugriffsänderungen im Abonnement für die letzten 7 Tage aufgeführt.

![PowerShell Get-AzureRMAuthorizationChangeLog – Screenshot](./media/role-based-access-control-configure/access-change-history.png)

### Exportieren der Zugriffsänderungen in eine Kalkulationstabelle
Zugriffsänderungen lassen sich zum einfacheren Überprüfen in eine Kalkulationstabelle exportieren.

![Änderungsprotokoll als Arbeitsblatt – Screenshot](./media/role-based-access-control-configure/change-history-spreadsheet.png)

## Benutzerdefinierte Rollen in Azure RBAC
Erstellen Sie eine benutzerdefinierte Rolle in Azure RBAC, falls keine der integrierten Rollen Ihre speziellen Zugriffsanforderungen erfüllt. Benutzerdefinierte Rollen können mit RBAC-Befehlszeilentools in Azure PowerShell und in der Azure-Befehlszeilenschnittstelle erstellt werden. Wie integrierte Rollen auch, können benutzerdefinierte Rollen Benutzern, Gruppen und Anwendungen auf Abonnement-, Ressourcengruppen- und Ressourcenebene zugewiesen werden.

Es folgt ein Beispiel für die Definition einer benutzerdefinierten Rolle, die das Überwachen und Neustarten virtueller Maschinen ermöglicht:

```
{
  "Name": "Virtual Machine Operator",
  "Id": "cadb4a5a-4e7a-47be-84db-05cad13b6769",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624",
    "/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2"
  ]
}
```
### Actions
Mit der Actions-Eigenschaft einer benutzerdefinierten Rolle werden die Azure-Vorgänge angegeben, auf die mit der Rolle Zugriff gewährt wird. Es handelt sich um eine Sammlung von Vorgangszeichenfolgen, mit denen sicherungsfähige Vorgänge von Azure-Ressourcenanbietern identifiziert werden. Vorgangszeichenfolgen mit Platzhaltern (*) gewähren Zugriff auf alle Vorgänge, die mit der Vorgangszeichenfolge übereinstimmen. Beispiel:

-	`*/read` gewährt Zugriff auf Lesevorgänge für alle Ressourcentypen aller Azure-Ressourcenanbieter.
-	`Microsoft.Network/*/read` gewährt Zugriff auf Lesevorgänge für alle Ressourcentypen im Microsoft.Network-Ressourcenanbieter von Azure.
-	`Microsoft.Compute/virtualMachines/*` gewährt Zugriff auf alle Vorgänge virtueller Maschinen und die dazugehörigen untergeordneten Ressourcentypen.
-	`Microsoft.Web/sites/restart/Action` gewährt Zugriff zum Neustarten von Websites.

Verwenden Sie den Befehl `Get-AzureRmProviderOperation` oder `azure provider operations show`, um die Vorgänge von Azure-Ressourcenanbietern aufzulisten. Sie können diese Befehle auch verwenden, um zu überprüfen, ob eine Vorgangszeichenfolge gültig ist, und um Zeichenfolgen für Platzhaltervorgänge zu erweitern.

![PowerShell Get-AzureRMProviderOperation – Screenshot](./media/role-based-access-control-configure/1-get-azurermprovideroperation-1.png)

![Azure-Befehlszeilenschnittstelle, Azure-Anbietervorgänge anzeigen – Screenshot](./media/role-based-access-control-configure/1-azure-provider-operations-show.png)

### NotActions
Verwenden Sie die **NotActions**-Eigenschaft einer benutzerdefinierten Rolle im folgenden Fall: Wenn sich die Gruppe der zulassenden Vorgänge leicht ausdrücken lässt, indem Sie bestimmte Vorgänge ausschließen, anstatt alle Vorgänge bis auf die Vorgänge einzuschließen, die ausgeschlossen werden sollen. Der effektive Zugriff, der von einer benutzerdefinierten Rolle gewährt wird, wird berechnet, indem die **NotActions**-Vorgänge von den **Actions**-Vorgängen ausgeschlossen werden.

> [AZURE.NOTE] Wenn einem Benutzer eine Rolle zugewiesen wird, mit der ein Vorgang in **NotActions** ausgeschlossen wird, und dem Benutzer dann durch Zuweisen einer zweiten Rolle der Zugriff auf denselben Vorgang gewährt wird, kann der Benutzer den Vorgang durchführen. **NotActions** ist keine Verweigerungsregel. Es ist lediglich eine bequeme Möglichkeit, eine Gruppe zulässiger Vorgänge zu erstellen, wenn bestimmte Vorgänge ausgeschlossen werden müssen.

### AssignableScopes
Mit der **AssignableScopes**-Eigenschaft der benutzerdefinierten Rolle werden die Bereiche (Abonnements, Ressourcengruppen oder Ressourcen) angegeben, in denen die benutzerdefinierte Rolle für die Zuweisung zu Benutzern, Gruppen und Anwendungen verfügbar ist. Mit **AssignableScopes** können Sie die benutzerdefinierte Rolle für die Zuweisung nur in den Abonnements oder Ressourcengruppen verfügbar machen, für die dies erforderlich ist. So wird verhindert, dass die Benutzeroberfläche für die restlichen Abonnements oder Ressourcengruppen unnötig belastet wird.

> [AZURE.NOTE] Sie müssen mindestens ein Abonnement, eine Ressourcengruppe oder eine Ressourcen-ID verwenden.

Mit **AssignableScopes** einer benutzerdefinierten Rolle wird außerdem gesteuert, wer die Rolle anzeigen, aktualisieren und löschen kann. Im Folgenden sind einige gültige zuweisbare Bereiche aufgeführt:

-	„/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e“, „/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624“: Macht die Rolle für die Zuweisung in zwei Abonnements verfügbar.
-	„/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e“: Macht die Rolle für die Zuweisung in einem einzelnen Abonnement verfügbar.
-	„/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network“: Macht die Rolle für die alleinige Zuweisung in der Netzwerkressourcengruppe verfügbar.

### Zugriffssteuerung für benutzerdefinierte Rollen
Mit der **AssignableScopes**-Eigenschaft der benutzerdefinierten Rolle wird festgelegt, wer die Rolle anzeigen, ändern und löschen kann.

**Wer kann eine benutzerdefinierte Rolle erstellen?** Die Erstellung der benutzerdefinierten Rolle ist nur erfolgreich, wenn der Benutzer, der die Rolle erstellt, eine benutzerdefinierte Rolle für alle angegebenen **AssignableScopes**-Bereiche erstellen darf. Die Erstellung der benutzerdefinierten Rolle ist nur erfolgreich, wenn der Benutzer, der die Rolle erstellt, `Microsoft.Authorization/roleDefinition/write operation` für alle **AssignableScopes**-Bereiche der Rolle durchführen kann. Besitzer (und Benutzerzugriff-Administratoren) von Abonnements, Ressourcengruppen und Ressourcen können also benutzerdefinierte Rollen für die Verwendung in diesen Bereichen erstellen.

**Wer kann eine benutzerdefinierte Rolle ändern?** Benutzer, die berechtigt sind, benutzerdefinierte Rollen für alle **AssignableScopes**-Bereiche einer Rolle zu aktualisieren, können diese benutzerdefinierte Rolle ändern. Benutzer, die den Vorgang `Microsoft.Authorization/roleDefinition/write` für alle **AssignableScopes**-Bereiche einer benutzerdefinierten Rolle durchführen können, können diese benutzerdefinierte Rolle ändern. Wenn eine benutzerdefinierte Rolle beispielsweise in zwei Azure-Abonnements zuweisbar ist (also in der **AssignableScopes**-Eigenschaft über zwei Abonnements verfügt), gilt Folgendes: Ein Benutzer muss Besitzer (oder Benutzerzugriff-Administrator) beider Abonnements sein, um die benutzerdefinierte Rolle ändern zu können.

**Wer kann benutzerdefinierte Rollen anzeigen, die für die Zuweisung in einem Bereich verfügbar sind?** Benutzer, die den Vorgang `Microsoft.Authorization/roleDefinition/read` für einen Bereich durchführen können, können die RBAC-Rollen anzeigen, die für die Zuweisung in diesem Bereich verfügbar sind. Alle integrierten Rollen in Azure RBAC ermöglichen das Anzeigen von Rollen, die für die Zuweisung verfügbar sind.

<!---HONumber=AcomDC_0211_2016-->