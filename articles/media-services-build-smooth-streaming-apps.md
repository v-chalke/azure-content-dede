<properties linkid="develop-media-services-tutorials-smooth-streaming-windows-store-app" urlDisplayName="Smooth Streaming Windows Store App" pageTitle="Smooth Streaming Windows Store App - Azure .NET tutorials" metaKeywords="" description="Learn how to use Azure Media Services to create a C# Windows Store application with a XML MediaElement control to playback Smooth Stream content." metaCanonical="" services="media-services" documentationCenter="" title="How to Build a Smooth Streaming Windows Store Application" authors="" solutions="" manager="" editor="" />

Erstellen einer Smooth Streaming Windows Store-Anwendung
========================================================

Mit dem Smooth Streaming Client SDK für Windows 8 können Entwickler Windows Store-Anwendungen erstellen, die Smooth Streaming-Inhalte bedarfsgesteuert und live wiedergeben können. Neben der Wiedergabe von Smooth Streaming-Inhalten bietet das SDK zahlreiche weitere Funktionen wie Microsoft PlayReady-Schutz, Qualitätsstufeneinschränkung, Live DVR, Wechsel von Audiodatenströmen, Überwachen von Statusaktualisierungen (z. B. Qualitätsstufenänderungen) und Fehlerereignissen usw. Weitere Informationen über die unterstützten Funktionen finden Sie in den [Versionshinweisen](http://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes).

In diesem Lernprogramm werden die APIs behandelt. Für die Playerentwicklung empfiehlt Microsoft dringend die Verwendung von [Player Framework for Windows 8](http://playerframework.codeplex.com/). Player Framework erleichtert das Erstellen von Anwendungen und bietet zahlreiche zusätzliche Funktionen.

Dieses Lernprogramm enthält vier Lektionen:

1.  Erstellen einer grundlegenden Smooth Streaming Store-Anwendung
2.  Hinzufügen eines Schiebereglers zum Steuern des Medienstatus
3.  Auswählen von Smooth Streaming-Datenströmen
4.  Auswählen von Smooth Streaming-Spuren

Voraussetzungen
===============

-   Windows 8 32 Bit oder 64 Bit. Sie erhalten die [Windows 8 Enterprise-Evaluierungsversion](http://msdn.microsoft.com/de-de/evalcenter/jj554510.aspx) bei MSDN.
-   Visual Studio 2012 oder Visual Studio Express 2012 für Windows 8 installiert unter Windows 8. Die Testversion erhalten Sie [hier](http://www.microsoft.com/visualstudio/11/en-us/downloads).
-   [Microsoft Smooth Streaming Client SDK for Windows 8](http://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Homehttp://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home).

Die fertige Lösung für jede Lektion können Sie von der Website mit den MSDN-Codebeispielen für Entwickler (Code Gallery) herunterladen: [Lektion 1](http://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f "A Simple Windows 8 Smooth Streaming Media Player") (Ein einfacher Windows 8 Smooth Streaming Media Player), [Lektion 2](http://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a "A Simple Windows 8 Smooth Streaming Media Player with a Slider Bar Control") (Ein einfacher Windows 8 Smooth Streaming Media Player mit Schieberegler), [Lektion 3](http://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44 "A Windows 8 Smooth Streaming Media Player with Stream Selection") (Ein Windows 8 Smooth Streaming Media Player mit Datenstromauswahl) und [Lektion 4](http://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907 "A Windows 8 Smooth Streaming Media Player with Track Selection") (Ein Windows 8 Smooth Streaming Media Player mit Spurauswahl; alle in englischer Sprache).

Lektion 1: Erstellen einer grundlegenden Smooth Streaming Store-Anwendung
=========================================================================

In dieser Lektion erstellen Sie eine Windows Store-Anwendung mit einem MediaElement-Steuerelement für die Wiedergabe von Smooth Stream-Inhalten. Die aktive Anwendung sieht folgendermaßen aus:

![Beispiel für Smooth Streaming Windows Store-Anwendung](./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png)

Weitere Informationen zum Entwickeln von Windows Store-Anwendungen finden Sie unter [Entwickeln von Apps für Windows 8](http://msdn.microsoft.com/de-de/windows/apps/br229512.aspx). Diese Lektion enthält die folgenden Prozeduren:

1.  Erstellen eines Windows Store-Projekts
2.  Entwerfen der Benutzeroberfläche (XAML)
3.  Ändern der CodeBehind-Datei
4.  Kompilieren und Testen der Anwendung

**So erstellen Sie ein Windows Store-Projekt**

1.  Führen Sie Visual Studio 2012 aus.
2.  Klicken Sie im Menü **Datei** auf **Neu** und dann auf **Projekt**.
3.  Geben Sie im Dialogfeld "Neues Projekt" die folgenden Werte ein, oder wählen Sie sie aus:


	<table border="1">
	<tr>
		<th>Name</th>
		<th>Wert</th>
	</tr>
	<tr>
		<td>Vorlagengruppe</td>
		<td>Installed/Templates/Visual C#/Windows Store</td>
	</tr>
	<tr>
		<td>Vorlage</td>
		<td>Leere App (XAML)</td>
	</tr>
	<tr>
		<td>Name</td>
		<td>SSPlayer</td>
	</tr>
	<tr>
		<td>Verzeichnis</td>
		<td>C:\SSTutorials</td>
	</tr>
	<tr>
		<td>Projektmappenname</td>
		<td>SSPlayer</td>
	</tr>
	<tr>
		<td>Projektmappenverzeichnis erstellen</td>
		<td>(aktiviert)</td>
	</tr>
	</table>

4.  Klicken Sie auf **OK**.

**So fügen Sie einen Verweis auf das Smooth Streaming Client SDK hinzu**

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **SSPlayer** und anschließend auf **Verweis hinzufügen**.
2.  Geben Sie folgende Werte ein bzw. wählen diese aus:


	<table border="1">
	<tr>
		<th>Name</th>
		<th>Wert</th>
	</tr>
	<tr>
		<td>Verweisgruppep</td>
		<td>Windows/Extensions</td>
	</tr>
	<tr>
		<td>Verweis</td>
		<td>Wählen Sie Microsoft Smooth Streaming Client SDK for Windows 8 und Microsoft Visual C++ Runtime Package. 
		</td>
	</tr>
	</table>

3.  Klicken Sie auf **OK**.

Nachdem Sie die Verweise hinzugefügt haben, müssen Sie die Zielplattform (x64 oder x86) auswählen. Das Hinzufügen von Verweisen funktioniert nicht bei der Plattformkonfiguration für eine beliebige CPU. Im Projektmappen-Explorer wird bei diesen hinzugefügten Verweisen eine gelbe Markierung angezeigt.

**So entwerfen Sie die Player-Benutzeroberfläche**

1.  Doppelklicken Sie im Projektmappen-Explorer auf **MainPage.xaml**, um die Datei in der Entwurfsansicht zu öffnen.
2.  Suchen Sie die Tags **&lt;Grid\>** und **&lt;/Grid\>** in der XAML-Datei, und fügen Sie zwischen diesen Tags folgenden Code ein:

        <Grid.RowDefinitions>
            <RowDefinition Height="20"/>    <!-- spacer -->
            <RowDefinition Height="50"/>    <!-- media controls -->
            <RowDefinition Height="100*"/>  <!-- media element -->
            <RowDefinition Height="80*"/>   <!-- media stream and track selection -->
            <RowDefinition Height="50"/>    <!-- status bar -->
        </Grid.RowDefinitions>
            
        <StackPanel Name="spMediaControl" Grid.Row="1" Orientation="Horizontal">
            <TextBlock x:Name="tbSource" Text="Source :  " FontSize="16" FontWeight="Bold" VerticalAlignment="Center" />
            <TextBox x:Name="txtMediaSource" Text="http://ecn.channel9.msdn.com/o9/content/smf/smoothcontent/elephantsdream/Elephants_Dream_1024-h264-st-aac.ism/manifest" FontSize="10" Width="700" Margin="0,4,0,10" />
            <Button x:Name="btnSetSource" Content="Set Source" Width="111" Height="43" Click="btnSetSource_Click"/>
            <Button x:Name="btnPlay" Content="Play" Width="111" Height="43" Click="btnPlay_Click"/>
            <Button x:Name="btnPause" Content="Pause"  Width="111" Height="43" Click="btnPause_Click"/>
            <Button x:Name="btnStop" Content="Stop"  Width="111" Height="43" Click="btnStop_Click"/>
            <CheckBox x:Name="chkAutoPlay" Content="Auto Play" Height="55" Width="Auto" IsChecked="{Binding AutoPlay, ElementName=mediaElement, Mode=TwoWay}"/>
            <CheckBox x:Name="chkMute" Content="Mute" Height="55" Width="67" IsChecked="{Binding IsMuted, ElementName=mediaElement, Mode=TwoWay}"/>
        </StackPanel>

        <StackPanel Name="spMediaElement" Grid.Row="2" Height="435" Width="1072"
                    HorizontalAlignment="Center" VerticalAlignment="Center">
            <MediaElement x:Name="mediaElement" Height="356" Width="924" MinHeight="225"
                          HorizontalAlignment="Center" VerticalAlignment="Center" 
                          AudioCategory="BackgroundCapableMedia" />
            <StackPanel Orientation="Horizontal">
                <Slider x:Name="sliderProgress" Width="924" Height="44"
                        HorizontalAlignment="Center" VerticalAlignment="Center"
                        PointerPressed="sliderProgress_PointerPressed"/>
                <Slider x:Name="sliderVolume" 
                        HorizontalAlignment="Right" VerticalAlignment="Center" Orientation="Vertical" 
                        Height="79" Width="148" Minimum="0" Maximum="1" StepFrequency="0.1" 
                        Value="{Binding Volume, ElementName=mediaElement, Mode=TwoWay}" 
                        ToolTipService.ToolTip="{Binding Value, RelativeSource={RelativeSource Mode=Self}}"/>
            </StackPanel>
        </StackPanel>

        <StackPanel Name="spStatus" Grid.Row="4" Orientation="Horizontal">
            <TextBlock x:Name="tbStatus" Text="Status :  " 
               FontSize="16" FontWeight="Bold" VerticalAlignment="Center" HorizontalAlignment="Center" />
            <TextBox x:Name="txtStatus" FontSize="10" Width="700" VerticalAlignment="Center"/>
        </StackPanel>

    Das MediaElement-Steuerelement dient zur Wiedergabe von Medien. Mit dem Schieberegler sliderProgress wird in der nächsten Lektion der Medienstatus gesteuert.

3.  Drücken Sie **STRG+S**, um die Datei zu speichern.

Das MediaElement-Steuerelement unterstützt Smooth Streaming-Inhalte nicht von sich aus. Um die Smooth Streaming-Unterstützung zu aktivieren, müssen Sie die Dateierweiterung und dem MIME-Typ des Smooth Streaming-Bytedatenstrom-Handlers registrieren. Verwenden Sie dazu die MediaExtensionManager.RegisterByteStremHandler-Methode des Windows.Media-Namespace.

In dieser XAML-Datei sind den Steuerelementen einige Ereignishandler zugeordnet. Sie müssen diese Ereignishandler definieren.

**So ändern Sie die CodeBehind-Datei**

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **MainPage.xaml**, und klicken Sie anschließend auf **Code anzeigen**.
2.  Fügen Sie am Anfang der Datei die folgende using-Anweisung hinzu:

        using Windows.Media;

3.  Fügen Sie am Anfang der **MainPage**-Klasse das folgende Datenmember hinzu:

        private MediaExtensionManager extensions = new MediaExtensionManager();

4.  Fügen Sie am Ende des **MainPage**-Konstruktors die beiden folgenden Zeilen hinzu:

        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");

5.  Fügen Sie am Ende der **MainPage**-Klasse den folgenden Code ein:

        #region UI Button Click Events
        private void btnPlay_Click(object sender, RoutedEventArgs e)
        {
            mediaElement.Play();
            txtStatus.Text = "MediaElement is playing ...";
        }
            
        private void btnPause_Click(object sender, RoutedEventArgs e)
        {
            mediaElement.Pause();
            txtStatus.Text = "MediaElement is paused";
        }
            
        private void btnSetSource_Click(object sender, RoutedEventArgs e)
        {
            sliderProgress.Value = 0;
            mediaElement.Source = new Uri(txtMediaSource.Text);
            
            if (chkAutoPlay.IsChecked == true)
            {
                txtStatus.Text = "MediaElement is playing ...";
            }
            else
            {
                txtStatus.Text = "Click the Play button to play the media source.";
            }
        }
            
        private void btnStop_Click(object sender, RoutedEventArgs e)
        {
            mediaElement.Stop();
            txtStatus.Text = "MediaElement is stopped";
        }
            
        private void sliderProgress_PointerPressed(object sender, PointerRoutedEventArgs e)
        {
            txtStatus.Text = "Seek to position " + sliderProgress.Value;
            mediaElement.Position = new TimeSpan(0, 0, (int)(sliderProgress.Value));
        }
        #endregion

    Der Ereignishandler sliderProgress\_PointerPressed wird hier definiert. Damit er einwandfrei funktioniert, sind einige weitere Schritte erforderlich, die in der nächsten Lektion dieses Lernprogramms behandelt werden.

6.  Drücken Sie **STRG+S**, um die Datei zu speichern.

Die fertige CodeBehind-Datei sieht folgendermaßen aus:

![Codeansicht der Smooth Streaming Windows Store-Anwendung in Visual Studio](./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png)

**So kompilieren und testen Sie die Anwendung**

1.  Klicken Sie im Menü **Build** auf **Konfigurations-Manager**.
2.  Ändern Sie die **Aktive Projektmappenplattform**, sodass sie Ihrer Entwicklungsplattform entspricht.
3.  Drücken Sie **F6**, um das Projekt zu kompilieren.
4.  Drücken Sie **F5**, um die Anwendung auszuführen.
5.  Am Anfang der Anwendung können Sie entweder den standardmäßigen Smooth Streaming-URL verwenden oder einen anderen URL eingeben.
6.  Klicken Sie auf **Set Source**. Da **Auto Play** standardmäßig aktiviert ist, werden die Medien automatisch wiedergegeben. Sie können die Wiedergabe der Medien mit den Schaltflächen **Play**, **Pause** und **Stop** steuern. Die Lautstärke der Medien steuern Sie mit dem vertikalen Schieberegler. Der horizontale Schieberegler zum Steuern des Medienstatus ist jedoch noch nicht voll implementiert.

Sie haben Lektion 1 abgeschlossen. In dieser Lektion haben Sie ein MediaElement-Steuerelement für die Wiedergabe von Smooth Streaming-Inhalten verwendet. In der nächsten Lektion fügen Sie einen Schieberegler hinzu, um den Fortschritt des Smooth Streaming-Inhalts zu steuern.

Lektion 2: Hinzufügen eines Schiebereglers zum Steuern des Medienstatus
=======================================================================

In Lektion 1 haben Sie eine Windows Store-Anwendung mit einem MediaElement-XAML-Steuerelement für die Wiedergabe von Smooth Streaming-Medieninhalten erstellt. Es umfasst einige grundlegende Medienfunktionen wie Start, Stopp und Pause. In dieser Lektion fügen Sie ein Schieberegler-Steuerelement zum Steuern der Anwendung hinzu.

In diesem Lernprogramm verwenden wir einen Timer, um die Schiebereglerposition je nach der aktuellen Position des MediaElement-Steuerelements zu aktualisieren. Bei Live-Inhalten müssen auch die Start- und Endzeit des Schiebereglers aktualisiert werden. Dies kann besser im Ereignis "Update der adaptiven Quelle" verarbeitet werden.

Medienquellen sind Objekte, die Mediendaten generieren. Der Source Resolver nimmt einen URL oder Bytedatenstrom und erstellt die entsprechende Medienquelle für den Inhalt. Der Source Resolver ist das Standardverfahren, nach dem Anwendungen Medienquellen erstellen.

Diese Lektion enthält die folgenden Prozeduren:

1.  Registrieren des Smooth Streaming-Handlers
2.  Hinzufügen der Ereignishandler für die Ebene des Managers der adaptiven Quelle
3.  Hinzufügen der Ereignishandler für die Ebene der adaptiven Quelle
4.  Hinzufügen von MediaElement-Ereignishandlern
5.  Hinzufügen von Code für den Schieberegler
6.  Kompilieren und Testen der Anwendung

**So registrieren Sie den Smooth Streaming-Bytedatenstrom-Handler und übergeben den Eigenschaftssatz**

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **MainPage.xaml**, und klicken Sie anschließend auf **Code anzeigen**.
2.  Fügen Sie am Anfang der Datei die folgende using-Anweisung hinzu:

        using Microsoft.Media.AdaptiveStreaming;

3.  Fügen Sie am Anfang der MainPage-Klasse die folgenden Datenmember hinzu:

        private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
        private IAdaptiveSourceManager adaptiveSourceManager;

4.  Fügen Sie im **MainPage**-Konstruktor den folgenden Code nach der Zeile **this.Initialize Components();** und den in der vorherigen Lektion geschriebenen Registrierungscodezeilen hinzu:

        // Ruft die Standardinstanz von AdaptiveSourceManager ab, die Smooth 
        // Streaming-Medienquellen verwaltet.
        adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
        // Legt den Eigenschaftsschlüsselwert auf die AdaptiveSourceManager-Standardinstanz fest.
        // {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" muss hartcodiert sein.
        propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;

5.  Ändern Sie innerhalb des **MainPage**-Konstruktors die beiden RegisterByteStreamHandler-Methoden, indem Sie die forth-Parameter hinzufügen:

        // Registriert den Smooth Streaming-Bytedatenstrom-Handler für die Erweiterung ".ism" und 
        // die MIME-Typen "text/xml" sowie "application/vnd.ms-ss" und übergibt das Eigenschaftsset. 
        // http://*.ism/manifest-URI-Ressourcen werden vom Bytedatenstrom-Handler aufgelöst.
        extensions.RegisterByteStreamHandler(
            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "text/xml", 
            propertySet );
        extensions.RegisterByteStreamHandler(
            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "application/vnd.ms-sstr+xml", 
        propertySet );

6.  Drücken Sie **STRG+S**, um die Datei zu speichern.

**So fügen Sie den Ereignishandler für die Ebene des Managers der adaptiven Quelle hinzu**

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **MainPage.xaml**, und klicken Sie anschließend auf **Code anzeigen**.
2.  Fügen Sie innerhalb der **MainPage**-Klasse das folgende Datenmember hinzu:

        private AdaptiveSource adaptiveSource = null;

3.  Fügen Sie am Ende der **MainPage**-Klasse den folgenden Ereignishandler hinzu:

        #region Adaptive Source Manager Level Events
        private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
        {
            adaptiveSource = args.AdaptiveSource;
        }
        #endregion Adaptive Source Manager Level Events

4.  Fügen Sie am Ende des **MainPage**-Konstruktors die folgende Zeile hinzu, um das Ereignis "Adaptive Quelle öffnen" zu abonnieren:

    adaptiveSourceManager.AdaptiveSourceOpenedEvent += new AdaptiveSourceOpenedEventHandler(mediaElement\_AdaptiveSourceOpened);

5.  Drücken Sie **STRG+S**, um die Datei zu speichern.

**So fügen Sie die Ereignishandler für die Ebene der adaptiven Quelle hinzu**

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **MainPage.xaml**, und klicken Sie anschließend auf **Code anzeigen**.
2.  Fügen Sie innerhalb der **MainPage**-Klasse das folgende Datenmember hinzu:

        private AdaptiveSourceStatusUpdatedEventArgs adaptiveSourceStatusUpdate; 
        private Manifest manifestObject;

3.  Fügen Sie am Ende der **MainPage**-Klasse die folgenden Ereignishandler hinzu:

        #region Adaptive Source Level Events
        private void mediaElement_ManifestReady(AdaptiveSource sender, ManifestReadyEventArgs args)
        {
            adaptiveSource = args.AdaptiveSource;
            manifestObject = args.AdaptiveSource.Manifest;
        }
            
        private void mediaElement_AdaptiveSourceStatusUpdated(AdaptiveSource sender, AdaptiveSourceStatusUpdatedEventArgs args)
        {
            adaptiveSourceStatusUpdate = args;
        }
            
        private void mediaElement_AdaptiveSourceFailed(AdaptiveSource sender, AdaptiveSourceFailedEventArgs args)
        {
            txtStatus.Text = "Error: " + args.HttpResponse;
        }
        #endregion Adaptive Source Level Events

4.  Fügen Sie am Ende der **mediaElement AdaptiveSourceOpened**-Methode den folgenden Code hinzu, um die Ereignisse zu abonnieren:

        adaptiveSource.ManifestReadyEvent +=
                    mediaElement_ManifestReady;
        adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 
            mediaElement_AdaptiveSourceStatusUpdated;
        adaptiveSource.AdaptiveSourceFailedEvent += 
            mediaElement_AdaptiveSourceFailed;

5.  Drücken Sie **STRG+S**, um die Datei zu speichern.

Die gleichen Ereignisse stehen auch auf der Ebene des Managers für adaptive Quellen zur Verfügung, die zum Verarbeiten von Funktionen verwendet werden kann, die allen Medienelementen in der App gemein sind. Jede adaptive Quelle enthält eigene Ereignisse, und alle Ereignisse der adaptiven Quelle werden vom Manager der adaptiven Quelle weitergegeben.

**So fügen Sie Ereignishandler für Medienelemente hinzu**

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **MainPage.xaml**, und klicken Sie anschließend auf **Code anzeigen**.
2.  Fügen Sie am Ende der **MainPage**-Klasse die folgenden Ereignishandler hinzu:

        #region Media Element Event Handlers 
        private void MediaOpened(object sender, RoutedEventArgs e)
        {
            txtStatus.Text = "MediaElement opened";
        }
            
        private void MediaFailed(object sender, ExceptionRoutedEventArgs e)
        {
            txtStatus.Text= "MediaElement failed: " + e.ErrorMessage;
        }
            
        private void MediaEnded(object sender, RoutedEventArgs e)
        {
            txtStatus.Text ="MediaElement ended.";
        }
        #endregion Media Element Event Handlers

3.  Fügen Sie am Ende des **MainPage**-Konstruktors den folgenden Code hinzu, um die Ereignisse zu abonnieren:

        mediaElement.MediaOpened += MediaOpened;
        mediaElement.MediaEnded += MediaEnded;
        mediaElement.MediaFailed += MediaFailed;

4.  Drücken Sie **STRG+S**, um die Datei zu speichern.

**So fügen Sie Code für den Schieberegler hinzu**

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **MainPage.xaml**, und klicken Sie anschließend auf **Code anzeigen**.
2.  Fügen Sie am Anfang der Datei die folgende using-Anweisung hinzu:

        using Windows.UI.Core;

3.  Fügen Sie innerhalb der **MainPage**-Klasse die folgenden Datenmember hinzu:

        public static CoreDispatcher _dispatcher;
        private DispatcherTimer sliderPositionUpdateDispatcher;

4.  Fügen Sie am Ende des **MainPage**-Konstruktors den folgenden Code hinzu:

        _dispatcher = Window.Current.Dispatcher;
        PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
        sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    

5.  Fügen Sie am Ende der **MainPage**-Klasse den folgenden Code hinzu:

        #region sliderMediaPlayer
        private double SliderFrequency(TimeSpan timevalue)
        {
            long absvalue = 0;
            double stepfrequency = -1;
            
            if (manifestObject != null)
            {
                absvalue = manifestObject.Duration - (long)manifestObject.StartTime;
            }
            else
            {
                absvalue = mediaElement.NaturalDuration.TimeSpan.Ticks;
            }
            
            TimeSpan totalDVRDuration = new TimeSpan(absvalue);
            
            if (totalDVRDuration.TotalMinutes >= 10 && totalDVRDuration.TotalMinutes < 30)
            {
               stepfrequency = 10;
            }
            else if (totalDVRDuration.TotalMinutes >= 30 
                     && totalDVRDuration.TotalMinutes < 60)
            {
                stepfrequency = 30;
            }
            else if (totalDVRDuration.TotalHours >= 1)
            {
                stepfrequency = 60;
            }
            
            return stepfrequency;
        }
            
        void updateSliderPositionoNTicks(object sender, object e)
        {
            sliderProgress.Value = mediaElement.Position.TotalSeconds;
        }
            
        public void setupTimer()
        {
            sliderPositionUpdateDispatcher = new DispatcherTimer();
            sliderPositionUpdateDispatcher.Interval = new TimeSpan(0, 0, 0, 0, 300);
            startTimer();
        }

        public void startTimer()
        {
            sliderPositionUpdateDispatcher.Tick += updateSliderPositionoNTicks;
            sliderPositionUpdateDispatcher.Start();
        }
            
        // Start- und Endzeit des Schiebereglers müssen bei Live-Inhalten aktualisiert werden
        public async void setSliderStartTime(long startTime)
        {
            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.StartTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Minimum = absvalue;
            });
        }
            
        // Start- und Endzeit des Schiebereglers müssen bei Live-Inhalten aktualisiert werden
        public async void setSliderEndTime(long startTime)
        {
            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Maximum = absvalue;
            });
        }
        #endregion sliderMediaPlayer

    **Hinweis:** Mit CoreDispatcher werden Änderungen am UI-Thread aus dem Nicht-UI-Thread übernommen. Bei einem Engpass des Dispatcher-Threads kann der Entwickler festlegen, das vom Dispatcher bereitgestellte UI-Element zu verwenden, das aktualisiert werden soll. Beispiel:

        await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 
          timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
        double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 
          sliderProgress.Maximum = absvalue; }); 

6.  Fügen Sie am Ende der **mediaElement\_AdaptiveSourceStatusUpdated**-Methode den folgenden Code hinzu:

        setSliderStartTime(args.StartTime);
        setSliderEndTime(args.EndTime);

7.  Fügen Sie am Ende der **MediaOpened**-Methode den folgenden Code hinzu:

    sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan); sliderProgress.Width = mediaElement.Width; setupTimer();

