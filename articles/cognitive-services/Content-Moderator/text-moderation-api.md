---
title: Textmoderation – Content Moderator
description: Verwenden Sie Textmoderation für möglicherweise unerwünschten Text, personenbezogene Informationen und benutzerdefinierte Begriffslisten.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 01/30/2018
ms.author: sajagtap
ms.openlocfilehash: 0948251c18ad8eece974914ee33dff18b5930553
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53535802"
---
# <a name="text-moderation"></a>Textmoderation

Verwenden Sie die computergestützte Textmoderation von Content Moderator sowie Funktionen zur [Überprüfung durch Personen](Review-Tool-User-Guide/human-in-the-loop.md), um Textinhalte zu moderieren.

Sie können Inhalte basierend auf Ihren Richtlinien und Schwellenwerten entweder blockieren, genehmigen oder überprüfen. Verwenden Sie diese Funktion, um in Umgebungen, in denen Partner, Mitarbeiter und Kunden Textinhalte generieren, die Moderation durch Personen zu ergänzen. Zu solchen Umgebungen zählen etwa Chatrooms, Diskussionsforen, Chatbots, E-Commerce-Kataloge und Dokumente. 

Die Antwort des Diensts enthält folgende Informationen:

- Obszönitäten: begriffsbasierter Abgleich mit einer integrierten Liste anstößiger Begriffe in verschiedenen Sprachen
- Klassifizierung: Computergestützte Klassifizierung in drei Kategorien
- Personenbezogene Informationen (Personally Identifiable Information, PII)
- Automatisch korrigierter Text
- Ursprünglicher Text
- Sprache

## <a name="profanity"></a>Obszönitäten

Wenn die API anstößige Begriffe in einer der [unterstützten Sprachen](Text-Moderation-API-Languages.md) erkennt, werden diese Begriffe in die Antwort aufgenommen. Die Antwort enthält auch deren Position (`Index`) im ursprünglichen Text. `ListId` im folgenden JSON-Beispielcode bezieht sich auf Begriffe aus [benutzerdefinierten Begriffslisten](try-terms-list-api.md) (sofern verfügbar).

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }

> [!NOTE]
> Weisen Sie für den Parameter **language** entweder `eng` zu, oder lassen Sie ihn leer, um die computergestützte **Klassifizierungsantwort** (Vorschaufeature) zu erhalten. **Dieses Feature wird nur für Englisch unterstützt**.
>
> Verwenden Sie für die Erkennung **anstößiger Begriffe** den [ISO 639-3-Code](http://www-01.sil.org/iso639-3/codes.asp) der unterstützten Sprachen aus diesem Artikel, oder lassen Sie ihn leer.

## <a name="classification"></a>Classification

Das computergestützte **Textklassifizierungsfeature** von Content Moderator wird **nur für Englisch** unterstützt und hilft bei der Erkennung potenziell unerwünschter Inhalte. Die gekennzeichneten Inhalte werden je nach Kontext unter Umständen als ungeeignet bewertet. Das Feature vermittelt die Wahrscheinlichkeit jeder Kategorie und kann eine Überprüfung durch Personen empfehlen. Für das Feature wird ein trainiertes Modell verwendet, um Äußerungen zu identifizieren, die unter Umständen beleidigend, abfällig oder diskriminierend sind. Dieses schließt Jargon und Abkürzungen sowie anstößige und absichtlich falsch geschriebene Wörter für die Überprüfung ein. 

Der folgende Auszug aus dem JSON-Auszug zeigt eine Beispielausgabe:

    "Classification": {
        "ReviewRecommended": true,
        "Category1": {
            "Score": 1.5113095059859916E-06
            },
        "Category2": {
            "Score": 0.12747249007225037
            },
        "Category3": {
            "Score": 0.98799997568130493
        }
    }

### <a name="explanation"></a>Erklärung

- `Category1` gibt an, dass unter Umständen Sprache vorhanden ist, die in bestimmten Situationen als sexuell freizügig oder nicht jugendfrei betrachtet werden kann.
- `Category2` gibt an, dass unter Umständen Sprache vorhanden ist, die in bestimmten Situationen als zweideutig bzw. anzüglich oder als nur für Erwachsene bestimmt betrachtet werden kann.
- `Category3` gibt an, dass unter Umständen Sprache vorhanden ist, die in bestimmten Situationen als anstößig betrachtet werden kann.
- `Score` ist ein Wert zwischen 0 und 1. Je höher der Wert, desto höher die vom Modell ermittelte Wahrscheinlichkeit, dass die Kategorie zutreffend ist. Diese Funktion basiert nicht auf manuell programmierten Ergebnissen, sondern auf einem statistischen Modell. Es wird empfohlen, anhand Ihrer eigenen Inhalte zu testen, wie die jeweiligen Kategorien zu Ihren Anforderungen passen.
- `ReviewRecommended` ist entweder „true“ oder „false“ (abhängig von den internen Ergebnisschwellenwerten). Kunden müssen entscheiden, ob sie diesen Wert verwenden oder auf der Grundlage ihrer Inhaltsrichtlinien eigene Schwellenwerte festlegen möchten.

## <a name="personally-identifiable-information-pii"></a>Personenbezogene Informationen (Personally Identifiable Information, PII)

Das PII-Feature erkennt, ob möglicherweise personenbezogene Informationen (Personally Identifiable Information, PII) vorhanden sind:

- E-Mail-Adresse
- US-Postadresse
- IP-Adresse
- US-Telefonnummer
- UK-Telefonnummer
- US-Sozialversicherungsnummer (Social Security Number, SSN)

Das folgende Beispiel zeigt eine exemplarische Antwort:

    "PII": {
        "Email": [{
            "Detected": "abcdef@abcd.com",
            "SubType": "Regular",
            "Text": "abcdef@abcd.com",
            "Index": 32
            }],
        "IPA": [{
            "SubType": "IPV4",
            "Text": "255.255.255.255",
            "Index": 72
            }],
        "Phone": [{
            "CountryCode": "US",
            "Text": "6657789887",
            "Index": 56
            }, {
            "CountryCode": "US",
            "Text": "870 608 4000",
            "Index": 212
            }, {
            "CountryCode": "UK",
            "Text": "+44 870 608 4000",
            "Index": 208
            }, {
            "CountryCode": "UK",
            "Text": "0344 800 2400",
            "Index": 228
            }, {
            "CountryCode": "UK",
            "Text": "0800 820 3300",
            "Index": 245
            }],
        "Address": [{
            "Text": "1 Microsoft Way, Redmond, WA 98052",
            "Index": 89
            }],
        "SSN": [{
            "Text": "999999999",
            "Index": 56
            }, {
            "Text": "999-99-9999",
            "Index": 267
            }]
        }

## <a name="auto-correction"></a>Autokorrektur

Angenommen, der Eingabetext lautet wie folgt („lzay“ und „f0x“ wurden absichtlich so geschrieben):

    The qu!ck brown f0x jumps over the lzay dog.

Bei Verwendung der Autokorrektur enthält die Antwort die korrigierte Version des Texts:

    The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Erstellen und Verwalten benutzerdefinierter Begriffslisten

Die standardmäßige globale Begriffsliste deckt zwar die meisten Fälle ab, in bestimmten Fällen möchten Sie Inhalte aber ggf. auf spezielle Begriffe im Zusammenhang mit Ihren geschäftlichen Anforderungen überprüfen. So kann es beispielsweise wünschenswert sein, Markennamen von Mitbewerbern aus Benutzerbeiträgen herauszufiltern.

> [!NOTE]
> Die Obergrenze liegt bei **fünf Benennungslisten**, wobei jede Liste **max. 10.000 Benennungen** enthalten kann.
>

Das folgende Beispiel zeigt die entsprechende Listen-ID:

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }

Content Moderator bietet eine [Begriffslisten-API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) mit Vorgängen für die Verwaltung benutzerdefinierter Begriffslisten. Beginnen Sie mit der [API-Konsole für Begriffslisten](try-terms-list-api.md), und verwenden Sie die REST-API-Codebeispiele. Falls Sie mit Visual Studio und C# vertraut sind, können Sie sich auch die [.NET-Schnellstartanleitung für Begriffslisten](term-lists-quickstart-dotnet.md) ansehen.

## <a name="next-steps"></a>Nächste Schritte

Testen Sie die [API-Konsole für die Textmoderation](try-text-api.md), und verwenden Sie die REST-API-Codebeispiele. Wenn Sie mit Visual Studio und C# vertraut sind, sehen Sie sich auch die [.NET-Schnellstartanleitung für die Textmoderation](text-moderation-quickstart-dotnet.md) an.
