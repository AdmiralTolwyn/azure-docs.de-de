---
title: 'Optimieren der Ausgabe der Sprachsynthese: Speech-Dienste'
titleSuffix: Azure Cognitive Services
description: Aktivieren Sie die Protokollierung im Speech SDK.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 2d56aba6d62d70c482ed1489a09276eeaa11f956
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025684"
---
# <a name="fine-tune-text-to-speech-output"></a>Optimieren der Ausgabe der Sprachsynthese

Mit den Azure Speech-Diensten können Sie die Geschwindigkeit, Aussprache, Lautstärke, Tonhöhe und Kontur der Ausgabe der Sprachsynthese mithilfe der [Markupsprache für Sprachsynthese (Speech Synthesis Markup Language, SSML)](speech-synthesis-markup.md) anpassen. SSML ist eine XML-basierte Markupsprache, die den Dienst mithilfe von Tags darüber informiert, welche Funktion optimiert werden muss. Die SSML-Nachricht wird dann im Text jeder Anforderung an den Sprachsynthesedienst gesendet. Zur Vereinfachung des Anpassungsprozesses bieten die Speech-Dienste jetzt ein Tool zur [Sprachoptimierung](https://aka.ms/voicetuning) an, mit dem Sie Sprachsyntheseausgaben in Echtzeit visuell überprüfen und optimieren können.

Das Tool zur Sprachoptimierung unterstützt die [standardmäßigen](language-support.md#standard-voices), [neuronalen](language-support.md#text-to-speech) und [benutzerdefinierten Stimmen](how-to-customize-voice-font.md) von Microsoft.

## <a name="get-started-with-the-voice-tuning-tool"></a>Erste Schritte mit dem Tool zur Sprachoptimierung

Bevor Sie mit der Optimierung der Sprachsyntheseausgabe mit dem Tool zur Sprachoptimierung beginnen können, müssen Sie diese Schritte ausführen:

1. Erstellen Sie ein [kostenloses Microsoft-Konto](https://account.microsoft.com/account), wenn Sie noch keines besitzen.
2. Erstellen Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/en-us/free/), wenn Sie noch keines besitzen. Klicken Sie auf **Kostenlos starten**, und erstellen Sie mithilfe Ihres Microsoft-Kontos ein neues Azure-Konto.

3. Erstellen Sie ein Speech-Dienstabonnement im Azure-Portal. Schrittweise Anleitungen zum [Erstellen einer Sprachressource](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure) stehen zur Verfügung.
   >[!NOTE]
   >Wenn Sie eine Sprachressource im Azure-Portal erstellen, müssen die Azure-Standortinformationen mit der TTS-Sprachregion übereinstimmen. Die neuronale TTS-Stimme unterstützt eine Teilmenge der Azure-Standorte. Eine vollständige Liste der unterstützten Standorte finden Sie unter [Regionen](regions.md#text-to-speech).

   >[!NOTE]
   >Sie müssen einen F0- oder S0-Schlüssel im Azure-Portal erstellen, bevor Sie den Dienst nutzen können. Die Sprachoptimierung unterstützt den [Schlüssel der 30-tägigen kostenlosen Testversion](https://review.docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started?branch=release-build-cogserv-speech-services#free-trial) **nicht**.

4. Melden Sie sich beim Portal für [Sprachoptimierung](https://aka.ms/voicetuning) an, und verbinden Sie Ihr Speech-Dienstabonnement. Wählen Sie ein einzelnes Speech-Dienstabonnement, und erstellen Sie dann ein Projekt.
5. Wählen Sie die Option für eine **neue Optimierung** aus. Führen Sie dann die folgenden Schritte durch:

   * Suchen Sie **Alle Abonnements**, und wählen Sie die Option aus.  
   * Wählen Sie **Verbindung mit vorhandenem Abonnement herstellen** aus.  
     ![Stellen Sie eine Verbindung mit einem vorhandenem Abonnement her](./media/custom-voice/custom-voice-connect-subscription.png).
   * Geben Sie Ihren Abonnementschlüssel für Azure Speech-Dienste aus dem Azure-Portal ein, und wählen Sie dann **Hinzufügen**. Ihre Abonnementschlüssel sind im Azure-Portal im Bereich „Ressourcenverwaltung“ verfügbar.  Sie können die Schlüssel auch aus der Abonnementliste im Portal für Sprachoptimierung beziehen.
   * Wenn Sie mehrere Speech-Dienstabonnements haben, die Sie verwenden möchten, wiederholen Sie diese Schritte für jedes Abonnement.

## <a name="customize-the-text-to-speech-output"></a>Anpassen der Ausgabe der Sprachsynthese

Nachdem Sie nun Konten erstellt und Ihr Abonnement verknüpft haben, können Sie mit der Anpassung der Ausgabe der Sprachsynthese beginnen.

1. Wählen Sie eine Stimme aus.
2. Geben Sie den Text ein, den Sie bearbeiten möchten.
3. Bevor Sie mit der Bearbeitung beginnen, spielen Sie die Audiodatei ab, um ein Gefühl für die Ausgabe zu bekommen.
4. Wählen Sie das Wort/den Satz aus, das bzw. den Sie optimieren möchten, und beginnen Sie mit verschiedenen SSML-basierten Funktionen zu experimentieren.

>[!TIP]
> Ausführliche Informationen zur Anpassung von SSML und Optimierung der Sprachausgabe finden Sie unter [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md).

## <a name="limitations"></a>Einschränkungen

Die Optimierung der neuronalen Stimme unterscheidet sich geringfügig von der Optimierung der standardmäßigen und benutzerdefinierten Stimmen.

* Intonation wird für neuronale Stimmen nicht unterstützt.
* Tonhöhen- und Lautstärkefunktionen funktionieren nur mit kompletten Sätzen. Diese Funktionen sind auf Wortebene nicht verfügbar.
* Zur Verdeutlichung können einige neuronale Stimmen basierend auf Wörtern optimiert werden, während andere die Auswahl ganzer Sätze erfordern.

> [!TIP]
> Das Tool zur Sprachoptimierung bietet Kontextinformationen zu Funktionen und Optimierung.

## <a name="next-steps"></a>Nächste Schritte
* [Erstellen einer Speech-Ressource in Azure](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure)
* [Starten der Sprachoptimierung](https://speech.microsoft.com/app.html#/VoiceTuning)
* [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md)
