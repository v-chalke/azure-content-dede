<properties linkid="develop-java-tutorials-web-site-custom_upload" urlDisplayName="Upload custom Java web site" pageTitle="Upload a custom Java web site to Azure" metaKeywords="" description="This tutorial shows you how to upload a custom Java web site to Azure." metaCanonical="" services="web-sites" documentationCenter="Java" title="Upload a custom Java web site to Azure" videoId="" scriptId="" authors="waltpo" solutions="" manager="keboyd" editor="mollybos" />

Hochladen einer benutzerdefinierten Java-Website in Azure
=========================================================

In diesem Thema wird erläutert, wie Sie eine benutzerdefinierte Java-Website in Azure hochladen. Es enthält Informationen, die für alle Java-Websites gelten, und einige Beispiele für spezifische Anwendungen.

Beachten Sie, dass Azure Möglichkeiten bietet, Java-Websites mithilfe der Konfigurations-UI des Azure-Portals und der Azure-Anwendungsgalerie zu erstellen. Informationen hierzu finden Sie unter [Erste Schritte mit Azure-Websites und Java](../web-sites-java-get-started). Dieses Lernprogramm eignet sich für Szenarien, in denen Sie nicht die Azure-Konfigurations-UI oder die Azure-Anwendungsgalerie verwenden möchten.

Konfigurationsrichtlinien
=========================

Im Folgenden werden die Einstellungen beschrieben, die für benutzerdefinierte Java-Websites auf Azure erwartet werden.

-   Der vom Java-Prozess verwendete HTTP-Port wird dynamisch zugewiesen. Der Prozess muss den Port der Umgebungsvariable `HTTP_PLATFORM_PORT` verwenden.
-   Bis auf den einzelnen HTTP-Listener sollten alle Überwachungsports deaktiviert sein. In Tomcat umfassen diese die Shutdown-, HTTPS- und AJP-Ports.
-   Der Container darf nur für IPv4-Verkehr konfiguriert sein.
-   Der Befehl für den **Start** der Anwendung muss in der Konfiguration festgelegt sein.
-   Anwendungen, die Verzeichnisse mit Schreibberechtigungen benötigen, müssen im Inhaltsverzeichnis der Azure-Website abgelegt werden. Dies befindet sich unter **D:\\home**.

Sie können Umgebungsvariablen wie in der web.config-Datei erforderlich festlegen.

httpPlatform-Konfiguration mit web.config
=========================================

Im Folgenden wird das Format **httpPlatform** innerhalb der web.config beschrieben.

**arguments** (Default=""). Argumente für das ausführbare Programm oder das Skript, die in der Einstellung **processPath** festgelegt sind.

Beispiele (mit **processPath**):

    processPath="d:\home\site\wwwroot\bin\tomcat\bin\catalina.bat"
    arguments="start"

    processPath="%JAVA_HOME\bin\java.exe"
    arguments="-Djava.net.preferIPv4Stack=true -Djetty.port=%HTTP\_PLATFORM\_PORT% -Djetty.base="d:\home\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115" -jar "d:\home\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115\start.jar""

**processPath** - Pfad zum ausführbaren Programm oder Skript, das einen Prozess startet, der HTTP-Anfragen abhört.

Beispiele:

    processPath="%JAVA_HOME%\bin\java.exe"

    processPath="d:\home\site\wwwroot\bin\tomcat\bin\startup.bat"

    processPath="d:\home\site\wwwroot\bin\tomcat\bin\catalina.bat"

**rapidFailsPerMinute** (Default=10.) Gibt an, wie oft der unter **processPath** festgelegte Prozess pro Minute abstürzen darf. Falls diese Grenze überschritten wird, startet **HttpPlatformHandler** diesen Prozess innerhalb der verbleibenden Zeit der Minute nicht mehr.

**requestTimeout** (Default="00:02:00".) Gibt an, wie lange **HttpPlatformHandler** auf eine Antwort von dem Prozess wartet, der `%HTTP_PLATFORM_PORT%` abhört.

