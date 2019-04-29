---
title: 'Einschränkungen und Begrenzungen: QnA Maker'
titleSuffix: Azure Cognitive Services
description: QnA Maker weist Metagrenzwerte für Teile der Wissensdatenbank und des Diensts auf. Es ist wichtig, Ihre Wissensdatenbank innerhalb dieser Grenzwerte zu halten, um sie testen und veröffentlichen zu können.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/26/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: fe15cafceea8128735f7241fa5e4187d4d9c47a9
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960053"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>Grenzwerte und Grenzen für QnA Maker-Wissensdatenbanken
Umfassende Liste der für QnA Maker geltenden Grenzwerte.

## <a name="knowledge-bases"></a>Knowledge Bases

* Die maximale Anzahl von Knowledge Bases basiert auf den [Grenzwerten des Azure Search-Tarifs](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity).

|**Azure Search-Tarif** | **Free** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximal zulässige Anzahl der veröffentlichten Wissensdatenbanken|2|14|49|199|199|2.999|

 Wenn Ihr Tarif beispielsweise 15 zulässige Indizes aufweist, können Sie 14 Wissensdatenbanken veröffentlichen (1 Index pro veröffentlichter Wissensdatenbank). Der fünfzehnte Index (`testkb`) wird für alle Wissensdatenbanken zum Erstellen und Testen verwendet. 

## <a name="extraction-limits"></a>Grenzwerte für die Extraktion
* Maximale Anzahl der Dateien, die extrahiert werden können, und maximale Dateigröße: Siehe [QnA Maker – Preise](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)
* Maximale Anzahl der Deep-Links, die zum Extrahieren von Fragen und Antworten (QnA) aus FAQ-HTML-Seiten durchlaufen werden können: 20

## <a name="metadata-limits"></a>Grenzwerte für Metadaten
* Die maximale Anzahl von Metadatenfeldern pro Knowledge Base basiert auf den [Grenzwerten des Azure Search-Tarifs](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity).

|**Azure Search-Tarif** | **Free** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximale Anzahl der Metadatenfelder pro QnA Maker-Dienst (für alle Knowledge Bases)|1.000|100*|1.000|1.000|1.000|1.000|

## <a name="knowledge-base-content-limits"></a>Grenzwerte für die Inhalte einer Knowledge Base
Allgemeine Grenzwerte für die Inhalte in der Knowledge Base:
* Länge des Antworttexts: 25.000
* Länge des Fragentexts: 1.000
* Länge des Texts für den Metadatenschlüssel/-wert: 100
* Unterstützte Zeichen für den Metadatennamen: Buchstaben, Ziffern und „_“  
* Unterstützte Zeichen für den Metadatenwerte: Alle Zeichen außer „:“ und „|“ 
* Länge des Dateinamens: 200
* Unterstützte Dateiformate: „.tsv“, „.pdf“, „.txt“, „.docx“, „.xlsx“.
* Maximale Anzahl von alternativen Fragen: 100
* Maximale Anzahl von Frage-Antwort-Paaren: Abhängig vom ausgewählten [Azure Search-Tarif](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits). Ein Frage-Antwort-Paar wird einem Dokument im Azure Search-Index zugeordnet. 

## <a name="create-knowledge-base-call-limits"></a>Grenzwerte für Aufrufe zum Erstellen einer Knowledge Base
Dabei handelt es sich um die Grenzwerte für die einzelnen Aktionen zum Erstellen einer Knowledge Base, d.h. Klicken auf *Create KB* (Knowledge Base erstellen) oder Aufrufen der CreateKnowledgeBase-API.
* Maximale Anzahl von alternativen Fragen pro Antwort: 100
* Maximale Anzahl von URLs: 10
* Maximale Anzahl von Dateien: 10

## <a name="update-knowledge-base-call-limits"></a>Grenzwerte für Aufrufe zum Aktualisieren einer Knowledge Base
Dabei handelt es sich um die Grenzwerte für die einzelnen Aktualisierungsaktionen, d.h. Klicken auf *Save and train* (Speichern und trainieren) oder Aufrufen der UpdateKnowledgeBase-API.
* Länge jedes Quellnamens: 300
* Maximale Anzahl hinzugefügter oder gelöschter alternativer Fragen: 100
* Maximale Anzahl hinzugefügter oder gelöschter Metadatenfelder: 10
* Maximale Anzahl der URLs, die aktualisiert werden können: 5

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wann und wie Dienstebenen geändert werden:

* [QnA Maker](how-to/upgrade-qnamaker-service.md#upgrade-qna-maker-management-sku): Wenn Sie in Ihrer Wissensdatenbank weitere Quelldateien oder größere Dokumente benötigen, die über Ihren aktuellen Tarif hinausgehen, upgraden Sie den Tarif Ihres QnA Maker-Diensts.
* [App Service](how-to/upgrade-qnamaker-service.md#upgrade-app-service): Wenn Ihre Wissensdatenbank mehr Anforderungen von Ihrer Client-App verarbeiten muss, upgraden Sie den Tarif Ihres App-Diensts.
* [Azure Search](how-to/upgrade-qnamaker-service.md#upgrade-azure-search-service): Wenn Sie voraussichtlich eine Vielzahl von Wissensdatenbanken benötigen, upgraden Sie den Tarif Ihres Azure Search-Dienst.
