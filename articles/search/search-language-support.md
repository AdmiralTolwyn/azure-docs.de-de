---
title: Mehrsprachige Indizierung für nicht-englische Suchabfragen – Azure Search
description: Azure Search unterstützt 56 Sprachen und nutzt Sprachanalysen mit Lucene- und Natural Language Processing-Technologie von Microsoft.
author: yahnoosh
manager: jlembicz
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: jlembicz
ms.openlocfilehash: 5383ad44f665ce809772143e23817932c3e2b7e6
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883890"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-search"></a>Erstellen eines Index für mehrere Sprachen in Azure Search

Indizes können Felder mit Inhalt aus mehreren Sprachen enthalten, z. B. beim Erstellen einzelner Felder für sprachspezifische Zeichenfolgen. Um optimale Ergebnisse bei Indizierung und Abfragen zu erzielen, weisen Sie eine Sprachanalyse zu, die die entsprechenden linguistischen Regeln bereitstellt. 

Azure Search bietet eine große Auswahl an Sprachanalysen von Lucene und Microsoft, die mithilfe der Analyzer-Eigenschaft einzelnen Feldern zugewiesen werden können. Sie können auch eine Sprachanalyse im Portal angeben, wie in diesem Artikel beschrieben.

## <a name="add-analyzers-to-fields"></a>Hinzufügen von Analysen zu Feldern

Beim Erstellen eines Felds wird eine Sprachanalyse angegeben. Zum Hinzufügen einer Analyse zu einer vorhandenen Felddefinition muss der Index überschrieben (und erneut geladen) werden, oder es muss ein neues Feld erstellt werden, das mit dem ursprünglichen identisch ist, jedoch mit einer Analysezuweisung. Sie können dann das nicht verwendete Feld ggf. löschen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und suchen Sie den Suchdienst.
1. Klicken Sie oben im Dienstdashboard auf der Befehlsleiste auf **Index hinzufügen** , um einen neuen Index zu beginnen, oder öffnen Sie einen vorhandenen Index, um eine Analyse für neue Felder festzulegen, die Sie einem vorhandenen Index hinzufügen.
1. Starten Sie eine Felddefinition, indem Sie einen Namen angeben.
1. Wählen Sie den Datentyp „Edm.String“ aus. Die Volltextsuche wird nur für Zeichenfolgenfelder unterstützt.
1. Legen Sie das **Searchable**-Attribut fest, um die Analyzer-Eigenschaft zu aktivieren. Felder müssen textbasiert sein, damit eine Sprachanalyse verwendet werden kann.
1. Wählen Sie eine der verfügbaren Analysen aus. 

![Zuweisen von Sprachanalysen während der Felddefinition](media/search-language-support/select-analyzer.png "Zuweisen von Sprachanalysen während der Felddefinition")

Standardmäßig verwenden alle durchsuchbaren Felder die sprachunabhängige [Lucene-Standardanalyse](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html). Die vollständige Liste der unterstützten Analysen finden Sie unter [Hinzufügen von Sprachanalysetools zu einem Azure Search-Index](index-add-language-analyzers.md).

Die im Portal verfügbaren Analysen sind für die Verwendung in der vorliegenden Form vorgesehen. Wenn Sie Anpassungen oder eine bestimmte Konfiguration von Filtern und Tokenizern benötigen, sollten Sie [eine benutzerdefinierte Analyse](index-add-custom-analyzers.md) im Code erstellen. Das Portal bietet keine Unterstützung für die Auswahl oder Konfiguration benutzerdefinierter Analysen.

## <a name="query-language-specific-fields"></a>Abfragen sprachspezifischer Felder

Sobald die Sprachanalyse für ein Feld ausgewählt wurde, wird sie für jede Indizierungs- und Suchanfrage für dieses Feld verwendet. Wenn eine Abfrage mithilfe verschiedener Analysen für mehrere Felder ausgegeben wird, wird sie von der zugewiesenen Analyse unabhängig für das jeweilige Feld verarbeitet.

Wenn die Sprache des Agents, der eine Abfrage ausgibt, bekannt ist, kann eine Suchabfrage mit dem **SearchFields** -Abfrageparameter auf ein bestimmtes Feld beschränkt werden. Die folgende Abfrage wird nur für die Beschreibung in polnischer Sprache ausgegeben:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=PolishContent&api-version=2019-05-06`

Sie können im Portal im [**Suchexplorer**](search-explorer.md) den Index abfragen, um eine Abfrage einzufügen, die der oben gezeigten ähnlich ist.

## <a name="boost-language-specific-fields"></a>Optimieren sprachspezifischer Felder

Manchmal ist die Sprache des Agents, der eine Abfrage ausgibt, nicht bekannt. In diesem Fall kann die Abfrage für alle Felder gleichzeitig ausgegeben werden. Bei Bedarf kann die Ausgabe der Ergebnisse in einer bestimmten Sprache mithilfe von [Bewertungsprofilen](index-add-scoring-profiles.md) festgelegt werden. Im folgenden Beispiel erhalten in der Beschreibung in englischer Sprache gefundene Übereinstimmungen eine höhere Bewertung als Übereinstimmungen in polnischer und französischer Sprache:

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2019-05-06`

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie .NET-Entwickler sind, können Sie Sprachanalysen mit dem [Azure Search .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Search) und der [Analyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet)-Eigenschaft konfigurieren. 