**startupRetryCount** (Default=10.) Gibt an, wie oft **HttpPlatformHandler** versuchen wird, den unter **processPath** angegebenen Prozess zu starten. Weitere Details erhalten Sie unter **startupTimeLimit**.

**startupTimeLimit** (Default=10 seconds.) Gibt an, wie lange **HttpPlatformHandler** wartet, bis das ausführbare Programm/Skript einen Prozess zum Abhören des Ports startet. Wenn diese Zeitbeschränkung überschritten wird, beendet **HttpPlatformHandler** den Prozess und versucht, ihn so oft erneut zu starten, wie unter **startupRetryCount** angegeben.

**stdoutLogEnabled** (Default="true".) Falls dies zutrifft, werden **stdout** und **stderr**, die für den Prozess in der Einstellung **processPath** festgelegt sind, an die Datei weitergeleitet, die unter **stdoutLogFile** angegeben ist (siehe Abschnitt **stdoutLogFile**).

**stdoutLogFile** (Default="d:\\home\\LogFiles\\httpPlatformStdout.log".) Absoluter Dateipfad, für den **stdout** und **stderr** von dem unter **processPath** angegebenen Prozess protokolliert werden.

> [WACOM.NOTE] `%HTTP_PLATFORM_PORT%` ist ein spezieller Platzhalter, der entweder als Teil von **Argumenten** oder als Teil der **httpPlatform**-Liste **environmentVariables** angegeben wird. Dieser wird von **HttpPlatformHandler** durch einen intern generierten Port ersetzt, damit der von **processPath** festgelegte Prozess diesen Port abhören kann.

Bereitstellung
==============

Java-basierte Websites können im Prinzip auf die gleiche Art und Weise bereitgestellt werden, wie Internet Information Services (IIS)-basierte Webanwendungen. FTP, Git und Kudu werden ebenso als Bereitstellungsmechanismen unterstützt wie die integrierte SCM-Funktion für Websites. WebDeploy fungiert als Protokoll. Da Java jedoch nicht in Visual Studio entwickelt wird, lässt sich WebDeploy nicht für Java-Website-Bereitstellungen verwenden.

Beispiele für die Anwendungskonfiguration
=========================================

Für die folgenden Anwendungen werden eine web.config-Datei und die Anwendungskonfiguration als Beispiele herangezogen, um zu zeigen, wie Sie Ihre Java-Anwendung auf Azure-Websites aktivieren.

Tomcat
------

Es gibt zwei Variationen von Tomcat, die mit Azure-Websites bereitgestellt werden. Es können jedoch auch weiterhin benutzerspezifische Instanzen hochgeladen werden. Im Folgenden finden Sie ein Beispiel einer Installation von Tomcat mit einer anderen JVM.

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <system.webServer>
        <handlers>
          <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
        </handlers>
        <httpPlatform processPath="d:\home\site\wwwroot\bin\tomcat\bin\startup.bat" 
            arguments="">
          <environmentVariables>
            <environmentVariable name="CATALINA_OPTS" value="-Dport.http=%HTTP_PLATFORM_PORT%" />
            <environmentVariable name="CATALINA_HOME" value="d:\home\site\wwwroot\bin\tomcat" />
            <environmentVariable name="JRE_HOME" value="d:\home\site\wwwroot\bin\java" /> <!-- optional, if not specified, this will default to %programfiles%\Java -->
            <environmentVariable name="JAVA_OPTS" value="-Djava.net.preferIPv4Stack=true" />
          </environmentVariables>
        </httpPlatform>
      </system.webServer>
    </configuration>

Bei Tomcat müssen einige Änderungen an der Konfiguration vorgenommen werden. Die server.xml-Datei muss folgendermaßen geändert werden:

-   Shutdown port = -1
-   HTTP connector port = {port.http}
-   HTTP connector address = "127.0.0.1"
-   Kommentieren der HTTPS- und AJP-Konnektoren
-   Die IPv4-Einstellung kann auch in der catalina.properties-Datei festgelegt werden, wo Sie `java.net.preferIPv4Stack=true` hinzufügen können.

