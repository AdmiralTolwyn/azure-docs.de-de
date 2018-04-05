---
title: Azure Mobile Engagement iOS-SDK für die Reach-Integration | Microsoft Docs
description: Neueste Updates und Verfahren für das iOS-SDK für Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 1f5f5857-867c-40c5-9d76-675a343a0296
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 12/13/2016
ms.author: piyushjo
ms.openlocfilehash: 8d531f5850e8f7f352774f5894285402bd4cc53e
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-integrate-engagement-reach-on-ios"></a>So integrieren Sie Engagement Reach auf iOS
> [!IMPORTANT]
> Azure Mobile Engagement wird am 31.3.2018 außer Kraft gesetzt. Diese Seite wird kurz danach gelöscht.
> 

Bevor Sie die in diesem Leitfaden beschriebenen Schritte ausführen, müssen Sie die im Dokument [Integrieren von Mobile Engagement unter iOS](mobile-engagement-ios-integrate-engagement.md) beschriebenen Verfahren ausführen.

Diese Dokumentation erfordert XCode 8. Wenn Sie auf XCode 7 tatsächlich nicht verzichten können, bietet sich das [iOS Engagement SDK 3.2.4](https://aka.ms/r6oouh)an. In dieser Vorgängerversion tritt bei Ausführung auf iOS 10-Geräten ein bekannter Fehler auf: Systembenachrichtigungen werden nicht umgesetzt. Zur Behebung dieses Problems müssen Sie die veraltete API `application:didReceiveRemoteNotification:` wie folgt in Ihrem App-Delegaten implementieren:

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [!IMPORTANT]
> **Wir empfehlen diese Problemumgebung nicht** , weil diese iOS-API veraltet ist und sich dieses Verhalten in anstehenden (auch kleineren) iOS-Versionsupgrades ändern kann. Sie sollten so bald wie möglich zu XCode 8 wechseln.
>
>

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Aktivieren der App für den Empfang von stillen Pushbenachrichtigungen
[!INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

## <a name="integration-steps"></a>Schritte für die Integration
### <a name="embed-the-engagement-reach-sdk-into-your-ios-project"></a>Einbetten des Engagement Reach-SDK in Ihr iOS-Projekt
* Fügen Sie das Reach-SDK in Ihr Xcode-Projekt ein. Wechseln Sie in Xcode zu **Project \> Add to project**, und wählen Sie den Ordner `EngagementReach`.

### <a name="modify-your-application-delegate"></a>Ändern des Anwendungsdelegaten
* Importieren Sie am Beginn Ihrer Implementierungsdatei das Engagement Reach-Modul:

      [...]
      #import "AEReachModule.h"
* Erstellen Sie in der Methode `applicationDidFinishLaunching:` oder `application:didFinishLaunchingWithOptions:` ein Reach-Modul, und übergeben Sie es an Ihre vorhandene Zeile für die Engagement-Initialisierung:

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
        [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
        [...]

        return YES;
      }
* Legen Sie die Zeichenfolge **'icon.png'** auf den Namen der Bilddatei fest, die Sie als Benachrichtigungssymbol verwenden möchten.
* Wenn Sie die Option *Badgewert aktualisieren* in Reach-Kampagnen verwenden oder native Pushkampagnen – \</SaaS/Reach API/Campaign format/Native Push\> – einsetzen möchten, müssen Sie dem Reichweitenmodul die Verwaltung des Badgesymbols gestatten (das Modul löscht automatisch das Anwendungsbadge und setzt den von Engagement gespeicherten Wert bei jedem Start bzw. bei jeder Aktivierung der Anwendung zurück). Dies geschieht durch Hinzufügen der folgenden Codezeile nach der Initialisierung des Reach-Moduls:

      [reach setAutoBadgeEnabled:YES];
* Wenn Sie Pushvorgänge für Reach-Daten verarbeiten möchten, muss der Anwendungsdelegat mit dem `AEReachDataPushDelegate`-Protokoll konform sein. Fügen Sie nach der Initialisierung des Reach-Moduls die folgende Zeile ein:

      [reach setDataPushDelegate:self];
* Anschließend können Sie die Methoden `onDataPushStringReceived:` und `onDataPushBase64ReceivedWithDecodedBody:andEncodedBody:` im Anwendungsdelegaten implementieren:

      -(BOOL)didReceiveStringDataPushWithCategory:(NSString*)category body:(NSString*)body
      {
         NSLog(@"String data push message with category <%@> received: %@", category, body);
         return YES;
      }

      -(BOOL)didReceiveBase64DataPushWithCategory:(NSString*)category decodedBody:(NSData *)decodedBody encodedBody:(NSString *)encodedBody
      {
         NSLog(@"Base64 data push message with category <%@> received: %@", category, encodedBody);
         // Do something useful with decodedBody like updating an image view
         return YES;
      }

### <a name="category"></a>Category (Kategorie)
Der category-Parameter ist optional, wenn Sie eine Datenpushkampagne erstellen und eine Filterung von Datenpushvorgängen ermöglichen. Dies ist nützlich, wenn Sie unterschiedliche Arten von `Base64` -Daten pushen und vor der Analyse deren Typ ermitteln möchten.

**Ihre Anwendung ist nun bereit, Reach-Inhalte zu empfangen und anzuzeigen!**

## <a name="how-to-receive-announcements-and-polls-at-any-time"></a>Jederzeitiges Empfangen von Ankündigungen und Umfragen
Engagement kann über APNS (Apple Push Notification Service) jederzeit Reach-Benachrichtigungen an Ihre Endbenutzer senden.

Zur Aktivierung dieser Funktionalität müssen Sie Ihre Anwendung auf Apple-Pushbenachrichtigungen vorbereiten und Ihren Anwendungsdelegaten ändern.

### <a name="prepare-your-application-for-apple-push-notifications"></a>Vorbereiten Ihrer Anwendung auf Apple-Pushbenachrichtigungen
Folgen Sie den Anweisungen im Handbuch: [Vorbereiten der Anwendung für Apple-Pushbenachrichtigungen](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)

### <a name="add-the-necessary-client-code"></a>Hinzufügen des erforderlichen Clientcodes
*Jetzt sollte Ihre Anwendung über ein registriertes Apple-Pushzertifikat im Engagement-Front-End verfügen.*

Sofern nicht bereits geschehen, müssen Sie Ihre Anwendung für den Empfang von Pushbenachrichtigungen registrieren.

* Importieren Sie das `User Notification` -Framework:

        #import <UserNotifications/UserNotifications.h>
* Fügen Sie die folgende Zeile beim Start der Anwendung ein (typischerweise in `application:didFinishLaunchingWithOptions:`):

        if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
        {
            if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
            {
                [UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
            }else
            {
                [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
            }
            [application registerForRemoteNotifications];
        }
        else
        {
            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

Anschließend müssen Sie für Engagement das von Apple-Servern zurückgegebene Gerätetoken bereitstellen. Dies wird mit der Methode `application:didRegisterForRemoteNotificationsWithDeviceToken:` in Ihrem Anwendungsdelegaten erreicht:

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
        [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

Anschließend muss das Engagement-SDK darüber informiert werden, wenn Ihre Anwendung eine Remotebenachrichtigung erhält. Hierzu rufen Sie die Methode `applicationDidReceiveRemoteNotification:fetchCompletionHandler:` in Ihrem Anwendungsdelegaten auf:

    - (void)application:(UIApplication*)application didReceiveRemoteNotification:(NSDictionary*)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

> [!IMPORTANT]
> Standardmäßig steuert Engagement Reach den completionHandler. Wenn Sie den Block `handler` in Ihrem Code manuell bearbeiten möchten, können Sie NULL für das Argument `handler`übergeben und den „completion“-Block selbst kontrollieren. Unter `UIBackgroundFetchResult` -Typ finden Sie eine Liste möglicher Werte.
>
>

### <a name="full-example"></a>Vollständiges Beispiel
Hier sehen Sie ein vollständiges Beispiel für die Integration:

    #pragma mark -
    #pragma mark Application lifecycle

    - (BOOL)application:(UIApplication*)application didFinishLaunchingWithOptions:(NSDictionary*)launchOptions
    {
      /* Reach module */
      AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
      [reach setAutoBadgeEnabled:YES];

      /* Engagement initialization */
      [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
      [[EngagementAgent shared] setPushDelegate:self];

      /* Views */
      [window addSubview:[tabBarController view]];
      [window makeKeyAndVisible];

      [application registerForRemoteNotificationTypes:UIRemoteNotificationTypeAlert|UIRemoteNotificationTypeBadge|UIRemoteNotificationTypeSound];
      return YES;
    }

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
      [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

### <a name="resolve-unusernotificationcenter-delegate-conflicts"></a>Lösen von Konflikten mit dem UNUserNotificationCenter-Delegaten

*Wenn weder in Ihrer Anwendung noch in einer der Bibliotheken von Drittanbietern ein `UNUserNotificationCenterDelegate` implementiert wird, können Sie diesen Abschnitt überspringen.*

Ein `UNUserNotificationCenter`-Delegat wird vom SDK verwendet, um den Lebenszyklus der Engagement-Benachrichtigungen auf Geräten zu überwachen, die unter iOS 10 oder höher ausgeführt werden. Das SDK verfügt über eine eigene Implementierung des `UNUserNotificationCenterDelegate`-Protokolls, pro Anwendung kann jedoch nur ein `UNUserNotificationCenter`-Delegat vorhanden sein. Jeder weitere dem `UNUserNotificationCenter`-Objekt hinzugefügte Delegat führt zu einem Konflikt mit dem Engagement-Delegaten. Wenn das SDK Ihren Delegaten oder den Delegaten eines Drittanbieters erkennt, wird die eigene Implementierung des SDK nicht verwendet, damit Sie die Konflikte lösen können. Sie müssen Ihrem eigenen Delegaten die Engagement-Logik hinzufügen, um die Konflikte lösen zu können.

Hierfür gibt es zwei Möglichkeiten.

Vorschlag 1: Durch einfaches Weiterleiten der Delegataufrufe an das SDK:

    #import <UIKit/UIKit.h>
    #import "EngagementAgent.h"
    #import <UserNotifications/UserNotifications.h>


    @interface MyAppDelegate : NSObject <UIApplicationDelegate, UNUserNotificationCenterDelegate>
    @end

    @implementation MyAppDelegate

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterWillPresentNotification:notification withCompletionHandler:completionHandler]
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterDidReceiveNotificationResponse:response withCompletionHandler:completionHandler]
    }
    @end

Oder Vorschlag 2: Durch Erben von der `AEUserNotificationHandler`-Klasse

    #import "AEUserNotificationHandler.h"
    #import "EngagementAgent.h"

    @interface CustomUserNotificationHandler :AEUserNotificationHandler
    @end

    @implementation CustomUserNotificationHandler

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center willPresentNotification:notification withCompletionHandler:completionHandler];
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse: UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
    }

    @end

> [!NOTE]
> Sie können bestimmen, ob eine Benachrichtigung von Engagement stammt oder nicht, indem das zugehörige `userInfo`-Wörterbuch an die `isEngagementPushPayload:`-Klassenmethode des Agents übergeben wird.

Stellen Sie sicher, dass der `UNUserNotificationCenter`-Delegat des Objekts entweder in der `application:willFinishLaunchingWithOptions:`- oder der `application:didFinishLaunchingWithOptions:`-Methode des Anwendungsdelegaten auf Ihren Delegaten festgelegt ist.
Wenn Sie beispielsweise den oben erwähnten Vorschlag 1 implementiert haben:

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        // Any other code

        [UNUserNotificationCenter currentNotificationCenter].delegate = self;
        return YES;
      }

## <a name="how-to-customize-campaigns"></a>Anpassen von Kampagnen
### <a name="notifications"></a>Benachrichtigungen
Es gibt zwei Arten von Benachrichtigungen: System- und anwendungsinterne Benachrichtigungen.

Systembenachrichtigungen werden von iOS verarbeitet und können nicht angepasst werden.

Anwendungsinterne Benachrichtigungen bestehen aus einer Ansicht, die dynamisch in das aktuelle Anwendungsfenster eingefügt wird. Dies wird als Benachrichtigungsüberlagerung bezeichnet. Benachrichtigungsoverlays eignen sich besonders für eine schnelle Integration, da für sie keine Änderung von Ansichten in Ihrer Anwendung erforderlich ist.

#### <a name="layout"></a>Layout 
Um das Layout Ihrer anwendungsinternen Benachrichtigungen zu ändern, können Sie einfach die Datei `AENotificationView.xib` an Ihre Anforderungen anpassen. Sie müssen hierbei jedoch darauf achten, dass die Tagwerte und -typen der vorhandenen Unteransichten beibehalten werden.

Standardmäßig werden anwendungsinterne Benachrichtigungen am unteren Bildschirmrand angezeigt. Wenn Sie eine Anzeige der Benachrichtigungen am oberen Bildschirmrand bevorzugen, bearbeiten Sie die bereitgestellte Datei `AENotificationView.xib`, und ändern Sie die Eigenschaft `AutoSizing` der Hauptansicht, sodass die Benachrichtigungen weiterhin im oberen Bereich der übergeordneten Ansicht angezeigt werden können.

#### <a name="categories"></a>Categories
Wenn Sie das bereitgestellte Layout ändern, ändern Sie das Aussehen aller Ihrer Benachrichtigungen. Mithilfe von Kategorien können Sie verschiedene zielgerichtete Layouts (Verhaltensweisen) für Benachrichtigungen definieren. Eine Kategorie kann beim Erstellen einer Reach-Kampagne angegeben werden. Bedenken Sie, dass Sie mithilfe von Kategorien auch Ankündigungen und Umfragen anpassen können (dies wird weiter unten in diesem Dokument beschrieben).

Um einen Kategorie-Handler für Ihre Benachrichtigungen zu registrieren, müssen Sie nach der Initialisierung des Reach-Moduls einen Aufruf hinzufügen.

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:@"my_category"];
    ...

Bei `myNotifier` muss es sich um eine Instanz eines Objekts handeln, das mit dem `AENotifier`-Protokoll konform ist.

Sie können die Protokollmethoden selbst implementieren oder erneut die vorhandene `AEDefaultNotifier`-Klasse implementieren, mit der bereits die meisten Aufgaben ausgeführt werden.

Wenn Sie beispielsweise die Benachrichtigungsansicht für eine spezifische Kategorie neu definieren möchten, können Sie diesem Beispiel folgen:

    #import "AEDefaultNotifier.h"
    #import "AENotificationView.h"
    @interface MyNotifier : AEDefaultNotifier
    @end

    @implementation MyNotifier

    -(NSString*)nibNameForCategory:(NSString*)category
    {
      return "MyNotificationView";
    }

    @end

Dieses einfache Beispiel einer Kategorie setzt voraus, dass Sie in Ihrem Hauptanwendungspaket über eine Datei namens `MyNotificationView.xib` verfügen. Wenn die Methode keine entsprechende `.xib`-Datei findet, wird die Benachrichtigung nicht angezeigt und Engagement gibt eine entsprechende Meldung in der Konsole aus.

Die bereitgestellte NIB-Datei sollte den folgenden Regeln entsprechen:

* Sie sollte nur eine Ansicht enthalten.
* Unteransichten sollten dieselben Typen aufweisen wie diejenigen in der bereitgestellten NIB-Datei namens `AENotificationView.xib`
* Unteransichten sollten dieselben Tags aufweisen wie diejenigen in der bereitgestellten NIB-Datei namens `AENotificationView.xib`

> [!TIP]
> Kopieren Sie einfach die bereitgestellte NIB-Datei namens `AENotificationView.xib`, und beginnen Sie Ihre Arbeit mit dieser Datei. Seien Sie jedoch vorsichtig, die Ansicht in dieser NIB-Datei ist mit der Klasse `AENotificationView`verknüpft. Diese Klasse definiert die Methode `layoutSubViews` neu, um die zugehörigen Unteransichten gemäß Kontext zu verschieben und in der Größe zu ändern. Sie können die Klasse durch `UIView` oder eine benutzerdefinierte Ansichtenklasse ersetzen.
>
>

Wenn Sie eine umfangreichere Anpassung der Benachrichtigungen benötigen (wenn Sie beispielsweise Ihre Ansicht direkt aus dem Code laden möchten), sollten Sie einen Blick auf den bereitgestellten Quellcode und die Klassendokumentation von `Protocol ReferencesDefaultNotifier` und `AENotifier` werfen.

Beachten Sie, dass Sie dieselben Notifier für mehrere Kategorien verwenden können.

Sie können auch den standardmäßigen Notifier folgendermaßen neu definieren:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:kAEReachDefaultCategory];

