---
title: Hochladen, Codieren und Streamen mit Azure Media Services | Microsoft Docs
description: Führen Sie die Schritte in diesem Tutorial aus, um eine Datei hochzuladen, das Video zu codieren und Ihre Inhalte mit Azure Media Services zu streamen.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 11/11/2018
ms.author: juliako
ms.openlocfilehash: a8d2cf577a6b637e910c283ba8c70d9ea4eedfbb
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334124"
---
# <a name="tutorial-upload-encode-and-stream-videos-using-apis"></a>Tutorial: Hochladen, Codieren und Streamen von Videos mit APIs

Azure Media Services ermöglicht das Codieren Ihrer Mediendateien in Formate, die mit einer Vielzahl von Browsern und Geräten wiedergegeben werden können. So können Sie Ihre Inhalte beispielsweise im HLS-Format von Apple oder im MPEG DASH-Format streamen. Vor dem Streamen sollten Sie Ihre digitale Mediendatei in hoher Qualität codieren. Anleitungen zur Codierung finden Sie im [Codierungskonzept](encoding-concept.md). In diesem Tutorial wird eine lokale Videodatei hochgeladen und die hochgeladene Datei codiert. Sie können auch Inhalt codieren, den Sie über eine HTTPS-URL zugänglich machen. Weitere Informationen finden Sie unter [Erstellen einer Auftragseingabe aus einer HTTP(S)-URL](job-input-from-http-how-to.md).

![Video abspielen](./media/stream-files-tutorial-with-api/final-video.png)

Dieses Tutorial veranschaulicht folgende Vorgehensweisen:    