Azure-Websites unterstützt keine Direct3D-Aufrufe. Falls Ihre Anwendung solche Aufrufe durchführt, fügen Sie die folgende Java-Option hinzu, um diese Aufrufe zu deaktivieren: `-Dsun.java2d.d3d=false`

Jetty
-----

Wie bei Tomcat, können Kunden auch für Jetty ihre eigenen Instanzen hochladen. Falls Sie die vollständige Installation von Jetty ausführen, würde die Konfiguration folgendermaßen aussehen:

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <system.webServer>
        <handlers>
          <add name="httppPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
        </handlers>
        <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" 
             arguments="-Djava.net.preferIPv4Stack=true -Djetty.port=%HTTP_PLATFORM_PORT% -Djetty.base="d:\home\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115" -jar "d:\home\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115\start.jar""
            startupTimeLimit="20"
          startupRetryCount="10"
          stdoutLogEnabled="true">
        </httpPlatform>
      </system.webServer>
    </configuration>

Die Jetty-Konfiguration muss in der start.ini-Datei geändert werden, um `java.net.preferIPv4Stack=true` festzulegen.

Hudson
------

In unserem Test verwendeten wir die Hudson 3.1.2-WAR-Datei und die Standardinstanz Tomcat 7.0.50, jedoch ohne die UI für die Einrichtung zu verwenden. Da es sich bei Hudson um ein softwarebasiertes Tool handelt, empfehlen wir die Installation auf dedizierten Instanzen, auf denen das Kennzeichen **AlwaysOn** auf der Website festgelegt werden kann.

1.  Erstellen Sie im Root-Verzeichnis auf Ihrer Azure-Website, z. B. **d:\\home\\site\\wwwroot**, ein Verzeichnis **webapps** (falls nicht bereits vorhanden), und speichern Sie die Hudson.war-Datei unter **d:\\home\\site\\wwwroot\\webapps**
2.  Laden Sie Apache Maven 3.0.5 herunter (mit Hudson kompatibel), und speichern Sie es unter **d:\\home\\site\\wwwroot**.
3.  Erstellen Sie unter **d:\\home\\site\\wwwroot** eine web.conifg-Datei, und kopieren Sie die folgenden Inhalte hinein:

        <?xml version="1.0" encoding="UTF-8"?>
         <configuration>
           <system.webServer>
             <handlers>
               <add name="httppPlatformHandler" path="*" verb="*" 
         modules="httpPlatformHandler" resourceType="Unspecified" />
             </handlers>
             <httpPlatform processPath="%AZURE_TOMCAT7_HOME%\bin\startup.bat"
         startupTimeLimit="20"
         startupRetryCount="10">
         <environmentVariables>
           <environmentVariable name="HUDSON_HOME" 
         value="d:\home\site\wwwroot\hudson_home" />
           <environmentVariable name="JAVA_OPTS" 
         value="-Djava.net.preferIPv4Stack=true -Duser.home=d:/home/site/wwwroot/user_home -Dhudson.DNSMultiCast.disabled=true" />
         </environmentVariables>            
             </httpPlatform>
           </system.webServer>
         </configuration>

    Nun kann die Website neu gestartet werden, um die Änderungen zu übernehmen. Stellen Sie eine Verbindung zu http://yoursite/hudson her, um Hudson zu starten.

4.  Nachdem Hudson sich selbst konfiguriert hat, sollte die folgende Anzeige zu sehen sein:

    ![Hudson](./media/web-sites-java-custom-upload/hudson1.png)

5.  Zugriff auf die Hudson-Konfigurationsseite: Klicken Sie auf **Manage Hudson** und dann auf **Configure System**.
6.  Konfigurieren Sie das JDK wie unten gezeigt:

    ![Hudson configuration](./media/web-sites-java-custom-upload/hudson2.png)

