---
title: 'Erstellen einer Firewallregel auf Serverebene: Azure SQL-Datenbank | Microsoft-Dokumentation'
description: Erstellen einer SQL-Datenbank-Firewallregel auf Serverebene für Einzel- und Pooldatenbanken
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 02/11/2019
ms.openlocfilehash: f708e5a3cd5bc0f11f8b0cfe79a791347c7a7a2b
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56108958"
---
# <a name="quickstart-create-a-server-level-firewall-rule-for-single-and-pooled-databases-using-the-azure-portal"></a>Schnellstart: Erstellen einer Firewallregel auf Serverebene für Einzel- und Pooldatenbanken über das Azure-Portal

In dieser Schnellstartanleitung erfahren Sie Schritt für Schritt, wie Sie über das Azure-Portal eine [Firewallregel auf Serverebene](sql-database-firewall-configure.md) für Einzel- und Pooldatenbanken in Azure SQL-Datenbank erstellen, um eine Verbindung mit Datenbankservern, Einzeldatenbanken und Pools für elastische Datenbanken (und den darin enthaltenen Datenbanken) herstellen zu können. Eine Firewallregel ist erforderlich, um eine Verbindung über andere Azure-Ressourcen und über lokale Ressourcen herstellen zu können.

## <a name="prerequisites"></a>Voraussetzungen

In dieser Schnellstartanleitung werden als Ausgangspunkt die Ressourcen verwendet, die unter [Quickstart: Create a single database in Azure SQL Database using the Azure portal](sql-database-single-database-get-started.md) (Schnellstart: Erstellen einer Einzeldatenbank in Azure SQL-Datenbank mit dem Azure-Portal) erstellt werden.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

## <a name="create-a-server-level-ip-firewall-rule"></a>Erstellen einer IP-Firewallregel auf Serverebene

Der SQL-Datenbank-Dienst erstellt eine Firewall auf Datenbankserverebene für Einzel- und Pooldatenbanken. Diese Firewall verhindert, dass Clientanwendungen eine Verbindung mit dem Server oder einer seiner Einzel- oder Pooldatenbanken herstellen, sofern Sie keine IP-Firewallregel erstellen, mit der die Firewall geöffnet wird. Für eine Verbindung über eine IP-Adresse, die sich außerhalb von Azure befindet, müssen Sie eine Firewallregel für eine bestimmte IP-Adresse oder für einen Bereich von Adressen erstellen, über die eine Verbindung möglich sein soll. Weitere Informationen zu IP-Firewallregeln auf Server- oder Datenbankebene finden Sie unter [Firewallregeln für Azure SQL-Datenbank and SQL Data Warehouse](sql-database-firewall-configure.md).

