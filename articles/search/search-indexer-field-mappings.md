---
title: Feldzuordnungen für die automatisierte Indizierung mithilfe von Indexern – Azure Search
description: Konfigurieren Sie Feldzuordnungen im Azure Search-Indexer zum Ausgleichen von Unterschieden in Feldnamen und Datendarstellungen.
ms.date: 05/02/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 3546e342b535a122ea4ed3f844cd5e28a76d551a
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147788"
---
# <a name="field-mappings-and-transformations-using-azure-search-indexers"></a>Feldzuordnungen und Transformationen mithilfe von Azure Search-Indexern

Bei Verwendung von Azure Search-Indexern kommt es gelegentlich vor, dass die Eingabedaten nicht ganz dem Schema des Zielindex entsprechen. In diesen Fällen können Sie **Feldzuordnungen** verwenden, um Ihre Daten während der Indizierung neu zu strukturieren.

Feldzuordnungen sind beispielsweise in folgenden Situationen hilfreich:

* Ihre Datenquelle enthält ein Feld namens `_id`, aber Azure Search lässt keine Feldnamen zu, die mit einem Unterstrich beginnen. Mit einer Feldzuordnung können Sie ein Feld effektiv umbenennen.
* Sie möchten im Index mehrere Felder aus der gleichen Datenquelle auffüllen. Beispielsweise möchten Sie verschiedene Analysen auf diese Felder anwenden.
* Sie möchten ein Indexfeld mit Daten aus mehreren Datenquellen auffüllen, und in den einzelnen Datenquellen werden unterschiedliche Feldnamen verwendet.
* Sie müssen Ihre Daten mit Base64 codieren oder decodieren. Feldzuordnungen unterstützen mehrere **Zuordnungsfunktionen**, einschließlich Funktionen für die Base64-Codierung und -Decodierung.

> [!NOTE]
> Die Feldzuordnungsfunktion von Azure Search-Indexern bietet eine einfache Möglichkeit zum Zuordnen von Datenfeldern zu Indexfeldern mit ein paar Optionen für die Datenkonvertierung. Komplexere Daten müssen möglicherweise vorverarbeitet werden, um sie in eine einfach zu indizierende Form umzuwandeln.
>
> Microsoft Azure Data Factory ist eine leistungsstarke cloudbasierte Lösung für das Importieren und Transformieren von Daten. Sie können auch Code zum Transformieren der Quelldaten vor der Indizierung schreiben. Codebeispiele finden Sie unter [Modellieren von relationalen Daten](search-example-adventureworks-modeling.md) und [Modellieren von Facets mit mehreren Ebenen](search-example-adventureworks-multilevel-faceting.md).
>

## <a name="set-up-field-mappings"></a>Einrichten von Feldzuordnungen

Eine Feldzuordnung besteht aus drei Teilen:

1. Einem `sourceFieldName`, der ein Feld in der Datenquelle darstellt. Diese Eigenschaft ist obligatorisch.
2. Einem optionalen `targetFieldName`, der ein Feld im Suchindex darstellt. Wenn dieser nicht angegeben wird, wird derselbe Name wie in der Datenquelle verwendet.
3. Einer optionalen `mappingFunction`, die die Daten mit einer von mehreren vordefinierten Funktionen transformieren kann. Die vollständige Liste der Funktionen finden Sie [unten](#mappingFunctions).

Feldzuordnungen werden dem Array `fieldMappings` der Indexerdefinition hinzugefügt.

## <a name="map-fields-using-the-rest-api"></a>Zuordnen von Feldern mithilfe der REST-API

Sie können Feldzuordnungen beim Erstellen eines neuen Indexers mithilfe der API-Anforderung [Indexer erstellen](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) hinzufügen. Sie können die Feldzuordnungen eines vorhandenen Indexers mithilfe der API-Anforderung [Indexer aktualisieren](https://docs.microsoft.com/rest/api/searchservice/update-indexer) verwalten.

Aus dem nachstehenden Beispiel geht hervor, wie Sie ein Quellfeld einem Zielfeld mit einem anderen Namen zuordnen:

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
}
```

Auf ein Quellfeld kann in mehreren Feldzuordnungen verwiesen werden. Im folgenden Beispiel wird gezeigt, wie Sie ein Feld „forken“, indem Sie das gleiche Quellfeld in zwei verschiedene Indexfelder kopieren:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Azure Search verwendet einen von Groß- und Kleinschreibung unabhängigen Vergleich zum Auflösen von Feld- und Funktionsnamen in Feldzuordnungen. Dies ist zwar praktisch, weil Sie die Groß- und Kleinschreibung nicht berücksichtigen müssen, bedeutet aber, dass die Datenquelle oder der Index keine Felder aufweisen dürfen, die sich nur in der Groß- und Kleinschreibung unterscheiden.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>Zuordnen von Feldern mithilfe des .NET SDK

Sie definieren Feldzuordnungen im .NET SDK mit der [FieldMapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping)-Klasse. Diese weist die Eigenschaften `SourceFieldName` und `TargetFieldName` und einen optionalen `MappingFunction`-Verweis auf.

Sie können Feldzuordnungen beim Erstellen des Indexers oder später durch direktes Festlegen der `Indexer.FieldMappings`-Eigenschaft angeben.

Im folgenden C#-Beispiel werden die Feldzuordnungen beim Erstellen eines Indexers festgelegt.

```csharp
  List<FieldMapping> map = new List<FieldMapping> {
    // removes a leading underscore from a field name
    new FieldMapping("_custId", "custId"),
    // URL-encodes a field for use as the index key
    new FieldMapping("docPath", "docId", FieldMappingFunction.Base64Encode() )
  };

  Indexer sqlIndexer = new Indexer(
    name: "azure-sql-indexer",
    dataSourceName: sqlDataSource.Name,
    targetIndexName: index.Name,
    fieldMappings: map,
    schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

  await searchService.Indexers.CreateOrUpdateAsync(indexer);
```

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Feldzuordnungsfunktionen

Eine Feldzuordnungsfunktion transformiert den Inhalt eines Felds, bevor es im Index gespeichert wird. Die folgenden Zuordnungsfunktionen werden derzeit unterstützt:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>Funktion „base64Encode“

Führt eine *URL-sichere* Base64-Codierung der Eingabezeichenfolge durch. Geht davon aus, dass die Eingabe mit UTF-8 codiert ist.

#### <a name="example---document-key-lookup"></a>Beispiel: Dokumentschlüsselsuche

Nur URL-sichere Zeichen können in einem Azure Search-Dokumentschlüssel enthalten sein (weil Kunden in der Lage sein müssen, das Dokument über die [Lookup-API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) aufrufen zu können). Wenn das Quellfeld für den Schlüssel URL-unsichere Zeichen enthält, können Sie die Funktion `base64Encode` verwenden, um die Zeichenfolge bei der Indizierung zu konvertieren.

Wenn Sie den codierten Schlüssel während der Suche abrufen, können Sie die Funktion `base64Decode` verwenden, um den ursprünglichen Schlüsselwert abzurufen, mit dem Sie dann das Quelldokument abrufen können.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "base64Encode",
      "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false }
    }
  }]
 ```

Wenn Sie keine parameters-Eigenschaft für Ihre Zuordnungsfunktion angeben, wird standardmäßig der Wert `{"useHttpServerUtilityUrlTokenEncode" : true}` verwendet.

Azure Search unterstützt zwei verschiedene Base64-Codierungen. Beim Codieren und Decodieren eines bestimmten Felds sollten Sie dieselben Parameter verwenden. Weitere Informationen, anhand der Sie entscheiden können, welche Parameter verwendet werden sollen, finden Sie unter [Base64-Codierungsoptionen](#base64details).

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>Funktion „base64Decode“

Führt die Base64-Decodierung der Eingabezeichenfolge durch. Es wird davon ausgegangen, dass es sich bei der Eingabe um eine *URL-sichere* Base64-codierte Zeichenfolge handelt.

#### <a name="example---decode-blob-metadata-or-urls"></a>Beispiel: Decodieren von Blobmetadaten oder URLs

Ihre Quelldaten enthalten möglicherweise Base64-codierte Zeichenfolgen wie Blobmetadaten-Zeichenfolgen oder Web-URLs, die Sie konvertieren möchten, damit sie als Nur-Text durchsuchbar sind. Mit der Funktion `base64Decode` können Sie die codierten Daten beim Auffüllen des Suchindex wieder in „normale“ Zeichenfolgen umwandeln.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { 
      "name" : "base64Decode", 
      "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false }
    }
  }]
```

Wenn Sie keine parameters-Eigenschaft angeben, wird standardmäßig der Wert `{"useHttpServerUtilityUrlTokenEncode" : true}` verwendet.

Azure Search unterstützt zwei verschiedene Base64-Codierungen. Verwenden Sie beim Codieren und Decodieren eines bestimmten Felds dieselben Parameter. Ausführlichere Informationen, anhand der Sie entscheiden können, welche Parameter verwendet werden sollen, finden Sie unter [Base64-Codierungsoptionen](#base64details).

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>Base64-Codierungsoptionen

Azure Search unterstützt zwei verschiedene Base64-Codierungen: das **HttpServerUtility-URL-Token** und die **URL-sichere Base64-Codierung ohne Auffüllung**. Eine während der Indizierung base64-codierte Zeichenfolge muss später mit denselben Codierungsoptionen decodiert werden. Andernfalls stimmt das Ergebnis nicht mit dem ursprünglichen Wert überein.

Wenn der Parameter `useHttpServerUtilityUrlTokenEncode` zum Codieren bzw. `useHttpServerUtilityUrlTokenDecode` zum Decodieren auf `true` festgelegt ist, verhält sich `base64Encode` wie [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) und `base64Decode` wie [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

Wenn Sie nicht das vollständige .NET Framework (sondern .NET Core oder ein anderes Framework) verwenden, um die Schlüsselwerte zum Emulieren des Azure Search-Verhaltens zu erzeugen, sollten Sie `useHttpServerUtilityUrlTokenEncode` und `useHttpServerUtilityUrlTokenDecode` auf `false` festlegen. Abhängig von der von Ihnen verwendeten Bibliothek können sich die Base64-Codierungs- und -Decodierungsfunktionen von den von Azure Search verwendeten Funktionen unterscheiden.

In der folgenden Tabelle werden verschiedene Base64-Codierungen der Zeichenfolge `00>00?00` verglichen. Um die erforderliche weitere Verarbeitung (sofern vorhanden) für die Base64-Funktionen zu ermitteln, wenden Sie die Codierfunktion der Bibliothek auf die Zeichenfolge `00>00?00` an und vergleichen die Ausgabe mit der erwarteten Ausgabe `MDA-MDA_MDA`.

| Codieren | Base64-codierte Ausgabe | Weitere Verarbeitung nach Codierung der Bibliothek | Weitere Verarbeitung vor Decodierung der Bibliothek |
| --- | --- | --- | --- |
| Base64 mit Auffüllung | `MDA+MDA/MDA=` | Verwenden URL-sicherer Zeichen und Entfernen der Auffüllung | Verwenden von Base64-Standardzeichen und Hinzufügen der Auffüllung |
| Base64-Codierung ohne Auffüllung | `MDA+MDA/MDA` | Verwenden URL-sicherer Zeichen | Verwenden von Base64-Standardzeichen |
| URL-sichere Base64-Codierung mit Auffüllung | `MDA-MDA_MDA=` | Entfernen der Auffüllung | Hinzufügen der Auffüllung |
| URL-sichere Base64-Codierung ohne Auffüllung | `MDA-MDA_MDA` | Keine | Keine |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>Funktion „extractTokenAtPosition“

Teilt ein Zeichenfolgenfeld mithilfe des angegebenen Trennzeichens und wählt das Token an der angegebenen Position in der resultierenden Teilung aus.

Diese Funktion verwendet die folgenden Parameter:

* `delimiter`: eine Zeichenfolge, die beim Teilen der Eingabezeichenfolge als Trennzeichen verwendet werden soll.
* `position`: eine ganzzahlige nullbasierte Position des Tokens, das nach der Teilung der Eingabezeichenfolge ausgewählt werden soll.

Wenn die Eingabe beispielsweise `Jane Doe` lautet, `" "` (Leerzeichen) als Trennzeichen (`delimiter`) dient und die `position` 0 ist, ist das Ergebnis `Jane`. Ist die `position` 1, ist das Ergebnis `Doe`. Wenn die Position auf ein Token verweist, das nicht vorhanden ist, wird ein Fehler zurückgegeben.

#### <a name="example---extract-a-name"></a>Beispiel: Extrahieren eines Namens

Die Datenquelle enthält ein Feld `PersonName`, und Sie möchten es als zwei separate Felder `FirstName` und `LastName` indizieren. Mit dieser Funktion können Sie die Eingabe mit dem Leerzeichen als Trennzeichen unterteilen.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } }
  },
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } }
  }]
```

<a name="jsonArrayToStringCollectionFunction"></a>

### <a name="jsonarraytostringcollection-function"></a>Funktion „jsonArrayToStringCollection“

Wandelt eine Zeichenfolge, die als JSON-Zeichenfolgenarray formatiert ist, in ein Zeichenfolgenarray um, das zum Auffüllen eines Felds `Collection(Edm.String)` im Index verwendet werden kann.

Wenn die Eingabezeichenfolge beispielsweise `["red", "white", "blue"]` lautet, wird das Zielfeld vom Typ `Collection(Edm.String)` mit drei Werten gefüllt: `red`, `white` und `blue`. Für Eingabewerte, die nicht als JSON-Zeichenfolgenarrays analysiert werden können, wird ein Fehler zurückgegeben.

#### <a name="example---populate-collection-from-relational-data"></a>Beispiel: Auffüllen der Sammlung aus relationalen Daten

Azure SQL-Datenbank verfügt über keinen integrierten Datentyp, der `Collection(Edm.String)`-Feldern in Azure Search natürlich zugeordnet werden kann. Um Zeichenfolgen-Sammlungsfelder aufzufüllen, können Sie Ihre Quelldaten als JSON-Zeichenfolgenarray vorverarbeiten und dann die Zuordnungsfunktion `jsonArrayToStringCollection` verwenden.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

Ein ausführliches Beispiel, in dem relationale Daten in Indexsammlungsfelder transformiert werden, finden Sie unter [Modellieren von relationalen Daten](search-example-adventureworks-modeling.md).
