<properties 
	pageTitle="Erste Schritte mit Daten (Android) | Microsoft Azure" 
	description="Erfahren Sie mehr über die ersten Schritte bei der Verwendung von Mobile Services zur Nutzung von Daten in Ihrer Android-App." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="08/08/2015" 
	ms.author="ricksal"/>

# Hinzufügen von Mobile Services zu einer vorhandenen App

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data-ec.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Dieses Thema beschreibt die Nutzung von mobilen Azure-Diensten für die Nutzung von Daten in einer Android-App. In diesem Lernprogramm laden Sie eine App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in eine App und melden sich dann beim Azure-Verwaltungsportal an, um Datenänderungen beim Ausführen der App anzuzeigen.</p>

</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">Lernprogramm ansehen</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-data-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Video abspielen</span></a><span class="time">15:32:00</span></div>
</div>

> [AZURE.NOTE]Dieses Lernprogramm vermittelt ein besseres Verständnis davon, wie Sie mit mobilen Diensten in Azure Daten aus einer Android-App speichern und abrufen können. Dieses Thema behandelt viele der Schritte, die Ihnen im Schnellstart für mobile Dienste abgenommen werden. Falls Sie noch keine Erfahrung mit mobilen Diensten haben, sollten Sie eventuell zuerst das Lernprogramm [Erste Schritte mit mobilen Diensten](../get-started-android-ec.md) abschließen.
> 
> Den Quellcode der fertig gestellten App finden Sie [hier](https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStartedWithData/Android/GetStartedWithData).

Für dieses Lernprogramm benötigen Sie Folgendes:

+ Ein Azure-Konto. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-de%2Fdocumentation%2Farticles%2Fmobile-services-android-get-started-data-EC%2F). 

+ Das [Mobile Services Android SDK]; das <a href="https://go.microsoft.com/fwLink/p/?LinkID=280125" target="_blank">Android-SDK</a>, das die integrierte Eclipse-Entwicklungsumgebung (IDE) und das Android Developer Tools (ADT)-Plug-In enthält, sowie Android 4.2 oder eine höhere Version.

> [AZURE.NOTE]Dieses Lernprogramm enthält Anleitungen für die Installation des Android-SDK und des Android-SDK für mobile Dienste. Das heruntergeladene GetStartedWithData-Projekt benötigt Android 4.2 oder eine neuere Version. Das SDK für mobile Dienste benötigt dagegen nur Android 2.2 oder eine neuere Version.

<!-- -->

> [AZURE.NOTE]Dieses Lernprogramm verwendet die neueste Version des Mobile Services SDK. Aus Gründen der Abwärtskompatibilität finden Sie <a href="http://go.microsoft.com/fwlink/p/?LinkID=280126">hier</a> eine frühere Version des SDK. Der in diesen Lernprogrammen enthaltene Code funktioniert damit jedoch nicht.

##<a name="download-app"></a>Herunterladen des GetStartedWithData-Projekts

###Laden Sie den Beispielcode herunter

[AZURE.INCLUDE [download-android-sample-code](../../includes/download-android-sample-code-ec.md)]

###Prüfen der Version des Android-SDK

[AZURE.INCLUDE [Prüfen des SDK](../../includes/mobile-services-verify-android-sdk-version-ec.md)]


###Prüfen und Ausführen des Beispielcodes

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../../includes/mobile-services-android-run-sample-code-ec.md)]

##<a name="create-service"></a>Erstellen eines neuen mobilen Diensts im Verwaltungsportal

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

##<a name="add-table"></a>Hinzufügen einer neuen Tabelle zum mobilen Dienst

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../../includes/mobile-services-create-new-service-data-2.md)]

##<a name="update-app"></a>Aktualisieren der App für den Datenzugriff über mobile Dienste

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../../includes/mobile-services-android-getting-started-with-data-ec.md)]

##<a name="test-app"></a>Testen der App mit dem neuen mobilen Dienst

Die App verwendet nun mobile Dienste als Back-End-Speicher, und Sie können sie entweder im Android-Emulator oder auf einem Android-Telefon gegen den mobilen Dienst testen.

1. Klicken Sie im Menü **Ausführen** auf **Ausführen**, um das Projekt zu starten.

	Dadurch wird Ihre mit dem Android-SDK erstellte App ausgeführt, die mithilfe der Clientbibliothek Elemente aus Ihrem mobilen Dienst abfragt.

5. Geben Sie wie zuvor einen lesbaren Text ein und klicken Sie auf **Hinzufügen**.

   	Dieser Code schickt ein neues einzufügendes Element an den mobilen Dienst.

3. Klicken Sie im [Verwaltungsportal] auf **Mobile Dienste** und dann auf Ihren mobilen Dienst.

4. Klicken Sie auf die Registerkarte **Daten** und dann auf **Durchsuchen**.

   	![][9]
  
   	Die **TodoItem**-Tabelle enthält nun Daten mit von Mobile Services generierten Werten, und der Tabelle wurden automatisch Spalten entsprechend der TodoItem-Klasse der App hinzugefügt.

Damit ist das Android-Lernprogramm **Erste Schritte mit Daten** abgeschlossen.

## <a name="next-steps"> </a>Nächste Schritte

Dieses Lernprogramm zeigt die Grundlagen für die Integration von Daten in mobilen Diensten in Android-Apps.

Als Nächstes können Sie diese anderen Android-Lernprogramme ausführen:

* [Erste Schritte mit der Authentifizierung] <br/>Informationen zur Authentifizierung von Benutzern Ihrer App.

* [Erste Schritte mit Pushbenachrichtigungen] <br/>Verschicken Sie mithilfe Ihres mobilen Dienstes eine einfache Pushbenachrichtigung an Ihre App.

<!-- Anchors. -->
[Download the Android app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[8]: ./media/mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-android-get-started-data-EC/mobile-todoitem-data-browse.png
[12]: ./media/mobile-services-android-get-started-data/mobile-eclipse-project.png
[13]: ./media/mobile-services-android-get-started-data/mobile-quickstart-startup-android.png
[14]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-project.png


<!-- URLs. -->
[Erste Schritte mit der Authentifizierung]: mobile-services-android-get-started-users.md
[Erste Schritte mit Pushbenachrichtigungen]: mobile-services-javascript-backend-android-get-started-push-ec.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Verwaltungsportal]: https://manage.windowsazure.com/
[Mobile Services Android SDK]: http://aka.ms/Iajk6q
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkID=282122
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
 

<!----HONumber=August15_HO8-->
