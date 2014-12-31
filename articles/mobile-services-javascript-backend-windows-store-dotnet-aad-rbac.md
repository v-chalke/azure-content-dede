﻿<properties urlDisplayName="Role Based Access Control with Azure Active Directory" pageTitle="Rollenbasierte Zugriffssteuerung in Mobile Services und Azure Active Directory (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to control access based on Azure Active Directory roles in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Role Based Access Control in Mobile Services and Azure Active Directory" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/29/2014" ms.author="wesmc" />

# Rollenbasierte Zugriffssteuerung in Mobile Services und Azure Active Directory

[WACOM.INCLUDE [mobile-services-selector-rbac](../includes/mobile-services-selector-rbac.md)]


Rollenbasierte Zugriffssteuerung (RBAC) besteht im Zuweisen von Berechtigungen für Rollen, die Benutzer innehaben können, um Beschränkungen für die Möglichkeiten bestimmter Benutzer zu definieren. In diesem Lernprogramm erfahren Sie, wie Sie grundlegende RBAC für Azure Mobile Services hinzufügen können.

In diesem Lernprogramm wird die rollenbasierte Zugriffssteuerung erläutert, wobei die Mitgliedschaft der einzelnen Benutzer in der Gruppe "Sales" geprüft wird, die in Azure Active Directory (AAD) definiert ist. Die Zugriffsprüfung erfolgt mit JavaScript im Mobile Services-Backe-Ed mithilfe der [Graph-API] für Azure Active Directory. Nur Benutzer, die der Rolle "Sales" angehören, dürfen Daten abfragen.


>[WACOM.NOTE] Dieses Lernprogramm ist dazu vorgesehen, Ihre Kenntnisse bezüglich der Authentifizierung um Autorisierungsmethoden zu erweitern. Es wird davon ausgegangen, dass Sie das Lernprogramm [Erste Schritte mit der Authentifizierung] unter Verwendung des Azure Active Directory-Authentifizierungsanbieters abgeschlossen haben. In diesem Lernprogramm wird die Anwendung TodoItem weiter aktualisiert, die im Lernprogramm [Erste Schritte mit der Authentifizierung] verwendet wurde.

In diesem Lernprogramm werden die folgenden Schritte behandelt:

1. [Erstellen der Gruppe "Sales" mit Mitgliedschaften]
2. [Erzeugen eines Schlüssels für die integrierte Anwendung]
3. [Hinzufügen eines gemeinsam genutzten Skripts zur Prüfung der Mitgliedschaft] 
4. [Hinzufügen einer rollenbasierten Zugriffsprüfung für Datenbankvorgänge]
5. [Testen des Clientzugriffs]

Für dieses Lernprogramm ist Folgendes erforderlich:

* Visual Studio 2013 für Windows 8.1
* Abschluss des Lernprogramms [Erste Schritte mit der Authentifizierung] unter Verwendung des Azure Active Directory-Authentifizierungsanbieters
* Abschluss des Lernprogramms [Speichern von Serverskripts] zur Verwendung eines Git-Repositorys zum Speichern von Serverskripts.
 


## <a name="create-group"></a>Erstellen der Gruppe "Sales" mit Mitgliedschaften

[WACOM.INCLUDE [mobile-services-aad-rbac-create-sales-group](../includes/mobile-services-aad-rbac-create-sales-group.md)]


## <a name="generate-key"></a>Erzeugen eines Schlüssels für die integrierte Anwendung


Im Lernprogramm [Erste Schritte mit der Authentifizierung] haben Sie eine Registrierung für die integrierte Anwendung erstellt, als Sie den Schritt [Registrieren für die Verwendung einer Azure Active Directory-Anmeldung] ausgeführt haben. In diesem Abschnitt erzeugen Sie einen Schlüssel, der beim Lesen der Verzeichnisinformationen mit der Client-ID der integrierten Anwendung verwendet wird. 

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]





## <a name="add-shared-script"></a>Hinzufügen eines gemeinsam genutzten Skripts zum mobilen Dienst zur Prüfung der Mitgliedschaft

In diesem Abschnitt verwenden Sie Git, um eine gemeinsam genutzte Skriptdatei namens *rbac.js* zu Ihrem mobilen Dienst hinzuzufügen. Diese gemeinsam genutzte Skriptdatei enthält die Funktionen, welche die [Graph-API] verwenden, um die Gruppenmitgliedschaft des Benutzers zu prüfen. 

Wenn Sie mit der Bereitstellung von Skripts mit Git für Ihren mobilen Dienst noch nicht vertraut sind, verwenden Sie das Lernprogramm [Speichern von Serverskripts], bevor Sie diesen Abschnitt abschließen.

