<properties
   pageTitle="SQL Data Warehouse – Kapazitätsgrenzen | Microsoft Azure"
   description="Maximalwerte für Verbindungen, Abfragen, Transact-SQL-DDL und -DML und Systemsichten für SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/23/2016"
   ms.author="barbkess;jrj;sonyama"/>

# Kapazitätsgrenzen von SQL Data Warehouse

Die folgenden Maximalwerte gewährleisten die Durchführung anspruchsvollster analytischer Workloads. Gleichzeitig ist sichergestellt, dass einzelnen Abfragen die für eine optimale Performance benötigten Ressourcen zur Verfügung stehen.

## Dienst

| Kategorie | Beschreibung | Maximum |
| :---------------- | :------------------------------------------- | :----------------- |
| Datenbank | Gleichzeitig geöffnete Sitzungen | 1\.024<br/><br/>Wir unterstützen maximal 1.024 aktive Verbindungen, die gleichzeitig Anforderungen an jede SQL Data Warehouse-Datenbank übermitteln können. Beachten Sie, dass die Anzahl der Abfragen, die gleichzeitig ausgeführt werden können, begrenzt ist. Wenn ein Grenzwert überschritten wird, gelangt die Anforderung in eine interne Warteschlange, in der sie auf die Verarbeitung wartet.|
| Datenbankverbindung | Maximaler Arbeitsspeicher für vorbereitete Anweisungen | 20 MB |


## Verarbeitung von Abfragen

| Kategorie | Beschreibung | Maximum |
| :---------------- | :------------------------------------------- | :----------------- |
| Abfrage | Gleichzeitige Abfragen von Benutzertabellen. | 32<br/><br/>Dies ist die obere Grenze für die gleichzeitige Ausführung von Benutzerabfragen. Zusätzliche Abfragen gelangen in eine interne Warteschlange, in der sie auf die Verarbeitung warten. Unabhängig von der Anzahl der Abfragen, die gleichzeitig ausgeführt werden, wird jede Abfrage optimiert, um die massive Parallelverarbeitungsarchitektur vollständig zu nutzen. Hinweis: Tatsächliche Gleichzeitigkeit kann von zusätzlicher Drosselung abhängen, die auf der DWU der Datenbankinstanz und der ausgewählten Ressourcenklasse der ausgeführten Abfragen basiert.|
| Abfrage | In Warteschlange gestellte Abfragen von Benutzertabellen. | 1000 |
| Abfrage | Gleichzeitige Abfragen von Systemsichten. | 100 |
| Abfrage | In Warteschlange gestellte Abfragen von Systemsichten | 1000 |
| Abfrage | Maximale Parameter | 2098 |
| Batch | Maximale Größe | 65\.536*4096 |


## Data Definition Language (DDL)

