---
title: Erfassen von Daten von Event Hubs in Azure Data Lake Store | Microsoft-Dokumentation
description: Verwenden von Azure Data Lake Store zum Erfassen von Daten von Event Hubs
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 9f91acf8c26fdec0c8d128f598f218cff091c7aa
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2018
---
# <a name="use-azure-data-lake-store-to-capture-data-from-event-hubs"></a>Verwenden von Azure Data Lake Store zum Erfassen von Daten von Event Hubs

Hier erfahren Sie, wie Sie Azure Data Lake Store zum Erfassen von Daten verwenden, die von Azure Event Hubs empfangen werden.

## <a name="prerequisites"></a>Voraussetzungen

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

* **Ein Azure Data Lake Store-Konto**. Eine Anleitung zur Erstellung finden Sie unter [Erste Schritte mit dem Azure Data Lake Store](data-lake-store-get-started-portal.md).

*  **Ein Event Hubs-Namespace**. Anweisungen hierzu finden Sie unter [Erstellen eines Event Hubs-Namespaces](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace). Stellen Sie sicher, dass das Data Lake Store-Konto und der Event Hubs-Namespace im selben Azure-Abonnement enthalten sind.


## <a name="assign-permissions-to-event-hubs"></a>Zuweisen von Berechtigungen für Event Hubs

In diesem Abschnitt erstellen Sie einen Ordner in dem Konto, in dem Sie die Daten von Event Hubs erfassen möchten. Zudem weisen Sie Event Hubs Berechtigungen zu, damit der Dienst Daten in ein Data Lake Store-Konto schreiben kann. 

