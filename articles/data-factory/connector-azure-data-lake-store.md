---
title: Kopieren von Daten nach oder aus Azure Data Lake Storage Gen1 mit Data Factory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe von Data Factory Daten aus unterstützten Quelldatenspeichern nach Azure Data Lake Storage oder aus Data Lake Storage in unterstützte Senkenspeicher kopieren.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: e0626d847b22c11ce5acca5633c9b1291c03742d
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839874"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Kopieren von Daten nach und aus Azure Data Lake Storage Gen1 mithilfe von Azure Data Factory
> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version von Azure Data Factory aus:"]
> * [Version 1](v1/data-factory-azure-datalake-connector.md)
> * [Aktuelle Version](connector-azure-data-lake-store.md)

In diesem Artikel wird beschrieben, wie Sie Daten in und aus Azure Data Lake Storage Gen1 kopieren. Informationen zu Azure Data Factory finden Sie im [Einführungsartikel](introduction.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser Azure Data Lake Storage Gen1-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen oder Senken-Matrix](copy-activity-overview.md)
- [Mapping Data Flow](concepts-data-flow-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
- [GetMetadata-Aktivität](control-flow-get-metadata-activity.md)

Mit diesem Connector können Sie insbesondere:

- Kopieren von Dateien mit einer der folgenden Authentifizierungsmethoden: „Dienstprinzipal“ oder „verwaltete Identitäten für Azure-Ressourcen“.
- Dateien im jeweiligen Zustand oder Analysieren bzw. Generieren von Dateien mit den [unterstützten Dateiformaten und Codecs für die Komprimierung](supported-file-formats-and-compression-codecs.md) kopieren.

> [!IMPORTANT]
> Wenn Sie Daten mithilfe der selbstgehosteten Integration Runtime kopieren, konfigurieren Sie die Unternehmensfirewall so, dass sie an Port 443 ausgehenden Datenverkehr an `<ADLS account name>.azuredatalakestore.net` und `login.microsoftonline.com/<tenant>/oauth2/token` zulässt. Letzteres ist der Azure-Sicherheitstokendienst, mit dem die Integration Runtime kommunizieren muss, um das Zugriffstoken abzurufen.

## <a name="get-started"></a>Erste Schritte

> [!TIP]
> Eine exemplarische Vorgehensweise zur Verwendung des Azure Data Lake Storage-Connectors finden Sie unter [Laden von Daten in Azure Data Lake Storage Gen1](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Informationen zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für Data Lake Storage verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den verknüpften Azure Data Lake Storage-Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die `type`-Eigenschaft muss auf **AzureDataLakeStore** festgelegt werden. | Ja |
| dataLakeStoreUri | Informationen zum Azure Data Lake Store-Konto. Diese Informationen haben eines der folgenden Formate: `https://[accountname].azuredatalakestore.net/webhdfs/v1` oder `adl://[accountname].azuredatalakestore.net/`. | Ja |
| subscriptionId | Die ID des Azure-Abonnements, zu dem das Data Lake Storage-Konto gehört. | Erforderlich für Senke |
| resourceGroupName | Der Name der Azure-Ressourcengruppe, zu der das Data Lake Storage-Konto gehört. | Erforderlich für Senke |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Sie können die Azure Integration Runtime oder eine selbstgehostete Integration Runtime verwenden, sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet. Wenn diese Eigenschaft nicht angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein |

### <a name="use-service-principal-authentication"></a>Verwenden der Dienstprinzipalauthentifizierung

Wenn Sie die Dienstprinzipalauthentifizierung verwenden möchten, registrieren Sie in Azure Active Directory eine Anwendungsentität, und gewähren Sie ihr Zugriff auf Data Lake Storage. Eine ausführliche Anleitung finden Sie unter [Dienst-zu-Dienst-Authentifizierung](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Notieren Sie sich die folgenden Werte, die Sie zum Definieren des verknüpften Diensts verwenden:

- Anwendungs-ID
- Anwendungsschlüssel
- Mandanten-ID

>[!IMPORTANT]
> Erteilen Sie dem Dienstprinzipal die korrekte Berechtigung in Data Lake Storage:
>- **Als Quelle**: Erteilen Sie zum Auflisten und Kopieren der Dateien in Ordnern und Unterordnern unter **Daten-Explorer** > **Zugriff** mindestens die Berechtigung zum **Lesen und Ausführen**. Zum Kopieren einer einzelnen Datei können Sie auch die Berechtigung **Lesen** gewähren. Sie können für rekursives Kopieren „Hinzufügen zu“ auf **Diesen Ordner und alle untergeordneten Ordner** und „Hinzufügen als“ auf **Ein Zugriffsberechtigungseintrag und ein Standardberechtigungseintrag** festlegen. Es gelten keine Anforderungen für die Zugriffssteuerung (Identity & Access Management, IAM) auf Kontoebene.
>- **Als Senke**: Erteilen Sie zum Erstellen von untergeordneten Elementen im Ordner unter **Daten-Explorer** > **Zugriff** mindestens die Berechtigung zum **Schreiben und Ausführen**. Sie können für rekursives Kopieren „Hinzufügen zu“ auf **Diesen Ordner und alle untergeordneten Ordner** und „Hinzufügen als“ auf **Ein Zugriffsberechtigungseintrag und ein Standardberechtigungseintrag** festlegen. Wenn Sie eine Azure Integration Runtime zum Kopieren verwenden (sowohl Quelle als auch Senke befinden sich in der Cloud), erteilen Sie in IAM mindestens die Rolle **Leser**, um Data Factory die Erkennung der Region für Data Lake Storage zu ermöglichen. Wenn Sie diese IAM-Rolle vermeiden möchten, [erstellen Sie explizit eine Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) mit dem Speicherort von Data Lake Storage. Wenn Ihr Data Lake Storage sich in Westeuropa befindet, erstellen Sie eine Azure Integration Runtime, deren Ort auf „Europa, Westen“ festgelegt ist. Erstellen Sie wie im folgenden Beispiel gezeigt eine Zuordnung im verknüpften Data Lake Storage-Dienst.

>[!NOTE]
>Zum Auflisten von Ordnern ab dem Stamm müssen Sie die Berechtigung, die dem Dienstprinzipal gewährt wird, **auf der Stammebene mit der Berechtigung „Ausführen“** festlegen. Dies gilt, wenn Sie:
>- Das **Tool zum Kopieren von Daten** verwenden, um eine Kopierpipeline zu erstellen.
>- Die **Data Factory-Benutzeroberfläche** verwenden, um die Verbindung zu testen und während der Erstellung in Ordnern zu navigieren
>Wenn Sie Bedenken haben, Berechtigungen auf Stammebene zu vergeben, können Sie die Testverbindung während der Erstellung überspringen und einen übergeordneten Pfad mit Berechtigung erstellen, um dann von diesem Pfad aus zu suchen. Kopieraktivitäten funktionieren, solange dem Dienstprinzipal die erforderlichen Berechtigungen für die zu kopierenden Dateien erteilt wurden.

Folgende Eigenschaften werden unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| servicePrincipalId | Geben Sie die Client-ID der Anwendung an. | Ja |
| servicePrincipalKey | Geben Sie den Schlüssel der Anwendung an. Markieren Sie dieses Feld als `SecureString`, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Ja |
| tenant | Geben Sie die Mandanteninformationen, wie Domänenname oder Mandanten-ID, für Ihre Anwendung an. Diese können Sie abrufen, indem Sie im Azure-Portal mit der Maus auf den Bereich oben rechts zeigen. | Ja |

**Beispiel:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> So verwenden Sie verwaltete Identitäten für die Azure-Ressourcenauthentifizierung

Eine Data Factory kann einer [verwalteten Identität für Azure-Ressourcen](data-factory-service-identity.md) zugeordnet werden, die diese spezielle Data Factory darstellt. Ähnlich wie bei der Verwendung Ihres eigenen Dienstprinzipals können Sie diese verwaltete Identität direkt für die Data Lake Storage-Authentifizierung verwenden. Sie erlaubt dieser bestimmten Factory den Zugriff auf Data Lake Storage sowie das Kopieren von Daten nach oder aus Data Lake Storage.

So verwenden Sie verwaltete Identitäten für die Azure-Ressourcenauthentifizierung:

1. [Rufen Sie die verwalteten Data Factory-Identitätsinformationen ab](data-factory-service-identity.md#retrieve-managed-identity), indem Sie den Wert von „Dienstidentitätsanwendungs-ID“ kopieren, der zusammen mit der Factory generiert wurde.
2. Gewähren Sie der verwalteten Identität auf dieselbe Weise wie beim Dienstprinzipal Zugriff auf Data Lake Storage, und beachten Sie dabei die folgenden Hinweise.

>[!IMPORTANT]
> Stellen Sie sicher, dass Sie der verwalteten Data Factory-Identität die richtige Berechtigung in Data Lake Storage gewähren:
>- **Als Quelle**: Erteilen Sie zum Auflisten und Kopieren der Dateien in Ordnern und Unterordnern unter **Daten-Explorer** > **Zugriff** mindestens die Berechtigung zum **Lesen und Ausführen**. Zum Kopieren einer einzelnen Datei können Sie auch die Berechtigung **Lesen** gewähren. Sie können für rekursives Kopieren „Hinzufügen zu“ auf **Diesen Ordner und alle untergeordneten Ordner** und „Hinzufügen als“ auf **Ein Zugriffsberechtigungseintrag und ein Standardberechtigungseintrag** festlegen. Es gelten keine Anforderungen für die Zugriffssteuerung (Identity & Access Management, IAM) auf Kontoebene.
>- **Als Senke**: Erteilen Sie zum Erstellen von untergeordneten Elementen im Ordner unter **Daten-Explorer** > **Zugriff** mindestens die Berechtigung zum **Schreiben und Ausführen**. Sie können für rekursives Kopieren „Hinzufügen zu“ auf **Diesen Ordner und alle untergeordneten Ordner** und „Hinzufügen als“ auf **Ein Zugriffsberechtigungseintrag und ein Standardberechtigungseintrag** festlegen. Wenn Sie eine Azure Integration Runtime zum Kopieren verwenden (sowohl Quelle als auch Senke befinden sich in der Cloud), erteilen Sie in IAM mindestens die Rolle **Leser**, um Data Factory die Erkennung der Region für Data Lake Storage zu ermöglichen. Wenn Sie diese IAM-Rolle vermeiden möchten, [erstellen Sie explizit eine Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) mit dem Speicherort von Data Lake Storage. Erstellen Sie wie im folgenden Beispiel gezeigt eine Zuordnung im verknüpften Data Lake Storage-Dienst.

>[!NOTE]
>Zum Auflisten von Ordnern ab dem Stamm müssen Sie die Berechtigung, die der verwalteten Identität gewährt wird, **auf der Stammebene mit der Berechtigung „Ausführen“** festlegen. Dies gilt, wenn Sie:
>- Das **Tool zum Kopieren von Daten** verwenden, um eine Kopierpipeline zu erstellen.
>- Die **Data Factory-Benutzeroberfläche** verwenden, um die Verbindung zu testen und während der Erstellung in Ordnern zu navigieren
>Wenn Sie Bedenken haben, Berechtigungen auf Stammebene zu vergeben, können Sie die Testverbindung während der Erstellung überspringen und einen übergeordneten Pfad mit Berechtigung erstellen, um dann von diesem Pfad aus zu suchen. Kopieraktivitäten funktionieren, solange dem Dienstprinzipal die erforderlichen Berechtigungen für die zu kopierenden Dateien erteilt wurden.

In Azure Data Factory müssen Sie außer den allgemeinen Data Lake Storage-Informationen im verknüpften Dienst keine Eigenschaften angeben.

**Beispiel:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). 

- Informationen zum **Parquet-Format, Textformat mit Trennzeichen und Binärformat** finden Sie im Abschnitt [Dataset für Parquet-Format, Textformat mit Trennzeichen und Binärformat](#format-based-dataset).
- Informationen zu anderen Formaten wie **ORC/Avro/JSON** finden Sie im Abschnitt [Dataset in anderen Formaten](#other-format-dataset).

### <a name="format-based-dataset"></a> Dataset für Parquet-Format, Textformat mit Trennzeichen und Binärformat

Informationen zum Kopieren von Daten in das und aus dem **Parquet-Format, Textformat mit Trennzeichen oder Binärformat** finden Sie in den Artikeln [Parquet-Format](format-parquet.md), [Textformat mit Trennzeichen](format-delimited-text.md) und [Binärformat](format-binary.md) zu formatbasierten Datasets und unterstützten Einstellungen.
Folgende Eigenschaften werden für Azure Data Lake Storage Gen1 unter `location`-Einstellungen in formatbasierten Datasets unterstützt:

| Eigenschaft   | BESCHREIBUNG                                                  | Erforderlich |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Die type-Eigenschaft unter `location` im Dataset muss auf **AzureDataLakeStoreLocation** festgelegt werden. | Ja      |
| folderPath | Der Pfad zu einem Ordner. Wenn Sie einen Platzhalter verwenden möchten, um Ordner zu filtern, überspringen Sie diese Einstellung, und geben Sie entsprechende Aktivitätsquelleneinstellungen an. | Nein       |
| fileName   | Der Name der Datei unter dem angegebenen „folderPath“. Wenn Sie einen Platzhalter verwenden möchten, um Ordner zu filtern, überspringen Sie diese Einstellung, und geben Sie ihn in den entsprechenden Aktivitätsquelleneinstellungen an. | Nein       |

> [!NOTE]
>
> Das Dataset vom Typ **AzureDataLakeStoreFile** mit dem im nächsten Abschnitt beschriebenen Parquet-Format/Textformat wird aus Gründen der Abwärtskompatibilität weiterhin unverändert für Kopieren-/Suchen-/GetMetadata-Aktivitäten unterstützt. Dies funktioniert jedoch nicht mit der Funktion zur Zuordnung von Datenflüssen. Wir empfehlen, dass Sie in Zukunft dieses neue Modell verwenden. Die Benutzeroberfläche für die Erstellung von Data Factory generiert diese neuen Typen.

**Beispiel:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen1 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureDataLakeStoreLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="other-format-dataset"></a>Dataset in anderen Formaten

Zum Kopieren von Daten in und aus Azure Data Lake Storage Gen1 im **ORC-, Avro- oder JSON-Format** werden die folgenden Eigenschaften unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf **AzureDataLakeStoreFile** festgelegt werden. |Ja |
| folderPath | Pfad zum Ordner in Data Lake Storage. Wenn keine Angabe vorhanden ist, wird auf das Stammverzeichnis verwiesen. <br/><br/>Der Platzhalterfilter wird unterstützt. Zulässige Platzhalter sind `*` (entspricht null [0] oder mehr Zeichen) und `?` (entspricht null [0] oder einem einzelnen Zeichen). Verwenden Sie `^` als Escapezeichen, wenn der tatsächliche Ordnername einen Platzhalter oder dieses Escapezeichen enthält. <br/><br/>Zum Beispiel: „Stammordner/Unterordner/“. Weitere Beispiele finden Sie unter [Beispiele für Ordner- und Dateifilter](#folder-and-file-filter-examples). |Nein |
| fileName | Name oder Platzhalterfilter für die Dateien unter dem angegebenen Wert für „folderPath“. Wenn Sie für diese Eigenschaft keinen Wert angeben, verweist das Dataset auf alle Dateien im Ordner. <br/><br/>Für Filter sind die Platzhalter `*` (entspricht null [0] oder mehr Zeichen) und `?` (entspricht null [0] oder einem einzelnen Zeichen) zulässig.<br/>- Beispiel 1: `"fileName": "*.csv"`<br/>- Beispiel 2: `"fileName": "???20180427.txt"`<br/>Verwenden Sie `^` als Escapezeichen, wenn der tatsächliche Dateiname einen Platzhalter oder dieses Escapezeichen enthält.<br/><br/>Wenn „fileName“ nicht für ein Ausgabedataset und **preserveHierarchy** nicht in der Aktivitätssenke angegeben sind, generiert die Kopieraktivität den Dateinamen automatisch mit dem folgenden Muster: „*Data.[GUID der Aktivitätsausführungs-ID].[GUID, sofern „FlattenHierarchy“].[Format, sofern konfiguriert].[Komprimierung, sofern konfiguriert]* “, z. B. „Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz“. Wenn Sie Daten aus einer Quelle im Tabellenformat kopieren und dabei anstelle einer Abfrage den Tabellennamen verwenden, lautet das Namensmuster „ *[Tabellenname].[ Format].[Komprimierung, sofern konfiguriert]* “, z. B. „MyTable.csv“. |Nein |
| modifiedDatetimeStart | Dateifilterung basierend auf dem Attribut „Letzte Änderung“. Die Dateien werden ausgewählt, wenn der Zeitpunkt der letzten Änderung innerhalb des Zeitbereichs zwischen `modifiedDatetimeStart` und `modifiedDatetimeEnd` liegt. Die Zeit wird auf die UTC-Zeitzone im Format „2018-12-01T05:00:00Z“ angewandt. <br/><br/> Die generelle Leistung der Datenverschiebung wird beeinträchtigt, wenn Sie diese Einstellung aktivieren und eine Dateifilterung für eine große Zahl von Dateien vornehmen möchten. <br/><br/> Die Eigenschaften können NULL sein, was bedeutet, dass kein Dateiattributfilter auf das Dataset angewandt wird. Wenn `modifiedDatetimeStart` einen datetime-Wert aufweist, aber `modifiedDatetimeEnd` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung größer oder gleich dem datetime-Wert ist. Wenn `modifiedDatetimeEnd` einen datetime-Wert aufweist, aber `modifiedDatetimeStart` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung kleiner als der datetime-Wert ist.| Nein |
| modifiedDatetimeEnd | Dateifilterung basierend auf dem Attribut „Letzte Änderung“. Die Dateien werden ausgewählt, wenn der Zeitpunkt der letzten Änderung innerhalb des Zeitbereichs zwischen `modifiedDatetimeStart` und `modifiedDatetimeEnd` liegt. Die Zeit wird auf die UTC-Zeitzone im Format „2018-12-01T05:00:00Z“ angewandt. <br/><br/> Die generelle Leistung der Datenverschiebung wird beeinträchtigt, wenn Sie diese Einstellung aktivieren und eine Dateifilterung für eine große Zahl von Dateien vornehmen möchten. <br/><br/> Die Eigenschaften können NULL sein, was bedeutet, dass kein Dateiattributfilter auf das Dataset angewandt wird. Wenn `modifiedDatetimeStart` einen datetime-Wert aufweist, aber `modifiedDatetimeEnd` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung größer oder gleich dem datetime-Wert ist. Wenn `modifiedDatetimeEnd` einen datetime-Wert aufweist, aber `modifiedDatetimeStart` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung kleiner als der datetime-Wert ist.| Nein |
| format | Wenn Sie Dateien unverändert zwischen dateibasierten Speichern kopieren möchten (binäre Kopie), können Sie den Formatabschnitt bei den Definitionen von Eingabe- und Ausgabedatasets überspringen.<br/><br/>Für das Analysieren oder Generieren von Dateien mit einem bestimmten Format werden die folgenden Dateiformattypen unterstützt: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** und **ParquetFormat**. Sie müssen die **type**-Eigenschaft unter **format** auf einen dieser Werte festlegen. Weitere Informationen finden Sie in den Abschnitten [Textformat](supported-file-formats-and-compression-codecs.md#text-format), [JSON-Format](supported-file-formats-and-compression-codecs.md#json-format), [Avro-Format](supported-file-formats-and-compression-codecs.md#avro-format), [Orc-Format](supported-file-formats-and-compression-codecs.md#orc-format) und [Parquet-Format](supported-file-formats-and-compression-codecs.md#parquet-format). |Nein (nur für Szenarien mit Binärkopien) |
| compression | Geben Sie den Typ und den Grad der Komprimierung für die Daten an. Weitere Informationen finden Sie unter [Unterstützte Dateiformate und Codecs für die Komprimierung](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Unterstützte Typen sind **GZip**, **Deflate**, **BZIP2** und **ZipDeflate**.<br/>Unterstützte Grade sind **Optimal** und **Schnellste**. |Nein |


>[!TIP]
>Wenn Sie alle Dateien eines Ordners kopieren möchten, geben Sie nur **folderPath** an.<br>Wenn Sie eine einzelne Datei mit einem bestimmten Namen kopieren möchten, geben Sie **folderPath** mit einem Ordner und **fileName** mit einem Dateinamen an.<br>Wenn Sie eine Teilmenge der Dateien eines Ordners kopieren möchten, geben Sie **folderPath** mit einem Ordner und **fileName** mit einem Platzhalterfilter an. 

**Beispiel:**

```json
{
    "name": "ADLSDataset",
    "properties": {
        "type": "AzureDataLakeStoreFile",
        "linkedServiceName":{
            "referenceName": "<ADLS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "datalake/myfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste der verfügbaren Abschnitte und Eigenschaften zum Definieren von Aktivitäten finden Sie unter [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Azure Data Lake Storage-Quelle und -Senke unterstützt werden.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store als Quelle

- Informationen zum Kopieren aus dem **Parquet-Format, Textformat mit Trennzeichen und Binärformat** finden Sie im Abschnitt [Quelle im Parquet-Format, Textformat mit Trennzeichen und Binärformat](#format-based-source).
- Informationen zum Kopieren aus anderen Formaten wie **ORC/Avro/JSON** finden Sie im Abschnitt [Quelle in anderen Formaten](#other-format-source).

#### <a name="format-based-source"></a> Quelle im Parquet-Format, Textformat mit Trennzeichen und Binärformat

Informationen zum Kopieren von Daten aus dem **Parquet-Format, Textformat mit Trennzeichen oder Binärformat** finden Sie in den Artikeln [Parquet-Format](format-parquet.md), [Textformat mit Trennzeichen](format-delimited-text.md) und [Binärformat](format-binary.md) zu formatbasierten Quellen für Kopieraktivitäten und unterstützten Einstellungen.  Folgende Eigenschaften werden für Azure Data Lake Storage Gen1 unter `storeSettings`-Einstellungen in der formatbasierten Kopierquelle unterstützt:

| Eigenschaft                 | BESCHREIBUNG                                                  | Erforderlich                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Die type-Eigenschaft unter `storeSettings` muss auf **AzureDataLakeStoreReadSetting** festgelegt werden. | Ja                                           |
| recursive                | Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. Wenn „recursive“ auf „true“ festgelegt ist und es sich bei der Senke um einen dateibasierten Speicher handelt, wird ein leerer Ordner oder Unterordner nicht in die Senke kopiert und dort auch nicht erstellt. Zulässige Werte sind **true** (Standard) und **false**. | Nein                                            |
| wildcardFolderPath       | Der Ordnerpfad mit Platzhalterzeichen, um Quellordner zu filtern. <br>Zulässige Platzhalter sind `*` (entspricht null [0] oder mehr Zeichen) und `?` (entspricht null [0] oder einem einzelnen Zeichen). Verwenden Sie `^` als Escapezeichen, wenn der tatsächliche Ordnername einen Platzhalter oder dieses Escapezeichen enthält. <br>Weitere Beispiele finden Sie unter [Beispiele für Ordner- und Dateifilter](#folder-and-file-filter-examples). | Nein                                            |
| wildcardFileName         | Der Dateiname mit Platzhalterzeichen unter dem angegebenen „folderPath/wildcardFolderPath“ für das Filtern von Quelldateien. <br>Zulässige Platzhalter sind `*` (entspricht null [0] oder mehr Zeichen) und `?` (entspricht null [0] oder einem einzelnen Zeichen). Verwenden Sie `^` als Escapezeichen, wenn der tatsächliche Ordnername einen Platzhalter oder dieses Escapezeichen enthält. Weitere Beispiele finden Sie unter [Beispiele für Ordner- und Dateifilter](#folder-and-file-filter-examples). | Ja, wenn `fileName` nicht im Dataset angegeben ist |
| modifiedDatetimeStart    | Dateifilterung basierend auf dem Attribut „Letzte Änderung“. Die Dateien werden ausgewählt, wenn der Zeitpunkt der letzten Änderung innerhalb des Zeitbereichs zwischen `modifiedDatetimeStart` und `modifiedDatetimeEnd` liegt. Die Zeit wird auf die UTC-Zeitzone im Format „2018-12-01T05:00:00Z“ angewandt. <br> Die Eigenschaften können NULL sein, was bedeutet, dass kein Dateiattributfilter auf das Dataset angewandt wird. Wenn `modifiedDatetimeStart` einen datetime-Wert aufweist, aber `modifiedDatetimeEnd` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung größer oder gleich dem datetime-Wert ist. Wenn `modifiedDatetimeEnd` einen datetime-Wert aufweist, aber `modifiedDatetimeStart` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung kleiner als der datetime-Wert ist. | Nein                                            |
| modifiedDatetimeEnd      | Wie oben.                                               | Nein                                            |
| maxConcurrentConnections | Die Anzahl von Verbindungen, die gleichzeitig mit einem Speicher hergestellt werden können. Geben Sie diesen Wert nur an, wenn Sie die gleichzeitigen Verbindungen mit dem Datenspeicher begrenzen möchten. | Nein                                            |

> [!NOTE]
> Beim Parquet-Format/Textformat mit Trennzeichen wird die im nächsten Abschnitt beschriebene Quelle der Kopieraktivität vom Typ **„AzureDataLakeStoreSource“** aus Gründen der Abwärtskompatibilität unverändert unterstützt. Wir empfehlen, dass Sie in Zukunft dieses neue Modell verwenden. Die Benutzeroberfläche für die Erstellung von Data Factory generiert diese neuen Typen.

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureDataLakeStoreReadSetting",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="other-format-source"></a>Quelle in anderen Formaten

Zum Kopieren von Daten aus Azure Data Lake Storage Gen1 im **ORC-, Avro- oder JSON-Format** werden die folgenden Eigenschaften im Abschnitt **source** der Copy-Aktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die `type`-Eigenschaft der Quelle der Kopieraktivität muss auf **AzureDataLakeStoreSource** festgelegt werden. |Ja |
| recursive | Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. Wenn `recursive` auf „true“ festgelegt ist und es sich bei der Senke um einen dateibasierten Speicher handelt, wird ein leerer Ordner oder Unterordner nicht in die Senke kopiert oder dort erstellt. Zulässige Werte sind **true** (Standard) und **false**. | Nein |
| maxConcurrentConnections | Die Anzahl von Verbindungen, die gleichzeitig mit dem Datenspeicher hergestellt werden können. Geben Sie diesen Wert nur an, wenn Sie die gleichzeitigen Verbindungen mit dem Datenspeicher begrenzen möchten. | Nein |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen1 input dataset name>",
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
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store als Senke

- Informationen zum Kopieren in das **Parquet-Format, Textformat mit Trennzeichen oder Binärformat** finden Sie im Abschnitt [Senke im Parquet-Format, Textformat mit Trennzeichen und Binärformat](#format-based-sink).
- Informationen zum Kopieren in andere Formate wie **ORC/Avro/JSON** finden Sie im Abschnitt [Senke in anderen Formaten](#other-format-sink).

#### <a name="format-based-sink"></a> Senke im Parquet-Format, Textformat mit Trennzeichen und Binärformat

Informationen zum Kopieren von Daten in das **Parquet-Format, Textformat mit Trennzeichen oder Binärformat** finden Sie in den Artikeln [Parquet-Format](format-parquet.md), [Textformat mit Trennzeichen](format-delimited-text.md) und [Binärformat](format-binary.md) zu formatbasierten Senken für Kopieraktivitäten und unterstützten Einstellungen.  Folgende Eigenschaften werden für Azure Data Lake Storage Gen1 unter `storeSettings`-Einstellungen in der formatbasierten Kopiersenke unterstützt:

| Eigenschaft                 | BESCHREIBUNG                                                  | Erforderlich |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Die type-Eigenschaft unter `storeSettings` muss auf **AzureDataLakeStoreWriteSetting** festgelegt werden. | Ja      |
| copyBehavior             | Definiert das Kopierverhalten, wenn es sich bei der Quelle um Dateien aus einem dateibasierten Datenspeicher handelt.<br/><br/>Zulässige Werte sind:<br/><b>- PreserveHierarchy (Standard)</b>: Behält die Dateihierarchie im Zielordner bei. Der relative Pfad der Quelldatei zum Quellordner ist mit dem relativen Pfad der Zieldatei zum Zielordner identisch.<br/><b>- FlattenHierarchy</b>: Alle Dateien aus dem Quellordner befinden sich auf der ersten Ebene des Zielordners. Die Namen für die Zieldateien werden automatisch generiert. <br/><b>- MergeFiles</b>: Alle Dateien aus dem Quellordner werden in einer Datei zusammengeführt. Wenn der Dateiname angegeben wurde, entspricht der zusammengeführte Dateiname dem angegebenen Namen. Andernfalls wird der Dateiname automatisch generiert. | Nein       |
| maxConcurrentConnections | Die Anzahl von Verbindungen, die gleichzeitig mit dem Datenspeicher hergestellt werden können. Geben Sie diesen Wert nur an, wenn Sie die gleichzeitigen Verbindungen mit dem Datenspeicher begrenzen möchten. | Nein       |

> [!NOTE]
> Beim Parquet-Format/Textformat mit Trennzeichen wird die im nächsten Abschnitt beschriebene Quelle der Kopieraktivitätssenke vom Typ **„AzureDataLakeStoreSink“** aus Gründen der Abwärtskompatibilität unverändert unterstützt. Wir empfehlen, dass Sie in Zukunft dieses neue Modell verwenden. Die Benutzeroberfläche für die Erstellung von Data Factory generiert diese neuen Typen.

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureDataLakeStoreWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>Senke in anderen Formaten

Zum Kopieren von Daten in Azure Data Lake Storage Gen1 im **ORC-, Avro- oder JSON-Format** werden die folgenden Eigenschaften im Abschnitt **sink** unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die `type`-Eigenschaft der Senke der Kopieraktivität muss auf **„AzureDataLakeStoreSink“** festgelegt werden. |Ja |
| copyBehavior | Definiert das Kopierverhalten, wenn es sich bei der Quelle um Dateien aus einem dateibasierten Datenspeicher handelt.<br/><br/>Zulässige Werte sind:<br/><b>- PreserveHierarchy (Standard)</b>: Behält die Dateihierarchie im Zielordner bei. Der relative Pfad der Quelldatei zum Quellordner ist mit dem relativen Pfad der Zieldatei zum Zielordner identisch.<br/><b>- FlattenHierarchy</b>: Alle Dateien aus dem Quellordner befinden sich auf der ersten Ebene des Zielordners. Die Namen für die Zieldateien werden automatisch generiert. <br/><b>- MergeFiles</b>: Alle Dateien aus dem Quellordner werden in einer Datei zusammengeführt. Wenn der Dateiname angegeben wurde, entspricht der zusammengeführte Dateiname dem angegebenen Namen. Andernfalls wird der Dateiname automatisch generiert. | Nein |
| maxConcurrentConnections | Die Anzahl von Verbindungen, die gleichzeitig mit dem Datenspeicher hergestellt werden können. Geben Sie diesen Wert nur an, wenn Sie die gleichzeitigen Verbindungen mit dem Datenspeicher begrenzen möchten. | Nein |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen1 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataLakeStoreSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Beispiele für Ordner- und Dateifilter

Dieser Abschnitt beschreibt das resultierende Verhalten für den Ordnerpfad und den Dateinamen mit Platzhalterfiltern.

| folderPath | fileName | recursive | Quellordnerstruktur und Filterergebnis (Dateien mit **Fettformatierung** werden abgerufen.)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Leer, Standardwert verwenden) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5.csv<br/>AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |
| `Folder*` | (Leer, Standardwert verwenden) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei5.csv**<br/>AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5.csv<br/>AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei5.csv**<br/>AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Beispiele für das Verhalten des Kopiervorgangs

In diesem Abschnitt wird das resultierende Verhalten des Kopiervorgangs für verschiedene Kombinationen von `recursive`- und `copyBehavior`-Werten beschrieben.

| recursive | copyBehavior | Struktur des Quellordners | Resultierendes Ziel |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei5 | Der Zielordner „Ordner1“ wird mit der gleichen Struktur erstellt wie die Quelle:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei5 | Der Zielordner „Ordner1“ wird mit der folgenden Struktur erstellt: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei5 | Der Zielordner „Ordner1“ wird mit der folgenden Struktur erstellt: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Die Inhalte von Datei1 + Datei2 + Datei3 + Datei4 + Datei5 werden in einer Datei mit einem automatisch generierten Dateinamen zusammengeführt. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei5 | Der Zielordner „Ordner1“ wird mit der folgenden Struktur erstellt:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/><br/>Unterordner1 mit Datei3, Datei4 und Datei5 wird nicht übernommen. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei5 | Der Zielordner „Ordner1“ wird mit der folgenden Struktur erstellt:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei2<br/><br/>Unterordner1 mit Datei3, Datei4 und Datei5 wird nicht übernommen. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei5 | Der Zielordner „Ordner1“ wird mit der folgenden Struktur erstellt:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Die Inhalte von Datei1 + Datei2 werden in einer Datei mit einem automatisch generierten Dateinamen zusammengeführt. Automatisch generierter Name für Datei1<br/><br/>Unterordner1 mit Datei3, Datei4 und Datei5 wird nicht übernommen. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Bewahren von Zugriffssteuerungslisten für Data Lake Storage Gen2

Wenn Sie die Zugriffssteuerungslisten zusammen mit Datendateien beim Upgrade von Data Lake Storage Gen1 auf Gen2 replizieren möchten, finden Sie weitere Informationen unter [Bewahren von Zugriffssteuerungslisten von Data Lake Storage Gen1](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1).

## <a name="mapping-data-flow-properties"></a>Eigenschaften von Mapping Data Flow

Mehr über [Quellentransformation](data-flow-source.md) und [Senkentransformation](data-flow-sink.md) finden Sie in der Funktion „Mapping Data Flow“.

## <a name="next-steps"></a>Nächste Schritte

Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md##supported-data-stores-and-formats).
