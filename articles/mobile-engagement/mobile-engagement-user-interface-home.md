---
title: "Azure Mobile Engagement - Benutzeroberfläche – Startseite"
description: Erfahren Sie, wie Sie mithilfe von Azure Mobile Engagement Ihre vorhandene Anwendung und vorhandene Projekte verwalten
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: aff578d2-40f6-43e4-b0ea-7d2674cb28a1
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 1e3e4825d06486e6e8dfa874271ec82d203f7a7b
ms.lasthandoff: 12/08/2016


---
# <a name="how-to-manage-your-existing-application-and-projects"></a>Verwalten der vorhandenen Anwendung und Projekte
In diesem Artikel wird die **Startseite** im Portal **Mobile Engagement** beschrieben. Verwenden Sie das **Mobile Engagement** -Portal, um Ihre mobilen Apps zu überwachen und zu verwalten. Beachten Sie, dass Sie zur Verwendung des Portals zuerst ein **Azure Mobile Engagement** -Konto erstellen müssen. 

Um zur Startseite zu gelangen, klicken Sie links oben auf der Seite auf **Startseite** . Sie enthält die Liste aller Ihrer Anwendungen, die Teil der ausgewählten Sammlung sind. Auf dieser Seite sehen Sie nur eine kurze Übersicht über Ihre Anwendungen.

Die Startseite enthält auch alle Ihre Projekte, die Anwendungen in Ihrem Konto einschließen können. Durch Erstellen eines Kontos kann jeder auf die Startseite der Benutzeroberfläche zugreifen. Sie müssen jedoch anderen Benutzern Berechtigungen erteilen, damit sie Zugriff auf Ihre benutzerdefinierten Anwendungen in **Meine Projekte** erhalten.

Sie können auch das Vergleichsdiagramm für die ausgewählten Anwendungen anzeigen. Oder Sie können die Vergleichstabelle für die ausgewählten Anwendungen in einem Projekt anzeigen.

![Home1][0]

## <a name="my-applications"></a>Meine Anwendungen
Die Kurzübersicht über Ihre Anwendungen ermöglicht Ihnen, die Anwendung auszuwählen, die Sie öffnen möchten, um die detaillierten Menübandoptionen anzuzeigen. Sie können auf den Namen der Anwendung klicken, um zum zuletzt verwendeten Menübandbefehl in Ihrer Anwendung zurückzukehren. Oder klicken Sie auf das Zahnradsymbol, um direkt zur Seite "Einstellungen" Ihrer Anwendung zu gelangen. Sie können die Informationen in den Anwendungstabellen durchsuchen, filtern oder sortieren. Sie können auch die Spaltenüberschriften ziehen und an anderer Stelle ablegen, um die Reihenfolge zu ändern.

Die Übersicht über Ihre Anwendungen umfasst unter anderem Folgendes:

* **Trend bei neuen Benutzern:**Entwicklung neuer Benutzer in den letzten zwei Wochen.
* **Aktive Benutzer:**Anzahl der aktiven Benutzer in den letzten 30 Tagen.
* **Trend bei aktiven Benutzern:**Entwicklung aktiver Benutzer in den letzten zwei Wochen.
* **Sitzungen:**Eine Sitzung ist eine Verwendung der Anwendung durch einen Benutzer, die vom Starten bis zum Beenden der Anwendung durch den Benutzer reicht.
* **Sitzungstrends:**Entwicklung der Sitzungen in den letzten zwei Wochen.

Sobald Sie auf eine Anwendung klicken, können Sie Ihre Apps über die Benutzeroberfläche überwachen und verwalten . Beispiel:    

* [Überwachen von Echtzeitdaten zu Ihrer Anwendung](mobile-engagement-user-interface-monitor.md)
* [Analysieren von Verlaufsdaten zu Ihrer Anwendung](mobile-engagement-user-interface-analytics.md)
* [Erstellen und Verwalten von Segmenten von Benutzern zur Identifikation von Verwendungsmustern](mobile-engagement-user-interface-segments.md)
* [Erreichen der Benutzer Ihrer Anwendungen mithilfe von Pushbenachrichtigungen](mobile-engagement-user-interface-reach.md)

## <a name="my-projects"></a>Meine Projekte
Sie können mithilfe von Projekten Ihre Anwendungen gruppieren und anderen Benutzern Berechtigungen zum Zugriff auf diese erteilen. Sie erteilen anderen Benutzern Berechtigungen durch die Bereitstellung der E-Mail-Adresse. Über die Schaltfläche **Neues Projekt** können Sie ein neues Projekt erstellen, indem Sie lediglich einen Namen und eine Beschreibung eingeben. Nachdem ein Projekt erstellt wurde, können Sie auf den Projektnamen klicken, um den Namen und die Beschreibung Ihres Produkts zu bearbeiten und alle Anwendungen auszuwählen, die in diesem Projekt angezeigt werden sollen.

![Home6][60]

Es gibt die folgenden Rollen:

* **Anzeigender Benutzer:**Ein anzeigender Benutzer ist ein Benutzer, der nur die zu einem Projekt gehörigen Anwendungen anzeigen kann. Ein anzeigender Benutzer kann auf Analyse- und Überwachungsdaten zugreifen und Reach-Ergebnisse einsehen. Ein anzeigender Benutzer kann weder Informationen ändern noch Anwendungen oder Benutzer verwalten. Ein anzeigender Benutzer kann den Reach-Kampagnenstatus weder erstellen noch ändern.
* **Entwickler:**Ein Entwickler ist ein Benutzer mit allen Rechten eines anzeigenden Benutzers, der zudem Anwendungen verwalten darf. Ein Entwickler kann Anwendungen aktivieren und deaktivieren, Anwendungsinformationen (z. B. Paket und Signatur) ändern und Reach-Kampagnen erstellen. Ein Entwickler kann keine Benutzer verwalten.
* **Administrator:**Ein Administrator ist ein Benutzer mit allen Rechten eines Entwicklers, der zudem Benutzer verwalten darf. Ein Administrator kann Benutzer zur Teilnahme an einem Projekt einladen sowie Benutzerrollen und Projektinformationen ändern. Berechtigungen auf Anwendungsebene können auch in "Einstellungen" festgelegt werden.

Klicken Sie auf ein Projekt, um alle Programme anzuzeigen, die zu diesem Projekt gehören. Die folgende Abbildung zeigt das Vergleichsdiagramm für die ausgewählten Anwendungen.

![Home2][3]

## <a name="see-also"></a>Weitere Informationen
* [Konzepte][Link 6]
* [Handbuch zur Problembehandlung – Dienst][Link 24]

<!--Image references-->
[0]: ./media/mobile-engagement-user-interface-home/home0.png
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[60]: ./media/mobile-engagement-user-interface-home/home6.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md

