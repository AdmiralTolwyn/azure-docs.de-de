---
title: Modellieren komplexer Datentypen in Azure Search | Microsoft Docs
description: Geschachtelte oder hierarchische Datenstrukturen lassen sich in einem Azure Search-Index mit einem vereinfachten Rowset und dem Datentyp „Sammlungen“ modellieren.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2017
ms.openlocfilehash: 81298bedd43a89ea948753dffc5f80248f5429ca
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2018
ms.locfileid: "31799072"
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>Gewusst wie: Modellieren komplexer Datentypen in Azure Search
Externe Datasets, die zum Auffüllen eines Azure Search-Index verwendet werden, weisen manchmal hierarchische oder geschachtelte Teilstrukturen auf, die in einem tabellarischen Rowset nicht sauber unterteilt werden. Beispiele für solche Strukturen können mehrere Standorte und Telefonnummern für einen einzelnen Kunden, mehrere Farben und Größen für eine einzelne SKU, mehrere Autoren für ein einzelnes Buch enthalten und so weiter. In der Modelliersprache werden diese Strukturen bisweilen als *komplexe Datentypen*, *zusammengesetzte Datentypen*, *verbundene Datentypen* oder *aggregierte Datentypen* bezeichnet, um nur einige zu nennen.

Komplexe Datentypen werden in Azure Search nicht systemintern unterstützt. Eine bewährte Lösung stellt jedoch ein zweistufiger Prozess dar, bei dem die Struktur reduziert und dann die innere Struktur mithilfe des Datentyps **Sammlung** wieder zusammengesetzt wird. Die in diesem Artikel beschriebene Technik ermöglicht das Durchsuchen, Facettieren, Filtern und Sortieren des Inhalts.

## <a name="example-of-a-complex-data-structure"></a>Beispiel für eine komplexe Datenstruktur
In der Regel befinden sich die betreffenden Daten als JSON- oder XML-Dokumente oder als Elemente in einem NoSQL-Speicher wie z. B. Azure Cosmos DB. Im Hinblick auf die Struktur besteht die Herausforderung in den zahlreichen untergeordneten Elementen, die gefiltert und durchsucht werden müssen.  Als Ausgangspunkt für die Veranschaulichung der Problemumgehung dient das folgende JSON-Dokument, in dem eine Gruppe von Kontakten als Beispiel aufgeführt ist:

~~~~~
[
  {
    "id": "1",
    "name": "John Smith",
    "company": "Adventureworks",
    "locations": [
      {
        "id": "1",
        "description": "Adventureworks Headquarters"
      },
      {
        "id": "2",
        "description": "Home Office"
      }
    ]
  }, 
  {
    "id": "2",
    "name": "Jen Campbell",
    "company": "Northwind",
    "locations": [
      {
        "id": "3",
        "description": "Northwind Headquarter"
      },
      {
        "id": "4",
        "description": "Home Office"
      }
    ]
}]
~~~~~

Während die Felder mit dem Namen „ID“, „Name“ und „Unternehmen“ problemlos 1:1 als Felder innerhalb eines Azure Search-Index zugeordnet werden können, enthält das Feld „Standorte“ ein Array von Standorten, die sowohl eine Gruppe von Standort-IDs als auch Standortbeschreibungen aufweisen. Angesichts der Tatsache, dass Azure Search keinen Datentyp aufweist, der dies unterstützt, benötigen wir für das Modellieren in Azure Search eine andere Methode. 

