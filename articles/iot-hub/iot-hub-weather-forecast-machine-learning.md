---
title: Wettervorhersage in Azure Machine Learning mit Sensordaten von IoT Hub | Microsoft-Dokumentation
description: Verwenden Sie Azure Machine Learning, um das Regenrisiko basierend auf den Temperatur- und Luftfeuchtigkeitsdaten vorherzusagen, die Ihr IoT Hub von einem Sensor erfasst.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timlt
tags: ''
keywords: Wettervorhersage, Machine Learning
ms.assetid: 8ba7d9e7-699c-4448-b353-0f3e1429d198
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: 453b4de8a93e897b4455403855438d7705945514
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2018
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>Wettervorhersage mithilfe von Sensordaten Ihres IoT Hub in Azure Machine Learning

![Lückenloses Diagramm](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Machine Learning ist ein Data Science-Verfahren, bei dem Computer aus vorhandenen Daten lernen können, um zukünftiges Verhalten, Ergebnisse und Trends vorherzusagen. Azure Machine Learning ist ein Predictive Analytics-Clouddienst, der die schnelle Erstellung und Bereitstellung von Vorhersagemodellen als Analyselösungen ermöglicht.

## <a name="what-you-learn"></a>Lerninhalt

Sie erfahren, wie Sie Azure Machine Learning für eine Wettervorhersage (Regenrisiko) mithilfe der Temperatur- und Luftfeuchtigkeitsdaten von Ihrem Azure IoT Hub verwenden. Das Regenrisiko ist die Ausgabe eines vorbereiteten Wettervorhersagemodells. Das Modell basiert auf Verlaufsdaten zur Vorhersage der Regenwahrscheinlichkeit basierend auf Temperatur und Luftfeuchtigkeit.

## <a name="what-you-do"></a>Aufgaben

- Stellen Sie das Wettervorhersagemodell als Webdienst bereit.
- Bereiten Sie Ihren IoT Hub für den Datenzugriff vor, indem Sie eine Consumergruppe hinzufügen.
- Erstellen Sie einen Stream Analytics-Auftrag, und konfigurieren Sie ihn für folgende Aufgaben:
  - Lesen der Temperatur- und Luftfeuchtigkeitsdaten von Ihrem IoT Hub.
  - Aufrufen des Webdiensts, um das Regenrisiko abzurufen.
  - Speichern des Ergebnisses in einem Azure Blob Storage.
- Verwenden des Microsoft Azure Storage-Explorers zur Anzeige der Wettervorhersage.

## <a name="what-you-need"></a>Voraussetzungen

- Abschluss des Tutorials [Einrichten Ihres Geräts](iot-hub-raspberry-pi-kit-node-get-started.md), in dem die folgenden Anforderungen behandelt werden:
  - Ein aktives Azure-Abonnement.
  - Ein Azure IoT Hub in Ihrem Abonnement.
  - Eine Clientanwendung, die Nachrichten an Ihren Azure IoT Hub sendet.
- Ein Azure Machine Learning Studio-Konto. ([Machine Learning Studio kostenlos testen](https://studio.azureml.net/)).

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Bereitstellen des Wettervorhersagemodells als Webdienst

1. Wechseln Sie zur [Seite des Wettervorhersagemodells](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).
1. Klicken Sie in Microsoft Azure Machine Learning Studio auf **In Studio öffnen**.
   ![Öffnen des Wettervorhersagemodells in Cortana Intelligence Gallery](media/iot-hub-weather-forecast-machine-learning/2_weather-prediction-model-in-cortana-intelligence-gallery.png)
1. Klicken Sie auf **Run**, um die Schritte im Modell zu überprüfen. Die Ausführung dieses Schritts kann 2 Minuten dauern.
   ![Öffnen des Wettervorhersagemodells in Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/3_open-weather-prediction-model-in-azure-machine-learning-studio.png)
1. Klicken Sie auf **SET UP WEB SERVICE** > **Predictive Web Service**.
   ![Bereitstellen des Wettervorhersagemodells in Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/4-deploy-weather-prediction-model-in-azure-machine-learning-studio.png)
1. Ziehen Sie im Diagramm das **Web service input**-Modul an eine beliebige Stelle in der Nähe des **Score Model**-Moduls.
1. Stellen Sie eine Verbindung des **Web service input**-Moduls mit dem **Score Model**-Modul her.
   ![Herstellen einer Verbindung von zwei Modulen in Azure Machine Learning](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)
1. Klicken Sie auf **RUN**, um die Schritte im Modell zu überprüfen.
1. Klicken Sie auf **DEPLOY WEB SERVICE**, um das Modell als Webdienst bereitzustellen.
1. Laden Sie auf dem Dashboard des Modells das **Excel 2010 or earlier workbook** für **REQUEST/RESPONSE** herunter.

   > [!Note]
   > Stellen Sie auch dann sicher, dass Sie das **Excel 2010 or earlier workbook** herunterladen, wenn eine höhere Version von Excel auf Ihrem Computer ausgeführt wird.

   ![Herunterladen der Excel-Arbeitsmappe für den REQUEST/RESPONSE-Endpunkt](media/iot-hub-weather-forecast-machine-learning/5_download-endpoint-app-excel-for-request-response.png)

1. Öffnen Sie die Excel-Arbeitsmappe, und notieren Sie sich **WEB SERVICE URL** und **ACCESS KEY**.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Erstellen, Konfigurieren und Ausführen eines Stream Analytics-Auftrags

### <a name="create-a-stream-analytics-job"></a>Erstellen eines Stream Analytics-Auftrags

1. Klicken Sie im [Azure-Portal](https://portal.azure.com/) auf **Ressource erstellen** > **Internet der Dinge** > **Stream Analytics-Auftrag**.
1. Geben Sie die folgenden Informationen für den Auftrag ein.

   **Auftragsname**: Der Name des Auftrags. Der Name muss global eindeutig sein.

   **Ressourcengruppe**: Verwenden Sie dieselbe Ressourcengruppe wie für Ihren IoT Hub.

   **Speicherort**: Verwenden Sie denselben Speicherort wie für Ihre Ressourcengruppe.

   **An Dashboard anheften**: Aktivieren Sie diese Option für den leichteren Zugriff auf Ihren IoT Hub über das Dashboard.

   ![Erstellen eines Stream Analytics-Auftrags in Azure](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. Klicken Sie auf **Create**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Hinzufügen einer Eingabe zum Stream Analytics-Auftrag

1. Öffnen Sie den Stream Analytics-Auftrag.
1. Klicken Sie unter **Auftragstopologie** auf **Eingaben**.
1. Klicken Sie im Bereich **Eingaben** auf **Hinzufügen**, und geben Sie die folgenden Informationen ein:

   **Eingabealias**: Der eindeutige Alias für die Eingabe.

   **Quelle**: Wählen Sie **IoT Hub** aus.

   **Consumergruppe**: Wählen Sie die Consumergruppe aus, die Sie erstellt haben.

   ![Hinzufügen einer Eingabe zum Stream Analytics-Auftrag in Azure](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. Klicken Sie auf **Create**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Hinzufügen einer Ausgabe zum Stream Analytics-Auftrag

1. Klicken Sie unter **Auftragstopologie** auf **Ausgaben**.
1. Klicken Sie im Bereich **Ausgaben** auf **Hinzufügen**, und geben Sie die folgenden Informationen ein:

   **Ausgabealias**: Der eindeutige Alias für die Ausgabe.

   **Senke**: Wählen Sie **Blob Storage** aus.

   **Speicherkonto**: Das Speicherkonto für Ihren Blob Storage. Sie können ein Speicherkonto erstellen oder ein vorhandenes auswählen.

   **Container**: Der Container, in dem das Blob gespeichert wird. Sie können einen Container erstellen oder einen vorhandenen auswählen.

   **Ereignisserialisierungsformat**: Wählen Sie **CSV** aus.

   ![Hinzufügen einer Ausgabe zum Stream Analytics-Auftrag in Azure](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. Klicken Sie auf **Create**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Hinzufügen einer Funktion zum Stream Analytics-Auftrag, um den von Ihnen bereitgestellten Webdienst aufzurufen

1. Klicken Sie unter **Auftragstopologie** auf **Funktionen** > **Hinzufügen**.
1. Geben Sie Folgendes ein:

   **Funktionsalias**: Geben Sie `machinelearning` ein.

   **Funktionstyp**: Wählen Sie **Azure ML**.

   **Importoption**: Wählen Sie **Aus einem anderen Abonnement importieren**.

   **URL**: Geben Sie die WEB SERVICE URL aus der Excel-Arbeitsmappe ein, die Sie notiert haben.

   **Schlüssel**: Geben Sie den ACCESS KEY aus der Excel-Arbeitsmappe ein, den Sie notiert haben.

   ![Hinzufügen einer Funktion zum Stream Analytics-Auftrag in Azure](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. Klicken Sie auf **Create**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurieren der Abfrage des Stream Analytics-Auftrags

1. Klicken Sie unter **Auftragstopologie** auf **Abfrage**.
1. Ersetzen Sie den vorhandenen Code durch den folgenden Code:

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[Scored Probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   Ersetzen Sie `[YourInputAlias]` durch den Eingabealias des Auftrags.

   Ersetzen Sie `[YourOutputAlias]` durch den Ausgabealias des Auftrags.

1. Klicken Sie auf **Speichern**.

### <a name="run-the-stream-analytics-job"></a>Ausführen des Stream Analytics-Auftrags

Klicken Sie im Stream Analytics-Auftrag auf **Starten** > **Jetzt** > **Starten**. Sobald der Auftrag erfolgreich gestartet wurde, ändert sich der Status des Auftrags von **Beendet** in **Wird ausgeführt**.

![Ausführen des Stream Analytics-Auftrags](media/iot-hub-weather-forecast-machine-learning/11_run-stream-analytics-job-azure.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Verwenden des Microsoft Azure Storage-Explorers zur Anzeige der Wettervorhersage

Führen Sie die Clientanwendung aus, um das Sammeln der Temperatur- und Luftfeuchtigkeitsdaten sowie das Senden an Ihren IoT Hub zu starten. Für jede Nachricht, die Ihr IoT Hub empfängt, ruft der Stream Analytics-Auftrags den Wettervorhersage-Webdienst auf, um das Regenrisiko vorherzusagen. Das Ergebnis wird dann in Ihrem Azure Blob Storage gespeichert. Der Azure Storage-Explorer ist ein Tool, mit dem Sie das Ergebnis anzeigen können.

1. [Laden Sie den Microsoft Azure Storage-Explorer herunter, und installieren Sie ihn](http://storageexplorer.com/).
1. Öffnen Sie den Azure Storage-Explorer.
1. Melden Sie sich beim Azure-Konto an.
1. Wählen Sie Ihr Abonnement aus.
1. Klicken Sie auf Ihr Abonnement > **Speicherkonten** > Ihr Speicherkonto > **Blobcontainer** > Ihr Container.
1. Öffnen Sie eine CSV-Datei, um das Ergebnis anzuzeigen. In der letzten Spalte ist das Regenrisiko aufgezeichnet.

   ![Abrufen des Ergebnisses der Wettervorhersage mit Azure Machine Learning](media/iot-hub-weather-forecast-machine-learning/12_get-weather-forecast-result-azure-machine-learning.png)

## <a name="summary"></a>Zusammenfassung

Sie haben Azure Machine Learning erfolgreich eingesetzt, um das Regenrisiko basierend auf den Temperatur- und Luftfeuchtigkeitsdaten vorherzusagen, die Ihr IoT Hub empfängt.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]