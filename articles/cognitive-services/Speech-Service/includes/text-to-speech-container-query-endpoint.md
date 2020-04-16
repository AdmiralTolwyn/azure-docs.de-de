---
title: Abfrage eines Containerendpunkts für die Sprachsynthese
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: b2a621a23a81e4fb4f47e7c99d780211973e30a0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81274910"
---
Der Container stellt [REST-basierte Endpunkt-APIs](../rest-text-to-speech.md) bereit. Es gibt viele [Beispielquellcodeprojekte](https://github.com/Azure-Samples/Cognitive-Speech-TTS) für Plattform-, Framework- und Sprachvarianten.

Mit dem Container für die *Standardsprachsynthese* sollten Sie sich auf das Gebietsschema und die Sprache des heruntergeladenen Imagetags verlassen. Wenn Sie z. B. das `latest`-Tag heruntergeladen haben, ist das Standardgebietsschema `en-US` und `JessaRUS` die Stimme. Das `{VOICE_NAME}`-Argument wäre dann [`en-US-JessaRUS`](../language-support.md#standard-voices). Betrachten Sie das folgende SSML-Beispiel:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Für die *benutzerdefinierte Sprachsynthese* müssen Sie jedoch die/das **Stimme/Modell** aus dem [Custom Voice-Portal](https://aka.ms/custom-voice-portal) abrufen. Der benutzerdefinierte Modellname ist gleichbedeutend mit dem Sprachnamen. Navigieren Sie zur Seite **Training**, und kopieren Sie die/das **Stimme/Modell**, die/das als `{VOICE_NAME}`-Argument verwendet werden soll.
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="Custom Voice-Modell – Sprachname":::

Betrachten Sie das folgende SSML-Beispiel:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Lassen Sie uns eine HTTP POST-Anforderung erstellen, die einige Header und eine Datennutzlast bereitstellt. Ersetzen Sie den Platzhalter `{VOICE_NAME}` durch Ihren eigenen Wert.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

Dieser Befehl:

* Erstellt eine HTTP POST-Anforderung für den Endpunkt `speech/synthesize/cognitiveservices/v1`.
* Gibt einen `Accept`-Header von `audio/*` an.
* Gibt einen `Content-Type`-Header von `application/ssml+xml` an. Weitere Informationen finden Sie unter [Anforderungstext](../rest-text-to-speech.md#request-body).
* Gibt einen `X-Microsoft-OutputFormat`-Header von `riff-16khz-16bit-mono-pcm` an. Weitere Optionen finden Sie unter [Audioausgabe](../rest-text-to-speech.md#audio-outputs).
* Sendet die SSML-Anforderung ([Speech Synthesis Markup Language](../speech-synthesis-markup.md)), die `{VOICE_NAME}` an den Endpunkt übergeben hat.