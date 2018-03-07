---
title: Kopieren von Daten aus SAP ECC mithilfe von Azure Data Factory | Microsoft-Dokumentation
description: "Erfahren Sie, wie Daten aus SAP ECC mithilfe einer Kopieraktivität in einer Azure Data Factory-Pipeline in unterstützte Senkendatenspeicher kopiert werden."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: jingwang
ms.openlocfilehash: efca2c129a1c7b8aca6b879d6d1311c6be157be1
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/28/2018
---
# <a name="copy-data-from-sap-ecc-using-azure-data-factory"></a>Kopieren von Daten aus SAP ECC mithilfe von Azure Data Factory

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus SAP ECC (SAP Enterprise Central Component) zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

> [!NOTE]
> Dieser Artikel bezieht sich auf Version 2 von Data Factory, die zurzeit als Vorschau verfügbar ist. Wenn Sie Version 1 des Data Factory-Diensts verwenden, der allgemein verfügbar (GA) ist, lesen Sie [Kopieraktivität in V1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Sie können Daten aus SAP ECC in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Dieser SAP ECC-Connector unterstützt insbesondere Folgendes:

- Kopieren von Daten aus SAP ECC auf SAP NetWeaver ab Version 7.0 
- Kopieren von Daten aus Objekten, die von SAP ECC-OData-Diensten (SAP Table/Views, BAPI, Data Extractors usw.) verfügbar gemacht werden, oder von an SAP PI gesendeten Daten/IDOCs, die als OData über relative Adapter empfangen werden können
- Kopieren von Daten mithilfe der Standardauthentifizierung

## <a name="prerequisites"></a>Voraussetzungen

Im Allgemeinen stellt SAP ECC Entitäten mithilfe von OData-Diensten über SAP-Gateway bereit. Zum Verwenden dieses SAP ECC-Connectors müssen Sie folgende Schritte durchführen:

- **Einrichten von SAP-Gateway**. Auf Servern mit SAP NetWeaver ab Version 7.4 ist SAP Gateway bereits installiert. Andernfalls müssen Sie ein eingebettetes Gateway oder einen Gateway-Hub installieren, bevor Sie SAP ECC-Daten über OData-Dienste bereitstellen. Im [Installationshandbuch](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm) erfahren Sie, wie Sie SAP Gateway einrichten.

- **Aktivieren und Konfigurieren der SAP-OData-Dienste**. Sie können die OData-Dienste über TCODE SICF in Sekunden aktivieren. Sie können auch konfigurieren, welche Objekte für Anforderungen verfügbar gemacht werden sollen. Hier ist eine beispielhafte [ausführliche Anleitung](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="getting-started"></a>Erste Schritte

Sie können mit dem .NET SDK, Python SDK, Azure PowerShell, der REST-API oder der Azure Resource Manager-Vorlage eine Pipeline mit einer Kopieraktivität erstellen. Im [Tutorial zur Kopieraktivität](quickstart-create-data-factory-dot-net.md) finden Sie detaillierte Anweisungen, wie Sie eine Pipeline mit einer Kopieraktivität erstellen können.

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den SAP ECC-Connector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit SAP ECC verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf **SapEcc** festgelegt werden. | Ja |
| URL | Die URL des SAP ECC OData-Diensts | Ja |
| username | Der Benutzername, mit dem die Verbindung mit SAP ECC hergestellt wird | Nein  |
| password | Das Klartextkennwort, mit dem die Verbindung mit SAP ECC hergestellt wird | Nein  |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden muss. Sie können die selbstgehostete Integration Runtime oder Azure Integration Runtime verwenden (sofern Ihr Datenspeicher öffentlich zugänglich ist). Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein  |

**Beispiel:**

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData url e.g. http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom SAP ECC-Dataset unterstützt werden.

Legen Sie zum Kopieren von Daten aus SAP ECC die type-Eigenschaft des Datasets auf **SapEccResource** fest. Folgende Eigenschaften werden unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| path | Pfad der SAP ECC OData-Entität | Ja |

**Beispiel**

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typePoperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der SAP ECC-Quelle unterstützt werden.

### <a name="sap-ecc-as-source"></a>SAP ECC als Quelle

Legen Sie zum Kopieren von Daten aus SAP ECC den Quelltyp in der Kopieraktivität auf **SapEccSource** fest. Folgende Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf **SapEccSource** festgelegt werden. | Ja |
| query | OData-Abfrageoptionen zum Filtern von Daten. Beispiel: „$select=Name,Description&$top=10“.<br/><br/>Das SAP ECC-Connector kopiert Daten aus der kombinierten URL: (im verknüpften Dienst angegebene URL)/(im Dataset angegebener Pfad)?(in der Quelle der Kopieraktivität angegebene Abfrage). Lesen Sie hierzu [OData-URL-Komponenten](http://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Ja |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromSAPECC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP ECC input dataset name>",
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
                "type": "SapEccSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-ecc"></a>Datentypzuordnung für SAP ECC

Beim Kopieren von Daten aus SAP ECC werden die folgenden Zuordnungen von OData-Datentypen für SAP ECC zu Azure Data Factory-Zwischendatentypen verwendet. Unter [Schema- und Datentypzuordnungen](copy-activity-schema-and-type-mapping.md) erfahren Sie, wie Sie Aktivitätszuordnungen für Quellschema und Datentyp in die Senke kopieren.

| OData-Datentyp | Data Factory-Zwischendatentyp |
|:--- |:--- |:--- |
| Edm.Binary | Zeichenfolge |
| Edm.Boolean | Bool |
| Edm.Byte | Zeichenfolge |
| Edm.DateTime | Datetime |
| Edm.Decimal | DECIMAL |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | Zeichenfolge |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | Zeichenfolge |
| Edm.Time | Zeitraum |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> Komplexe Datentypen werden derzeit nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