##### <a name="notification-handling"></a>Benachrichtigungsverarbeitung
Bei Verwendung der Standardkategorie werden für das `AEReachContent`-Objekt einige Lebenszyklusmethoden aufgerufen, um Statistiken bereitzustellen und den Kampagnenstatus zu aktualisieren:

* Bei Anzeige der Benachrichtigung in der Anwendung wird von die `displayNotification`-Methode von `AEReachModule` aufgerufen (diese liefert Statistiken), wenn `handleNotification:` den Wert `YES` zurückgibt.
* Wird die Benachrichtigung geschlossen, wird die Methode `exitNotification` aufgerufen, es werden Statistiken bereitgestellt, und es können weitere Kampagnen verarbeitet werden.
* Wird auf die Benachrichtigung geklickt, wird `actionNotification` aufgerufen, es werden Statistiken bereitgestellt, und die verknüpfte Aktion wird ausgeführt.

Wenn Ihre Implementierung von `AENotifier` das Standardverhalten umgeht, müssen Sie diese Lebenszyklusmethoden selbst aufrufen. Das folgende Beispiel zeigt einige Fälle, in denen das Standardverhalten umgangen wird:

* Es erfolgt keine Erweiterung von `AEDefaultNotifier`, d. h. Sie haben die Kategorieverarbeitung von Grund auf implementiert.
* Sie haben `prepareNotificationView:forContent:` außer Kraft gesetzt. Stellen Sie sicher, dass mindestens `onNotificationActioned` oder `onNotificationExited` einem Ihrer Benutzeroberflächen-Steuerelemente zugeordnet ist.

