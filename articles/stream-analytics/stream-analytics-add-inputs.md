---
title: "Hinzufügen von Dateneingaben zu Stream Analytics-Aufträgen | Microsoft Docs"
description: "Erfahren Sie, wie Sie eine Datenquelle als Streamingdateneingabe aus Event Hubs oder als Verweisdaten aus Blobspeicher mit Ihrem Stream Analytics-Auftrag verknüpfen."
keywords: Dateneingabe, Streamingdaten
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 9e59bd24-2a80-4ecb-b6b2-309a07c70bcd
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f95fe2afab1205998f4c8401e162748b50ae9850
ms.lasthandoff: 11/17/2016


---
# <a name="add-a-streaming-data-input-or-reference-data-to-a-stream-analytics-job"></a>Hinzufügen einer Streamingdateneingabe oder von Verweisdaten zu einem Stream Analytics-Auftrag
Erfahren Sie, wie Sie eine Datenquelle als Streamingdateneingabe aus Event Hubs oder als Verweisdaten aus dem Blobspeicher mit Ihrem Stream Analytics-Auftrag verknüpfen.

Azure Stream Analytics-Aufträge können mit einer oder mehreren Dateneingaben verknüpft werden, die jeweils eine Verbindung mit einer vorhandenen Datenquelle definieren. Werden Daten an diese Datenquelle gesendet, werden sie von dem Stream Analytics-Auftrag übernommen und in Echtzeit als Streamingdaten verarbeitet. Stream Analytics verfügt über eine hervorragende Integration in [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) und [Azure Blob Storage](../storage/storage-dotnet-how-to-use-blobs.md) sowohl innerhalb als auch außerhalb des Auftragsabonnements.

Dieser Artikel ist ein Schritt im [Stream Analytics-Lernpfad](/documentation/learning-paths/stream-analytics/).

## <a name="data-input-streaming-data-and-reference-data"></a>Dateneingabe: Streamen von Daten und Referenzdaten
Es gibt zwei unterschiedliche Eingabetypen in Stream Analytics: Datenströme und Verweisdaten.

* **Datenströme**: Stream Analytics-Aufträge müssen mindestens eine Datenstromeingabe enthalten, die vom Auftrag genutzt und umgewandelt werden soll. Azure-BLOB-Speicher und Azure Event Hubs werden als Datenstrom-Eingabequellen unterstützt. Azure Event Hubs werden verwendet, um Ereignisströme von verbundenen Geräten, Diensten und Anwendungen zu erfassen. Azure-Blobspeicher kann als eine Eingabequelle für das Erfassen von Massendaten als Strom verwendet werden.  
* **Verweisdaten**: Stream Analytics unterstützt einen zweiten zusätzlichen Eingabetyp, der als „Verweisdaten“ bezeichnet wird.  Im Gegensatz zu Daten in Bewegung sind diese Daten statisch oder ändern sich nur langsam.  In der Regel werden Sie zum Durchführen von Suchen und Korrelationen mit Datenströmen verwendet, um einen umfangreicheren Datensatz zu erstellen.  Azure-BLOB-Speicher ist derzeit die einzige unterstützte Eingabequelle für Verweisdaten.  

So fügen Sie Ihrem Stream Analytics-Auftrag eine Eingabe hinzu:

1. Klicken Sie im Azure-Portal auf **Eingaben** und dann in Ihrem Stream Analytics-Auftrag auf **Eingabe hinzufügen**.
   
    ![Klassisches Azure-Portal – Hinzufügen einer Eingabe](./media/stream-analytics-add-inputs/1-stream-analytics-add-inputs.png)  
   
    Klicken Sie im Azure-Portal in Ihrem Stream Analytics-Auftrag auf die Kachel **Eingaben** .  
   
    ![Azure-Portal – Hinzufügen von Dateneingaben](./media/stream-analytics-add-inputs/7-stream-analytics-add-inputs.png)  
2. Geben Sie den Eingabetyp an: entweder **Datenstrom** oder **Verweisdaten**.
   
    ![Hinzufügen der richtigen Dateneingabe: Datenstrom oder Verweis](./media/stream-analytics-add-inputs/2-stream-analytics-add-inputs.png)  
   
    ![Hinzufügen der richtigen Dateneingabe: Datenstrom oder Verweis](./media/stream-analytics-add-inputs/8-stream-analytics-add-inputs.png)  
3. Geben Sie beim Erstellen einer Datenstromeingabe den Quelltyp für die Eingabe an.  Während der Erstellung von Verweisdaten kann dieser Schritt übersprungen werden, da zu diesem Zeitpunkt nur Blobspeicher unterstützt wird.
   
    ![Hinzufügen eines Datenstroms als Dateneingabe](./media/stream-analytics-add-inputs/3-stream-analytics-add-inputs.png)  
   
    ![Hinzufügen eines Datenstroms als Dateneingabe](./media/stream-analytics-add-inputs/9-stream-analytics-add-inputs.png)  
4. Geben Sie im Feld „Eingabealias“ einen Anzeigenamen für diese Eingabe ein.  Dieser Name wird später in der Abfrage Ihres Auftrags zum Verweisen auf die Eingabe verwendet.
   
    Geben Sie den Rest der erforderlichen Verbindungseigenschaften ein, um eine Verbindung mit Ihrer Datenquelle herzustellen. Diese Felder unterscheiden sich je nach Eingabe- und Quelltyp und sind [hier](stream-analytics-create-a-job.md)im Detail definiert.  
   
    ![Hinzufügen eines Event Hubs als Dateneingabe](./media/stream-analytics-add-inputs/4-stream-analytics-add-inputs.png)  
5. Geben Sie die Serialisierungseinstellungen für die Eingabedaten an:
   
   * Um sicherzustellen, dass Ihre Abfragen erwartungsgemäß funktionieren, geben Sie das **Ereignisserialisierungsformat** von eingehenden Daten an.  Unterstützte Serialisierungsformate sind JSON, CSV und Avro.
   * Überprüfen Sie die **Codierung** für die Daten.  UTF-8 ist derzeit das einzige unterstützte Codierungsformat.
     
     ![Datenserialisierungseinstellungen für die Dateneingabe](./media/stream-analytics-add-inputs/5-stream-analytics-add-inputs.png)  
     
     ![Datenserialisierungseinstellungen für die Dateneingabe](./media/stream-analytics-add-inputs/10-stream-analytics-add-inputs.png)  
6. Nach Abschluss der Eingabeerstellung überprüft Stream Analytics, ob eine Verbindung mit der Eingabequelle hergestellt werden kann.  Sie können den Status des Verbindungstests im Benachrichtigungshub anzeigen.
   
    ![Verbindungstest für die Streamingdateneingabe](./media/stream-analytics-add-inputs/6-stream-analytics-add-inputs.png)  
   
    ![Verbindungstest für die Streamingdateneingabe](./media/stream-analytics-add-inputs/11-stream-analytics-add-inputs.png)  

## <a name="get-help-with-streaming-data-inputs"></a>Abrufen von Hilfe zum Streamen von Dateneingaben
Um Hilfe zu erhalten, besuchen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Nächste Schritte
* [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
* [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md)
* [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)


