---
title: Aktualisieren und Skalieren einer Azure API Management-Instanz | Microsoft-Dokumentation
description: In diesem Thema wird beschrieben, wie Sie eine Azure API Management-Instanz aktualisieren und skalieren.
services: api-management
documentationcenter: 
author: vladvino
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/17/2017
ms.author: apimpm
ms.openlocfilehash: 6ae977344101c02222fd9930e26a083bf5e3f800
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2017
---
# <a name="upgrade-and-scale-an-api-management-instance"></a>Aktualisieren und Skalieren einer API Management-Instanz 

Kunden können eine APIM-Instanz (API Management) skalieren, indem sie Einheiten hinzufügen und entfernen. Eine **Einheit** umfasst dedizierte Azure-Ressourcen und verfügt über eine bestimmte Lastkapazität, die als Anzahl von API-Aufrufen pro Monat ausgedrückt wird. Diese stellt nicht die maximale Anzahl von Aufrufen, sondern eher einen maximalen Durchsatzwert für die grobe Kapazitätsplanung dar. Der tatsächliche Durchsatz und die Latenz variieren aufgrund der folgenden Faktoren erheblich: Anzahl und Übertragungsrate von parallelen Verbindungen, Anzahl und Art von konfigurierten Richtlinien, Umfang von Anforderungen und Antworten sowie Back-End-Latenzzeiten.

Die Kapazität und der Preis der einzelnen Einheiten richtet sich nach dem **Tarif** der Einheit. Sie können zwischen vier Tarifen wählen: **Developer**, **Basic**, **Standard** und **Premium**. Wenn Sie die Kapazität für einen Dienst eines Tarifs erhöhen müssen, sollten Sie eine Einheit hinzufügen. Falls der Tarif, der in Ihrer APIM-Instanz derzeit ausgewählt ist, das Hinzufügen von weiteren Einheiten nicht zulässt, müssen Sie ein Upgrade auf einen höheren Tarif durchführen. 

Der Preis jeder Einheit und die verfügbaren Features (z.B. Bereitstellung in mehreren Regionen) richten sich nach dem Tarif, den Sie für Ihre APIM-Instanz gewählt haben. Im Artikel zu den [Preisdetails](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) erfahren Sie, welchen Preis pro Einheit und welche Features Sie im jeweiligen Tarif erhalten. 

>[!NOTE]
>Im Artikel zu den [Preisdetails](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) sind ungefähre Werte für die Einheitenkapazität der einzelnen Tarife angegeben. Um noch genauere Werte zu erhalten, sollten Sie sich ein realistisches Szenario für Ihre APIs ansehen. Informationen hierzu finden Sie unten im Abschnitt „Planen der Kapazität“.

## <a name="prerequisites"></a>Voraussetzungen

Zum Ausführen der in diesem Artikel beschriebenen Schritte benötigen Sie Folgendes:

+ Ein aktives Azure-Abonnement.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Eine APIM-Instanz. Weitere Informationen finden Sie unter [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md).

## <a name="how-to-plan-for-capacity"></a>Wie kann die Kapazität geplant werden?

Testen Sie die zu erwartenden Workloads, um zu ermitteln, ob ausreichend Einheiten zur Bewältigung Ihres Datenverkehrs vorhanden sind. 

Wie bereits erwähnt, hängt die Anzahl von Anforderungen, die eine APIM-Einheit pro Sekunde verarbeiten kann, von vielen Variablen ab. Beispiele hierfür sind: das Verbindungsmuster, die Größe der Anforderung und Antwort, für die einzelnen APIs konfigurierte Richtlinien und die Anzahl von Clients, die Anforderungen senden.

Nutzen Sie **Metriken** (mit Verwendung von Azure Monitor-Funktionen), um zu verstehen, wie viel Kapazität jeweils verwendet wird.