8.  Drücken Sie **STRG+S**, um die Datei zu speichern.

**So kompilieren und testen Sie die Anwendung**

1.  Drücken Sie **F6**, um das Projekt zu kompilieren.
2.  Drücken Sie **F5**, um die Anwendung auszuführen.
3.  Am Anfang der Anwendung können Sie entweder den standardmäßigen Smooth Streaming-URL verwenden oder einen anderen URL eingeben.
4.  Klicken Sie auf **Set Source**.
5.  Testen Sie den Schieberegler.

Sie haben Lektion 2 abgeschlossen. In dieser Lektion haben Sie der Anwendung einen Schieberegler hinzugefügt.

Lektion 3: Auswählen von Smooth Streaming-Datenströmen
======================================================

Smooth Streaming kann Inhalte mit Audiospuren in mehreren Sprachen streamen, die von den Betrachtern ausgewählt werden können. In dieser Lektion geben Sie den Betrachtern die Möglichkeit, Datenströme auszuwählen. Diese Lektion enthält die folgenden Prozeduren:

1.  Ändern der XAML-Datei
2.  Ändern der CodeBehind-Datei
3.  Kompilieren und Testen der Anwendung

**So ändern Sie die XAML-Datei**

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **MainPage.xaml**, und klicken Sie anschließend auf **Ansicht-Designer**.
2.  Suchen Sie &lt;Grid.RowDefinitions\>, und ändern Sie die RowDefinitions folgendermaßen:

         <Grid.RowDefinitions>            
             <RowDefinition Height="20"/>
             <RowDefinition Height="50"/>
             <RowDefinition Height="100"/>
             <RowDefinition Height="80"/>
             <RowDefinition Height="50"/>
         </Grid.RowDefinitions>

