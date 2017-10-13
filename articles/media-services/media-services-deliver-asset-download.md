---
title: "Herunterladen von Media Services-Medienobjekten auf Ihren Computer – Azure | Microsoft-Dokumentation"
description: "Erfahren Sie, wie Sie Medienobjekte auf Ihren Computer herunterladen. Die Codebeispiele sind in C# geschrieben und verwenden das Media Services SDK für .NET."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 8908a1dd-3ffb-4f18-955d-4c8e2d82fc5d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako
ms.openlocfilehash: d8e740e969f68c85842f42c109328423da1b4414
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-deliver-an-asset-by-download"></a>Gewusst wie: Bereitstellen eines Medienobjekts durch Herunterladen
Dieser Artikel beschreibt Optionen zur Bereitstellung von Medienobjekten, die in Media Services hochgeladen wurden. Sie können Media Services-Inhalte in verschiedenen Anwendungsszenarien bereitstellen. Sie können Medienobjekte herunterladen oder über einen Locator abrufen. Sie können Medieninhalte an andere Anwendungen oder andere Inhaltsanbieter senden. Für verbesserte Leistung und Skalierbarkeit können Sie Inhalte auch über ein Netzwerk für die Inhaltsübermittlung (Content Delivery Network, CDN) anbieten.

Dieses Beispiel zeigt, wie Sie Medienobjekte von Media Services auf Ihren lokalen Computer herunterladen können. Der Code fragt die Jobs des Media Services-Kontos nach Job-ID ab und greift auf die **OutputMediaAssets**-Sammlung zu (eine Sammlung mit einem oder mehreren Ausgabemedienobjekten als Ergebnis einer Jobausführung). Dieses Beispiel zeigt, wie Sie Ausgabemedienobjekte eines Jobs herunterladen können. Dieser Ansatz funktioniert auch für den Download anderer Medienobjekte.

>[!NOTE]
>Es gilt ein Grenzwert von 1.000.000 Richtlinien für verschiedene AMS-Richtlinien (z.B. für die Locator-Richtlinie oder für ContentKeyAuthorizationPolicy). Wenn Sie immer die gleichen Tage/Zugriffsberechtigungen verwenden, z.B. Richtlinien für Locator, die für einen längeren Zeitraum vorgesehen sind (Richtlinien ohne Upload), sollten Sie dieselbe Richtlinien-ID verwenden. Weitere Informationen finden Sie in [diesem](media-services-dotnet-manage-entities.md#limit-access-policies) Thema.

    // Download the output asset of the specified job to a local folder.
    static IAsset DownloadAssetToLocal( string jobId, string outputFolder)
    {
        // This method illustrates how to download a single asset. 
        // However, you can iterate through the OutputAssets
        // collection, and download all assets if there are many. 

        // Get a reference to the job. 
        IJob job = GetJob(jobId);

        // Get a reference to the first output asset. If there were multiple 
        // output media assets you could iterate and handle each one.
        IAsset outputAsset = job.OutputMediaAssets[0];

        // Create a SAS locator to download the asset
        IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
        ILocator locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset, accessPolicy);

        BlobTransferClient blobTransfer = new BlobTransferClient
        {
            NumberOfConcurrentTransfers = 20,
            ParallelTransferThreadCount = 20
        };

        var downloadTasks = new List<Task>();
        foreach (IAssetFile outputFile in outputAsset.AssetFiles)
        {
            // Use the following event handler to check download progress.
            outputFile.DownloadProgressChanged += DownloadProgress;

            string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);

            Console.WriteLine("File download path:  " + localDownloadPath);

            downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));

            outputFile.DownloadProgressChanged -= DownloadProgress;
        }

        Task.WaitAll(downloadTasks.ToArray());

        return outputAsset;
    }

    static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
    {
        Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
    }



## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Weitere Informationen
[Bereitstellen von Streaming-Inhalten](media-services-deliver-streaming-content.md)

