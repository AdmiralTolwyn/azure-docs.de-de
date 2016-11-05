---
title: 'Stream Analytics-Ausgaben: Optionen für Speicher, Analyse | Microsoft Docs'
description: Erfahren Sie, wie Sie Optionen für Stream Analytics-Datenausgaben einschließlich Power BI für Analyseergebnisse einsetzen.
keywords: Datentransformation, Analyseergebnisse, Datenspeicheroptionen
services: stream-analytics,documentdb,sql-database,event-hubs,service-bus,storage
documentationcenter: ''
author: jeffstokes72
manager: jhubbard
editor: cgronlun

ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 09/26/2016
ms.author: jeffstok

---
# Stream Analytics-Ausgaben: Optionen für Speicher, Analyse
Überlegen Sie beim Erstellen eines Stream Analytics-Auftrags, wie die resultierenden Daten genutzt werden. Wie möchten Sie die Ergebnisse des Stream Analytics-Auftrags anzeigen, und wo möchten Sie sie speichern?

Um verschiedene Anwendungsmuster zu ermöglichen, stellt Azure Stream Analytics verschiedene Optionen zum Speichern der Ausgabe und zum Anzeigen von Analyseergebnissen bereit. Dadurch wird sowohl einfaches Anzeigen der Auftragsausgabe als auch Flexibilität bei der Nutzung und Speicherung der Auftragsausgabe für Data Warehousing und andere Zwecke erreicht. Jede Ausgabe, die im Auftrag konfiguriert wird, muss vorhanden sein, bevor der Auftrag gestartet wird und Ereignisse übertragen werden. Wird beispielsweise ein Blob Storage als Ausgabe verwendet, erstellt der Auftrag nicht automatisch ein Speicherkonto. Es muss vom Benutzer erstellt werden, bevor der ASA-Auftrag gestartet wird.

