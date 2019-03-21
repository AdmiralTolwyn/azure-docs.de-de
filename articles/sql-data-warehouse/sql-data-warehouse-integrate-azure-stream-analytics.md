---
title: Verwenden von Azure Stream Analytics mit SQL Data Warehouse | Microsoft Docs
description: Tipps für die Verwendung von Azure Stream Analytics mit Azure SQL Data Warehouse für die Entwicklung von Lösungen.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 605598d1470cbb535d626c15a5e8e4e08aa4d571
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57883813"
---
# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>Verwenden von Azure Stream Analytics mit SQL Data Warehouse
Azure Stream Analytics ist ein vollständig verwalteter Dienst, der eine geringe Latenz, hohe Verfügbarkeit und eine skalierbare komplexe Ereignisverarbeitung durch das Streaming von Daten in der Cloud bietet. Die Grundlagen finden Sie unter [Einführung in Azure Stream Analytics][Introduction to Azure Stream Analytics]. In [Erste Schritte mit Azure Stream Analytics][Get started using Azure Stream Analytics] können Sie sich dann mit dem Erstellen einer End-to-End-Lösung mit Stream Analytics vertraut machen.

In diesem Artikel erfahren Sie, wie Sie die Azure SQL Data Warehouse-Datenbank als Ausgabesenke für Stream Analytics-Aufgaben verwenden.

## <a name="prerequisites"></a>Voraussetzungen
Führen Sie zuerst die folgenden Schritte im Lernprogramm [Erste Schritte mit Azure Stream Analytics][Get started using Azure Stream Analytics] aus.  

1. Erstellen einer Event Hub-Eingabe
2. Konfigurieren und starten der Ereignisgenerator-Anwendung
3. Bereitstellen eines Stream Analytics-Auftrags
4. Festlegen der Auftragseingabe und -abfrage

Erstellen Sie dann eine Azure SQL Data Warehouse-Datenbank.

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>Festlegen der Auftragsausgabe: Azure SQL Data Warehouse-Datenbank
### <a name="step-1"></a>Schritt 1
Klicken Sie in Ihrem Stream Analytics-Auftrag am oberen Rand der Seite auf **AUSGABE**, und klicken Sie anschließend auf **AUSGABE HINZUFÜGEN**.

### <a name="step-2"></a>Schritt 2
Wählen Sie „SQL-Datenbank“ aus, und klicken Sie auf „Weiter“.

![][add-output]

### <a name="step-3"></a>Schritt 3
Geben Sie auf der nächsten Seite die folgenden Werte ein:

* *Ausgabealias:* Geben Sie einen Anzeigenamen für diese Auftragsausgabe ein.
* *Abonnement*:
  * Befindet sich die SQL Data Warehouse-Datenbank im selben Abonnement wie der Stream Analytics-Auftrag, wählen Sie die Option "SQL-Datenbank aus aktuellem Abonnement verwenden" aus.
  * Wenn die Datenbank sich in einem anderen Abonnement befindet, wählen Sie "SQL-Datenbank aus einem anderen Abonnement verwenden" aus.
* *Datenbank*: Geben Sie den Namen einer Zieldatenbank an.
* *Server Name:* Geben Sie den Servernamen für die Datenbank an, die Sie soeben angegeben haben. Diese Angaben finden Sie im Azure-Portal.

![][server-name]

* *Benutzername*: Geben Sie den Benutzernamen eines Kontos mit Schreibberechtigungen für die Datenbank an.
* *Kennwort*: Geben Sie das Kennwort für das angegebene Benutzerkonto an.
* *Tabelle*: Geben Sie den Namen der Zieltabelle in der Datenbank an.

![][add-database]

### <a name="step-4"></a>Schritt 4
Klicken Sie auf das Häkchen, um diese Auftragsausgabe hinzuzufügen und um zu überprüfen, ob Stream Analytics erfolgreich mit der Datenbank verbunden werden kann.

![][test-connection]

Wenn die Verbindung mit der Datenbank hergestellt wird, wird unten im Portal eine entsprechende Meldung angezeigt. Sie können dann unten auf "Verbindung testen" klicken, um die Verbindung mit der Datenbank zu testen.

## <a name="next-steps"></a>Nächste Schritte
Einen Überblick über die Integration finden Sie unter [SQL Data Warehouse-Integration (Übersicht)][SQL Data Warehouse integration overview].

Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht für SQL Data Warehouse][SQL Data Warehouse development overview].

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Introduction to Azure Stream Analytics]: ../stream-analytics/stream-analytics-introduction.md
[Get started using Azure Stream Analytics]: ../stream-analytics/stream-analytics-real-time-fraud-detection.md
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: https://azure.microsoft.com/documentation/services/stream-analytics/
