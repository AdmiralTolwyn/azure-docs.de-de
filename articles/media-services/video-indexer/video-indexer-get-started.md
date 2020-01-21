---
title: 'Registrieren für Video Indexer und Hochladen Ihres ersten Videos: Azure'
titleSuffix: Azure Media Services
description: Erfahren Sie, wie Sie sich beim Video Indexer-Portal registrieren und Ihr erstes Video hochladen.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: tutorial
ms.date: 01/13/2020
ms.author: juliako
ms.openlocfilehash: 3a5ddf5bd4614b68e97e7616173a3e0640007530
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941554"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>Schnellstart: Registrieren und Hochladen Ihres ersten Videos

In diesem Tutorial zu den ersten Schritten wird veranschaulicht, wie Sie sich bei der Video Indexer-Website anmelden und Ihr erstes Video hochladen.

Beim Erstellen eines Video Indexer-Kontos können Sie ein kostenloses Testkonto (mit einer bestimmten Anzahl von kostenlosen Indizierungsminuten) oder eine kostenpflichtige Option wählen (ohne Einschränkung durch eine Kontingentvorgabe). Bei der kostenlosen Testversion stellt Video Indexer bis zu 600 Minuten an kostenloser Indizierungszeit für Websitebenutzer und bis zu 2.400 Minuten an kostenloser Indizierungszeit für API-Benutzer bereit. Bei der kostenpflichtigen Option erstellen Sie ein Video Indexer-Konto, [das mit Ihrem Azure-Abonnement und einem Azure Media Services-Konto verbunden ist](connect-to-azure.md). Die Indizierungszeit wird minutenweise abgerechnet. Außerdem fallen Gebühren für das Azure Media Services-Konto an. 

## <a name="sign-up-for-video-indexer"></a>Registrieren bei Video Indexer

Um mit der Entwicklung mit Video Indexer zu beginnen, müssen Sie sich bei der [Video Indexer](https://www.videoindexer.com)-Website registrieren.

## <a name="upload-a-video-using-the-video-indexer-website"></a>Hochladen eines Videos auf der Video Indexer-Website

> [!NOTE]
> Der Name des Videos darf nicht mehr als 80 Zeichen umfassen.

### <a name="supported-file-formats-for-video-indexer"></a>Unterstützte Dateiformate für Video Indexer

Im Artikel [Eingabecontainer/Dateiformate](../latest/media-encoder-standard-formats.md#input-containerfile-formats) finden Sie eine Liste der Dateiformate, die Sie mit Video Indexer verwenden können.

### <a name="upload-a-video"></a>Hochladen eines Videos

1. Melden Sie sich bei der [Video Indexer](https://www.videoindexer.ai/)-Website an.
2. Klicken Sie auf die Schaltfläche bzw. den Link **Hochladen**, um ein Video hochzuladen.

    ![Upload](./media/video-indexer-get-started/video-indexer-upload.png)

    Nachdem Ihr Video hochgeladen wurde, beginnt Video Indexer mit dem Indizieren und Analysieren des Videos.

    ![Hochgeladen](./media/video-indexer-get-started/video-indexer-uploaded.png) 

    Wenn Video Indexer die Analyse abgeschlossen hat, erhalten Sie eine Benachrichtigung mit einem Link zu Ihrem Video und einer kurzen Beschreibung dazu, was in Ihrem Video gefunden wurde. Beispiel: Personen, Themen, OCR-Daten.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Hochladen und Indizieren Ihrer Videos](upload-index-videos.md).

Nach dem Hochladen und Indizieren eines Videos können Sie die [Video Indexer-Website](video-indexer-view-edit.md) oder das [Video Indexer-Entwicklerportal](video-indexer-use-apis.md) verwenden, um die aus dem Video gewonnenen Erkenntnisse anzuzeigen. 

## <a name="see-also"></a>Weitere Informationen

[What is Video Indexer? (preview)](video-indexer-overview.md) (Was ist Video Indexer? (Vorschauversion))

[Einstieg in APIs](video-indexer-use-apis.md)

