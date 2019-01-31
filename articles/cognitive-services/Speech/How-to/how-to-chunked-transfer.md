---
title: Segmentierte Übertragung des Audiodatenstroms | Microsoft-Dokumentation
titlesuffix: Azure Cognitive Services
description: Verwenden der segmentierten Übertragung zum Senden von Audiodatenströmen an den Bing-Spracheingabe-Dienst
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: 82c07332af7d4a5df4a6af15ef65abcd8a00f603
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55216879"
---
# <a name="chunked-transfer-encoding"></a>Codierung für segmentierte Übertragung

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Für das Transkribieren von Sprache in Text können Sie die Audiodaten mit der Spracherkennungs-API von Microsoft als vollständigen Block senden oder in kleinere Audiosegmente unterteilen. Wenn Sie Audiodaten effizient streamen und die Transkriptionslatenz reduzieren möchten, empfehlen wir Ihnen die Verwendung der Codierung für die segmentierte Übertragung ([Chunked Transfer Encoding](https://en.wikipedia.org/wiki/Chunked_transfer_encoding)), um die Audiodaten an den Dienst zu streamen. Bei anderen Implementierungen kann es zu dazu kommen, dass von Benutzern eine höhere Latenz wahrgenommen wird. Weitere Informationen finden Sie auf der Seite [Audiodatenströme](../concepts.md#audio-streams).

> [!NOTE]
> Sie können in einer Anforderung nicht mehr als 10 Sekunden an Audiodaten hochladen, und die Gesamtdauer der Anforderung darf 14 Sekunden nicht überschreiten.
> [!NOTE]
> Sie müssen die Codierung für die segmentierte Übertragung nur angeben, wenn Sie die [REST-APIs](../GetStarted/GetStartedREST.md) verwenden, um den Spracherkennungsdienst aufzurufen. Für Anwendungen, die [Clientbibliotheken](../GetStarted/GetStartedClientLibraries.md) nutzen, muss die Codierung für die segmentierte Übertragung nicht konfiguriert werden.

Im folgenden Code wird gezeigt, wie Sie die Codierung für die segmentierte Übertragung festlegen und eine Audiodatei senden, die in Segmente mit einer Größe von 1024 Byte unterteilt ist.

```cs

HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = @"speech.platform.bing.com";
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{

    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```
