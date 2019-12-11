---
title: Herstellen einer Verbindung mit Office 365 Outlook
description: Verwalten von E-Mails, Kontakten und Kalendern mit Office 365-REST-APIs und Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/18/2016
tags: connectors
ms.openlocfilehash: 858366947fe21a20d6f112fc51899d1533a36472
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789605"
---
# <a name="get-started-with-the-office-365-outlook-connector"></a>Erste Schritte mit dem Office 365 Outlook-Connector
Der Office 365 Outlook-Connector ermöglicht die Interaktion mit Outlook in Office 365. Verwenden Sie diesen Connector zum Erstellen, Bearbeiten und Aktualisieren von Kontakten und Kalenderelementen sowie zum Empfangen, Senden und Beantworten von E-Mails.

Office 365 Outlook ermöglicht Folgendes:

* Erstellen Ihres Workflows unter Verwendung der E-Mail- und Kalenderfeatures in Office 365. 
* Verwenden von Triggern, um den Workflow zu starten, wenn beispielsweise eine neue E-Mail vorliegt oder ein Kalenderelement aktualisiert wird.
* Verwenden von Aktionen, um beispielsweise eine E-Mail zu senden oder ein neues Kalenderereignis zu erstellen. Wenn also beispielsweise ein neues Objekt in Salesforce vorhanden ist (Trigger), kann eine E-Mail an Office 365 Outlook gesendet werden (Aktion). 

Dieser Artikel beschreibt, wie Sie den Office 365 Outlook-Connector in einer Logik-App verwenden, und enthält eine Liste mit den Triggern und Aktionen.

> [!NOTE]
> Diese Version des Artikels gilt für die allgemein verfügbare Version von Logic Apps.
> 
> 