> [!WARNING]
> Wenn `handleNotification:` eine Ausnahme generiert, wird der Inhalt gelöscht, und es erfolgt ein Aufruf von `drop`. Dies wird in der Statistik berichtet, und es können weitere Kampagnen verarbeitet werden.
>
>

#### <a name="include-notification-as-part-of-an-existing-view"></a>Einschließen von Benachrichtigungen als Bestandteil einer vorhandenen Ansicht
Overlays eignen sich hervorragend für eine schnelle Integration, sind aber gelegentlich nicht praktisch oder haben unerwünschte Nebeneffekte.

Wenn Sie mit dem Overlaysystem in einigen Ihrer Ansichten nicht zufrieden sind, können Sie es für diese Ansichten anpassen.

Sie können unser Benachrichtigungslayout in Ihre vorhandenen Ansichten einfügen. Die Implementierung kann auf zwei Arten erfolgen:

1. Fügen Sie die Benachrichtigungsansicht mit dem Interface Builder hinzu.

   * Öffnen Sie den *Interface Builder*
   * Platzieren Sie ein `UIView` -Element der Größe 320 x 60 (oder 768 x 60 für das iPad) an der Stelle, an der die Benachrichtigung angezeigt werden soll.
   * Legen Sie den Tagwert für diese Ansicht auf diesen Wert fest: **36822491**
