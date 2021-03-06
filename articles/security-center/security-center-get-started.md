<properties
   pageTitle="Erste Schritte mit Azure Security Center | Microsoft Azure"
   description="Dieses Dokument unterstützt Sie beim Einstieg in Azure Security Center. Sie erhalten eine Einführung in die Sicherheitsüberwachungs- und Richtlinienverwaltungs-Komponenten und werden zu weiterführenden Schritten geleitet."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/09/2016"
   ms.author="terrylan"/>

# Erste Schritte mit Azure Security Center

Dieses Dokument unterstützt Sie beim Einstieg in Azure Security Center. Sie erhalten eine Einführung in die Sicherheitsüberwachungs- und Richtlinienverwaltungs-Komponenten und werden zu weiterführenden Schritten geleitet.

> [AZURE.NOTE] Die Informationen in diesem Dokument gelten für die Vorschauversion von Azure Security Center. Der Dienst wird anhand einer Beispielbereitstellung vorgestellt. Es ist keine schrittweise Anleitung.

## Was ist Azure Security Center?
 Security Center unterstützt Sie beim Vorbeugen, Erkennen und Beheben von Bedrohungen. Mit dieser Cloudlösung gewinnen Sie mehr Transparenz und bessere Kontrolle über die Sicherheit Ihrer Azure-Ressourcen. Es bietet integrierte Sicherheitsüberwachung und Richtlinienverwaltung für Ihre Abonnements, hilft bei der Erkennung von Bedrohungen, die andernfalls möglicherweise unbemerkt bleiben, und kann gemeinsam mit einem breiten Sektrum an Sicherheitslösungen verwendet werden.

## Voraussetzungen