1. Erstellen Sie eine neue Skriptdatei namens *rbac.js* im Verzeichnis *./service/shared/* des lokalen Repositorys für Ihren mobilen Dienst.
2. Fügen Sie das folgende Skript am Anfang der Datei ein, welches die Funktion `getAADToken` definiert. Anhand der *Mandantendomäne*, der *Client-ID* der integrierten Anwendung sowie des *Anwendungsschlüssels* stellt diese Funktion ein Graph-Zugriffstoken bereit, das zum Lesen der Verzeichnisinformationen verwendet wird.

    >[WACOM.NOTE] Sie sollten den Token zwischenspeichern, statt für jede Zugriffsprüfung einen neuen Token zu erstellen. Aktualisieren Sie den Zwischenspeicher, wenn bei der Verwendung des Tokens ein Authentication_ExpiredToken-Fehler 401 ausgegeben wird, wie in der [Graph-API-Fehlerreferenz] beschrieben ist. Der Einfachheit halber wird dies im Code unten nicht dargestellt, aber dadurch wird zusätzlicher Netzwerkverkehr mit Active Directory verringert. 

        var appSettings = require('mobileservice-config').appSettings;
        var tenant_domain = appSettings.AAD_TENANT_DOMAIN;
        var clientID = appSettings.AAD_CLIENT_ID;
        var key = appSettings.AAD_CLIENT_KEY;
        exports.SalesGroup = appSettings.AAD_GROUP_ID;
         
        function getAADToken(callback) {
            var req = require("request");
            var options = {
                url: "https://login.windows.net/" + tenant_domain + "/oauth2/token?api-version=1.0",
                method: 'POST',
                form: {
                    grant_type: "client_credentials",
                    resource: "https://graph.windows.net",
                    client_id: clientID,
                    client_secret: key
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).access_token);
                });
        }


3. Fügen Sie den folgenden Code zu *rbac.js* hinzu, um die Funktion `getGroupId` zu definieren. Diese Funktion verwendet das Zugriffstoken, um die Gruppen-ID basierend auf dem Gruppennamen abzurufen, der in einem Filter verwendet wird.
 
    >[WACOM.NOTE] Dieser Code schlägt die Active Directory-Gruppe nach dem Namen nach. In vielen Fällen ist es empfehlenswert, die Gruppen-ID als App-Einstellung des mobilen Diensts zu speichern und nur diese Gruppen-ID zu verwenden. Der Gruppenname kann sich ändern, die ID bleibt dagegen immer gleich. Mit der Änderung des Gruppennamens ist jedoch meist eine Änderung des Rollenumfangs verbunden, die auch eine Aktualisierung des Codes des mobilen Diensts erforderlich machen kann.   

        function getGroupId(groupname, accessToken, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/groups" + 
                      "?$filter=displayName%20eq%20'" + groupname + "'" + 
	              "&api-version=2013-04-05" ,
                method: 'GET',
                headers: {
                    "Authorization": "Bearer " + accessToken,
                    "Content-Type": "application/json",
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).value[0].objectId);
            });
        }


4. Fügen Sie den folgenden Code zu *rbac.js* hinzu, um die Funktion `isMemberOf` zu definieren, welche den [IsMemberOf]-Endpunkt der Graph-REST-API aufruft.

    Diese Funktion ist ein einfacher Wrapper für den [IsMemberOf]-Endpunkt der Graph-REST-API. Sie verwendet das Graph-Zugriffstoken, um basierend auf der Gruppen-ID zu prüfen, ob die Verzeichnisobjekt-ID des Benutzers Mitglied der Gruppe ist.

        function isMemberOf(access_token, userObjectId, groupObjectId, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/isMemberOf" + "?api-version=2013-04-05",
                method: 'POST',
                headers: {
                    "Authorization": "Bearer " + access_token,
                    "Content-Type": "application/json",
                },
                body: JSON.stringify({
                "groupId": groupObjectId,
                "memberId": userObjectId
                })
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).value);
            });
        };

    

7. Fügen Sie die folgende exportierte Funktion `checkGroupMembership` zu *rbac.js* hinzu.  

    Diese Funktion fasst die Verwendung der anderen Skriptfunktionen ein und wird aus dem freigegebenen Skript exportiert, sodass sie von anderen Skripts zur Durchführung der tatsächlichen Zugriffsprüfung aufgerufen werden kann. Anhand des Benutzerobjekts des mobilen Diensts und der Gruppen-ID ruft das Skript die Objekt-ID aus Azure Active Directory für die Benutzer-ID ab und überprüft die Mitgliedschaft in der Gruppe.

        exports.checkGroupMembership = function (user, groupName, callback) {
            user.getIdentities({
                success: function (identities) {
                    var objectId = identities.aad.oid;
                    getAADToken(function (err, access_token) {
                        if (err) callback(err);
		        else getGroupId(groupName, access_token, function (err, groupId) { 
                            if (err) callback(err);
                            else isMemberOf(access_token, objectId, groupId, function (err, isInGroup) {
       	                        if (err) errorHandler(err);
               	                else callback(null, isInGroup);
                            });
                        });
                    });
                },
                error: callback
            });
        }

8. Speichern Sie die Änderungen an *rbac.js*.

## <a name="add-access-checking"></a>Hinzufügen einer rollenbasierten Zugriffsprüfung für Datenbankvorgänge


Als Sie das Lernprogramm [Erste Schritte mit der Authentifizierung] abgeschlossen haben, sollten Sie bereits die Tabellenvorgänge zur Verwendung von Authentifizierung eingerichtet haben, wie unten dargestellt.

![][3]

Für jeden Datenbankvorgang, der Authentifizierung erfordert, können wir Skripts hinzufügen, die das Benutzerobjekt für die Zugriffsprüfung verwenden.

Die folgenden Schritte zeigen, wie Sie rollenbasierte Zugriffssteuerung mithilfe von Skripts zu jedem Tabellenvorgang im mobilen Dienst hinzufügen. Für jeden Tabellenvorgang wird ein Skript ausgeführt, welches das freigegebene Skript *rbac.js* verwendet.

1. Erstellen Sie eine neue Skriptdatei namens *todoitem.insert.js* im Verzeichnis *./service/table/* des lokalen Git-Repositorys für Ihren mobilen Dienst. Fügen Sie das folgende Skript in diese Datei ein:

        function insert(item, user, request) {
        
            var RBAC = require('../shared/rbac.js');
        
            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

2. Erstellen Sie eine neue Skriptdatei namens *todoitem.read.js* im Verzeichnis *./service/table/* des lokalen Git-Repositorys für Ihren mobilen Dienst. Fügen Sie das folgende Skript in diese Datei ein:

        function read(query, user, request) {
        
            var RBAC = require('../shared/rbac.js');
        
            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

3. Erstellen Sie eine neue Skriptdatei namens *todoitem.update.js* im Verzeichnis *./service/table/* des lokalen Git-Repositorys für Ihren mobilen Dienst. Fügen Sie das folgende Skript in diese Datei ein:

        function update(item, user, request) {
        
            var RBAC = require('../shared/rbac.js');
        
            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

4. Erstellen Sie eine neue Skriptdatei namens *todoitem.delete.js* im Verzeichnis *./service/table/* des lokalen Git-Repositorys für Ihren mobilen Dienst. Fügen Sie das folgende Skript in diese Datei ein:

        function del(id, user, request) {
        
            var RBAC = require('../shared/rbac.js');
        
            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

5. Fügen Sie an der Befehlszeilenschnittstelle für Ihr Git-Repository die Verfolgung der hinzugefügten Skriptdateien mit dem folgenden Befehl hinzu:

        git add .

6. Schreiben Sie an der Befehlszeilenschnittstelle für Ihr Git-Repository die Aktualisierungen mit dem folgenden Befehl fest:

        git commit -m "Added role based access control to table operations"
  
7. Stellen Sie an der Befehlszeilenschnittstelle für Ihr Git-Repository die Aktualisierungen für Ihr lokales Git-Repository mit dem folgenden Befehl für den mobilen Dienst bereit. Bei diesem Befehl wird davon ausgegangen, dass Sie die Aktualisierungen in der Verzweigung *master* vorgenommen haben.

        git push origin master

8. Sie sollten im [Azure-Verwaltungsportal] nun in der Lage sein, zu den Tabellenvorgängen für den mobilen Dienst zu navigieren und die Aktualisierungen dort anzuzeigen, wie unten dargestellt. 

    ![][4]

## <a name="test-client"></a>Testen des Clientzugriffs

[WACOM.INCLUDE [mobile-services-aad-rbac-test-app](../includes/mobile-services-aad-rbac-test-app.md)]





<!-- Anchors. -->
[Erstellen der Gruppe "Sales" mit Mitgliedschaften]: #create-group
[Erzeugen eines Schlüssels für die integrierte Anwendung]: #generate-key
[Hinzufügen eines gemeinsam genutzten Skripts zur Prüfung der Mitgliedschaft]: #add-shared-script
[Hinzufügen einer rollenbasierten Zugriffsprüfung für Datenbankvorgänge]: #add-access-checking
[Testen des Clientzugriffs]: #test-client


<!-- Images -->
[0]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/users.png
[1]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/group-membership.png
[2]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/sales-group.png
[3]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/table-perms.png
[4]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/insert-table-op-view.png
[5]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/sales-group-id.png
[6]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/client-id-and-key.png

<!-- URLs. -->
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[So registrieren Sie sich beim Azure Active Directory]: /de-de/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Verzeichnissynchronisierungsszenarien]: http://msdn.microsoft.com/library/azure/jj573653.aspx
[Speichern von Serverskripts]: /de-de/documentation/articles/mobile-services-store-scripts-source-control/
[Registrieren für die Verwendung einer Azure Active Directory-Anmeldung]: /de-de/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Graph-API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[Graph-API-Fehlerreferenz]: http://msdn.microsoft.com/de-de/library/azure/hh974480.aspx
[IsMemberOf]: http://msdn.microsoft.com/de-de/library/azure/dn151601.aspx