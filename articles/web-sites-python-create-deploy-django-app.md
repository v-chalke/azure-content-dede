<properties linkid="develop-python-web-site-with-django" urlDisplayName="Web Sites with Django" pageTitle="Python Web Sites with Django - Azure tutorial" metaKeywords="Azure django, django website" description="A tutorial that introduces you to running a Python web site on Azure." metaCanonical="" services="web-sites" documentationCenter="Python" title="Creating Web Sites with Django" authors="" solutions="" manager="" editor="" />

Erstellen von Websites mit Django
=================================

In diesem Lernprogramm werden die ersten Schritte bei der Ausführung von Python auf Azure-Websites beschrieben. Azure-Websites bieten uneingeschränktes kostenloses Hosting und schnelle Bereitstellung, und jetzt können Sie auch noch Python verwenden! Wenn die Anwendung größer wird, können Sie zu kostenpflichtigem Hosting wechseln und außerdem alle anderen Azure-Dienste integrieren.

In diesem Lernprogramm erfahren Sie, wie Sie eine Anwendung, die mit dem Django Web-Framework erstellt wurde, bereitstellen. Das Lernprogramm führt Sie schrittweise durch die Bereitstellung der Anwendung und der erforderlichen Bibliotheken, einschließlich Django. Das alles erfolgt in einem Git-Repository, das schnelle und einfache Aktualisierungsübertragungen auf Ihre Website ermöglicht. Und schließlich konfigurieren Sie die neu erstellte Website über Azure, sodass sie in Ihrer Python-Anwendung ausgeführt werden kann.

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

In diesem Lernprogramm wird Python 2.7 und Django 1.4 verwendet. Sie können diese entweder selbst abrufen oder schnell und einfach über den Windows-Installationslink unter <http://www.windowsazure.com/de-de/develop/python/> installieren.

**Hinweis**: Auf Azure-Websites sind jetzt Python 2.7 und der wfastcgi-Handler vorinstalliert. Web-Frameworks wie Django sind jedoch nicht enthalten. Sie können auch einen anderen Python-Übersetzer verwenden. Dazu müssen Sie ihn nur ins Git-Repository übernehmen und die Website zur Verwendung dieses Übersetzers statt des vorinstallierten Python 2.7-Übersetzers konfigurieren.