2. Fügen Sie die Benachrichtigungsansicht programmatisch hinzu. Fügen Sie einfach nach dem Initialisieren Ihrer Ansicht den folgenden Code ein:

       UIView* notificationView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 320, 60)]; //Replace x and y coordinate values to your needs.
       notificationView.tag = NOTIFICATION_AREA_VIEW_TAG;
       [self.view addSubview:notificationView];

Das Makro `NOTIFICATION_AREA_VIEW_TAG` befindet sich in `AEDefaultNotifier.h`.

> [!NOTE]
> Der standardmäßige Notifier erkennt automatisch, dass das Benachrichtigungslayout in dieser Ansicht enthalten ist und fügt kein Overlay hinzu.
>
>

### <a name="announcements-and-polls"></a>Ankündigungen und Umfragen
#### <a name="layouts"></a>Layouts
Sie können die Dateien `AEDefaultAnnouncementView.xib` und `AEDefaultPollView.xib` ändern, solange Sie die Tagwerte und -typen der vorhandenen Unteransichten beibehalten.

#### <a name="categories"></a>Categories
##### <a name="alternate-layouts"></a>Alternative Layouts
Wie Benachrichtigungen können die Kampagnenkategorien dazu verwendet werden, alternative Layouts für Ankündigungen und Umfragen bereitzustellen.