1. Öffnen Sie das Data Lake Store-Konto, in dem Sie Daten von Event Hubs erfassen möchten, und klicken Sie dann auf **Daten-Explorer**.

    ![Data Lake Store-Daten-Explorer](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Data Lake Store-Daten-Explorer")

2.  Klicken Sie auf **Neuer Ordner**, und geben Sie dann einen Namen für den Ordner ein, in dem Sie die Daten erfassen möchten.

    ![Erstellen eines neuen Ordners in Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "Erstellen eines neuen Ordners in Data Lake Store")

3. Weisen Sie auf der Data Lake Store-Stammebene Berechtigungen zu. 

    a. Klicken Sie auf **Daten-Explorer**, wählen Sie den Stamm des Data Lake Store-Kontos aus, und klicken Sie dann auf **Zugriff**.

    ![Zuweisen von Berechtigungen für den Data Lake Store-Stamm](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "Zuweisen von Berechtigungen für den Data Lake Store-Stamm")

    b. Klicken Sie unter **Zugriff** auf **Hinzufügen** und dann auf **Benutzer oder Gruppe auswählen**, und suchen Sie anschließend nach `Microsoft.EventHubs`. 

    ![Zuweisen von Berechtigungen für den Data Lake Store-Stamm](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Zuweisen von Berechtigungen für den Data Lake Store-Stamm")
    
    Klicken Sie auf **Auswählen**.

    c. Klicken Sie unter **Berechtigungen zuweisen** auf **Berechtigungen auswählen**. Legen Sie **Berechtigungen** auf **Ausführen** fest. Legen Sie **Add to** (Hinzufügen zu) auf **Diesen Ordner und alle untergeordneten Ordner** fest. Legen Sie **Add as** (Hinzufügen als) auf **Ein Zugriffsberechtigungseintrag und ein Standardberechtigungseintrag** fest.

> [!IMPORTANT]
> Wenn Sie eine neue Ordnerhierarchie für die Erfassung der von Azure Event Hubs empfangenen Daten erstellen, ist dies eine einfache Möglichkeit, den Zugriff auf den Zielordner sicherzustellen.  Das Hinzufügen von Berechtigungen zu allen untergeordneten Ordnern eines Ordners auf oberster Ebene mit vielen untergeordneten Dateien und Ordnern kann jedoch lange dauern.  Wenn Ihr Stammordner eine große Anzahl von Dateien und Ordnern enthält, kann es schneller sein, die Berechtigung **Ausführen** für `Microsoft.EventHubs` jedem Ordner im Pfad zu Ihrem endgültigen Zielordner einzeln hinzuzufügen. 

    ![Assign permissions for Data Lake Store root](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "Assign permissions for Data Lake Store root")

    Click **OK**.

4. Weisen Sie Berechtigungen für den Ordner im Data Lake Store-Konto zu, in dem Sie Daten erfassen möchten.

    a. Klicken Sie auf **Daten-Explorer**, wählen Sie den Ordner im Data Lake Store-Konto aus, und klicken Sie dann auf **Zugriff**.

    ![Zuweisen von Berechtigungen für den Data Lake Store-Ordner](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "Zuweisen von Berechtigungen für den Data Lake Store-Ordner")

    b. Klicken Sie unter **Zugriff** auf **Hinzufügen** und dann auf **Benutzer oder Gruppe auswählen**, und suchen Sie anschließend nach `Microsoft.EventHubs`. 

    ![Zuweisen von Berechtigungen für den Data Lake Store-Ordner](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Zuweisen von Berechtigungen für den Data Lake Store-Ordner")
    
    Klicken Sie auf **Auswählen**.

    c. Klicken Sie unter **Berechtigungen zuweisen** auf **Berechtigungen auswählen**. Legen Sie **Berechtigungen** auf **Read, Write** (Lesen, Schreiben) und **Ausführen** fest. Legen Sie **Add to** (Hinzufügen zu) auf **Diesen Ordner und alle untergeordneten Ordner** fest. Legen Sie abschließend **Add as** (Hinzufügen als) auf **Ein Zugriffsberechtigungseintrag und ein Standardberechtigungseintrag** fest.

    ![Zuweisen von Berechtigungen für den Data Lake Store-Ordner](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "Zuweisen von Berechtigungen für den Data Lake Store-Ordner")
    
    Klicken Sie auf **OK**. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-store"></a>Konfigurieren von Event Hubs zum Erfassen von Daten in Data Lake Store

In diesem Abschnitt erstellen Sie einen Event Hub innerhalb eines Event Hubs-Namespaces. Außerdem konfigurieren Sie den Event Hub zum Erfassen von Daten in einem Azure Data Lake Store-Konto. In diesem Abschnitt wird davon ausgegangen, dass Sie bereits einen Event Hubs-Namespace erstellt haben.

2. Klicken Sie im Bereich **Übersicht** des Event Hubs-Namespaces auf **+ Event Hub**.

    ![Erstellen eines Event Hubs](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "Erstellen eines Event Hubs")

3. Geben Sie die folgenden Werte an, um Event Hubs zum Erfassen von Daten in Data Lake Store zu konfigurieren.

    ![Erstellen eines Event Hubs](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "Erstellen eines Event Hubs")

    a. Geben Sie einen Namen für den Event Hub an.
    
    b. In diesem Tutorial legen Sie **Partitionsanzahl** und **Nachrichtenaufbewahrung** auf die Standardwerte fest.
    
    c. Legen Sie **Erfassen** auf **On** (Ein) fest. Legen Sie das **Zeitfenster** (Häufigkeit der Erfassung) und **Größenfenster** (zu erfassende Datengröße) fest. 
    
    d. Wählen Sie für **Capture Provider** (Erfassungsanbieter) die Option **Azure Data Lake Store** aus, und wählen Sie dann den zuvor erstellten Data Lake Store aus. Geben Sie für **Data Lake Path** (Data Lake-Pfad) den Namen des Ordners ein, den Sie im Data Lake Store-Konto erstellt haben. Sie müssen nur den relativen Pfad zum Ordner angeben.

    e. Übernehmen Sie für **Beispielnamensformate für Erfassungsdateien** den Standardwert. Diese Option steuert die Ordnerstruktur, die unter dem Erfassungsordner erstellt wird.

    f. Klicken Sie auf **Create**.

## <a name="test-the-setup"></a>Testen der Einstellungen

Sie können die Lösung jetzt testen, indem Sie Daten an den Azure Event Hub senden. Folgen Sie den Anweisungen unter [Senden von Ereignissen an Azure Event Hubs](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). Sobald Sie mit dem Senden der Daten beginnen, werden die Daten in der von Ihnen angegebenen Ordnerstruktur in Data Lake Store angezeigt. Wie im folgenden Screenshot gezeigt, sehen Sie beispielsweise eine Ordnerstruktur in Ihrem Data Lake Store.

![EventHub-Beispieldaten in Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "EventHub-Beispieldaten in Data Lake Store")

> [!NOTE]
> Auch wenn keine Nachrichten in Event Hubs eingeben, schreibt Event Hubs leere Dateien, die nur die Header enthalten, in das Data Lake Store-Konto. Die Dateien werden in dem Zeitintervall geschrieben, das Sie beim Erstellen des Event Hubs angegeben haben.
> 
>

## <a name="analyze-data-in-data-lake-store"></a>Analysieren von Daten im Data Lake-Speicher

Sobald die Daten sich in Data Lake Store befinden, können Sie Analyseaufträge zum Verarbeiten und Berechnen der Daten ausführen. Informationen zum Ausführen solcher Aufträge mit Azure Data Lake Analytics finden Sie unter [U-SQL Avro Example](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) (U-SQL-Avro-Beispiel).
  

## <a name="see-also"></a>Weitere Informationen
* [Sichern von Daten in Data Lake Store](data-lake-store-secure-data.md)
* [Kopieren von Daten aus Azure Storage-Blobs in den Data Lake-Speicher](data-lake-store-copy-data-azure-storage-blob.md)
