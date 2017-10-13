---
title: "Überwachen von Surface Hubs mit Azure Log Analytics | Microsoft-Dokumentation"
description: "Verwenden Sie die Surface Hub-Lösung, um die Integrität Ihrer Surface Hubs zu überwachen und um zu verstehen, wie sie verwendet werden."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 8b4e56bc-2d4f-4648-a236-16e9e732ebef
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b6ecd0d09589fec85c1633f528afc1165c346b7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-surface-hubs-with-log-analytics-to-track-their-health"></a>Überwachen von Surface Hubs mit Log Analytics zum Verfolgen ihrer Integrität

![Surface Hub-Symbol](./media/log-analytics-surface-hubs/surface-hub-symbol.png)

Dieser Artikel beschreibt, wie Sie die Surface Hub-Lösung in Log Analytics verwenden können, um Microsoft Surface Hub-Geräte mit der Microsoft Operations Management Suite (OMS) zu überwachen. Mit Log Analytics können Sie die Integrität Ihrer Surface Hubs überwachen und untersuchen, wie sie verwendet werden.

Auf jedem Surface Hub ist Microsoft Monitoring Agent installiert. Über den Agent können Sie Daten von Ihrem Surface Hub an OMS senden. Protokolldateien werden auf den Surface Hubs gelesen und dann an den OMS-Dienst gesendet. Probleme wie Server, die offline sind, Kalender, die nicht synchronisiert werden, oder ein Gerätekonto, das sich nicht bei Skype anmelden kann, werden in OMS im Surface Hub-Dashboard angezeigt. Mithilfe der Daten im Dashboard können Sie Geräte identifizieren, die nicht ausgeführt werden oder bei denen andere Probleme vorliegen, und möglicherweise Fehlerbehebungen für die erkannten Probleme anwenden.

## <a name="installing-and-configuring-the-solution"></a>Installieren und Konfigurieren der Lösung
Verwenden Sie die folgenden Informationen zum Installieren und Konfigurieren der Lösung. Um die Surface Hubs über die Microsoft Operations Management Suite (OMS) zu verwalten, benötigen Sie Folgendes:

