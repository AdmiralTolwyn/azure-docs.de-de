---
title: "Hinzufügen des Azure SQL-Datenbank-Connectors in Ihren Logik-Apps | Microsoft Docs"
description: "Übersicht über den Azure SQL-Datenbank-Connector mit REST-API-Parametern"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: d8a319d0-e4df-40cf-88f0-29a6158c898c
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: a3d5cb909dbfcb00f3fbfa0165bb6cd58eb18688
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-the-azure-sql-database-connector"></a>Erste Schritte mit dem Azure SQL-Datenbank-Connector
Mithilfe des Azure SQL-Datenbank-Connectors können Sie für Ihre Organisation Workflows zur Verwaltung von Tabellendaten erstellen. 

SQL-Datenbank ermöglicht Folgendes:

* Erstellen Sie Ihren Workflow, indem Sie einer Kundendatenbank einen neuen Kunden hinzufügen oder einen Auftrag in einer Auftragsdatenbank aktualisieren.
* Verwenden Sie Aktionen, um eine Datenzeile abzurufen, eine neue Zeile einzufügen oder Löschvorgänge auszuführen. Wenn also etwa ein Datensatz in Dynamics CRM Online erstellt wird (Trigger), soll eine Zeile in eine Azure SQL-Datenbank eingefügt werden (Aktion). 

Dieses Thema beschreibt, wie Sie den SQL-Datenbank-Connector in einer Logik-App verwenden, und enthält eine Liste mit Aktionen.

Weitere Informationen zu Logik-Apps finden Sie unter [Was sind Logik-Apps](../logic-apps/logic-apps-what-are-logic-apps.md) sowie unter [Erstellen einer Logik-App zum Verbinden von SaaS-Diensten](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-azure-sql-database"></a>Herstellen einer Verbindung mit Azure SQL-Datenbank
Damit Ihre Logik-App überhaupt auf einen Dienst zugreifen kann, muss zunächst eine *Verbindung* mit dem Dienst hergestellt werden. Eine Verbindung stellt den Kontakt zwischen einer Logik-App und einem anderen Dienst her. Für die Verbindungsherstellung mit SQL-Datenbank erstellen Sie beispielsweise zuerst eine SQL-Datenbank-*Verbindung*. Hierzu geben Sie die Anmeldeinformationen ein, mit denen Sie normalerweise auf den Dienst zugreifen, mit dem Sie eine Verbindung herstellen möchten. Geben Sie in SQL-Datenbank also Ihre SQL-Datenbank-Anmeldeinformationen ein, um die Verbindung zu erstellen. 

#### <a name="create-the-connection"></a>Erstellen der Verbindung
> [!INCLUDE [Create the connection to SQL Azure](../../includes/connectors-create-api-sqlazure.md)]
> 
> 

## <a name="use-a-trigger"></a>Verwenden eines Triggers
Dieser Connector verfügt über keine Trigger. Verwenden Sie andere Trigger, um die Logik-App zu starten – beispielsweise einen Wiederholungstrigger, einen HTTP-Webhook-Trigger oder einen Trigger eines anderen Connectors. Unter [Erstellen einer Logik-App zum Verbinden von SaaS-Diensten](../logic-apps/logic-apps-create-a-logic-app.md) finden Sie ein Beispiel.

## <a name="use-an-action"></a>Verwenden einer Aktion
Eine Aktion ist ein Vorgang, der durch den in einer Logik-App definierten Workflow ausgeführt wird. Weitere Informationen zu Aktionen finden Sie [hier](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

1. Wählen Sie das Pluszeichen aus. Es stehen mehrere Auswahlmöglichkeiten zur Verfügung: **Aktion hinzufügen**, **Bedingung hinzufügen** oder eine der Optionen unter **Mehr**.
   
    ![](./media/connectors-create-api-sqlazure/add-action.png)
2. Wählen Sie **Aktion hinzufügen**aus.
3. Geben Sie im Textfeld die Zeichenfolge „sql“ ein, um eine Liste mit allen verfügbaren Aktionen zu erhalten.
   
    ![](./media/connectors-create-api-sqlazure/sql-1.png) 
4. In unserem Beispiel wählen wir **SQL Server - Get row** (SQL Server – Zeile abrufen) aus. Falls bereits eine Verbindung vorhanden ist, wählen Sie in der Dropdownliste den **Tabellennamen** aus, und geben Sie die **Zeilen-ID** ein, die zurückgegeben werden soll.
   
    ![](./media/connectors-create-api-sqlazure/sample-table.png)
   
    Wenn Sie zur Eingabe der Verbindungsinformationen aufgefordert werden, geben Sie die Details ein, um die Verbindung zu erstellen. Die Eigenschaften werden in diesem Thema unter [Erstellen der Verbindung](connectors-create-api-sqlazure.md#create-the-connection) beschrieben. 
   
   > [!NOTE]
   > In diesem Beispiel geben wir eine Zeile aus einer Tabelle zurück. Fügen Sie zum Anzeigen der Daten aus dieser Zeile eine weitere Aktion hinzu, die eine Datei mit den Feldern aus der Tabelle erstellt. Fügen Sie beispielsweise eine OneDrive-Aktion hinzu, die auf der Grundlage der Felder „FirstName“ und „LastName“ eine neue Datei im Cloudspeicherkonto erstellt. 
   > 
   > 
5. Speichern Sie Ihre Änderungen. (Die Option **Speichern** befindet sich links oben auf der Symbolleiste.) Ihre Logik-App wird gespeichert und ggf. automatisch aktiviert.

## <a name="connector-specific-details"></a>Connectorspezifische Details

Zeigen Sie die in Swagger definierten Trigger und Aktionen sowie mögliche Beschränkungen in den [Connectordetails](/connectors/sql/) an. 

## <a name="next-steps"></a>Nächste Schritte
[Erstellen Sie eine Logik-App](../logic-apps/logic-apps-create-a-logic-app.md). Informieren Sie sich in unserer [API-Liste](apis-list.md)über die anderen verfügbaren Connectors für Logik-Apps.