> [!NOTE]
> Dieses Verfahren wird auch im Blogbeitrag [Indexing Cosmos DB with Azure Search](https://blogs.msdn.microsoft.com/kaevans/2015/03/09/indexing-documentdb-with-azure-seach/) (Indizieren von Cosmos DB mit Azure Search) von Kirk Evans beschrieben. In diesem wird ein Verfahren namens „Vereinfachen der Daten“ gezeigt, bei dem Felder namens `locationsID` und `locationsDescription` vorhanden sind, bei denen es sich jeweils um [Sammlungen](https://msdn.microsoft.com/library/azure/dn798938.aspx) (oder ein Array von Zeichenfolgen) handelt.   
> 
> 

## <a name="part-1-flatten-the-array-into-individual-fields"></a>Teil 1: Vereinfachen des Arrays in einzelne Felder
Zum Erstellen eines Azure Search-Index, der für dieses Dataset ausgelegt ist, erstellen Sie einzelne Felder für die geschachtelte Unterstruktur: `locationsID` und `locationsDescription` mit dem Datentyp [Sammlungen](https://msdn.microsoft.com/library/azure/dn798938.aspx) (oder ein Array von Zeichenfolgen). In diesen Feldern indizieren Sie in diesem Fall die Werte „1“ und „2“ im Feld `locationsID` für Bernhard Kohler und die Werte „3“ und „4“ im Feld `locationsID` für Klarissa Wolf.  

Ihre Daten werden innerhalb von Azure Search folgendermaßen dargestellt: 

![Beispieldaten, 2 Zeilen](./media/search-howto-complex-data-types/sample-data.png)

## <a name="part-2-add-a-collection-field-in-the-index-definition"></a>Teil 2: Hinzufügen eines Sammlungsfelds in der Indexdefinition
Im Indexschema können Felddefinitionen diesem Beispiel ähneln.

~~~~
var index = new Index()
{
    Name = indexName,
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("name", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("company", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("locationsId", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("locationsDescription", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true }
    }
};
~~~~

## <a name="validate-search-behaviors-and-optionally-extend-the-index"></a>Überprüfen des Suchverhaltens und optionales Erweitern des Index
Sobald Sie den Index erstellt und die Daten geladen haben, können Sie die Lösung testen, um die Ausführung von Suchabfragen für das Dataset zu überprüfen. Jedes **Sammlungsfeld** sollte **durchsuchbar**, **filterbar** und **facettenreich** sein. Sie sollten Abfragen wie die folgenden ausführen können:

* Alle Personen suchen, die in der Hauptniederlassung von Adventureworks arbeiten
* Anzahl der Personen abrufen, die in einem Heimbüro arbeiten  
* Für Personen, die in einem Heimbüro arbeiten, die anderen Büros, in denen sie arbeiten sowie die Anzahl der Personen an jedem Standort anzeigen  

Dieses Verfahren funktioniert nicht, wenn Sie eine Suche durchführen müssen, bei der die ID und die Standortbeschreibung kombiniert werden. Beispiel: 

* Alle Personen suchen, die ein Heimbüro und die Standort-ID 4 haben  

Sie erinnern sich vielleicht, dass der ursprüngliche Inhalt folgendermaßen aussah:

~~~~
   {
        id: '4',
        description: 'Home Office'
   }
~~~~

Da jetzt jedoch die Daten in separate Felder aufgeteilt sind, ist unklar, ob sich das Heimbüro für Klarissa Wolf auf `locationsID 3` oder `locationsID 4` bezieht.  

Für diesen Fall definieren Sie ein anderes Feld im Index, das alle Daten in einer einzelnen Sammlung kombiniert.  Für unser Beispiel nennen wir dieses Feld `locationsCombined` und trennen den Inhalt durch `||`. Sie können jedoch jedes Trennzeichen verwenden, das Sie für eine eindeutige Zeichengruppe für Ihren Inhalt halten. Beispiel:  

![Beispieldaten, 2 Zeilen mit Trennzeichen](./media/search-howto-complex-data-types/sample-data-2.png)

Mit diesem `locationsCombined`-Feld sind sogar noch mehr Abfragen möglich, z. B.:

* Anzahl der Personen anzeigen, die in einem Heimbüro mit der Standort-ID 4 arbeiten  
* Personen suchen, die in einem Heimbüro mit der Standort-ID 4 arbeiten 

## <a name="limitations"></a>Einschränkungen
Diese Methode eignet sich für eine Reihe von Szenarien, ist jedoch nicht in jedem Fall anwendbar.  Beispiel: 

1. Wenn in Ihrem komplexen Datentyp kein statischer Satz von Feldern vorhanden ist und es keine Möglichkeit gab, alle möglichen Typen einem einzelnen Feld zuzuordnen. 
2. Das Aktualisieren der geschachtelten Objekte erfordert einige zusätzliche Aufgaben, um zu bestimmen, was im Azure Search-Index genau aktualisiert werden muss.

## <a name="sample-code"></a>Beispielcode
Ein Beispiel für das Indizieren eines komplexen JSON-Datasets in Azure Search und das Durchführen einer Reihe von Abfragen über dieses Dataset finden Sie in diesem [GitHub-Repository](https://github.com/liamca/AzureSearchComplexTypes).

## <a name="next-step"></a>Nächster Schritt
[für eine systemeigene Unterstützung von komplexen Datentypen](https://feedback.azure.com/forums/263029-azure-search) , und teilen Sie uns alle weiteren Anregungen mit, die wir bei der Implementierung von Features berücksichtigen sollten. Sie können mich auch direkt auf Twitter unter @liamca erreichen.