3.  Fügen Sie innerhalb der Tags &lt;Grid\>&lt;/Grid\> den folgenden Code hinzu, um ein Listenfeld zu definieren, sodass die Benutzer die Liste verfügbarer Datenströme sehen und Datenströme auswählen können:

         <Grid Name="gridStreamAndBitrateSelection" Grid.Row="3">
             <Grid.RowDefinitions>
                 <RowDefinition Height="300"/>
             </Grid.RowDefinitions>
             <Grid.ColumnDefinitions>
                 <ColumnDefinition Width="250*"/>
                 <ColumnDefinition Width="250*"/>
             </Grid.ColumnDefinitions>
             <StackPanel Name="spStreamSelection" Grid.Row="1" Grid.Column="0">
                 <StackPanel Orientation="Horizontal">
                     <TextBlock Name="tbAvailableStreams" Text="Available Streams:" FontSize="16" VerticalAlignment="Center"></TextBlock>
                     <Button Name="btnChangeStreams" Content="Submit" Click="btnChangeStream_Click"/>
                 </StackPanel>
                 <ListBox x:Name="lbAvailableStreams" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                    ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
                     <ListBox.ItemTemplate>
                         <DataTemplate>
                            <CheckBox Content="{Binding Name}" IsChecked="{Binding isChecked, Mode=TwoWay}" />
                         </DataTemplate>
                     </ListBox.ItemTemplate>
                 </ListBox>
             </StackPanel>
         </Grid>

