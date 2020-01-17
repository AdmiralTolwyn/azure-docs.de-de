---
title: Beheben von Problemen mit Datenflüssen
description: In diesem Artikel wird beschrieben, wie Sie in Azure Data Factory Datenflussprobleme beheben.
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.custom: seo-lt-2019
ms.date: 12/19/2019
ms.openlocfilehash: 06746cfc3b39a242c16a6b4f4c95b3c212a9abd5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443945"
---
# <a name="troubleshoot-azure-data-factory-data-flows"></a>Problembehandlung für Azure Data Factory-Datenflüsse

In diesem Artikel werden die gängigen Problembehandlungsmethoden für Datenflüsse in Azure Data Factory beschrieben.

## <a name="common-errors-and-messages"></a>Häufige Fehler und Meldungen

### <a name="error-message-df-sys-01-shadeddatabricksorgapachehadoopfsazureazureexception-commicrosoftazurestoragestorageexception-the-specified-container-does-not-exist"></a>Fehlermeldung: DF-SYS-01: shaded.databricks.org.apache.hadoop.fs.azure.AzureException: com.microsoft.azure.storage.StorageException: Der angegebene Container ist nicht vorhanden.

- **Symptome:** Fehler beim Anzeigen von Daten in der Vorschau, beim Debuggen und bei der Datenflussausführung in der Pipeline, weil der Container nicht vorhanden ist

- **Ursache:** Das Dataset enthält einen Container, der im Speicher nicht vorhanden ist.

- **Lösung:** Stellen Sie sicher, dass der Container vorhanden ist, auf den Sie im Dataset verweisen.

### <a name="error-message-df-sys-01-javalangassertionerror-assertion-failed-conflicting-directory-structures-detected-suspicious-paths"></a>Fehlermeldung: DF-SYS-01: java.lang.AssertionError: Assertionsfehler: Conflicting directory structures detected. (Es wurden widersprüchliche Verzeichnisstrukturen erkannt.) Suspicious paths (Verdächtige Pfade)

- **Symptome:** Platzhalter werden in der Quelltransformation mit Parquet-Dateien verwendet.

- **Ursache:** Falsche oder ungültige Platzhaltersyntax

- **Lösung:** Überprüfen Sie die Platzhaltersyntax, die Sie in den Optionen für die Quelltransformation verwenden.

### <a name="error-message-df-src-002-container-container-name-is-required"></a>Fehlermeldung: DF-SRC-002: 'container' (Containername) ist erforderlich

- **Symptome:** Fehler beim Anzeigen von Daten in der Vorschau, beim Debuggen und bei der Datenflussausführung in der Pipeline, weil der Container nicht vorhanden ist

- **Ursache:** Das Dataset enthält einen Container, der im Speicher nicht vorhanden ist.

- **Lösung:** Stellen Sie sicher, dass der Container vorhanden ist, auf den Sie im Dataset verweisen.

### <a name="error-message-df-uni-001-primarykeyvalue-has-incompatible-types-integertype-and-stringtype"></a>Fehlermeldung: DF-UNI-001: PrimaryKeyValue has incompatible types IntegerType and StringType (PrimaryKeyValue weist inkompatible Typen IntegerType und StringType auf)

- **Symptome:** Fehler beim Anzeigen von Daten in der Vorschau, beim Debuggen und bei der Datenflussausführung in der Pipeline, weil der Container nicht vorhanden ist

- **Ursache:** Tritt auf, wenn versucht wird, einen falschen Primärschlüsseltyp in Datenbanksenken einzufügen.

- **Lösung:** Verwenden Sie eine Derived Column, um die im Primärschlüssel des Datenflusses verwendete Spalte umzuwandeln, sodass sie dem Datentyp der Zieldatenbank entspricht.

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-the-tcpip-connection-to-the-host-xxxxxdatabasewindowsnet-port-1433-has-failed-error-xxxxdatabasewindowsnet-verify-the-connection-properties-make-sure-that-an-instance-of-sql-server-is-running-on-the-host-and-accepting-tcpip-connections-at-the-port-make-sure-that-tcp-connections-to-the-port-are-not-blocked-by-a-firewall"></a>Fehlermeldung: DF-SYS-01: com.microsoft.sqlserver.jdbc.SQLServerException: Fehler beim Herstellen der TCP/IP-Verbindung mit dem Host xxxxx.database.windows.net, Port 1433. Fehler: „xxxx.database.windows.net. Überprüfen Sie die Verbindungseigenschaften. Stellen Sie sicher, dass eine SQL Server-Instanz auf dem Host ausgeführt wird, die TCP/IP-Verbindungen am Port annimmt. Überprüfen Sie außerdem, dass die TCP-Verbindungen mit dem Port nicht von einer Firewall blockiert werden.“

- **Symptome:** Vorschau von Daten oder Ausführen der Pipeline mit der Datenbankquelle oder der Senke ist nicht möglich.

- **Ursache:** Die Datenbank ist durch eine Firewall geschützt.

- **Lösung:** Gestatten Sie den Firewallzugriff auf die Datenbank.

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-there-is-already-an-object-named-xxxxxx-in-the-database"></a>Fehlermeldung: DF-SYS-01: com.microsoft.sqlserver.jdbc.SQLServerException: In der Datenbank ist bereits ein Objekt mit dem Namen 'xxxxxx' vorhanden.

- **Symptome:** Die Senke kann keine Tabelle erstellen.

- **Ursache:** In der Zieldatenbank ist bereits eine Tabelle mit dem Namen vorhanden, der in der Quelle oder im Dataset definiert ist.