> [!div class="checklist"]
> * Zugreifen auf die Media Services-API
> * Konfigurieren der Beispiel-App
> * Überprüfen des Codes, mit dem hochgeladen, codiert und gestreamt wird
> * Ausführen der App
> * Testen der Streaming-URL
> * Bereinigen von Ressourcen

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie Visual Studio noch nicht installiert haben, können Sie [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15) abrufen.
- Installieren und verwenden Sie die CLI lokal. Dieser Artikel erfordert mindestens Version 2.0 der Azure CLI. Führen Sie `az --version` aus, um herauszufinden, welche Version Sie haben. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](/cli/azure/install-azure-cli). 

    Derzeit funktionieren nicht alle Befehle der [CLI von Media Services v3](https://aka.ms/ams-v3-cli-ref) in Azure Cloud Shell. Es wird empfohlen, die CLI lokal zu verwenden.

- [Erstellen Sie ein Media Services-Konto.](create-account-cli-how-to.md)

    Merken Sie sich die Werte, die Sie für den Namen der Ressourcengruppe und des Media Services-Kontos verwendet haben.

## <a name="download-the-sample"></a>Herunterladen des Beispiels

Klonen Sie ein GitHub-Repository auf Ihren Computer, das das .NET-Streamingbeispiel enthält, indem Sie den folgenden Befehl verwenden:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Das Beispiel befindet sich im Ordner [UploadEncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/UploadEncodeAndStreamFiles).

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-code-that-uploads-encodes-and-streams"></a>Überprüfen des Codes, mit dem hochgeladen, codiert und gestreamt wird

In diesem Abschnitt werden die Funktionen untersucht, die in der Datei „[Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs)“ des *UploadEncodeAndStreamFiles*-Projekts definiert werden.

Mit dem Beispiel werden die folgenden Aktionen durchgeführt:

1. Erstellen einer neuen Transformation (nach vorheriger Überprüfung, ob die angegebene Transformation vorhanden ist) 
2. Erstellen eines Ausgabemedienobjekts, das als Ausgabe des Codierungsauftrags verwendet wird
3. Erstellen eines Eingabemedienobjekts und Hochladen der angegebenen lokalen Videodatei in das Objekt. Das Medienobjekt wird als Eingabe des Auftrags verwendet. 
4. Übermittlung des Codierungsauftrag mit der erstellten Ein- und Ausgabe
5. Überprüfung des Auftragsstatus
6. Erstellen eines Streaminglocators
7. Erstellen von Streaming-URLs

### <a name="a-idstartusingdotnet-start-using-media-services-apis-with-net-sdk"></a><a id="start_using_dotnet" />Starten der Verwendung von Media Services-APIs mit dem .NET SDK

Um mit der Verwendung von Media Services-APIs in .NET zu beginnen, müssen Sie ein **AzureMediaServicesClient**-Objekt erstellen. Zum Erstellen des Objekts müssen Sie Anmeldeinformationen bereitstellen, die für den Client zum Herstellen einer Verbindung mit Azure mithilfe von Azure AD erforderlich sind. In dem Code, den Sie zu Beginn des Artikels geklont haben, erstellt die Funktion **GetCredentialsAsync** das ServiceClientCredentials-Objekt basierend auf den in der lokalen Konfigurationsdatei angegebenen Anmeldeinformationen. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Erstellen eines Eingabeobjekts und Hochladen einer lokalen Datei in dieses 

Die **CreateInputAsset**-Funktion erstellt ein neues [Eingabeobjekt](https://docs.microsoft.com/rest/api/media/assets) und lädt die angegebene lokale Videodatei in dieses hoch. Dieses Objekt wird als Eingabe für Ihren Codierungsauftrag verwendet. In Media Services v3 kann die Eingabe in einen Auftrag ein Objekt sein, oder es kann sich um Inhalte handeln, die Sie für Ihr Media Services-Konto über HTTPS-URLs zur Verfügung stellen. Wenn Sie erfahren möchten, wie die Codierung aus einer HTTPS-URL erfolgt, lesen Sie [diesen](job-input-from-http-how-to.md) Artikel.  

In Media Services v3 verwenden Sie Azure Storage-APIs zum Hochladen von Dateien. Im folgenden Codeausschnitt wird gezeigt, wie dies funktioniert.

Die folgende Funktion führt diese Aktionen aus:

* Erstellen eines Objekts 
* Abrufen einer nicht schreibgeschützten [SAS-URL](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) für den [Container](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container) des Objekts im Speicher
* Hochladen der Datei in den Container im Speicher mithilfe der SAS-URL

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Erstellen eines Ausgabeobjekts zum Speichern des Ergebnisses eines Auftrags 

Das [Ausgabeobjekt](https://docs.microsoft.com/rest/api/media/assets) speichert das Ergebnis Ihres Codierungsauftrags. Das Projekt definiert die **DownloadResults**-Funktion, die die Ergebnisse aus diesem Ausgabeobjekt in den Ordner „output“ herunterlädt, damit Sie sehen können, was Sie abgerufen haben.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Erstellen einer Transformation und eines Auftrags, der die hochgeladene Datei codiert
Bei der Codierung oder Verarbeitung von Inhalten in Media Services besteht ein allgemeines Muster darin, die Codierungseinstellungen als eine Anleitung einzurichten. Anschließend übermitteln Sie einen **Auftrag**, um diese Anleitung auf ein Video anzuwenden. Durch die Übermittlung von neuen Aufträgen für jedes neue Video wenden Sie diese Anleitung auf alle Videos in Ihrer Bibliothek an. Eine Anleitung wird in Media Services als eine **Transformation** aufgerufen. Weitere Informationen finden Sie unter [Transformationen und Aufträge](transform-concept.md). Das in diesem Tutorial beschriebene Beispiel definiert eine Anleitung, die das Video codiert, damit es auf eine Vielzahl von iOS- und Android-Geräte gestreamt werden kann. 

#### <a name="transform"></a>Transformieren

Beim Erstellen einer neuen [Transformations](https://docs.microsoft.com/rest/api/media/transforms)instanz müssen Sie angeben, was als Ausgabe generiert werden soll. Der erforderliche Parameter ist ein **TransformOutput**-Objekt, wie im folgenden Code gezeigt. Jedes **TransformOutput**-Objekt enthält eine **Voreinstellung**. Die **Voreinstellung** beschreibt die schrittweisen Anweisungen von Video- und/oder Audioverarbeitungsvorgängen, die verwendet werden sollen, um das gewünschte **TransformOutput**-Objekt zu generieren. Das in diesem Artikel beschriebene Beispiel verwendet eine integrierte Voreinstellung namens **AdaptiveStreaming**. Die Voreinstellung codiert das Eingabevideo in eine automatisch generierte Bitratenkette (Paare aus Bitrate und Auflösung) auf Basis der Eingabeauflösung und -bitrate und generiert ISO MP4-Dateien mit H.264-Video und AAC-Audio entsprechend jedem Paar aus Bitrate und Auflösung. Weitere Informationen zu dieser Voreinstellung finden Sie unter [Automatische Erstellen einer Bitratenkette](autogen-bitrate-ladder.md).

Sie können ein integriertes EncoderNamedPreset-Objekt oder benutzerdefinierte Voreinstellungen verwenden. Weitere Informationen finden Sie unter [Anpassen von Encoder-Voreinstellungen](customize-encoder-presets-how-to.md).

Beim Erstellen einer [Transformation](https://docs.microsoft.com/rest/api/media/transforms) sollten Sie zunächst mit der **Get**-Methode überprüfen, ob eine solche bereits vorhanden ist. Der folgende Code zeigt dies.  In Media Services v3 geben **Get**-Methoden für Entitäten **null** zurück, wenn die Entität nicht vorhanden ist (eine Überprüfung des Namens ohne Unterscheidung zwischen Groß-/Kleinschreibung ).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Auftrag

Wie bereits erwähnt, ist das [Transformations](https://docs.microsoft.com/rest/api/media/transforms)objekt die Anleitung und ein [Auftrag](https://docs.microsoft.com/rest/api/media/jobs) die tatsächliche Anforderung an Media Services, diese **Transformation** auf ein bestimmtes Eingabevideo oder einen Audioinhalt anzuwenden. Der **Auftrag** gibt Informationen wie den Speicherort des Eingabevideos und den Speicherort für die Ausgabe an.

In diesem Beispiel wurde das Eingabevideo vom lokalen Computer hochgeladen. Wenn Sie erfahren möchten, wie die Codierung aus einer HTTPS-URL erfolgt, lesen Sie [diesen](job-input-from-http-how-to.md) Artikel.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Warten auf den Abschluss des Auftrags

Der Abschluss des Auftrags nimmt einige Zeit in Anspruch. Wenn er erfolgt ist, möchten Sie benachrichtigt werden. Das Codebeispiel unten zeigt, wie der Status des [Auftrags](https://docs.microsoft.com/rest/api/media/jobs) vom Dienst abgerufen wird. Der Abruf ist aufgrund potenzieller Latenzzeiten keine empfohlene bewährte Methode für Produktionsanwendungen. Der Abruf kann gedrosselt werden, wenn er für ein Konto im Übermaß verwendet wird. Entwickler sollten stattdessen Event Grid verwenden.

Event Grid ist für hohe Verfügbarkeit, konsistente Leistung und dynamische Skalierung ausgelegt. Mit Event Grid können Ihre Apps Ereignisse aus praktisch allen Azure-Diensten sowie aus benutzerdefinierten Quellen überwachen und darauf reagieren. Eine einfache, HTTP-basierte, reaktive Ereignisverarbeitung hilft Ihnen mit intelligentem Filtern und Routing von Ereignissen dabei, effiziente Lösungen zu erstellen.  Weitere Informationen finden Sie unter [Routing von Ereignissen an einen benutzerdefinierten Webendpunkt](job-state-events-cli-how-to.md).

Der **Auftrag** durchläuft in der Regel die folgenden Zustände: **Geplant**, **In der Warteschlange**, **Verarbeitung** und **Abgeschlossen** (der letzte Zustand). Wenn für den Auftrag ein Fehler aufgetreten ist, erhalten Sie den Zustand **Fehler**. Wenn der Auftrag aktuell abgebrochen wird, erhalten Sie **Abbrechen** und **Abgebrochen**, wenn dies geschehen ist.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#WaitForJobToFinish)]

### <a name="get-a-streaminglocator"></a>Abrufen eines StreamingLocator

Nachdem die Codierung abgeschlossen ist, besteht der nächste Schritt darin, das Video im Ausgabeobjekt Clients für die Wiedergabe zur Verfügung zu stellen. Sie können dies in zwei Schritten bewerkstelligen: Erstellen Sie zunächst einen [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) und dann die Streaming-URLs, die Clients verwenden können. 

Der Vorgang des Erstellens eines **StreamingLocator** wird als „Veröffentlichen“ bezeichnet. Standardmäßig ist der **StreamingLocator** sofort nach dem Vornehmen der API-Aufrufe gültig und bleibt es auch, bis er gelöscht wird (es sei denn, Sie konfigurieren die optionalen Start- und Endzeiten). 

Beim Erstellen eines [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) müssen Sie den gewünschten **StreamingPolicyName** angeben. In diesem Beispiel streamen Sie klare (oder unverschlüsselte) Inhalte, daher wird die vordefinierte Richtlinie für unverschlüsseltes Streaming (**PredefinedStreamingPolicy.ClearStreamingOnly**) verwendet.

> [!IMPORTANT]
> Wenn Sie eine benutzerdefinierte [Streamingrichtlinie](https://docs.microsoft.com/rest/api/media/streamingpolicies) verwenden, sollten Sie eine begrenzte Sammlung solcher Richtlinien für Ihr Media Services-Konto erstellen und diese für Ihre StreamingLocators wiederverwenden, wenn dieselben Verschlüsselungsoptionen und Protokolle benötigt werden. Ihr Media Services-Konto weist ein Kontingent für die Anzahl von StreamingPolicy-Einträgen auf. Sie sollten nicht für jeden StreamingLocator eine neue StreamingPolicy erstellen.

Im folgende Code wird davon ausgegangen, dass Sie die Funktion mit einem eindeutigen locatorName aufrufen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Das Beispiel in diesem Thema beschäftigt sich mit Streaming. Sie können den gleichen Aufruf jedoch auch verwenden, um einen StreamingLocator für die Übermittlung von Video über progressiven Download zu erstellen.

### <a name="get-streaming-urls"></a>Abrufen von Streaming-URLs

Nachdem der **StreamingLocator** erstellt wurde, können Sie die Streaming-URLs wie unter [GetStreamingURLs](https://docs.microsoft.com/rest/api/media/streaminglocators) gezeigt abrufen. Um eine URL zu erstellen, müssen Sie den [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints)-Hostnamen und den **StreamingLocator**-Pfad miteinander verketten. In diesem Beispiel wird der *standardmäßige* **StreamingEndpoint** verwendet. Bei der erstmaligen Erstellung eines Media Services-Kontos befindet sich dieser *standardmäßige* **StreamingEndpoint** im Zustand „Beendet“. Sie müssen daher **Start** aufrufen.

> [!NOTE]
> In dieser Methode benötigen Sie den LocatorName, der beim Erstellen des **StreamingLocator** für das Ausgabeobjekt verwendet wurde.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

### <a name="clean-up-resources-in-your-media-services-account"></a>Bereinigen von Ressourcen in Ihrem Media Services-Konto

Im Allgemeinen sollten Sie alles mit Ausnahme der Objekte bereinigen, die Sie wiederverwenden möchten (in der Regel werden Sie Transformationen wiederverwenden, und Sie werden StreamingLocators usw. persistent speichern). Wenn Sie Ihr Konto nach dem Experimentieren bereinigen möchten, sollten Sie die Ressourcen löschen, bei denen Sie nicht beabsichtigen, sie wiederzuverwenden.  Der folgende Code löscht beispielsweise Aufträge.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Ausführen der Beispiel-App

1. Drücken Sie STRG+F5 zum Ausführen der *EncodeAndStreamFiles*-Anwendung.
2. Kopieren Sie eine der Streaming-URLs aus der Konsole.

Dieses Beispiel zeigt die URLs an, die zur Wiedergabe des Videos mit unterschiedlichen Protokollen verwendet werden können:

![Output](./media/stream-files-tutorial-with-api/output.png)

## <a name="test-the-streaming-url"></a>Testen der Streaming-URL

Um den Stream zu testen, wird in diesem Artikel Azure Media Player verwendet. 

> [!NOTE]
> Wenn ein Player auf einer HTTPS-Website gehostet wird, stellen Sie sicher, die URL mit „https“ zu aktualisieren.

1. Öffnen Sie einen Webbrowser, und navigieren Sie zu [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Fügen Sie im Feld **URL:** einen der URL-Streamingwerte ein, die Sie bei der Ausführung der Anwendung abgerufen haben. 
3. Drücken Sie **Player aktualisieren**.

Azure Media Player kann zum Testen verwendet werden, sollte jedoch nicht in einer Produktionsumgebung zum Einsatz kommen. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie keine Ressourcen in Ihrer Ressourcengruppe mehr benötigen, einschließlich der Media Services und Speicherkonten, die Sie für dieses Tutorial erstellt haben, löschen Sie die zuvor erstellte Ressourcengruppe.

Führen Sie den folgenden CLI-Befehl aus:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Multithreading

Die Azure Media Services v3 SDKs sind nicht threadsicher. Beim Entwickeln einer Multithreadanwendung sollten Sie ein neues AzureMediaServicesClient-Objekt pro Thread generieren und verwenden.

## <a name="next-steps"></a>Nächste Schritte

Da Sie nun wissen, wie Sie Ihr Video hochladen, codieren und streamen können, lesen Sie den folgenden Artikel: 

> [!div class="nextstepaction"]
> [Analysieren von Videos](analyze-videos-tutorial-with-api.md)
