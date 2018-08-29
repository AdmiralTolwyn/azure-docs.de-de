---
title: Kopieren von Daten nach und aus Azure Table Storage mit Data Factory | Microsoft-Dokumentation
description: Erfahren Sie, wie mithilfe von Data Factory Daten aus unterstützten Quellspeichern in Azure Table Storage oder aus Table Storage in unterstützte Senkenspeicher kopiert werden.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: jingwang
ms.openlocfilehash: 0399836191050996ac3eaf0fbe59496e10e2b426
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2018
ms.locfileid: "42144633"
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>Kopieren von Daten nach und aus Azure Table Storage mit Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-azure-table-connector.md)
> * [Aktuelle Version](connector-azure-table-storage.md)

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten nach und aus Azure Table Storage zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Zudem können Sie Daten aus jedem unterstützten Quelldatenspeicher nach Table Storage kopieren. Sie können auch Daten aus Table Storage in jeden unterstützten Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Dieser Azure Table-Connector unterstützt insbesondere das Kopieren von Daten mithilfe der Authentifizierung sowohl per Kontoschlüssel als auch per Dienst-SAS (Shared Access Signature).

## <a name="get-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für Table Storage verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

### <a name="use-an-account-key"></a>Verwenden eines Kontoschlüssels

Sie können mithilfe des Kontoschlüssels einen mit Azure Storage verknüpften Dienst erstellen. Dadurch hat die Data Factory weltweiten Zugriff auf Storage. Die folgenden Eigenschaften werden unterstützt.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf **AzureTableStorage** festgelegt werden. |JA |
| connectionString | Geben Sie für die connectionString-Eigenschaft die Informationen ein, die zum Herstellen einer Verbindung mit Azure Storage erforderlich sind. Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). |JA |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Sie können die Azure Integration Runtime oder selbstgehostete Integration Runtime verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein  |

>[!NOTE]
>Wenn Sie den verknüpften Dienst vom Typ „AzureStorage“ verwendet haben, wird dies weiterhin unterstützt. Es wird jedoch empfohlen, in Zukunft diesen neuen verknüpften Dienst vom Typ „AzureTableStorage“ zu verwenden.

**Beispiel:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-shared-access-signature-authentication"></a>Verwenden der SAS-Authentifizierung (Shared Access Signature)

Sie können einen mit Storage verknüpften Dienst auch mithilfe einer Shared Access Signature erstellen. Dies ermöglicht der Data Factory eingeschränkten/zeitgebundenen Zugriff auf alle bzw. bestimmte Ressourcen im Speicher.