- **Lösung:** Ändern Sie den Namen der Tabelle, die Sie erstellen möchten.

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-string-or-binary-data-would-be-truncated"></a>Fehlermeldung: DF-SYS-01: com.microsoft.sqlserver.jdbc.SQLServerException: String- oder binäre Daten würden abgeschnitten. 

- **Symptome:** Beim Schreiben von Daten in eine SQL-Senke tritt bei Ihrem Datenfluss bei der Pipelineausführung ein Fehler auf, möglicherweise ein Kürzungsfehler.

- **Ursache:** Ein Feld aus dem Datenfluss wird einer Spalte in Ihrer SQL-Datenbank zugeordnet, die nicht breit genug ist, um den Wert zu speichern, sodass der SQL-Treiber diesen Fehler auslöst.

- **Lösung:** Sie können die Länge der Daten für Zeichenfolgenspalten mithilfe von ```left()``` in einer abgeleiteten Spalte reduzieren oder das [„Fehlerzeilen“-Muster implementieren.](how-to-data-flow-error-rows.md)

### <a name="error-message-since-spark-23-the-queries-from-raw-jsoncsv-files-are-disallowed-when-the-referenced-columns-only-include-the-internal-corrupt-record-column"></a>Fehlermeldung: Seit Spark 2.3 sind die Abfragen aus JSON-/CSV-Dateien mit Rohdaten unzulässig, wenn die Spalten, auf die verwiesen wird, nur die interne Spalte mit beschädigten Datensätzen enthalten. 

- **Symptome:** Fehler beim Lesen aus einer JSON-Quelle

- **Ursache:** Beim Lesen aus einer JSON-Quelle mit einem einzelnen Dokument in zahlreichen geschachtelten Zeilen kann ADF nicht über Spark ermitteln, wo das vorherige Dokument endet und ein neues Dokument beginnt.

- **Lösung:** Erweitern Sie in der Quelltransformation, die ein JSON-Dataset verwendet, die Option „JSON-Einstellungen“, und aktivieren Sie „Einzelnes Dokument“.

### <a name="error-message-duplicate-columns-found-in-join"></a>Fehlermeldung: Doppelte Spalten bei Join gefunden

- **Symptome:** Die JOIN-Transformation führte zu Spalten auf der linken und der rechten Seite, die doppelte Spaltennamen enthalten.

- **Ursache:** Die verknüpften Datenströme enthalten identische Spaltennamen.

- **Lösung:** Fügen Sie nach dem Join eine SELECT-Transformation hinzu, und wählen Sie „Doppelte Spalten entfernen“ für die Eingabe und die Ausgabe aus.

### <a name="error-message-possible-cartesian-product"></a>Fehlermeldung: Mögliches kartesisches Produkt

- **Symptome:** Die Join- oder Lookup-Transformation hat bei der Ausführung Ihres Datenflusses ein mögliches kartesisches Produkt entdeckt.

- **Ursache:** Der Datenfluss kann fehlschlagen, wenn Sie Azure Data Factory nicht direkt angewiesen haben, ein Kreuzprodukt zu verwenden.

- **Lösung:** Ändern Sie die Lookup- oder Join-Transformation in einen Join, der ein benutzerdefiniertes Kreuzprodukt verwendet, und geben Sie Ihre Lookup- oder Joinbedingung in den Ausdrucks-Editor ein. Wenn Sie explizit ein vollständiges kartesisches Produkt erzeugen möchten, verwenden Sie vor dem Join die Transformation für abgeleitete Spalten in beiden unabhängigen Datenströmen, um einen synthetischen Schlüssel für den Vergleich zu erstellen. Erstellen Sie z. B. mit der Transformation für abgeleitete Spalten eine neue Spalte in jedem Datenstrom mit dem Namen ```SyntheticKey``` und legen Sie ihn auf ```1``` fest. Verwenden Sie dann ```a.SyntheticKey == b.SyntheticKey``` als benutzerdefinierten Joinausdruck.

> [!NOTE]
> Achten Sie darauf, dass Sie mindestens eine Spalte von jeder Seite der linken und rechten Beziehung in ein benutzerdefiniertes Kreuzprodukt einbeziehen. Die Ausführung von Kreuzprodukten mit statischen Werten anstelle von Spalten von jeder Seite führt zu vollständigen Überprüfungen des gesamten Datasets, sodass der Datenfluss mit geringer Leistung ausgeführt wird.

## <a name="general-troubleshooting-guidance"></a>Allgemeine Anleitungen zur Problembehandlung

1. Überprüfen Sie den Status der Datasetverbindungen. Besuchen Sie in jeder Quell-und Senkentransformation den verknüpften Dienst für jedes verwendete Dataset, und testen Sie die Verbindungen.
2. Überprüfen Sie den Status der Datei- und Tabellenverbindungen aus dem Datenfluss-Designer. Aktivieren Sie das Debuggen, und klicken Sie auf „Datenvorschau“ für Ihre Quelltransformationen, um sicherzustellen, dass Sie auf die Daten zugreifen können.
3. Wenn in der Datenvorschau keine Probleme erkennbar sind, wechseln Sie zum Pipeline-Designer, und fügen Sie den Datenfluss in eine Pipelineaktivität ein. Debuggen Sie die Pipeline in einem End-to-End-Test.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung finden Sie in diesen Ressourcen:

*  [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory-Funktionsanfragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-Videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN-Forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Anleitung zur Leistung und Optimierung der Mapping Data Flow-Funktion](concepts-data-flow-performance.md)
