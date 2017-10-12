---
title: "Hinzufügen von Meldungen in die Warteschlange von Azure Storage mithilfe von Functions | Microsoft-Dokumentation"
description: Erstellen Sie mithilfe von Azure Functions eine serverlose Funktion, die durch eine Meldung an eine Warteschlange von Azure Storage aufgerufen wird.
services: azure-functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: 0b609bc0-c264-4092-8e3e-0784dcc23b5d
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/19/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 822879861ee8189cdd413f0061f26fb91819d88d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="add-messages-to-an-azure-storage-queue-using-functions"></a>Hinzufügen von Meldungen in die Warteschlange von Azure Storage mithilfe von Functions

Eingabe- und Ausgabebindungen bieten in Azure Functions eine deklarative Möglichkeit, eine Verbindung mit externen Dienstdaten Ihrer Funktion herzustellen. Erfahren Sie in diesem Thema, wie Sie eine vorhandene Funktion aktualisieren können, indem Sie eine Ausgabebindung hinzufügen, die Meldungen an Azure Queue Storage sendet.  

![Zeigen Sie die Meldung in den Protokollen an.](./media/functions-integrate-storage-queue-output-binding/functions-integrate-storage-binding-in-portal.png)

## <a name="prerequisites"></a>Voraussetzungen 

[!INCLUDE [Previous topics](../../includes/functions-quickstart-previous-topics.md)]

* Installieren Sie den [Microsoft Azure-Speicher-Explorer](http://storageexplorer.com/).

## <a name="add-binding"></a>Ausgabebindung hinzufügen
 
1. Erweitern Sie sowohl Ihre Funktionen-App als auch Ihre Funktion.

2. Wählen Sie **Integrieren** und **+ Neue Ausgabe** und anschließend **Azure Queue Storage** und dann **Auswählen** aus.
    
    ![Hinzufügen einer Ausgabebindung von Queue Storage in einer Funktion im Azure-Portal](./media/functions-integrate-storage-queue-output-binding/function-add-queue-storage-output-binding.png)

3. Verwenden Sie die Proxyeinstellungen aus der Tabelle: 

    ![Hinzufügen einer Ausgabebindung von Queue Storage in einer Funktion im Azure-Portal](./media/functions-integrate-storage-queue-output-binding/function-add-queue-storage-output-binding-2.png)

    | Einstellung      |  Empfohlener Wert   | Beschreibung                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Warteschlangenname**   | myqueue-items    | Der Name der zu verknüpfenden Warteschlange in Ihrem Speicherkonto. |
    | **Speicherkontoverbindung** | AzureWebJobStorage | Sie können die Speicherkontoverbindung verwenden, die bereits von Ihrer Funktionen-App verwendet wird, oder eine neue erstellen.  |
    | **Name des Meldungsparameters** | outputQueueItem | Der Name des Ausgabebindungsparameters | 

4. Klicken Sie auf **Speichern**, um die Bindung hinzuzufügen.
 
Nachdem Sie eine Ausgabebindung definiert haben, müssen Sie den Code aktualisieren, um die Bindung zum Hinzufügen von Meldungen in eine Warteschlange verwenden zu können.  

## <a name="update-the-function-code"></a>Aktualisieren des Funktionscodes

1. Wählen Sie die Funktion, um den Funktionscode im Editor anzuzeigen. 

2. Aktualisieren Sie für eine C#-Funktion die Definition der Funktion wie folgt, um den Speicherbindungsparameter **outputQueueItem** hinzuzufügen. Überspringen Sie diesen Schritt bei einer JavaScript-Funktion.

    ```cs   
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, 
        ICollector<string> outputQueueItem, TraceWriter log)
    {
        ....
    }
    ```

3. Fügen Sie folgenden Code der Funktion hinzu, kurz bevor die Methode zurückgibt. Verwenden Sie den Ausschnitt, der der Sprache der Funktion entspricht.

    ```javascript
    context.bindings.outputQueueItem = "Name passed to the function: " + 
                (req.query.name || req.body.name);
    ```

    ```cs
    outputQueueItem.Add("Name passed to the function: " + name);     
    ```

4. Klicken Sie zum Speichern der Änderungen auf **Speichern**.

Der an den HTTP-Trigger übergebene Wert ist in einer Meldung beinhaltet, die der Warteschlange hinzugefügt wird.
 
## <a name="test-the-function"></a>Testen der Funktion 

1. Nachdem die Codeänderungen gespeichert wurden, klicken Sie auf **Ausführen**. 

    ![Hinzufügen einer Ausgabebindung von Queue Storage in einer Funktion im Azure-Portal](./media/functions-integrate-storage-queue-output-binding/functions-test-run-function.png)

2. Überprüfen Sie die Protokolle, um sicherzustellen, dass die Funktion erfolgreich durchgeführt wurde. Eine neue Warteschlange mit dem Namen **outqueue** wird in Ihrem Speicherkonto von der Laufzeit von Azure Functions erstellt, wenn die Ausgabebindung zum ersten Mal verwendet wird.

Als Nächstes können Sie Ihr Speicherkonto verknüpfen, um die neue Warteschlange und die von Ihnen hinzugefügte Meldung zu überprüfen. 

## <a name="connect-to-the-queue"></a>Herstellen einer Verbindung mit der Warteschlange

Überspringen Sie die ersten drei Schritte, wenn Sie Storage-Explorer bereits installiert und mit Ihrem Speicherkonto verbunden haben.    

1. Wählen Sie in der Funktion **Integrieren** und die neue Ausgabebindung von **Azure Queue Storage** aus, und erweitern Sie dann **Dokumentation**. Notieren Sie sich sowohl den **Kontonamen** als auch den **Kontoschlüssel**. Diese Anmeldeinformationen benötigen Sie für die Verbindung mit dem Speicherkonto.
 
    ![Beschaffen Sie die Anmeldeinformationen für die Speicherkontenverbindung.](./media/functions-integrate-storage-queue-output-binding/function-get-storage-account-credentials.png)

2. Führen Sie das Tool [Microsoft Azure-Speicher-Explorer](http://storageexplorer.com/) aus, klicken Sie auf das Verbindungssymbol auf der linken Seite, und wählen Sie **Use a storage account name and key** (Name und Schlüssel eines Speicherkontos verwenden) aus. Klicken Sie anschließend auf **Weiter**.

    ![Führen Sie das Tool Storage Account-Explorer aus.](./media/functions-integrate-storage-queue-output-binding/functions-storage-manager-connect-1.png)
    
3. Fügen Sie **Kontoname** und **Kontoschlüssel** aus Schritt 1 in die entsprechenden Felder ein, und wählen Sie **Weiter** und **Verbinden**. 
  
    ![Fügen Sie die Speicheranmeldeinformationen ein, und stellen Sie eine Verbindung her.](./media/functions-integrate-storage-queue-output-binding/functions-storage-manager-connect-2.png)

4. Erweitern Sie das angefügte Speicherkonto und dann **Warteschlangen**, und überprüfen Sie, ob die Warteschlange **myqueue-items** vorhanden ist. Außerdem sollten Sie bereits eine Meldung in der Warteschlange sehen.  
 
    ![Erstellen Sie eine Speicherwarteschlange.](./media/functions-integrate-storage-queue-output-binding/function-queue-storage-output-view-queue.png)
 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

Sie haben eine Ausgabebindung in eine vorhandene Funktion hinzugefügt. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Weitere Informationen zu Bindungen an Queue Storage finden Sie unter [Azure Storage-Warteschlangenbindungen in Azure Functions](functions-bindings-storage-queue.md). 



