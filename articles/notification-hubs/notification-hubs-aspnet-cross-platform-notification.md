---
title: Senden plattformübergreifender Benachrichtigungen an Benutzer mit Azure Notification Hubs (ASP.NET)
description: Erfahren Sie, wie Sie mithilfe von Notification Hubs-Vorlagen in einer einzigen Anforderung eine plattformunabhängige Benachrichtigung senden können, die auf alle Plattformen abzielt.
services: notification-hubs
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 11d2131b-f683-47fd-a691-4cdfc696f62b
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 95793aac3c25563e3af39f3c47cebdd06e25e35f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777952"
---
# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Senden plattformübergreifender Benachrichtigungen an Benutzer mit Benachrichtigungs-Hubs
Im vorherigen Tutorial [Benachrichtigen von Benutzern mit Notification Hubs] haben Sie gelernt, wie Sie Pushbenachrichtigungen an alle Geräte eines bestimmten authentifizierten Benutzers senden können. Dieses Lernprogramm benötigte mehrere Anfragen für den Versand einer Benachrichtigung an jede unterstützte Client-Plattform. Azure Notification Hubs unterstützt Vorlagen, mit denen Sie angeben können, wie ein bestimmtes Gerät Benachrichtigungen empfangen soll. Auf diese Weise können Sie den Versand plattformübergreifender Benachrichtigungen vereinfachen. 

In diesem Thema wird der Einsatz von Vorlagen für den Versand einer plattformunabhängigen Benachrichtigung für alle Plattformen mit nur einer Anfrage beschrieben. Weitere Informationen zu Vorlagen finden Sie unter [Überblick über Azure Notification Hubs][Templates].

> [!IMPORTANT]
> Windows Phone-Projekte der Version 8.1 und älter werden in Visual Studio 2017 nicht unterstützt. Weitere Informationen finden Sie unter [Visual Studio 2017 – Zielplattformen und Kompatibilität](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

> [!NOTE]
> Mit Notification Hubs kann ein Gerät mehrere Vorlagen mit demselben Tag registrieren. In diesem Fall werden bei einer eingehenden Nachricht für das entsprechende Tag mehrere Benachrichtigungen an das Gerät verschickt (eine pro Vorlage). Auf diese Weise können Sie dieselbe Nachricht in mehreren visuellen Darstellungen anzeigen, z.B. als Signal und als Popupbenachrichtigung in einer Windows Store-App.
> 
> 

Um plattformunabhängige Benachrichtigungen mit Vorlagen zu verschicken, gehen Sie wie folgt vor:

1. Erweitern Sie im Projektmappen-Explorer in Visual Studio den Ordner **Controllers**, und öffnen Sie die Datei „RegisterController.cs“.

2. Suchen Sie in der **Put**-Methode den Codeblock, der eine neue Registrierung erstellt, und ersetzen Sie den Inhalt von `switch` durch den folgenden Code:
   
        switch (deviceUpdate.Platform)
        {
            case "mpns":
                var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                    "<wp:Notification xmlns:wp=\"WPNotification\">" +
                       "<wp:Toast>" +
                            "<wp:Text1>$(message)</wp:Text1>" +
                       "</wp:Toast> " +
                    "</wp:Notification>";
                registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "wns":
                toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
                registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "apns":
                var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
                registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
                break;
            case "gcm":
                var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
                registration = new GcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }
   
    Dieser Code ruft die plattformspezifische Methode zur Erstellung einer Vorlagenregistrierung anstelle der Systemregistrierung auf. Da Vorlagenregistrierungen von nativen Registrierungen abgeleitet sind, müssen Sie vorhandene Registrierungen nicht ändern.

3. Ersetzen Sie im **Notifications**-Controller die Methode **sendNotification** durch den folgenden Code:
   
        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:" + user;
   
            var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
            await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);
   
            return Request.CreateResponse(HttpStatusCode.OK);
        }
   
    Dieser Code sendet eine Benachrichtigung an alle Plattformen gleichzeitig, ohne dass eine native Nutzlast angegeben werden muss. Über Notification Hubs wird die richtige Nutzlast an alle Geräte mit dem angegebenen *tag*-Wert erstellt und übermittelt, wie in den registrierten Vorlagen festgelegt.

4. Veröffentlichen Sie Ihr WebAPI-Back-End-Projekt erneut.

5. Führen Sie die Client-App erneut aus, und vergewissern Sie sich, dass die Registrierung erfolgreich war.

6. (Optional:) Stellen Sie die Client-App auf einem zweiten Gerät bereit, und führen Sie die App aus.
    Auf jedem Gerät wird eine Benachrichtigung angezeigt.

## <a name="next-steps"></a>Nächste Schritte
Nach Abschluss dieses Lernprogramms finden Sie weitere Informationen über Notification Hubs und Vorlagen in den folgenden Themen:

* [Use Notification Hubs to send breaking news]: Demonstrates another scenario for using templates.
* [Übersicht über Azure Notification Hubs][Templates] enthält weitere Informationen zu Vorlagen.

<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Push to users ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Push to users Mobile Services]: /manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[Benachrichtigen von Benutzern mit Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: http://msdn.microsoft.com/library/windowsazure/jj927172.aspx