> [!NOTE]
> SQL-Datenbank kommuniziert über Port 1433. Wenn Sie versuchen, eine Verbindung über ein Unternehmensnetzwerk herzustellen, wird ausgehender Datenverkehr über Port 1433 von der Firewall Ihres Netzwerks unter Umständen nicht zugelassen. In diesem Fall können Sie nur dann eine Verbindung mit Ihrem Azure SQL-Datenbank-Server herstellen, wenn Ihre IT-Abteilung Port 1433 öffnet.
> [!IMPORTANT]
> Bei Verwendung der Firewallregel 0.0.0.0 können alle Azure-Dienste die Firewall auf Serverebene passieren und über den Server eine Verbindung mit einer Einzel- oder Pooldatenbank herstellen. Weitere Informationen zur Verwendung von VNET-Regeln finden Sie im Abschnitt [VNET-Regeln als Alternative zu IP-Regeln](sql-database-firewall-configure.md#virtual-network-rules-as-alternatives-to-ip-rules).

Führen Sie die hier angegebenen Schritte zum Erstellen einer IP-Firewallregel auf Serverebene für die IP-Adresse Ihres Clients und zum Zulassen externer Verbindungen durch die SQL-Datenbank-Firewall nur für Ihre IP-Adresse aus.

1. Nachdem die [Voraussetzungen für die Bereitstellung von Azure SQL-Datenbank](#prerequisites) erfüllt sind, können Sie im Menü auf der linken Seite **SQL-Datenbanken** und dann auf der Seite **SQL-Datenbanken** die Option **mySampleDatabase** wählen. Die Übersichtsseite für Ihre Datenbank wird geöffnet, die den vollqualifizierten Servernamen (z.B. **mynewserver20170824.database.windows.net**) und Optionen für die weitere Konfiguration enthält.

2. Kopieren Sie diesen vollqualifizierten Servernamen, damit Sie ihn beim Herstellen der Verbindung mit Ihrem Server und den zugehörigen Datenbanken in anderen Schnellstartanleitungen verwenden können.

   ![Servername](./media/sql-database-get-started-portal/server-name.png)

3. Wählen Sie in der Symbolleiste die Option **Serverfirewall festlegen**. Die Seite **Firewalleinstellungen** für den Datenbankserver wird geöffnet.

   ![IP-Firewallregel auf Serverebene](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Wählen Sie auf der Symbolleiste die Option **Client-IP-Adresse hinzufügen**, um Ihre aktuelle IP-Adresse einer neuen IP-Firewallregel auf Serverebene hinzuzufügen. Eine IP-Firewallregel auf Serverebene kann Port 1433 für eine einzelne IP-Adresse oder einen Bereich von IP-Adressen öffnen.

   > [!IMPORTANT]
   > Standardmäßig ist der Zugriff über die SQL-Datenbank-Firewall für alle Azure-Dienste aktiviert. Wählen Sie auf dieser Seite die Option **AUS**, um dies für alle Azure-Dienste zu deaktivieren.
   >

5. Wählen Sie **Speichern** aus. Für Ihre aktuelle IP-Adresse wird eine IP-Firewallregel auf Serverebene erstellt, und auf dem SQL-Datenbank-Server wird der Port 1433 geöffnet.

6. Schließen Sie die Seite **Firewalleinstellungen**.

Sie können für diese IP-Adresse jetzt eine Verbindung mit dem SQL-Datenbank-Server und den dazugehörigen Datenbanken herstellen. Verwenden Sie hierfür SQL Server Management Studio oder ein anderes Tool Ihrer Wahl sowie das zuvor erstellte Serveradministratorkonto.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Speichern Sie diese Ressourcen, um unter [Nächste Schritte](#next-steps) zu erfahren, wie Sie eine Verbindung herstellen und Ihre Datenbank mit verschiedenen Methoden abfragen. Sie können die Ressourcen, die Sie im Rahmen dieser Schnellstartanleitung erstellt haben, aber auch wieder löschen. Gehen Sie hierzu wie folgt vor:

1. Wählen Sie im Azure-Portal im Menü auf der linken Seite die Option **Ressourcengruppen** und dann **myResourceGroup**.
2. Wählen Sie auf der Seite mit Ihrer Ressourcengruppe die Option **Löschen**, geben Sie im Textfeld **myResourceGroup** ein, und wählen Sie anschließend **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

- Nachdem Sie nun über eine Datenbank verfügen, können Sie mit Ihren bevorzugten Tools oder Sprachen [eine Verbindung herstellen und Abfragen ausführen](sql-database-connect-query.md). Dazu zählen auch:
  - [Verbinden und Abfragen mit SQL Server Management Studio (SSMS)](sql-database-connect-query-ssms.md)
  - [Verbinden und Abfragen mit Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Informationen zum Entwerfen Ihrer ersten Datenbank, zum Erstellen von Tabellen und zum Einfügen von Daten finden Sie in diesen Tutorials:
  - [Tutorial: Entwurf Ihrer ersten Azure SQL-Datenbank mithilfe von SSMS](sql-database-design-first-database.md)
  - [Tutorial: Entwerfen einer Azure SQL-Datenbank und Herstellen einer Verbindung für diese mit C# und ADO.NET](sql-database-design-first-database-csharp.md)