Shared Access Signatures bieten delegierten Zugriff auf Ressourcen in Ihrem Speicherkonto. Sie können diese verwenden, um einem Client für einen bestimmten Zeitraum spezielle eingeschränkte Berechtigungen für Objekte in Ihrem Speicherkonto zu gewähren. Sie müssen die Zugriffsschlüssel für Ihr Konto nicht freigeben. Die SAS ist ein URI, dessen Abfrageparameter alle erforderlichen Informationen für den authentifizierten Zugriff auf eine Speicherressource enthalten. Um mit der SAS auf Speicherressourcen zuzugreifen, muss der Client diese nur an den entsprechenden Konstruktor bzw. die entsprechende Methode übergeben. Weitere Informationen zu Shared Access Signatures finden Sie unter [Verwenden von Shared Access Signatures (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Data Factory unterstützt jetzt **SAS (Shared Access Signatures) für Dienste** sowie für **Konten**. Weitere Informationen zu diesen beiden SAS-Arten und ihrer Erstellung finden Sie unter [Arten von Shared Access Signatures](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures). 

> [!TIP]
> Um eine Dienst-SAS für Ihr Speicherkonto zu generieren, können Sie die folgenden PowerShell-Befehle ausführen. Ersetzen Sie die Platzhalter, und gewähren Sie die erforderliche Berechtigung.
> `$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Für die Verwendung der SAS-Authentifizierung werden die folgenden Eigenschaften unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf **AzureTableStorage** festgelegt werden. |JA |
| sasUri | Geben Sie den SAS-URI für die Storage-Ressourcen wie Blobs, Container oder Tabellen an. Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). |JA |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Sie können die Azure Integration Runtime oder die selbstgehostete Integration Runtime verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein  |

>[!NOTE]
>Wenn Sie den verknüpften Dienst vom Typ „AzureStorage“ verwendet haben, wird dies weiterhin unterstützt. Es wird jedoch empfohlen, in Zukunft diesen neuen verknüpften Dienst vom Typ „AzureTableStorage“ zu verwenden.

**Beispiel:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Berücksichtigen Sie beim Erstellen eines SAS-URIs die folgenden Aspekte:

- Legen Sie geeignete Lese-/Schreib-Berechtigungen für Objekte fest, basierend auf der Verwendung des verknüpften Diensts in Ihrer Data Factory (Lesen, Schreiben, Lesen/Schreiben).
- Legen Sie für **Ablaufzeit** einen geeigneten Wert fest. Stellen Sie sicher, dass der Zugriff auf Storage-Objekte nicht während des aktiven Zeitraums der Pipeline abläuft.
- Der URI sollte je nach Bedarf auf der richtigen Tabellenebene erstellt werden.

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom Azure Table-Dataset unterstützt werden.

Legen Sie zum Kopieren von Daten aus und nach Azure Table Storage die type-Eigenschaft des Datasets auf **AzureTable** fest. Die folgenden Eigenschaften werden unterstützt.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf **AzureTable** festgelegt werden. |JA |
| tableName |Der Name der Tabelle in der Table Storage-Datenbankinstanz, auf die der verknüpfte Dienst verweist. |JA |

**Beispiel:**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "linkedServiceName": {
            "referenceName": "<Azure Table storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Schema per Data Factory

Bei schemafreien Datenspeichern wie Azure Table leitet der Data Factory-Dienst das Schema auf eine der folgenden Arten ab:

* Wenn Sie die Struktur der Daten mithilfe der **structure**-Eigenschaft in der Datasetdefinition angeben, betrachtet Data Factory diese Struktur als das Schema. Wenn in diesem Fall eine Zeile keinen Wert für eine Spalte enthält, wird ein NULL-Wert für sie angegeben.
* Wenn Sie die Struktur der Daten nicht mithilfe der **structure** -Eigenschaft in der Datasetdefinition angeben, leitet Data Factory das Schema unter Verwendung der ersten Zeile in den Daten ab. Wenn in diesem Fall die erste Zeile nicht das vollständige Schema enthält, fehlen im Ergebnis des Kopiervorgangs einige Spalten.

Bei schemafreien Datenquellen empfiehlt es sich, die Struktur der Daten mithilfe der **structure**-Eigenschaft anzugeben.

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Azure Table-Quelle und -Senke unterstützt werden.

### <a name="azure-table-as-a-source-type"></a>Azure Table als Quelltyp

Legen Sie zum Kopieren von Daten aus der Azure-Tabelle den Quelltyp in der Kopieraktivität auf **AzureTableSource** fest. Die folgenden Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf **AzureTableSource** festgelegt werden. |JA |
| AzureTableSourceQuery |Verwendet die benutzerdefinierte Table Storage-Abfrage zum Lesen von Daten. Siehe Beispiele im folgenden Abschnitt. |Nein  |
| azureTableSourceIgnoreTableNotFound |Gibt an, ob es zulässig ist, wenn die Ausnahme der Tabelle nicht vorhanden ist.<br/>Zulässige Werte sind **true** und **false** (Standard). |Nein  |

### <a name="azuretablesourcequery-examples"></a>Beispiele für azureTableSourceQuery

Wenn die Azure Table-Spalte den datetime-Typ aufweist:

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Wenn die Azure Table-Spalte den Zeichenfolgentyp aufweist:

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

Wenn Sie den pipeline-Parameter verwenden, wandeln Sie den datetime-Wert gemäß den oben genannten Beispielen in das richtige Format um.

### <a name="azure-table-as-a-sink-type"></a>Azure Table als Senkentyp

Legen Sie zum Kopieren von Daten in die Azure-Tabelle den Senkentyp in der Kopieraktivität auf **AzureTableSink** fest. Die folgenden Eigenschaften werden im Abschnitt **sink** der Kopieraktivität unterstützt.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Senke der Kopieraktivität muss auf **AzureTableSink** festgelegt werden. |JA |
| azureTableDefaultPartitionKeyValue |Der standardmäßige Partitionsschlüsselwert, der von der Senke verwendet werden kann. |Nein  |
| azureTablePartitionKeyName |Geben Sie den Namen der Spalte an, deren Werte als Partitionsschlüssel verwendet werden. Wenn dieser nicht angegeben ist, wird „AzureTableDefaultPartitionKeyValue“ als Partitionsschlüssel verwendet. |Nein  |
| azureTableRowKeyName |Geben Sie den Namen der Spalte an, deren Werte als Zeilenschlüssel verwendet werden. Wenn nicht angegeben, verwenden Sie für jede Zeile eine GUID. |Nein  |
| azureTableInsertType |Der Modus zum Einfügen von Daten in Azure Table. Diese Eigenschaft steuert, ob die Werte von vorhandenen Zeilen in der Ausgabetabelle, deren Partitions- und Zeilenschlüssel übereinstimmen, ersetzt oder zusammengeführt werden. <br/><br/>Zulässige Werte sind **merge** (Standard) und **replace**. <br/><br> Diese Einstellung wird auf Zeilenebene, nicht auf Tabellenebene angewendet. Keine der Optionen löscht Zeilen in der Ausgabetabelle, die in der Eingabe nicht vorhanden sind. Informationen zur Funktionsweise der Einstellungen zum Zusammenführen und Ersetzen finden Sie unter [Insert Or Merge Entity](https://msdn.microsoft.com/library/azure/hh452241.aspx) (Entität einfügen oder zusammenführen) und [Insert Or Replace Entity](https://msdn.microsoft.com/library/azure/hh452242.aspx) (Entität einfügen oder ersetzen). |Nein  |
| writeBatchSize |Fügt Daten in Azure Table ein, wenn „writeBatchSize“ oder „writeBatchTimeout“ erreicht werden.<br/>Zulässige Werte sind Integer-Werte (Anzahl der Zeilen). |Nein (Standardwert ist 10.000) |
| writeBatchTimeout |Fügt Daten in Azure Table ein, wenn „writeBatchSize“ oder „writeBatchTimeout“ erreicht werden.<br/>Zulässige Werte sind Timespan-Werte. Beispiel: 00:20:00 (20 Minuten). |Nein (Standardwert ist 90 Sekunden, der Standardtimeout des Speicherclients) |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyToAzureTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Table output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureTableSink",
                "azureTablePartitionKeyName": "<column name>",
                "azureTableRowKeyName": "<column name>"
            }
        }
    }
]
```

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName

Bevor Sie die Zielspalte als „azureTablePartitionKeyName“ verwenden können, müssen Sie einer Zielspalte mithilfe der Eigenschaft **translator** eine Quellspalte zuordnen.

Im folgenden Beispiel wird die Quellspalte „DivisionID“ der Zielspalte „DivisionID“ zugeordnet:

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

„DivisionID“ ist als Partitionsschlüssel angegeben.

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Datentypzuordnung für Azure-Tabellen

Beim Kopieren von Daten aus und nach Azure Table werden die folgenden Zuordnungen von Azure Table-Datentypen zu Data Factory-Zwischendatentypen verwendet. Weitere Informationen dazu, wie die Kopieraktivität das Quellschema und den Datentyp zur Senke zuordnet, finden Sie unter [Schema- und Datentypzuordnungen](copy-activity-schema-and-type-mapping.md).

Beim Verschieben von Daten von und nach Azure Table werden die folgenden [von Azure Table definierten Zuordnungen](https://msdn.microsoft.com/library/azure/dd179338.aspx) verwendet, um Azure Table-OData-Typen zu .NET-Typen und umgekehrt zuzuordnen.

| Azure-Tabellendatentyp | Data Factory-Zwischendatentyp | Details |
|:--- |:--- |:--- |
| Edm.Binary |Byte[] |Ein Array von Bytes mit einer Größe bis zu 64KB. |
| Edm.Boolean |bool |Ein boolescher Wert. |
| Edm.DateTime |Datetime |Ein 64-Bit-Wert, ausgedrückt als koordinierte Weltzeit (UTC). Der unterstützte DateTime-Bereich beginnt am 1. Januar 1601 n. Chr um Mitternacht. (unsere Zeitrechnung), UTC Der Bereich endet am 31. Dezember 9999. |
| Edm.Double |double |Ein 64-Bit-Gleitkommawert. |
| Edm.Guid |Guid |Ein 128-Bit-GUID. |
| Edm.Int32 |Int32 |Eine 32-Bit-Ganzzahl. |
| Edm.Int64 |Int64 |Eine 64-Bit-Ganzzahl. |
| Edm.String |Zeichenfolge |Ein UTF-16-codierter Wert. Zeichenfolgenwerte können bis zu 64 KB groß sein. |

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