4.  Drücken Sie **STRG+S**, um die Änderungen zu speichern.

**So ändern Sie die CodeBehind-Datei**

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **MainPage.xaml**, und klicken Sie anschließend auf **Code anzeigen**.
2.  Fügen Sie im SSPlayer-Namespace eine neue Klasse hinzu: 
  \#region class Stream

         public class Stream
         {
             private IManifestStream stream;
             public bool isCheckedValue;
             public string name;

             public string Name
             {
                 get { return name; }
                 set { name = value; }
             }

             public IManifestStream ManifestStream
             {
                 get { return stream; }
                 set { stream = value; }
             }

             public bool isChecked
             {
                 get { return isCheckedValue; }
                 set
                 {
                     // Den Videodatenstrom immer prüfen lassen.
                     if (stream.Type == MediaStreamType.Video)
                     {
                         isCheckedValue = true;
                     }
                     else
                     {
                         isCheckedValue = value;
                     }
                 }
             }

             public Stream(IManifestStream streamIn)
             {
                 stream = streamIn;
                 name = stream.Name;
             }
         }
         #endregion class Stream

3.  Fügen Sie am Anfang der MainPage-Klasse die folgenden Variablendefinitionen hinzu:

         private List<Stream> availableStreams;
         private List<Stream> availableAudioStreams;
         private List<Stream> availableTextStreams;
         private List<Stream> availableVideoStreams;

