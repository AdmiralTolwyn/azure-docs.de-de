---
title: Erste Schritte mit der Bing-Spracheingabe-API mithilfe von Clientbibliotheken | Microsoft-Dokumentation
titlesuffix: Azure Cognitive Services
description: Verwenden Sie die Clientbibliotheken der Bing-Spracheingabe-API in Microsoft Cognitive Services, um Anwendungen zu entwickeln, mit denen Sprachaudiodaten in Text konvertiert werden.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: 6166875c04ff9d183336a89da56c4b77521d0f29
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55217423"
---
# <a name="get-started-with-bing-speech-service-client-libraries"></a>Erste Schritte mit Clientbibliotheken des Bing-Spracherkennungsdiensts

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Mit dem Bing-Spracherkennungsdienst können Entwickler nicht nur direkte HTTP-Anforderungen per REST-API nutzen, sondern auch Speech-Clientbibliotheken in anderen Sprachen. Für die Speech-Clientbibliotheken gilt Folgendes:

- Unterstützung von erweiterten Funktionen für die Spracherkennung, z.B. Zwischenergebnisse in Echtzeit, lange Audiodatenströme (bis zu 10 Minuten) und fortlaufende Erkennung.
- Bereitstellung einer einfachen und idiomatischen API in der Sprache Ihrer Wahl.
- Ausblendung von nebensächlichen Kommunikationsdetails.

Derzeit sind die folgenden Clientbibliotheken des Bing-Spracherkennungsdiensts verfügbar:

- [C#-Desktopbibliothek](GetStartedCSharpDesktop.md)
- [C#-Dienstbibliothek](GetStartedCSharpServiceLibrary.md)
- [JavaScript-Bibliothek](GetStartedJSWebsockets.md)
- [Java-Bibliothek für Android](GetStartedJavaAndroid.md)
- [Objective-C-Bibliothek für iOS](Get-Started-ObjectiveC-iOS.md)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- Auf der Seite mit den [Beispielen](../samples.md) werden umfassende Beispiele für die Nutzung von Speech-Clientbibliotheken bereitgestellt.
- Falls Sie eine Clientbibliothek benötigen, die noch nicht unterstützt wird, können Sie ein eigenes SDK erstellen. Implementieren Sie das [Speech-WebSocket-Protokoll](../API-Reference-REST/websocketprotocol.md) auf der Plattform, und verwenden Sie die Sprache Ihrer Wahl.

## <a name="license"></a>Lizenz

Alle Cognitive Services-SDKs und -Beispiele sind mit der MIT-Lizenz lizenziert. Weitere Informationen finden Sie [hier](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).

