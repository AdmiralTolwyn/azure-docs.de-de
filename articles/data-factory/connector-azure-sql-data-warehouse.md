---
title: Kopieren von Daten nach bzw. aus Azure SQL Data Warehouse mit Data Factory | Microsoft-Dokumentation
description: Erfahren Sie, wie mithilfe der Data Factory Daten von unterstützten Quellspeichern nach Azure SQL Data Warehouse oder aus SQL Data Warehouse in unterstützte Senkenspeicher kopiert werden.
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
ms.date: 05/28/2018
ms.author: jingwang
ms.openlocfilehash: c862f269a8e32814dfb6d311706e65b57d52d1bb
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34617075"
---
# <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Kopieren von Daten nach und aus Azure SQL Data Warehouse mithilfe von Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1: allgemein verfügbar](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Version 2 – Vorschauversion](connector-azure-sql-data-warehouse.md)

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten nach und aus Azure SQL Data Warehouse zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

> [!NOTE]
> Dieser Artikel bezieht sich auf Version 2 von Data Factory, die zurzeit als Vorschau verfügbar ist. Wenn Sie Version 1 des Data Factory-Diensts verwenden, die allgemein verfügbar (General Availability, GA) ist, lesen Sie [Azure SQL Data Warehouse-Connector in V1](v1/data-factory-azure-sql-data-warehouse-connector.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Sie können Daten aus bzw. nach Azure SQL Data Warehouse in einen beliebigen unterstützten Senkendatenspeicher oder Daten aus einem beliebigen unterstützten Quelldatenspeicher in Azure SQL Data Warehouse kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Der Azure SQL Data Warehouse-Connector unterstützt insbesondere Folgendes:

- Kopieren von Daten mit **SQL-Authentifizierung** und **Azure Active Directory-Anwendungstokenauthentifizierung** mit Dienstprinzipal oder verwalteter Dienstidentität (Managed Service Identität, MSI).
- Als Quelle das Abrufen von Daten mithilfe einer SQL-Abfrage oder gespeicherten Prozedur
- Als Senke das Laden von Daten durch **PolyBase** oder Masseneinfügung. Um bessere Kopierergebnisse zu erzielen, wird **empfohlen**, letztere der beiden Optionen zu verwenden.

> [!IMPORTANT]
> Beachten Sie, dass PolyBase nur die SQL-Authentifizierung, nicht jedoch die Azure Active Directory-Authentifizierung unterstützt.

> [!IMPORTANT]
> Wenn Sie Daten mithilfe der Azure Integration Runtime kopieren, konfigurieren Sie die [Azure SQL Server-Firewall](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) mit [Azure-Diensten den Zugriff auf den Server erlauben](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Wenn Sie Daten mithilfe einer selbst gehosteten Integration Runtime kopieren, konfigurieren Sie die Azure SQL Server-Firewall so, dass der entsprechende IP-Adressbereich, einschließlich der IP-Adresse des Computers, der für die Verbindung mit Azure SQL-Datenbank verwendet wird, zugelassen wird.

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den Azure SQL Data Warehouse-Connector verwendet werden:

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit Azure SQL Data Warehouse verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die "type"-Eigenschaft muss auf **AzureSqlDW** | Ja |
| connectionString |Geben Sie Informationen, die zur Verbindung mit der Azure SQL Data Warehouse-Instanz erforderlich sind, für die Eigenschaft "connectionString" ein. Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). |Ja |
| servicePrincipalId | Geben Sie die Client-ID der Anwendung an. | Ja, bei der AAD-Authentifizierung mit dem Dienstprinzipal |
| servicePrincipalKey | Geben Sie den Schlüssel der Anwendung an. Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Ja, bei der AAD-Authentifizierung mit dem Dienstprinzipal |
| Mandant | Geben Sie die Mandanteninformationen (Domänenname oder Mandanten-ID) für Ihre Anwendung an. Diese können Sie abrufen, indem Sie im Azure-Portal mit der Maus auf den Bereich oben rechts zeigen. | Ja, bei der AAD-Authentifizierung mit dem Dienstprinzipal |
| connectVia | Die [Integrationslaufzeit](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden muss. Sie können die Azure-Integrationslaufzeit oder selbstgehostete Integrationslaufzeit verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein  |

Weitere Voraussetzungen und JSON-Beispiele für die verschiedenen Authentifizierungstypen finden Sie in den folgenden Abschnitten:

- [Verwenden der SQL-Authentifizierung](#using-sql-authentication)
- [Verwenden der AAD-Anwendungstokenauthentifizierung – Dienstprinzipal](#using-service-principal-authentication)
- [Verwenden der AAD-Anwendungstokenauthentifizierung – verwaltete Dienstidentität](#using-managed-service-identity-authentication)

### <a name="using-sql-authentication"></a>Verwenden der SQL-Authentifizierung

**Beispiel eines verknüpften Diensts mit SQL-Authentifizierung:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-service-principal-authentication"></a>Verwenden der Dienstprinzipalauthentifizierung

Um die AAD-Anwendungstokenauthentifizierung basierend auf dem Dienstprinzipal zu verwenden, gehen Sie folgendermaßen vor:

1. **[Erstellen Sie eine Azure Active Directory-Anwendung im Azure-Portal](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application).**  Notieren Sie sich den Anwendungsnamen und die folgenden Werte, die Sie zum Definieren des verknüpften Diensts verwenden:

    - Anwendungs-ID
    - Anwendungsschlüssel
    - Mandanten-ID

2. **[Geben Sie einen Azure Active Directory-Administrator](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** für Ihre Azure SQL Server-Instanz im Azure-Portal an, falls dies noch nicht geschehen ist. Der AAD-Administrator kann ein AAD-Benutzer oder eine AAD-Gruppe sein. Wenn Sie der Gruppe mit der MSI eine Administatorrolle zuweisen, überspringen Sie die Schritte 3 und 4 unten, da der Administrator Vollzugriff auf die Datenbank hätte.

3. **Erstellen Sie einen Benutzer einer eigenständigen Datenbank für den Dienstprinzipal**, indem Sie eine Verbindung mit dem Data Warehouse, aus dem bzw. in das Sie Daten mithilfe von Tools wie SSMS kopieren möchten, herstellen. Verwenden Sie dazu eine AAD-Identität mit mindestens der Berechtigung „Beliebigen Benutzer ändern“, und führen Sie die folgende T-SQL-Anweisung aus. Weitere Informationen zu Benutzern eigenständiger Datenbanken finden Sie [hier](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities).
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Gewähren Sie dem Dienstprinzipal die notwendigen Berechtigungen**, wie bei SQL-Benutzern üblich, indem Sie z.B. Folgendes ausführen:

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. Konfigurieren Sie in ADF einen verknüpften Azure SQL Data Warehouse-Dienst.


**Beispiel eines verknüpften Diensts mit Dienstprinzipalauthentifizierung:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            },
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-managed-service-identity-authentication"></a>Verwenden der verwalteten Dienstidentitätsauthentifizierung

Eine Data Factory kann einer [verwalteten Dienstidentität (MSI)](data-factory-service-identity.md) zugeordnet werden, die diese spezielle Data Factory darstellt. Sie können diese Dienstidentität für die Azure SQL Data Warehouse-Authentifizierung verwenden, die dieser Factory den Zugriff auf Daten und das Kopieren in das bzw. aus dem Data Warehouse ermöglicht.

> [!IMPORTANT]
> PolyBase wird für die MSI-Authentifizierung derzeit nicht unterstützt.

Um die AAD-Anwendungstokenauthentifizierung basierend auf der MSI zu verwenden, gehen Sie folgendermaßen vor:

1. **Erstellen Sie eine Gruppe in Azure AD, und fügen Sie die MSI als Mitglied dieser Gruppe hinzu.**

    a. Suchen Sie die Data Factory-Dienstidentität im Azure-Portal. Wechseln Sie zu der Data Factory, wählen Sie „Eigenschaften“ > „Kopieren“ aus, und kopieren Sie die **Dienstidentitäts-ID**.

    b. Installieren Sie das [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2)-Modul, melden Sie sich mit dem Befehl `Connect-AzureAD` an, und führen Sie die folgenden Befehle zum Erstellen einer Gruppe und zum Hinzufügen der Data Factory-MSI als Mitglied aus.
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```

2. **[Geben Sie einen Azure Active Directory-Administrator](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** für Ihre Azure SQL Server-Instanz im Azure-Portal an, falls dies noch nicht geschehen ist.

3. **Erstellen Sie einen Benutzer einer eigenständigen Datenbank für die AAD-Gruppe**, indem Sie eine Verbindung mit dem Data Warehouse, aus dem bzw. in das Sie Daten mithilfe von Tools wie SSMS kopieren möchten, herstellen. Verwenden Sie dazu eine AAD-Identität mit mindestens der Berechtigung „Beliebigen Benutzer ändern“, und führen Sie die folgende T-SQL-Anweisung aus. Weitere Informationen zu Benutzern eigenständiger Datenbanken finden Sie [hier](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities).
    
    ```sql
    CREATE USER [your AAD group name] FROM EXTERNAL PROVIDER;
    ```

4. **Gewähren Sie der AAD-Gruppe die notwendigen Berechtigungen**, wie bei SQL-Benutzern üblich, indem Sie z.B. Folgendes ausführen:

    ```sql
    EXEC sp_addrolemember [role name], [your AAD group name];
    ```

5. Konfigurieren Sie in ADF einen verknüpften Azure SQL Data Warehouse-Dienst.

**Beispiel eines verknüpften Diensts mit MSI-Authentifizierung:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
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

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu Datasets. Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom Azure SQL Data Warehouse-Dataset unterstützt werden.

Legen Sie zum Kopieren von Daten aus bzw. nach Azure SQL Data Warehouse die type-Eigenschaft des Datasets auf **AzureSqlDWTable** fest. Folgende Eigenschaften werden unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf **AzureSqlDWTable** festgelegt werden. | Ja |
| tableName |Name der Tabelle oder Ansicht in der Azure SQL Data Warehouse-Instanz, auf die der verknüpfte Dienst verweist. | Ja |

**Beispiel:**

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Data Warehouse linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Azure SQL Data Warehouse-Quelle und -Senke unterstützt werden.

### <a name="azure-sql-data-warehouse-as-source"></a>Azure SQL Data Warehouse als Quelle

Legen Sie zum Kopieren von Daten aus Azure SQL Data Warehouse den Quelltyp in der Kopieraktivität auf **SqlDWSource** fest. Folgende Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf **SqlDWSource** festgelegt werden. | Ja |
| SqlReaderQuery |Verwendet die benutzerdefinierte SQL-Abfrage zum Lesen von Daten. Beispiel: `select * from MyTable`. |Nein  |
| sqlReaderStoredProcedureName |Der Name der gespeicherten Prozedur, die Daten aus der Quelltabelle liest. Die letzte SQL-Anweisung muss eine SELECT-Anweisung in der gespeicherten Prozedur sein. |Nein  |
| storedProcedureParameters |Parameter für die gespeicherte Prozedur.<br/>Zulässige Werte: Name/Wert-Paare. Die Namen und die Groß-/Kleinschreibung von Parametern müssen denen der Parameter der gespeicherten Prozedur entsprechen. |Nein  |

**Beachten Sie Folgendes:**

- Wenn **sqlReaderQuery** für „SqlSource“ angegeben ist, führt die Kopieraktivität diese Abfrage für die Azure SQL Data Warehouse-Quelle aus, um die Daten abzurufen. Alternativ dazu können Sie eine gespeicherte Prozedur angeben, indem Sie **sqlReaderStoredProcedureName** und **storedProcedureParameters** angeben (sofern die gespeicherten Prozeduren Parameter verwenden).
- Ohne Angabe von „sqlReaderQuery“ oder „sqlReaderStoredProcedureName“ werden die im Abschnitt „structure“ des Dataset-JSON-Bereichs definierten Spalten verwendet, um eine Abfrage (`select column1, column2 from mytable`) für Azure SQL Data Warehouse zu erstellen. Falls die Datasetdefinition nicht den Abschnitt „structure“ enthält, werden alle Spalten der Tabelle ausgewählt.
- Bei Verwendung von **sqlReaderStoredProcedureName** müssen Sie trotzdem einen Wert für die Dummyeigenschaft **tableName** im JSON-Code des Datasets angeben.

**Beispiel: Verwenden von SQL-Abfragen**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Beispiel: Verwenden von gespeicherten Prozeduren**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Die Definition der gespeicherten Prozedur:**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="azure-sql-data-warehouse-as-sink"></a>Azure SQL Data Warehouse als Senke

Legen Sie zum Kopieren von Daten nach Azure SQL Data Warehouse den Senkentyp in der Kopieraktivität auf **SqlDWSink** fest. Folgende Eigenschaften werden im Abschnitt **sink** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Senke der Kopieraktivität muss auf **SqlDWSink** festgelegt werden. | Ja |
| allowPolyBase |Gibt an, ob (falls zutreffend) PolyBase anstelle des BULKINSERT-Mechanismus verwendet werden soll. <br/><br/> **as empfohlene Verfahren zum Laden von Daten in SQL Data Warehouse ist PolyBase.** Einschränkungen und Einzelheiten finden Sie im Abschnitt [Daten unter Verwendung von PolyBase in Azure SQL Data Warehouse laden](#use-polybase-to-load-data-into-azure-sql-data-warehouse) .<br/><br/>Zulässige Werte sind **True** und **False** (Standard).  |Nein  |
| polyBaseSettings |Eine Gruppe von Eigenschaften, die angegeben werden können, wenn die **allowPolybase**-Eigenschaft auf **true** festgelegt ist. |Nein  |
| rejectValue |Gibt die Anzahl oder den Prozentsatz von Zeilen an, die abgelehnt werden können, bevor für die Abfrage ein Fehler auftritt.<br/><br/>Weitere Informationen zu den PolyBase-Ablehnungsoptionen finden Sie im Abschnitt **Argumente** des Themas [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) . <br/><br/>Zulässige Werte sind „0“ (Standard), „1“, „2“ etc. |Nein  |
| rejectType |Gibt an, ob die rejectValue-Option als Literalwert oder Prozentsatz angegeben ist.<br/><br/>Zulässige Werte sind **Value** (Standard) und **Percentage**. |Nein  |
| rejectSampleValue |Gibt die Anzahl von Zeilen an, die abgerufen werden, bevor PolyBase den Prozentsatz der abgelehnten Zeilen neu berechnet.<br/><br/>Zulässige Werte sind „1“, „2“ etc. |Ja, wenn für **rejectType** der Wert **percentage** festgelegt ist. |
| useTypeDefault |Gibt an, wie fehlende Werte in durch Trennzeichen getrennten Textdateien verarbeitet werden sollen, wenn PolyBase Daten aus der Textdatei abruft.<br/><br/>Weitere Informationen zu dieser Eigenschaft finden Sie im Abschnitt zu Argumenten im Thema [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx)verwenden können.<br/><br/>Zulässige Werte sind **True** oder **False** (Standard). |Nein  |
| writeBatchSize |Fügt Daten in die SQL-Tabelle ein, wenn die Puffergröße "writeBatchSize" erreicht. Ist nur anwendbar, wenn PolyBase nicht verwendet wird.<br/><br/>Zulässige Werte: Ganze Zahlen (Anzahl der Zeilen). |Nein (Standard = 10.000) |
| writeBatchTimeout |Die Wartezeit für den Abschluss der Batcheinfügung, bis das Timeout wirksam wird. Ist nur anwendbar, wenn PolyBase nicht verwendet wird.<br/><br/>Zulässige Werte: Zeitraum Beispiel: 00:30:00 (30 Minuten) |Nein  |
| preCopyScript |Geben Sie eine auszuführende SQL-Abfrage für die Kopieraktivität an, ehe Sie bei der jeder Ausführung Daten in Azure SQL Data Warehouse schreiben. Sie können diese Eigenschaft nutzen, um die vorab geladenen Daten zu bereinigen. |Nein  |(#repeatability-during-copy). |Eine Abfrageanweisung. |Nein  |

**Beispiel:**

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

Im nächsten Abschnitt erfahren Sie mehr über das effiziente Laden in SQL Data Warehouse mithilfe von PolyBase.

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Daten unter Verwendung von PolyBase in Azure SQL Data Warehouse laden

Mit **[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** lassen sich große Datenmengen effizient und mit hohem Durchsatz in Azure SQL Data Warehouse laden. Wenn Sie PolyBase anstelle des standardmäßigen BULKINSERT-Mechanismus verwenden, wird der Durchsatz erheblich gesteigert. Einen ausführlichen Vergleich finden Sie in der [Leistungsreferenz zur Kopieraktivität](copy-activity-performance.md#performance-reference). Eine exemplarische Vorgehensweise mit einem Anwendungsfall finden Sie unter [Laden von 1 TB in Azure SQL Data Warehouse in weniger als 15 Minuten mit Azure Data Factory](connector-azure-sql-data-warehouse.md).

* Wenn sich die Quelldaten in **Azure Blob Storage oder Azure Data Lake Store** befinden und ihr Format mit PolyBase kompatibel ist, können Sie sie mithilfe von PolyBase direkt nach Azure SQL Data Warehouse kopieren. Details finden Sie unter **[Direktes Kopieren mithilfe von PolyBase](#direct-copy-using-polybase)**.
* Wenn der Speicher und das Format der Quelldaten von PolyBase ursprünglich nicht unterstützt wird, können Sie stattdessen das Feature **[Gestaffeltes Kopieren mit PolyBase](#staged-copy-using-polybase)** verwenden. Es bietet auch einen besseren Durchsatz, da die Daten automatisch in ein PolyBase-kompatibles Format konvertiert und in Azure Blob Storage gespeichert werden. Anschließend werden die Daten in SQL Data Warehouse geladen.

> [!IMPORTANT]
> PolyBase wird für die AAD-Anwendungstokenauthentifizierung basierend auf der verwalteten Dienstidentität (Managed Service Identity, MSI) derzeit nicht unterstützt.

### <a name="direct-copy-using-polybase"></a>Direktes Kopieren mithilfe von PolyBase

PolyBase in SQL Data Warehouse bietet direkte Unterstützung für Azure Blob Storage und Azure Data Lake Store (mit dem Dienstprinzipal) als Quelle und mit bestimmten Anforderungen an das Datendateiformat. Wenn Ihre Daten die in diesem Abschnitt beschriebenen Kriterien erfüllen, können Sie mithilfe von PolyBase direkt aus dem Quelldatenspeicher in Azure SQL Data Warehouse kopieren. Andernfalls können Sie [gestaffeltes Kopieren mit PolyBase](#staged-copy-using-polybase)verwenden.

> [!TIP]
> Weitere Informationen zum effizienten Kopieren von Daten aus Data Lake Store nach SQL Data Warehouse finden Sie unter [Azure Data Factory makes it even easier and convenient to uncover insights from data when using Data Lake Store with SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/) (Wenn Data Lake Store mit SQL Data Warehouse verwendet wird, lassen sich mit Azure Data Factory noch einfacher Erkenntnisse aus Daten gewinnen) (in englischer Sprache).

Falls die Anforderungen nicht erfüllt werden, überprüft Azure Data Factory die Einstellungen und greift bei der Datenverschiebung automatisch auf den BULKINSERT-Mechanismus zurück.

1. Der **mit der Quelle verknüpfte Dienst** ist vom Typ **AzureStorage** oder **AzureDataLakeStore** mit Dienstprinzipalauthentifizierung.
2. Das **Eingabedataset** ist vom Typ **AzureBlob** oder **AzureDataLakeStoreFile**, und der Formattyp unter den `type`-Eigenschaften lautet **OrcFormat**, **ParquetFormat** oder **TextFormat** mit folgenden Konfigurationen:

   1. `rowDelimiter` muss **\n** sein.
   2. `nullValue` ist auf eine **leere Zeichenfolge** ("") festgelegt, oder `treatEmptyAsNull` ist auf **true** festgelegt.
   3. `encodingName` ist auf **utf-8** festgelegt. (Dies ist der **Standardwert**.)
   4. `escapeChar`, `quoteChar`, `firstRowAsHeader` und `skipLineCount` sind nicht angegeben.
   5. `compression` kann auf **keine Komprimierung** oder auf **Gzip** oder **Deflate** (Verkleinern) festgelegt sein.

    ```json
    "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",
           "nullValue": "",
           "encodingName": "utf-8"
       },
       "compression": {
           "type": "GZip",
           "level": "Optimal"
       }
    },
    ```

3. Für die Kopieraktivität in der Pipeline ist unter **BlobSource** oder **AzureDataLakeStore** keine `skipHeaderLineCount`-Einstellung vorhanden.
4. Für die Kopieraktivität in der Pipeline ist unter **SqlDWSink** keine `sliceIdentifierColumnName`-Einstellung vorhanden. (PolyBase stellt sicher, dass in einem Durchlauf entweder alle oder keine Daten aktualisiert werden). Um **Wiederholbarkeit** zu erreichen, kann `sqlWriterCleanupScript` verwendet werden.)

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-using-polybase"></a>gestaffeltes Kopieren mit PolyBase

Falls Ihre Quelldaten die Kriterien aus dem vorherigen Abschnitt nicht erfüllen, können Sie die Daten über einen zwischengeschalteten Azure Blob Storage mit Staging kopieren (darf nicht Storage Premium sein). In diesem Fall führt Azure Data Factory die erforderlichen Transformationen für die Daten automatisch durch, um die Datenformatanforderungen von PolyBase zu erfüllen. Laden Sie anschließend die Daten mithilfe von PolyBase in SQL Data Warehouse, und bereinigen Sie dann die temporären Daten in Blob Storage. Unter [Gestaffeltes Kopieren](copy-activity-performance.md#staged-copy) finden Sie ausführliche Informationen zur allgemeinen Funktionsweise des Kopierens von Daten über ein Azure-Stagingblob.

Erstellen Sie zum Verwenden dieses Features einen [verknüpften Azure Storage-Dienst](connector-azure-blob-storage.md#linked-service-properties), der auf das Azure Storage-Konto mit dem zwischengeschalteten Blobspeicher verweist, und geben Sie dann für die Kopieraktivität die Eigenschaften `enableStaging` und `stagingSettings` an, wie im folgenden Code zu sehen:

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

## <a name="best-practices-when-using-polybase"></a>Bewährte Methoden bei Verwendung von PolyBase

Die folgenden Abschnitte enthalten bewährte Methoden zusätzlich zu den in [Bewährte Methoden für Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-best-practices.md) aufgeführten Vorgängen.

### <a name="required-database-permission"></a>Erforderliche Datenbankberechtigungen

Um PolyBase verwenden zu können, muss der zum Laden von Daten in SQL Data Warehouse verwendete Benutzer über die [Berechtigung „CONTROL“](https://msdn.microsoft.com/library/ms191291.aspx) für die Zieldatenbank verfügen. Sie können dies beispielsweise erreichen, indem Sie diesen Benutzer als Mitglied der Rolle „db_owner“ hinzufügen. [In diesem Abschnitt](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization) erfahren Sie, wie das geht.

### <a name="row-size-and-data-type-limitation"></a>Beschränkungen hinsichtlich Zeilengröße und Datentyp

Der Ladevorgang in PolyBase ist auf das Laden von Zeilen beschränkt, die kleiner als **1 MB** sind und nicht in Spalten des Typs VARCHR(MAX), NVARCHAR(MAX) oder VARBINARY(MAX) geladen werden können. Informationen finden Sie [hier](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Wenn Sie über Quelldaten mit Zeilen verfügen, deren Größe über 1 MB liegt, sollten Sie die Quelltabellen vertikal in kleinere Tabellen unterteilen, sodass die Zeilengröße der einzelnen Tabellen den Höchstwert nicht überschreitet. Die kleineren Tabellen können dann mit PolyBase geladen und in Azure SQL Data Warehouse zusammengeführt werden.

### <a name="sql-data-warehouse-resource-class"></a>SQL Data Warehouse-Ressourcenklasse

Um einen optimalen Durchsatz zu erzielen, sollten Sie dem Benutzer, der zum Laden von Daten in SQL Data Warehouse über PolyBase verwendet wird, eine größere Ressourcenklasse zuweisen.

### <a name="tablename-in-azure-sql-data-warehouse"></a>„tableName“ in Azure SQL Data Warehouse

Die folgende Tabelle enthält Beispiele für das Angeben der **tableName** -Eigenschaft in Dataset-JSON für diverse Kombinationen aus Schema und Tabellenname:

| Datenbankschema | Tabellenname | JSON-Eigenschaft „tableName“ |
| --- | --- | --- |
| dbo |MyTable |MyTable oder dbo.MyTable oder [dbo].[MyTable] |
| dbo1 |MyTable |dbo1.MyTable oder [dbo1].[MyTable] |
| dbo |My.Table |[My.Table] oder [dbo].[My.Table] |
| dbo1 |My.Table |[dbo1].[My.Table] |

Sollte der folgende Fehler auftreten, liegt dies unter Umständen am Wert für die tableName-Eigenschaft. Informationen zur korrekten Angabe von Werten für die JSON-Eigenschaft „tableName“ finden Sie in der Tabelle.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Spalten mit Standardwerten

Das PolyBase-Feature in Data Factory akzeptiert aktuell lediglich dieselbe Anzahl von Spalten wie in der Zieltabelle. Angenommen, Sie verfügen über eine Tabelle mit vier Spalten, von denen eine mit einem Standardwert definiert ist. Die Eingabedaten müssen trotzdem vier Spalten umfassen. Bei Bereitstellung eines Eingabedatasets mit drei Spalten tritt ein Fehler wie der folgende auf:

```
All columns of the table must be specified in the INSERT BULK statement.
```

Der NULL-Wert ist eine Sonderform eines Standardwerts. Wenn die Spalte NULL-Werte zulässt, könnten die Eingabedaten (im Blob) für diese Spalte leer sein (sie dürfen im Eingabedataset nicht fehlen). PolyBase fügt für diese Daten in Azure SQL Data Warehouse den Wert „NULL“ ein.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Datentypzuordnung für Azure SQL Data Warehouse

Beim Kopieren von Daten aus bzw. nach Azure SQL Data Warehouse werden die folgenden Zuordnungen von Azure SQL Data Warehouse-Datentypen zu Azure Data Factory-Zwischendatentypen verwendet. Unter [Schema- und Datentypzuordnungen](copy-activity-schema-and-type-mapping.md) erfahren Sie, wie Sie Aktivitätszuordnungen für Quellschema und Datentyp in die Senke kopieren.

| Azure SQL Data Warehouse-Datentyp | Data Factory-Zwischendatentyp |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| Bit |Boolescher Wert |
| char |String, Char[] |
| date |Datetime |
| DateTime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| DECIMAL |DECIMAL |
| FILESTREAM-Attribut (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |DECIMAL |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |DECIMAL |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| smallint |Int16 |
| smallmoney |DECIMAL |
| sql_variant |Object * |
| text |String, Char[] |
| time |Zeitraum |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |xml |

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md##supported-data-stores-and-formats).
