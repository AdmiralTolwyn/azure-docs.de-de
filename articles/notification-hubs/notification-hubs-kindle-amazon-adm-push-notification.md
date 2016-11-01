<properties
	pageTitle="Erste Schritte mit Azure Notification Hubs für Kindle-Apps | Microsoft Azure"
	description="In diesem Lernprogramm erfahren Sie, wie Sie mithilfe von Azure Notification Hubs eine Pushbenachrichtigung an eine Kindle-Anwendung senden."
	services="notification-hubs"
	documentationCenter=""
	authors="wesmc7777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-kindle"
	ms.devlang="Java"
	ms.topic="hero-article"
	ms.date="06/29/2016"
	ms.author="wesmc"/>

# Erste Schritte mit Notification Hubs für Kindle-Apps

[AZURE.INCLUDE [Notification-Hubs-Auswahl-Erste-Schritte](../../includes/notification-hubs-selector-get-started.md)]

##Übersicht

In diesem Lernprogramm wird gezeigt, wie Sie mit Azure Benachrichtigungshubs Pushbenachrichtigungen an eine Kindle-Anwendung senden können. Sie erstellen eine leere Kindle-App, die Pushbenachrichtigungen mithilfe von Amazon Device Messaging (ADM) empfängt.

##Voraussetzungen

Für dieses Lernprogramm ist Folgendes erforderlich:

+ Laden Sie das Android-SDK (wir gehen davon aus, dass Sie Eclipse verwenden) von der <a href="http://go.microsoft.com/fwlink/?LinkId=389797">Android-Website</a> herunter.
+ Führen Sie die Schritte unter <a href="https://developer.amazon.com/appsandservices/resources/development-tools/ide-tools/tech-docs/01-setting-up-your-development-environment">Setting Up Your Development Environment</a> zum Einrichten der Entwicklungsumgebung für Kindle aus.

##Hinzufügen einer neuen App zum Entwicklerportal

1. Erstellen Sie zuerst eine App im [Amazon-Entwicklerportal].

	![][0]

2. Kopieren Sie den **Anwendungsschlüssel**.

	![][1]

3. Klicken Sie im Portal auf den Namen der App und dann auf die Registerkarte **Device Messaging**.

	![][2]

4. Klicken Sie auf **Create a New Security Profile**, und erstellen Sie ein neues Sicherheitsprofil (z. B. **TestAdm Sicherheitsprofil**). Klicken Sie anschließend auf **Save**.

	![][3]

5. Klicken Sie auf **Security Profiles**, um das gerade erstellte Sicherheitsprofil anzuzeigen. Kopieren Sie die Werte für **Client ID** und **Client Secret** zur späteren Verwendung.

	![][4]

## Erstellen eines API-Schlüssels

1. Öffnen Sie eine Eingabeaufforderung mit Administratorberechtigungen.
2. Navigieren Sie zum Android SDK-Ordner.
3. Geben Sie den folgenden Befehl ein:

    	keytool -list -v -alias androiddebugkey -keystore ./debug.keystore

	![][5]

4.  Geben Sie als **keystore**-Kennwort **android** ein.

5.  Kopieren Sie den **MD5**-Fingerabdruck.
6.  Klicken Sie im Entwicklerportal auf der Registerkarte **Messaging** auf **Android/Kindle**, und geben Sie den Paketnamen für Ihre App (z. B. **com.sample.notificationhubtest**) und den Wert für **MD5**, und klicken Sie dann auf **Generate API Key**.

## Hinzufügen von Anmeldeinformationen zum Hub

Geben Sie im Portal den geheimen Clientschlüssel und die Client-ID in die Registerkarte **Configure** des Benachrichtigungshubs ein.

## Einrichten der Anwendung

> [AZURE.NOTE] Verwenden Sie beim Erstellen einer Anwendung mindestens API-Level 17.

Fügen Sie die ADM-Bibliotheken zum Eclipse-Projekt hinzu:

1. Um die ADM-Bibliothek zu erhalten, [laden Sie das SDK herunter]. Extrahieren Sie die SDK-ZIP-Datei.
2. Klicken Sie in Eclipse mit der rechten Maustaste auf das Projekt, und klicken Sie dann auf **Properties**. Wählen Sie **Java Build Path** auf der linken Seite aus, und klicken Sie anschließend oben auf die Registerkarte **Bibliotheken**. Klicken Sie auf **Externe Jar hinzufügen**, und wählen Sie die Datei `\SDK\Android\DeviceMessaging\lib\amazon-device-messaging-*.jar` aus dem Verzeichnis aus, in das Sie das Amazon-SDK extrahiert haben.
3. Laden Sie das NotificationHubs Android-SDK (Link) herunter.
4. Entpacken Sie das Paket, und ziehen Sie die Datei `notification-hubs-sdk.jar` in den Ordner `libs` in Eclipse.

Bearbeiten Sie Ihr App-Manifest zur Unterstützung von ADM:

1. Fügen Sie den Amazon-Namespace in das Manifest-Stammelement ein:


		xmlns:amazon="http://schemas.amazon.com/apk/res/android"

2. Fügen Sie Berechtigungen als erstes Element unter dem Manifestelement hinzu. Ersetzen Sie **[IHR PAKETNAME]** durch das Paket, mit dem Sie die App erstellt haben.

		<permission
	     android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
	     android:protectionLevel="signature" />

		<uses-permission android:name="android.permission.INTERNET"/>

		<uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />

		<!-- This permission allows your app access to receive push notifications
		from ADM. -->
		<uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />

		<!-- ADM uses WAKE_LOCK to keep the processor from sleeping when a message is received. -->
		<uses-permission android:name="android.permission.WAKE_LOCK" />

3. Fügen Sie das folgende Element als erstes untergeordnetes Element des Anwendungselements ein. Ersetzen Sie **[IHR DIENSTNAME]** durch den Namen des ADM-Meldungshandlers, den Sie im nächsten Abschnitt erstellen (einschließlich Paket), und ersetzen Sie **[IHR PAKETNAME]** durch den Namen des Pakets, mit dem Sie Ihre App erstellt haben.

		<amazon:enable-feature
		      android:name="com.amazon.device.messaging"
		             android:required="true"/>
		<service
		    android:name="[YOUR SERVICE NAME]"
		    android:exported="false" />

		<receiver
		    android:name="[YOUR SERVICE NAME]$Receiver" />

		    <!-- This permission ensures that only ADM can send your app registration broadcasts. -->
		    android:permission="com.amazon.device.messaging.permission.SEND" >

		    <!-- To interact with ADM, your app must listen for the following intents. -->
		    <intent-filter>
		  <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
		  <action android:name="com.amazon.device.messaging.intent.RECEIVE" />

		  <!-- Replace the name in the category tag with your app's package name. -->
		  <category android:name="[YOUR PACKAGE NAME]" />
		    </intent-filter>
		</receiver>

## Erstellen des ADM-Meldungshandlers

1. Erstellen Sie eine neue Klasse als Erbe von `com.amazon.device.messaging.ADMMessageHandlerBase`, und nennen Sie sie `MyADMMessageHandler`, wie in der folgenden Abbildung gezeigt:

	![][6]

2. Fügen Sie die folgenden `import`-Anweisungen ein:

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.support.v4.app.NotificationCompat;
		import com.amazon.device.messaging.ADMMessageReceiver;
		import com.microsoft.windowsazure.messaging.NotificationHub

