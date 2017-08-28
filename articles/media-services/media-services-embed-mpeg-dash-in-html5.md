---
title: Einbetten eines Videos mit adaptivem Streaming im MPEG-DASH-Format in eine HTML5-Anwendung mit DASH.js | Microsoft-Dokumentation
description: "In diesem Thema wird die Einbettung eines Videos mit adaptivem Streaming im MPEG-DASH-Format mithilfe von DASH.js in eine HTML5-Anwendung erläutert."
author: Juliako
manager: SyntaxC4
editor: 
services: media-services
documentationcenter: 
ms.assetid: 5aa0e7b6-f5c3-4cc1-aa33-ed16ea4780c2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 35ba9161f70a27a215685830d1a9e7c1881cc3bb
ms.contentlocale: de-de
ms.lasthandoff: 11/17/2016

---
# <a name="embedding-a-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>Einbetten eines Videos mit adaptivem Streaming im MPEG-DASH-Format in eine HTML5-Anwendung mit DASH.js
## <a name="overview"></a>Übersicht
MPEG-DASH ist ein ISO-Standard für adaptives Streaming von Videoinhalten, der wichtige Vorteile für das Bereitstellen qualitativ hochwertiger Videoausgaben mit adaptivem Streaming bietet. Mit MPEG-DASH wird der Videostream automatisch auf eine niedrigere Auflösung gesetzt, wenn das Netzwerk überlastet ist. Dadurch verringert sich die Wahrscheinlichkeit eines "angehaltenen" Videos, während der Player die nächsten Sekunden für die Wiedergabe herunterlädt (Pufferung). Wenn die Netzwerküberlastung abnimmt, kehrt der Videoplayer zu einem Streaming mit höherer Qualität zurück. Diese Möglichkeit, die erforderliche Bandbreite anzupassen, führt außerdem zu einer kürzeren Startzeit für Videos. Das bedeutet, dass die ersten Sekunden in einem schnell herunterladbaren Segment mit niedriger Qualität wiedergegeben werden können und die Qualität anschließend erhöht wird, wenn genügend Inhalte gepuffert wurden.

Dash.js ist ein Open Source-MPEG-DASH-Videoplayer, der in JavaScript geschrieben wurde. Es handelt sich um einen robusten, plattformübergreifenden Player, der kostenlos in Anwendungen zur Videowiedergabe verwendet werden kann. Er stellt die MPEG-DASH-Wiedergabe in allen Browsern zur Verfügung, die W3C MSE (Media Source Extensions) unterstützen. Zurzeit sind dies Chrome, Microsoft Edge und IE11, für andere Browser wurde eine baldige Unterstützung von MSE zugesichert. Weitere Informationen zu DASH.js finden Sie im dash.js-Repository von GitHub.

## <a name="creating-a-browser-based-streaming-video-player"></a>Erstellen eines browserbasierten Streaming-Videoplayers
So erstellen Sie eine einfache Webseite, auf der ein Videoplayer mit den erwarteten Bedienelementen angezeigt wird, z. B. Wiedergeben, Anhalten, Bildsuchlauf usw.:

1. Erstellen einer HTML-Seite
2. Hinzufügen des Videotags
3. Hinzufügen des Players "dash.js"
4. Initialisieren des Players
5. Hinzufügen von CSS-Formatvorlagen
6. Anzeigen der Ergebnisse in einem Browser, der MSE implementiert

Die Initialisierung des Players kann in nur wenigen Codezeilen des JavaScript-Codes ausgeführt werden. Mit dash.js ist es sehr einfach, MPEG-DASH-Videos in Ihre browserbasierten Anwendungen einzubetten.

## <a name="creating-the-html-page"></a>Erstellen der HTML-Seite
Im ersten Schritt wird eine HTML-Standardseite mit dem **video**-Element erstellt und die Datei unter dem Namen „basicPlayer.html“ gespeichert, wie im folgenden Beispiel veranschaulicht:

    <!DOCTYPE html>
    <html>
      <head><title>Adaptive Streaming in HTML5</title></head>
      <body>
        <h1>Adaptive Streaming with HTML5</h1>
        <video id="videoplayer" controls></video>
      </body>
    </html>

