<properties linkid="develop-java-how-to-twilio-phone-call" urlDisplayName="How to Make a Phone Call from Twilio in Java" pageTitle="How to Make a phone call from Twilio (Java) - Azure" metaKeywords="Azure Twilio call, Twilio call website, Azure Twilio Java" description="Learn how to make a phone call from a web page using Twilio in a Java application on Azure." metaCanonical="" services="" documentationCenter="Java" title="How to Make a Phone Call Using Twilio in a Java Application on Azure" authors="waltpo" videoId="" scriptId="" solutions="" manager="bjsmith" editor="mollybos" />

Tätigen eines Telefonanrufs mithilfe von Twilio in einer Java-Anwendung auf Azure
=================================================================================

Das folgende Beispiel zeigt, wie Sie von einer in Azure gehosteten Webseite einen Anruf über Twilio tätigen können. Die Anwendung fragt den Benutzer nach Werten für den Telefonanruf, wie im folgenden Screenshot gezeigt.

![Azure-Anrufformular mit Twilio und Java](./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg)

Sie benötigen Folgendes, um den Code in diesem Artikel ausführen zu können:

1.  Ein Twilio-Konto und ein Authentifizierungs-Token. Sie können Sie die Preise von Twilio unter <http://www.twilio.com/pricing> ansehen. Sie können sich unter [https://www.twilio.com/try-twilio](http://www.twilio.com/try-twilio) registrieren. Weitere Informationen über die von Twilio bereitgestellte API finden Sie unter <http://www.twilio.com/api>.
2.  Verifizieren Sie Ihre Telefonnummer als ausgehende Anrufer-ID bei Twilio. Informationen zum Verifizieren Ihrer Telefonnummer finden Sie unter <https://www.twilio.com/user/account/phone-numbers/verified#>. Alternativ zur Verwendung einer vorhandenen Telefonnummer besteht die Möglichkeit, eine Twilio-Telefonnummer zu kaufen. Verwenden Sie für dieses Beispiel die verifizierte Telefonnummer als **From**-Wert in callform.jsp (weiter unten beschrieben).
3.  Laden Sie das Twilio-JAR herunter. Unter [https://github.com/twilio/twilio-java](http://github.com/twilio/twilio-java), können Sie die GitHub-Quellen herunterladen und Ihr eigenes JAR erstellen oder ein bereits erstelltes JAR herunterladen (mit oder ohne Abhängigkeiten). Der Code in diesem Beispiel wurde mit dem vorkompilierten TwilioJava-3.3.8-with-dependencies-Jar geschrieben.
4.  Fügen Sie die JAR-Datei zu Ihrem Buildpfad hinzu.
5.  Falls Sie Eclipse für diese Java-Anwendung verwenden, binden Sie das Twilio-JAR mit der Bereitstellungs-Assembly-Funktion von Eclipse in Ihre Bereitstellungsdatei (WAR) ein. Falls Sie Eclipse für diese Java-Anwendung nicht verwenden, müssen Sie sicherstellen, dass das Twilio-JAR in derselben Azure-Rolle enthalten ist wie Ihre Java-Anwendung und dass Sie diese zum Classpath Ihrer Anwendung hinzugefügt haben.
6.  Stellen Sie sicher, dass Ihr cacerts-Zertifikatspeicher das Zertifikat der Equifax-Zertifizierungsstelle mit dem MD5-Fingerabdruck 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 enthält (die Seriennummer ist 35:DE:F4:CF und der SHA1-Fingerabdruck ist D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Dies ist das Zertifizierungsstellen (ZS)-Zertifikat für den [https://api.twilio.com](http://api.twilio.com)-Dienst, der beim Verwenden der Twilio-APIs aufgerufen wird. Informationen zum Hinzufügen dieses ZS-Zertifikats zum Cacert-Speicher Ihres SDK finden Sie unter [Hinzufügen eines Zertifikats zum Java ZS-Zertifikatspeicher](../java-add-certificate-ca-store).

Außerdem sollten Sie sich mit den Informationen unter [Erstellen einer Hello World-Anwendung mithilfe des Azure-Plug-Ins für Eclipse with Java (von Microsoft Open Technologies)](http://msdn.microsoft.com/de-de/library/windowsazure/hh690944.aspx) oder mit anderen Techniken zum Hosten von Java-Anwendungen in Azure vertraut machen, falls Sie Eclipse nicht verwenden.

Erstellen eines Web-Formulars für den Anruf
-------------------------------------------

Der folgende Code erstellt ein Web-Formular zur Eingabe der Benutzerdaten für den Anruf. Für dieses Beispiel wurde ein neues dynamisches Webprojekt mit dem Namen **TwilioCloud** erstellt und **callform.jsp** als JSP-Datei hinzugefügt.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Automated call form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Make this call</b>.</p>
     <br/>
      <form action="makecall.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="callTo" value="" />
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="callFrom" value="" />
           </td>
         </tr>
         <tr>
           <td>Call message:</td>
           <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Make this call" />
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

Erstellen des Codes für den Anruf
---------------------------------

Der folgende Code wird aufgerufen wenn der Benutzer das Formular aus callform.jsp abschließt und generiert die Anrufnachricht und führt den Anruf aus. Für dieses Beispiel wurde die Datei **makecall.jsp** genannt und zum **TwilioCloud**-Projekt hinzugefügt. (Ersetzen Sie die Platzhalterwerte **accountSID** und **authToken** im folgenden Code durch Ihr Twilio-Konto und Ihr Authentifizierungs-Token.)

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
         import="java.util.*"
         import="com.twilio.*"
         import="com.twilio.sdk.*"
         import="com.twilio.sdk.resource.factory.*"
         import="com.twilio.sdk.resource.instance.*"
         pageEncoding="ISO-8859-1" %>
         <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
         <html>
         <head>
         <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
         <title>Call processing happens here</title>
         </head>
         <body>
             <b>This is my make call page.</b><p/>
          <%
         try 
         {
              // Ersetzen Sie die Platzhalter durch Ihre Konto-SID und
              // Ihr Authentifizierungs-Token.
         String accountSID = "your_twilio_account";
              String authToken = "your_twilio_authentication_token";
          
         // Instanz des Twilio-Clients erstellen.     
         TwilioRestClient client;
              client = new TwilioRestClient(accountSID, authToken);
         
         // Konto abrufen, wird später für die Erstellung einer CallFactory verwendet.
         Account account = client.getAccount();
         
              // Client-Endpunkt anzeigen. 
         out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");
          
              // API-Version anzeigen.
         String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
              out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");
         
              // Vom Benutzer eingegebene Werte abrufen.
         String callTo = request.getParameter("callTo");  
              // Die ausgehende Anrufer-ID wird für den From-Parameter verwendet
              // und muss zuvor bei Twilio verifiziert worden sein.
         String callFrom = request.getParameter("callFrom");
              String userText = request.getParameter("callText");
          
              // Leerzeichen im Text durch '%20' ersetzen, um den 
              // Text für URL-kompatibel zu machen.
         userText = userText.replace(" ", "%20");
          
              // Erstellen einer URL aus der Twilio-Nachricht und dem Text vom Benutzer.
         String Url="http://twimlets.com/message";
              Url = Url + "
         Message%5B0%5D=" + userText;
          
              // URL der Nachricht anzeigen.
         out.println("<p>");
              out.println("The URL is " + Url);
              out.println("</p>");
         
              // Werte für From, To und URL in einer Hashzuordnung speichern. 
         HashMap<String, String> params = new HashMap<String, String>();
              params.put("From", callFrom);
              params.put("To", callTo);
              params.put("Url", Url);
          
              CallFactory callFactory = account.getCallFactory();
              Call call = callFactory.create(params);
              out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
         } 
         catch (TwilioRestException e) 
         {
             out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
             out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
         }
         catch (Exception e) 
         {
             out.println("<p>Exception encountered: " + e.getMessage() + "");
             out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
         }
         %>
         </body>
         </html>

makecall.jsp führt nicht nur den Anruf aus, sondern gibt auch noch den Twilio-Endpunkt, die API-Version und den Anrufstatus aus. Der folgende Screenshot zeigt ein Beispiel:

![Azure-Anrufantwort mit Twilio und Java](./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg)

Ausführen der Anwendung
-----------------------

Es folgen nun die allgemeinen Schritte zum Ausführen Ihrer Anwendung. Details zu diesen Schritten finden Sie unter [Erstellen einer Hello World-Anwendung mithilfe des Azure-Plug-Ins für Eclipse with Java (von Microsoft Open Technologies)](http://msdn.microsoft.com/de-de/library/windowsazure/hh690944.aspx).

1.  Exportieren Sie Ihr TwilioCloud-WAR in den **approot**-Ordner in Azure.
2.  Ändern Sie **startup.cmd**, um Ihr TwilioCloud-WAR zu entzippen.
3.  Kompilieren Sie Ihre Anwendung für den Serveremulator.
4.  Starten Sie Ihre Bereitstellung im Serveremulator.
5.  Starten Sie Ihren Browser und öffnen Sie die Adresse **http://localhost:8080/TwilioCloud/callform.jsp**.
6.  Geben Sie Werte in das Formular ein, klicken Sie auf **Make this call** und sehen Sie sich die Ergebnisse in makecall.jsp an.

Wenn Sie die Anwendung in Azure bereitstellen möchten, kompilieren Sie diese für die Cloud-Bereitstellung, laden Sie sie in Azure hoch und öffnen Sie http://*your\_hosted\_name*.cloudapp.net/TwilioCloud/callform.jsp in Ihrem Browser (ersetzen Sie *your\_hosted\_name* durch Ihren entsprechenden Wert).

Nächste Schritte
----------------

Dieser Code demonstriert die allgemeinen Funktionen für die Verwendung von Twilio mit Java in Azure. Bevor Sie dieses Beispiel in einer Produktionsumgebung bereitstellen, sollten Sie einige Funktionen zur Fehlerbehandlung oder andere Features hinzufügen. Beispiel:

-   Anstelle eines Web-Formulars könnten Sie Azure-Speicher-Blobs oder eine SQL-Datenbank zum Speichern von Telefonnummern und Anruftexten verwenden. Weitere Informationen zur Verwendung von Azure-Speicher-Blobs in Java finden Sie unter [Verwenden des Blob-Speicherdiensts in Java](http://www.windowsazure.com/de-de/develop/java/how-to-guides/blob-storage/). Informationen zur Verwendung von SQL-Datenbanken in Java finden Sie unter [Verwenden der SQL-Datenbank in Java](http://msdn.microsoft.com/de-de/library/windowsazure/hh749029.aspx).
-   Sie können **RoleEnvironment.getConfigurationSettings** verwenden, um die Twilio-Konto-ID und das Authentifizierungs-Token aus den Konfigurationseinstellungen Ihrer Anwendung abzurufen, anstelle diese Werte in makecall.jsp fest einzuprogrammieren. Weitere Informationen zur **RoleEnvironment**-Klasse finden Sie unter [Verwenden der Azure-Dienst-Runtimebibliothek JSP](http://msdn.microsoft.com/de-de/library/windowsazure/hh690948.aspx) und in der Dokumentation der Azure-Dienst-Runtimebibliothek unter <http://dl.windowsazure.com/javadoc>.
-   Der Code von makecall.jsp weist die von Twilio bereitgestellte URL <http://twimlets.com/message> an die Variable **Url** zu. Diese URL gibt eine Antwort in der Twilio Markup Language (TwiML), die Twilio mitteilt, wie mit dem Anruf verfahren werden soll. Das zurückgegebene TwiML kann z. B. ein **&lt;Say\>**-Verb enthalten, das dazu führt, dass dem Anrufempfänger ein bestimmter Text vorgesprochen wird. Anstelle der von Twilio bereitgestellten URL können Sie auch mit einem eigenen Dienst auf die Twilio-Anfrage antworten. Weitere Informationen finden Sie unter [Verwenden von Twilio für Telefonie- und SMS-Funktionen in Java](../partner-twilio-java-how-to-use-voice-sms). Weitere Informationen zu TwiML finden Sie unter <http://www.twilio.com/docs/api/twiml>, und weitere Informationen zu **&lt;Say\>** und anderen Twilio-Verben finden Sie unter <http://www.twilio.com/docs/api/twiml/say>.
-   Lesen Sie die Twilio-Sicherheitsrichtlinien unter [https://www.twilio.com/docs/security](http://www.twilio.com/docs/security).

Weitere Informationen zu Twilio finden Sie unter [https://www.twilio.com/docs](http://www.twilio.com/docs).

Weitere Informationen
---------------------

-   [Verwenden von Twilio für Telefonie- und SMS-Funktionen in Java](../partner-twilio-java-how-to-use-voice-sms)
-   [Hinzufügen eines Zertifikats zum Java ZS-Zertifikatspeicher](../java-add-certificate-ca-store)
