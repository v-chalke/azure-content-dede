<properties 
	pageTitle="Lizenzierung des Microsoft® Smooth Streaming Client Porting Kit" 
	description="Weitere Informationen zur Lizenzierung des Microsoft® Smooth Streaming Client Porting Kit" 
	services="media-services" 
	documentationCenter="" 
	authors="xpouyat,vsood" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/04/2016"  
	ms.author="xpouyat"/>

#Lizenzierung des Microsoft® Smooth Streaming Client Porting Kit

##Übersicht

Test Microsoft Smooth Streaming Client Porting Kit (**SSPK**) ist eine Smooth Streaming Client-Implementierung, die Herstellern eingebetteter Geräte, Kabel- und Mobilnetzbetreibern, Inhaltsdienstanbietern, Mobilgeräteherstellern, unabhängigen Softwareanbietern (ISV) und Lösungsanbietern beim Anbieten von Produkten und Dienstleistungen zum Streamen adaptiver Streaminginhalte im Smooth Streaming-Format helfen soll. SSPK ist eine geräte- und plattformunabhängige Implementierung von Smooth Streaming Client, die vom Lizenznehmer auf jedes Gerät und jede Plattform portiert werden kann.

Nachfolgend finden Sie eine allgemeine Architektur. IIS Smooth Streaming Porting Kit ist die Smooth Streaming Client-Implementierung von Microsoft und beinhaltet die gesamte grundlegende Logik für die Wiedergabe vom Smooth Streaming-Inhalten. Diese werden dann von Partnern für ein spezifisches Gerät oder eine spezifische Plattform portiert, indem entsprechende Schnittstellen implementiert werden.

![SSPK](./media/media-services-sspk/sspk-arch.png)

##Beschreibung

SSPK ist zu Bedingungen lizenziert, die einen großen Mehrwert für Ihr Unternehmen schaffen. Die SSPK-Lizenz umfasst:

- Smooth Streaming Porting Kit-Quelle in C++ 
  - implementiert Smooth Streaming Client-Funktionen
  - bietet Formatanalyse, Heuristik, Pufferungslogik usw.
- Playeranwendungs-APIs 
  -	Programmierschnittstellen für die Interaktion mit einer Medienwiedergabeanwendung
- Plattformabstraktionsschicht (PAL)-Schnittstelle 
  -	Programmierschnittstellen für die Interaktion mit dem Betriebssystem (Threads, Sockets)
- Hardwareabstraktionsschicht (HAL)-Schnittstelle 
  -	Programmierschnittstellen für die Interaktion mit Hardware-A/V-Decodern (Decodieren, Rendern)
