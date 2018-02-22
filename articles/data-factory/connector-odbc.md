---
title: Kopieren von Daten aus ODBC-Quellen mithilfe von Azure Data Factory | Microsoft-Dokumentation
description: "Erfahren Sie, wie Daten aus OData-Quellen mithilfe einer Kopieraktivität in eine Azure Data Factory-Pipeline in unterstützte Senkendatenspeicher kopiert werden."
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
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: ab29338b5ce2090166e75f3860744562aa9f6b01
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2018
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Kopieren von Daten aus ODBC-Datenspeichern bzw. in ODBC-Datenspeicher mithilfe von Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1: allgemein verfügbar](v1/data-factory-odbc-connector.md)
> * [Version 2 – Vorschauversion](connector-odbc.md)

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus einem bzw. in einen ODBC-Datenspeicher zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

> [!NOTE]
> Dieser Artikel bezieht sich auf Version 2 von Data Factory, die zurzeit als Vorschau verfügbar ist. Wenn Sie Version 1 des Data Factory-Diensts verwenden, der allgemein verfügbar (General Availability, GA) ist, lesen Sie [ODBC-Connector in V1](v1/data-factory-odata-connector.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Sie können Daten aus einer ODBC-Quelle in jeden unterstützten Senkendatenspeicher oder Daten aus jedem unterstützten Quelldatenspeicher in eine ODBC-Senke kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Dieser ODBC-Connector unterstützt insbesondere das Kopieren von Daten aus **ODBC-kompatiblen Datenspeichern** bzw. in diese mithilfe der **Standard**- oder **anonymen** Authentifizierung.

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie zum Verwenden dieses ODBC-Connectors diese Schritte aus:

- Richten Sie eine selbstgehostete Integration Runtime ein. Im Artikel [Selbstgehostete Integration Runtime](create-self-hosted-integration-runtime.md) finden Sie Details.
- Installieren des ODBC-Treibers für den Datenspeicher auf dem Computer mit der Integrationslaufzeit.

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den ODBC-Connector verwendet werden:

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit ODBC verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf **Odbc** festgelegt werden. | Ja |
| connectionString | Die Verbindungszeichenfolge, ausgenommen des Teils mit den Anmeldeinformationen. Sie können die Verbindungszeichenfolge mit einem Muster wie `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"` angeben oder den System-DSN (Data Source Name) verwenden, den Sie mit `"DSN=<name of the DSN on IR machine>;"` auf dem Computer mit der Integrationslaufzeit eingerichtet haben. (Sie müssen nach wie vor den Teil mit den Anmeldeinformationen im verknüpften Dienst entsprechend angeben.)<br>Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md).| Ja |
| authenticationType | Typ der Authentifizierung für die Verbindung mit dem ODBC-Datenspeicher.<br/>Zulässige Werte sind **Basic** oder **Anonymous**. | Ja |
| userName | Geben Sie den Benutzernamen an, wenn Sie die Standardauthentifizierung (Basic) verwenden. | Nein  |
| password | Geben Sie das Kennwort für das Benutzerkonto an, das Sie für „userName“ angegeben haben. Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Nein  |
| credential | Der zum Zugriff bestimmte Teil der Anmeldeinformationen in der Verbindungszeichenfolge. Er wird in einem treiberspezifischen Format in Eigenschaft und Wert angegeben. Beispiel: `"RefreshToken=<secret refresh token>;"`. Legen Sie für dieses Feld „SecureString“ fest. | Nein  |
| connectVia | Die [Integrationslaufzeit](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden muss. Eine selbstgehostete Integrationslaufzeit ist erforderlich, wie unter [Voraussetzungen](#prerequisites) erwähnt wird. |Ja |

**Beispiel 1: Verwenden der Standardauthentifizierung**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<connection string>"
            },
            "authenticationType": "Basic",
            "userName": "<username>",
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

**Beispiel 2: Verwenden der anonymen Authentifizierung**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<connection string>"
            },
            "authenticationType": "Anonymous",
            "credential": {
                "type": "SecureString",
                "value": "RefreshToken=<secret refresh token>;"
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

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu Datasets. Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom ODBC-Dataset unterstützt werden.

Legen Sie zum Kopieren von Daten aus ODBC-kompatiblen Datenspeichern bzw. in diese die type-Eigenschaft des Datasets auf **RelationalTable** fest. Folgende Eigenschaften werden unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf **RelationalTable** festgelegt werden. | Ja |
| tableName | Der Name der Tabelle im ODBC-Datenspeicher. | Nein bei Quellen (wenn „query“ in der Aktivitätsquelle angegeben ist);<br/>ja bei Senken |

**Beispiel**

```json
{
    "name": "ODBCDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<ODBC linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der ODBC-Quelle unterstützt werden.

### <a name="odbc-as-source"></a>ODBC als Quelle

Legen Sie zum Kopieren von Daten aus ODBC-kompatiblen Datenspeichern den Quelltyp in der Kopieraktivität auf **RelationalSource** fest. Folgende Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die „type“-Eigenschaft der Quelle der Kopieraktivität muss auf **RelationalSource** festgelegt werden. | Ja |
| query | Verwendet die benutzerdefinierte SQL-Abfrage zum Lesen von Daten. Beispiel: `"SELECT * FROM MyTable"`. | Nein (wenn „tableName“ im Dataset angegeben ist) |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ODBC input dataset name>",
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

### <a name="odbc-as-sink"></a>ODBC als Senke

Legen Sie zum Kopieren von Daten in ODBC-kompatible Datenspeicher den Senkentyp in der Kopieraktivität auf **OdbcSink** fest. Folgende Eigenschaften werden im Abschnitt **sink** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Senke der Kopieraktivität muss auf **OdbcSink** festgelegt werden. | Ja |
| writeBatchTimeout |Die Wartezeit für den Abschluss der Batcheinfügung, bis das Timeout wirksam wird.<br/>Zulässige Werte: Zeitraum Beispiel: 00:30:00 (30 Minuten) |Nein  |
| writeBatchSize |Fügt Daten in die SQL-Tabelle ein, wenn die Puffergröße "writeBatchSize" erreicht.<br/>Zulässige Werte: Ganze Zahlen (Anzahl der Zeilen). |Nein (Standard ist 0 – automatisch erkannt) |
| preCopyScript |Geben Sie eine auszuführende SQL-Abfrage für die Kopieraktivität an, ehe Sie bei der jeder Ausführung Daten in Datenspeicher schreiben. Sie können diese Eigenschaft nutzen, um die vorab geladenen Daten zu bereinigen. |Nein  |

> [!NOTE]
> Wenn „writeBatchSize“ nicht festgelegt (automatisch erkannt) wird, erkennt die Kopieraktivität zunächst, ob der Treiber Batchvorgänge unterstützt. Falls ja, wird die Anzahl der Batchvorgänge auf 10.000 festgelegt, falls nicht auf 1. Wenn Sie explizit einen Wert ungleich 0 festlegen, berücksichtigt die Kopieraktivität den Wert. Es tritt dann ein Fehler zur Laufzeit auf, wenn der Treiber nicht Batchvorgänge unterstützt.

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyToODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ODBC output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OdbcSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="ibm-informix-source"></a>IBM Informix-Quelle

Mit dem generischen ODBC-Connector können Sie Daten von IBM Informix-Datenbanken kopieren.

Richten Sie eine selbstgehostete Integrationslaufzeit auf einem Computer mit Zugriff auf Ihren Datenspeicher ein. Die Integrationslaufzeit verwendet den ODBC-Treiber für Informix, um eine Verbindung mit dem Datenspeicher herzustellen. Installieren Sie also den Treiber, falls dieser noch nicht auf demselben Computer installiert ist. Beispielsweise können Sie den Treiber „IBM INFORMIX ODBC DRIVER (64-bit)“ verwenden. Details finden Sie im Abschnitt [Voraussetzungen](#prerequisites).

Bevor Sie die Informix-Quelle in einer Data Factory-Lösung verwenden können, überprüfen Sie anhand der Anweisungen im Abschnitt [Behandeln von Konnektivitätsproblemen](#troubleshoot-connectivity-issues), ob die Integrationslaufzeit eine Verbindung mit dem Datenspeicher herstellen kann.

Erstellen Sie einen über die ODBC verknüpften Dienst, um einen IBM Informix-Datenspeicher mit Azure Data Factory zu verknüpfen, wie im folgenden Beispiel zu sehen ist:

```json
{
    "name": "InformixLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<Informix connection string or DSN>"
            },
            "authenticationType": "Basic",
            "userName": "<username>",
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

Lesen Sie den Artikel vom Anfang, um einen detaillierten Überblick über die Verwendung von ODBC-Datenspeichern als Quell-/Senkendatenspeicher in einem Kopiervorgang zu erhalten.

## <a name="microsoft-access-source"></a>Microsoft Access-Quelle

Mit dem generischen ODBC-Connector können Sie Daten von Microsoft Access-Datenbanken kopieren.

Richten Sie eine selbstgehostete Integrationslaufzeit auf einem Computer mit Zugriff auf Ihren Datenspeicher ein. Die Integrationslaufzeit verwendet den ODBC-Treiber für Microsoft Access, um eine Verbindung mit dem Datenspeicher herzustellen. Installieren Sie also den Treiber, falls dieser noch nicht auf demselben Computer installiert ist. Details finden Sie im Abschnitt [Voraussetzungen](#prerequisites).

Bevor Sie die Microsoft Access-Quelle in einer Data Factory-Lösung verwenden können, überprüfen Sie anhand der Anweisungen im Abschnitt [Behandeln von Konnektivitätsproblemen](#troubleshoot-connectivity-issues), ob die Integrationslaufzeit eine Verbindung mit dem Datenspeicher herstellen kann.

Erstellen Sie einen über die ODBC verknüpften Dienst, um eine Microsoft Access-Datenbank mit Azure Data Factory zu verknüpfen, wie im folgenden Beispiel zu sehen ist:

```json
{
    "name": "MicrosoftAccessLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Driver={Microsoft Access Driver (*.mdb, *.accdb)};Dbq=<path to your DB file e.g. C:\\mydatabase.accdb>;"
            },
            "authenticationType": "Basic",
            "userName": "<username>",
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

Lesen Sie den Artikel vom Anfang, um einen detaillierten Überblick über die Verwendung von ODBC-Datenspeichern als Quell-/Senkendatenspeicher in einem Kopiervorgang zu erhalten.

## <a name="ge-historian-source"></a>GE Historian-Quelle

Mit dem generischen ODBC-Connector können Sie Daten von GE Historian kopieren.

Richten Sie eine selbstgehostete Integrationslaufzeit auf einem Computer mit Zugriff auf Ihren Datenspeicher ein. Die Integrationslaufzeit verwendet den ODBC-Treiber für GE Historian, um eine Verbindung mit dem Datenspeicher herzustellen. Installieren Sie also den Treiber, falls dieser noch nicht auf demselben Computer installiert ist. Details finden Sie im Abschnitt [Voraussetzungen](#prerequisites).

Bevor Sie die GE Historian-Quelle in einer Data Factory-Lösung verwenden können, überprüfen Sie anhand der Anweisungen im Abschnitt [Behandeln von Konnektivitätsproblemen](#troubleshoot-connectivity-issues), ob die Integrationslaufzeit eine Verbindung mit dem Datenspeicher herstellen kann.

Erstellen Sie einen über die ODBC verknüpften Dienst, um eine Microsoft Access-Datenbank mit Azure Data Factory zu verknüpfen, wie im folgenden Beispiel zu sehen ist:

```json
{
    "name": "HistorianLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<GE Historian store connection string or DSN>"
            },
            "authenticationType": "Basic",
            "userName": "<username>",
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

Lesen Sie den Artikel vom Anfang, um einen detaillierten Überblick über die Verwendung von ODBC-Datenspeichern als Quell-/Senkendatenspeicher in einem Kopiervorgang zu erhalten.

## <a name="sap-hana-sink"></a>SAP HANA-Senke

>[!NOTE]
>Informationen zum Kopieren von Daten aus SAP HANA-Datenspeichern finden Sie im Artikel zum nativen [SAP HANA-Connector](connector-sap-hana.md). Um Daten in SAP HANA zu kopieren, befolgen Sie diese Anweisung zur Verwendung des ODBC-Connectors. Beachten Sie, dass die verknüpften Dienste für SAP HANA-Connectors und ODBC-Connectors unterschiedlichen Typs sind und daher nicht wiederverwendet werden können.
>

Mit dem generischen ODBC-Connector können Sie Daten von SAP HANA-Datenbanken kopieren.

Richten Sie eine selbstgehostete Integrationslaufzeit auf einem Computer mit Zugriff auf Ihren Datenspeicher ein. Die Integrationslaufzeit verwendet den ODBC-Treiber für SAP HANA, um eine Verbindung mit dem Datenspeicher herzustellen. Installieren Sie also den Treiber, falls dieser noch nicht auf demselben Computer installiert ist. Details finden Sie im Abschnitt [Voraussetzungen](#prerequisites).

Bevor Sie die SAP HANA-Senke in einer Data Factory-Lösung verwenden können, überprüfen Sie anhand der Anweisungen im Abschnitt [Behandeln von Konnektivitätsproblemen](#troubleshoot-connectivity-issues), ob die Integrationslaufzeit eine Verbindung mit dem Datenspeicher herstellen kann.

Erstellen Sie einen über die ODBC verknüpften Dienst, um einen SAP HANA-Datenspeicher mit Azure Data Factory zu verknüpfen, wie im folgenden Beispiel zu sehen ist:

```json
{
    "name": "SAPHANAViaODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Driver={HDBODBC};servernode=<HANA server>.clouddatahub-int.net:30015"
            },
            "authenticationType": "Basic",
            "userName": "<username>",
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

Lesen Sie den Artikel vom Anfang, um einen detaillierten Überblick über die Verwendung von ODBC-Datenspeichern als Quell-/Senkendatenspeicher in einem Kopiervorgang zu erhalten.

## <a name="troubleshoot-connectivity-issues"></a>Behandeln von Konnektivitätsproblemen

Um Verbindungsprobleme zu behandeln, verwenden Sie die Registerkarte **Diagnose** im **Konfigurations-Manager für die Integrationslaufzeit**.

1. Starten Sie den **Konfigurations-Manager für die Integrationslaufzeit**.
2. Wechseln Sie zur Registerkarte **Diagnose** .
3. Wählen Sie im Abschnitt „Verbindung testen“ den **Typ** des Datenspeichers (verknüpfter Dienst).
4. Geben Sie die **Verbindungszeichenfolge** an, die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet wird, wählen Sie die **Authentifizierung** aus, und geben Sie **Benutzername**, **Kennwort** und/oder **Anmeldeinformationen** ein.
5. Klicken Sie auf **Verbindung testen** , um die Verbindung mit dem Datenspeicher zu testen.

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md##supported-data-stores-and-formats).