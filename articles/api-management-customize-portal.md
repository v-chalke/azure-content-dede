<properties pageTitle="Anpassen des Entwicklerportals in der Azure API-Verwaltung" metaKeywords="" description="Anpassen des Entwicklerportals in der Azure API-Verwaltung." metaCanonical="" services="api-management" documentationCenter="API Management" title="Anpassen des Entwicklerportals in der Azure API-Verwaltung" authors="sdanie" solutions="" manager="dwrede" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# Anpassen des Entwicklerportals in der Azure API-Verwaltung

Diese Anleitung beschreibt, wie Sie das Erscheinungsbild des Entwicklerportals in der API-Verwaltung an Ihre Marke anpassen können.

## In diesem Thema

-   [Ändern von Text/Logo in den Kopfzeilen][Ändern von Text/Logo in den Kopfzeilen]
-   [Ändern des Stils der Kopfzeilen][Ändern des Stils der Kopfzeilen]
-   [Bearbeiten der Seiteninhalte][Bearbeiten der Seiteninhalte]
-   [Nächste Schritte][Nächste Schritte]

## <a name="change-page-headers"> </a>Ändern von Text/Logo in der Kopfzeile

Einer der Schlüsselaspekte der Portalanpassung ist die Möglichkeit, den Text am oberen Rand aller Seiten durch Ihren Firmennamen oder Ihr Logo zu ersetzen.

Die Inhalte im Entwicklerportal werden über das Veröffentlichungsportal erstellt und konfiguriert, das im Azure-Verwaltungsportal erreichbar ist. Um die API-Verwaltungskonsole zu erreichen, klicken Sie im Azure-Portal auf **Verwaltungskonsole** für Ihren API-Verwaltungsdienst.

![Verwaltungskonsole][Verwaltungskonsole]

Das Entwicklerportal basiert auf einem Content Management-System oder CMS. Bei der auf jeder Seite angezeigten Kopfzeile handelt es sich um einen Inhaltstyp, der auch Widget genannt wird. Um den Inhalt des Widgets zu bearbeiten, klicken Sie auf **Widgets** im Menü **Entwicklerportal** auf der linken Seite und wählen Sie das Widget **Header** in der Liste aus.

![Kopfzeilen-Widget][Kopfzeilen-Widget]

Sie können den Inhalt der Fußzeile im Feld **Body** bearbeiten. Ändern Sie den Text zu "Fabrikam-Entwicklerportal" und klicken Sie unten in der Seite auf **Speichern**.

Die neue Kopfzeile sollte nun auf allen Seiten im Entwicklerportal angezeigt werden!

> Klicken Sie in der oberen Leiste auf **Entwicklerportal**, um das Entwicklerportal aus der Verwaltungskonsole heraus zu öffnen.

## <a name="change-headers-styling"> </a>Ändern des Stils der Kopfzeilen

Farben, Schriftarten, Schriftgrößen und andere stilverwandte Elemente aller Portalseiten werden durch Stilregeln definiert. Klicken Sie auf **Darstellung** im Menü **Entwicklerportal** im Veröffentlichungsportal, um die Stilregeln zu bearbeiten. Klicken Sie anschließend auf **Anpassung beginnen**, um den Stileditor zu aktivieren.

Ihr Browser wechselt zu einer versteckten Seite im Entwicklerportal mit Beispielen für die Inhalte unter Anwendungen aller Stilregeln, die irgendwo auf der Seite verwendet werden. Um den Stileditor zu öffnen, bewegen Sie Ihren Mauszeiger über die dünne vertikale graue Linie ganz links auf der Seite. Die Editor-Symbolleiste sollte angezeigt werden.

![Anpassungssymbolleiste][Anpassungssymbolleiste]

Für die Bearbeitung der Stilregeln gibt es zwei Bearbeitungsmodi - **Alle Regeln bearbeiten** zeigt eine Liste aller im Portal verwendeten Stilregeln an; und mit **Element auswählen** können Sie ein Element auf der aktuellen Seite auswählen, um nur die Stilregeln für dieses Element anzuzeigen.

In diesem Abschnitt werden wir den Stil für die Kopfzeile ändern. Klicken Sie in der Stileditor-Symbolleiste auf **Element auswählen** und anschließend auf **Anzupassendes Element auswählen**. Die Elemente werden nun hervorgehoben, wenn Sie den Mauszeiger darüber bewegen, um anzuzeigen, welche Stilregeln des Elements Sie mit einem Klick bearbeiten können. Bewegen Sie die Maus über den Text mit dem Firmennamen in der Kopfzeile ("Fabrikam-Entwicklerportal", falls Sie die Anweisungen im vorigen Abschnitt ausgeführt haben) und klicken Sie darauf. Eine Liste mit benannten und kategorisierten Stilregeln wird daraufhin im Stileditor angezeigt.

