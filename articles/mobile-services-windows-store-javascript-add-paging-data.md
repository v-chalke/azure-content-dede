<properties pageTitle="Add paging to data (JavaScript) - Azure Mobile Services" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Windows Store JavaScript app from Mobile Services." metaCanonical="http://www.windowsazure.com/de-de/develop/mobile/tutorials/add-paging-to-data-dotnet/" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="" solutions="" manager="" editor="" />

Verfeinern von Mobile Services-Abfragen mit Paging
==================================================

> [AZURE.SELECTOR-LIST (Platform | Backend )] 
> - [(Windows Store C\# | .NET)](mobile-services-dotnet-backend-windows-store-dotnet-add-paging-data.md) 
> - [(Windows Store C\# | JavaScript)](mobile-services-windows-store-dotnet-add-paging-data.md) 
> - [(Windows Store JavaScript | .NET)](mobile-services-dotnet-backend-windows-store-javascript-add-paging-data.md) 
> - [(Windows Store JavaScript | JavaScript)](mobile-services-windows-store-javascript-add-paging-data.md) 
> - [(Windows Phone | .NET)](mobile-services-dotnet-backend-windows-phone-add-paging-data) 
> - [(Windows Phone | JavaScript)](mobile-services-windows-phone-add-paging-data) 
> - [(iOS | JavaScript)](mobile-services-ios-add-paging-data) 
> - [(Android | JavaScript)](mobile-services-android-add-paging-data) 
> - [(HTML | .NET)](mobile-services-html-add-paging-data) 
> - [(Xamarin iOS | .NET)](partner-xamarin-mobile-services-ios-add-paging-data) 
> - [(Xamarin Android | .NET)](partner-xamarin-mobile-services-android-add-paging-data)

In diesem Thema erfahren Sie, wie Sie die aus Azure Mobile Services an Ihre Windows Store-App zurückgegebene Datenmenge mithilfe von Paging steuern können. Dieses Lernprogramm verwendet die Abfragemethoden **Take** und **Skip** im Client, um bestimmte "Seiten" von Daten abzufragen.

> [WACOM.NOTE]Mobile Services verwendet standardmäßig eine Seitengröße von 50 Elementen pro Antwort, um mobile Clients nicht mit Daten zu überfluten. Durch Angabe der Seitengröße können Sie bis zu 1.000 Elemente in einer Antwort abfragen.

Dieses Lernprogramm basiert auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Daten](/de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-data/). Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zumindest das erste Lernprogramm aus der Daten-Reihe abschließen, [Erste Schritte mit Daten](/de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-data/).

[WACOM.INCLUDE [mobile-services-javascript-paging](../includes/mobile-services-javascript-paging.md)]

Nächste Schritte
----------------

Dies bildet den Abschluss der Lernprogramm-Reihe über Grundlagen mit Daten in Mobile Services. Weitere Informationen zu Mobile Services:

-   [Erste Schritte mit der Authentifizierung](/de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-users/)
    Lernen Sie, wie Benutzer Ihrer App mit einem Windows-Konto authentifiziert werden.

-   [Erste Schritte mit Pushbenachrichtigungen](/de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-push/)
    Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

