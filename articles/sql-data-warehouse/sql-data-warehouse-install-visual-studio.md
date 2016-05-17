<properties
   pageTitle="Installieren von Visual Studio und/oder SSDT für SQL Data Warehouse | Microsoft Azure"
   description="Installieren von Visual Studio und/oder SSDT für Azure SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/20/2016"
   ms.author="sonyama;barbkess"/>

# Installieren von Visual Studio 2015 und/oder SSDT für SQL Data Warehouse

Für die Entwicklung von Anwendungen für SQL Data Warehouse empfiehlt es sich, Visual Studio 2015 in Verbindung mit der neuesten Version von SQL Server Data Tools (SSDT) zu verwenden. Visual Studio 2013 mit SSDT wird ebenfalls unterstützt.

Zum Ausführen von Abfragen von der integrierten Visual Studio-Entwicklungsumgebung (IDE) aus müssen Sie nur SSDT installieren. Zusammen mit SSDT wird die Visual Studio-IDE installiert, sodass Sie den SQL Server-Objekt-Explorer für die Verbindung mit Ihrer Azure SQL Server-Instanz verwenden können. Daraufhin können Sie Ihre SQL Data Warehouse-Datenbanken anzeigen und Abfragen ausführen.


## Schritt 1: Herunterladen und Installieren von Visual Studio

Wenn Sie sich für die Installation von Visual Studio entscheiden, können Sie entweder Visual Studio 2013 oder Visual Studio 2015 mit SQL Data Warehouse verwenden. Wenn Sie bereits Visual Studio 2013 oder 2015 installiert haben, fahren Sie mit Schritt 2 fort, um SSDT zu installieren.

So installieren Sie Visual Studio 2015

1. [Laden Sie Visual Studio 2015][] aus Visual Studio Team Services herunter.
2. Befolgen Sie die Anleitung [Installieren von Visual Studio][] in MSDN, und wählen Sie die Standardkonfigurationen aus.

## Schritt 2: Herunterladen und Installieren der neuesten SQL Server Data Tools (SSDT)

Unabhängig davon, ob Sie Visual Studio installiert haben, benötigen Sie die neueste Version von SQL Server Data Tools (SSDT), die SQL Data Warehouse unterstützt.

So installieren Sie die neueste Version von SSDT

1. [Laden Sie die SQL Server Data Tools-Vorschau für Visual Studio 2013 oder 2015 herunter.][]
2. Installieren Sie sie gemäß den Installationsanleitungen auf der Downloadwebsite.

## Nächste Schritte

Da Sie jetzt die neueste Version von SSDT verwenden, sind Sie bereit, eine [Verbindung][] mit der Datenbank herzustellen.

<!--Anchors-->

<!--Image references-->

<!--Arcticles-->
[Verbindung]: ./sql-data-warehouse-get-started-connect.md

<!--Other-->
[Laden Sie Visual Studio 2015]: https://www.visualstudio.com/downloads/
[Installieren von Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[Laden Sie die SQL Server Data Tools-Vorschau für Visual Studio 2013 oder 2015 herunter.]: https://msdn.microsoft.com/library/mt204009.aspx

<!---HONumber=AcomDC_0511_2016-->