4.  Fügen Sie innerhalb der MainPage-Klasse den folgenden Bereich hinzu:

         #region stream selection
         ///<summary>
         ///Funktion zur Auswahl von Datenströmen aus verfügbaren IManifestStream-Datenströmen
         /// </summary>
            
         // Diese Funktion wird über den Ereignishandler mediaElement_ManifestReady aufgerufen, 
         // um die Datenströme abzurufen und die lokalen Datenmember mit ihnen zu füllen.
         public void getStreams(Manifest manifestObject)
         {
             availableStreams = new List<Stream>();
             availableVideoStreams = new List<Stream>();
             availableAudioStreams = new List<Stream>();
             availableTextStreams = new List<Stream>();
            
             try
             {
                 for (int i = 0; i<manifestObject.AvailableStreams.Count; i++)
                 {
                     Stream newStream = new Stream(manifestObject.AvailableStreams[i]);
                     newStream.isChecked = false;
            
                     //Die Datenstromlisten entsprechend den Typen füllen
                     availableStreams.Add(newStream);
            
                     switch (newStream.ManifestStream.Type)
                     {
                         case MediaStreamType.Video:
                             availableVideoStreams.Add(newStream);
                             break;
                         case MediaStreamType.Audio:
                             availableAudioStreams.Add(newStream);
                             break;
                         case MediaStreamType.Text:
                             availableTextStreams.Add(newStream);
                             break;
                     }
            
                     // Aus dem Manifest die ausgewählten Standardstreams auswählen.
                     for (int j = 0; j<manifestObject.SelectedStreams.Count; j++)
                     {
                         string selectedStreamName = manifestObject.SelectedStreams[j].Name;
                         if (selectedStreamName.Equals(newStream.Name))
                         {
                             newStream.isChecked = true;
                             break;
                         }
                     }
                 }
             }
             catch (Exception e)
             {
                 txtStatus.Text = "Error: " + e.Message;
             }
         }
            
         // Mit dieser Funktion wird das Listenfeld "ItemSource" festgelegt.
         private async void refreshAvailableStreamsListBoxItemSource()
         {
             try
             {
                 //Die Datenstrom-Kontrollkästchenliste in der Benutzeroberfläche aktualisieren
                 await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                     => { lbAvailableStreams.ItemsSource = availableStreams; });
             }
             catch (Exception e)
             {
                 txtStatus.Text = "Error: " + e.Message;
             }
         }
            
         // Diese Funktion erstellt eine Liste ausgewählter Datenströme
         private void createSelectedStreamsList(List<IManifestStream> selectedStreams)
         {
             bool isOneVideoSelected = false;
             bool isOneAudioSelected = false;
            
             // Nur ein Videodatenstrom kann ausgewählt werden
             for (int j = 0; j<availableVideoStreams.Count; j++)
             {
                 if (availableVideoStreams[j].isChecked && (!isOneVideoSelected))
                 {
                     selectedStreams.Add(availableVideoStreams[j].ManifestStream);
                     isOneVideoSelected = true;
                 }
             }
            
             // Den ersten Videodatenstrom in der Liste auswählen, wenn kein Videodatenstrom ausgewählt ist
             if (!isOneVideoSelected)
             {
                 availableVideoStreams[0].isChecked = true;
                 selectedStreams.Add(availableVideoStreams[0].ManifestStream);
             }
            
             // Nur ein Audiodatenstrom kann ausgewählt werden
             for (int j = 0; j<availableAudioStreams.Count; j++)
             {
                 if (availableAudioStreams[j].isChecked && (!isOneAudioSelected))
                 {
                     selectedStreams.Add(availableAudioStreams[j].ManifestStream);
                     isOneAudioSelected = true;
                     txtStatus.Text = "The audio stream is changed to " + availableAudioStreams[j].ManifestStream.Name;
                 }
             }
            
             // Den ersten Audiodatenstrom in der Liste auswählen, wenn kein Audiodatenstrom ausgewählt ist
             if (!isOneAudioSelected)
             {
                 availableAudioStreams[0].isChecked = true;
                 selectedStreams.Add(availableAudioStreams[0].ManifestStream);
             }
            
             // Mehrere Textstreams werden unterstützt.
             for (int j = 0; j < availableTextStreams.Count; j++)
             {
                 if (availableTextStreams[j].isChecked)
                 {
                     selectedStreams.Add(availableTextStreams[j].ManifestStream);
                 }
             }
         }
            
         // Datenströme in einer Smooth Streaming-Präsentation mit mehreren Videodatenströmen wechseln.
         private async void changeStreams(List<IManifestStream> selectStreams)
         {
             try
             {
                 IReadOnlyList<IStreamChangedResult> returnArgs =
                     await manifestObject.SelectStreamsAsync(selectStreams);
             }
             catch (Exception e)
             {
                 txtStatus.Text = "Error: " + e.Message;
             }
         }
         #endregion stream selection