Um eine Kategorie für eine Ankündigung zu erstellen, müssen Sie **AEAnnouncementViewController** erweitern und nach der Initialisierung des Reach-Moduls registrieren:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:@"my_category"];

> [!NOTE]
> Jedes Mal, wenn ein Benutzer auf eine Benachrichtigung für eine Ankündigung mit der Kategorie „my\_category“ klickt, wird Ihr registrierter Ansichtscontroller (in diesem Fall `MyCustomAnnouncementViewController`) durch einen Aufruf der Methode `initWithAnnouncement:` initialisiert, und die Ansicht wird dem aktuellen Anwendungsfenster hinzugefügt.
>
>

In Ihrer Implementierung der Klasse `AEAnnouncementViewController` müssen Sie die Eigenschaft `announcement` lesen, um Ihre Unteransichten zu initialisieren. Siehe hierzu das nachfolgende Beispiel, in dem mithilfe der Eigenschaften `title` und `body` der Klasse `AEReachAnnouncement` zwei Labels initialisiert werden:

    -(void)loadView
    {
        [super loadView];

        UILabel* titleLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        titleLabel.font = [UIFont systemFontOfSize:32.0];
        titleLabel.text = self.announcement.title;

        UILabel* bodyLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        bodyLabel.font = [UIFont systemFontOfSize:24.0];
        bodyLabel.text = self.announcement.body;

        [self.view addSubview:titleLabel];
        [self.view addSubview:bodyLabel];
    }