7.  Konfigurieren Sie Maven wie unten gezeigt:

    ![Maven configuration](./media/web-sites-java-custom-upload/maven.png)

8.  Speichern Sie die Einstellungen. Hudson sollte nun konfiguriert und einsatzbereit sein.

Weitere Informationen zu Hudson finden Sie unter <http://hudson-ci.org>.

Liferay
-------

Liferay wird auf Azure-Websites unterstützt. Da Liferay möglicherweise enorm viel Speicherplatz benötigt, muss die Website auf einem mittleren oder großen dedizierten Worker ausgeführt werden, der genügend Speicherplatz bietet. Der Startvorgang von Liferay nimmt einige Minuten in Anspruch. Aus diesem Grund empfiehlt es sich, die Einstellung **Always On** für die Website zu verwenden.

Bei der Verwendung von Liferay 6.1.2 Community Edition GA3 in Kombination mit Tomcat wurden die folgenden Dateien nach dem Herunterladen von Liferay bearbeitet:

**Server.xml**

-   Legen Sie für den Shutdown-Port "-1" fest.
-   Legen Sie für den HTTP-Konnektor `<Connector port="${port.http}" protocol="HTTP/1.1" connectionTimeout="600000" address="127.0.0.1" URIEncoding="UTF-8" />` fest.
-   Kommentieren Sie den AJP-Konnektor.

Erstellen Sie im Ordner **liferay\\tomcat-7.0.40\\webapps\\ROOT\\WEB-INF\\classes** eine Datei namens **portal-ext.properties**. Die Datei muss die folgende Zeile enthalten:

    liferay.home=d:/home/site/wwwroot/liferay

Erstellen Sie auf der Verzeichnisebene des Ordners "tomcat-7.0.40" eine Datei namens **web.config** mit folgendem Inhalt:

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <system.webServer>
        <handlers>
    <add name="httpPlatformHandler" path="*" verb="*"
         modules="httpPlatformHandler" resourceType="Unspecified" />
        </handlers>
        <httpPlatform processPath="d:\home\site\wwwroot\tomcat-7.0.40\bin\catalina.bat" 
                      arguments="run" 
                      startupTimeLimit="10" 
                      requestTimeout="00:10:00" 
                      stdoutLogEnabled="true">
          <environmentVariables>
      <environmentVariable name="CATALINA_OPTS" value="-Dport.http=%HTTP_PLATFORM_PORT%" />
      <environmentVariable name="CATALINA_HOME" value="d:\home\site\wwwroot\tomcat-7.0.40" />
            <environmentVariable name="JRE_HOME" value="D:\Program Files\Java\jdk1.7.0_51" /> 
            <environmentVariable name="JAVA_OPTS" value="-Djava.net.preferIPv4Stack=true" />
          </environmentVariables>
        </httpPlatform>
      </system.webServer>
    </configuration>

Unterhalb des Blocks **httpPlatform** wird für **requestTimeout** "â€œ00:10:00â€" festgelegt. Dies kann auch reduziert werden, allerdings werden Ihnen dann möglicherweise einige Zeitüberschreitungsfehler beim Bootstrapping von Liferay angezeigt. Wenn dieser Wert geändert wird, sollte auch **connectionTimeout** in der server.xml-Datei von Tomcat geändert werden.

Es sollte darauf hingewiesen werden, dass die JRE\_HOME-Umgebungsvariable in der oben genannten web.config-Datei festgelegt wurde, um auf das 64-Bit-JDK zu verweisen. Der Standardwert ist 32-Bit, aber da Liferay möglicherweise mehr Speicher benötigt, wird empfohlen, das 64-Bit-JDK zu verwenden.

Sobald Sie diese Änderungen vorgenommen haben, starten Sie Ihre Website neu mit Liferay, und öffnen Sie http://yoursite. Das Liferay-Portal ist über das Website-Root-Verzeichnis verfügbar.

Weitere Informationen über Liferay finden Sie unter <http://www.liferay.com>.