Für den Einstieg in Security Center benötigen Sie ein Microsoft Azure-Abonnement. Security Center wird mit Ihrem Abonnement aktiviert. Wenn Sie nicht über ein Abonnement verfügen, können Sie sich für ein [kostenloses Testabonnement](https://azure.microsoft.com/pricing/free-trial/) registrieren.

 Der Zugriff auf Security Center erfolgt über das [Azure-Portal](https://azure.microsoft.com/features/azure-portal/). Weitere Informationen finden Sie in der [Dokumentation zum Portal](https://azure.microsoft.com/documentation/services/azure-portal/).


## Zugreifen auf Security Center

Führen Sie im Portal folgende Schritte aus, um auf Security Center zuzugreifen:

1. Wählen Sie **Durchsuchen** aus, und führen Sie dann einen Bildlauf zur Option **Security Center** durch. ![Auf Azure Security Center im Portal zugreifen][1]

2. Wählen Sie **Security Center** aus. Dadurch wird das Blatt **Security Center** geöffnet.
3. Um zukünftig einfacher auf das Blatt **Security Center** zugreifen zu können, wählen Sie die Option **Blatt an Dashboard anheften** (oben rechts) aus. ![Option „Blatt an Dashboard anheften“][2]

## Verwenden von Security Center

Konfigurieren einer Sicherheits**richtlinie** für Ihre Abonnements:

4. Klicken Sie auf dem Blatt **Security Center** auf die Kachel **Sicherheitsrichtlinie**.
5. Wählen Sie auf dem Blatt **Sicherheitsrichtlinie – Richtlinie pro Abonnement definieren** ein Abonnement aus.
6. Aktivieren Sie auf dem Blatt **Sicherheitsrichtlinie** die Option **Datensammlung**, um automatisch Protokolle zu erfassen. Durch das Aktivieren der Option **Datensammlung** wird auch Überwachungserweiterung auf allen aktuellen und neuen VMs im Abonnement bereitgestellt.
7. Klicken Sie auf **Speicherkonto auswählen**. Wählen Sie für jede Region, in der Sie virtuelle Computer ausführen, ein Speicherkonto, in dem Daten dieser virtuellen Computer gespeichert werden. Wenn Sie kein Speicherkonto für die einzelnen Regionen auswählen, wird ein Speicherkonto für Sie erstellt. Die gesammelten Daten werden aus Sicherheitsgründen logisch von anderen Kundendaten getrennt.
8. Aktivieren Sie die **Empfehlungen**, die Sie als Teil Ihrer Sicherheitsrichtlinie verwenden möchten. Beispiele:

  - Durch das Aktivieren von **Systemupdates** werden alle unterstützten virtuellen Maschinen auf fehlende Betriebssystemupdates überprüft.
  - Durch das Aktivieren von **Basisregeln** werden alle unterstützten virtuellen Maschinen untersucht, um Betriebssystemkonfigurationen zu ermitteln, die die virtuelle Maschine anfälliger für Angriffe machen können.

![Kachel „Sicherheitsrichtlinie“ in Azure Security Center][3]

**Empfehlungen**:

9. Kehren Sie zum Blatt **Security Center** zurück, und klicken Sie auf die Kachel **Empfehlungen**. Security Center analysiert in regelmäßigen Abständen den Sicherheitsstatus der Azure-Ressourcen. Wenn mögliche Sicherheitsrisiken festgestellt werden, wird hier eine Empfehlung angezeigt.
11.	Klicken Sie auf jede Empfehlung, um weitere Informationen anzuzeigen oder Schritte zur Behebung des Problems auszuführen.

![Empfehlungen in Azure Security Center][4]

Anzeigen des Integritäts- und Sicherheitsstatus Ihrer Ressourcen über **Resources health**:

12.	Kehren Sie zum Blatt **Security Center** zurück.
13.	Die Kachel **Resources health** enthält Indikatoren, die den Sicherheitsstatus für **virtuelle Computer**, **Netzwerk**, **SQL** und **Anwendungen** anzeigen.
14.	Wählen Sie für weitere Informationen **Virtuelle Computer** aus.
15.	Auf dem Blatt **Virtuelle Computer** wird eine Statuszusammenfassung angezeigt. Sie enthält den Status von Antischadsoftware, Systemupdates, Neustarts und die Grundregeln Ihrer virtuellen Maschinen.
16.	Wählen Sie ein Element unter **VORBEUGUNGSSCHRITTE** aus, um weitere Informationen anzuzeigen und/oder Maßnahmen zur Konfiguration der erforderlichen Steuerelemente zu ergreifen.
17.	Führen Sie einen Drilldown durch, um zusätzliche Informationen zu bestimmten virtuellen Computern anzuzeigen.

![Kachel „Ressourcenintegrität“ in Azure Security Center][5]

**Sicherheitswarnungen**:

19.	Kehren Sie zum Blatt **Security Center** zurück, und klicken Sie auf die Kachel **Sicherheitswarnungen**. Auf dem Blatt **Sicherheitswarnungen** wird eine Liste der Warnungen angezeigt. Die Warnungen werden bei der Security Center-Analyse Ihrer Sicherheitsprotokolle und der Netzwerkaktivität generiert. Die Liste enthält auch Warnungen integrierter Partnerlösungen. ![Sicherheitswarnungen in Azure Security Center][6]

21.	Klicken Sie auf eine Warnung, um weitere Informationen anzuzeigen.

  ![Details zu Sicherheitswarnungen in Azure Security Center][7]

## Nächste Schritte
In diesem Dokument wurden die Sicherheitsüberwachungs- und Richtlinienverwaltungskomponenten in Security Center vorgestellt. Weitere Informationen finden Sie in den folgenden Artikeln:

- [Festlegen von Sicherheitsrichtlinien in Azure Security Center](security-center-policies.md): Erfahren Sie, wie Sie Sicherheitsrichtlinien konfigurieren.
- [Verwalten von Sicherheitsempfehlungen in Azure Security Center](security-center-recommendations.md): Erfahren Sie, wie Empfehlungen Ihnen beim Schutz der Azure-Ressourcen helfen.
- [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md): Erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
- [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md): Erfahren Sie, wie Sie Sicherheitswarnungen verwalten und darauf reagieren.
- [Azure Security Center – häufig gestellte Fragen](security-center-faq.md): Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.
- [Azure Security Blog](http://blogs.msdn.com/b/azuresecurity/): Enthält Neuigkeiten und Informationen zur Azure-Sicherheit.

<!--Image references-->
[1]: ./media/security-center-get-started/security-tile.png
[2]: ./media/security-center-get-started/pin-blade.png
[3]: ./media/security-center-get-started/security-policy.png
[4]: ./media/security-center-get-started/recommendations.png
[5]: ./media/security-center-get-started/resources-health.png
[6]: ./media/security-center-get-started/security-alert.png
[7]: ./media/security-center-get-started/security-alert-detail.png

<!---HONumber=AcomDC_0211_2016-->