* Ein gültiges Abonnement für [OMS](http://www.microsoft.com/oms).
* Eine [OMS-Abonnementebene](https://azure.microsoft.com/pricing/details/log-analytics/), die die Anzahl der Geräte unterstützt, die Sie überwachen möchten. OMS-Preise hängen davon ab, wie viele Geräte registriert sind und wie viele Daten verarbeitet werden. Sie sollten dies bei der Planung Ihrer Surface Hub-Implementierung berücksichtigen.

Anschließend fügen Sie entweder ein OMS-Abonnement zu Ihrem vorhandenen Microsoft Azure-Abonnement hinzu oder erstellen direkt über das OMS-Portal einen neuen Arbeitsbereich. Detaillierte Anweisungen für die Nutzung der Methoden finden Sie unter [Erste Schritte mit Log Analytics](log-analytics-get-started.md). Nachdem das OMS-Abonnement eingerichtet wurde, haben Sie zwei Möglichkeiten, Ihre Surface Hub-Geräte zu registrieren:

* Automatisch über Intune
* Manuell über **Einstellungen** auf Ihrem Surface Hub-Gerät

## <a name="set-up-monitoring"></a>Einrichten der Überwachung
Sie können die Integrität und Aktivität des Surface Hubs mit Log Analytics in OMS überwachen. Sie können den Surface Hub in OMS mit Intune oder lokal über **Einstellungen** auf dem Surface Hub registrieren.

## <a name="connect-surface-hubs-to-oms-through-intune"></a>Verbinden von Surface Hubs mit OMS über Intune
Sie benötigen die Arbeitsbereichs-ID und den Arbeitsbereichsschlüssel für den OMS-Arbeitsbereich, der Ihre Surface Hubs verwalten soll. Sie können beides aus dem OMS-Portal abrufen.

Intune ist ein Microsoft-Produkt, mit dem Sie die OMS-Konfigurationseinstellungen zentral verwalten können, die auf ein oder mehrere Geräte angewendet werden. Gehen Sie wie folgt vor, um Ihre Geräte über Intune zu konfigurieren:

1. Melden Sie sich bei Intune an.
2. Navigieren Sie zu **Einstellungen** > **Verbundene Quellen**.
3. Erstellen oder bearbeiten Sie eine Richtlinie basierend auf der Surface Hub-Vorlage.
4. Navigieren Sie zum OMS-Abschnitt (Azure Operational Insights) der Richtlinie, und fügen Sie die *Arbeitsbereichs-ID* und den *Arbeitsbereichsschlüssel* zur Richtlinie hinzu.
5. Speichern Sie die Richtlinie.
6. Verknüpfen Sie die Richtlinie mit der entsprechenden Gruppe von Geräten.

   ![Intune-Richtlinie](./media/log-analytics-surface-hubs/intune.png)

Intune synchronisiert dann die OMS-Einstellungen mit den Geräten in der Zielgruppe und registriert diese in Ihrem OMS-Arbeitsbereich.

## <a name="connect-surface-hubs-to-oms-using-the-settings-app"></a>Verbinden von Surface Hubs mit OMS über die App „Einstellungen“
Sie benötigen die Arbeitsbereichs-ID und den Arbeitsbereichsschlüssel für den OMS-Arbeitsbereich, der Ihre Surface Hubs verwalten soll. Sie können beides aus dem OMS-Portal abrufen.

Wenn Sie Intune verwenden, um Ihre Umgebung zu verwalten, können Sie Geräte manuell über **Einstellungen** auf jedem Surface Hub registrieren:

1. Öffnen Sie auf Ihrem Surface Hub **Einstellungen**.
2. Geben Sie die Anmeldeinformationen des Administrators für das Gerät auf Aufforderung ein.
3. Klicken Sie auf **Dieses Gerät**, und klicken Sie dann unter **Überwachung** auf **OMS-Einstellungen konfigurieren**.
4. Wählen Sie **Überwachung aktivieren** aus.
5. Geben Sie im Dialogfeld mit den OMS-Einstellungen die **Arbeitsbereichs-ID** und den **Arbeitsbereichsschlüssel** ein.  
   ![Einstellungen](./media/log-analytics-surface-hubs/settings.png)
6. Klicken Sie auf **OK**, um die Konfiguration abzuschließen.

Eine Bestätigung informiert Sie darüber, ob die OMS-Konfiguration erfolgreich auf das Gerät angewendet wurde. Ist dies der Fall, wird eine Meldung angezeigt, die besagt, dass der Agent erfolgreich mit dem OMS-Dienst verbunden wurde. Das Gerät beginnt dann mit dem Senden von Daten an OMS, wo Sie sie anzeigen und darauf reagieren können.

## <a name="monitor-surface-hubs"></a>Überwachen von Surface Hubs
Das Überwachen Ihrer Surface Hubs mit OMS gleicht dem Überwachen anderer registrierter Geräte.

1. Melden Sie sich beim OMS-Portal an.
2. Navigieren Sie zum Dashboard des Surface Hub-Lösungspakets.
3. Die Integrität des Geräts wird angezeigt.

   ![Surface Hub-Dashboard](./media/log-analytics-surface-hubs/surface-hub-dashboard.png)

Sie können [Warnungen](log-analytics-alerts.md) auf der Grundlage von vorhandenen oder benutzerdefinierten Protokollsuchvorgängen erstellen. Mit den Daten, die OMS auf Surface Hubs sammelt, können Sie nach Problemen suchen und Warnungen für die Bedingungen ausgegeben, die Sie für Ihre Geräte definieren.

## <a name="next-steps"></a>Nächste Schritte
* Verwenden Sie die [Protokollsuche in Log Analytics](log-analytics-log-searches.md), um ausführliche Surface Hub-Daten anzuzeigen.
* Erstellen Sie [Warnungen](log-analytics-alerts.md), um benachrichtigt zu werden, wenn Probleme mit Surface Hubs auftreten.