Jede Regel steht für eine Stileigenschaft des ausgewählten Elements. Der ausgewählte Kopfzeilentext hat z. B. den Schriftgrad @font-size-h1, und der Name der Schriftart mit Alternativen befindet sich in @headings-font-family.

> Falls Sie mit [Bootstrap][Bootstrap] vertraut sind, werden Sie feststellen, dass es sich bei diesen Regeln um [LESS-Variablen][LESS-Variablen] aus dem Bootstrap-Design für das Entwicklerportal handelt.

Ändern Sie nun die Farbe der Überschrift. Wählen Sie den Eintrag im Feld <**@headings-color*>\* aus und geben Sie \#000000 ein. Dies ist der Hexadezimalcode für die Farbe Schwarz. Daraufhin sollte ein rechteckiger Farbindikator am Ende des Textfelds angezeigt werden. Wenn Sie diesen Indikator anklicken, können Sie mit einem Farbwähler eine Farbe auswählen.

![Farbauswahl][Farbauswahl]

Wenn Sie Ihre Änderungen an den Stilregeln des ausgewählten Elements abgeschlossen haben, klicken Sie auf **Änderungen anzeigen**, um das Resultat auf dem Bildschirm anzuzeigen. Zu diesem Zeitpunkt sind die Änderungen nur für Administratoren sichtbar. Um die Änderungen für alle Benutzer sichtbar zu machen, klicken Sie auf **Veröffentlichen** im Stileditor und bestätigen Sie die Änderungen.

![Veröffentlichungsmenü][Veröffentlichungsmenü]

> Um die Stilregeln der anderen Elemente auf der Seite zu ändern, führen Sie dieselben Schritte wie bei der Änderung der Kopfzeile aus. Klicken Sie auf **Element auswählen** im Stileditor, wählen Sie das gewünschte Element aus und ändern Sie die Werte der Stilregeln, die auf dem Bildschirm angezeigt werden.

## <a name="edit-page-contents"> </a>Bearbeiten der Seiteninhalte

Das Entwicklerportal besteht aus automatisch generierten Seiten wie z. B. APIs, Produkte Anwendungen, Probleme und manuell erstellte Inhalte. Da das Portal auf einem Content Management-System basiert, können Sie diese Inhalte jederzeit erstellen.

Klicken Sie auf **Inhalte** im Menü **Entwicklerportal** in der Verwaltungskonsole, um eine Liste aller verfügbaren Inhaltsseiten anzuzeigen.

![Inhalt verwalten][Inhalt verwalten]

Klicken Sie auf die Willkommensseite, um den Inhalt zu bearbeiten, der auf der Startseite des Entwicklerportals angezeigt wird. Nehmen Sie die gewünschten Änderungen vor, zeigen Sie die Änderungen in einer Vorschau an und klicken Sie anschließend auf **Jetzt veröffentlichen**, um sie für alle Benutzer sichtbar zu machen.

> Die Startseite verwendet ein spezielles Layout, mit dem ein Banner am oberen Rand angezeigt werden kann. Dieses Banner kann nicht im Inhaltsbereich bearbeitet werden. Um dieses Banner zu bearbeiten, klicken Sie auf **Widgets** im Menü **Entwicklerportal**, wählen Sie **Startseite** in der Dropdownliste **Aktuelle Ebene** aus und öffnen Sie das Element **Banner** im Bereich „Ausgewählte“. Sie können den Inhalt dieses Widgets ebenso wie alle anderen Seiten bearbeiten.

## <a name="next-steps"> </a>Nächste Schritte

-   Lesen Sie die anderen Themen im Lernprogramm [Erste Schritte bei der erweiterten API-Konfiguration][Erste Schritte bei der erweiterten API-Konfiguration].

  [Ändern von Text/Logo in den Kopfzeilen]: #change-page-headers
  [Ändern des Stils der Kopfzeilen]: #change-headers-styling
  [Bearbeiten der Seiteninhalte]: #edit-page-contents
  [Nächste Schritte]: #next-steps
  [Verwaltungskonsole]: ./media/api-management-customize-portal/api-management-management-console.png
  [Kopfzeilen-Widget]: ./media/api-management-customize-portal/api-management-widgets-header.png
  [Anpassungssymbolleiste]: ./media/api-management-customize-portal/api-management-customization-toolbar.png
  [Bootstrap]: http://getbootstrap.com/
  [LESS-Variablen]: http://getbootstrap.com/css/
  [Farbauswahl]: ./media/api-management-customize-portal/api-management-customization-toolbar-color-picker.png
  [Veröffentlichungsmenü]: ./media/api-management-customize-portal/api-management-customization-toolbar-publish-form.png
  [Inhalt verwalten]: ./media/api-management-customize-portal/api-management-customization-manage-content.png
  [Erste Schritte bei der erweiterten API-Konfiguration]: ../api-management-get-started-advanced