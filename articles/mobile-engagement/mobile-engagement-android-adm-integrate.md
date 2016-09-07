<properties
	pageTitle="Integration des Azure Mobile Engagement Android SDKs"
	description="Neueste Updates und Verfahren für das Android SDK für Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="08/19/2016"
	ms.author="piyushjo" />


#So integrieren Sie ADM in Engagement

> [AZURE.IMPORTANT] Bevor Sie dieser Anleitung folgen, müssen Sie das unter „Integrieren von Mobile Engagement unter Android“ beschriebene Integrationsverfahren befolgen.
>
> Dieses Dokument ist nur relevant, wenn Sie das Reach-Modul bereits integriert haben und Pushübertragungen an Amazon-Geräte planen. Lesen Sie zunächst das Dokument "So integrieren Sie Engagement Reach auf Android", um Informationen zum Integrieren von Reach-Kampagnen zu erhalten.

##Einführung

Durch die Integration von ADM kann Ihre Anwendung Pushnachrichten empfangen, wenn Amazon Android-Geräte das Ziel sind.

ADM-Nutzlasten, die mithilfe von Push an das SDK übertragen werden, enthalten immer den `azme`-Schlüssel im Datenobjekt. Wenn Sie ADM in Ihrer Anwendung also zu einem anderen Zweck verwenden, können Sie Push-Vorgänge basierend auf diesem Schlüssel filtern.

> [AZURE.IMPORTANT] Von Amazon Device Messaging werden nur Amazon Kindle-Geräte mit Android 4.0.3 oder höher unterstützt, dieser Code kann jedoch ohne Sicherheitsbedenken auf anderen Geräten integriert werden.

##Anmelden bei ADM

Sofern nicht bereits geschehen, müssen Sie ADM über Ihr Amazon-Konto aktivieren.

Detaillierte Informationen zum Vorgehen finden Sie hier: [<https://developer.amazon.com/sdk/adm/credentials.html>].

Nach Abschluss des Verfahrens erhalten Sie:

-   OAuth-Anmeldeinformationen(eine Client-ID und einen Clientschlüssel) für Engagement, um Pushnachrichten an Ihre Geräte zu senden.
-   Einen API-Schlüsse, der in Ihre Anwendung integriert werden muss.

##SDK-Integration

### Verwalten von Geräteregistrierungen

Jedes Gerät muss einen Registrierungsbefehl an die ADM-Server senden, anderenfalls sind sie nicht erreichbar.

Wenn Sie bereits die [ADM-Clientbibliothek] verwenden und eine [ADM-Integration] durchgeführt haben, können Sie direkt zu "android-sdk-adm-receive" wechseln.

Wenn Sie ADM noch nicht integriert haben, bietet Engagement eine einfache Methode zur Aktivierung in Ihrer Anwendung:

Bearbeiten Sie Ihre `AndroidManifest.xml`-Datei:

-   Fügen Sie den Amazon-Namespace hinzu, die Datei sollte folgendermaßen beginnen:

		<?xml version="1.0" encoding="utf-8"?>
		<manifest xmlns:android="http://schemas.android.com/apk/res/android"
		          xmlns:amazon="http://schemas.amazon.com/apk/res/android"

-   Fügen Sie im `<application/>`-Tag diesen Abschnitt hinzu:

		<amazon:enable-feature
		   android:name="com.amazon.device.messaging"
		   android:required="false"/>

		<meta-data android:name="engagement:adm:register" android:value="true" />

-   Nach dem Hinzufügen des Amazon-Tags erhalten Sie möglicherweise einen Buildfehler, wenn der Buildzielname des aktuellen Projekts unter Android 2.1 liegt. Sie müssen als Buildzielname **Android 2.1+** verwenden (keine Sorge, Sie können `minSdkVersion` weiterhin auf 4 festlegen).
-   Integrieren Sie den ADM-API-Schlüssel als Medienobjekt, indem Sie [dieses Verfahren] ausführen.

Folgen Sie anschließend den Anweisungen in den nächsten Abschnitten.

### Übermitteln der Registrierungs-ID an den Engagement-Pushdienst, um Benachrichtigungen zu erhalten

Um die Registrierungs-ID des Geräts an den Engagement-Pushdienst zu übermitteln und Benachrichtigungen zu empfangen, fügen Sie in der `AndroidManifest.xml`-Datei im Tag `<application/>` den folgenden Code ein (selbst, wenn Sie ADM ohne Engagement verwenden):

		<receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
		  android:exported="false">
		  <intent-filter>
		    <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
		  </intent-filter>
		</receiver>

		 <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
		   android:permission="com.amazon.device.messaging.permission.SEND">
		  <intent-filter>
		    <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
		    <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
		    <category android:name="<your_package_name>"/>
		  </intent-filter>
		</receiver>   

Stellen Sie sicher, dass Sie die folgenden Berechtigungen in der Datei `AndroidManifest.xml` festgelegt haben (vor dem Tag `</application>`).

		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE"/>
		<uses-permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE"/>
		<permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE" android:protectionLevel="signature"/>

##Gewähren von OAuth-Anmeldeinformationen für Engagement

Übermitteln Sie Ihre OAuth-Anmeldeinformationen (Client-ID und geheimen Clientschlüssel) in das Engagement-Portal.

[<https://developer.amazon.com/sdk/adm/credentials.html>]: https://developer.amazon.com/sdk/adm/credentials.html
[ADM-Clientbibliothek]: https://developer.amazon.com/sdk/adm/setup.html
[ADM-Integration]: https://developer.amazon.com/sdk/adm/integrating-app.html
[dieses Verfahren]: https://developer.amazon.com/sdk/adm/integrating-app.html#Asset

<!---HONumber=AcomDC_0824_2016-->