| Kategorie | Beschreibung | Maximum |
| :---------------- | :------------------------------------------- | :----------------- |
| Tabelle | Tabellen pro Datenbank | 2 Milliarden |
| Tabelle | Spalten pro Tabelle | 1024 Spalten |
| Tabelle | Bytes pro Spalte | 8000 Bytes |
| Tabelle | Bytes pro Zeile, definierte Größe | 8\.060 Bytes<br/><br/>Die Anzahl der Bytes pro Zeile wird auf die gleiche Weise wie bei SQL Server mit aktivierter Seitenkomprimierung berechnet. Wie SQL Server auch, unterstützt SQL Data Warehouse die Speicherung von Zeilenüberlaufsdaten, sodass Spalten variabler Länge aus der Zeile verschoben werden können. Im Hauptdatensatz für Spalten variabler Länge, die aus einer Zeile verschoben wurden, wird nur ein 24-Byte-Stamm gespeichert. Weitere Informationen finden Sie unter [Zeilenüberlauf bei Daten über 8 KB](https://msdn.microsoft.com/library/ms186981.aspx) in der SQL Server-Onlinedokumentation.<br/><br/>Eine Liste der SQL Data Warehouse-Datentypgrößen finden Sie unter [CREATE TABLE (Azure SQL Data Warehouse)](https://msdn.microsoft.com/library/mt203953.aspx). |
| Tabelle | Bytes pro Zeile, die Größe des internen Puffers zum Verschieben von Daten | 32\.768<br/><br/>HINWEIS: Diese Einschränkung besteht zurzeit, wird aber bald behoben.<br/><br/>SQL Data Warehouse verwendet einen internen Puffer, um Zeilen innerhalb des verteilten SQL Data Warehouse-Systems zu verschieben. Der Dienst, der Zeilen verschiebt, heißt Data Movement Service (DMS) und speichert Zeilen in einem Format, das von SQL Server abweicht.<br/><br/>Wenn eine Zeile nicht in den internen Puffer passt, erhalten Sie einen Abfragekompilierungsfehler oder einen internen Datenverschiebungsfehler. Um dieses Problem zu vermeiden, lesen Sie [Details about the DMS buffer size](#details-about-the-dms-buffer-size) (Details zur DMS-Puffergröße).|
| Tabelle | Partitionen pro Tabelle | 15\.000<br/><br/>Um eine hohe Leistung zu erzielen, empfehlen wir, die Anzahl der Partitionen zu minimieren, die Sie zum Erfüllen Ihrer Geschäftsanforderungen benötigen. Mit einer steigenden Anzahl von Partitionen wächst der Verarbeitungsaufwand für Datendefinitionssprache (DDL)- und Datenbearbeitungssprache (DML)-Vorgänge, was zu Leistungseinbußen führt.|
| Tabelle | Zeichen pro Partitionsbegrenzungswert.| 4000 |
| Index | Nicht gruppierte Indizes pro Tabelle. | 999<br/><br/>Gilt nur für Rowstore-Tabellen|
| Index | Gruppierte Indizes pro Tabelle. | 1<br><br/>Gilt sowohl für Rowstore- als auch für Columnstore-Tabellen|
| Index | Zeilen in einer Rowgroup eines Columnstore-Indexes | 1\.024<br/><br/>Jeder Columnstore-Index wird als mehrfacher Columnstore-Index implementiert. Beachten Sie, dass beim Einfügen von 1.024 Zeilen in einen SQL Data Warehouse-Columnstore-Index die Zeilen nicht alle in dieselbe Rowgroup übertragen werden.|
| Index | Parallele Erstellung gruppierter Columnstore-Indizes. | 32<br/><br/>Gilt, wenn die gruppierten Columnstore-Indizes alle aus verschiedenen Tabellen erstellt werden Pro Tabelle ist nur das Erstellen eines gruppierten Columnstore-Index zulässig. Zusätzliche Anforderungen werden in eine Warteschlange gestellt.|
| Index | Größe des Indexschlüssels. | 900 Bytes<br/><br/>Gilt nur für Rowstore-Indizes.<br/><br/>Indizes für „varchar“-Spalten mit einer maximalen Größe von mehr als 900 Bytes können erstellt werden, wenn die vorhandenen Daten in den Spalten bei der Indexerstellung nicht größer als 900 Bytes sind. Anschließende auf die Spalten angewendete INSERT- oder UPDATE-Anweisungen, die bewirken, dass die Gesamtgröße 900 Bytes überschreitet, haben allerdings keinen Erfolg.|
| Index | Schlüsselspalten pro Index. | 16<br/><br/>Gilt nur für Rowstore-Indizes Gruppierte Columnstore-Indizes enthalten alle Spalten.|
| Statistiken | Größe der kombinierten Spaltenwerte. | 900 Bytes. |
| Statistiken | Spalten pro Statistikobjekt. | 32 |
| Statistiken | Für Spalten pro Tabelle erstellte Statistiken. | 30\.000 |
| Gespeicherte Prozeduren | Maximale Schachtelungsebenen. | 8 |
| Sicht | Spalten pro Sicht | 1024 |


## Data Manipulation Language (DML)

| Kategorie | Beschreibung | Maximum |
| :---------------- | :------------------------------------------- | :----------------- |
| SELECT-Ergebnisse | Spalten pro Zeile | 4\.096<br/><br/>Das Ergebnis einer SELECT-Anweisung kann nie mehr als 4.096 Spalten pro Zeile enthalten. Es gibt keine Garantie, dass Sie stets über 4096 verfügen. Wenn der Abfrageplan eine temporäre Tabelle erfordert, gilt möglicherweise der Maximalwert von 1024 Spalten pro Tabelle.|
| SELECT | Geschachtelte Unterabfragen | 32<br/><br/>In einer SELECT-Anweisung sind maximal 32 geschachtelte Unterabfragen zulässig. Es gibt keine Garantie, dass Sie stets über 32 verfügen. Ein JOIN-Befehl kann z. B. eine Unterabfrage in den Abfrageplan einführen. Die Anzahl der Unterabfragen kann auch durch den verfügbaren Speicher eingeschränkt werden.|
| SELECT | Spalten pro JOIN | 1\.024 Spalten<br/><br/>Für einen JOIN sind maximal 1.024 Spalten zulässig. Es gibt keine Garantie, dass Sie stets über 1024 verfügen. Wenn der JOIN-Plan eine temporäre Tabelle mit mehr Spalten als das JOIN-Ergebnis erfordert, gilt die Grenze von 1024 für die temporäre Tabelle. |
| SELECT | Bytes pro GROUP BY-Spalten. | 8\.060<br/><br/>Die Maximalgröße von Spalten in der GROUP BY-Klausel beträgt 8.060 Bytes.|
| SELECT | Bytes pro ORDER BY-Spalten | 8\.060<br/><br/>Die Maximalgröße von Spalten in der ORDER BY-Klausel beträgt 8.060 Bytes.|
| Bezeichner und Konstanten pro Anweisung | Anzahl der Bezeichner und Konstanten, auf die verwiesen wird. | 65\.535<br/><br/>SQL Data Warehouse beschränkt die Anzahl von Bezeichnern und Konstanten, die in einem einzelnen Ausdruck einer Abfrage enthalten sein können. Dieser Grenzwert ist 65.535. Das Überschreiten dieses Werts führt zum SQL Server-Fehler 8632. Weitere Informationen finden Sie unter [Interner Fehler: ein Ausdrucksdienstelimit wurde erreicht.](http://support.microsoft.com/kb/913050/).|

## Systemsichten

| Systemsicht | Maximale Anzahl von Zeilen |
| :--------------------------------- | :------------|
| sys.dm\_pdw\_component\_health\_alerts | 10\.000 |
| sys.dm\_pdw\_dms\_cores | 100 |
| sys.dm\_pdw\_dms\_workers | Gesamtanzahl der DMS-Worker für die letzten 1000 SQL-Anforderungen. |
| sys.dm\_pdw\_errors | 10\.000 |
| sys.dm\_pdw\_exec\_requests | 10\.000 |
| sys.dm\_pdw\_exec\_sessions | 10\.000 |
| sys.dm\_pdw\_request\_steps | Die Gesamtzahl der Schritte für die letzten 1000 SQL-Anforderungen, die in „sys.dm\_pdw\_exec\_requests“ gespeichert sind. |
| sys.dm\_pdw\_os\_event\_logs | 10\.000 |
| sys.dm\_pdw\_sql\_requests | Die letzten 1000 SQL-Anforderungen, die in „sys.dm\_pdw\_exec\_requests“ gespeichert sind. |


## Details zur Größe des DMS-Puffers

SQL Data Warehouse verwendet einen internen Puffer, um Zeilen unter den Back-End-Computeknoten zu verschieben. Die Daten werden mit einem Dienst zur Datenverschiebung (Data Movement Service, DMS) verschoben und in einem Format gespeichert, das sich von SQL Server unterscheidet.

Zum Verbessern der Leistung paralleler Abfragen füllt DMS alle Daten mit variabler Länge auf die maximale für die SQL-Datenbank definierte Größe auf. Der Wert „Hallo“ für ein `nvarchar(2000) NOT NULL` belegt z. B. tatsächlich 4.002 Bytes im DMS-Puffer. Er verwendet 2 Byte für jedes der 2000 Zeichen plus 2 Bytes für den NULL-Terminator.

> [AZURE.NOTE] Ein interner Fehler tritt auf, wenn DMS versucht, eine Zeile zu verschieben, die die DMS-Puffergröße von 32.768 Bytes überschreitet. Wenn die Zeilengröße die DMS-Puffergröße überschreitet, müssen Sie die Tabellendefinition überarbeiten, damit die Zeile in den DMS-Puffer passt.

### Bestimmen der Zeilengröße für den DMS-Puffer
In diesem Beispiel werden die vom DMS definierten Größen von Daten mit variabler Länge beschrieben. Anschließend wird gezeigt, die die DMS-Puffergröße für eine Zeile berechnet wird.
 
Im DMS-Puffer ist die Größe von Daten variabler Länge die Summe folgender Angaben:

- Definierte Größe für Zeichen.
- NULL-Typen verwenden 8 Bytes für den NULL-Indikator.
- ASCII-Typen verwenden 1 Zeichen für den NULL-Terminator.
- Unicode-Typen verwenden 2 Zeichen für den NULL-Terminator.
             
| Datentyp | DMS-Puffergröße |
| :---------------------- | :-------------------------- |                
| char(1000) NULL | 1009 Bytes = 1000 + 8 + 1 |
| char(1000) NOT NULL | 1001 Bytes = 1000 + 1 |
| nchar(1000) NULL | 2010 Bytes = 2000 + 8 + 2 |
| varchar(1000) NULL | 1009 Bytes = 1000 + 8 + 1 |
| varchar(1000) NOT NULL | 1009 Bytes = 1000 + 1 |
| nvarchar(1000) NULL | 2010 Bytes = 2000 + 8 + 2 |
| nvarchar(1000) NOT NULL | 2010 Bytes = 2000 + 2 |
                
Im DNS-Puffer belegen Spalten mit fester Länge die native SQL Server-Größe. Wenn der Typ NULL-Werte zulässt, erfordert DMS zusätzliche 8 Bytes. Die SQL Server-Größe finden Sie im Feld „max\_length“ in „sys.types“.

Beispiel:

| Datentyp mit fester Breite | DMS-Puffergröße |
| :-------------------- | :----------------- |
| int NULL | 12 Bytes = 4 + 8 |
| int NOT NULL | 4 Bytes = 4 + 0 | 
                
In Summe ist die definierte Größe für den DMS-Puffer für die folgende Zeile 31.134 Bytes, die in den DMS-Puffer passen.

| Spalte | Datentyp | Spaltengröße |
| :----- | :------------------ | :------------------------ |
| col1 | datetime2 (7) NULL | 20 Bytes = 8 + 8 |
| col2 | float (4) NULL | 12 Bytes = 4 + 8 |
| col3 | nchar (6) NULL | 22 bytes = 12 + 8 + 2 |
| col4 | char (7000) NULL | 7009 Bytes = 7000 + 8 +1 |
| col5 | nvarchar (4000) | 8002 Bytes = 8000 + 2 |
| col6 | varchar (8000) NULL | 8009 bytes = 8000 + 8 + 1 |
| col7 | varbinary (8000) | 8000 Bytes |
| col8 | binary (60) | 60 Bytes |
                
              
### Beispiel für das Überschreiten der DMS-Puffergröße

Dieses Beispiel zeigt, wie Sie eine Zeile erfolgreich in SQL Data Warehouse einfügen können, dann aber einen DMS-Überlauffehler auslösen, wenn DMS die Zeile für einen nicht verteilungskompatiblen JOIN verschieben muss. Das Fazit lautet, dass kleinere Zeilen erstellt werden sollten, die in den DMS-Puffer passen.

Im folgenden Beispiel erstellen wir die Tabelle T1. Die maximale Größe der Zeile, wenn alle „nvarchar“-Variablen 4000 Unicode-Zeichen aufweisen, ist mehr als 40.000 Bytes, was die maximale Größe des DMS-Puffers überschreitet.

Da die tatsächliche definierte Größe eines „nvarchar“-Werts 26 Bytes ist, beträgt die Zeilendefinition weniger als 8060 Bytes und kann deshalb auf eine SQL Server-Seite passen. Daher ist die CREATE TABLE-Anweisung erfolgreich, obwohl ein DMS-Fehler auftritt, wenn versucht wird, diese Zeile in den DMS-Puffer zu laden.

```sql
CREATE TABLE T1
  (
    c0 int NOT NULL,
    CustomerKey int NOT NULL,
    c1 nvarchar(4000),
    c2 nvarchar(4000),
    c3 nvarchar(4000),
    c4 nvarchar(4000),
    c5 nvarchar(4000)
  )
WITH ( DISTRIBUTION = HASH (c0) )
;
```

Dieser nächste Schritt zeigt, dass wir INSERT erfolgreich verwenden können, um Daten in die Tabelle einzufügen. Diese Anweisung lädt die Daten direkt in SQL Server, ohne DMS zu verwenden, weshalb kein Fehler aufgrund eines DMS-Pufferüberlaufs auftritt. Integrationsdienste laden diese Zeile ebenfalls erfolgreich.</para>

```sql
--The INSERT operation succeeds because the row is inserted directly into SQL Server without requiring DMS to buffer the row.
INSERT INTO T1
VALUES (
    25,
    429817,
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.'
  )
```

Zum Veranschaulichen der Datenverschiebung erstellt dieses Beispiel eine zweite Tabelle mit „CustomerKey“ für die Verteilungsspalte.

```sql
--This second table is distributed on CustomerKey. 
CREATE TABLE T2
  (
    c0 int NOT NULL,
    CustomerKey int NOT NULL,
    c1 nvarchar(4000),
    c2 nvarchar(4000),
    c3 nvarchar(4000),
    c4 nvarchar(4000),
    c5 nvarchar(4000)
  )
WITH ( DISTRIBUTION = HASH (CustomerKey) )
;

INSERT INTO T2
VALUES (
    32,
    429817,
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.'
  )
```
Da beide Tabellen nicht anhand von „CustomerKey“ verteilt werden, ist ein JOIN zwischen T1 und T2 anhand von „CustomerKey“ nicht für die Verteilung kompatibel. DMS muss mindestens eine Zeile laden und sie in eine andere Verteilung kopieren.

```sql
SELECT * FROM T1 JOIN T2 ON T1.CustomerKey = T2.CustomerKey;
```

Wie erwartet, kann DMS den Join nicht ausführen, da die Zeile, wenn alle „nvarchar“-Spalten aufgefüllt werden, größer als der DMS-Puffer (32.768 Bytes) ist. Die folgende Fehlermeldung wird angezeigt.

```sql
Msg 110802, Level 16, State 1, Line 126

An internal DMS error occurred that caused this operation to fail. Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Workers.DmsSqlNativeException, Message: SqlNativeBufferReader.ReadBuffer, error in OdbcReadBuffer: SqlState: , NativeError: 0, 'COdbcReadConnection::ReadBuffer: not enough buffer space for one row | Error calling: pReadConn-&gt;ReadBuffer(pBuffer, bufferOffset, bufferLength, pBytesRead, pRowsRead) | state: FFFF, number: 81, active connections: 8', Connection String: Driver={SQL Server Native Client 11.0};APP=DmsNativeReader:P13521-CMP02\sqldwdms (4556) - ODBC;Trusted_Connection=yes;AutoTranslate=no;Server=P13521-SQLCMP02,1500
```


## Nächste Schritte
Weitere Referenzinformationen finden Sie unter [SQL Data Warehouse-Referenz – Übersicht][].

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse-Referenz – Übersicht]: sql-data-warehouse-overview-reference.md

<!--MSDN references-->

<!---HONumber=AcomDC_0330_2016-->