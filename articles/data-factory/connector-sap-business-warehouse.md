---
title: Kopieren von Daten aus SAP BW mithilfe von Azure Data Factory | Microsoft-Dokumentation
description: Erfahren Sie, wie Daten aus SAP Business Warehouse mithilfe einer Kopieraktivität in eine Azure Data Factory-Pipeline in unterstützte Senkendatenspeicher kopiert werden.
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
ms.date: 08/07/2018
ms.author: jingwang
ms.openlocfilehash: 52bbf93d73af281f3959e056a4d5b959e7286cb5
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/07/2018
ms.locfileid: "39590329"
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Kopieren von Daten aus SAP Business Warehouse mithilfe von Azure Data Factory
> [!div class="op_single_selector" title1="Wählen Sie die Version des Data Factory-Dienstes aus, den Sie verwenden:"]
> * [Version 1](v1/data-factory-sap-business-warehouse-connector.md)
> * [Aktuelle Version](connector-sap-business-warehouse.md)

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus SAP Business Warehouse (BW) zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Sie können Daten aus SAP Business Warehouse in jeden unterstützten Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Dieser SAP Business Warehouse-Connector unterstützt insbesondere Folgendes:

- SAP Business Warehouse **Version 7.x**
- Kopieren von Daten aus **InfoCubes und QueryCubes** (einschließlich BEx-Abfragen) über MDX-Abfragen
- Kopieren von Daten mithilfe der Standardauthentifizierung

## <a name="prerequisites"></a>Voraussetzungen

Zur Verwendung dieses SAP Business Warehouse-Connectors müssen Sie folgende Schritte durchführen:

- Richten Sie eine selbstgehostete Integration Runtime ein. Im Artikel [Selbstgehostete Integration Runtime](create-self-hosted-integration-runtime.md) finden Sie Details.
- Installieren der **SAP NetWeaver-Bibliothek** auf dem Computer mit der Integrationslaufzeit. Sie erhalten die SAP Netweaver-Bibliothek vom SAP-Administrator oder direkt aus dem [SAP Software Download Center](https://support.sap.com/swdc). Suchen Sie nach der **SAP Note #1025361**, um den Downloadspeicherort für die neueste Version zu ermitteln. Stellen Sie sicher, dass Sie die **64-Bit**-Version der SAP NetWeaver-Bibliothek auswählen, die der Installation der Integration Runtime entspricht. Installieren Sie dann alle Dateien, die im SAP NetWeaver RFC SDK enthalten sind, entsprechend der SAP-Mitteilung (SAP Note). Die SAP NetWeaver-Bibliothek ist auch in der SAP Client Tools-Installation enthalten.

>[!TIP]
>Um Konnektivitätsprobleme bei SAP BW zu beheben, stellen Sie Folgendes sicher:
>- Alle Abhängigkeitsbibliotheken, die aus dem NetWeaver RFC SDK extrahiert wurden, werden im Ordner „%windir%\system32“ platziert. In der Regel enthält er Folgendes: icudt34.dll, icuin34.dll, icuuc34.dll, libicudecnumber.dll, librfc32.dll, libsapucum.dll, sapcrypto.dll, sapcryto_old.dll, sapnwrfc.dll.
>- Die erforderlichen Ports, die zum Herstellen einer Verbindung mit dem SAP-Server verwendet werden, sind auf dem Computer der selbstgehosteten IR aktiviert. Dies sind üblicherweise Port 3300 und 3201.

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den SAP Business Warehouse-Connector verwendet werden:

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit SAP Business Warehouse (BW) verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf **SapBw** festgelegt werden. | JA |
| server | Der Name des Servers, auf dem sich die SAP BW-Instanz befindet. | JA |
| systemNumber | Die Systemnummer des SAP BW-Systems.<br/>Zulässiger Wert: Zweistellige Dezimalzahl, die als Zeichenfolge angegeben ist. | JA |
| clientId | Client-ID des Clients im SAP BW-System.<br/>Zulässiger Wert: Dreistellige Dezimalzahl, die als Zeichenfolge angegeben ist. | JA |
| userName | Der Name des Benutzers, der Zugriff auf den SAP-Server hat. | JA |
| password | Kennwort für den Benutzer Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | JA |
| connectVia | Die [Integrationslaufzeit](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden muss. Eine selbstgehostete Integrationslaufzeit ist erforderlich, wie unter [Voraussetzungen](#prerequisites) erwähnt wird. |JA |

**Beispiel:**

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
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

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu Datasets. Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom SAP BW-Dataset unterstützt werden.

Legen Sie zum Kopieren von Daten aus SAP BW die type-Eigenschaft des Datasets auf **RelationalTable** fest. Es sind keine typspezifischen Eigenschaften, die für das SAP BW-Dataset des Typs „RelationalTable“ unterstützt werden.

**Beispiel:**

```json
{
    "name": "SAPBWDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<SAP BW linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der SAP BW-Quelle unterstützt werden.

### <a name="sap-bw-as-source"></a>SAP BW als Quelle

Legen Sie zum Kopieren von Daten aus SAP BW den Quelltyp in der Kopieraktivität auf **RelationalSource** fest. Folgende Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die „type“-Eigenschaft der Quelle der Kopieraktivität muss auf **RelationalSource** festgelegt werden. | JA |
| query | Gibt die MDX-Abfrage an, mit der Daten aus der SAP BW-Instanz gelesen werden. | JA |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromSAPBW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW input dataset name>",
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
                "query": "<MDX query for SAP BW>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw"></a>Datentypzuordnung für SAP BW

Beim Kopieren von Daten aus SAP BW werden die folgenden Zuordnungen von SAP BW-Datentypen zu Azure Data Factory-Zwischendatentypen verwendet. Unter [Schema- und Datentypzuordnungen](copy-activity-schema-and-type-mapping.md) erfahren Sie, wie Sie Aktivitätszuordnungen für Quellschema und Datentyp in die Senke kopieren.

| SAP BW-Datentyp | Data Factory-Zwischendatentyp |
|:--- |:--- |
| ACCP | int |
| CHAR | Zeichenfolge |
| CLNT | Zeichenfolge |
| CURR | Decimal |
| CUKY | Zeichenfolge |
| DEC | DECIMAL |
| FLTP | Double |
| INT1 | Byte |
| INT2 | Int16 |
| INT4 | int |
| LANG | Zeichenfolge |
| LCHR | Zeichenfolge |
| LRAW | Byte[] |
| PREC | Int16 |
| QUAN | DECIMAL |
| RAW | Byte[] |
| RAWSTRING | Byte[] |
| STRING | Zeichenfolge |
| EINHEIT | Zeichenfolge |
| DATS | Zeichenfolge |
| NUMC | Zeichenfolge |
| TIMS | Zeichenfolge |


## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
