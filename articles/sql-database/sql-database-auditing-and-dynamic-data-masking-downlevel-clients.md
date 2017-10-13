---
title: "Tabellenüberwachung, TDS-Umleitung und IP-Endpunkte für Azure SQL-Datenbank | Microsoft-Dokumentation"
description: "Erfahren Sie etwas über die Überwachung, TDS-Umleitungen und IP-Endpunktänderungen beim Implementieren der Tabellenüberwachung in Azure SQL-Datenbank."
services: sql-database
documentationcenter: 
author: giladm
manager: jhubbard
editor: 
ms.assetid: 4ef19ed1-e798-43a2-ad99-0e563f93ab53
ms.service: sql-database
ms.custom: security
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: giladm
ms.openlocfilehash: d4a7e6658ec65a70bd7e07859e2a69acee58b7b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="sql-database----downlevel-clients-support-and-ip-endpoint-changes-for-table-auditing"></a>SQL-Datenbank – Unterstützung für kompatible Clients und IP-Endpunktänderungen für die Tabellenüberwachung

> [!IMPORTANT]
> Dieses Dokument gilt nur für die Tabellenüberwachung, die als **veraltet** gekennzeichnet ist.<br>
> Verwenden Sie die neue Methode zur [Blobüberwachung](sql-database-auditing.md), die **keine** tiefgreifenden Änderungen an den Clientverbindungszeichenfolgen erfordert. Weitere Informationen zur Blobüberwachung finden Sie unter [Erste Schritte bei der Überwachung von SQL-Datenbank](sql-database-auditing.md).

Die [Datenbanküberwachung](sql-database-auditing.md) erfolgt automatisch bei SQL-Clients, die eine TDS-Umleitung unterstützen. Beachten Sie, dass diese Umleitung bei Verwendung der Blobüberwachungsmethode nicht angewendet wird.

## <a id="subheading-1"></a>Unterstützung für kompatible Clients
Jeder Client, der TDS 7.4 implementiert, sollte auch die Umleitung unterstützen. Zu den Ausnahmen gehören JDBC 4.0, in dem die Umleitungsfunktion nicht vollständig unterstützt wird, und Tedious for Node.JSS, in dem die Umleitungsfunktion nicht implementiert wurde.

Für "Vorgängerversionsclients", d. h. Clients, die TDS 7.3 und ältere Versionen unterstützen, sollte der vollqualifizierte Domänenname des Servers in der Verbindungszeichenfolge geändert werden:

Ursprünglicher vollqualifizierter Domänenname des Servers in der Verbindungszeichenfolge: <*Servername*>.database.windows.net

Geänderter vollqualifizierter Domänenname des Servers in der Verbindungszeichenfolge: <*Servername*>.database.**secure**.windows.net

Eine unvollständige Liste der "Vorgängerversionsclients":

* .NET 4.0 und ältere Versionen
* ODBC 10.0 und ältere Versionen
* JDBC (JDBC unterstützt zwar TDS 7.4, aber die TDS-Umleitungsfunktion wird nicht vollständig unterstützt)
* Tedious (for Node.JS)

**Anmerkung:** Die oben genannte Änderung des vollqualifizierten Domänennamens des Servers kann sich auch als nützlich erweisen bei der Anwendung einer Richtlinie für die Überwachung auf SQL Server-Ebene, ohne dass ein Konfigurationsschritt in jeder Datenbank ausgeführt werden muss (temporäre Minderung).

## <a id="subheading-2"></a>IP-Endpunkt-Änderungen beim Aktivieren der Überwachung
Beachten Sie, dass sich beim Aktivieren der Tabellenüberwachung der IP-Endpunkt Ihrer Datenbank ändert. Wenn Sie strenge Firewall-Einstellungen haben, aktualisieren Sie diese Firewalleinstellungen entsprechend.

Der neue Datenbank-IP-Endpunkt ist abhängig von der Region der Datenbank:

| Datenbankregion | Mögliche IP-Endpunkte |
| --- | --- |
| China, Norden |139.217.29.176, 139.217.28.254 |
| China, Osten |42.159.245.65, 42.159.246.245 |
| Australien (Osten) |104.210.91.32, 40.126.244.159, 191.239.64.60, 40.126.255.94 |
| Australien (Südost) |191.239.184.223, 40.127.85.81, 191.239.161.83, 40.127.81.130 |
| Brasilien Süd |104.41.44.161, 104.41.62.230, 23.97.99.54, 104.41.59.191 |
| USA (Mitte) |104.43.255.70, 40.83.14.7, 23.99.128.244, 40.83.15.176 |
| USA, Mitte (EUAP) |52.180.178.16, 52.180.176.190 |
| Ostasien |23.99.125.133, 13.75.40.42, 23.97.71.138, 13.94.43.245 |
| USA (Ost) 2 |104.209.141.31, 104.208.238.177, 191.237.131.51, 104.208.235.50 |
| USA (Ost) |23.96.107.223, 104.41.150.122, 23.96.38.170, 104.41.146.44 |
| USA, Osten (EUAP) |52.225.190.86, 52.225.191.187 |
| Indien (Mitte) |104.211.98.219, 104.211.103.71 |
| Indien (Süden) |104.211.227.102, 104.211.225.157 |
| Indien, Westen |104.211.161.152, 104.211.162.21 |
| Japan Ost |104.41.179.1, 40.115.253.81, 23.102.64.207, 40.115.250.196 |
| Japan (Westen) |104.214.140.140, 104.214.146.31, 191.233.32.34, 104.214.146.198 |
| USA (Mitte/Norden) |191.236.155.178, 23.96.192.130, 23.96.177.169, 23.96.193.231 |
| Nordeuropa |104.41.209.221, 40.85.139.245, 137.116.251.66, 40.85.142.176 |
| USA (Mitte/Süden) |191.238.184.128, 40.84.190.84, 23.102.160.153, 40.84.186.66 |
| Südostasien |104.215.198.156, 13.76.252.200, 23.97.51.109, 13.76.252.113 |
| Westeuropa |104.40.230.120, 13.80.23.64, 137.117.171.161, 13.80.8.37, 104.47.167.215, 40.118.56.193, 104.40.176.73, 40.118.56.20 |
| USA (West) |191.236.123.146, 138.91.163.240, 168.62.194.148, 23.99.6.91 |
| USA, Westen 2 |13.66.224.156, 13.66.227.8 |
| USA, Westen-Mitte |52.161.29.186, 52.161.27.213 |
| Kanada, Mitte |13.88.248.106, 13.88.248.110 |
| Kanada, Osten |40.86.227.82, 40.86.225.194 |
| Großbritannien, Norden |13.87.101.18, 13.87.100.232 |
| Großbritannien, Süden 2 |13.87.32.202, 13.87.32.226 |