## <a name="adding-the-dashjs-player"></a>Hinzufügen des Players "DASH.js"
Zum Hinzufügen der Referenzimplementierung von "dash.js" zu Ihrer Anwendung müssen Sie die Datei "dash.all.js" vom dash.js-Projekt, Version 1.0, abrufen. Diese Datei sollte im JavaScript-Ordner der Anwendung gespeichert werden. Der gesamte dash.js-Code ist in dieser Datei bequem zusammengefasst. Wenn Sie sich das dash.js-Repository ansehen, finden Sie dort die einzelnen Dateien, den Testcode und vieles mehr. Wenn Sie allerdings nur "dash.js" verwenden möchten, benötigen Sie nur die Datei "dash.all.js".

Zum Hinzufügen des Players "dash.js" zu Ihren Anwendungen fügen Sie dem Anfangsabschnitt von "basicPlayer.html" ein Skript-Tag hinzu:

    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>


Erstellen Sie anschließend eine Funktion zum Initialisieren des Players beim Laden der Seite. Fügen Sie das folgende Skript nach der Zeile ein, in der Sie "dash.all.js" laden:

    <script>
    // setup the video element and attach it to the Dash player
    function setupVideo() {
      var url = "http://wams.edgesuite.net/media/MPTExpressionData02/BigBuckBunny_1080p24_IYUV_2ch.ism/manifest(format=mpd-time-csf)";
      var context = new Dash.di.DashContext();
      var player = new MediaPlayer(context);
                      player.startup();
                      player.attachView(document.querySelector("#videoplayer"));
                      player.attachSource(url);
    }
    </script>

Diese Funktion erstellt zunächst einen DashContext. Dieser wird zum Konfigurieren der Anwendung für eine bestimmte Laufzeitumgebung verwendet. Aus technischer Sicht definiert er die Klassen, die das Dependency Injection-Framework beim Erstellen der Anwendung verwenden soll. In den meisten Fällen verwenden Sie Dash.di.DashContext.

Als Nächstes instanziieren Sie MediaPlayer, die Hauptklasse des dash.js-Frameworks. Diese Klasse enthält die benötigten Kernmethoden, z. B. "Wiedergeben" oder "Anhalten", und verwaltet die Beziehung mit dem Videoelement sowie die Interpretation der MPD-Datei (Media Presentation Description), die das Video beschreibt, das wiedergeben werden soll.

Die startup()-Funktion der MediaPlayer-Klasse wird aufgerufen, um sicherzustellen, dass der Player für die Wiedergabe des Videos bereit ist. Diese Funktion stellt unter anderem sicher, dass alle erforderlichen Klassen (wie vom Kontext definiert) geladen wurden. Wenn der Player bereit ist, können Sie ihn mithilfe der attachView()-Funktion mit dem Videoelement verknüpfen. Auf diese Weise kann MediaPlayer den Videostream in das Element einfügen und zudem die Wiedergabe nach Bedarf steuern.

Übergeben Sie die URL der MPD-Datei an MediaPlayer, damit das Video bekannt ist, das wiedergegeben werden soll. Die soeben erstellte setupVideo()-Funktion muss ausgeführt werden, wenn die Seite vollständig geladen wurde. Verwenden Sie dazu das onload-Ereignis des Textelements. Ändern Sie das <body>-Element wie folgt:

    <body onload="setupVideo()">

Legen Sie schließlich die Größe des Videoelements mithilfe von CSS fest. In einer Umgebung für adaptives Streaming ist dies besonders wichtig, da sich die Größe des wiedergegebenen Videos ändern kann, wenn die Wiedergabe an veränderte Netzwerkbedingungen angepasst wird. In dieser einfachen Demo wird das Videoelement einfach auf 80 % des verfügbaren Browserfensters gesetzt, indem das folgende CSS zum Anfangsabschnitt der Seite hinzugefügt wird:

    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>

## <a name="playing-a-video"></a>Wiedergeben eines Videos
Zeigen Sie zur Wiedergabe eines Videos in Ihrem Browser auf die Datei "basicPlayback.html", und klicken Sie dann im angezeigten Videoplayer auf die Schaltfläche für die Wiedergabe.

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Weitere Informationen
[Entwickeln von Videoplayeranwendungen](media-services-develop-video-players.md)

[dash.js-Repository von GitHub (in englischer Sprache)](https://github.com/Dash-Industry-Forum/dash.js) 


