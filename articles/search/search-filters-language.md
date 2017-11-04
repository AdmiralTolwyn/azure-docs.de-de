---
title: Sprachfilter in Azure Search | Microsoft-Dokumentation
description: Filtern Sie nach Kriterien wie Benutzersicherheits-ID, Sprache, geografischem Standort oder numerischen Werten, um Suchergebnisse bei Abfragen in Azure Search (in Microsoft Azure gehosteter Cloudsuchdienst) einzugrenzen.
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
ms.assetid: 
ms.service: search
ms.devlang: 
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/23/2017
ms.author: heidist
ms.openlocfilehash: 31404e9ae2dac559f6b4f9f8c0edd0a785142912
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/25/2017
---
# <a name="how-to-filter-by-language-in-azure-search"></a>Filtern nach Sprache in Azure Search 

In einer mehrsprachigen Suchanwendung ist es wichtig, nach Ergebnissen in der Sprache des Benutzers suchen und entsprechende Ergebnisse abrufen zu können. In Azure Search kann hierzu beispielsweise eine Reihe von Feldern für die Speicherung von Zeichenfolgen in einer bestimmten Sprache erstellt und die Volltextsuche dann bei der Abfrage auf diese Felder beschränkt werden.

Mithilfe von Abfrageparametern in der Anforderung wird der Gültigkeitsbereich der Suchabfrage festgelegt, und Ergebnisse aus Feldern, deren Inhalt nicht der gewünschten Sucherfahrung entspricht, werden ignoriert.

| Parameter | Zweck |
|-----------|--------------|
| **searchFields** | Grenzt die Volltextsuche auf die Liste der angegebenen Felder ein. |
| **$select** | Kürzt die Antwort, sodass nur die von Ihnen angegebenen Felder berücksichtigt werden. Standardmäßig werden alle abrufbaren Felder zurückgegeben. Mit dem Parameter **$select** können Sie auswählen, welche Felder berücksichtigt werden sollen. |

Der Erfolg dieser Methode hängt von der Integrität der Feldinhalte ab. Azure Search übersetzt keine Zeichenfolgen und führt auch keine Spracherkennung durch. Sie müssen selbst dafür sorgen, dass die Felder die erwarteten Zeichenfolgen enthalten.

## <a name="define-fields-for-content-in-different-languages"></a>Definieren von Feldern für Inhalt in verschiedenen Sprachen

In Azure Search sind Abfragen auf einen einzelnen Index ausgerichtet. Entwickler, die sprachspezifische Zeichenfolgen in einer einzelnen Suchumgebung bereitstellen möchten, definieren üblicherweise dedizierte Felder, um die Werte zu speichern – also ein Feld für englische Zeichenfolgen, ein Feld für Französisch und so weiter. 

In unseren Beispielen (etwa im [Immobilienbeispiel](search-get-started-portal.md) weiter unten) sind Ihnen wahrscheinlich bereits Felddefinitionen wie auf dem folgenden Screenshot aufgefallen. Dieses Beispiel zeigt die Sprachanalysezuweisungen für die Felder in diesem Index. Aus Leistungsgründen empfiehlt es sich, Felder mit Textinhalt bei der Volltextsuche mit einer Analysekomponente zu kombinieren, die die linguistischen Regeln der Zielsprache behandelt.

  ![](./media/search-filters/lang-fields.png)

> [!Note]
> Codebeispiele zur Veranschaulichung von Felddefinitionen mit Sprachanalysen finden Sie unter [Definieren eines Index (.NET)](https://docs.microsoft.com/azure/search/search-create-index-dotnet#define-your-azure-search-index) sowie unter [Definieren eines Index (REST)](https://docs.microsoft.com/azure/search/search-create-index-rest-api#define-your-azure-search-index-using-well-formed-json).

## <a name="build-and-load-an-index"></a>Erstellen und Laden eines Index

Ein (wahrscheinlich naheliegender) Zwischenschritt vor der Erstellung einer Abfrage ist das [Erstellen und Auffüllen des Index](https://docs.microsoft.com/azure/search/search-create-index-dotnet#create-the-index). Er wird daher nur der Vollständigkeit halber erwähnt. Die Verfügbarkeit des Index kann unter anderem durch Überprüfen der Indexliste im [Portal](https://portal.azure.com) ermittelt werden.

## <a name="constrain-the-query-and-trim-results"></a>Einschränken der Abfrage und Ausschließen von Ergebnissen

Abfrageparameter dienen dazu, die Suche auf bestimmte Felder einzugrenzen und anschließend die Ergebnisse von Feldern auszuschließen, die für Ihr Szenario nicht relevant sind. Wenn Sie die Suche also beispielsweise auf Felder mit französischen Zeichenfolgen einschränken möchten, richten Sie die Abfrage mithilfe von **searchFields** auf Felder aus, die Zeichenfolgen in dieser Sprache enthalten. 

Standardmäßig werden bei einer Suche alle als abrufbar markierten Felder zurückgegeben. Es empfiehlt sich daher, Felder auszuschließen, die für die angestrebte sprachspezifische Sucherfahrung nicht geeignet sind. Wenn Sie die Suche auf ein Feld mit französischen Zeichenfolgen eingeschränkt haben, möchten Sie wahrscheinlich Felder mit englischen Zeichenfolgen aus Ihren Ergebnissen ausschließen. Mithilfe des Abfrageparameters **$select** können Sie steuern, welche Felder an die aufrufende Anwendung zurückgegeben werden sollen.

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> Die Abfrage enthält zwar kein $filter-Argument, der Anwendungsfall ist jedoch eng mit Filterkonzepten verwandt, weshalb wir ihn als Filterszenario präsentieren.

## <a name="see-also"></a>Weitere Informationen

+ [Filter in Azure Search](search-filters.md)
+ [Sprachanalysen](https://docs.microsoft.com/rest/api/searchservice/language-support)
+ [Funktionsweise der Volltextsuche in Azure Search](search-lucene-query-architecture.md)
+ [Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) (Suchen nach Dokumenten (Azure Search Service-REST-API))