Weitere Informationen zu Logik-Apps finden Sie unter [Was sind Logik-Apps](../logic-apps/logic-apps-overview.md) sowie unter [Erstellen einer Logik-App zum Verbinden von SaaS-Diensten](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-office-365"></a>Herstellen einer Verbindung mit Office 365
Damit Ihre Logik-App überhaupt auf einen Dienst zugreifen kann, muss zunächst eine *Verbindung* mit dem Dienst hergestellt werden. Eine Verbindung stellt den Kontakt zwischen einer Logik-App und einem anderen Dienst her. Wenn Sie also etwa eine Verbindung mit Office 365 Outlook herstellen möchten, benötigen Sie zunächst eine *Verbindung* mit Office 365. Geben Sie zum Erstellen einer Verbindung die Anmeldeinformationen ein, mit denen Sie normalerweise auf den Dienst zugreifen, mit dem Sie eine Verbindung herstellen möchten. Geben Sie für Office 365 Outlook also die Anmeldeinformationen Ihres Office 365-Kontos ein, um die Verbindung zu erstellen.

## <a name="create-the-connection"></a>Erstellen der Verbindung
> [!INCLUDE [Steps to create a connection to Office 365](../../includes/connectors-create-api-office365-outlook.md)]
> 
> 

## <a name="use-a-trigger"></a>Verwenden eines Triggers
Ein Trigger ist ein Ereignis, mit dem ein in einer Logik-App definierter Workflow gestartet werden kann. Trigger fragen den Dienst im gewünschten Intervall und mit der gewünschten Häufigkeit ab. Weitere Informationen zu Triggern finden Sie [hier](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Geben Sie in der Logik-App die Zeichenfolge „office 365“ ein, um eine Liste mit den Triggern abzurufen:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-trigger.png)
2. Wählen Sie **Office 365 Outlook - When an upcoming event is starting soon** (Office 365 Outlook – Wenn ein anstehendes Ereignis in Kürze beginnt) aus. Falls bereits eine Verbindung vorhanden ist, wählen Sie in der Dropdownliste einen Kalender aus.
   
    ![](./media/connectors-create-api-office365-outlook/sample-calendar.png)
   
    Wenn Sie zur Anmeldung aufgefordert werden, geben Sie die Anmeldeinformationen ein, um die Verbindung zu erstellen. Die erforderlichen Schritte sind in diesem Thema unter [Erstellen der Verbindung](connectors-create-api-office365-outlook.md#create-the-connection) aufgeführt. 
   
   > [!NOTE]
   > In diesem Beispiel wird die Logik-App ausgeführt, wenn ein Kalenderereignis aktualisiert wird. Fügen Sie zum Anzeigen der Ergebnisse dieses Triggers eine weitere Aktion hinzu, die Ihnen eine SMS sendet. Fügen Sie beispielsweise die Twilio-Aktion *Nachricht senden* hinzu, um eine SMS zu erhalten, wenn das Ereignis in 15 Minuten beginnt. 
   > 
   > 
3. Wählen Sie die Schaltfläche **Bearbeiten** aus, und legen Sie Werte für **Häufigkeit** und **Intervall** fest. Falls die Abfrage durch den Trigger also beispielsweise alle 15 Minuten erfolgen soll, legen Sie **Häufigkeit** auf **Minute** und **Intervall** auf **15** fest. 
   
    ![](./media/connectors-create-api-office365-outlook/calendar-settings.png)
4. Speichern Sie Ihre Änderungen. (Die Option **Speichern** befindet sich links oben auf der Symbolleiste.) Ihre Logik-App wird gespeichert und ggf. automatisch aktiviert.

## <a name="use-an-action"></a>Verwenden einer Aktion
Eine Aktion ist ein Vorgang, der durch den in einer Logik-App definierten Workflow ausgeführt wird. Weitere Informationen zu Aktionen finden Sie [hier](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Wählen Sie das Pluszeichen aus. Sie sehen mehrere Auswahlmöglichkeiten: **Aktion hinzufügen**, **Bedingung hinzufügen** oder eine der Optionen unter **Mehr**.
   
    ![](./media/connectors-create-api-office365-outlook/add-action.png)
2. Wählen Sie **Aktion hinzufügen**aus.
3. Geben Sie im Textfeld die Zeichenfolge „office 365“ ein, um eine Liste mit allen verfügbaren Aktionen zu erhalten.
   
    ![](./media/connectors-create-api-office365-outlook/office365-actions.png) 
4. In unserem Beispiel wählen wir **Office 365 Outlook – Kontakt erstellen** aus. Falls bereits eine Verbindung vorhanden ist, wählen Sie **Ordner-ID**, **Vorname** und andere Eigenschaften aus:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-sampleaction.png)
   
    Wenn Sie zur Eingabe der Verbindungsinformationen aufgefordert werden, geben Sie die Details ein, um die Verbindung zu erstellen. Die Eigenschaften werden in diesem Thema unter [Erstellen der Verbindung](connectors-create-api-office365-outlook.md#create-the-connection) beschrieben. 
   
   > [!NOTE]
   > In diesem Beispiel erstellen wir einen neuen Kontakt in Office 365 Outlook. Für die Erstellung des Kontakts kann die Ausgabe eines anderen Triggers verwendet werden. Fügen Sie beispielsweise den Salesforce-Trigger *Wenn ein Objekt erstellt wird* hinzu. Fügen Sie dann die Office 365 Outlook-Aktion *Kontakt erstellen* hinzu, die den neuen Kontakt in Office 365 auf der Grundlage der Salesforce-Felder erstellt. 
   > 
   > 
5. Speichern Sie Ihre Änderungen. (Die Option **Speichern** befindet sich links oben auf der Symbolleiste.) Ihre Logik-App wird gespeichert und ggf. automatisch aktiviert.

## <a name="connector-specific-details"></a>Connectorspezifische Details

Zeigen Sie die in Swagger definierten Trigger und Aktionen sowie mögliche Beschränkungen in den [Connectordetails](/connectors/office365connector/) an. 

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)