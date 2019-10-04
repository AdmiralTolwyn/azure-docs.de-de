---
title: Erste Schritte mit Blob Storage und verbundenen Visual Studio-Diensten (WebJob-Projekte) | Microsoft Docs
description: Erste Schritte mit Blobspeicher in einem WebJob-Projekt nach dem Herstellen einer Verbindung mit einem Azure-Speicher mithilfe von verbundenen Visual Studio-Diensten
services: storage
author: ghogen
manager: jillfra
ms.assetid: 324c9376-0225-4092-9825-5d1bd5550058
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 1e951fde7e47ccfcce5f64db4ef27ac767d63480
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510654"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>Erste Schritte mit Azure Blob Storage und verbundenen Visual Studio-Diensten (WebJob-Projekte)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Übersicht
Dieser Artikel enthält C#-Codebeispiele, die zeigen, wie Sie einen Prozess auslösen, wenn ein Azure-Blob erstellt oder aktualisiert wird. In den Codebeispielen wird Version 1.x des [WebJobs-SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) verwendet. Wenn Sie über das Visual Studio-Dialogfeld **Verbundene Dienste hinzufügen** ein Speicherkonto zu einem WebJob-Projekt hinzufügen, wird das entsprechende Azure Storage-NuGet-Paket installiert, die entsprechenden .NET-Verweise werden dem Projekt hinzugefügt, und die Verbindungszeichenfolgen für das Speicherkonto werden in der Datei "App.config" aktualisiert.

## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>Auslösen einer Funktion, wenn ein BLOB erstellt oder aktualisiert wird
Dieser Abschnitt beschreibt die Verwendung des **BlobTrigger** -Attributs.

 **Hinweis:** Das WebJobs-SDK durchsucht Protokolldateien nach neuen oder geänderten Blobs. Dieser Vorgang ist naturgemäß recht langsam. Eine Funktion wird unter Umständen erst mehrere Minuten nach der Bloberstellung (oder noch später) ausgelöst.  Wenn Ihre Anwendung Blobs sofort verarbeiten muss, empfiehlt es sich, zusammen mit dem Blob eine Warteschlangennachricht zu erstellen, und für die Funktion, die das Blob verarbeitet, anstelle des **BlobTrigger**-Attributs das **QueueTrigger**-Attribut zu verwenden.

### <a name="single-placeholder-for-blob-name-with-extension"></a>Einzelner Platzhalter für Blobnamen mit Erweiterung
Das folgende Codebeispiel kopiert Textblobs aus dem Container *input* in den Container *output*:

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Der Attributkonstruktor verwendet einen Zeichenfolgenparameter, der den Containernamen und einen Platzhalter für den Blobnamen angibt. Wenn in diesem Beispiel im Container *input* ein Blob mit dem Namen *Blob1.txt* erstellt wird, erstellt die Funktion ein Blob mit dem Namen *Blob1.txt* im Container *output*.

Sie können mit dem Platzhalter für Blobnamen ein Namensmuster angeben, wie im folgenden Codebeispiel gezeigt:

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Dieser Code kopiert nur Blob, deren Namen mit "original-" beginnt. So wird beispielsweise *original-Blob1.txt* aus dem Container *input* als *copy-Blob1.txt* in den Container *output* kopiert.

Wenn Sie ein Namensmuster für Blobnamen angeben müssen, die geschweifte Klammern enthalten, verdoppeln Sie die geschweiften Klammern. Wenn Sie beispielsweise Blob im Container *images* suchen, deren Namen wie folgt lauten:

        {20140101}-soundfile.mp3

verwenden Sie dies für das Muster:

        images/{{20140101}}-{name}

In diesem Beispiel lautet der Platzhalterwert *name* wie folgt: *soundfile.mp3*.

### <a name="separate-blob-name-and-extension-placeholders"></a>Separate Platzhalter für Blobnamen und Erweiterung
Im folgenden Beispiel ändert sich die Dateierweiterung, wenn Blobs aus dem Container *input* in den Container *output* kopiert werden. Der Code protokolliert die Erweiterung des *input*-Blobs und legt die Erweiterung des *output*-Blobs auf *.txt* fest.

        public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
            [Blob("output/{name}.txt")] out string output,
            string name,
            string ext,
            TextWriter logger)
        {
            logger.WriteLine("Blob name:" + name);
            logger.WriteLine("Blob extension:" + ext);
            output = input.ReadToEnd();
        }

## <a name="types-that-you-can-bind-to-blobs"></a>Typen, die Sie an BLOBs binden können
Sie können das **BlobTrigger** -Attribut für die folgenden Typen verwenden:

* **string**
* **TextReader**
* **Stream**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* Andere von [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder)

Wenn Sie direkt mit dem Azure-Speicherkonto arbeiten möchten, können Sie der Methodensignatur auch einen **CloudStorageAccount** -Parameter hinzufügen.

## <a name="getting-text-blob-content-by-binding-to-string"></a>Abrufen von BLOB-Textinhalt durch Bindung an eine Zeichenfolge
Wenn Textblobs erwartet werden, kann **BlobTrigger** auf einen **string**-Parameter angewendet werden. Im folgenden Beispiel wird ein Textblob an einen **string**-Parameter mit dem Namen **logMessage** gebunden. Die Funktion verwendet diesen Parameter, um den Inhalt des Blobs in das Dashboard des WebJobs-SDK zu schreiben.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>Abrufen serialisierter BLOB-Inhalte mithilfe von ICloudBlobStreamBinder
Im folgenden Codebeispiel wird eine Klasse verwendet, die **ICloudBlobStreamBinder** implementiert, um das **BlobTrigger**-Attribut für das Binden eines Blobs an den **WebImage**-Typ zu aktivieren.

        public static void WaterMark(
            [BlobTrigger("images3/{name}")] WebImage input,
            [Blob("images3-watermarked/{name}")] out WebImage output)
        {
            output = input.AddTextWatermark("WebJobs SDK",
                horizontalAlign: "Center", verticalAlign: "Middle",
                fontSize: 48, opacity: 50);
        }
        public static void Resize(
            [BlobTrigger("images3-watermarked/{name}")] WebImage input,
            [Blob("images3-resized/{name}")] out WebImage output)
        {
            var width = 180;
            var height = Convert.ToInt32(input.Height * 180 / input.Width);
            output = input.Resize(width, height);
        }

Der **WebImage**-Bindungscode wird in einer **WebImageBinder**-Klasse bereitgestellt, die von **ICloudBlobStreamBinder** abgeleitet ist.

        public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
        {
            public Task<WebImage> ReadFromStreamAsync(Stream input,
                System.Threading.CancellationToken cancellationToken)
            {
                return Task.FromResult<WebImage>(new WebImage(input));
            }
            public Task WriteToStreamAsync(WebImage value, Stream output,
                System.Threading.CancellationToken cancellationToken)
            {
                var bytes = value.GetBytes();
                return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
            }
        }

## <a name="how-to-handle-poison-blobs"></a>Behandlung von nicht verarbeitbaren BLOBs
Wenn bei einer **BlobTrigger** -Funktion ein Fehler auftritt, wird sie für den Fall, dass es sich um einen vorübergehenden Fehler handelt, erneut vom SDK aufgerufen. Wenn der Inhalt des Blobs den Fehler verursacht hat, misslingt die Funktion bei jedem Versuch, das Blob zu verarbeiten. Standardmäßig ruft das SDK eine Funktion bis zu 5 Mal für ein angegebenes Blob auf. Ist auch der fünfte Versuch nicht erfolgreich, fügt das SDK einer Warteschlange namens *webjobs-blobtrigger-poison*eine Nachricht hinzu.

Die maximale Anzahl von Wiederholungen ist konfigurierbar. Für die Verarbeitung nicht verarbeitbarer Blobs und der dazugehörigen Nachrichtenwarteschlange wird die gleiche **MaxDequeueCount** -Einstellung verwendet.

Die Warteschlangennachricht für nicht verarbeitbare Blobs ist ein JSON-Objekt, das die folgenden Eigenschaften enthält:

* FunctionId (im Format *{WebJob-Name}* .Functions. *{Funktionsname}* ; Beispiel: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" oder "PageBlob")
* ContainerName
* BlobName
* ETag (eine Blobversions-ID, z.B.: „0x8D1DC6E70A277EF“)

Im folgenden Codebeispiel enthält die **CopyBlob**-Funktion Code, der bewirkt, dass bei jedem Aufruf ein Fehler auftritt. Nachdem das SDK die Funktion mit der maximalen Anzahl von Wiederholungen aufgerufen hat, wird in der Warteschlange für nicht verarbeitbare Blobs eine Nachricht erstellt. Diese Nachricht wird von der **LogPoisonBlob**-Funktion verarbeitet.

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("textblobs/output-{name}")] out string output)
        {
            throw new Exception("Exception for testing poison blob handling");
            output = input.ReadToEnd();
        }

        public static void LogPoisonBlob(
        [QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
            TextWriter logger)
        {
            logger.WriteLine("FunctionId: {0}", message.FunctionId);
            logger.WriteLine("BlobType: {0}", message.BlobType);
            logger.WriteLine("ContainerName: {0}", message.ContainerName);
            logger.WriteLine("BlobName: {0}", message.BlobName);
            logger.WriteLine("ETag: {0}", message.ETag);
        }

Das SDK deserialisiert die JSON-Nachricht automatisch. Hier sehen Sie die **PoisonBlobMessage** -Klasse:

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>BLOB-Abrufalgorithmus
Das WebJobs-SDK durchsucht beim Start der Anwendung alle vom **BlobTrigger**-Attribut angegebenen Container. Bei einem großen Speicherkonto kann diese Überprüfung durchaus länger dauern. Daher kann etwas Zeit vergehen, bis neue Blobs gefunden und **BlobTrigger**-Funktionen ausgeführt werden.

Um neue oder geänderte Blobs nach dem Start der Anwendung zu erkennen, liest das SDK in regelmäßigen Abständen die Blobspeicherprotokolle. Die Blobprotokolle werden gepuffert und nur etwa alle 10 Minuten physisch geschrieben. Daher kann nach der Erstellung oder Aktualisierung eines Blobs möglicherweise eine erhebliche Verzögerung eintreten, ehe die entsprechende **BlobTrigger**-Funktion ausgeführt wird.

Für Blobs, die Sie mit dem **Blob** -Attribut erstellen, gibt es eine Ausnahme. Wenn das WebJobs-SDK ein neues Blob erstellt, übergibt es dieses sofort an alle entsprechenden **BlobTrigger** -Funktionen. Wenn Sie also eine Kette von Ein- und Ausgaben von Blobs haben, kann das SDK sie effizient verarbeiten. Wenn es Ihnen bei Ihren Blobverarbeitungsfunktionen für anderweitig erstellte oder aktualisierte Blobs allerdings auf eine geringe Latenz ankommt, empfiehlt sich die Verwendung von **QueueTrigger** anstelle von **BlobTrigger**.

### <a name="blob-receipts"></a>BLOB-Zugänge
Das WebJobs-SDK stellt sicher, dass **BlobTrigger** -Funktionen für ein neues oder aktualisiertes Blob nicht mehrmals aufgerufen werden. Zu diesem Zweck wird mittels Verwaltung der *Blobbelege* bestimmt, ob eine bestimmte Blobversion verarbeitet wurde.

Blobbelege werden in einem Container mit dem Namen *azure-webjobs-hosts* in dem Azure-Speicherkonto gespeichert, das in der Verbindungszeichenfolge "AzureWebJobsStorage" angegeben ist. Ein Blobbeleg enthält die folgenden Informationen:

* Die Funktion, die für das Blob aufgerufen wurde („ *{WebJob-Name}* .Functions. *{Funktionsname}* “; Beispiel: „WebJob1.Functions.CopyBlob“)
* Containername
* Blobtyp ("BlockBlob" oder "PageBlob")
* Blobname
* ETag (eine Blobversions-ID, z.B.: „0x8D1DC6E70A277EF“)

Wenn Sie eine erneute Verarbeitung eines Blobs erzwingen möchten, können Sie den Blobbeleg für dieses Blob manuell aus dem Container *azure-webjobs-hosts* löschen.

## <a name="related-topics-covered-by-the-queues-article"></a>Verwandte in den Artikeln zu Warteschlangen behandelte Themen
Informationen zur Handhabung der Blobverarbeitung, die durch eine Warteschlangennachricht ausgelöst wird, oder zu Szenarien für das WebJobs-SDK, die sich nicht speziell auf die Blobverarbeitung beziehen, finden Sie unter [Verwenden von Azure-Warteschlangenspeicher mit dem WebJobs-SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

In diesem Artikel werden u. a. die folgenden Themen behandelt:

* Asynchrone Funktionen
* Mehrere Instanzen
* Ordnungsgemäßes Herunterfahren
* Verwenden von WebJobs-SDK-Attributen im Hauptteil einer Funktion
* Festlegen der SDK-Verbindungszeichenfolgen im Code
* Festlegen von Werten für WebJobs SDK-Konstruktorparametern im Code
* Konfigurieren von **MaxDequeueCount** für die Behandlung nicht verarbeitbarer Blobs
* Manuelles Auslösen einer Funktion
* Schreiben von Protokollen

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel wurden Codebeispiele bereitgestellt, in denen der Umgang mit gängigen Szenarien für das Arbeiten mit Azure-Blobs veranschaulicht wird. Weitere Informationen zur Verwendung von Azure WebJobs und des WebJobs SDK finden Sie unter [Dokumentationsressourcen für Azure WebJobs](https://go.microsoft.com/fwlink/?linkid=390226).