Wenn Sie Ihre Ansichten nicht selbst laden, sondern lediglich das Ansichtenlayout der Standardankündigung wiederverwenden möchten, können Sie einfach über Ihren benutzerdefinierten Ansichtencontroller die bereitgestellte Klasse `AEDefaultAnnouncementViewController`erweitern. In diesem Fall duplizieren Sie die NIB-Datei `AEDefaultAnnouncementView.xib`, und benennen Sie sie um, sodass Sie von Ihrem benutzerdefinierten Ansichtencontroller geladen werden kann (wenn Ihr Controller beispielsweise `CustomAnnouncementViewController` heißt, sollten Sie Ihrer NIB-Datei den Namen `CustomAnnouncementView.xib` geben).

Um die Standardkategorie für Ankündigungen zu ersetzen, registrieren Sie einfach Ihren benutzerdefinierten Ansichtencontroller für die in `kAEReachDefaultCategory`definierte Kategorie:

    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:kAEReachDefaultCategory];

Umfragen können auf die gleiche Weise angepasst werden:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerPollController:[MyCustomPollViewController class] forCategory:@"my_category"];

Dieses Mal muss der bereitgestellte `MyCustomPollViewController` zur Erweiterung von `AEPollViewController` verwendet werden. Alternativ können Sie eine Erweiterung über den Standardcontroller durchführen: `AEDefaultPollViewController`.