5.  Suchen Sie die mediaElement\_ManifestReady-Methode, und fügen Sie den folgenden Code am Ende der Funktion hinzu:

         getStreams(manifestObject);
         refreshAvailableStreamsListBoxItemSource();

    Wenn das MediaElement-Manifest fertig ist, ruft der Code eine Liste der verfügbaren Datenströme ab und füllt das Listenfeld in der Benutzeroberfläche mit den entsprechenden Einträgen.

6.  Suchen Sie innerhalb der MainPage-Klasse den Bereich "UI Buttons Click Events", und fügen Sie die folgende Funktionsdefinition hinzu:

         private void btnChangeStream_Click(object sender, RoutedEventArgs e)
         {
             List<IManifestStream> selectedStreams = new List<IManifestStream>();

             // Eine Liste der ausgewählten Datenströme erstellen
             createSelectedStreamsList(selectedStreams);

             // Datenströme in der Präsentation wechseln
             changeStreams(selectedStreams);
         }

**So kompilieren und testen Sie die Anwendung**

1.  Drücken Sie **F6**, um das Projekt zu kompilieren.
2.  Drücken Sie **F5**, um die Anwendung auszuführen.
3.  Am Anfang der Anwendung können Sie entweder den standardmäßigen Smooth Streaming-URL verwenden oder einen anderen URL eingeben.
4.  Klicken Sie auf **Set Source**.
5.  Die Standardsprache ist "audio\_eng". Versuchen Sie, zwischen "audio\_eng" und "audio\_es" zu wechseln. Jedes Mal, wenn Sie einen neuen Datenstrom auswählen, müssen Sie auf "Senden" klicken.