- Digital Rights Management (DRM)-Schnittstelle 
  -	Programmierschnittstellen für die Handhabung von DRM über die DRM-Abstraktionsschicht (DRM Abstraction Layer, DAL)
  -	Das Microsoft PlayReady Porting Kit ist separat erhältlich und wird über diese Schnittstelle integriert. Weitere Informationen zur Lizenzierung von Microsoft PlayReady Device finden Sie [hier](http://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
- Beispiele für die Implementierung 
  -	PAL-Implementierungsbeispiel für Linux
  -	HAL-Implementierungsbeispiel für GStreamer

##Lizenzoptionen

Microsoft Smooth Streaming Client Porting Kit steht Lizenznehmern mit zwei verschiedenen Lizenzvereinbarungen zur Verfügung: eine für das Entwickeln von Smooth Streaming Client-Interimsprodukten und eine zweite für das Verteilen der finalen Smooth Streaming Client-Produkte an die Endbenutzer.
 
- Im Falle von Chipsatzherstellern, Systemintegratoren und unabhängigen Softwareanbietern (ISV), die ein Quellcode-Portierungskit zum Entwickeln von Interimsprodukten benötigen, empfiehlt sich die **Interimsproduktlizenz** für Microsoft Smooth Streaming Client Porting Kit.
- Gerätehersteller oder ISV, die Verteilungsrechte für finale Smooth Streaming Client-Produkte an die Endbenutzer benötigen, empfiehlt sich die **Finale Produktlizenz** für Microsoft Smooth Streaming Client Porting Kit.

###Microsoft Smooth Streaming Client Porting Kit Interim Product License

Mit dieser Lizenz bietet Microsoft ein Smooth Streaming Client Porting Kit und die erforderlichen Schutz- und Urheberrechte zum Entwickeln und Verteilen von Smooth Streaming Client-Interimsprodukten an andere Lizenznehmer von Smooth Streaming Client Porting Kit-Geräten, die finale Smooth Streaming Client-Produkte verteilen.

####Gebührenstruktur

Eine einmalige Lizenzgebühr von 50.000 US-Dollar ermöglicht den Zugriff auf Smooth Streaming Client Porting Kit.

###Finale Produktlizenz für das Microsoft Smooth Streaming Client Porting Kit

Mit dieser Lizenz bietet Microsoft alle erforderlichen Schutz- und Urheberrechte zum Erhalten von Smooth Streaming Client-Interimsprodukten von anderen Smooth Streaming Client Porting Kit-Lizenznehmern und zum Verteilen von firmeneigenen finalen Smooth Streaming Client-Produkten an Endbenutzer.

####Gebührenstruktur

Finale Smooth Streaming Client-Produkte werden mit dem folgenden Lizenzmodell angeboten:

- 0,10 US-Dollar pro gelieferte Geräteimplementierung
- Die Lizenz ist pro Jahr auf 50.000 US-Dollar begrenzt.
- Für die ersten 10.000 Geräteimplementierungen im Jahr fällt keine Lizenzgebühr an. 

##Lizenzierungsverfahren und SSPK-Zugriff

Wenden Sie sich bei Fragen zur Lizenzierung per E-Mail an [sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com).

Registrierte Interimslizenznehmer können auf das [SSPK-Verteilungsportal](https://microsoft.sharepoint.com/teams/SSPKDOWNLOAD/) zugreifen.

Lizenznehmer von SSPK-Lizenzen (Interim und final) können sich bei technischen Fragen an [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com) wenden.

##Lizenznehmer der Microsoft Smooth Streaming Client Interim Product Agreement

- Adroit Business Solutions, Inc.
- Advanced Digital Broadcast SA
- AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
- Albis Technologies Ltd.
- Alticast Corporation
- Amazon Digital Services, Inc.
- Amlogic, Co., Ltd.
- AVC Multimedia Software Co., Ltd.
- EchoStar Purchasing Corporation
- Enseo, Inc.
- Guangdong OPPO Mobile Telecommunications Corp., Ltd.
- HANDAN BroadInfoCom Co., Ltd.
- Infomir GMBH
- Inside Secure
- Irdeto USA Inc.
- Liberty Global Services BV
- MediaTek Inc.
- MStar Co, Ltd
- Nintendo Co., Ltd.
- OpenTV, Inc.
- Research In Motion Limited
- Saffron Digital Limited
- Sichuan Changhong Electric Co., Ltd
- Tatung Technology Inc.
- Telechips Inc.
- Vestel Elektronik Sanayi ve Ticaret A.S.
- VisualOn, Inc.
- ZTE Corporation

##Lizenznehmer der Microsoft Smooth Streaming Client Final Product Agreement

- Advanced Digital Broadcast SA
- AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
- Albis Technologies Ltd.
- Amazon Digital Services, Inc.
- AmTRAN Technology Co., Ltd.
- Arcadyan Technology Corporation
- ATMACA ELEKTRONİK SAN. VE TİC. A.Ş
- British Sky Broadcasting Limited
- CastPal Technology Inc., Shenzhen
- Compal Electronics, Inc.
- Dongguan Digital AV Technology Corp., Ltd.
- Enseo, Inc.
- Filmflex Movies Limited
- Guangdong OPPO Mobile Telecommunications Corp., Ltd.
- HANDAN BroadInfoCom Co., Ltd.
- Hisense International Co., Ltd
- Homecast Co.,Ltd
- Hon Hai Precision Industry Co., Ltd.
- Infomir GMBH
- Kaonmedia Co., Ltd.
- KDDI Corporation
- Nintendo Co., Ltd.
- Orange SA
- PIXELA Corporation
- Saffron Digital Limited
- Sagemcom Broadband SAS
- Sharp Corporation
- Shenzhen Coship Electronics CO., LTD
- Shenzhen Jiuzhou Electric Co.,Ltd
- Shenzhen Skyworth Digital Technology Co., Ltd
- Sichuan Changhong Electric Co., Ltd.
- SmarDTV S.A.
- SoftAtHome
- TCL Overseas Marketing (Macao Commercial Offshore) Limited
- Technicolor Delivery Technologies, SAS
- Toshiba Lifestyle Products & Services Corporation
- Virgin Media Limited
- VIZIO, Inc.
- Wistron Corporation
- WOOX Innovations Limited
- ZTE Corporation

##Media Services-Lernpfade

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geben

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0211_2016-->
