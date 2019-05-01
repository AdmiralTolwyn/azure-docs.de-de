---
title: Grundlegendes zu den Ausgaben von Azure Stream Analytics
description: In diesem Artikel werden die Datenausgabeoptionen beschrieben, die in Azure Stream Analytics verfügbar sind, z.B. Power BI für Analyseergebnisse.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/25/2019
ms.custom: seodec18
ms.openlocfilehash: f9663fb502d9eda4810c54a89a94481b7d00cd43
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926058"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Grundlegendes zu den Ausgaben von Azure Stream Analytics
In diesem Artikel werden die Arten von Ausgaben beschrieben, die für einen Azure Stream Analytics-Auftrag verfügbar sind. Mit Ausgaben können Sie die Ergebnisse des Stream Analytics-Auftrags aufbewahren und speichern. Indem Sie die Ausgabedaten verwenden, können Sie weitere Geschäftsanalysen und Data Warehousing-Vorgänge für Ihre Daten durchführen.

Verweisen Sie beim Entwerfen Ihrer Stream Analytics-Abfrage auf den Namen der Ausgabe, indem Sie die [INTO-Klausel](https://msdn.microsoft.com/azure/stream-analytics/reference/into-azure-stream-analytics) verwenden. Sie können eine einzelne Ausgabe pro Auftrag oder (bei Bedarf) auch mehrere Ausgaben pro Streamingauftrag verwenden, indem Sie in der Abfrage mehrere INTO-Klauseln angeben.

Zum Erstellen, Bearbeiten und Testen von Stream Analytics-Auftragsausgaben können Sie das [Azure-Portal](stream-analytics-quick-create-portal.md#configure-job-output), [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), die [.NET-API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), die [REST-API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output) und [Visual Studio](stream-analytics-quick-create-vs.md) verwenden.

Einige Ausgabetypen unterstützen die [Partitionierung](#partitioning). Die [Größe der Ausgabebatches](#output-batch-size) variiert, um den Durchsatz zu optimieren.


## <a name="azure-data-lake-store"></a>Azure Data Lake Store
Stream Analytics unterstützt [Azure Data Lake-Speicher](https://azure.microsoft.com/services/data-lake-store/). Azure Data Lake Store ist ein unternehmensweites Hyperscale-Repository für Big Data-Analyseworkloads. Mit Data Lake Store können Sie Daten von beliebiger Größe, Art und Erfassungsgeschwindigkeit zur Durchführung operativer und explorativer Analysen speichern. Stream Analytics muss autorisiert werden, um auf Data Lake Store zugreifen zu können.

Die Azure Data Lake Store-Ausgabe aus Stream Analytics ist zurzeit nicht in den Regionen Azure China (21Vianet) und Azure Deutschland (T-Systems International) verfügbar.

### <a name="authorize-an-azure-data-lake-store-account"></a>Autorisieren eines Azure Data Lake Store-Kontos

1. Wenn Sie Data Lake Store im Azure-Portal als Ausgabe auswählen, werden Sie aufgefordert, eine Verbindung mit einer vorhandenen Data Lake Store-Instanz zu autorisieren.

   ![Autorisieren einer Verbindung mit Data Lake Store](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)

2. Wenn Sie bereits Zugriff auf Data Lake Store haben, wählen Sie **Jetzt autorisieren** aus. Es wird eine Seite geöffnet, die **Umleitung an die Autorisierung** anzeigt. Nach erfolgter Autorisierung wird die Seite angezeigt, über die Sie die Data Lake Store-Ausgabe konfigurieren können.

3. Nachdem Sie das Data Lake Store-Konto authentifiziert haben, können Sie die Eigenschaften für die Data Lake Store-Ausgabe konfigurieren.

   ![Definieren von Data Lake Store als Stream Analytics-Ausgabe](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)

In der folgenden Tabelle sind Eigenschaftsnamen und deren Beschreibungen aufgeführt, um Ihre Data Lake Store-Ausgabe zu konfigurieren.   

| Eigenschaftenname | BESCHREIBUNG |
| --- | --- |
| Ausgabealias | Ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an Data Lake Store weiterzuleiten. |
| Kontoname | Der Name des Data Lake Store-Kontos, an das Sie die Ausgabe senden. Eine Dropdownliste der in Ihrem Abonnement verfügbaren Data Lake Store-Konten wird angezeigt. |
| Präfixmuster des Pfads | Der Dateipfad, mit dem Ihre Dateien im angegebenen Data Lake Store-Konto geschrieben werden. Sie können eine oder mehrere Instanzen der Variablen {date} und {time} angeben:<br /><ul><li>Beispiel 1: folder1/logs / {date} / {time}</li><li>Beispiel 2: folder1/logs / {date}</li></ul><br />Der Zeitstempel der erstellten Ordnerstruktur folgt der UTC, nicht der lokalen Zeit.<br /><br />Wenn das Dateipfadmuster keinen nachgestellten Schrägstrich („/“) enthält, wird auch das letzte Muster im Dateipfad als Dateinamenpräfix behandelt. <br /><br />In diesen Fällen werden neue Dateien erstellt:<ul><li>Änderung im Ausgabeschema</li><li>Externer oder interner Neustart eines Auftrags</li></ul> |
| Datumsformat | Optional. Wenn das date-Token im Pfadpräfix verwendet wird, können Sie das Datumsformat auswählen, unter dem die Dateien gespeichert werden. Beispiel: YYYY/MM/DD |
|Zeitformat | Optional. Wenn das time-Token im Pfadpräfix verwendet wird, können Sie das Zeitformat auswählen, unter dem die Dateien gespeichert werden. Der einzige derzeit unterstützte Wert ist HH |
| Ereignisserialisierungsformat | Das Serialisierungsformat für Ausgabedaten. Es werden JSON, CSV und Avro unterstützt.|
| Codieren | Bei Verwendung des CSV- oder JSON-Formats muss eine Codierung angegeben werden. UTF-8 ist derzeit das einzige unterstützte Codierungsformat.|
| Trennzeichen | Gilt nur für die CSV-Serialisierung. Stream Analytics unterstützt eine Reihe von üblichen Trennzeichen zum Serialisieren der CSV-Daten. Unterstützte Werte sind Komma, Semikolon, Leerzeichen, Tabstopp und senkrechter Strich.|
| Format | Gilt nur für die JSON-Serialisierung. **Separate Zeile** gibt an, dass die Ausgabe so formatiert wird, dass jedes JSON-Objekt in einer neuen Zeile enthalten ist. **Array** gibt an, dass die Ausgabe als Array aus JSON-Objekten formatiert wird. Dieses Array wird nur geschlossen, wenn der Auftrag beendet wird oder Stream Analytics mit dem nächsten Zeitfenster fortfährt. Im Allgemeinen ist es besser, in separaten Zeilen geschriebenen JSON-Code zu verwenden, da er keine spezielle Behandlung erfordert, während noch in die Ausgabedatei geschrieben wird.|

### <a name="renew-data-lake-store-authorization"></a>Erneuern der Data Lake Store-Autorisierung
Sie müssen Ihr Data Lake Store-Konto erneut authentifizieren, wenn das Kennwort seit der Erstellung oder letzten Authentifizierung Ihres Auftrags geändert wurde. Wenn Sie sich nicht erneut authentifizieren, ergibt Ihr Auftrag keine Ausgabeergebnisse und zeigt einen Fehler mit dem Hinweis an, dass in den Vorgangsprotokollen eine erneute Autorisierung erfolgen muss. 

Derzeit muss das Authentifizierungstoken alle 90 Tage manuell für sämtliche Aufträge mit der Data Lake Store-Ausgabe aktualisiert werden muss. Sie können diese Einschränkung durch [Authentifizierung mithilfe verwalteter Identitäten (Vorschau)](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-managed-identities-adls) überwinden.

So erneuern Sie die Autorisierung

1. Wählen Sie **Beenden** aus, um Ihren Auftrag zu beenden.
1. Gehen Sie zu Ihrer Data Lake Store-Ausgabe, und wählen Sie den Link **Autorisierung erneuern** aus.

   Für kurze Zeit zeigt eine Popupseite **Umleitung an die Autorisierung** an. Wenn die Autorisierung erfolgreich ist, zeigt die Seite **Autorisierung wurde erfolgreich erneuert** an und wird dann automatisch geschlossen. 
   
1. Wählen Sie unten auf der Seite **Speichern** aus. Sie können Ihren Auftrag dann über **Letzte Beendigungszeit** neu starten, um Datenverluste zu vermeiden.

![Erneuern der Data Lake Store-Autorisierung in der Ausgabe](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)

## <a name="sql-database"></a>SQL-Datenbank
Sie können [Azure SQL-Datenbank](https://azure.microsoft.com/services/sql-database/) als Ausgabe für relationale Daten oder für Anwendungen verwenden, die auf Inhalten aufsetzen, die in einer relationalen Datenbank gehostet werden. Stream Analytics-Aufträge schreiben in eine vorhandene Tabelle in einer SQL-Datenbank. Das Tabellenschema muss genau den Feldern und deren Typen in der Ausgabe Ihres Auftrags entsprechen. Sie können auch [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) als Ausgabe über die Ausgabeoption „SQL-Datenbank“ angeben. Weitere Informationen zu Möglichkeiten zur Verbesserung des Schreibdurchsatzes finden Sie im Artikel [Stream Analytics mit Azure SQL-Datenbank als Ausgabe](stream-analytics-sql-output-perf.md). 

Die folgende Tabelle enthält die Eigenschaftennamen und die entsprechenden Beschreibungen zum Erstellen einer SQL-Datenbank-Ausgabe.

| Eigenschaftenname | BESCHREIBUNG |
| --- | --- |
| Ausgabealias |Ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an diese Datenbank weiterzuleiten. |
| Datenbank | Der Name der Datenbank, an die Sie die Ausgabe senden. |
| Servername | Der Name des SQL-Datenbank-Servers |
| Username | Der Benutzername, der Schreibzugriff auf die Datenbank hat. Stream Analytics unterstützt nur die SQL-Authentifizierung. |
| Kennwort | Das Kennwort zum Herstellen einer Verbindung mit der Datenbank |
| Table | Der Name der Tabelle, in die die Ausgabe geschrieben wird. Beim Tabellennamen wird die Groß- und Kleinschreibung beachtet. Das Schema dieser Tabelle sollte genau der Anzahl der Felder und deren Typen entsprechen, die Ihre Auftragsausgabe generiert. |
|Erben des Partitionsschemas| Eine Option zum Erben des Partitionierungsschemas Ihres vorherigen Abfrageschrittes, um die vollständig parallele Topologie mit mehreren in die Tabelle Schreibenden zu aktivieren. Weitere Informationen finden Sie unter [Azure Stream Analytics-Ausgabe an Azure SQL-Datenbank](stream-analytics-sql-output-perf.md).|
|Übereinstimmung mit der Batchanzahl| Der empfohlene Grenzwert für die Anzahl der Sätze, die mit jeder Transaktion zum Masseneinfügen gesendet werden.|

> [!NOTE]
> Zurzeit wird das Azure SQL-Datenbank-Angebot für eine Auftragsausgabe in Stream Analytics unterstützt. Ein virtueller Azure-Computer mit SQL Server und angefügter Datenbank wird nicht unterstützt. Dies soll in zukünftigen Versionen geändert werden.
>

## <a name="blob-storage"></a>Blob Storage
Azure Blob Storage bietet eine kostengünstige und skalierbare Lösung zum Speichern von großen Mengen unstrukturierter Daten in der Cloud. Eine Einführung in Blobspeicher und dessen Nutzung finden Sie unter [Verwenden von Blobs](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

Die folgende Tabelle enthält die Eigenschaftennamen und die entsprechenden Beschreibungen zum Erstellen einer Blobausgabe.

| Eigenschaftenname       | BESCHREIBUNG                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Ausgabealias        | Ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an diesen Blobspeicher weiterzuleiten. |
| Speicherkonto     | Der Name des Speicherkontos, an das Sie die Ausgabe senden.               |
| Speicherkontoschlüssel | Der geheime Schlüssel, der dem Speicherkonto zugeordnet ist.                              |
| Speichercontainer   | Eine logische Gruppierung für Blobs, die im Azure Blob-Dienst gespeichert sind. Wenn Sie ein Blob in den Blobdienst hochladen, müssen Sie einen Container für das Blob angeben. |
| Pfadmuster | Optional. Das Dateipfadmuster, mit dem Ihre Blobs im angegebenen Container geschrieben werden. <br /><br /> In dem Pfadmuster können Sie mindestens eine Instanz der date- und time-Variablen verwenden, um die Häufigkeit anzugeben, mit der Blobs geschrieben werden: <br /> {date}, {time} <br /><br />Mithilfe von benutzerdefinierter Blob-Partitionierung können Sie einen benutzerdefinierten {field}-Namen aus Ihren Ereignissen angeben, um Blobs zu partitionieren. Der Feldname ist alphanumerisch und kann Leerstellen, Bindestriche und Unterstriche enthalten. Für benutzerdefinierte Felder gelten die folgenden Einschränkungen: <ul><li>Bei Feldnamen wird nicht zwischen Groß- und Kleinschreibung unterschieden. Der Dienst kann z. B. nicht zwischen Spalte „ID“ und Spalte „id“ unterscheiden.</li><li>Geschachtelte Felder sind nicht zulässig. Verwenden Sie stattdessen einen Alias in der Auftragsabfrage zum Vereinfachen des Felds.</li><li>Ausdrücke können nicht als Feldname verwendet werden.</li></ul> <br />Diese Funktion ermöglicht die Verwendung von Konfigurationen für benutzerdefinierte Angaben des Datums-/Uhrzeitformats im Pfad. Benutzerdefinierte Datums- und Uhrzeitformate müssen einzeln nacheinander angegeben werden und in das Schlüsselwort {datetime:\<Spezifizierer>} eingeschlossen sein. Zulässige Eingaben für \<Spezifizierer> sind „yyyy“, „MM“, „M“, „dd“, „d“, „HH“, „H“, „mm“, „m“, „ss“ und „s“. Das Schlüsselwort {datetime:\<Spezifizierer>} kann mehrmals im Pfad verwendet werden, um benutzerdefinierte Konfigurationen für Datum und Uhrzeit zu bilden. <br /><br />Beispiele: <ul><li>Beispiel 1: cluster1/logs/{date}/{time}</li><li>Beispiel 2: cluster1/logs/{date}</li><li>Beispiel 3: cluster1/{client_id}/{date}/{time}</li><li>Beispiel 4: cluster1/{datetime:ss}/{myField}; hierbei lautet die Abfrage: SELECT data.myField AS myField FROM Input;</li><li>Beispiel 5: cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />Der Zeitstempel der erstellten Ordnerstruktur folgt der UTC, nicht der lokalen Zeit.<br /><br />Die Dateibenennung verwendet die nachstehende Konvention: <br /><br />{Präfixmuster des Pfads}/schemaHashcode_Guid_Number.extension<br /><br />Beispielausgabedateien:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />Weitere Informationen zu diesem Feature finden Sie unter [Azure Stream Analytics – benutzerdefinierte Ausgabepartitionierung von Blobs](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Datumsformat | Optional. Wenn das date-Token im Pfadpräfix verwendet wird, können Sie das Datumsformat auswählen, unter dem die Dateien gespeichert werden. Beispiel: YYYY/MM/DD |
| Zeitformat | Optional. Wenn das time-Token im Pfadpräfix verwendet wird, können Sie das Zeitformat auswählen, unter dem die Dateien gespeichert werden. Der einzige derzeit unterstützte Wert ist HH |
| Ereignisserialisierungsformat | Das Serialisierungsformat für Ausgabedaten. Es werden JSON, CSV und Avro unterstützt. |
| Codieren    | Bei Verwendung des CSV- oder JSON-Formats muss eine Codierung angegeben werden. UTF-8 ist derzeit das einzige unterstützte Codierungsformat. |
| Trennzeichen   | Gilt nur für die CSV-Serialisierung. Stream Analytics unterstützt eine Reihe von üblichen Trennzeichen zum Serialisieren der CSV-Daten. Unterstützte Werte sind Komma, Semikolon, Leerzeichen, Tabstopp und senkrechter Strich. |
| Format      | Gilt nur für die JSON-Serialisierung. **Separate Zeile** gibt an, dass die Ausgabe so formatiert wird, dass jedes JSON-Objekt in einer neuen Zeile enthalten ist. **Array** gibt an, dass die Ausgabe als Array aus JSON-Objekten formatiert wird. Dieses Array wird nur geschlossen, wenn der Auftrag beendet wird oder Stream Analytics mit dem nächsten Zeitfenster fortfährt. Im Allgemeinen ist es besser, in separaten Zeilen geschriebenen JSON-Code zu verwenden, da er keine spezielle Behandlung erfordert, während noch in die Ausgabedatei geschrieben wird. |

Wenn Sie Blobspeicher als Ausgabe verwenden, wird in den folgenden Fällen eine neue Datei im Blob erstellt:

* Wenn die Datei die maximale Anzahl von zulässigen Blöcken (derzeit 50.000) überschreitet. Sie können die maximale zulässige Anzahl von Blöcken erreichen, ohne die maximal zulässige Blobgröße zu erreichen. Wenn die Ausgaberate z.B. hoch ist, können Sie mehr Bytes pro Block vorhanden sein, und die Datei ist größer. Wenn die Ausgaberate niedrig ist, weist jeder Block weniger Daten auf, und die Datei ist kleiner.
* Wenn es eine Schemaänderung in der Ausgabe gibt und das Ausgabeformat ein festes Schema erfordert (CSV und Avro).
* Wenn ein Auftrag neu gestartet wird, entweder extern, weil er von einem Benutzer beendet und gestartet wurde, oder intern zu Systemwartungs- oder Fehlerbehebungszwecken.
* Wenn die Abfrage vollständig partitioniert ist und für jede Ausgabepartition eine neue Datei erstellt wird.
* Wenn der Benutzer eine Datei oder einen Container des Speicherkontos löscht.
* Wenn die Ausgabe zeitpartitioniert ist, indem das Pfadpräfixmuster und ein neues Blob verwendet wird, wenn die Abfrage zur nächsten Stunde wechselt.
* Wenn die Ausgabe über ein benutzerdefiniertes Feld partitioniert und pro Partitionsschlüssel ein neues Blob erstellt wird, falls es noch nicht vorhanden ist.
* Falls die Ausgabe über ein benutzerdefiniertes Feld partitioniert wird, für das die Kardinalität des Partitionsschlüssels den Wert 8,000 übersteigt und pro Partitionsschlüssel ein neues Blob erstellt wird.

## <a name="event-hubs"></a>Event Hubs
Der Dienst [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) ist ein hoch skalierbarer Veröffentlichen-Abonnieren-Ereignisingestor. Er kann mehrere Millionen Ereignisse pro Sekunde erfassen. Eine Verwendungsmöglichkeit eines Event Hubs als Ausgabe ergibt sich, wenn die Ausgabe eines Stream Analytics-Auftrags zur Eingabe eines anderen Streamingauftrags wird.

Sie benötigen einige Parameter, um Datenströme von Event Hubs als Ausgabe zu konfigurieren.

| Eigenschaftenname | BESCHREIBUNG |
| --- | --- |
| Ausgabealias | Ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an diesen Event Hub weiterzuleiten. |
| Event Hub-Namespace |Ein Container für einen Satz von Nachrichtenentitäten. Sie haben bei der Erstellung eines neuen Event Hubs auch einen Event Hub-Namespace erstellt. |
| Event Hub-Name | Der Name Ihrer Event Hub-Ausgabe. |
| Event Hub-Richtlinienname | Die Richtlinie für den gemeinsamen Zugriff, die Sie auf der Registerkarte **Konfigurieren** des Event Hubs erstellen können. Jede SAS-Richtlinie umfasst einen Namen, die von Ihnen festgelegten Berechtigungen und Zugriffsschlüssel. |
| Event Hub-Richtlinienschlüssel | Der Schlüssel für den gemeinsamen Zugriff, der für die Authentifizierung des Zugriffs auf den Event Hub-Namespace verwendet wird. |
| Partitionsschlüsselspalte | Optional. Eine Spalte, die den Partitionsschlüssel für die Event Hub-Ausgabe enthält. |
| Ereignisserialisierungsformat | Das Serialisierungsformat für Ausgabedaten. Es werden JSON, CSV und Avro unterstützt. |
| Codieren | Bei CSV und JSON ist UTF-8 gegenwärtig das einzige unterstützte Codierungsformat. |
| Trennzeichen | Gilt nur für die CSV-Serialisierung. Stream Analytics unterstützt eine Reihe von üblichen Trennzeichen zum Serialisieren der Daten im CSV-Format. Unterstützte Werte sind Komma, Semikolon, Leerzeichen, Tabstopp und senkrechter Strich. |
| Format | Gilt nur für die JSON-Serialisierung. **Separate Zeile** gibt an, dass die Ausgabe so formatiert wird, dass jedes JSON-Objekt in einer neuen Zeile enthalten ist. **Array** gibt an, dass die Ausgabe als Array aus JSON-Objekten formatiert wird. Dieses Array wird nur geschlossen, wenn der Auftrag beendet wird oder Stream Analytics mit dem nächsten Zeitfenster fortfährt. Im Allgemeinen ist es besser, in separaten Zeilen geschriebenen JSON-Code zu verwenden, da er keine spezielle Behandlung erfordert, während noch in die Ausgabedatei geschrieben wird. |
| Eigenschaftenspalten | Optional. Durch Komma getrennte Spalten, die anstelle der Nutzlast als Benutzereigenschaften der ausgehenden Nachricht angefügt werden müssen. Weitere Informationen zu diesem Feature finden Sie im Abschnitt [Benutzerdefinierte Metadateneigenschaften für die Ausgabe](#custom-metadata-properties-for-output). |

## <a name="power-bi"></a>Power BI
Sie können [Power BI](https://powerbi.microsoft.com/) als Ausgabe für einen Stream Analytics-Auftrag verwenden, um eine umfassende Visualisierungsumgebung für die Analyseergebnisse bereitzustellen. Diese Funktionalität kann für Vorgangsdashboards, die Erstellung von Berichten und eine metrikgesteuerte Berichterstellung verwendet werden.

Die Power BI-Ausgabe aus Stream Analytics ist zurzeit nicht in den Regionen Azure China (21Vianet) und Azure Deutschland (T-Systems International) verfügbar.

### <a name="authorize-a-power-bi-account"></a>Autorisieren eines Power BI-Kontos
1. Wenn Power BI im Azure-Portal als Ausgabe ausgewählt ist, werden Sie aufgefordert, einen vorhandenen Power BI-Benutzer zu autorisieren oder ein neues Power BI-Konto zu erstellen.
   
   ![Autorisieren von Power BI-Benutzern für die Ausgabekonfiguration](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)

2. Erstellen Sie ein neues Konto, wenn Sie noch keines haben, und wählen Sie dann **Jetzt autorisieren** aus. Die folgende Seite wird angezeigt:
   
   ![Authentifizieren bei Power BI über ein Azure-Konto](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)

3. Geben Sie das Geschäfts-, Schul- oder Unikonto für die Autorisierung der Power BI-Ausgabe an. Wenn Sie noch nicht für Power BI angemeldet sind, wählen Sie **Jetzt anmelden** aus. Das für Power BI verwendete Geschäfts- Schul- oder Unikonto kann sich vom Azure-Abonnementkonto unterscheiden, mit dem Sie jetzt angemeldet sind.

### <a name="configure-the-power-bi-output-properties"></a>Konfigurieren der Eigenschaften der Power BI-Ausgabe
Nachdem Sie das Power BI-Konto authentifiziert haben, können Sie die Eigenschaften für die Power BI-Ausgabe konfigurieren. In der folgenden Tabelle sind Eigenschaftsnamen und deren Beschreibungen aufgeführt, um Ihre Power BI-Ausgabe zu konfigurieren.

| Eigenschaftenname | BESCHREIBUNG |
| --- | --- |
| Ausgabealias |Stellen Sie einen Anzeigenamen bereit, der in Abfragen verwendet wird, um die Abfrageausgabe an diese Power BI-Ausgabe weiterzuleiten. |
| Gruppenarbeitsbereich |Um die gemeinsame Datennutzung mit anderen Power BI-Benutzern zu ermöglichen, können Sie Gruppen in Ihrem Power BI-Konto auswählen. Wählen Sie alternativ **Mein Arbeitsbereich**, wenn Sie nicht in eine Gruppe schreiben möchten. Zum Aktualisieren einer vorhandenen Gruppe muss die Power BI-Authentifizierung erneuert werden. |
| Datasetname |Geben Sie einen Datasetnamen an, den die Power BI-Ausgabe verwenden soll. |
| Tabellenname |Geben Sie einen Tabellennamen unter dem Dataset der Power BI-Ausgabe ein. Derzeit darf die Power BI-Ausgabe von Stream Analytics-Aufträgen nur eine Tabelle pro Dataset aufweisen. |

Eine Schritt-für-Schritt-Anleitung zum Konfigurieren einer Power BI-Ausgabe und eines Power BI-Dashboards erhalten Sie im Artikel [Azure Stream Analytics und Power BI](stream-analytics-power-bi-dashboard.md).

> [!NOTE]
> Erstellen Sie das Dataset und die Tabelle nicht explizit im Power BI-Dashboard. Das Dataset und die Tabelle werden automatisch mit Daten aufgefüllt, wenn der Auftrag gestartet wird und damit beginnt, Ausgaben an Power BI weiterzuleiten. Wenn die Auftragsabfrage keine Ergebnisse generiert, werden das Dataset und die Tabelle nicht erstellt. Wenn Power BI bereits über ein Dataset und eine Tabelle mit demselben Namen verfügt, der in diesem Stream Analytics-Auftrag angegeben wurde, werden die vorhandenen Daten überschrieben.
>

### <a name="create-a-schema"></a>Erstellen eines Schemas
Azure Stream Analytics erstellt für den Benutzer ein Power BI-Dataset und ein Tabellenschema, falls diese noch nicht vorhanden sind. In allen anderen Fällen wird die Tabelle mit neuen Werten aktualisiert. Derzeit kann innerhalb eines Datasets nur eine Tabelle vorhanden sein. 

Power BI verwendet die FIFO-Aufbewahrungsrichtlinie (First In, First Out). Daten werden in einer Tabelle gesammelt, bis diese 200.000 Zeilen erreicht.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Konvertieren eines Datentyps aus Stream Analytics in Power BI
Azure Stream Analytics aktualisiert das Datenmodell dynamisch zur Laufzeit, wenn sich das Ausgabeschema ändert. Nachverfolgt werden Änderungen an Spaltennamen und -typen sowie das Hinzufügen/Entfernen von Spalten.

Die folgende Tabelle enthält die Datentypkonvertierungen von [Stream Analytics-Datentypen](https://msdn.microsoft.com/library/azure/dn835065.aspx) in [EDM-Typen (Entity Data Model)](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) von Power BI für den Fall, dass noch kein Power BI-Dataset und noch keine Tabelle vorhanden sind.

Quelle: Stream Analytics | Ziel: Power BI
-----|-----
bigint | Int64
nvarchar(max) | Zeichenfolge
datetime | Datetime
Gleitkommawert | Double
Datensatzarray | Zeichenfolgentyp, Konstantenwert „IRecord“ oder „IArray“

### <a name="update-the-schema"></a>Aktualisieren des Schemas
Stream Analytics leitet das Datenmodellschema vom ersten Ereignissatz in der Ausgabe ab. Später wird das Datenmodellschema bei Bedarf aktualisiert, um eingehende Ereignisse zu berücksichtigen, die unter Umständen nicht in das ursprüngliche Schema passen.

Vermeiden Sie die `SELECT *`-Abfrage, um zeilenübergreifende dynamische Schemaaktualisierungen zu verhindern. Neben einer möglichen Beeinträchtigung der Leistung ist möglicherweise auch der Zeitaufwand für die Ergebnisse ungewiss. Wählen Sie die genauen Felder aus, die auf dem Power BI-Dashboard angezeigt werden sollen. Außerdem müssen die Datenwerte mit dem ausgewählten Datentyp kompatibel sein.


Vorher/Aktuell | Int64 | Zeichenfolge | DateTime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | Zeichenfolge | Zeichenfolge | Double
Double | Double | Zeichenfolge | Zeichenfolge | Double
Zeichenfolge | Zeichenfolge | Zeichenfolge | Zeichenfolge | Zeichenfolge 
Datetime | Zeichenfolge | Zeichenfolge |  Datetime | Zeichenfolge


### <a name="renew-power-bi-authorization"></a>Erneuern der Power BI-Autorisierung
Wenn sich das Kennwort Ihres Power BI-Kontos ändert, nachdem der Stream Analytics-Auftrag erstellt oder zuletzt authentifiziert wurde, müssen Sie Stream Analytics erneut authentifizieren. Wenn Azure Multi-Factor Authentication auf Ihrem Azure Active Directory (Azure AD)-Mandanten konfiguriert ist, müssen Sie die Power BI-Autorisierung ebenfalls alle zwei Wochen erneuern. Dieses Problem zeigt sich daran, dass keine Auftragsausgabe erfolgt und in den Vorgangsprotokollen ein Benutzerauthentifizierungsfehler angezeigt wird:

  ![Fehler beim Authentifizieren von Benutzern bei Power BI](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)

Um dieses Problem zu beheben, halten Sie den laufenden Auftrag an, und wechseln Sie zur Power BI-Ausgabe. Wählen Sie den Link **Autorisierung erneuern**, und starten Sie den Auftrag ab **Letzte Beendigungszeit** neu, um Datenverlust zu vermeiden.

  ![Erneuern der Power BI-Autorisierung für die Ausgabe](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)

## <a name="table-storage"></a>Table Storage
[Azure-Tabellenspeicher](../storage/common/storage-introduction.md) bietet einen hoch verfügbaren, in hohem Maße skalierbaren Speicher, sodass eine Anwendung automatisch an die Bedürfnisse der Benutzer angepasst werden kann. Tabellenspeicher ist Microsofts NoSQL-Schlüssel-/Attributspeicher, der für strukturierte Daten genutzt werden kann, die weniger Einschränkungen hinsichtlich des Schemas aufweisen. Azure-Tabellenspeicher kann zum Speichern von Daten für dauerhafte Archivierung und effizienten Abruf verwendet werden.

Die folgende Tabelle enthält die Eigenschaftennamen und die entsprechenden Beschreibungen zum Erstellen einer Tabellenausgabe.

| Eigenschaftenname | BESCHREIBUNG |
| --- | --- |
| Ausgabealias |Ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an diesen Tabellenspeicher weiterzuleiten. |
| Speicherkonto |Der Name des Speicherkontos, an das Sie die Ausgabe senden. |
| Speicherkontoschlüssel |Der Zugriffsschlüssel, der dem Speicherkonto zugeordnet ist. |
| Tabellenname |Der Name der Tabelle. Die Tabelle wird erstellt, wenn sie nicht vorhanden ist. |
| Partitionsschlüssel |Der Name der Ausgabespalte, die den Partitionsschlüssel enthält. Der Partitionsschlüssel ist ein eindeutiger Bezeichner für die Partition innerhalb einer Tabelle, die den ersten Teil des Primärschlüssels einer Entität bildet. Dabei handelt es sich um einen Zeichenfolgenwert, der bis zu 1 KB groß sein kann. |
| Zeilenschlüssel |Der Name der Ausgabespalte, die den Zeilenschlüssel enthält. Der Zeilenschlüssel ist ein eindeutiger Bezeichner für eine Entität innerhalb einer Partition. Sie bildet den zweiten Teil des Primärschlüssels einer Entität. Der Zeilenschlüssel ist ein Zeichenfolgenwert, der bis zu 1 KB groß sein kann. |
| Batchgröße |Dies ist die Anzahl von Datensätzen für einen Batchvorgang. Der Standardwert (100) ist für die meisten Aufträge ausreichend. Weitere Details zur Änderung dieser Einstellung finden Sie in der [TableBatchOperation-Spezifikation](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx). |

## <a name="service-bus-queues"></a>Service Bus-Warteschlangen
[Service Bus-Warteschlangen](https://msdn.microsoft.com/library/azure/hh367516.aspx) bieten eine FIFO-Nachrichtenzustellung (First In, First Out) an einen Consumer oder an mehrere konkurrierende Consumer. In der Regel werden Nachrichten von den Empfängern in der zeitlichen Reihenfolge empfangen und verarbeitet, in der sie zur Warteschlange hinzugefügt wurden. Jede Nachricht wird von nur einem Nachrichtenconsumer empfangen und verarbeitet.

Die folgende Tabelle enthält die Eigenschaftennamen und die entsprechenden Beschreibungen zum Erstellen einer Warteschlangenausgabe.

| Eigenschaftenname | BESCHREIBUNG |
| --- | --- |
| Ausgabealias |Ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an diese Service Bus-Warteschlange weiterzuleiten. |
| Service Bus-Namespace |Ein Container für einen Satz von Nachrichtenentitäten. |
| Warteschlangenname |Der Name der Service Bus-Warteschlange. |
| Name der Warteschlangenrichtlinie |Beim Erstellen einer Warteschlange können Sie auf der Registerkarte **Konfigurieren**der Warteschlange entsprechende Richtlinien für den gemeinsamen Zugriff erstellen. Jede SAS-Richtlinie umfasst einen Namen, die von Ihnen festgelegten Berechtigungen und Zugriffsschlüssel. |
| Schlüssel der Warteschlangenrichtlinie |Der Schlüssel für den gemeinsamen Zugriff, der für die Authentifizierung des Zugriffs auf den Service Bus-Namespace verwendet wird. |
| Ereignisserialisierungsformat |Das Serialisierungsformat für Ausgabedaten. Es werden JSON, CSV und Avro unterstützt. |
| Codieren |Bei CSV und JSON ist UTF-8 gegenwärtig das einzige unterstützte Codierungsformat. |
| Trennzeichen |Gilt nur für die CSV-Serialisierung. Stream Analytics unterstützt eine Reihe von üblichen Trennzeichen zum Serialisieren der Daten im CSV-Format. Unterstützte Werte sind Komma, Semikolon, Leerzeichen, Tabstopp und senkrechter Strich. |
| Format |Gilt nur für den JSON-Typ. **Separate Zeile** gibt an, dass die Ausgabe so formatiert wird, dass jedes JSON-Objekt in einer neuen Zeile enthalten ist. **Array** gibt an, dass die Ausgabe als Array aus JSON-Objekten formatiert wird. |
| Eigenschaftenspalten | Optional. Durch Komma getrennte Spalten, die anstelle der Nutzlast als Benutzereigenschaften der ausgehenden Nachricht angefügt werden müssen. Weitere Informationen zu diesem Feature finden Sie im Abschnitt [Benutzerdefinierte Metadateneigenschaften für die Ausgabe](#custom-metadata-properties-for-output). |

Die Anzahl der Partitionen [basiert auf der Service Bus-SKU und -Größe](../service-bus-messaging/service-bus-partitioning.md). Ein Partitionsschlüssel gibt einen eindeutigen ganzzahligen Wert für jede Partition an.

## <a name="service-bus-topics"></a>Service Bus-Themen
Service Bus-Warteschlangen bieten eine 1:1-Kommunikationsmethode vom Sender zum Empfänger. [Service Bus-Themen](https://msdn.microsoft.com/library/azure/hh367516.aspx) bieten eine 1:N-Kommunikationsmethode.

Die folgende Tabelle enthält die Eigenschaftennamen und die entsprechenden Beschreibungen zum Erstellen einer Themenausgabe.

| Eigenschaftenname | BESCHREIBUNG |
| --- | --- |
| Ausgabealias |Ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an dieses Service Bus-Thema weiterzuleiten. |
| Service Bus-Namespace |Ein Container für einen Satz von Nachrichtenentitäten. Sie haben bei der Erstellung eines neuen Event Hubs auch einen Service Bus-Namespace erstellt. |
| Themenname |Themen sind Messagingentitäten, vergleichbar mit Event Hubs und Warteschlangen. Sie wurden für die Erfassung von Ereignisstreams von Geräten und Diensten entwickelt. Wenn ein Thema erstellt wird, wird ihm auch ein bestimmter Name zugewiesen. Die an ein Thema gesendeten Nachrichten sind nur verfügbar, wenn ein Abonnement erstellt wurde. Stellen Sie daher sicher, dass es mindestens ein Abonnement unter dem Thema gibt. |
| Name der Themenrichtlinie |Beim Erstellen eines Themas können Sie auf der Registerkarte **Konfigurieren** des Themas entsprechende Richtlinien für den gemeinsamen Zugriff erstellen. Jede SAS-Richtlinie umfasst einen Namen, die von Ihnen festgelegten Berechtigungen und Zugriffsschlüssel. |
| Schlüssel der Themenrichtlinie |Der Schlüssel für den gemeinsamen Zugriff, der für die Authentifizierung des Zugriffs auf den Service Bus-Namespace verwendet wird. |
| Ereignisserialisierungsformat |Das Serialisierungsformat für Ausgabedaten. Es werden JSON, CSV und Avro unterstützt. |
| Codieren |Bei Verwendung des CSV- oder JSON-Formats muss eine Codierung angegeben werden. UTF-8 ist derzeit das einzige unterstützte Codierungsformat. |
| Trennzeichen |Gilt nur für die CSV-Serialisierung. Stream Analytics unterstützt eine Reihe von üblichen Trennzeichen zum Serialisieren der Daten im CSV-Format. Unterstützte Werte sind Komma, Semikolon, Leerzeichen, Tabstopp und senkrechter Strich. |
| Eigenschaftenspalten | Optional. Durch Komma getrennte Spalten, die anstelle der Nutzlast als Benutzereigenschaften der ausgehenden Nachricht angefügt werden müssen. Weitere Informationen zu diesem Feature finden Sie im Abschnitt [Benutzerdefinierte Metadateneigenschaften für die Ausgabe](#custom-metadata-properties-for-output). |

Die Anzahl der Partitionen [basiert auf der Service Bus-SKU und -Größe](../service-bus-messaging/service-bus-partitioning.md). Der Partitionsschlüssel gibt einen eindeutigen ganzzahligen Wert für jede Partition an.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) ist ein global verteilter Datenbankdienst, der eine grenzenlose elastische Skalierung rund um den Globus, umfangreiche Abfragen und automatische Indizierung über schemaunabhängige Datenmodelle bietet. Weitere Informationen zu den Azure Cosmos DB-Sammlungsoptionen für Stream Analytics finden Sie im Artikel [Azure Stream Analytics mit Azure Cosmos DB als Ausgabe](stream-analytics-documentdb-output.md).

Die Azure Cosmos DB-Ausgabe aus Stream Analytics ist zurzeit nicht in den Regionen Azure China (21Vianet) und Azure Deutschland (T-Systems International) verfügbar.

> [!Note]
> Derzeit unterstützt Azure Stream Analytics nur die Verbindung mit Azure Cosmos DB über die SQL-API.
> Andere Azure Cosmos DB-APIs werden noch nicht unterstützt. Wenn Sie Azure Stream Analytics auf die mit anderen APIs erstellten Azure Cosmos DB-Konten verweisen, werden die Daten unter Umständen nicht richtig gespeichert.

In der folgenden Tabelle werden die Eigenschaften zum Erstellen einer Azure Cosmos DB-Ausgabe beschrieben.

| Eigenschaftenname | BESCHREIBUNG |
| --- | --- |
| Ausgabealias | Ein Alias zum Verweisen auf diese Ausgabe in Ihrer Stream Analytics-Abfrage. |
| Senke | Azure Cosmos DB. |
| Importoption | Wählen Sie entweder **Cosmos DB über das eigene Abonnement auswählen** oder **Cosmos DB-Einstellungen manuell festlegen** aus.
| Konto-ID | Der Name oder Endpunkt-URI des Azure Cosmos DB-Kontos. |
| Kontoschlüssel | Der Schlüssel für den gemeinsamen Zugriff für das Azure Cosmos DB-Konto. |
| Datenbank | Der Name der Azure Cosmos DB-Datenbank. |
| Muster des Sammlungsnamen | Der Sammlungsname oder das Muster für die zu verwendenden Sammlungen. <br />Sie können das Sammlungsnamenformat mit dem optionalen Token {partition} bilden, wobei Partitionen bei 0 beginnen. Zwei Beispiele:  <br /><ul><li> _MyCollection_: Eine Sammlung mit dem Namen „MyCollection“ muss vorhanden sein.</li>  <li> _MyCollection{partition}_: Basierend auf der Partitionierungsspalte.</li></ul> Die Sammlungen der Partitionierungsspalten müssen vorhanden sein: „MyCollection0,“ „MyCollection1,“ „MyCollection2,“ usw. |
| Partitionsschlüssel | Optional. Dies ist nur erforderlich, wenn Sie ein {partition}-Token in Ihrem Namensmuster der Sammlung verwenden.<br /> Der Partitionierungsschlüssel ist der Name des Felds in Ausgabeereignissen, das zur Angabe des Schlüssels für die sammlungsübergreifende Partitionierung der Ausgabe verwendet wird.<br /> Für die Ausgabe einer einzelnen Sammlung können Sie eine beliebige Ausgabespalte verwenden. Ein Beispiel ist „PartitionId“. |
| Dokument-ID |Optional. Der Name des Felds in Ausgabeereignissen, das zur Angabe des Primärschlüssels verwendet wird, auf dem Einfüge- und Aktualisierungsvorgänge basieren.

## <a name="azure-functions"></a>Azure-Funktionen
Azure Functions ist ein serverloser Computedienst, mit dem Sie Code bedarfsgesteuert ausführen können, ohne eine explizite Infrastruktur bereitstellen oder verwalten zu müssen. Mit diesem Dienst können Sie Codes implementieren, die durch in Azure- oder Partnerdiensten auftretende Ereignisse ausgelöst werden. Aufgrund der Möglichkeit, auf Trigger zu antworten, ist Azure Functions die ideale Ausgabe für Azure Stream Analytics. Mithilfe dieses Ausgabeadapters können Benutzer eine Verbindung zwischen Stream Analytics und Azure Functions herstellen und als Reaktion auf verschiedenste Ereignisse ein Skript oder einen Codeausschnitt ausführen.

Die Azure Functions-Ausgabe aus Stream Analytics ist zurzeit nicht in den Regionen Azure China (21Vianet) und Azure Deutschland (T-Systems International) verfügbar.

Azure Stream Analytics ruft Azure Functions über HTTP-Trigger auf. Der Azure Functions-Ausgabeadapter wird mit folgenden konfigurierbaren Eigenschaften zur Verfügung gestellt:

| Eigenschaftenname | BESCHREIBUNG |
| --- | --- |
| Funktionen-App |Der Name der Azure Functions-App. |
| Funktion |Der Name der Funktion in der Azure Functions-App. |
| Schlüssel |Wenn Sie eine Azure-Funktion aus einem anderen Abonnement verwenden möchten, können Sie dazu den Schlüssel für den Zugriff auf Ihre Funktion angeben. |
| Max Batch Size |Eine Eigenschaft, mit der Sie die maximale Größe für jeden Ausgabebatch festlegen können, der an Ihre Azure-Funktion gesendet wird. Die Eingabeeinheit ist Bytes. Standardmäßig ist dieser Wert auf 262,144 Bytes (256 KB) festgelegt. |
| Max Batch Count  |Eine Eigenschaft, mit der Sie die maximale Anzahl von Ereignissen in jedem Batch angeben können, die an Azure Functions gesendet werden. Der Standardwert ist 100. |

Die Größe der an Azure Functions gesendeten Batches wird verringert, wenn in Azure Stream Analytics Ausnahme 413 (HTTP-Anforderungseinheit zu groß) durch Azure Functions auftritt. Verwenden Sie in Ihrem Azure-Funktionscode diese Ausnahme, um sicherzustellen, dass Azure Stream Analytics keine übermäßig großen Batches sendet. Stellen Sie außerdem sicher, dass die Werte für die maximal zulässige Batchanzahl und -größe in der Funktion mit den Werten übereinstimmen, die im Stream Analytics-Portal eingegeben wurden.

Zudem wird in Fällen, in denen kein Ereignis in einem Zeitfenster auftritt, keine Ausgabe generiert. Als Ergebnis wird die **computeResult**-Funktion nicht aufgerufen. Dieses Verhalten entspricht den integrierten Aggregatfunktionen im Fenstermodus.

## <a name="custom-metadata-properties-for-output"></a>Benutzerdefinierte Metadateneigenschaften für die Ausgabe 

Sie können Abfragespalten als Benutzereigenschaften an Ihre ausgehenden Nachrichten anfügen. Diese Spalten gelangen nicht in die Nutzlast. Die Eigenschaften liegen in Form eines Wörterbuchs für die Ausgabemeldung vor. *Schlüssel* ist der Spaltenname und der *Wert* ist der Spaltenwert im Eigenschaftenwörterbuch. Alle Stream Analytics-Datentypen werden mit Ausnahme von „Datensatz“ und „Array“ unterstützt.  

Unterstützte Ausgaben: 
* Service Bus-Warteschlange 
* Service Bus-Topic 
* Event Hub 

Im folgenden Beispiel fügen wir die beiden Felder `DeviceId` und `DeviceStatus` zu den Metadaten hinzu. 
* Abfrage: `select *, DeviceId, DeviceStatus from iotHubInput`
* Ausgabekonfiguration: `DeviceId,DeviceStatus`.

![Eigenschaftenspalten](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

Der folgende Screenshot zeigt die Eigenschaften der Ausgabemeldung, die in EventHub durch [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) überprüft wurden.

![Benutzerdefinierte Ereigniseigenschaften](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="partitioning"></a>Partitionierung

In der folgenden Tabelle werden die Partitionsunterstützung und die Anzahl der Ausgabeschreiber für die einzelnen Ausgabetypen zusammengefasst:

| Ausgabetyp | Unterstützung der Partitionierung | Partitionsschlüssel  | Anzahl der Ausgabeschreiber |
| --- | --- | --- | --- |
| Azure Data Lake Store | Ja | Verwenden Sie die {date}- und {time}-Tokens im Pfadpräfixmuster. Wählen Sie ein Datumsformat wie JJJJ/MM/TT, TT/MM/JJJJ oder MM-TT-JJJJ. „HH“ wird für das Uhrzeitformat verwendet. | Hierbei wird die Eingabepartitionierung für [vollständig parallelisierbare Abfragen](stream-analytics-scale-jobs.md) befolgt. |
| Azure SQL-Datenbank | Ja | Basierend auf der PARTITION BY-Klausel in der Abfrage. | Hierbei wird die Eingabepartitionierung für [vollständig parallelisierbare Abfragen](stream-analytics-scale-jobs.md) befolgt. Weitere Informationen zum Erzielen einer höheren Durchsatzleistung beim Laden von Daten in Azure SQL-Datenbank finden Sie unter [Azure Stream Analytics-Ausgabe an Azure SQL-Datenbank](stream-analytics-sql-output-perf.md). |
| Azure Blob Storage | Ja | Verwenden Sie die Token {date} und {time} aus Ihren Ereignisfeldern im Pfadmuster. Wählen Sie ein Datumsformat wie JJJJ/MM/TT, TT/MM/JJJJ oder MM-TT-JJJJ. „HH“ wird für das Uhrzeitformat verwendet. Die Blobausgabe kann durch ein einzelnes benutzerdefiniertes Ereignisattribut {fieldname} oder {datetime:\<Spezifizierer>} partitioniert werden. | Hierbei wird die Eingabepartitionierung für [vollständig parallelisierbare Abfragen](stream-analytics-scale-jobs.md) befolgt. |
| Azure Event Hubs | Ja | Ja | Variiert je nach Partitionsausrichtung.<br /> Wenn der Partitionsschlüssel für die Event Hub-Ausgabe gleichmäßig mit dem (vorherigen) Upstream-Abfrageschritt ausgerichtet ist, entspricht die Anzahl der Writer der Anzahl der Partitionen in der Event Hub-Ausgabe. Jeder Writer verwendet die [EventHubSender-Klasse](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet), um Ereignisse an die jeweilige Partition zu senden. <br /> Wenn der Partitionsschlüssel für die Event Hub-Ausgabe nicht mit dem (vorherigen) Upstream-Abfrageschritt ausgerichtet ist, entspricht die Anzahl der Writer der Anzahl der Partitionen in diesem vorherigen Schritt. Jeder Writer verwendet die [SendBatchAsync-Klasse](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) in **EventHubClient**, um Ereignisse an alle Ausgabepartitionen zu senden. |
| Power BI | Nein  | Keine | Nicht zutreffend |
| Azure-Tabellenspeicher | Ja | Eine beliebige Ausgabespalte.  | Hierbei wird die Eingabepartitionierung für [vollständig parallelisierte Abfragen](stream-analytics-scale-jobs.md) befolgt. |
| Azure Service Bus-Thema | Ja | Wird automatisch ausgewählt. Die Anzahl der Partitionen basiert auf der [Service Bus-SKU und -Größe](../service-bus-messaging/service-bus-partitioning.md). Der Partitionsschlüssel gibt einen eindeutigen ganzzahligen Wert für jede Partition an.| Entspricht der Anzahl von Partitionen im Ausgabethema.  |
| Azure Service Bus-Warteschlange | Ja | Wird automatisch ausgewählt. Die Anzahl der Partitionen basiert auf der [Service Bus-SKU und -Größe](../service-bus-messaging/service-bus-partitioning.md). Der Partitionsschlüssel gibt einen eindeutigen ganzzahligen Wert für jede Partition an.| Entspricht der Anzahl von Partitionen in der Ausgabewarteschlange. |
| Azure Cosmos DB | Ja | Verwenden Sie das {partition}-Token im Sammlungsnamensmuster. Der {partition}-Wert basiert auf der PARTITION BY-Klausel in der Abfrage. | Hierbei wird die Eingabepartitionierung für [vollständig parallelisierte Abfragen](stream-analytics-scale-jobs.md) befolgt. |
| Azure-Funktionen | Nein  | Keine | Nicht zutreffend |

Wenn der Ausgabeadapter nicht partitioniert ist, führt das Fehlen von Daten in einer Eingabepartition zu einer Verzögerung, bis die Zeitspanne für die Eingangsverzögerung verstrichen ist. In solchen Fällen wird die Ausgabe in einen einzigen Writer zusammengeführt, was Engpässe in Ihrer Pipeline verursachen kann. Weitere Informationen zur Richtlinie bei Eingangsverzögerung finden Sie unter [Überlegungen zur Ereignisreihenfolge in Azure Stream Analytics](stream-analytics-out-of-order-and-late-events.md).

## <a name="output-batch-size"></a>Ausgabebatchgröße
Für Azure Stream Analytics werden Batches mit variabler Größe verwendet, um Ereignisse zu verarbeiten und in Ausgaben zu schreiben. Normalerweise schreibt das Stream Analytics-Modul nicht nur jeweils eine Nachricht, sondern Batches, um die Effizienz zu steigern. Wenn die Rate der eingehenden und ausgehenden Ereignisse hoch ist, verwendet Stream Analytics größere Batches. Falls die Ausgangsrate niedrig ist, werden kleinere Batches genutzt, um die Latenz gering zu halten.

In der folgenden Tabelle sind einige Aspekte von Ausgabebatches beschrieben:

| Ausgabetyp | Maximale Nachrichtengröße | Optimierung der Batchgröße |
| :--- | :--- | :--- |
| Azure Data Lake Store | Siehe [Grenzwerte für Data Lake Store](../azure-subscription-service-limits.md#data-lake-store-limits). | Verwenden Sie bis zu 4 MB pro Schreibvorgang. |
| Azure SQL-Datenbank | Maximal 10.000 Zeilen pro Masseneinfügen-Vorgang.<br />Mindestens 100 Zeilen pro Masseneinfügen-Vorgang. <br />Siehe [Einschränkungen für Azure SQL-Datenbank](../sql-database/sql-database-resource-limits.md). |  Jeder Batch wird zunächst mit maximaler Batchgröße als Massenvorgang eingefügt. Sie können den Batch in zwei Hälften teilen (bis zur minimalen Batchgröße), basierend auf wiederholbaren Fehlern von SQL. |
| Azure Blob Storage | Siehe [Azure Storage-Grenzwerte](../azure-subscription-service-limits.md#storage-limits). | Die maximale Größe von Blobblöcken beträgt 4 MB.<br />Die maximale Anzahl von Blobblöcken beträgt 50.000. |
| Azure Event Hubs  | 256 KB pro Nachricht. <br />Siehe [Event Hubs-Grenzwerte](../event-hubs/event-hubs-quotas.md). |  Wenn die E/A-Partitionierung nicht aneinander ausgerichtet ist, wird jedes Ereignis einzeln in **EventData** verpackt und als Batch gesendet, dessen Größe bis zur maximalen Nachrichtengröße reichen kann (1 MB für Premium-SKU). <br /><br />  Falls die E/A-Partitionierung aneinander ausgerichtet ist, werden mehrere Ereignisse bis zur maximalen Nachrichtengröße in eine einzelne **EventData**-Instanz verpackt und gesendet.  |
| Power BI | Siehe [Einschränkungen für Power BI-REST-API](https://msdn.microsoft.com/library/dn950053.aspx). |
| Azure-Tabellenspeicher | Siehe [Azure Storage-Grenzwerte](../azure-subscription-service-limits.md#storage-limits). | Der Standardwert ist 100 Entitäten pro Einzeltransaktion. Sie können bei Bedarf einen niedrigeren Wert konfigurieren. |
| Azure Service Bus-Warteschlange   | 256 KB pro Nachricht.<br /> Siehe [Service Bus-Grenzwerte](../service-bus-messaging/service-bus-quotas.md). | Verwenden Sie ein einzelnes Ereignis pro Nachricht. |
| Azure Service Bus-Thema | 256 KB pro Nachricht.<br /> Siehe [Service Bus-Grenzwerte](../service-bus-messaging/service-bus-quotas.md). | Verwenden Sie ein einzelnes Ereignis pro Nachricht. |
| Azure Cosmos DB   | Siehe [Einschränkungen für Azure Cosmos DB](../azure-subscription-service-limits.md#azure-cosmos-db-limits). | Die Batchgröße und Schreibfrequenz wird basierend auf den Azure Cosmos DB-Antworten dynamisch angepasst. <br /> Es gelten keine vordefinierten Stream Analytics-Einschränkungen. |
| Azure-Funktionen   | | Die Standardbatchgröße beträgt 262.144 Bytes (256 KB). <br /> Die Standardereignisanzahl pro Batch beträgt 100. <br /> Die Batchgröße ist konfigurierbar und kann in den [Ausgabeoptionen](#azure-functions) von Stream Analytics erhöht oder verringert werden.

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> 
> [Schnellstart: Erstellen eines Stream Analytics-Auftrags mithilfe des Azure-Portals](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