> [!IMPORTANT]
> Vergessen Sie nicht, entweder `action` (`submitAnswers:` für benutzerdefinierte Ansichtencontroller für Umfragen) oder die Methode `exit` aufzurufen, bevor der Ansichtencontroller verworfen wird. Andernfalls werden keine Statistiken gesendet (z. B. Analysen der Kampagne) und – noch wichtiger – weitere Kampagnen erhalten erst eine Benachrichtigung, wenn der Anwendungsprozess neu gestartet wurde.
>
>

##### <a name="implementation-example"></a>Beispiel für die Implementierung
In dieser Implementierung wird die benutzerdefinierte Ankündigungsansicht aus einer externen XIB-Datei geladen.

Wie bei der erweiterten Benachrichtigungsanpassung wird empfohlen, sich den Quellcode der Standardimplementierung anzusehen.

`CustomAnnouncementViewController.h`

    //Interface
    @interface CustomAnnouncementViewController : AEAnnouncementViewController {
      UILabel* titleLabel;
      UITextView* descTextView;
      UIWebView* htmlWebView;
      UIButton* okButton;
      UIButton* cancelButton;
    }

    @property (nonatomic, retain) IBOutlet UILabel* titleLabel;
    @property (nonatomic, retain) IBOutlet UITextView* descTextView;
    @property (nonatomic, retain) IBOutlet UIWebView* htmlWebView;
    @property (nonatomic, retain) IBOutlet UIButton* okButton;
    @property (nonatomic, retain) IBOutlet UIButton* cancelButton;

    -(IBAction)okButtonClicked:(id)sender;
    -(IBAction)cancelButtonClicked:(id)sender;

`CustomAnnouncementViewController.m`

    //Implementation
    @implementation CustomAnnouncementViewController
    @synthesize titleLabel;
    @synthesize descTextView;
    @synthesize htmlWebView;
    @synthesize okButton;
    @synthesize cancelButton;

    -(id)initWithAnnouncement:(AEReachAnnouncement*)anAnnouncement
    {
      self = [super initWithNibName:@"CustomAnnouncementViewController" bundle:nil];
      if (self != nil) {
        self.announcement = anAnnouncement;
      }
      return self;
    }

    - (void) dealloc
    {
      [titleLabel release];
      [descTextView release];
      [htmlWebView release];
      [okButton release];
      [cancelButton release];
      [super dealloc];
    }

    - (void)viewDidLoad {
      [super viewDidLoad];

      /* Init announcement title */
      titleLabel.text = self.announcement.title;

      /* Init announcement body */
      if(self.announcement.type == AEAnnouncementTypeHtml)
      {
        titleLabel.hidden = YES;
        htmlWebView.hidden = NO;
        [htmlWebView loadHTMLString:self.announcement.body baseURL:[NSURL URLWithString:@"http://localhost/"]];
      }
      else
      {
        titleLabel.hidden = NO;
        htmlWebView.hidden = YES;
        descTextView.text = self.announcement.body;
      }

      /* Set action button label */
      if([self.announcement.actionLabel length] > 0)
        [okButton setTitle:self.announcement.actionLabel forState:UIControlStateNormal];

      /* Set exit button label */
      if([self.announcement.exitLabel length] > 0)
        [cancelButton setTitle:self.announcement.exitLabel forState:UIControlStateNormal];
    }

    #pragma mark Actions

    -(IBAction)okButtonClicked:(id)sender
    {
        [self action];
    }

    -(IBAction)cancelButtonClicked:(id)sender
    {
        [self exit];
    }

    @end