### <a name="use-the-azure-portal-to-examine-metrics"></a>Verwenden des Azure-Portals zum Untersuchen von Metriken 

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrer APIM-Instanz.
2. Klicken Sie auf **Metriken**.
3. Wählen Sie unter **Verfügbare Metriken** die Metrik **Kapazität**. 

    Anhand der Metrik „Kapazität“ können Sie ermitteln, wie viel Computekapazität in Ihrem Mandanten genutzt wird. Der Wert wird von den Computeressourcen abgeleitet, die Ihr Mandant verwendet, z.B. Arbeitsspeicher, CPU und Länge von Warteschlangen. Er entspricht nicht direkt der Anzahl der verarbeiteten Anforderungen. Testen Sie dies, indem Sie die Anforderungslast auf Ihren Mandanten erhöhen und prüfen, welcher Wert der Kapazitätsmetrik Ihrer Spitzenlast entspricht. Sie können eine Metrikwarnung festlegen, damit Sie benachrichtigt werden, wenn etwas Unerwartetes passiert. Beispiel: Ihre APIM-Instanz hat ihre erwartete Höchstkapazität seit mehr als 10 Minuten überschritten.

    >[!TIP]
    > Sie können die Warnung so konfigurieren, dass Sie benachrichtigt werden, wenn die Kapazität für Ihren Dienst knapp wird, oder dass eine Logik-App aufgerufen wird, die durch das Hinzufügen einer Einheit automatisch eine Skalierung durchführt.

## <a name="upgrade-and-scale"></a>Aktualisieren und Skalieren 

Wie bereits erwähnt, können Sie zwischen vier Tarifen wählen: **Developer**, **Basic**, **Standard** und **Premium**. Der Tarif **Developer** ist zum Evaluieren des Diensts und nicht zur Verwendung in der Produktion bestimmt. Der Tarif **Developer** verfügt nicht über eine Vereinbarung zum Servicelevel (SLA) und ist nicht skalierbar (Einheiten hinzufügen/entfernen). 

Bei **Basic**, **Standard** und **Premium** handelt es sich um Tarife für die Produktion, die über eine Vereinbarung zum Servicelevel (SLA) verfügen und skaliert werden können. Der Tarif **Basic** ist der niedrigste Tarif mit SLA und er kann auf bis zu zwei Einheiten skaliert werden, während der Tarif **Standard** auf bis zu vier Einheiten skaliert werden kann. Im Tarif **Premium** können Sie eine beliebige Anzahl von Einheiten hinzufügen.

Bei Verwendung des Tarifs **Premium** können Sie eine einzelne API Management-Instanz auf eine beliebige Anzahl von Azure-Regionen verteilen. Wenn Sie einen API Management-Dienst erstellen, enthält die Instanz nur eine Einheit und ist nur in einer Azure-Region angeordnet. Diese erste Region wird als **primäre** Region bezeichnet. Zusätzliche Regionen können leicht hinzugefügt werden. Beim Hinzufügen einer Region geben Sie die Anzahl von Einheiten an, die Sie zuordnen möchten. Beispielsweise können Sie eine Einheit in der **primären** Region und fünf Einheiten in einer anderen Region verwenden. Sie können die Anzahl der Einheiten an den Datenverkehr anpassen, die Sie in jeder Region verzeichnen. Weitere Informationen finden Sie unter [Bereitstellen einer Azure API Management-Dienstinstanz für mehrere Azure-Regionen](api-management-howto-deploy-multi-region.md).

Sie können für alle Tarife ein Upgrade oder ein Downgrade durchführen. Beim Upgrade oder Downgrade können ggf. einige Features wegfallen, z.B. VNETs oder die Bereitstellung in mehreren Regionen bei Downgrades vom Premium-Tarif auf den Standard- oder Basic-Tarif.

>[!NOTE]
>Es kann zwischen 15 und 45 Minuten dauern, bis der Upgrade- bzw. Skalierungsprozess abgeschlossen ist. Sie erhalten nach Abschluss des Prozesses eine Benachrichtigung.

### <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Verwenden des Azure-Portals zum Durchführen von Upgrades und Skalierungen

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrer APIM-Instanz.
2. Wählen Sie die Option **Skalierung und Preise**.
3. Wählen Sie den gewünschten Tarif aus.
4. Geben Sie die Anzahl von **Einheiten** an, die Sie hinzufügen möchten. Sie können entweder den Schieberegler verwenden oder die Anzahl von Einheiten eingeben.<br/>
    Wenn Sie den Tarif **Premium** wählen, müssen Sie zuerst eine Region auswählen.
5. Klicken Sie auf **Speichern**.

## <a name="next-steps"></a>Nächste Schritte

[Bereitstellen einer Azure API Management-Dienstinstanz für mehrere Azure-Regionen](api-management-howto-deploy-multi-region.md)

