---
title: "Datenbank-Migrationstool für Azure Cosmos DB | Microsoft-Dokumentation"
description: "Erfahren Sie, wie Sie das Open-Source-basierte Azure Cosmos DB-Datenmigrationstool verwenden, um Daten aus verschiedenen Quellen, z. B. MongoDB, SQL Server, Azure Table Storage, Amazon DynamoDB, CSV- und JSON-Dateien, in Azure Cosmos DB zu importieren. CSV-zu-JSON-Konvertierung."
keywords: CSV zu JSON, Datenbank-Migrationstools, Konvertieren von CSV zu JSON
services: cosmos-db
author: andrewhoh
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: d173581d-782a-445c-98d9-5e3c49b00e25
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: anhoh
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5bbeb9d4516c2b1be4f5e076a7f63c35e4176b36
ms.openlocfilehash: 23a4a82dbdb611f4da90562af936fca28da9b24d
ms.contentlocale: de-de
ms.lasthandoff: 06/13/2017

---
# <a name="how-to-import-data-into-azure-cosmos-db-for-the-documentdb-api"></a>Importieren von Daten in Azure Cosmos DB für die DocumentDB-API

Dieses Tutorial enthält Anweisungen zur Verwendung des DocumentDB-API-Datenmigrationstools von Azure Cosmos DB, mit dem Daten aus verschiedenen Quellen, z.B. JSON-Dateien, CSV-Dateien, SQL, MongoDB, Azure-Tabellenspeicher, Amazon DynamoDB und Azure Cosmos DB DocumentDB-API-Sammlungen zur Verwendung mit Azure Cosmos DB und der DocumentDB-API importiert werden können. Das Datenmigrationstool kann auch zur Migration von einer Sammlung mit einer einzelnen Partition zu einer Sammlung mit mehreren Partitionen für die DocumentDB-API verwendet werden.

Das Datenmigrationstool funktioniert nur beim Importieren von Daten in Azure Cosmos DB für die Verwendung mit der DocumentDB-API. Das Importieren von Daten für die Verwendung mit der Tabellen- oder Graph-API wird zu diesem Zeitpunkt nicht unterstützt. 

Weitere Informationen zum Importieren von Daten für die Verwendung mit der MongoDB-API finden Sie unter [Azure Cosmos-DB: Migrieren von Daten für die MongoDB-API](mongodb-migrate.md).

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> * Installieren des Datenmigrationstools
> * Importieren von Daten aus verschiedenen Datenquellen
> * Exportieren aus Azure Cosmos DB zu JSON

## <a id="Prerequisites"></a>Voraussetzungen
Bevor Sie diesen Artikel durcharbeiten, sollten Sie sicherstellen, dass Folgendes installiert ist:

* [Microsoft .NET Framework 4.51](https://www.microsoft.com/download/developer-tools.aspx) oder höher.

## <a id="Overviewl"></a>Übersicht über das Datenmigrationstool
Das Datenmigrationstool ist eine Open-Source-Lösung, mit der Daten aus verschiedensten Quellen in Azure Cosmos DB importiert werden können:

* JSON-Dateien
* MongoDB
* SQL Server
* CSV-Dateien
* Azure-Tabellenspeicher
* Amazon DynamoDB
* HBase
* Azure Cosmos DB-Sammlungen

Das Importtool enthält zwar eine grafische Benutzeroberfläche (dtui.exe), kann aber auch über die Befehlszeile (dt.exe) gesteuert werden. In der Tat gibt es eine Option, mit der nach dem Einrichten eines Imports über die Benutzeroberfläche zugeordnete Befehl ausgegeben werden kann. Quelldateien in Tabellenformat (z. B. SQL Server- oder CSV-Dateien) können so umgewandelt werden, dass während des Imports hierarchische Beziehungen (Filialdokumente) erstellt werden können. Hier erhalten Sie weitere Informationen zu Quelloptionen, Beispielbefehlszeilen zum Importieren aus jedem Quellformat, Zieloptionen und zum Anzeigen der Importergebnisse.

## <a id="Install"></a>Installieren des Datenmigrationstools
Der Quellcode des Migrationstools ist in GitHub in [diesem Repository](https://github.com/azure/azure-documentdb-datamigrationtool) und eine kompilierte Version ist im [Microsoft Download Center](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) verfügbar. Sie können die Projektmappe kompilieren oder einfach die kompilierte Version in ein Verzeichnis Ihrer Wahl herunterladen und extrahieren. Führen Sie anschließend eine dieser Dateien aus:

* **Dtui.exe**: GUI-Version des Tools
* **Dt.exe**: Befehlszeilenversion des Tools

## <a name="import-data"></a>Daten importieren

Nachdem Sie das Tool installiert haben, können Ihre Daten importiert werden. Welche Art von Daten möchten Sie importieren?

* [JSON-Dateien](#JSON)
* [MongoDB](#MongoDB)
* [MongoDB-Exportdateien](#MongoDBExport)
* [SQL Server](#SQL)
* [CSV-Dateien](#CSV)
* [Azure Table Storage](#AzureTableSource)
* [Amazon DynamoDB](#DynamoDBSource)
* [Blob](#BlobImport)
* [Azure Cosmos DB-Sammlungen](#DocumentDBSource)
* [HBase](#HBaseSource)
* [Azure Cosmos DB-Massenimport](#DocumentDBBulkImport)
* [Sequenzieller Datensatzimport mit Azure DB Cosmos](#DocumentDSeqTarget)


## <a id="JSON"></a>So importieren Sie JSON-Dateien
Mit der Importprogrammoption für JSON-Dateiquellen können Sie ein oder mehrere JSON-Dateien mit einem einzelnen Dokument oder JSON-Dateien mit jeweils einem Array aus JSON-Dokumenten importieren. Wenn Sie Ordner hinzufügen, die zu importierende JSON-Dateien enthalten, können Sie in den Unterordnern rekursiv nach Dateien suchen.

![Screenshot der Optionen für JSON-Dateiquellen – Datenbank-Migrationstools](./media/import-data/jsonsource.png)

Hier finden Sie einige Beispiele für Befehlszeilen zum Importieren von JSON-Dateien:

    #Import a single JSON file
    dt.exe /s:JsonFile /s.Files:.\Sessions.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

    #Import a directory of JSON files
    dt.exe /s:JsonFile /s.Files:C:\TESessions\*.json /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

    #Import a directory (including sub-directories) of JSON files
    dt.exe /s:JsonFile /s.Files:C:\LastFMMusic\**\*.json /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Music /t.CollectionThroughput:2500

    #Import a directory (single), directory (recursive), and individual JSON files
    dt.exe /s:JsonFile /s.Files:C:\Tweets\*.*;C:\LargeDocs\**\*.*;C:\TESessions\Session48172.json;C:\TESessions\Session48173.json;C:\TESessions\Session48174.json;C:\TESessions\Session48175.json;C:\TESessions\Session48177.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:subs /t.CollectionThroughput:2500

    #Import a single JSON file and partition the data across 4 collections
    dt.exe /s:JsonFile /s.Files:D:\\CompanyData\\Companies.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:comp[1-4] /t.PartitionKey:name /t.CollectionThroughput:2500

## <a id="MongoDB"></a>So importieren Sie von MongoDB

> [!IMPORTANT]
> Wenn Sie in ein Azure Cosmos DB-Konto mit Unterstützung für MongoDB importieren, befolgen Sie diese [Anweisungen](mongodb-migrate.md).
> 
> 

Mit der Importprogrammoption für MongoDB-Dateiquellen können Sie Dateien aus einer einzelnen MongoDB-Sammlung importieren und optional Dokumente mithilfe einer Abfrage filtern und/oder die Dokumentstruktur mithilfe einer Projektion ändern.  

![Screenshot der Optionen für MongoDB-Quellen](./media/import-data/mongodbsource.png)

Die Verbindungszeichenfolge weist das MongoDB-Standardformat auf:

    mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>

> [!NOTE]
> Verwenden Sie den Befehl "Überprüfen", um sicherzustellen, dass auf die im Feld "Verbindungszeichenfolge" angegebene MongoDB-Instanz zugegriffen werden kann.
> 
> 

Geben Sie den Namen der Sammlung ein, aus der Daten importiert werden sollen. Optional können Sie eine Abfrage (z. B. {pop: {$gt:5000}}) und/oder eine Projektion (z. B. {loc:0}) angeben oder eine Datei für eine Abfrage bzw. Projektion bereitstellen, um die zu importierenden Daten zu filtern und zu formen.

Hier finden Sie einige Beispiele für Befehlszeilen zum Importieren aus MongoDB:

    #Import all documents from a MongoDB collection
    dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionThroughput:2500

    #Import documents from a MongoDB collection which match the query and exclude the loc field
    dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionThroughput:2500

## <a id="MongoDBExport"></a>So importieren Sie MongoDB-Exportdateien

> [!IMPORTANT]
> Wenn Sie in ein Azure Cosmos DB-Konto mit Unterstützung für MongoDB importieren, befolgen Sie diese [Anweisungen](mongodb-migrate.md).
> 
> 

Mit der Importprogrammoption für JSON-Dateiquellen und MongoDB-Exportdateien können Sie eine oder mehrere JSON-Dateien importieren, die mit dem mongoexport-Dienstprogramm erzeugt wurden.  

![Screenshot der Optionen für MongoDB-Exportquellen](./media/import-data/mongodbexportsource.png)

Wenn Sie Ordner hinzufügen, die zu importierende JSON-Dateien aus einem MongoDB-Export enthalten, können Sie in den Unterordnern rekursiv nach Dateien suchen:

Hier finden Sie ein Beispiel für eine Befehlszeile zum Importieren von JSON-Dateien aus einem MongoDB-Export:

    dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionThroughput:2500

## <a id="SQL"></a>So importieren Sie von SQL Server
Mit der Importprogrammoption für SQL-Quellen können Sie Datensätze aus einer einzelnen SQL Server-Datenbank importieren und mithilfe einer Abfrage filtern. Darüber hinaus können Sie die Dokumentstruktur ändern, indem Sie ein Schachtelungstrennzeichen angeben (mehr dazu weiter unten).  

![Screenshot der Optionen für SQL-Dateiquellen – Datenbank-Migrationstools](./media/import-data/sqlexportsource.png)

Die Verbindungszeichenfolge weist das standardmäßige Format für SQL-Verbindungszeichenfolgen auf.

> [!NOTE]
> Verwenden Sie den Befehl "Überprüfen", um sicherzustellen, dass auf die im Feld "Verbindungszeichenfolge" angegebene SQL Server-Instanz zugegriffen werden kann.
> 
> 

Die Schachtelungstrennzeicheneigenschaft wird zum Erstellen hierarchischer Beziehungen (Filialdokumente) während des Imports verwendet. Betrachten Sie die folgende SQL-Abfrage:

*select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'*

Diese Abfrage gibt die folgenden (partiellen) Ergebnisse zurück:

![Screenshot der SQL-Abfrageergebnisse](./media/import-data/sqlqueryresults.png)

Beachten Sie die Aliase, wie z. B. Address.AddressType und Address.Location.StateProvinceName. Durch Angabe des Schachtelungstrennzeichens "." erstellt das Importtool während des Imports die Filialdokumente "Address" und "Address.Location". Hier finden Sie ein Beispiel für ein Zieldokument in Azure Cosmos DB:

*{ "id": "956", "Name": "Finer Sales and Service", "Address": { "AddressType": "Main Office", "AddressLine1": "#500-75 O'Connor Street", "Location": { "City": "Ottawa", "StateProvinceName": "Ontario" }, "PostalCode": "K4B 1S2", "CountryRegionName": "Canada" } }*

Hier finden Sie einige Beispiele für Befehlszeilen zum Importieren aus SQL Server:

    #Import records from SQL which match a query
    dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionThroughput:2500

    #Import records from sql which match a query and create hierarchical relationships
    dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionThroughput:2500

## <a id="CSV"></a>So importieren Sie CSV-Dateien und konvertieren von CSV zu JSON
Mit der Importprogrammoption für CSV-Dateiquellen können Sie eine oder mehrere CSV-Dateien importieren. Wenn Sie Ordner hinzufügen, die zu importierende CSV-Dateien enthalten, können Sie in den Unterordnern rekursiv nach Dateien suchen.

![Screenshot der Optionen für CSV-Quellen – CSV in JSON](media/import-data/csvsource.png)

Ähnlich wie bei SQL-Quelldateien wird die Schachtelungstrennzeicheneigenschaft zum Erstellen hierarchischer Beziehungen (Filialdokumente) während des Imports verwendet. Betrachten Sie folgende CSV-Kopfzeile und Datenzeilen:

![Screenshot der CSV-Beispieldatensätze – CSV in JSON](./media/import-data/csvsample.png)

Beachten Sie die Aliase wie z. B."DomainInfo.Domain_Name" und "RedirectInfo.Redirecting". Durch Angabe des Schachtelungstrennzeichens "." erstellt das Importtool während des Imports die Filialdokumente "DomainInfo" und "RedirectInfo". Hier finden Sie ein Beispiel für ein Zieldokument in Azure Cosmos DB:

*{ "DomainInfo": { "Domain_Name": "ACUS.GOV", "Domain_Name_Address": "http://www.ACUS.GOV" }, "Federal Agency": "Administrative Conference of the United States", "RedirectInfo": { "Redirecting": "0", "Redirect_Destination": "" }, "id": "9cc565c5-ebcd-1c03-ebd3-cc3e2ecd814d" }*

Das Importtool versucht, Typinformationen für Werte ohne Anführungszeichen in CSV-Dateien abzuleiten (Werte in Anführungszeichen werden immer als Zeichenfolgen behandelt).  Typen werden in der folgenden Reihenfolge identifiziert: number, datetime, boolean.  

In Bezug auf CSV-Importe gibt es zwei weitere Dinge zu beachten:

1. Standardmäßig werden Tabstopps und Anführungszeichen aus Werten ohne Anführungszeichen immer gekürzt, während Werte in Anführungszeichen unverändert beibehalten werden. Dieses Verhalten kann mit dem Kontrollkästchen "Trim quoted values" oder der Befehlszeilenoption "/s.TrimQuoted" überschrieben werden.
2. Standardmäßig wird eine Null ohne Anführungszeichen als Nullwert behandelt. Dieses Verhalten kann mit dem Kontrollkästchen "Treat unquoted NULL as string" oder der Befehlszeilenoption "/s.NoUnquotedNulls" überschrieben werden (d. h., eine Null ohne Anführungszeichen wird als NULL-Zeichenfolge behandelt).

Hier finden Sie ein Beispiel für eine Befehlszeile zum Importieren von CSV-Dateien:

    dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionThroughput:2500

## <a id="AzureTableSource"></a>So importieren Sie aus Azure-Tabellenspeicher
Mit der Importprogrammoption für Azure-Tabellenspeicherquellen können Sie Daten aus einer einzelnen Azure-Tabellenspeichertabelle importieren und optional die zu importierenden Tabellenentitäten filtern. Beachten Sie, dass Sie mit dem Datenmigrationstool keine Azure-Tabellenspeicherdaten für die Verwendung mit der Tabellen-API in Azure Cosmos DB importieren können. Zu diesem Zeitpunkt wird nur das Importieren in Azure Cosmos DB für die Verwendung mit der DocumentDB-API unterstützt.

![Screenshot der Optionen für Azure-Tabellenspeicherquellen](./media/import-data/azuretablesource.png)

Die Verbindungszeichenfolge für den Azure-Tabellenspeicher weist folgendes Format auf:

    DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;

> [!NOTE]
> Verwenden Sie den Befehl "Überprüfen", um sicherzustellen, dass auf die im Feld "Verbindungszeichenfolge" angegebene Azure-Tabellenspeicherinstanz zugegriffen werden kann.
> 
> 

Geben Sie den Namen des Azure-Tabellenspeichers ein, aus dem Daten importiert werden sollen. Optional können Sie einen [Filter](https://msdn.microsoft.com/library/azure/ff683669.aspx)angeben.

Die Importprogrammoption für Azure-Tabellenspeicherquellen weist die folgenden zusätzlichen Optionen auf:

1. Interne Felder einschließen
   1. Alle - alle internen Felder einschließen (PartitionKey, RowKey und Timestamp)
   2. Keine - alle externen Felder ausschließen
   3. RowKey - nur das RowKey-Feld einschließen
2. Spalten auswählen
   1. Azure-Tabellenspeicherfilter unterstützen keine Projektionen. Wenn Sie nur bestimmte Eigenschaften der Azure-Tabellenentität importieren möchten, fügen Sie sie zur Liste "Spalten auswählen" hinzu. Alle anderen Entitätseigenschaften werden ignoriert.

Hier finden Sie ein Beispiel für eine Befehlszeile zum Importieren aus einem Azure-Tabellenspeicher:

    dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:metrics /t.CollectionThroughput:2500

## <a id="DynamoDBSource"></a>So importieren Sie von Amazon DynamoDB
Mit der Importprogrammoption für Amazon DynamoDB-Quellen können Sie aus einer einzelnen Amazon DynamoDB-Tabelle importieren und auf Wunsch die zu importierenden Entitäten filtern. Mehrere Vorlagen werden für einen einfachen Import bereitgestellt.

![Screenshot der Optionen für Amazon DynamoDB-Dateiquellen – Datenbank-Migrationstools](./media/import-data/dynamodbsource1.png)

![Screenshot der Optionen für Amazon DynamoDB-Dateiquellen – Datenbank-Migrationstools](./media/import-data/dynamodbsource2.png)

Das Format der Amazon-DynamoDB-Verbindungszeichenfolge ist:

    ServiceURL=<Service Address>;AccessKey=<Access Key>;SecretKey=<Secret Key>;

> [!NOTE]
> Verwenden Sie den Befehl „Überprüfen“, um sicherzustellen, dass auf die im Feld „Verbindungszeichenfolge“ angegebene Amazon DynamoDB-Instanz zugegriffen werden kann.
> 
> 

Hier finden Sie ein Beispiel für die Befehlszeile zum Importieren von Amazon DynamoDB:

    dt.exe /s:DynamoDB /s.ConnectionString:ServiceURL=https://dynamodb.us-east-1.amazonaws.com;AccessKey=<accessKey>;SecretKey=<secretKey> /s.Request:"{   """TableName""": """ProductCatalog""" }" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<Azure Cosmos DB Endpoint>;AccountKey=<Azure Cosmos DB Key>;Database=<Azure Cosmos DB Database>;" /t.Collection:catalogCollection /t.CollectionThroughput:2500

## <a id="BlobImport"></a>So importieren Sie Dateien von Azure Blob Storage
Mit den Importprogrammoptionen für JSON-Dateien, MongoDB-Exportdateien und die CSV-Dateien können Sie eine oder mehrere Dateien aus dem Azure-Blob-Speicher importieren. Nach dem Angeben von Blob-Container-URL und Kontoschlüssel geben Sie einfach einen regulären Ausdruck ein, um die Datei(en) zum Importieren auszuwählen.

![Screenshot der Optionen für Blob-Dateiquellen](./media/import-data/blobsource.png)

Hier finden Sie ein Beispiel für eine Befehlszeile zum Importieren von JSON-Dateien aus dem Azure-Blob-Speicher:

    dt.exe /s:JsonFile /s.Files:"blobs://<account key>@account.blob.core.windows.net:443/importcontainer/.*" /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:doctest

## <a id="DocumentDBSource"></a>So importieren Sie aus einer DocumentDB-API-Auflistung für Azure Cosmos DB
Mit der Importprogrammoption für Azure Cosmos DB-Quellen können Sie Daten aus einer oder mehreren Azure Cosmos DB-Sammlungen importieren und optional Dokumente mithilfe einer Abfrage filtern.  

![Screenshot der Optionen für Azure Cosmos DB-Quellen](./media/import-data/documentdbsource.png)

Die Azure Cosmos DB-Verbindungszeichenfolge weist folgendes Format auf:

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

Die Verbindungszeichenfolge für das Azure Cosmos DB-Konto kann im Azure-Portal im Blatt „Schlüssel“ abgerufen werden. Eine Beschreibung hierzu finden Sie unter [Verwalten eines Azure Cosmos DB-Kontos](manage-account.md). Der Name der Datenbank muss jedoch im folgenden Format an das Ende der Verbindungszeichenfolge angehängt werden:

    Database=<CosmosDB Database>;

> [!NOTE]
> Verwenden Sie den Befehl „Überprüfen“, um sicherzustellen, dass auf die im Feld „Verbindungszeichenfolge“ angegebene Azure Cosmos DB-Instanz zugegriffen werden kann.
> 
> 

Zum Importieren von Daten aus einer einzelnen Azure Cosmos DB-Sammlung geben Sie den Namen der Sammlung ein, aus der Daten importiert werden sollen. Zum Importieren von Daten aus mehreren Azure Cosmos DB-Sammlungen geben Sie einen regulären Ausdruck ein, der mit den Namen von einer oder mehreren Sammlungen übereinstimmt (z. B. Sammlung01 | Sammlung02 | Sammlung03). Optional können Sie eine Abfrage angeben oder eine Datei für eine Abfrage bereitstellen, um die zu importierenden Daten zu filtern und zu formen.

> [!NOTE]
> Wenn Sie Daten aus einer einzelnen Sammlung importieren, deren Name in regulären Ausdrücken verwendete Zeichen enthält, müssen diese Zeichen durch Escapezeichen geschützt werden, da für das Feld "Collection" reguläre Ausdrücke zulässig sind.
> 
> 

Die Importprogrammoption für Azure Cosmos DB-Quellen weist die folgenden erweiterten Optionen auf:

1. Interne Felder einschließen: Gibt an, ob Azure Cosmos DB-Dokumentsystemeigenschaften (z. B. _rid, _ts) in den Export eingeschlossen werden oder nicht.
2. Anzahl der Wiederholungsversuche bei Fehler: Gibt an, wie häufig bei einem vorübergehenden Fehler (z. B. Unterbrechung der Netzwerkverbindung) versucht werden soll, eine Verbindung zu Azure Cosmos DB herzustellen.
3. Intervall der Wiederholungsversuche: Gibt an, wie lang bei einem vorübergehenden Fehler (z. B. Unterbrechung der Netzwerkverbindung) gewartet werden soll, bevor erneut versucht wird, eine Verbindung zu Azure Cosmos DB herzustellen.
4. Verbindungsmodus: Gibt den Verbindungsmodus an, der mit Azure Cosmos DB verwendet werden soll. Verfügbare Optionen sind DirectTcp, DirectHttps und Gateway. Die Direktverbindungsmodi sind schneller, der Gatewaymodus ist besser für Firewalls geeignet, da nur Port 443 verwendet wird.

![Screenshot der erweiterten Optionen für Azure Cosmos DB-Quellen](./media/import-data/documentdbsourceoptions.png)

> [!TIP]
> Standardmäßig verwendet das Importtool den Verbindungsmodus DirectTcp. Wenn Firewallprobleme auftreten, wechseln Sie zum Gatewaymodus, da dieser nur Port 443 erfordert.
> 
> 

Hier finden Sie einige Beispiele für Befehlszeilen zum Importieren aus Azure Cosmos DB:

    #Migrate data from one Azure Cosmos DB collection to another Azure Cosmos DB collections
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:TEColl /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:TESessions /t.CollectionThroughput:2500

    #Migrate data from multiple Azure Cosmos DB collections to a single Azure Cosmos DB collection
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:singleCollection /t.CollectionThroughput:2500

    #Export an Azure Cosmos DB collection to a JSON file
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite /t.CollectionThroughput:2500

> [!TIP]
> Das Azure Cosmos DB-Tool zum Importieren von Daten unterstützt auch das Importieren von Daten aus dem [Azure Cosmos DB Emulator](local-emulator.md). Legen Sie beim Importieren von Daten aus einem lokalen Emulator den Endpunkt auf `https://localhost:<port>` fest. 
> 
> 

## <a id="HBaseSource"></a>So importieren Sie von HBase
Die Importprogrammoption für HBase-Quellen können Sie zum Importieren von Daten aus einer HBase-Tabelle und optional zum Filtern der Daten verwenden. Mehrere Vorlagen werden für einen einfachen Import bereitgestellt.

![Screenshot der Optionen für HBase-Quellen](./media/import-data/hbasesource1.png)

![Screenshot der Optionen für HBase-Quellen](./media/import-data/hbasesource2.png)

Das Format der Verbindungszeichenfolge „HBase Stargate“ lautet:

    ServiceURL=<server-address>;Username=<username>;Password=<password>

> [!NOTE]
> Verwenden Sie den Befehl „Überprüfen“, um sicherzustellen, dass auf die im Feld „Verbindungszeichenfolge“ angegebene HBase-Instanz zugegriffen werden kann.
> 
> 

Hier finden Sie ein Beispiel für die Befehlszeile zum Importieren von HBase:

    dt.exe /s:HBase /s.ConnectionString:ServiceURL=<server-address>;Username=<username>;Password=<password> /s.Table:Contacts /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:hbaseimport

## <a id="DocumentDBBulkTarget"></a>So importieren Sie in die DocumentDB-API (Massenimport)
Mit dem Azure Cosmos DB-Massenimportprogramm können Sie Daten aus allen verfügbaren Quelloptionen importieren und dabei zur Erhöhung der Effizienz eine gespeicherte Azure Cosmos DB-Prozedur verwenden. Das Tool unterstützt den Import in eine Azure Cosmos DB-Sammlung mit einzelner Partition sowie den Shardimport, bei dem die Daten über mehrere Azure Cosmos DB-Sammlungen mit einzelner Partition hinweg partitioniert werden. Weitere Informationen zur Partitionierung von Daten finden Sie unter [Partitionieren und Skalieren von Daten in Azure Cosmos DB](partition-data.md). Mit dem Tool wird die gespeicherte Prozedur erstellt, ausgeführt und dann aus der Zielsammlung bzw. den Zielsammlungen gelöscht.  

![Screenshot der Optionen für Azure Cosmos DB-Massenvorgänge](./media/import-data/documentdbbulk.png)

Die Azure Cosmos DB-Verbindungszeichenfolge weist folgendes Format auf:

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

Die Verbindungszeichenfolge für das Azure Cosmos DB-Konto kann im Azure-Portal im Blatt „Schlüssel“ abgerufen werden. Eine Beschreibung hierzu finden Sie unter [Verwalten eines Azure Cosmos DB-Kontos](manage-account.md). Der Name der Datenbank muss jedoch im folgenden Format an das Ende der Verbindungszeichenfolge angehängt werden:

    Database=<CosmosDB Database>;

> [!NOTE]
> Verwenden Sie den Befehl „Überprüfen“, um sicherzustellen, dass auf die im Feld „Verbindungszeichenfolge“ angegebene Azure Cosmos DB-Instanz zugegriffen werden kann.
> 
> 

Zum Importieren von Daten in eine einzelne Sammlung geben Sie den Namen der Sammlung ein, in die Daten importiert werden sollen, und klicken dann auf die Schaltfläche "Hinzufügen". Zum Importieren von Daten in mehrere Sammlungen geben Sie entweder jeden Sammlungsnamen einzeln ein, oder geben Sie mit der folgenden Syntax mehrere Sammlungen an: *Sammlungspräfix*[IndexStart - IndexEnde]. Wenn Sie mehrere Sammlungen über die oben genannte Syntax angeben, berücksichtigen Sie Folgendes:

1. Nur Namensmuster im ganzzahligen Bereich werden unterstützt. Durch Eingabe von "Sammlung[0-3]" können Sie z. B. folgende Sammlungen angeben: Sammlung0, Sammlung1, Sammlung2, Sammlung3.
2. Sie können eine abgekürzte Syntax verwenden: Durch Eingabe von "Sammlung[3]" wird derselbe Satz von Sammlungen ausgegeben wie in Schritt 1.
3. Mehrere Ersetzungen können bereitgestellt werden. "Sammlung[0-1] [0-9]" generiert z. B. 20 Sammlungsnamen mit führenden Nullen (Sammlung01, ...02, ...03).

Nachdem Sie den bzw. die Sammlungsnamen angegeben haben, wählen Sie den gewünschten Durchsatz für die Sammlung(en) aus (400 RUs bis 10.000 RUs). Wählen Sie einen höheren Durchsatz, um die beste Importleistung zu erreichen. Weitere Informationen zu Leistungsebenen finden Sie unter [Leistungsebenen in Azure Cosmos DB](performance-levels.md).

> [!NOTE]
> Die Einstellung für den Leistungsdurchsatz gilt nur für die Sammlungserstellung. Wenn die angegebene Sammlung bereits vorhanden ist, wird der zugehörige Durchsatz nicht geändert.
> 
> 

Beim Importieren von Daten in mehrere Sammlungen unterstützt das Importtool Sharding auf Hashbasis. Geben Sie in diesem Szenario die Dokumenteigenschaft an, die als Partitionsschlüssel verwendet werden soll (wenn der Partitionsschlüssel leer ist, werden die Dokumente nach dem Zufallsprinzip über die Zielsammlungen hinweg partitioniert).

Optional können Sie angeben, welches Feld in der Importquelle während des Imports als Azure Cosmos DB-Dokument-ID-Eigenschaft verwendet werden soll (bitte beachten: falls Dokumente diese Eigenschaft nicht enthalten, generiert das Importtool eine GUID als ID-Eigenschaftswert).

Während des Imports steht eine Reihe von erweiterten Optionen zur Verfügung. Erstens: Zwar enthält das Tool eine standardmäßige gespeicherte Prozedur für den Massenimport (BulkInsert.js), Sie können jedoch auch eine eigene gespeicherte Prozedur für den Import angeben.

 ![Screenshot der Azure Cosmos DB-Option zum Masseneinfügen mit gespeicherten Prozeduren](./media/import-data/bulkinsertsp.png)

Darüber hinaus können Sie beim Importieren von Datentypen (z. B. aus SQL Server oder MongoDB) zwischen drei Importoptionen auswählen:

 ![Screenshot der Optionen für den Azure Cosmos DB-Import von Datum und Uhrzeit](./media/import-data/datetimeoptions.png)

* Zeichenfolge: Als Zeichenfolgenwert beibehalten
* Epoche: Als numerischen Epochenwert beibehalten
* Beide: Sowohl als Zeichenfolgen- als auch als numerischen Epochenwert beibehalten Diese Option erstellt ein Filialdokument, zum Beispiel: "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

Das Azure Cosmos DB-Massenimportprogramm weist die folgenden erweiterten Optionen auf:

1. Batchgröße: Das Tool verwendet standardmäßig eine Batchgröße von 50.  Wenn die zu importierenden Dokumente groß sind, erwägen Sie, die Batchgröße zu verringern. Wenn die zu importierenden Dokumente dagegen klein sind, erwägen Sie, die Batchgröße zu erhöhen.
2. Maximale Skriptgröße (Byte): Das Tool verwendet standardmäßig eine maximale Skriptgröße von 512 KB.
3. Automatische ID-Generierung deaktivieren: Wenn jedes zu importierende Dokument ein ID-Feld enthält, kann durch Auswahl dieser Option die Leistung erhöht werden. Dokumente, die kein Feld für eine eindeutige ID enthalten, werden nicht importiert.
4. Aktualisieren vorhandener Dokumente: Das Tool ersetzt standardmäßig vorhandene Dokumente mit ID-Konflikten nicht. Durch Auswahl dieser Option können vorhandene Dokumente mit übereinstimmenden IDs überschrieben werden. Diese Funktion ist hilfreich für geplante Datenmigrationen, die vorhandene Dokumente aktualisieren.
5. Anzahl der Wiederholungsversuche bei Fehler: Gibt an, wie häufig bei einem vorübergehenden Fehler (z. B. Unterbrechung der Netzwerkverbindung) versucht werden soll, eine Verbindung zu Azure Cosmos DB herzustellen.
6. Intervall der Wiederholungsversuche: Gibt an, wie lang bei einem vorübergehenden Fehler (z. B. Unterbrechung der Netzwerkverbindung) gewartet werden soll, bevor erneut versucht wird, eine Verbindung zu Azure Cosmos DB herzustellen.
7. Verbindungsmodus: Gibt den Verbindungsmodus an, der mit Azure Cosmos DB verwendet werden soll. Verfügbare Optionen sind DirectTcp, DirectHttps und Gateway. Die Direktverbindungsmodi sind schneller, der Gatewaymodus ist besser für Firewalls geeignet, da nur Port 443 verwendet wird.

![Screenshot der erweiterten Optionen für den Azure Cosmos DB-Massenimport](./media/import-data/docdbbulkoptions.png)

> [!TIP]
> Standardmäßig verwendet das Importtool den Verbindungsmodus DirectTcp. Wenn Firewallprobleme auftreten, wechseln Sie zum Gatewaymodus, da dieser nur Port 443 erfordert.
> 
> 

## <a id="DocumentDBSeqTarget"></a>So importieren Sie in die DocumentDB-API (sequenzieller Datensatzimport)
Mit dem Programm für den Import von sequenziellen Azure Cosmos DB-Datensätzen können Sie Datensätze einzeln aus den verfügbaren Quelloptionen importieren. Sie können diese Option auswählen, wenn Sie Datensätze in eine vorhandene Sammlung importieren, die das Kontingent an gespeicherten Prozeduren erreicht hat. Das Tool unterstützt den Import in eine einzelne Azure Cosmos DB-Sammlung (mit sowohl einer einzelnen Partition als auch mit mehreren Partitionen) sowie den Shardimport, bei dem die Daten über mehrere Azure Cosmos DB-Sammlungen mit sowohl einer einzelnen Partition und/oder mehreren Partitionen hinweg partitioniert werden. Weitere Informationen zur Partitionierung von Daten finden Sie unter [Partitionieren und Skalieren von Daten in Azure Cosmos DB](partition-data.md).

![Screenshot der Optionen für den sequenziellen Azure Cosmos DB-Datensatzimport](./media/import-data/documentdbsequential.png)

Die Azure Cosmos DB-Verbindungszeichenfolge weist folgendes Format auf:

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

Die Verbindungszeichenfolge für das Azure Cosmos DB-Konto kann im Azure-Portal im Blatt „Schlüssel“ abgerufen werden. Eine Beschreibung hierzu finden Sie unter [Verwalten eines Azure Cosmos DB-Kontos](manage-account.md). Der Name der Datenbank muss jedoch im folgenden Format an das Ende der Verbindungszeichenfolge angehängt werden:

    Database=<Azure Cosmos DB Database>;

> [!NOTE]
> Verwenden Sie den Befehl „Überprüfen“, um sicherzustellen, dass auf die im Feld „Verbindungszeichenfolge“ angegebene Azure Cosmos DB-Instanz zugegriffen werden kann.
> 
> 

Zum Importieren von Daten in eine einzelne Sammlung geben Sie den Namen der Sammlung ein, in die Daten importiert werden sollen, und klicken dann auf die Schaltfläche "Hinzufügen". Zum Importieren von Daten in mehrere Sammlungen geben Sie entweder jeden Sammlungsnamen einzeln ein, oder geben Sie mit der folgenden Syntax mehrere Sammlungen an: *Sammlungspräfix*[IndexStart - IndexEnde]. Wenn Sie mehrere Sammlungen über die oben genannte Syntax angeben, berücksichtigen Sie Folgendes:

1. Nur Namensmuster im ganzzahligen Bereich werden unterstützt. Durch Eingabe von "Sammlung[0-3]" können Sie z. B. folgende Sammlungen angeben: Sammlung0, Sammlung1, Sammlung2, Sammlung3.
2. Sie können eine abgekürzte Syntax verwenden: Durch Eingabe von "Sammlung[3]" wird derselbe Satz von Sammlungen ausgegeben wie in Schritt 1.
3. Mehrere Ersetzungen können bereitgestellt werden. "Sammlung[0-1] [0-9]" generiert z. B. 20 Sammlungsnamen mit führenden Nullen (Sammlung01, ...02, ...03).

Nachdem Sie den bzw. die Sammlungsnamen angegeben haben, wählen Sie den gewünschten Durchsatz für die Sammlung(en) aus (400 RUs bis 250.000 RUs). Wählen Sie einen höheren Durchsatz, um die beste Importleistung zu erreichen. Weitere Informationen zu Leistungsebenen finden Sie unter [Leistungsebenen in Azure Cosmos DB](performance-levels.md). Importvorgänge in Sammlungen mit einem Durchsatz von mehr als 10.000 RUs erfordern einen Partitionsschlüssel. Wenn Sie mehr als 250.000 RUs auswählen, müssen Sie im Portal eine Anfrage stellen, um Ihr Konto zu erhöhen.

> [!NOTE]
> Die Durchsatzeinstellung gilt nur für die Sammlungserstellung. Wenn die angegebene Sammlung bereits vorhanden ist, wird der zugehörige Durchsatz nicht geändert.
> 
> 

Beim Importieren von Daten in mehrere Sammlungen unterstützt das Importtool Sharding auf Hashbasis. Geben Sie in diesem Szenario die Dokumenteigenschaft an, die als Partitionsschlüssel verwendet werden soll (wenn der Partitionsschlüssel leer ist, werden die Dokumente nach dem Zufallsprinzip über die Zielsammlungen hinweg partitioniert).

Optional können Sie angeben, welches Feld in der Importquelle während des Imports als Azure Cosmos DB-Dokument-ID-Eigenschaft verwendet werden soll (bitte beachten: falls Dokumente diese Eigenschaft nicht enthalten, generiert das Importtool eine GUID als ID-Eigenschaftswert).

Während des Imports steht eine Reihe von erweiterten Optionen zur Verfügung. Sie können beim Importieren von Datentypen (z. B. aus SQL Server oder MongoDB) zwischen drei Importoptionen auswählen:

 ![Screenshot der Optionen für den Azure Cosmos DB-Import von Datum und Uhrzeit](./media/import-data/datetimeoptions.png)

* Zeichenfolge: Als Zeichenfolgenwert beibehalten
* Epoche: Als numerischen Epochenwert beibehalten
* Beide: Sowohl als Zeichenfolgen- als auch als numerischen Epochenwert beibehalten Diese Option erstellt ein Filialdokument, zum Beispiel: "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

Das Programm für den Import von sequenziellen Azure Cosmos DB-Datensätzen weist die folgenden erweiterten Optionen auf:

1. Anzahl paralleler Anforderungen: Das Programm verwendet standardmäßig 2 parallele Anforderungen. Wenn die zu importierenden Dokumente klein sind, erwägen Sie, die Anzahl paralleler Anforderungen zu erhöhen. Beachten Sie, dass es zu einer Drosselung beim Import kommen kann, wenn eine zu hohe Anzahl festgelegt wird.
2. Automatische ID-Generierung deaktivieren: Wenn jedes zu importierende Dokument ein ID-Feld enthält, kann durch Auswahl dieser Option die Leistung erhöht werden. Dokumente, die kein Feld für eine eindeutige ID enthalten, werden nicht importiert.
3. Aktualisieren vorhandener Dokumente: Das Tool ersetzt standardmäßig vorhandene Dokumente mit ID-Konflikten nicht. Durch Auswahl dieser Option können vorhandene Dokumente mit übereinstimmenden IDs überschrieben werden. Diese Funktion ist hilfreich für geplante Datenmigrationen, die vorhandene Dokumente aktualisieren.
4. Anzahl der Wiederholungsversuche bei Fehler: Gibt an, wie häufig bei einem vorübergehenden Fehler (z. B. Unterbrechung der Netzwerkverbindung) versucht werden soll, eine Verbindung zu Azure Cosmos DB herzustellen.
5. Intervall der Wiederholungsversuche: Gibt an, wie lang bei einem vorübergehenden Fehler (z. B. Unterbrechung der Netzwerkverbindung) gewartet werden soll, bevor erneut versucht wird, eine Verbindung zu Azure Cosmos DB herzustellen.
6. Verbindungsmodus: Gibt den Verbindungsmodus an, der mit Azure Cosmos DB verwendet werden soll. Verfügbare Optionen sind DirectTcp, DirectHttps und Gateway. Die Direktverbindungsmodi sind schneller, der Gatewaymodus ist besser für Firewalls geeignet, da nur Port 443 verwendet wird.

![Screenshot der erweiterten Optionen für den sequenziellen Azure Cosmos DB-Datensatzimport](./media/import-data/documentdbsequentialoptions.png)

> [!TIP]
> Standardmäßig verwendet das Importtool den Verbindungsmodus DirectTcp. Wenn Firewallprobleme auftreten, wechseln Sie zum Gatewaymodus, da dieser nur Port 443 erfordert.
> 
> 

## <a id="IndexingPolicy"></a>Festlegen einer Indizierungsrichtlinie zum Erstellen von Azure Cosmos DB-Sammlungen
Wenn Sie zulassen, dass das Migrationstool während des Imports Sammlungen erstellt, können Sie die Indizierungsrichtlinie der Sammlungen festlegen. Navigieren Sie in den erweiterten Optionen für den Azure Cosmos DB-Massenimport und den sequenziellen Azure Cosmos DB-Datensatzimport zum Abschnitt zur Indizierungsrichtlinie.

![Screenshot der erweiterten Optionen für die Azure Cosmos DB-Indizierungsrichtlinie](./media/import-data/indexingpolicy1.png)

Mit der erweiterten Option der Indizierungsrichtlinie können Sie eine Indizierungsrichtliniendatei auswählen, manuell eine Indizierungsdatei eingeben oder aus einer Reihe von Standardvorlagen wählen (durch Rechtsklick in das Textfeld der Indizierungsrichtlinie).

Das Tool bietet folgende Richtlinienvorlagen:

* Standard. Diese Richtlinie empfiehlt sich, wenn Sie Gleichheitsabfragen für Zeichenfolgen durchführen und ORDER BY-, Bereichs- und Gleichheitsabfragen für Zahlen verwenden. Der Indexspeicheraufwand dieser Richtlinie ist geringer als bei „Bereich“.
* Bereich. Wählen Sie diese Richtlinie, wenn Sie ORDER BY-, Bereichs- und Gleichheitsabfragen für Zahlen und Zeichenfolgen durchführen. Der Indexspeicheraufwand dieser Richtlinie ist höher als bei „Standard“ oder „Hash“.

![Screenshot der erweiterten Optionen für die Azure Cosmos DB-Indizierungsrichtlinie](./media/import-data/indexingpolicy2.png)

> [!NOTE]
> Wenn Sie keine Indizierungsrichtlinien angeben, wird die Standardrichtlinie verwendet. Weitere Informationen zu den Indizierungsrichtlinien finden Sie unter [Indizierungsrichtlinien für Azure Cosmos DB](indexing-policies.md).
> 
> 

## <a name="export-to-json-file"></a>Exportieren in JSON-Datei
Mit dem Azure Cosmos DB-JSON-Exportprogramm können Sie alle verfügbaren Quelloptionen in eine JSON-Datei exportieren, die ein Array aus JSON-Dokumenten enthält. Sie können auswählen, ob das Programm den Export verarbeiten soll, oder ob Sie den resultierenden Migrationsbefehl anzeigen und den Befehl selbst ausführen möchten Die generierte JSON-Datei kann lokal oder im Azure Blob-Speicher gespeichert werden.

![Screenshot der Option zum Erstellen lokaler Azure Cosmos DB JSON-Dateiexporte](./media/import-data/jsontarget.png)

![Screenshot der Option zum Erstellen von Azure Cosmos DB JSON Azure Blob Storage-Exporten](./media/import-data/jsontarget2.png)

Sie können optional die generierte JSON-Datei  optimieren, wodurch die Größe des resultierenden Dokuments reduziert wird und die Inhalte besser lesbar werden.

    Standard JSON export
    [{"id":"Sample","Title":"About Paris","Language":{"Name":"English"},"Author":{"Name":"Don","Location":{"City":"Paris","Country":"France"}},"Content":"Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.","PageViews":10000,"Topics":[{"Title":"History of Paris"},{"Title":"Places to see in Paris"}]}]

    Prettified JSON export
    [
     {
    "id": "Sample",
    "Title": "About Paris",
    "Language": {
      "Name": "English"
    },
    "Author": {
      "Name": "Don",
      "Location": {
        "City": "Paris",
        "Country": "France"
      }
    },
    "Content": "Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.",
    "PageViews": 10000,
    "Topics": [
      {
        "Title": "History of Paris"
      },
      {
        "Title": "Places to see in Paris"
      }
    ]
    }]

## <a name="advanced-configuration"></a>Erweiterte Konfiguration
Geben Sie auf dem Bildschirm "Erweiterte Konfiguration" den Speicherort der Protokolldatei an, in die Fehler geschrieben werden sollen. Für diese Seite gelten folgende Regeln:

1. Wenn kein Dateiname angegeben ist, werden alle Fehler auf der Seite "Results" zurückgegeben.
2. Wenn eine Datei ohne ein Verzeichnis angegeben ist, wird die Datei im aktuellen Umgebungsverzeichnis erstellt (oder überschrieben).
3. Bei Auswahl einer vorhandenen Datei wird diese Datei überschrieben; es gibt keine Option zum Anfügen.

Wählen Sie dann, ob alle, nur die kritischen oder gar keine Fehlermeldungen protokolliert werden sollen. Entscheiden Sie zum Schluss, wie häufig der Fortschritt in der Bildschirmmeldung zur Übertragung aktualisiert wird.

    ![Screenshot of Advanced configuration screen](./media/import-data/AdvancedConfiguration.png)

## <a name="confirm-import-settings-and-view-command-line"></a>Bestätigen der Importeinstellungen und Anzeigen der Befehlszeile
1. Nachdem Sie die Quell- und Zielinformationen und die erweiterte Konfiguration angegeben haben, überprüfen Sie die Migrationszusammenfassung, und zeigen Sie optional den resultierenden Migrationsbefehl an bzw. kopieren Sie ihn (das Kopieren des Befehls ist hilfreich, um Importvorgänge zu automatisieren):
   
    ![Screenshot des Übersichtsbildschirms](./media/import-data/summary.png)
   
    ![Screenshot des Übersichtsbildschirms](./media/import-data/summarycommand.png)
2. Wenn Sie die Quell- und Zieloptionen zu Ihrer Zufriedenheit eingerichtet haben, klicken Sie auf **Importieren**. Während des Importvorgangs werden die verstrichene Zeit, die Anzahl der Übertragungen und Fehlerinformationen (sofern Sie in der erweiterten Konfiguration keinen Dateinamen angegeben haben) aktualisiert. Wenn der Vorgang abgeschlossen ist, können Sie die Ergebnisse exportieren (um z. B. Importfehler zu behandeln).
   
    ![Screenshot der Option zum Erstellen von Azure Cosmos DB JSON-Exporten](./media/import-data/viewresults.png)
3. Sie können auch einen neuen Import starten und dabei entweder die vorhandenen Einstellungen (z. B. Informationen zur Verbindungszeichenfolge, Auswahl von Quelle und Ziel usw.) beibehalten oder alle Werte zurücksetzen.
   
    ![Screenshot der Option zum Erstellen von Azure Cosmos DB JSON-Exporten](./media/import-data/newimport.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie die folgenden Aufgaben ausgeführt:

> [!div class="checklist"]
> * Installieren des Datenmigrationstools
> * Importieren von Daten aus verschiedenen Datenquellen
> * Exportieren aus Azure Cosmos DB zu JSON

Sie können nun mit dem nächsten Tutorial fortfahren und sich darüber informieren, wie Sie Daten mithilfe von Azure Cosmos DB abfragen können. 

> [!div class="nextstepaction"]
>[Abfragen von Daten](../cosmos-db/tutorial-query-documentdb.md)

