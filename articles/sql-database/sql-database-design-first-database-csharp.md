---
title: "Entwerfen Ihrer ersten Azure SQL-Datenbank – C# | Microsoft-Dokumentation"
description: "Erfahren Sie, wie Sie Ihre erste Azure SQL-Datenbank entwerfen und für diese mithilfe von ADO.NET eine Verbindung mit einem C#-Programm herstellen."
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg-msft
editor: CarlRabeler
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: develop databases, mvc, devcenter
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 08/25/2017
ms.author: genemi
ms.openlocfilehash: 7d6c1ca0f48974f8a7d839b23f71d43bfeb400ba
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/31/2017
---
# <a name="design-an-azure-sql-database-and-connect-with-cx23-and-adonet"></a>Entwerfen einer Azure SQL-Datenbank und Herstellen einer Verbindung für diese mit C&#x23; und ADO.NET

Azure SQL-Datenbank ist eine relationale DBaaS-Lösung (Database as a Service) in Microsoft Cloud („Azure“). In diesem Tutorial erfahren Sie, wie Sie das Azure-Portal und ADO.NET mit Visual Studio für folgende Aufgaben verwenden: 

> [!div class="checklist"]
> * Erstellen einer Datenbank im Azure-Portal
> * Einrichten einer Firewallregel auf Serverebene im Azure-Portal
> * Herstellen einer Verbindung für die Datenbank mit ADO.NET und Visual Studio
> * Erstellen von Tabellen mit ADO.NET
> * Einfügen, Aktualisieren und Löschen von Daten mit ADO.NET 
> * Abfragen von Daten mit ADO.NET

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Eine Installation von [Visual Studio Community 2017, Visual Studio Professional 2017 oder Visual Studio Enterprise 2017](https://www.visualstudio.com/downloads/).

<!-- The following included .md, sql-database-tutorial-portal-create-firewall-connection-1.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-tutorial-portal-create-firewall-connection-1](../../includes/sql-database-tutorial-portal-create-firewall-connection-1.md)]


<!-- The following included .md, sql-database-csharp-adonet-create-query-2.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurden grundlegende Datenbankaufgaben behandelt, z.B. das Erstellen einer Datenbank und von Tabellen, das Laden und Abfragen von Daten und das Wiederherstellen der Datenbank zu einem früheren Zeitpunkt. Es wurde Folgendes vermittelt:
> [!div class="checklist"]
> * Erstellen einer Datenbank
> * Einrichten einer Firewallregel
> * Herstellen einer Verbindung mit der Datenbank mit [Visual Studio und C#](sql-database-connect-query-dotnet-visual-studio.md)
> * Erstellen von Tabellen.
> * Einfügen, Aktualisieren und Löschen von Daten
> * Abfragen von Daten

Im nächsten Tutorial erfahren Sie, wie Sie Ihre Daten migrieren.

> [!div class="nextstepaction"]
>[Migrieren einer SQL Server-Datenbank zu Azure SQL-Datenbank](sql-database-migrate-your-sql-server-database.md)