Sie haben Lektion 3 abgeschlossen. In dieser Lektion haben Sie die Funktion zur Auswahl von Datenströmen hinzugefügt.

Lektion 4: Auswählen von Smooth Streaming-Spuren
================================================

Eine Smooth Streaming-Präsentation kann mehrere Videodateien enthalten, die in unterschiedlichen Qualitätsstufen (Bitraten) und Auflösungen codiert sind. In dieser Lektion geben Sie den Benutzern die Möglichkeit, Spuren auszuwählen. Diese Lektion enthält die folgenden Prozeduren:

1.  Ändern der XAML-Datei
2.  Ändern der CodeBehind-Datei
3.  Kompilieren und Testen der Anwendung

**So ändern Sie die XAML-Datei**

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **MainPage.xaml**, und klicken Sie anschließend auf **Ansicht-Designer**.
2.  Suchen Sie das &lt;Grid\>-Tag mit dem Namen **gridStreamAndBitrateSelection**, und fügen Sie am Ende des Tags den folgenden Code hinzu:

         <StackPanel Name="spBitRateSelection" Grid.Row="1" Grid.Column="1">
          <StackPanel Orientation="Horizontal">
              <TextBlock Name="tbBitRate" Text="Available Bitrates:" FontSize="16" VerticalAlignment="Center"/>
              <Button Name="btnChangeTracks" Content="Submit" Click="btnChangeTrack_Click" />
          </StackPanel>
          <ListBox x:Name="lbAvailableVideoTracks" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                   ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
              <ListBox.ItemTemplate>
                  <DataTemplate>
                      <CheckBox Content="{Binding Bitrate}" IsChecked="{Binding isChecked, Mode=TwoWay}"/>
                  </DataTemplate>
              </ListBox.ItemTemplate>
          </ListBox>
         </StackPanel>

3.  Drücken Sie **STRG+S**, um die Änderungen zu speichern.

**So ändern Sie die CodeBehind-Datei**

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **MainPage.xaml**, und klicken Sie anschließend auf **Code anzeigen**.
2.  Fügen Sie im SSPlayer-Namespace eine neue Klasse hinzu:

         #region class Track
         public class Track
         {
             private IManifestTrack trackInfo;
             public string _bitrate;
             public bool isCheckedValue;

             public IManifestTrack TrackInfo
             {
                 get { return trackInfo; }
                 set { trackInfo = value; }
             }

             public string Bitrate
             {
                 get { return _bitrate; }
                 set { _bitrate = value; }
             }

             public bool isChecked
             {
                 get { return isCheckedValue; }
                 set
                 {
                     isCheckedValue = value;
                 }
             }

             public Track(IManifestTrack trackInfoIn)
             {
                 trackInfo = trackInfoIn;
                 _bitrate = trackInfoIn.Bitrate.ToString();
             }
             //public Track() { }
         }
         #endregion class Track