Außerdem müssen Sie eine Bereitstellungsoption für die Übertragung der Website auf Azure installieren. Es gibt verschiedene Bereitstellungstools, aber in diesem Lernprogramm wird Git verwendet. Wir empfehlen die Verwendung von [msysgit](http://code.google.com/p/msysgit/).

**Hinweis**: TFS-Veröffentlichung wird derzeit für Python-Projekte nicht unterstützt.

Nachdem Python, Django und Git installiert sind, haben Sie alles, um beginnen zu können.

Website-Erstellung im Portal
----------------------------

Der erste Schritt beim Erstellen einer Anwendung ist das Erstellen der Website im Azure-Verwaltungsportal. Melden Sie sich dazu beim Portal an, und klicken Sie unten links auf die Schaltfläche NEW. Daraufhin wird ein Fenster angezeigt. Klicken Sie auf **Quick Create**, geben Sie eine URL ein, und wählen Sie **Create Web Site** aus.

![](./media/web-sites-python-create-deploy-django-app/django-ws-003.png)

Die Website wird umgehend eingerichtet. Nun fügen Sie Unterstützung für die Veröffentlichung mit Git hinzu. Dazu wählen Sie **Set up deployment from source control** aus.

![](./media/web-sites-python-create-deploy-django-app/django-ws-004.png)

Blättern Sie im Dialogfeld **Set up deployment** nach unten, und wählen Sie die Option **Local Git** aus. Klicken Sie auf den Pfeil nach rechts, um fortzufahren.

![](./media/web-sites-python-create-deploy-django-app/django-ws-005.png)

Nach dem Einrichten der Git-Veröffentlichung wird kurz eine Seite mit der Information zur Repository-Erstellung angezeigt. Sobald das Repository bereit ist, gelangen Sie zur Registerkarte "Deployments". Die Registerkarte "Deployments" enthält Anweisungen zur Verbindungserstellung.

![](./media/web-sites-python-create-deploy-django-app/django-ws-006.png)

Website-Bereitstellung
----------------------

Nachdem Sie nun das Git-Repository in Azure erstellt haben, füllen Sie es mit der Website von Ihrem lokalen Computer auf. Der erste Schritt ist das Klonen der vorhandenen leeren Site mit der angegebenen URL:

![](./media/web-sites-python-create-deploy-django-app/django-ws-007.png)

Nun sind Sie bereit, die Einbeziehung auf der Website einzurichten. Folgendes ist erforderlich:

1.  Einschließen der Django-Bibliothek und anderer Bibliotheken, die Sie zum Ausführen der Website verwenden
2.  Einschließen des Django-Anwendungscodes

Schließen Sie zuerst die Django-Bibliothek ein. Erstellen Sie dazu ein neues Verzeichnis namens "site-packages", und kopieren Sie die installierte Version von Django mit folgenden Befehlen dorthin:

    mkdir site-packages
    cd site-packages
    xcopy /s C:\Python27\lib\site-packages\* .

Diese Befehle kopieren alle in "site-packages" vorhandenen Bibliotheken, einschließlich Django. Wenn diese Bibliotheken nicht von Ihrer Website verwendet werden, können Sie sie auch entfernen.

![](./media/web-sites-python-create-deploy-django-app/django-ws-008.png)

Anschließend erstellen Sie die anfängliche Django-Anwendung. Dies erfolgt entweder wie jede andere Django-Anwendung über die Befehlszeile, oder Sie verwenden [Python-Tools für Visual Studio](http://pytools.codeplex.com/), um das Projekt zu erstellen. Beide Optionen werden hier vorgestellt.

**Option 1:** 
Führen Sie den folgenden Befehl aus, um das neue Projekt über die Befehlszeile zu erstellen. Der Befehl erstellt die Django-Anwendung im Ordner "DjangoApplication":

     C:\Python27\python.exe -m django.bin.django-admin startproject DjangoApplication

![](./media/web-sites-python-create-deploy-django-app/django-ws-010.png)

**Option 2:**
 Sie können die neue Website auch mit den Python-Tools für Visual Studio erstellen. Starten Sie Visual Studio mit den installierten Python-Tools für Visual Studio, und wählen Sie **File**-\>**New Project** aus. Zeigen Sie unter **Other Languages** Details zu den Python-Projekten an, und wählen Sie **Django Application** aus. Geben Sie **DjangoApplication** als Projektnamen an, und stellen Sie sicher, dass **Create directory for solution** deaktiviert ist, um genau dieselbe Verzeichnisstruktur zu erhalten, als wenn Sie eine Django-Anwendung über die Befehlszeile erstellen. Mit dieser Option erstellen Sie eine Visual Studio-Lösung und eine Projektdatei, die für eine hervorragende Benutzerfreundlichkeit bei der lokalen Entwicklung, einschließlich Vorlagen-Debugging und IntelliSense, sorgen.

![](./media/web-sites-python-create-deploy-django-app/django-ws-011.png)

Nun müssen Sie nur noch alle soeben hinzugefügten Dateien hinzufügen und die Website an Git übertragen. Führen Sie dazu folgende Befehle aus:

    git add DjangoApplication site-packages
    git commit -m "Initial site"
    git remote add azure https://dinov@pythonwebsite.scm.azurewebsites.net/PythonWebSite.git
    git push azure master

Der erste Befehl fügt die nicht verfolgten Dateien zur Nachverfolgung hinzu. Der zweite Befehl übernimmt die soeben hinzugefügten Dateien in das Repository. Der dritte Befehl fügt eine Remote-Site mit dem Namen *azure* zum Repository hinzu. Der letzte Befehl überträgt die Änderungen auf das Remote-Repository. Dieser letzte Befehl startet außerdem die Bereitstellung. Nachdem Sie dies alles ausgeführt haben, sollte das Ergebnis in etwa wie folgt aussehen:

![](./media/web-sites-python-create-deploy-django-app/django-ws-013.png)

Nach der Übertragung wird das Azure-Portal aktualisiert und die aktive Bereitstellung angezeigt:

![](./media/web-sites-python-create-deploy-django-app/django-ws-014.png)

Website-Konfiguration
---------------------

Nun müssen Sie die Website so konfigurieren, dass sie das Django-Projekt erkennt und den wfastcgi-Handler verwendet. Dazu klicken Sie auf die Registerkarte "Configure" oben auf dem Bildschirm und blättern dann zur unteren Hälfte der Seite, welche die Abschnitte **app settings** und **handler mappings** enthält.

Alle hier vorgenommenen Einstellungen werden während der eigentlichen Abfrage in Umgebungsvariablen umgewandelt. Das bedeutet, dass Sie dies verwenden können, um die Umgebungsvariable DJANGO\_SETTINGS\_MODULE oder auch PYTHONPATH und WSGI\_HANDLER zu konfigurieren. Falls Ihre Anwendung andere Konfigurationswerte aufweist, können Sie diese hier zuweisen und dann aus der Umgebung abrufen. Unter Umständen möchten Sie einen Pfad zu einer Datei auf der Website angeben, beispielsweise PYTHONPATH. Wenn Sie dies als Azure-Website ausführen, befindet sich die Website unter **D:\\home\\site\\wwwroot\**, daher können Sie dies überall verwenden, wo Sie einen vollständigen Pfad zu einer Datei auf der Festplatte angeben möchten.

Zum Einrichten einer Django-Anwendung müssen Sie drei Umgebungsvariablen erstellen. Die erste ist DJANGO\_SETTINGS\_MODULE, welche den Modulnamen der Django-Anwendung angibt, die für die Konfiguration verwendet wird. Die zweite ist die Umgebungsvariable PYTHONPATH, welche das Paket angibt, in dem sich das Einstellungsmodul befindet. Die dritte ist WSGI\_HANDLER. Diese Variable ist ein Modul-/Paketname, gefolgt vom zu verwendenden Attribut im Modul (z. B. mypackage.mymodule.handler). Fügen Sie Klammern hinzu, um anzugeben, dass das Attribut aufgerufen werden soll. Diese Variablen werden wie folgt angegeben:

    DJANGO_SETTINGS_MODULE    DjangoApplication.settings
    PYTHONPATH                D:\home\site\wwwroot\DjangoApplication;D:\home\site\wwwroot\site-packages
    WSGI_HANDLER              django.core.handlers.wsgi.WSGIHandler()

![](./media/web-sites-python-create-deploy-django-app/django-ws-015.png)

Nun müssen Sie die Handlerzuordnung konfigurieren. Registrieren Sie dazu den Handler für alle Erweiterungen mithilfe des Pfads für den Python-Übersetzer und des Pfads für das wfastcgi.py-Skript:

    EXTENSION                 *
    SCRIPT PROCESSOR PATH     D:\python27\python.exe
    ADDITIONAL ARGUMENTS      D:\python27\scripts\wfastcgi.py

![](./media/web-sites-python-create-deploy-django-app/django-ws-016.png)

An diesem Punkt können Sie unten auf die Schaltfläche **Save** klicken.

Abschließend wechseln Sie zurück zum Dashboard. Blättern Sie links unten zur **SITE URL**. Klicken Sie auf den Link, und öffnen Sie die neue Django-Website, die in etwa so aussieht:

![](./media/web-sites-python-create-deploy-django-app/django-ws-017.png)

Nächste Schritte
----------------

Sie können nun die Django-Anwendung mit den bereits verwendeten Tools weiterentwickeln. Falls Sie [Python-Tools für Visual Studio](http://pytools.codeplex.com/) für die Entwicklung verwenden, sollten Sie [VisualGit](http://code.google.com/p/visualgit/) installieren, um die Integration der Quellcodeverwaltung in Visual Studio zu ermöglichen.

Die Anwendung kann auch Abhängigkeiten über Python und Django hinaus aufweisen. Wenn Sie Python über das Installationsprogramm unter <http://www.windowsazure.com/de-de/develop/python/> installiert haben, ist PIP bereits installiert und Sie können damit schnell neue Abhängigkeiten hinzufügen. Um beispielsweise das Natural Language Toolkit und alle seine Abhängigkeiten zu installieren, geben Sie Folgendes ein:

    pip install nltk

Anschließend müssen Sie das Verzeichnis "site-packages" aktualisieren, indem Sie die Dateien unter "C:\\Python27\\Lib\\site-packages" in das lokale "site-packages"-Verzeichnis kopieren.

Nachdem Sie die Dateien kopiert haben, führen Sie den Befehl **git status** aus, um die neu hinzugefügten Dateien anzuzeigen, und **git add** gefolgt von **git commit**, um die Änderungen in das Repository zu übernehmen. Und schließlich können Sie noch **git push** ausführen, um die aktualisierte Website auf Azure bereitzustellen.

Nun können Sie "manage.py" im Verzeichnis "DjangoApplication" wie gewohnt verwenden, um neue Anwendungen zum Django-Projekt hinzuzufügen.
