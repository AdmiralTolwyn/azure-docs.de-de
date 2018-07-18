---
title: Einrichten von Abrechnungs- oder Guthabenwarnungen für Azure-Abonnements | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie Warnungen für Ihre Azure-Rechnung einrichten können, um Überraschungen bei der Abrechnung zu vermeiden.
keywords: Guthabenwarnung, Abrechnungswarnung
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/9/2017
ms.author: adpick
ms.openlocfilehash: 981cb1153e0268e6572207f8d2401edb23485863
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34607802"
---
# <a name="set-up-billing-or-credit-alerts-for-your-microsoft-azure-subscriptions"></a>Einrichten von Abrechnungs- oder Guthabenwarnungen für Microsoft Azure-Abonnements
Wenn Sie der Kontoadministrator für ein Azure-Abonnement sind, können Sie den Dienst für Azure-Abrechnungswarnungen zum Erstellen von benutzerdefinierten Abrechnungswarnungen verwenden, um die Abrechnungsaktivitäten für Ihre Azure-Konten zu überwachen und zu verwalten.

Dieser Dienst befindet sich in der Vorschau, daher müssen Sie ihn zunächst auf der Seite mit Vorschaufeatures aktivieren.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="set-the-alert-threshold-and-email-recipients"></a>Festlegen des Schwellenwerts für Warnungen und von E-Mail-Empfängern
1. Besuchen Sie [die Seite mit Vorschaufeatures](https://account.windowsazure.com/PreviewFeatures), und aktivieren Sie **Billing Alert Service**.

1. Nachdem Sie die E-Mail-Bestätigung erhalten haben, dass der Abrechnungsdienst für Ihr Abonnement aktiviert ist, besuchen Sie die [Abonnementseite](https://account.windowsazure.com/Subscriptions) im Kontoportal. Klicken Sie auf das zu überwachende Abonnement, und klicken Sie dann auf **Benachrichtigungen**.

    ![Screenshot der Ansicht „Abonnements“ des Azure-Kontocenters mit hervorgehobenen Warnungen][Image1]

2. Klicken Sie anschließend auf **Warnung hinzufügen**, um Ihre erste Warnung zu erstellen. Sie können bis zu fünf Abrechnungswarnungen pro Abonnement mit unterschiedlichen Schwellenwerten und mit jeweils bis zu zwei E-Mail-Empfängern einrichten.

    ![Screenshot der Ansicht „Warnungen“, in der Sie eine Warnung hinzufügen können][Image2]

3. Wenn Sie eine Warnung hinzufügen, weisen Sie ihr einen eindeutigen Namen zu, wählen einen Ausgabenschwellenwert und dann die E-Mail-Adressen aus, an die Warnungen gesendet werden. Beim Einrichten des Schwellenwerts können Sie entweder **Abrechnung gesamt** oder **Monetäre Gutschriften** aus der Liste **Warnung für** auswählen. Für "Abrechnung gesamt" wird eine Warnung gesendet, wenn die Abonnementausgaben den Schwellenwert überschreiten. Für "Monetäre Gutschriften" wird eine Warnung gesendet, wenn das Guthaben unter den Grenzwert fällt. Guthaben gelten normalerweise für die kostenlose Testversion und Visual Studio-Abonnements.

    ![Screenshot der Ansicht zum Hinzufügen von Warnungen, in der Sie Empfänger konfigurieren können][Image3]

Azure unterstützt beliebige E-Mail-Adressen, überprüft dabei jedoch nicht, ob die E-Mail-Adresse funktioniert. Vermeiden Sie daher Tippfehler.

## <a name="check-on-your-alerts"></a>Überprüfen Ihrer Warnungen
Nach dem Einrichten von Warnungen werden diese im Account Center aufgelistet. Zudem wird angezeigt, wie viele zusätzliche Warnungen eingerichtet werden können. Für jede Warnung werden Datum und Uhrzeit des Sendevorgangs angezeigt. Außerdem wird das festgelegte Limit und die Information angegeben, ob es sich um eine Abrechnungssumme oder monetäre Gutschrift handelt. Die Uhrzeit wird als UTC-Zeit (Universal Time Coordinate) im 24-Stunden-Format und das Datum im Format JJJJ-MM-TT angegeben. Klicken Sie in der Liste auf das Pluszeichen einer Warnung, um diese zu bearbeiten, oder klicken Sie auf den Papierkorb, um sie zu löschen.

## <a name="delete-alerts-or-email-addresses-from-the-azure-billing-alert-service"></a>Löschen von Warnungen oder E-Mail-Adressen vom Azure-Abrechnungswarndienst
Wenn Sie Informationen vom Dienst entfernen müssen, aktualisieren Sie die gespeicherte E-Mail-Adresse, oder löschen Sie die Warnung vollständig.

   ![Screenshot der Ansicht zum Löschen der Warnung, in der Sie persönliche Informationen entfernen können][Image4]

## <a name="billing-alerts-for-enterprise-agreement-ea-customers"></a>Abrechnungswarnungen für Kunden mit Enterprise Agreement (EA)
EA-Abonnements werden von diesem Dienst nicht unterstützt, stattdessen können EA-Kunden für alle Abteilungen mit einer Registrierung Warnungen empfangen, indem Ausgabenkontingente eingerichtet werden. Informationen zum Einstieg finden Sie unter [Department Spending Quotas](https://ea.azure.com/helpdocs/departmentSpendingQuotas) (Ausgabenkontingente für Abteilungen) im EA-Portal.

## <a name="learn-more-about-azure-cost-management"></a>Erfahren Sie mehr über die Azure-Kostenverwaltung
- Schätzen Sie Kosten mit dem [Preisrechner](https://azure.microsoft.com/pricing/calculator/), dem [Gesamtbetriebskosten-Rechner](https://aka.ms/azure-tco-calculator) und beim Hinzufügen eines Diensts.
- [Überprüfen Sie regelmäßig Ihre Nutzung und die Kosten im Azure-Portal](billing-getting-started.md#costs).
- Aktivieren Sie [Kostenempfehlungen vom Azure Advisor](../advisor/advisor-cost-recommendations.md).

Weitere Informationen finden Sie im [Leitfaden zur Azure-Kostenverwaltung](billing-getting-started.md).

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png 
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png 
[Image4]: ./media/azure-billing-set-up-alerts/AlertsDeleteScreen1.PNG