3.  Fügen Sie am Anfang der MainPage-Klasse die folgenden Variablendefinitionen hinzu:

         private List<Track> availableTracks;

4.  Fügen Sie innerhalb der MainPage-Klasse den folgenden Bereich hinzu:

         #region track selection
         /// <summary>
         /// Funktion zur Auswahl von Videodatenströmen
         /// </summary>

         /// Diese Funktion ruft die Spuren des ausgewählten Videodatenstroms ab
         public void getTracks(Manifest manifestObject)
         {
             availableTracks = new List<Track>();

             IManifestStream videoStream = getVideoStream();
             IReadOnlyList<IManifestTrack> availableTracksLocal = videoStream.AvailableTracks;
             IReadOnlyList<IManifestTrack> selectedTracksLocal = videoStream.SelectedTracks;

             try
             {
                 for (int i = 0; i < availableTracksLocal.Count; i++)
                 {
                     Track thisTrack = new Track(availableTracksLocal[i]);
                     thisTrack.isChecked = true;

                     for (int j = 0; j < selectedTracksLocal.Count; j++)
                     {
                         string selectedTrackName = selectedTracksLocal[j].Bitrate.ToString();
                         if (selectedTrackName.Equals(thisTrack.Bitrate))
                         {
                             thisTrack.isChecked = true;
                             break;
                         }
                     }
                     availableTracks.Add(thisTrack);
                 }
             }
             catch (Exception e)
             {
                 txtStatus.Text = e.Message;
             }
         }

         // Diese Funktion ruft den Videodatenstrom ab, der derzeit wiedergegeben wird
         private IManifestStream getVideoStream()
         {
             IManifestStream videoStream = null;
             for (int i = 0; i < manifestObject.SelectedStreams.Count; i++)
             {
                 if (manifestObject.SelectedStreams[i].Type == MediaStreamType.Video)
                 {
                     videoStream = manifestObject.SelectedStreams[i];
                     break;
                 }
             }
             return videoStream;
         }

         // Mit dieser Funktion wird das UI-Listenfeld "ItemSource" festgelegt.
         private async void refreshAvailableTracksListBoxItemSource()
         {
             try
             {
                 // Die Spur-Kontrollkästchenliste in der Benutzeroberfläche aktualisieren 
                 await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                     => { lbAvailableVideoTracks.ItemsSource = availableTracks; });
             }
             catch (Exception e)
             {
                 txtStatus.Text = "Error: " + e.Message;
             }        
         }

         // Diese Funktion erstellt eine Liste der ausgewählten Spuren.
         private void createSelectedTracksList(List<IManifestTrack> selectedTracks)
         {
             // Eine Liste ausgewählter Spuren erstellen
             for (int j = 0; j < availableTracks.Count; j++)
             {
                 if (availableTracks[j].isCheckedValue == true)
                 {
                     selectedTracks.Add(availableTracks[j].TrackInfo);
                 }
             }
         }

         // Diese Funktion wählt die Spuren je nach Benutzerauswahl aus 
         private void changeTracks(List<IManifestTrack> selectedTracks)
         {
             IManifestStream videoStream = getVideoStream();
             try
             {
                 videoStream.SelectTracks(selectedTracks);
             }
             catch (Exception ex)
             {
                 txtStatus.Text = ex.Message;
             }
         }
         #endregion track selection

5.  Suchen Sie die mediaElement\_ManifestReady-Methode, und fügen Sie den folgenden Code am Ende der Funktion hinzu:

         getTracks(manifestObject);
         refreshAvailableTracksListBoxItemSource();

6.  Suchen Sie innerhalb der MainPage-Klasse den Bereich "UI Buttons Click Events", und fügen Sie die folgende Funktionsdefinition hinzu:

         private void btnChangeStream_Click(object sender, RoutedEventArgs e)
         {
             List<IManifestStream> selectedStreams = new List<IManifestStream>();

             // Eine Liste der ausgewählten Datenströme erstellen
             createSelectedStreamsList(selectedStreams);

             // Datenströme in der Präsentation wechseln
             changeStreams(selectedStreams);
         }

**So kompilieren und testen Sie die Anwendung**

1.  Drücken Sie **F6**, um das Projekt zu kompilieren.
2.  Drücken Sie **F5**, um die Anwendung auszuführen.
3.  Am Anfang der Anwendung können Sie entweder den standardmäßigen Smooth Streaming-URL verwenden oder einen anderen URL eingeben.
4.  Klicken Sie auf **Set Source**.
5.  Standardmäßig sind alle Spuren des Videodatenstroms ausgewählt. Um mit Änderungen der Bitrate zu experimentieren, können Sie die niedrigste und danach die höchste verfügbare Bitrate auswählen. Nach jedem Wechsel müssen Sie auf "Senden" klicken. Sie können sehen, dass sich die Qualität des Videos ändert.

Sie haben Lektion 4 abgeschlossen. In dieser Lektion haben Sie die Funktion zur Auswahl von Spuren hinzugefügt.

Weitere Ressourcen:
===================

-   [How to build a Smooth Streaming Windows 8 JavaScript application with advanced features (Erstellen einer Smooth Streaming Windows 8 JavaScript-Anwendung mit erweiterten Funktionen, in englischer Sprache)](http://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
-   [Smooth Streaming Technical Overview (Smooth Streaming – Technische Übersicht, in englischer Sprache)](http://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)
