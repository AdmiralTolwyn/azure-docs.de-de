---
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.topic: include
ms.date: 11/08/2019
ms.openlocfilehash: ea0ae1b1527aa1f527c8ac8fbcd3b4e4f6b6fe2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789194"
---
* Wenn Sie Azure SQL-Datenbank verwenden, führen Sie die Schritte unter [Herstellen einer Verbindung mit Azure SQL-Datenbank](#connect-azure-sql-db) aus.

* Wenn Sie SQL Server verwenden, führen Sie die Schritte unter [Herstellen einer Verbindung mit SQL Server](#connect-sql-server) aus.

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Herstellen einer Verbindung mit Azure SQL-Datenbank

Wenn Sie durch den SQL-Trigger oder die Aktion aufgefordert werden, Verbindungsinformationen anzugeben, befolgen Sie diese Schritte, die sowohl für Trigger als auch für Aktionen gelten.

1. Erstellen Sie für **Verbindungsname** einen Namen für Ihre Verbindung.

1. Wählen Sie unter **SQL Server-Name** Ihren Azure SQL Server aus. Wenn die Liste **SQL-Datenbankname** angezeigt wird, wählen Sie Ihre Datenbank aus. Geben Sie den Benutzernamen und das Kennwort für Ihren Azure SQL-Server an.

   Sie finden diese Informationen auch im Azure-Portal in den Eigenschaften Ihrer SQL-Datenbank oder in der Verbindungszeichenfolge:

   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Herstellen einer Verbindung mit Azure SQL-Datenbank](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. Wählen Sie **Erstellen**, wenn Sie fertig sind.

1. Nachdem Sie die Verbindung erstellt haben, fahren Sie mit [Hinzufügen eines SQL-Triggers](#add-sql-trigger) oder [Hinzufügen einer SQL-Aktion](#add-sql-action) fort.

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Verbindung mit SQL Server herstellen

Wenn Sie durch den SQL-Trigger oder die Aktion aufgefordert werden, Verbindungsinformationen anzugeben, befolgen Sie diese Schritte, die sowohl für Trigger als auch für Aktionen gelten. Für Szenarien, die die Installation des [lokalen Datengateways](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-install) auf einem lokalen Computer und [Erstellen der Azure-Datengatewayressource](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection) erfordern, stellen Sie sicher, dass Sie zuerst diese Anforderungen erfüllen. Andernfalls wird Ihre Gatewayressource bei der Erstellung der Verbindung nicht in der Gatewayliste angezeigt.

Auch wenn Sie Windows-Authentifizierung mit dem SQL Server-Connector in einer [Integrationsdienstumgebung (ISE)](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment-overview) verwenden möchten, verwenden Sie die Nicht-ISE-Version des Connectors und das lokale Datengateway. Die Version mit der Bezeichnung „ISE“ unterstützt keine Windows-Authentifizierung.

1. Erstellen Sie für **Verbindungsname** einen Namen für Ihre Verbindung.

1. Wählen Sie in den Trigger oder die Aktion und dann **Über lokales Datengateway verbinden** aus, damit die SQL Server-Optionen angezeigt werden.

1. Geben Sie für **SQL Servername** und **SQL-Datenbankname** die Adresse Ihres SQL Servers und den Namen Ihrer Datenbank an. Geben Sie für **Benutzername** und **Kennwort** den Benutzernamen und das Kennwort für Ihren Server an.

   Sie finden diese Informationen auch in Ihrer Verbindungszeichenfolge:

   * `Server=<your-server-address>`
   * `Database=<your-database-name>`
   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Herstellen der Verbindung mit SQL Server](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)

1. Wenn auf dem SQL Server Windows- oder Standardauthentifizierung verwendet wird, wählen Sie den **Authentifizierungstyp** aus.

1. Wählen Sie unter **Gateways**  das Azure-Abonnement aus, das Ihrem zuvor erstellten lokalen Datengateway zugeordnet ist, und wählen Sie den Namen für Ihr lokales Datengateway aus.

   Wenn Ihr Gateway nicht in der Liste angezeigt wird, überprüfen Sie, ob Sie das [Gateway richtig eingerichtet](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection) haben.

   ![Erstellen einer SQL Server-Verbindung abgeschlossen.](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. Wählen Sie **Erstellen**, wenn Sie fertig sind.

1. Nachdem Sie die Verbindung erstellt haben, fahren Sie mit [Hinzufügen eines SQL-Triggers](#add-sql-trigger) oder [Hinzufügen einer SQL-Aktion](#add-sql-action) fort.
