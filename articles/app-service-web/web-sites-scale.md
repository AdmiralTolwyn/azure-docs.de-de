<properties 
	pageTitle="Skalieren einer Web-App in Azure App Service" 
	description="Erfahren Sie, wie Sie eine Web-App in Azure App Service vertikal und horizontal skalieren sowie eine automatische Skalierung durchführen." 
	services="app-service" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/25/2016" 
	ms.author="cephalin"/>

# Skalieren einer Web-App in Azure App Service #

Zur Steigerung von Leistung und Durchsatz für Ihre Web-Apps unter Microsoft Azure können Sie mithilfe des [Azure-Portals](http://portal.azure.com) Ihren[App Service](http://go.microsoft.com/fwlink/?LinkId=529714)-Plan vom Modus **Kostenlos** zum Modus **Freigegeben**,**Basic**, **Standard** oder **Premium** hochstufen.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

Das Skalieren von Azure-Web-Apps umfasst zwei zusammenhängende Vorgänge: Ändern des App Service-Plan-Modus auf einen höhere Servicelevel und Konfigurieren bestimmter Einstellungen nach dem Wechsel zum höheren Servicelevel. Beide Themen werden in diesem Artikel erläutert. Höhere Servicelevel wie die Modi **Standard** und **Premium** bieten bessere Stabilität und Flexibilität bei der Festlegung, wie Ressourcen auf Azure verwendet werden.

Diese Skalierungseinstellungen werden innerhalb von Sekunden angewendet und wirken sich auf alle Web-Apps im App Service-Plan aus. Dafür muss weder der Code geändert noch Anwendungen erneut bereitgestellt werden.

Weitere Informationen zu App Service-Plänen finden Sie unter [Was ist ein App Service-Plan?](../app-service/app-service-how-works-readme.md) und [Azure App Service-Pläne – Detaillierte Übersicht](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). Informationen zur Preisgestaltung und zu den Funktionen der einzelnen App Service-Pläne finden Sie unter [App Service-Preisdetails](/pricing/details/web-sites/).

> [AZURE.NOTE] Bevor Sie Web-Apps vom Modus **Kostenlos** in den Modus **Basic**, **Standard** oder **Premium** ändern, müssen Sie zuerst das für Ihr Microsoft Azure App Service-Abonnement geltende Ausgabenlimit entfernen. Informationen zum Anzeigen oder Ändern von Optionen für Ihr Microsoft Azure App Service-Abonnement finden Sie unter [Microsoft Azure-Abonnements][azuresubscriptions].

<a name="scalingsharedorbasic"></a>
<!-- ===================================== -->
## Skalierung auf Modus "Freigegeben" oder "Basic"
<!-- ===================================== -->

1. Öffnen Sie in Ihrem Browser das [Azure-Portal][portal].
	
2. Klicken Sie auf dem Blatt Ihrer Web-App auf **Alle Einstellungen** und anschließend auf **Zentral hochskalieren**.
	
	![Auswählen eines Plans][ChooseWHP]
	
4. Markieren Sie im Blatt **Wählen Sie Ihre Preisstufe** entweder den Modus **Freigegeben** oder **Basic**, und klicken Sie dann auf **Auswählen**.
	
	Auf der Registerkarte **Benachrichtigungen** wird in grüner Schrift der Text **ERFOLGREICH** angezeigt, sobald der Vorgang abgeschlossen wurde.
	
5. Klicken Sie in den Einstellungen auf **Horizontal hochskalieren**, wählen Sie in der Dropdownliste *Manuell ausgewählte Anzahl der Instanzen* aus, und schieben Sie den Balken **Instanz** von links nach rechts, um die Anzahl der Instanzen zu erhöhen. Klicken Sie dann in der Befehlsleiste auf **Speichern**. Die Instanzgrößenoption ist im Modus **Freigegeben** nicht verfügbar. Weitere Informationen zu diesen Instanzgrößen finden Sie unter [App Service-Preise][vmsizes].
	
	![Instanzgröße für Basic-Modus][ChooseBasicInstances]
	
	Auf der Registerkarte **Benachrichtigungen** wird in grüner Schrift der Text **ERFOLGREICH** angezeigt, sobald der Vorgang abgeschlossen wurde.
	
<a name="scalingstandard"></a>
<!-- ================================= -->
## Skalieren auf Standard- oder Premium-Modus
<!-- ================================= -->

> [AZURE.NOTE] Bevor Sie einen App Service-Plan in den Modus **Standard** oder **Premium** ändern, müssen Sie das für Ihr Microsoft Azure App Service-Abonnement geltende Ausgabenlimit entfernen. Andernfalls besteht das Risiko, dass Ihre Web-App nicht mehr verfügbar ist, wenn Sie Ihr Ausgabenlimit vor dem Ende der Abrechnungsperiode erreichen. Informationen zum Anzeigen oder Ändern von Optionen für Ihr Microsoft Azure App Service-Abonnement finden Sie unter [Microsoft Azure-Abonnements][azuresubscriptions].

1. Gehen Sie für die Skalierung zum Modus **Standard** oder **Premium** zunächst vor wie bei der Skalierung auf **Freigegeben** oder **Basic**. Wählen Sie dann unter **Wählen Sie Ihre Preisstufe** den Modus **Standard** oder **Premium**, und klicken Sie anschließend auf **Auswählen**. 
	
	Auf der Registerkarte **Benachrichtigungen** wird in grüner Schrift der Text **ERFOLGREICH** angezeigt, sobald der Vorgang abgeschlossen wurde, und die **Autoskalierung** wird aktiviert.
	
	![Skalieren im Standard- oder Premium-Modus][ScaleStandard]
	
	Sie können wie im Modus **Basic** (siehe oben) weiterhin den Schieberegler **Instanz** bewegen, um manuell die Anzahl von Instanzen zu erhöhen. In diesem Abschnitt erfahren Sie jedoch, wie Sie Ihre App automatisch skalieren können.
	
2. Wählen Sie unter **Skalieren nach** die Option ** Zeitplan und Leistungsregeln**, um Ihre App automatisch zu skalieren.
	
	![Festlegen des Modus für automatische Skalierung nach Leistung][Autoscale]
	
3. Klicken Sie in den **Einstellungen** auf **Standard, Skalierung 1-1**, und verschieben Sie die beiden Schieberegler, um die minimale und maximale Anzahl von Instanzen bei der automatischen Skalierung für den App Service-Plan zu definieren. In diesem Lernprogramm wird der Schieberegler für die maximale Instanzenanzahl auf **6** festgelegt.
	
4. Klicken Sie auf **OK**.
	
4. Klicken Sie unter **Einstellungen** auf **CPU-Prozentsatz > 80 (Anzahl erhöhen um 1)**, um die Regeln für die automatische Skalierung für die Standardmetrik zu konfigurieren.
	
	![Festlegen von Zielmetriken][SetTargetMetrics]
	
	Sie können Regeln für die automatische Skalierung konfigurieren, die für verschiedene Leistungsmetriken gelten, wie z. B. CPU, Arbeitsspeicher, Datenträgerwarteschlange,HTTP-Warteschlange und Datenfluss. In diesem Beispiel konfigurieren Sie die automatische Skalierung für den CPU-Prozentsatz, der Folgendes bewirkt:
	
	- Zentral hochskalieren um 1 Instanz, wenn der CPU-Prozentsatz in den letzten 10 Minuten über 80 % liegt
	- Skalierung nach oben um 3 Instanzen, wenn der CPU-Prozentsatz in den letzten 5 Minuten über 90 % liegt
	- Skalierung nach unten um 1 Instanz, wenn der CPU-Prozentsatz in den letzten 30 Minuten unter 50 % liegt 
	
	
4. Behalten Sie im Dropdownfeld **Metrikname** die Einstellung **CPU-Prozentsatz** bei.
	
5. Konfigurieren Sie unter **Regeln zum Hochskalieren** die erste Regel, indem Sie **Operator** auf **Größer als**, **Schwellenwert** auf **70** (%), **Dauer** auf **10** (Minuten), **Zeitaggregation** auf „Durchschnitt“, **Aktion** auf **Anzahl erhöhen um**, „Wert“ auf **1** (Instanz) und **Abklingen** auf **10** (Minuten) festlegen.
	
	![Festlegen der ersten Regel für automatische Skalierung][SetFirstRule]
	
	>[AZURE.NOTE] Die Einstellung **Cooldown** gibt an, wie lange diese Regel nach einer Skalierungsaktion bis zur nächsten Skalierung warten soll.
	
6. Klicken Sie auf **Regel hinzufügen**, und konfigurieren Sie die zweite Regel, indem Sie **Operator** auf **Größer als**, **Schwellenwert** auf **90** (%), **Dauer** auf **1** (Minute), **Zeitaggregation** auf „Durchschnitt“, **Aktion** auf **Anzahl erhöhen um**, **Wert** auf **3** (Instanz) und **Abklingen** auf **1** (Minute) festlegen.

7. Klicken Sie auf **OK**.
	
	![Festlegen der zweiten Regel für automatische Skalierung][SetSecondRule]
	
5. Klicken Sie in den **Einstellungen** auf **Regel hinzufügen**, um die dritte Regel zu konfigurieren, indem Sie **Operator** auf **Kleiner als**, **Schwellenwert** auf **50** (%), **Dauer** auf **30** (Minuten), **Zeitaggregation** auf **Durchschnitt**, **Aktion** auf **Anzahl verringern um**, **Wert** auf **1** (Instanz) und **Abklingen** auf **60** (Minuten) festlegen.
	
	![Festlegen der dritten Regel für automatische Skalierung][SetThirdRule]
	
7. Klicken Sie auf **OK**. Ihre Regel für die automatische Skalierung sollte jetzt im Blatt **Skalierungseinstellung** angezeigt werden.
	
	![Festlegen der Ergebnisse der Regeln für automatisches Skalieren][SetRulesFinal]

<a name="ScalingSQLServer"></a>
##Skalieren einer SQL Server-Datenbank mit Verbindung zu Ihrer Web-App
Wenn Sie eine oder mehrere SQL Server-Datenbanken mit Ihrer Web-App verknüpft haben (unabhängig vom App Service-Planmodus), können Sie diese basierend auf Ihren Anforderungen jederzeit schnell skalieren.

1. Um einer der verknüpften Datenbanken zu skalieren, öffnen Sie das Blatt Ihrer Web-App im [Azure-Portal][portal]. Klicken Sie im reduzierbaren Dropdownfeld **Grundlagen** auf den Link **Ressourcengruppe**. Klicken Sie anschließend im Bereich **Zusammenfassung** des Blatts für die Ressourcengruppe auf eine der verknüpften Datenbanken.

	![Verknüpfte Datenbank][ResourceGroup]
	
2. Klicken Sie im Blatt für Ihre verknüpfte SQL-Datenbank auf den Bereich **Einstellungen** > **Tarif**, wählen Sie basierend auf Ihren Leistungsanforderungen einen Tarif aus, und klicken Sie auf **Auswählen**.
	
	![Skalieren Ihrer SQL-Datenbank][ScaleDatabase]
	
3. Sie können außerdem die geografische Replikation einrichten, um eine hohe Verfügbarkeit und eine Notfallwiederherstellung für Ihre SQL-Datenbank zu gewährleisten. Klicken Sie hierzu auf den Bereich **Geografische Replikation**.
	
	![Einrichten der geografischen Replikation für SQL-Datenbanken][GeoReplication]

<a name="devfeatures"></a>
## Entwicklerfunktionen
Je nach Modus der Web-App stehen die folgenden entwicklungsbezogenen Funktionen zur Verfügung:

### Bitness ###

- Die Modi **Basic**, **Standard** und **Premium** unterstützen 64-Bit- und 32-Bit-Anwendungen.
- Die Planmodi **Kostenlos** und **Freigegeben** unterstützt nur 32-Bit-Anwendungen.

### Debugger-Support ###

- Debugger-Support ist für die Modi **Kostenlos**, **Freigegeben** und**Basic** bei 1 gleichzeitigen Verbindung pro App Service-Plan verfügbar.
- Debugger-Support ist für die Modi **Standard** und **Premium** bei 5 gleichzeitigen Verbindungen pro App Service-Plan verfügbar.

<a name="OtherFeatures"></a>
## Andere Funktionen

### Web Endpoint Monitoring ###

- Die Webendpunkt-Überwachung ist in den Modi **Basic**, **Standard** und **Premium** verfügbar. Informationen zur Web-Endpunktüberwachung finden Sie unter [Überwachen von Web-Apps](web-sites-monitor.md).

- Ausführliche Informationen zu allen weiteren Funktionen in den App Service-Plänen, einschließlich Preisgestaltung und allgemein interessanten Funktionen (auch für Entwickler) finden Sie unter [App Service-Preisdetails](/pricing/details/web-sites/).

>[AZURE.NOTE] Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

<a name="Next Steps"></a>
## Nächste Schritte

- Informationen zu den ersten Schritten mit Azure finden Sie unter [Kostenlose Microsoft Azure-Testversion](/pricing/free-trial/).
- Informationen zu Preisen, Support und SLAs erhalten Sie unter den folgenden Links.
	
	[Preisdetails zur Datenübertragung](/pricing/details/data-transfers/)
	
	[Supportpläne für Microsoft Azure](/support/plans/)
	
	[Vereinbarungen zum Servicelevel (SLAs)](/support/legal/sla/)
	
	[Preisdetails für SQL-Datenbanken](/pricing/details/sql-database/)
	
	[Größen virtueller Computer und Clouddienste für Microsoft Azure][vmsizes]
	
	[App-Service-Preisdetails](/pricing/details/web-sites/)
	
	[App-Service-Preisdetails - SSL-Verbindungen](/pricing/details/web-sites/#ssl-connections)

- Informationen zu den Best Practices für Azure App Service, einschließlich dem Aufbau einer skalierbaren, robusten Architektur, finden Sie unter [Best Practices: Azure App Service-Web-Apps](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx).

- Videos zum Skalieren von Web-Apps:
	
	- [Wann sollte man Azure-Websites skalieren? - Mit Stefan Schackow](/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
	- [Automatisches Skalieren von Azure-Websites, CPU-abhängig oder geplant - Mit Stefan Schackow](/documentation/videos/auto-scaling-azure-web-sites/)
	- [Wie sollte man Azure-Websites skalieren? - Mit Stefan Schackow](/documentation/videos/how-azure-web-sites-scale/)

## Änderungen
* Hinweise zu den Änderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).

<!-- LINKS -->
[vmsizes]: /pricing/details/app-service/
[SQLaccountsbilling]: http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]: http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ChooseBasicInstances]: ./media/web-sites-scale/scale2InstancesBasic.png
[SaveButton]: ./media/web-sites-scale/05SaveButton.png
[BasicComplete]: ./media/web-sites-scale/06BasicComplete.png
[ScaleStandard]: ./media/web-sites-scale/scale3InstancesStandard.png
[Autoscale]: ./media/web-sites-scale/scale4AutoScale.png
[SetTargetMetrics]: ./media/web-sites-scale/scale5AutoScaleTargetMetrics.png
[SetFirstRule]: ./media/web-sites-scale/scale6AutoScaleFirstRule.png
[SetSecondRule]: ./media/web-sites-scale/scale7AutoScaleSecondRule.png
[SetThirdRule]: ./media/web-sites-scale/scale8AutoScaleThirdRule.png
[SetRulesFinal]: ./media/web-sites-scale/scale9AutoScaleFinal.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
 

<!---HONumber=AcomDC_0518_2016-->