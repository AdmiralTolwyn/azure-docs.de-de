---
title: Containerrepositorys und -images
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Zwei Tabellen, die die Containerregistrierungen, Repositorys und Imagenamen für alle Cognitive Service-Angebote darstellen.
ms.service: cognitive-services
ms.topic: include
ms.date: 11/15/2019
ms.author: dapine
ms.openlocfilehash: 2058dd6e52ddb417e24368b27384df9a222c378e
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2019
ms.locfileid: "74142197"
---
### <a name="container-repositories-and-images"></a>Containerrepositorys und -images

Die folgenden Tabellen stellen eine Liste der verfügbaren Containerimages dar, die von Azure Cognitive Services geboten werden. Eine vollständige Liste aller verfügbaren Namen für Containerimages und ihrer verfügbaren Tags finden Sie unter [Cognitive Services-Containerimagetags](../container-image-tags.md).

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Öffentliche „nicht verwaltete“ Vorschau (Containerregistrierung: `mcr.microsoft.com`)

Die Microsoft-Containerregistrierung (MCR) syndikalisiert alle öffentlich verfügbaren, „nicht verwalteten“ Container für Cognitive Services. Sie sind auch direkt über den [Docker-Hub](https://hub.docker.com/_/microsoft-azure-cognitive-services)verfügbar.

| Dienst | Container | Containerregistrierung/Repository/Imagename |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [Textanalyse](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Schlüsselwortextraktion | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Textanalyse](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Spracherkennung | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Textanalyse](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Standpunktanalyse | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Öffentliche "verwaltete" Vorschau (Containerregistrierung: `containerpreview.azurecr.io`)

Die Vorschauversion der Containerregistrierung hostet alle öffentlich verfügbaren, „verwalteten“ Container für Cognitive Services. Diese Container erfordern eine formelle Zugriffsanforderung, um sie zu nutzen.

| Dienst | Container | Containerregistrierung/Repository/Imagename |
|--|--|--|
| [Anomalieerkennung](../../anomaly-detector/anomaly-detector-container-howto.md) | Anomalieerkennung | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Maschinelles Sehen](../../Computer-vision/computer-vision-how-to-install-containers.md) | Lesen | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Gesichtserkennung](../../face/face-how-to-install-containers.md) | Gesicht | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Formularerkennung](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Formularerkennung | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Spracherkennungsdienst-API](../../speech-service/speech-container-howto.md?tab=stt) | Spracherkennung | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Spracherkennungsdienst-API](../../speech-service/speech-container-howto.md?tab=cstt) | Benutzerdefinierte Spracherkennung | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [Spracherkennungsdienst-API](../../speech-service/speech-container-howto.md?tab=tts) | Text-zu-Sprache | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [Spracherkennungsdienst-API](../../speech-service/speech-container-howto.md?tab=ctts) | Benutzerdefinierte Sprachsynthese | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