3. Fügen Sie den folgenden Code zur soeben erstellten Klasse hinzu. Ersetzen Sie den Hubnamen und die Verbindungszeichenfolge (Überwachung):

		public static final int NOTIFICATION_ID = 1;
		private NotificationManager mNotificationManager;
		NotificationCompat.Builder builder;
      	private static NotificationHub hub;
		public static NotificationHub getNotificationHub(Context context) {
			Log.v("com.wa.hellokindlefire", "getNotificationHub");
			if (hub == null) {
				hub = new NotificationHub("[hub name]", "[listen connection string]", context);
			}
			return hub;
		}

		public MyADMMessageHandler() {
				super("MyADMMessageHandler");
			}

			public static class Receiver extends ADMMessageReceiver
    		{
        		public Receiver()
        		{
            		super(MyADMMessageHandler.class);
        		}
    		}

			private void sendNotification(String msg) {
				Context ctx = getApplicationContext();

	   		 mNotificationManager = (NotificationManager)
	    			ctx.getSystemService(Context.NOTIFICATION_SERVICE);

	    	PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
	          	new Intent(ctx, MainActivity.class), 0);

	    	NotificationCompat.Builder mBuilder =
	          	new NotificationCompat.Builder(ctx)
	          	.setSmallIcon(R.mipmap.ic_launcher)
	          	.setContentTitle("Notification Hub Demo")
	          	.setStyle(new NotificationCompat.BigTextStyle()
	                     .bigText(msg))
	          	.setContentText(msg);

	     	mBuilder.setContentIntent(contentIntent);
	     	mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
		}

4. Fügen Sie den folgenden Code in die Methode `OnMessage()` ein:

		String nhMessage = intent.getExtras().getString("msg");
		sendNotification(nhMessage);

5. Fügen Sie den folgenden Code in die Methode `OnRegistered` ein:

			try {
		getNotificationHub(getApplicationContext()).register(registrationId);
			} catch (Exception e) {
		Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
			}

6.	Fügen Sie den folgenden Code in die Methode `OnUnregistered` ein:

			try {
				getNotificationHub(getApplicationContext()).unregister();
			} catch (Exception e) {
				Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
			}

7. Fügen Sie in der Methode `MainActivity` die folgende Import-Anweisung ein:

		import com.amazon.device.messaging.ADM;

8. Fügen Sie am Ende der Methode `OnCreate` folgenden Code hinzu:

		final ADM adm = new ADM(this);
		if (adm.getRegistrationId() == null)
		{
		   adm.startRegister();
		} else {
			new AsyncTask() {
			      @Override
			      protected Object doInBackground(Object... params) {
			         try {			        	 MyADMMessageHandler.getNotificationHub(getApplicationContext()).register(adm.getRegistrationId());
			         } catch (Exception e) {
			        	 Log.e("com.wa.hellokindlefire", "Failed registration with hub", e);
			        	 return e;
			         }
			         return null;
			     }
			   }.execute(null, null, null);
		}

## Hinzufügen Ihres API-Schlüssels zur App

1. Erstellen Sie in Eclipse eine neue Datei namens **api\_key.txt** im Verzeichnisobjekt des Projekts.
2. Öffnen Sie die Datei, und kopieren Sie den API-Schlüssel, den Sie im Amazon-Entwicklerportal generiert haben.

## Ausführen der App

1. Starten Sie den Emulator.
2. Streifen Sie im Emulator nach unten, und klicken Sie auf **Einstellungen** und anschließend auf **Mein Konto**, und registrieren Sie sich mit einem gültigen Amazon-Konto.
3. Führen Sie die App in Eclipse aus.

> [AZURE.NOTE] Falls Probleme auftreten, überprüfen Sie die Uhrzeit des Emulators (oder des Geräts). Die Uhrzeit muss stimmen. Um die Uhrzeit des Kindle-Emulators zu ändern, können Sie folgenden Befehl aus dem Tools-Verzeichnis der Android SDK-Plattform ausführen:

		adb shell  date -s "yyyymmdd.hhmmss"

## Senden einer Nachricht

So senden Sie eine Nachricht mithilfe von .NET:

		static void Main(string[] args)
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

            hub.SendAdmNativeNotificationAsync("{"data":{"msg" : "Hello from .NET!"}}").Wait();
        }

![][7]

<!-- URLs. -->
[Amazon-Entwicklerportal]: https://developer.amazon.com/home.html
[laden Sie das SDK herunter]: https://developer.amazon.com/public/resources/development-tools/sdk

[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png

<!---HONumber=AcomDC_0706_2016-->