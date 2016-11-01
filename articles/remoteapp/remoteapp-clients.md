
<properties
    pageTitle="Zugreifen auf Apps von einem beliebigen Gerät aus | Microsoft Azure"
    description="Sie erfahren, welche Clients für Azure RemoteApp unterstützt werden und wie Sie auf Ihre Apps zugreifen."
    services="remoteapp"
	documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# Zugreifen auf Anwendungen in Azure RemoteApp

> [AZURE.IMPORTANT]
Azure RemoteApp wird eingestellt. Details finden Sie in der [Ankündigung](https://go.microsoft.com/fwlink/?linkid=821148).

Einer der unschlagbaren Vorteile von Azure RemoteApp ist, dass Sie über sämtliche Ihrer Geräte auf Apps zugreifen können. Und was noch besser ist: Sie können mit der Arbeit auf einem Gerät beginnen und dann nahtlos zu einem zweiten Gerät wechseln und an dem Punkt weiterarbeiten, an dem Sie aufgehört haben. Zuerst müssen Sie hierzu den entsprechenden Client für Ihr Gerät herunterladen und sich am Dienst anmelden.

Dieses Thema enthält die derzeit unterstützten Clients sowie Informationen zum Download. Anschließend wird beschrieben, wie Sie sich auf den einzelnen Clients bei RemoteApp anmelden.

## Unterstützte Clients

Sie können mit einem der folgenden Schritte auf RemoteApp zugreifen, wenn auf dem Gerät eines dieser Betriebssysteme ausgeführt wird:

 - Windows 10
 - Windows 8.1
 - Windows 8
 - Windows 7 Service Pack 1
 - Windows Phone 8,1
 - iOS
 - Mac OS X
 - Android


 Werden Thin Clients unterstützt? Die folgenden Windows Embedded-Thin Clients werden unterstützt:

- Windows Embedded Standard 7
- Windows Embedded 8 Standard
- Windows Embedded 8.1 Industry Pro
- Windows 10 IoT Enterprise


## Herunterladen des Clients

Es spielt keine Rolle, welche Plattform Sie verwenden: Der Client, den Sie zum Zugreifen auf RemoteApp benötigen, befindet sich auf der Seite zum Herunterladen des [Remotedesktopclients](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx).

Wenn Sie auf die Links klicken, wird der Download des Clients entweder direkt gestartet, oder Sie gelangen auf die Clientdownloadseite im App Store für die Plattform. Installieren Sie den Client gemäß der Anleitung auf dem Bildschirm.

Nachdem Sie den Client auf Ihrem Gerät installiert und gestartet haben, können Sie auf den entsprechenden Abschnitt zugreifen, um zu erfahren, wie Sie sich mit dem Client an RemoteApp anmelden.

## Android

Nachdem Sie die Microsoft Remotedesktop-App aus dem Google Play Store installiert haben, wird sie in Ihrer App-Liste unter **Remotedesktop** aufgeführt.

1. Wenn Sie die App starten, gelangen Sie zu einem leeren Connection Center, es sei denn, Sie haben die App bereits verwendet. Um Azure RemoteApp zu verwenden, tippen Sie auf die Schaltfläche „Hinzufügen“ (**""+""**) und dann auf **Azure RemoteApp**.

	 ![Leeres Connection Center](./media/remoteapp-clients/Android1.png)

2. Sie müssen sich mit Ihrer E-Mail-Adresse anmelden, um auf den Dienst zuzugreifen. Tippen Sie auf **Erste Schritte**.

	![Eingabeaufforderung zur Anmeldung](./media/remoteapp-clients/Android2.png)

3. Geben Sie auf der nächsten Seite Ihre **E-Mail-Adresse** ein, und tippen Sie auf **Weiter**. Der Registrierungsprozess über Azure Active Directory wird gestartet.

	![Erste Azure Active Directory-Seite](./media/remoteapp-clients/Android3.png)

4. Folgen Sie den Anweisungen auf dem Bildschirm, um sich mit Ihrem Microsoft-Konto (früher „LiveID“ genannt) oder Ihrer Unternehmens-ID anzumelden. Nach der Anmeldung wird unter Umständen eine Seite angezeigt, auf der alle Einladungen aufgeführt sind, die Sie erhalten haben. Wenn dies der Fall ist, können Sie die Einladungen auswählen, denen Sie vertrauen, und auf **Fertig** tippen.

	![Seite „Einladungen“](./media/remoteapp-clients/Android4.png)

5. Nach dem Akzeptieren der Einladungen wird die Liste mit den Apps, auf die Sie zugreifen können, auf Ihr Gerät heruntergeladen und im Connection Center zur Verfügung gestellt. Tippen Sie auf eine App, um sie zu verwenden.

	![Connection Center mit einem Feed](./media/remoteapp-clients/Android5.png)

6. Wenn Sie noch keine Einladung erhalten haben, können Sie den Dienst trotzdem ausprobieren. Tippen Sie hierzu auf **Kostenlose Testversion**, wenn die entsprechende Aufforderung angezeigt wird.

	![Demofeed-Eingabeaufforderung](./media/remoteapp-clients/Android6.png)

7. Sie erhalten dann Zugriff auf einen grundlegenden Satz von Apps, damit Sie mit der Verwendung von RemoteApp beginnen können.

	![Demofeed für Azure RemoteApp](./media/remoteapp-clients/Android7.png)

## iOS

Nachdem Sie die Microsoft Remotedesktop-App aus dem App Store installiert haben, wird sie in Ihrer App-Liste unter **RD-Client** aufgeführt.

1. Wenn Sie die App starten, gelangen Sie zu einem leeren Connection Center, es sei denn, Sie haben die App bereits verwendet. Um Azure RemoteApp zu verwenden, tippen Sie auf die Schaltfläche „Hinzufügen“ (**""+""**) und dann auf **Azure RemoteApp hinzufügen**.

	![Leeres Connection Center](./media/remoteapp-clients/IOS1.png)

2. Sie müssen sich mit Ihrer E-Mail-Adresse anmelden, um auf den Dienst zuzugreifen. Geben Sie zum Starten des Prozesses Ihre **E-Mail-Adresse** ein, und tippen Sie auf **Weiter**.

	![Eingabeaufforderung zur Anmeldung](./media/remoteapp-clients/picture1.png)

3. Folgen Sie den Anweisungen auf dem Bildschirm, um sich mit Ihrem Microsoft-Konto (LiveID) oder Ihrer Unternehmens-ID anzumelden. Nach der Anmeldung wird unter Umständen eine Seite angezeigt, auf der alle Einladungen aufgeführt sind, die Sie erhalten haben. Wenn dies der Fall ist, können Sie die Einladungen auswählen, denen Sie vertrauen, und auf **Fertig** tippen.

	![Seite „Einladungen“](./media/remoteapp-clients/IOS3.png)

4. Nach dem Akzeptieren der Einladungen wird die Liste mit den Apps, auf die Sie zugreifen können, auf Ihr Gerät heruntergeladen und im Connection Center zur Verfügung gestellt. Tippen Sie auf eine App, um sie zu starten und mit der Verwendung zu beginnen.

	![Connection Center mit einem Feed](./media/remoteapp-clients/IOS4.png)

5. Wenn Sie noch keine Einladung erhalten haben, können Sie den Dienst trotzdem ausprobieren. Tippen Sie hierzu auf **Kostenlose Testversion**, wenn die entsprechende Aufforderung angezeigt wird.

	![Demofeed-Eingabeaufforderung](./media/remoteapp-clients/IOS5.png)

6. Sie erhalten dann Zugriff auf einen grundlegenden Satz von Apps, damit Sie mit der Verwendung von RemoteApp beginnen können.

	![Demofeed für Azure RemoteApp](./media/remoteapp-clients/IOS6.png)

## Mac OS X

Nachdem Sie die Microsoft Remotedesktop-App aus dem App Store installiert haben, wird sie in Ihrer App-Liste unter **Microsoft Remotedesktop** aufgeführt.

1. Wenn Sie die App starten, gelangen Sie zu einem leeren Connection Center, es sei denn, Sie haben die App bereits verwendet. Um Azure RemoteApp zu verwenden, klicken Sie auf die Schaltfläche **Azure RemoteApp**.

	![Leeres Connection Center](./media/remoteapp-clients/Mac1.png)

2. Sie müssen sich mit Ihrer E-Mail-Adresse anmelden, um auf den Dienst zuzugreifen. Tippen Sie zum Starten des Prozesses auf **Erste Schritte**.

	![Eingabeaufforderung zur Anmeldung](./media/remoteapp-clients/Mac2.png)

3. Geben Sie auf der nächsten Seite Ihre **E-Mail-Adresse** ein, und tippen Sie auf **Weiter**. Der Registrierungsprozess über Azure Active Directory wird gestartet.

	![Erste Azure Active Directory-Seite](./media/remoteapp-clients/picture2.png)

4. Folgen Sie den Anweisungen auf dem Bildschirm, um sich mit Ihrem Microsoft-Konto (LiveID) oder Ihrer Unternehmens-ID anzumelden. Nach der Anmeldung wird unter Umständen eine Seite angezeigt, auf der alle Einladungen aufgeführt sind, die Sie erhalten haben. Wenn dies der Fall ist, können Sie die Einladungen auswählen, denen Sie vertrauen, und das Dialogfeld schließen.

	![Seite „Einladungen“](./media/remoteapp-clients/Mac4.png)

5. Nach dem Akzeptieren der Einladungen wird die Liste mit den Apps, auf die Sie zugreifen können, auf Ihr Gerät heruntergeladen und im Connection Center zur Verfügung gestellt. Doppelklicken Sie auf eine App, um sie zu starten und mit der Verwendung zu beginnen.

	![Connection Center mit einem Feed](./media/remoteapp-clients/Mac5.png)

6. Wenn Sie noch keine Einladung erhalten haben, können Sie den Dienst trotzdem ausprobieren. Klicken Sie hierzu auf **Kostenlose Testversion**, wenn die entsprechende Aufforderung angezeigt wird.

	![Demofeed-Eingabeaufforderung](./media/remoteapp-clients/Mac6.png)

7. Sie erhalten dann Zugriff auf einen grundlegenden Satz von Apps, damit Sie mit der Verwendung von RemoteApp beginnen können.

	![Demofeed für Azure RemoteApp](./media/remoteapp-clients/Mac7.png)

## Windows (alle unterstützten Versionen außer Windows Phone)

Der Client wird nach Abschluss der Installation automatisch gestartet. Wenn Sie später erneut darauf zugreifen möchten, finden Sie ihn in der App-Liste unter dem Namen **Azure RemoteApp**.

1. Nach dem Starten des Clients wird als Erstes eine „Willkommen“-Seite für Azure RemoteApp angezeigt. Klicken Sie auf **Erste Schritte**, um fortzufahren.

	![Seite „Willkommen“ des Azure RemoteApp-Clients](./media/remoteapp-clients/Windows1.png)

2. Auf der nächsten Seite beginnt der Anmeldeprozess für Azure RemoteApp über Azure Active Directory. Dieser Prozess wird Ihnen bekannt vorkommen, wenn Sie Microsoft-Dienste schon einmal verwendet haben. Geben Sie zuerst Ihre **E-Mail-Adresse** ein, und klicken Sie auf **Weiter**.

	![Erste Azure Active Directory-Eingabeaufforderung](./media/remoteapp-clients/Windows2.png)

3. Folgen Sie den Anweisungen auf dem Bildschirm, um sich mit Ihrem Microsoft-Konto (LiveID) oder Ihrer Unternehmens-ID anzumelden. Nach der Anmeldung wird unter Umständen eine Seite angezeigt, auf der alle Einladungen aufgeführt sind, die Sie erhalten haben. Wenn dies der Fall ist, können Sie die Einladungen auswählen, denen Sie vertrauen, und auf **Fertig** klicken.

	![Seite „Einladungen“ des Azure RemoteApp-Clients](./media/remoteapp-clients/Windows3.png)

4. Nach dem Akzeptieren der Einladungen wird die Liste mit den Apps, auf die Sie zugreifen können, auf Ihr Gerät heruntergeladen und im Connection Center zur Verfügung gestellt. Doppelklicken Sie auf eine App, um sie zu starten und mit der Verwendung zu beginnen.

	![Connection Center des Azure RemoteApp-Clients](./media/remoteapp-clients/Windows4.png)

5. Falls Sie noch keine Einladung erhalten haben, ist das kein Problem. Sie haben Zugriff auf eine Demosammlung, damit Sie den Dienst testen können.

	![Demofeed für Azure RemoteApp](./media/remoteapp-clients/Windows5.png)

## Windows Phone 8,1

Nachdem Sie die Microsoft Remotedesktop-App aus dem Windows Phone 8.1 Store installiert haben, wird sie in Ihrer App-Liste unter **Remotedesktop** aufgeführt.

1. Wenn Sie die App starten, gelangen Sie direkt zu einem leeren Connection Center, es sei denn, Sie haben die App bereits verwendet. Um Azure RemoteApp zu verwenden, tippen Sie unten auf dem Bildschirm auf die Schaltfläche „Hinzufügen“ (**""+""**).

	![Leeres Connection Center](./media/remoteapp-clients/WinPhone1.png)

2. Tippen Sie anschließend auf **Azure RemoteApp**.

	![Seite „Element hinzufügen“](./media/remoteapp-clients/WinPhone2.png)

3. Sie müssen sich mit Ihrer E-Mail-Adresse anmelden, um auf den Dienst zuzugreifen. Tippen Sie zum Starten des Prozesses auf **Verbinden**.

	![Eingabeaufforderung zur Anmeldung](./media/remoteapp-clients/WinPhone3.png)

4. Geben Sie auf der nächsten Seite Ihre **E-Mail-Adresse** ein, und tippen Sie auf **Weiter**. Der Registrierungsprozess über Azure Active Directory wird gestartet.

	![Erste Azure Active Directory-Seite](./media/remoteapp-clients/WinPhone4.png)

5. Folgen Sie den Anweisungen auf dem Bildschirm, um sich mit Ihrem Microsoft-Konto (LiveID) oder Ihrer Unternehmens-ID anzumelden. Nach der Anmeldung wird unter Umständen eine Seite angezeigt, auf der alle Einladungen aufgeführt sind, die Sie erhalten haben. Wenn dies der Fall ist, können Sie die Einladungen auswählen, denen Sie vertrauen, und auf **Speichern** tippen.

	![Seite „Einladungen“](./media/remoteapp-clients/WinPhone5.png)

6. Nach dem Akzeptieren der Einladungen wird die Liste mit den Apps, auf die Sie zugreifen können, auf Ihr Gerät heruntergeladen und im Connection Center zur Verfügung gestellt. Tippen Sie auf eine App, um sie zu starten und mit der Verwendung zu beginnen.

	![Connection Center mit einem Feed](./media/remoteapp-clients/WinPhone6.png)

7. Wenn Sie noch keine Einladung erhalten haben, können Sie den Dienst trotzdem ausprobieren. Tippen Sie hierzu auf **Ja**, wenn die entsprechende Aufforderung angezeigt wird.

	![Demofeed-Eingabeaufforderung](./media/remoteapp-clients/WinPhone7.png)

8. Sie erhalten dann Zugriff auf einen grundlegenden Satz von Apps, damit Sie mit der Verwendung von RemoteApp beginnen können.

	![Demofeed für Azure RemoteApp](./media/remoteapp-clients/WinPhone8.png)
 

<!---HONumber=AcomDC_0817_2016-->