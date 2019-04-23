---
title: 'Schnellstart: Verwenden von C# zum Aufrufen der Textanalyse-API'
titleSuffix: Azure Cognitive Services
description: Informationen und Codebeispiele für den schnellen Einstieg in die Verwendung der Textanalyse-API.
services: cognitive-services
author: ashmaka
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 04/12/2019
ms.author: assafi
ms.openlocfilehash: 0bc50ddbd93fce24454ca1628894f87b90d0d57c
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59608145"
---
# <a name="quickstart-using-c-to-call-the-text-analytics-cognitive-service"></a>Schnellstart: Verwenden von C# zum Aufrufen der Textanalyse von Cognitive Services
<a name="HOLTop"></a>

Dieser Artikel veranschaulicht, wie Sie mithilfe der  [Textanalyse-APIs](//go.microsoft.com/fwlink/?LinkID=759711) mit C# Sprachen erkennen, Stimmungen analysieren und Schlüsselausdrücke extrahieren. Der Code wurde für eine .NET Core-Anwendung geschrieben und enthält minimale Verweise auf externe Bibliotheken, damit Sie ihn auch unter Linux oder macOS ausführen können. Den Quellcode für diese Schnellstartanleitung finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/TextAnalytics).

Die technische Dokumentation für die APIs finden Sie in den [API-Definitionen](//go.microsoft.com/fwlink/?LinkID=759346).

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Außerdem benötigen Sie den [Endpunkt und den Zugriffsschlüssel](../How-tos/text-analytics-how-to-access-key.md) – beide wurden der Registrierung für Sie generiert.

## <a name="install-the-nuget-sdk-package"></a>Installieren des NuGet-SDK-Pakets
1. Erstellen Sie eine neue Konsolenlösung mit `.netcoreapp2.0` oder höher in Visual Studio.
1. Klicken Sie mit der rechten Maustaste auf die Projektmappe, und klicken Sie dann auf **Manage NuGet Packages for Solution** (NuGet-Pakete für Projektmappe verwalten).
1. Klicken Sie auf die Registerkarte **Durchsuchen**, und suchen Sie nach **Microsoft.Azure.CognitiveServices.Language.TextAnalytics**.

> [!Tip]
>  Sie können die [HTTP-Endpunkte](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) zwar direkt über C# aufrufen, das SDK Microsoft.Azure.CognitiveServices.Language erleichtert jedoch das Aufrufen des Diensts erheblich, und Sie müssen sich nicht um die Serialisierung und Deserialisierung von JSON kümmern.
>
> Einige nützliche Links:
> - [NuGet-Seite des SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)
> - [SDK-Code](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Language/TextAnalytics)

## <a name="call-the-text-analytics-api-using-the-sdk"></a>Aufrufen der Textanalyse-API mit dem SDK

1. Ersetzen Sie den Code in „Program.cs“ durch den unten stehenden Code. Dieses Programm veranschaulicht die Funktionen der Textanalyse-API in drei Abschnitten (Sprachextraktion, Schlüsselausdrucksextraktion und Stimmungsanalyse).
1. Ersetzen Sie den Headerwert `Ocp-Apim-Subscription-Key` durch einen für Ihr Abonnement gültigen Zugriffsschlüssel.
1. Ersetzen Sie die Region in `Endpoint`. Ihren Endpunkt finden Sie im Übersichtsabschnitt Ihrer Textanalyseressource im [Azure-Portal](<https://ms.portal.azure.com>). Nehmen Sie nur diesen Teil Ihres Endpunkts auf: „https://[region].api.cognitive.microsoft.com“.
1. Führen Sie das Programm aus.

```csharp
using System;
using System.Collections.Generic;
using System.Net.Http;
using System.Threading;
using System.Threading.Tasks;

using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
using Microsoft.Rest;

namespace ConsoleApp1
{
    class Program
    {
        private const string SubscriptionKey = ""; //Insert your Text Anaytics subscription key

        private class ApiKeyServiceClientCredentials : ServiceClientCredentials
        {
            public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", SubscriptionKey);
                return base.ProcessHttpRequestAsync(request, cancellationToken);
            }
        }

        static async Task Main(string[] args)
        {

            // Create a client.
            ITextAnalyticsClient client = new TextAnalyticsClient(new ApiKeyServiceClientCredentials())
            {
                Endpoint = "https://westus.api.cognitive.microsoft.com"
            }; //Replace 'westus' with the correct region for your Text Analytics subscription

            Console.OutputEncoding = System.Text.Encoding.UTF8;

            // Extracting language
            Console.WriteLine("===== LANGUAGE EXTRACTION ======");

            var langResults = await client.DetectLanguageAsync(
                false,
                new LanguageBatchInput(
                    new List<LanguageInput>
                        {
                          new LanguageInput(id: "1", text: "This is a document written in English."),
                          new LanguageInput(id: "2", text: "Este es un document escrito en Español."),
                          new LanguageInput(id: "3", text: "这是一个用中文写的文件")
                        }));

            // Printing language results.
            foreach (var document in langResults.Documents)
            {
                Console.WriteLine($"Document ID: {document.Id} , Language: {document.DetectedLanguages[0].Name}");
            }

            // Getting key-phrases
            Console.WriteLine("\n\n===== KEY-PHRASE EXTRACTION ======");

            var kpResults = await client.KeyPhrasesAsync(
                false,
                new MultiLanguageBatchInput(
                    new List<MultiLanguageInput>
                    {
                        new MultiLanguageInput("ja", "1", "猫は幸せ"),
                        new MultiLanguageInput("de", "2", "Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
                        new MultiLanguageInput("en", "3", "My cat is stiff as a rock."),
                        new MultiLanguageInput("es", "4", "A mi me encanta el fútbol!")
                    }));

            // Printing keyphrases
            foreach (var document in kpResults.Documents)
            {
                Console.WriteLine($"Document ID: {document.Id} ");

                Console.WriteLine("\t Key phrases:");

                foreach (string keyphrase in document.KeyPhrases)
                {
                    Console.WriteLine($"\t\t{keyphrase}");
                }
            }

            // Extracting sentiment
            Console.WriteLine("\n\n===== SENTIMENT ANALYSIS ======");

            var sentimentResults = await client.SentimentAsync(
                false,
                new MultiLanguageBatchInput(
                    new List<MultiLanguageInput>
                    {
                        new MultiLanguageInput("en", "1", "I had the best day of my life."),
                        new MultiLanguageInput("en", "2", "This was a waste of my time. The speaker put me to sleep."),
                        new MultiLanguageInput("es", "3", "No tengo dinero ni nada que dar..."),
                        new MultiLanguageInput("it", "4", "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."),
                    }));


            // Printing sentiment results
            foreach (var document in sentimentResults.Documents)
            {
                Console.WriteLine($"Document ID: {document.Id} , Sentiment Score: {document.Score:0.00}");
            }


            // Identify entities
            Console.WriteLine("\n\n===== ENTITIES ======");

            var entitiesResult = await client.EntitiesAsync(
                false,
                new MultiLanguageBatchInput(
                    new List<MultiLanguageInput>()
                    {
                        new MultiLanguageInput("en", "1", "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."),
                        new MultiLanguageInput("es", "2", "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle.")
                    }));

            // Printing entities results
            foreach (var document in entitiesResult.Documents)
            {
                Console.WriteLine($"Document ID: {document.Id} ");

                Console.WriteLine("\t Entities:");

                foreach (var entity in document.Entities)
                {
                    Console.WriteLine($"\t\tName: {entity.Name},\tType: {entity.Type ?? "N/A"},\tSub-Type: {entity.SubType ?? "N/A"}");
                    foreach (var match in entity.Matches)
                    {
                        Console.WriteLine($"\t\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.EntityTypeScore:F3}");
                    }
                }
            }

            Console.ReadLine();
        }
    }
}
```

## <a name="application-output"></a>Anwendungsausgabe

In der Anwendung werden die folgenden Informationen angezeigt:

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified


===== KEY-PHRASE EXTRACTION ======
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                rock
Document ID: 4
         Key phrases:
                fútbol


===== SENTIMENT ANALYSIS ======
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00


===== ENTITIES ======
Document ID: 1
         Entities:
                Name: Microsoft,        Type: Organization,     Sub-Type: N/A
                        Offset: 0,      Length: 9,      Score: 1.000
                Name: Bill Gates,       Type: Person,   Sub-Type: N/A
                        Offset: 25,     Length: 10,     Score: 1.000
                Name: Paul Allen,       Type: Person,   Sub-Type: N/A
                        Offset: 40,     Length: 10,     Score: 0.999
                Name: April 4,  Type: Other,    Sub-Type: N/A
                        Offset: 54,     Length: 7,      Score: 0.800
                Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
                        Offset: 54,     Length: 13,     Score: 0.800
                Name: BASIC,    Type: Other,    Sub-Type: N/A
                        Offset: 89,     Length: 5,      Score: 0.800
                Name: Altair 8800,      Type: Other,    Sub-Type: N/A
                        Offset: 116,    Length: 11,     Score: 0.800
Document ID: 2
         Entities:
                Name: Microsoft,        Type: Organization,     Sub-Type: N/A
                        Offset: 21,     Length: 9,      Score: 1.000
                Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
                        Offset: 60,     Length: 7,      Score: 0.991
                Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
                        Offset: 71,     Length: 13,     Score: 0.800
                Name: Seattle,  Type: Location, Sub-Type: N/A
                        Offset: 88,     Length: 7,      Score: 1.000
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Textanalyse mit Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Weitere Informationen

 [Übersicht über die Textanalyse](../overview.md) [Häufig gestellte Fragen (FAQ)](../text-analytics-resource-faq.md)