## Azure Data Lake Store
Stream Analytics unterstützt [Azure Data Lake-Speicher](https://azure.microsoft.com/services/data-lake-store/). Dieser Speicher bietet Ihnen die Möglichkeit, Daten von beliebiger Größe, Art und Erfassungsgeschwindigkeit zur Durchführung operativer und explorativer Analysen zu speichern. Derzeit wird die Erstellung und Konfiguration von Data Lake-Speicherausgaben nur im klassischen Azure-Portal unterstützt. Darüber hinaus muss Stream Analytics autorisiert werden, um auf Data Lake-Speicher zuzugreifen. Informationen zur Autorisierung und wie Sie sich für die Data Lake-Speicher-Vorschau anmelden (falls erforderlich), finden Sie im Artikel zur [Data Lake-Ausgabe](stream-analytics-data-lake-output.md).

### Autorisieren eines Azure Data Lake Store
Wenn Data Lake-Speicherung im Azure-Verwaltungsportal als Ausgabe ausgewählt ist, werden Sie aufgefordert, eine Verbindung mit einem vorhandenen Data Lake Store zu autorisieren.

![Autorisieren von Data Lake-Speicher](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)

Füllen Sie dann die Eigenschaften für die Data Lake Store-Ausgabe wie unten dargestellt aus:

![Autorisieren von Data Lake-Speicher](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)

Die folgende Tabelle enthält die Namen und Beschreibungen der Eigenschaften, die für die Erstellung einer Data Lake-Speicherausgabe erforderlich sind.

<table>
<tbody>
<tr>
<td><B>EIGENSCHAFTENNAME</B></td>
<td><B>BESCHREIBUNG</B></td>
</tr>
<tr>
<td>Ausgabealias</td>
<td>Dies ist ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an diesen Data Lake-Speicher weiterzuleiten.</td>
</tr>
<tr>
<td>Kontoname</td>
<td>Der Name des Data Lake-Speicherkontos, an das Sie die Ausgabe senden. Ihnen wird eine Dropdown-Liste der Data Lake-Speicherkonten angezeigt, auf die der im Portal angemeldete Benutzer Zugriff hat.</td>
</tr>
<tr>
<td>Präfixmuster des Pfads [<I>optional</I>]</td>
<td>Der Dateipfad, mit dem Ihre Dateien im angegebenen Data Lake-Speicherkonto geschrieben werden. <BR>{date}, {time}<BR>Beispiel 1: folder1/logs / {date} / {time}<BR>Beispiel 2: folder1/logs / {date}</td>
</tr>
<tr>
<td>Datumsformat [<I>optional</I>]</td>
<td>Wenn das date-Token im Pfadpräfix verwendet wird, können Sie das Datumsformat auswählen, unter dem die Dateien gespeichert werden. Beispiel: YYYY/MM/TT</td>
</tr>
<tr>
<td>Zeitformat [<I>optional</I>]</td>
<td>Wenn das time-Token im Pfadpräfix verwendet wird, können Sie das Zeitformat auswählen, unter dem die Dateien gespeichert werden. Der einzige derzeit unterstützte Wert ist HH</td>
</tr>
<tr>
<td>Ereignisserialisierungsformat</td>
<td>Das Serialisierungsformat für Ausgabedaten. Es werden JSON, CSV und Avro unterstützt.</td>
</tr>
<tr>
<td>Codieren</td>
<td>Beim CSV- oder JSON-Format muss eine Codierung angegeben werden. Das einzige derzeit unterstützte Codierungsformat ist UTF-8.</td>
</tr>
<tr>
<td>Trennzeichen</td>
<td>Gilt nur für die CSV-Serialisierung. Stream Analytics unterstützt eine Reihe von üblichen Trennzeichen zum Serialisieren der CSV-Daten. Unterstützte Werte sind Komma, Semikolon, Leerzeichen, Tabulator und senkrechter Strich.</td>
</tr>
<tr>
<td>Format</td>
<td>Gilt nur für die JSON-Serialisierung. "Separate Zeile" gibt an, dass die Ausgabe so formatiert wird, dass jedes JSON-Objekt in einer neuen Zeile enthalten ist. "Array" gibt an, dass die Ausgabe als Array aus JSON-Objekten formatiert wird.</td>
</tr>
</tbody>
</table>

### Erneuern der Data Lake-Speicherautorisierung
Sie müssen Ihr Data Lake Store-Konto erneut authentifizieren, wenn das Kennwort seit der Erstellung oder letzten Authentifizierung Ihres Auftrags geändert wurde.

![Autorisieren von Data Lake-Speicher](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)

## SQL-Datenbank
[Azure SQL-Datenbank](https://azure.microsoft.com/services/sql-database/) kann als Ausgabe für relationale Daten oder für Anwendungen verwendet werden, die auf Inhalten aufsetzen, die in einer relationalen Datenbank gehostet werden. Stream Analytics-Aufträge werden in eine vorhandene Tabelle in einer Azure SQL-Datenbank geschrieben. Beachten Sie, dass das Tabellenschema genau den Feldern und deren Typen entsprechen muss, die aus Ihrem Auftrag ausgegeben werden. Ein [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) kann auch über die SQL-Datenbank-Ausgabeoption als Ausgabe angegeben werden (diese Funktionen steht Ihnen in der Vorschau zur Verfügung). Die folgende Tabelle enthält die Eigenschaftennamen und die entsprechenden Beschreibungen zum Erstellen einer SQL-Datenbank-Ausgabe.

| Eigenschaftenname | Beschreibung |
| --- | --- |
| Ausgabealias |Dies ist ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an diese Datenbank weiterzuleiten. |
| Datenbank |Der Name der Datenbank, an die Sie die Ausgabe senden. |
| Servername |Der Servername der SQL-Datenbank. |
| Benutzername |Der Benutzername, der Schreibzugriff auf die Datenbank hat. |
| Kennwort |Das Kennwort, um eine Verbindung zur Datenbank herzustellen. |
| Tabelle |Der Name der Tabelle, in die die Ausgabe geschrieben wird. Beim Tabellennamen muss die Groß-/Kleinschreibung beachtet werden. Das Schema dieser Tabelle sollte genau der Anzahl Felder und ihrer Typen entsprechen, die von der Auftragsausgabe generiert werden. |

> [!NOTE]
> Zurzeit wird das Azure SQL-Datenbank-Angebot für eine Auftragsausgabe in Stream Analytics unterstützt. Eine Azure-VM mit SQL Server und angefügter Datenbank wird jedoch nicht unterstützt. Dies soll in zukünftigen Versionen geändert werden.
> 
> 

## Blob-Speicher
Blobspeicher bietet eine kostengünstige und skalierbare Lösung zum Speichern von großen Mengen unstrukturierter Daten in der Cloud. Eine Einführung in Azure-Blob-Speicher und dessen Nutzung finden Sie in der Dokumentation unter [Verwenden des Blob-Speichers mit .NET](../storage/storage-dotnet-how-to-use-blobs.md).

Die folgende Tabelle enthält die Eigenschaftennamen und die entsprechenden Beschreibungen zum Erstellen einer Blobausgabe.

<table>
<tbody>
<tr>
<td>EIGENSCHAFTENNAME</td>
<td>BESCHREIBUNG</td>
</tr>
<tr>
<td>Ausgabealias</td>
<td>Dies ist ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an diesen Blobspeicher weiterzuleiten.</td>
</tr>
<tr>
<td>Speicherkonto</td>
<td>Der Name des Speicherkontos, an das Sie die Ausgabe senden.</td>
</tr>
<tr>
<td>Speicherkontoschlüssel</td>
<td>Der geheime Schlüssel, der dem Speicherkonto zugeordnet ist.</td>
</tr>
<tr>
<td>Speichercontainer</td>
<td>Container stellen eine logische Gruppierung für Blobs bereit, die im Microsoft Azure-Blobdienst gespeichert sind. Wenn Sie ein Blob in den Blobdienst hochladen, müssen Sie einen Container für das Blob angeben.</td>
</tr>
<tr>
<td>Präfixmusters des Pfads [optional]</td>
<td>Der Dateipfad, mit dem Ihre Blobs im angegebenen Container geschrieben werden.<BR>In dem Pfad können Sie mindestens eine Instanz der beiden folgenden Variablen verwenden, um die Frequenz anzugeben, in der Blobs geschrieben werden:<BR>{date}, {time}<BR>Beispiel 1: cluster1/logs/{date}/{time}<BR>Beispiel 2: cluster1/logs/{date}</td>
</tr>
<tr>
<td>Datumsformat [optional]</td>
<td>Wenn das date-Token im Pfadpräfix verwendet wird, können Sie das Datumsformat auswählen, unter dem die Dateien gespeichert werden. Beispiel: YYYY/MM/TT</td>
</tr>
<tr>
<td>Zeitformat [optional]</td>
<td>Wenn das time-Token im Pfadpräfix verwendet wird, können Sie das Zeitformat auswählen, unter dem die Dateien gespeichert werden. Der einzige derzeit unterstützte Wert ist HH</td>
</tr>
<tr>
<td>Ereignisserialisierungsformat</td>
<td>Das Serialisierungsformat für Ausgabedaten. Es werden JSON, CSV und Avro unterstützt.</td>
</tr>
<tr>
<td>Codieren</td>
<td>Beim CSV- oder JSON-Format muss eine Codierung angegeben werden. Das einzige derzeit unterstützte Codierungsformat ist UTF-8.</td>
</tr>
<tr>
<td>Trennzeichen</td>
<td>Gilt nur für die CSV-Serialisierung. Stream Analytics unterstützt eine Reihe von üblichen Trennzeichen zum Serialisieren der CSV-Daten. Unterstützte Werte sind Komma, Semikolon, Leerzeichen, Tabulator und senkrechter Strich.</td>
</tr>
<tr>
<td>Format</td>
<td>Gilt nur für die JSON-Serialisierung. "Separate Zeile" gibt an, dass die Ausgabe so formatiert wird, dass jedes JSON-Objekt in einer neuen Zeile enthalten ist. "Array" gibt an, dass die Ausgabe als Array aus JSON-Objekten formatiert wird.</td>
</tr>
</tbody>
</table>

## Event Hub
[Event Hubs](https://azure.microsoft.com/services/event-hubs/) ist ein hoch skalierbarer Veröffentlichen-Abonnieren-Ereigniserfasser. Er kann mehrere Millionen Ereignisse pro Sekunde erfassen. Eine Verwendung eines Event Hubs als Ausgabe ergibt sich, wenn die Ausgabe eines Stream Analytics-Auftrags die Eingabe eines anderen Streamingauftrags ist.

Es gibt einige Parameter, die erforderlich sind, um Event Hub-Datenströme als Ausgabe zu konfigurieren.

| Eigenschaftenname | Beschreibung |
| --- | --- |
| Ausgabealias |Dies ist ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an diesen Event Hub weiterzuleiten. |
| Service Bus- Namespace |Ein Service Bus-Namespace ist ein Container für einen Satz von Nachrichtenentitäten. Sie haben bei der Erstellung eines neuen Event Hubs auch einen Service Bus-Namespace erstellt. |
| Event Hub |Der Name Ihrer Event Hub-Ausgabe. |
| Event Hub-Richtlinienname |Die Richtlinie für den gemeinsamen Zugriff, die auf der Registerkarte "Event Hub-Konfiguration" erstellt werden kann. Jede Richtlinie für den gemeinsamen Zugriff verfügt über einen Namen, die von Ihnen festgelegten Berechtigungen und Zugriffsschlüssel. |
| Event Hub-Richtlinienschlüssel |Der Schlüssel für den gemeinsamen Zugriff, der für die Authentifizierung des Zugriffs auf den Service Bus-Namespace verwendet wird. |
| Partitionsschlüsselspalte [optional] |Diese Spalte enthält den Partitionsschlüssel für die Event Hub-Ausgabe. |
| Ereignisserialisierungsformat |Das Serialisierungsformat für Ausgabedaten. Es werden JSON, CSV und Avro unterstützt. |
| Codieren |Bei CSV und JSON ist UTF-8 gegenwärtig das einzige unterstützte Codierungsformat. |
| Trennzeichen |Gilt nur für die CSV-Serialisierung. Stream Analytics unterstützt eine Reihe von üblichen Trennzeichen zum Serialisieren der Daten im CSV-Format. Unterstützte Werte sind Komma, Semikolon, Leerzeichen, Tabulator und senkrechter Strich. |
| Format |Gilt nur für den JSON-Typ. "Separate Zeile" gibt an, dass die Ausgabe so formatiert wird, dass jedes JSON-Objekt in einer neuen Zeile enthalten ist. "Array" gibt an, dass die Ausgabe als Array aus JSON-Objekten formatiert wird. |

## Power BI
[Power BI](https://powerbi.microsoft.com/) kann als Ausgabe für einen Stream Analytics-Auftrag verwendet werden, um eine umfassende Visualisierungsumgebung für die Analyseergebnisse bereitzustellen. Diese Funktionalität kann für betriebliche Dashboards, die Erstellung von Berichten und eine metrikgesteuerte Berichterstellung verwendet werden.

### Autorisieren eines Power BI-Kontos
1. Wenn Power BI im Azure-Verwaltungsportal als Ausgabe ausgewählt ist, werden Sie aufgefordert, einen vorhandenen Power BI-Benutzer zu autorisieren oder ein neues Power BI-Konto zu erstellen.
   
   ![Autorisieren von Power BI-Benutzern](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)
2. Erstellen Sie ein neues Konto, wenn Sie noch keines haben, und klicken Sie dann auf "Jetzt autorisieren". Es wird ein Bildschirm ähnlich dem folgenden angezeigt.
   
   ![Azure-Konto Power BI](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)
3. In diesem Schritt geben Sie das Geschäfts- oder Schulkonto für die Autorisierung der Power BI-Ausgabe an. Wenn Sie noch nicht für Power  BI angemeldet sind, wählen Sie "Jetzt anmelden" aus. Das für Power BI verwendete Geschäfts- oder Schulkonto kann sich vom Azure-Abonnementkonto unterscheiden, bei dem Sie derzeit angemeldet sind.

### Konfigurieren der Eigenschaften der Power BI-Ausgabe
Sobald Sie das Power BI-Konto authentifiziert haben, können Sie die Eigenschaften für die Power BI-Ausgabe konfigurieren. Die folgende Tabelle enthält eine Liste von Eigenschaftennamen und der entsprechenden Beschreibung zum Konfigurieren der Power BI-Ausgabe.

| Eigenschaftenname | Beschreibung |
| --- | --- |
| Ausgabealias |Dies ist ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an diese Power BI-Ausgabe weiterzuleiten. |
| Gruppenarbeitsbereich |Um die gemeinsame Datennutzung mit anderen Power BI-Benutzern zu ermöglichen, können Sie Gruppen in Ihrem Power BI-Konto auswählen. Wählen Sie alternativ „Arbeitsbereich“, wenn Sie nicht in eine Gruppe schreiben möchten. Zum Aktualisieren einer vorhandenen Gruppe muss die Power BI-Authentifizierung erneuert werden. |
| Datasetname |Geben Sie einen Datasetnamen an, der für die Power BI-Ausgabe verwendet werden soll. |
| Tabellenname |Geben Sie einen Tabellennamen unter dem Dataset der Power BI-Ausgabe ein. Derzeit darf die Power BI-Ausgabe von Stream Analytics-Aufträgen nur eine Tabelle pro Dataset aufweisen. |

Eine schrittweise Anleitung zum Konfigurieren eine Power BI-Ausgabe und eines Power BI-Dashboards erhalten Sie im Artikel [Azure Stream Analytics & Power BI](stream-analytics-power-bi-dashboard.md).

> [!NOTE]
> Erstellen Sie das Dataset und die Tabelle nicht explizit im Power BI-Dashboard. Das Dataset und die Tabelle werden automatisch ausgefüllt, wenn der Auftrag gestartet wird und wenn der Auftrag damit beginnt, Ausgaben in Power BI zu speisen. Wenn die Auftragsabfrage keine Ergebnisse generiert, werden die Datasets und die Tabelle nicht erstellt. Wenn Power BI bereits über ein Dataset und eine Tabelle mit demselben Namen verfügt, der in diesem Stream Analytics-Auftrag angegeben wurde, beachten Sie bitte, dass die vorhandenen Daten überschrieben werden.
> 
> 

### Erneuern der Power BI-Autorisierung
Sie müssen Ihr Power BI-Konto erneut authentifizieren, wenn das Kennwort seit der Erstellung oder letzten Authentifizierung Ihres Auftrags geändert wurde. Wenn Multi-Factor Authentication (MFA) auf Ihrem Azure Active Directory (AAD)-Mandanten konfiguriert ist, müssen Sie auch Power BI-Autorisierung alle 2 Wochen erneuern. Dieses Problem zeigt sich daran, dass keine Auftragsausgabe erfolgt und in den Vorgangsprotokollen ein Benutzerauthentifizierungsfehler angezeigt wird:

  ![Power BI-Aktualisierungstoken-Fehler](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)

Um dieses Problem zu beheben, halten Sie den laufenden Auftrag an, und wechseln Sie zur Power BI-Ausgabe. Klicken Sie auf den Link "Autorisierung erneuern", und starten Sie den Auftrag ab dem letzten Anhaltepunkt neu, um Datenverlust zu vermeiden.

  ![Power BI-Erneuerungsautorisierung](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)

## Tabellenspeicher
[Azure-Tabellenspeicher](../storage/storage-introduction.md) bietet einen hoch verfügbaren, in hohem Maße skalierbaren Speicher, sodass eine Anwendung automatisch an die Bedürfnisse der Benutzer angepasst werden kann. Tabellenspeicher ist Microsofts NoSQL-Schlüssel-/Attributspeicher, der für strukturierte Daten genutzt werden kann, die weniger Einschränkungen hinsichtlich des Schemas haben. Azure-Tabellenspeicher kann zum Speichern von Daten für dauerhafte Archivierung und effizienten Abruf verwendet werden.

Die folgende Tabelle enthält die Eigenschaftennamen und die entsprechenden Beschreibungen zum Erstellen einer Tabellenausgabe.

| Eigenschaftenname | Beschreibung |
| --- | --- |
| Ausgabealias |Dies ist ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an diesen Tabellenspeicher weiterzuleiten. |
| Speicherkonto |Der Name des Speicherkontos, an das Sie die Ausgabe senden. |
| Speicherkontoschlüssel |Der Zugriffsschlüssel, der dem Speicherkonto zugeordnet ist. |
| Tabellenname |Der Name der Tabelle. Die Tabelle wird erstellt, wenn sie noch nicht vorhanden ist. |
| Partitionsschlüssel |Der Name der Ausgabespalte mit dem Partitionsschlüssel. Der Partitionsschlüssel ist ein eindeutiger Bezeichner für die Partition innerhalb einer Tabelle, die den ersten Teil des Primärschlüssels einer Entität bildet. Dabei handelt es sich um einen Zeichenfolgenwert, der bis zu 1 KB groß sein kann. |
| Zeilenschlüssel |Der Name der Ausgabespalte, die den Zeilenschlüssel enthält. Der Zeilenschlüssel ist ein eindeutiger Bezeichner für eine Entität innerhalb einer Partition. Sie bildet den zweiten Teil des Primärschlüssels einer Entität. Der Zeilenschlüssel ist ein Zeichenfolgenwert, der bis zu 1 KB groß sein kann. |
| Batchgröße |Dies ist die Anzahl von Datensätzen für einen Batchvorgang. In der Regel ist die Standardeinstellung für die meisten Aufträge ausreichend. Ausführlichere Informationen zum Ändern dieser Einstellung finden Sie in der [TableBatchOperation-Spezifikation](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) (in englischer Sprache). |

## Service Bus-Warteschlangen
[Service Bus-Warteschlangen](https://msdn.microsoft.com/library/azure/hh367516.aspx) bieten eine FIFO-Nachrichtenzustellung (First In, First Out) an einen Consumer oder an mehrere konkurrierende Consumer. Typischerweise wird erwartet, dass Nachrichten in der zeitlichen Reihenfolge von den Consumern empfangen und verarbeitet werden, in der sie der Warteschlange hinzugefügt wurden, und jede Nachricht wird nur von einem Nachrichtenconsumer empfangen und verarbeitet.

Die folgende Tabelle enthält die Eigenschaftennamen und die entsprechenden Beschreibungen zum Erstellen einer Warteschlangenausgabe.

| Eigenschaftenname | Beschreibung |
| --- | --- |
| Ausgabealias |Dies ist ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an diese Service Bus-Warteschlange weiterzuleiten. |
| Service Bus- Namespace |Ein Service Bus-Namespace ist ein Container für einen Satz von Nachrichtenentitäten. |
| Warteschlangenname |Der Name der Service Bus-Warteschlange. |
| Name der Warteschlangenrichtlinie |Beim Erstellen einer Warteschlange können Sie auf der Registerkarte "Warteschlange konfigurieren" Richtlinien für den gemeinsamen Zugriff erstellen. Jede Richtlinie für den gemeinsamen Zugriff umfasst einen Namen, die von Ihnen festgelegten Berechtigungen und Zugriffsschlüssel. |
| Schlüssel der Warteschlangenrichtlinie |Der Schlüssel für den gemeinsamen Zugriff, der für die Authentifizierung des Zugriffs auf den Service Bus-Namespace verwendet wird. |
| Ereignisserialisierungsformat |Das Serialisierungsformat für Ausgabedaten. Es werden JSON, CSV und Avro unterstützt. |
| Codieren |Bei CSV und JSON ist UTF-8 gegenwärtig das einzige unterstützte Codierungsformat. |
| Trennzeichen |Gilt nur für die CSV-Serialisierung. Stream Analytics unterstützt eine Reihe von üblichen Trennzeichen zum Serialisieren der Daten im CSV-Format. Unterstützte Werte sind Komma, Semikolon, Leerzeichen, Tabulator und senkrechter Strich. |
| Format |Gilt nur für den JSON-Typ. "Separate Zeile" gibt an, dass die Ausgabe so formatiert wird, dass jedes JSON-Objekt in einer neuen Zeile enthalten ist. "Array" gibt an, dass die Ausgabe als Array aus JSON-Objekten formatiert wird. |

## Service Bus-Themen
Während Service Bus-Warteschlangen eine 1:1-Kommunikationsmethode vom Absender zum Empfänger bereitstellen, bieten [Service Bus-Themen](https://msdn.microsoft.com/library/azure/hh367516.aspx) eine 1:n-Form der Kommunikation.

Die folgende Tabelle enthält die Eigenschaftennamen und die entsprechenden Beschreibungen zum Erstellen einer Tabellenausgabe.

| Eigenschaftenname | Beschreibung |
| --- | --- |
| Ausgabealias |Dies ist ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an dieses Service Bus-Thema weiterzuleiten. |
| Service Bus- Namespace |Ein Service Bus-Namespace ist ein Container für einen Satz von Nachrichtenentitäten. Sie haben bei der Erstellung eines neuen Event Hubs auch einen Service Bus-Namespace erstellt. |
| Themenname |Themen sind Messagingentitäten, vergleichbar mit Event Hubs und Warteschlangen. Sie wurden für die Erfassung von Ereignisstreams von einer Reihe unterschiedlicher Geräte und Dienste entwickelt. Wenn ein Thema erstellt wird, wird ihm auch ein bestimmter Name zugewiesen. Die an ein Thema gesendeten Nachrichten sind nur verfügbar, wenn ein Abonnement erstellt wurde. Stellen Sie daher sicher, dass es mindestens ein Abonnement unter dem Thema gibt. |
| Name der Themenrichtlinie |Beim Erstellen eines Themas können Sie auf der Registerkarte „Thema konfigurieren“ Richtlinien für den gemeinsamen Zugriff erstellen. Jede Richtlinie für den gemeinsamen Zugriff verfügt über einen Namen, die von Ihnen festgelegten Berechtigungen und Zugriffsschlüssel. |
| Schlüssel der Themenrichtlinie |Der Schlüssel für den gemeinsamen Zugriff, der für die Authentifizierung des Zugriffs auf den Service Bus-Namespace verwendet wird. |
| Ereignisserialisierungsformat |Das Serialisierungsformat für Ausgabedaten. Es werden JSON, CSV und Avro unterstützt. |
| Codieren |Beim CSV- oder JSON-Format muss eine Codierung angegeben werden. Das einzige derzeit unterstützte Codierungsformat ist UTF-8. |
| Trennzeichen |Gilt nur für die CSV-Serialisierung. Stream Analytics unterstützt eine Reihe von üblichen Trennzeichen zum Serialisieren der Daten im CSV-Format. Unterstützte Werte sind Komma, Semikolon, Leerzeichen, Tabulator und senkrechter Strich. |

## DocumentDB
[Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) ist ein vollständig verwalteter NoSQL-Dokumentendatenbankdienst, der Abfragen und Transaktionen über schemafreie Daten, vorhersagbare und zuverlässige Leistung sowie schnelle Entwicklung bietet.

Die folgende Tabelle enthält die Eigenschaftennamen und die entsprechenden Beschreibungen zum Erstellen einer DocumentDB-Ausgabe.

<table>
<tbody>
<tr>
<td>EIGENSCHAFTENNAME</td>
<td>BESCHREIBUNG</td>
</tr>
<tr>
<td>Kontoname</td>
<td>Der Name des DocumentDB-Kontos. Dies kann auch der Endpunkt für das Konto sein.</td>
</tr>
<tr>
<td>Kontoschlüssel</td>
<td>Der gemeinsame Zugriffsschlüssel für das DocumentDB-Konto.</td>
</tr>
<tr>
<td>Datenbank</td>
<td>Der Name der DocumentDB-Datenbank.</td>
</tr>
<tr>
<td>Muster des Sammlungsnamen</td>
<td>Das Sammlungsnamenmuster für die zu verwendenden Sammlungen. Das Sammlungsnamenformat kann mit dem optionalen Token {partition} gebildet werden, wobei Partitionen bei 0 beginnen.<BR>Beispiel: Die Eingaben sind zulässig:<BR>MyCollection{partition}<BR>MyCollection<BR>Beachten Sie, dass Sammlungen vorhanden sein müssen, bevor der Stream Analytics-Auftrag gestartet wird, und nicht automatisch erstellt werden.</td>
</tr>
<tr>
<td>Partitionsschlüssel</td>
<td>Der Name des Felds in Ausgabeereignissen, das zur Angabe des Schlüssels für die Partitionierung der Ausgabe über Sammlungen hinweg verwendet wird.</td>
</tr>
<tr>
<td>Dokument-ID</td>
<td>Der Name des Felds in Ausgabeereignissen, das zur Angabe des Primärschlüssels verwendet wird, auf dem Einfüge- und Updatevorgänge basieren.</td>
</tr>
</tbody>
</table>


## Hier erhalten Sie Hilfe
Um Hilfe zu erhalten, besuchen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/de-DE/home?forum=AzureStreamAnalytics).

## Nächste Schritte
Sie haben nun Stream Analytics kennengelernt, einen verwalteten Dienst für Stream Analytics für Daten aus dem Internet der Dinge. Weitere Informationen zu diesem Dienst finden Sie unter:

* [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md)
* [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!---HONumber=AcomDC_0928_2016-->