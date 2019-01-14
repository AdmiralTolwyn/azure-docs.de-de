---
title: Kopieren von Daten aus MySQL mithilfe von Azure Data Factory | Microsoft-Dokumentation
description: Erfahren Sie mehr über den MySQL-Connector in Azure Data Factory, mit dem Sie Daten von einer MySQL-Datenbank in einem als Senke unterstützten Datenspeicher kopieren können.
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
ms.date: 06/23/2018
ms.author: jingwang
ms.openlocfilehash: bb3179f1db077aacc7e36acf16486ee77a7f36e7
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051262"
---
# <a name="copy-data-from-mysql-using-azure-data-factory"></a>Kopieren von Daten aus MySQL mithilfe von Azure Data Factory
> [!div class="op_single_selector" title1="Wählen Sie die Version des Data Factory-Dienstes aus, den Sie verwenden:"]
> * [Version 1](v1/data-factory-onprem-mysql-connector.md)
> * [Aktuelle Version](connector-mysql.md)

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus einer MySQL-Datenbank zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Sie können Daten aus einer MySQL-Datenbank in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Dieser MySQL-Connector unterstützt insbesondere MySQL **Version 5.1 oder höher**.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Ihre MySQL-Datenbank nicht öffentlich zugänglich ist, müssen Sie eine selbstgehostete Integration Runtime einrichten. Details zur selbstgehosteten Integrationslaufzeit finden Sie im Artikel [Selbstgehostete Integrationslaufzeit](create-self-hosted-integration-runtime.md). Die Integration Runtime bietet ab Version 3.7 einen integrierten MySQL-Treiber. Daher müssen keine Treiber manuell installiert werden.

Bei einer selbstgehosteten IR-Version vor 3.7 müssen Sie den [MySQL-Connector bzw. .NET für Microsoft Windows](https://dev.mysql.com/downloads/connector/net/) (Version 6.6.5 bis 6.10.7) auf dem Computer mit der Integration Runtime installieren. Dieser 32-Bit-Treiber ist mit 64-Bit-IR kompatibel.

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den MySQL-Connector verwendet werden:

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit MySQL verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf **MySql** festgelegt werden. | Ja |
| connectionString | Geben Sie Informationen an, die zum Herstellen einer Verbindung mit der Azure Database for MySQL-Instanz erforderlich sind. Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Ja |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden muss. Sie können die selbstgehostete Integration Runtime oder Azure Integration Runtime verwenden (sofern Ihr Datenspeicher öffentlich zugänglich ist). Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein  |

Eine typische Verbindungszeichenfolge ist `Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`. Weitere Eigenschaften, die Sie für Ihren Fall festlegen können:

| Eigenschaft | BESCHREIBUNG | Optionen | Erforderlich |
|:--- |:--- |:--- |:--- |:--- |
| SSLMode | Diese Option gibt an, ob der Treiber beim Herstellen der Verbindung mit MySQL SSL-Verschlüsselung und Überprüfung verwendet. Beispiel: `SSLMode=<0/1/2/3/4>`| DISABLED (0) / PREFERRED (1) **(Standard)** / REQUIRED (2) / VERIFY_CA (3) / VERIFY_IDENTITY (4) | Nein  |
| UseSystemTrustStore | Diese Option gibt an, ob ein Zertifizierungsstellenzertifikat aus dem Vertrauensspeicher des Systems oder aus einer angegebenen PEM-Datei verwendet werden soll. Beispiel: `UseSystemTrustStore=<0/1>;`| Enabled (1) / Disabled (0) **(Standard)** | Nein  |

**Beispiel:**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Wenn Sie den verknüpften MySQL-Dienst mit der folgenden Nutzlast verwendet haben, wird er weiterhin unverändert unterstützt. Es wird jedoch empfohlen, zukünftig die neue Version zu verwenden.

**Vorherige Nutzlast:**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu Datasets. Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom MySQL-Dataset unterstützt werden.

Legen Sie zum Kopieren von Daten aus MySQL die type-Eigenschaft des Datasets auf **RelationalTable** fest. Folgende Eigenschaften werden unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf **RelationalTable** festgelegt werden. | Ja |
| tableName | Name der Tabelle in der MySQL-Datenbank. | Nein (wenn „query“ in der Aktivitätsquelle angegeben ist) |

**Beispiel**

```json
{
    "name": "MySQLDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<MySQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der MySQL-Quelle unterstützt werden.

### <a name="mysql-as-source"></a>MySQL als Quelle

Legen Sie zum Kopieren von Daten aus MySQL den Quelltyp in der Kopieraktivität auf **RelationalSource** fest. Folgende Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die „type“-Eigenschaft der Quelle der Kopieraktivität muss auf **RelationalSource** festgelegt werden. | Ja |
| query | Verwendet die benutzerdefinierte SQL-Abfrage zum Lesen von Daten. Beispiel: `"SELECT * FROM MyTable"`. | Nein (wenn „tableName“ im Dataset angegeben ist) |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MySQL input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "RelationalSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-mysql"></a>Datentypzuordnung für MySQL

Beim Kopieren von Daten aus MySQL werden die folgenden Zuordnungen von MySQL-Datentypen zu Azure Data Factory-Zwischendatentypen verwendet. Unter [Schema- und Datentypzuordnungen](copy-activity-schema-and-type-mapping.md) erfahren Sie, wie Sie Aktivitätszuordnungen für Quellschema und Datentyp in die Senke kopieren.

| MySQL-Datentyp | Data Factory-Zwischendatentyp |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit(1)` |`Boolean` |
| `bit(M), M>1`|`Byte[]`|
| `blob` |`Byte[]` |
| `bool` |`Int16` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal, String` |
| `double` |`Double` |
| `double precision` |`Double` |
| `enum` |`String` |
| `float` |`Single` |
| `int` |`Int32` |
| `int unsigned` |`Int64`|
| `integer` |`Int32` |
| `integer unsigned` |`Int64` |
| `long varbinary` |`Byte[]` |
| `long varchar` |`String` |
| `longblob` |`Byte[]` |
| `longtext` |`String` |
| `mediumblob` |`Byte[]` |
| `mediumint` |`Int32` |
| `mediumint unsigned` |`Int64` |
| `mediumtext` |`String` |
| `numeric` |`Decimal` |
| `real` |`Double` |
| `set` |`String` |
| `smallint` |`Int16` |
| `smallint unsigned` |`Int32` |
| `text` |`String` |
| `time` |`TimeSpan` |
| `timestamp` |`Datetime` |
| `tinyblob` |`Byte[]` |
| `tinyint` |`Int16` |
| `tinyint unsigned` |`Int16` |
| `tinytext` |`String` |
| `varchar` |`String` |
| `year` |`Int` |


## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
