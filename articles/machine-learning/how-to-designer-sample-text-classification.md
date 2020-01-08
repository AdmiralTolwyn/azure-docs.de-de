---
title: 'Designer: Klassifizieren von Buchrezensionen – Beispiel'
titleSuffix: Azure Machine Learning
description: Erstellen Sie eine Klassifizierung für eine logistische Regression mit mehreren Klassen, um die Unternehmenskategorie anhand des Wikipedia SP 500-Datasets mit Microsoft Azure Machine Learning-Designer vorherzusagen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: 3116b88cd50fa9f348b418cfc90fe21986944b74
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/28/2019
ms.locfileid: "75535158"
---
# <a name="build-a-classifier-to-predict-company-category-using-azure-machine-learning-designer"></a>Erstellen Sie mit Azure Machine Learning-Designer einen Klassifizierer zur Vorhersage der Unternehmenskategorie.

**Designer (Vorschauversion) – Beispiel 7**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Dieses Beispiel veranschaulicht das Erstellen einer Textklassifizierungspipeline mit Textanalysemodulen im Azure Machine Learning-Designer (Vorschauversion).

Das Ziel der Textklassifizierung besteht darin, einen Text mindestens einer vordefinierten Klasse oder Kategorie zuzuweisen. Bei dem Text kann es sich beispielsweise um ein Dokument, einen Nachrichtenartikel, eine Suchabfrage, eine E-Mail, einen Tweet, ein Supportticket, Kundenfeedback oder um die Produktrezension eines Benutzers handeln. Zu den Anwendungsbereichen der Textklassifizierung zählen unter anderem die Kategorisierung von Zeitungsartikeln und Nachrichteninhalten in Themen, die Strukturierung von Webseiten in hierarchische Kategorien, Standpunktanalysen, die Vorhersage der Benutzerabsicht auf der Grundlage von Suchabfragen, die Weiterleitung von Supporttickets sowie die Analyse von Kundenfeedback. 

Diese Pipeline trainiert eine **Klassifizierung für eine logistische Regression mit mehreren Klassen**, um die Unternehmenskategorie anhand des von **Wikipedia abgeleiteten SP 500-Datasets** vorherzusagen.  

Das Trainieren eines Machine Learning-Modells mit Textdaten umfasst grundsätzlich folgende Schritte:

1. Abrufen von Daten

1. Vorverarbeiten der Textdaten

1. Featureentwicklung

   Konvertieren des Textfeatures mittels Featureextraktionsmodul (beispielsweise Feature Hashing) in das numerische Feature und Extrahieren des N-Gramm-Feature aus den Textdaten

1. Trainieren des Modells

1. Bewerten des Datasets

1. Auswerten des Modells

Das endgültige, vollständige Diagramm der Pipeline, an der wir arbeiten, sieht wie folgt aus. Es werden die Gründe für alle Module bereitgestellt, sodass Sie ähnliche Entscheidungen selbst treffen können.

[![Graph der Pipeline](./media/how-to-designer-sample-text-classification/nlp-modules-overall.png)](./media/how-to-designer-sample-text-classification/nlp-modules-overall.png#lightbox)

## <a name="data"></a>Data

In dieser Pipeline wird das Dataset **Wikipedia SP 500** verwendet. Das Dataset wurde von Wikipedia (https://www.wikipedia.org/) abgeleitet und basiert auf Artikeln aller S&P-500-Unternehmen. Das Dataset wurde vor dem Hochladen in den Azure Machine Learning-Designer wie folgt verarbeitet:

- Extrahieren der Textinhalte für die einzelnen Unternehmen
- Entfernen der Wiki-Formatierung
- Entfernen aller nicht alphanumerischen Zeichen
- Konvertieren sämtlicher Texte in Kleinbuchstaben
- Bekannte Firmenkategorien wurden hinzugefügt.

Da für einige Unternehmen keine Artikel gefunden werden konnten, ist die Anzahl der Datensätze kleiner als 500.

## <a name="pre-process-the-text-data"></a>Vorverarbeiten der Textdaten

Zur Vorverarbeitung der Textdaten verwenden wir das Modul **Preprocess Text** (Text vorverarbeiten). Im Zuge der Vorverarbeitung werden unter anderem Sätze erkannt und tokenisiert. Alle unterstützten Optionen finden Sie im Artikel [**Preprocess Text**](algorithm-module-reference/preprocess-text.md). Nach der Vorverarbeitung der Textdaten verwenden wir das Modul **Split Data** (Daten aufteilen), um die Eingabedaten nach dem Zufallsprinzip so aufzuteilen, dass das Trainings- und das Testdataset jeweils 50 Prozent der ursprünglichen Daten enthalten.

## <a name="feature-engineering"></a>Featureentwicklung
In diesem Beispiel werden zwei Methoden für die Featureentwicklung verwendet.

### <a name="feature-hashing"></a>Feature Hashing
Wir haben das Modul [**Feature Hashing**](algorithm-module-reference/feature-hashing.md) verwendet, um den reinen Text der Artikel in ganze Zahlen zu konvertieren und die ganzzahligen Werte als Eingabefeatures für das Modell zu verwenden. 

Mit dem Modul **Feature Hashing** können Textdokumente mit variabler Länge unter Verwendung der in der Vowpal Wabbit-Bibliothek zur Verfügung stehenden 32-Bit-Hashingmethode „murmurhash v3“ in gleich lange numerische Featurevektoren konvertiert werden. Feature Hashing dient zur Dimensionalitätsreduzierung und beschleunigt zudem die Suche nach Featuregewichtungen zur Klassifizierungszeit, da anstelle eines Zeichenfolgenvergleichs ein Hashwertvergleich verwendet wird.

In der Beispielpipeline wurde die Anzahl von Hashingbits auf 14 und die Anzahl von N-Grammen auf 2 festgelegt. Mit diesen Einstellungen kann die Hashtabelle 2^14 Einträge enthalten. Dabei stellt jedes Hashingfeature mindestens ein N-Gramm-Feature und der zugehörige Wert die Vorkommenshäufigkeit dieses N-Gramms in der Textinstanz dar. Eine Hashtabelle dieser Größe ist für die meisten Aufgaben mehr als ausreichend. In manchen Fällen ist jedoch möglicherweise mehr Platz erforderlich, um Konflikte zu vermeiden. Testen Sie die Leistung Ihrer Machine Learning-Lösung mit verschiedenen Bitwerten. 

### <a name="extract-n-gram-feature-from-text"></a>Extrahieren von N-Gramm-Features aus Text

Ein N-Gramm ist eine zusammenhängende Sequenz von N Begriffen aus einer bestimmten Textsequenz. Ein N-Gramm mit der Größe 1 wird als Monogramm bezeichnet. Ein N-Gramm mit der Größe 2 ist ein Bigramm. Und ein N-Gramm mit der Größe 3 ist ein Trigramm. Größere N-Gramme werden mitunter nach ihrem N-Wert benannt (also beispielsweise 4-Gramm, 5-Gramm usw.).

Wir haben das Modul [**Extract N-Gram Feature from Text**](algorithm-module-reference/extract-n-gram-features-from-text.md) (Extrahieren von N-Gramm-Features aus Text) als weitere Lösung für die Featureentwicklung verwendet. Dieses Modul extrahiert zunächst die Gruppe von N-Grammen. Zusätzlich zu den N-Grammen wird die Anzahl von Dokumenten gezählt, in denen die einzelnen N-Gramme im Text vorkommen (DF). In diesem Beispiel wird zur Berechnung von Featurewerten die TF-IDF-Metrik verwendet. Anschließend werden unstrukturierte Textdaten in gleich lange numerische Featurevektoren konvertiert, wobei jedes Feature den TF-IDF-Wert eines N-Gramms in einer Textinstanz darstellt.

Nachdem die Textdaten in numerische Featurevektoren konvertiert wurden, wird ein Modul vom Typ **Select Column** (Spalte auswählen) verwendet, um die Textdaten aus dem Dataset zu entfernen. 

## <a name="train-the-model"></a>Trainieren des Modells

Die Auswahl des Algorithmus hängt häufig von den Anforderungen des Anwendungsfalls ab. Da diese Pipeline die Vorhersage der Unternehmenskategorie zum Ziel hat, bietet sich die Verwendung eines Klassifizierungsmodells mit mehreren Klassen an. Aufgrund der hohen Anzahl von Features und deren Seltenheit verwenden wir für diese Pipeline das Modul **Multiclass Logistic Regression**.

## <a name="test-evaluate-and-compare"></a>Testen, Evaluieren und Vergleichen

 Wir teilen das Dataset auf und verwenden unterschiedliche Datasets zum Trainieren und Testen des Modells, um die Auswertung des Modells objektiver zu gestalten.

Nachdem das Modell trainiert wurde, verwenden wir die Module **Score Model** (Modell bewerten) und **Evaluate Model** (Modell auswerten), um die vorhergesagten Ergebnisse zu generieren und die Modelle auszuwerten. Vor der Verwendung des Moduls **Score Model** (Modell bewerten) muss jedoch wie beim Trainieren die Featureentwicklung durchgeführt werden. 

Im Falle des Moduls **Feature Hashing** kann die Featureentwicklung für die Bewertung genau wie beim Trainieren ausgeführt werden. Verwenden Sie das Modul **Feature Hashing** direkt, um die Eingabetextdaten zu verarbeiten.

Für das Modul **Extract N-Gram Feature from Text** (Extrahieren von N-Gramm-Features aus Text) müssen wir eine Verbindung zwischen der **Ergebnisvokabularausgabe** aus dem Trainingsdataflow und dem **Eingabevokabular** aus dem Bewertungsdataflow herstellen und den Parameter **Vocabulary mode** (Vokabularmodus) auf **ReadOnly** festlegen.
[![Diagramm der N-Gramm-Bewertung](./media/how-to-designer-sample-text-classification/n-gram.png)](./media/how-to-designer-sample-text-classification/n-gram.png)

Nach Abschluss des Entwicklungsschritts kann **Score Model** (Modell bewerten) verwendet werden, um unter Verwendung des trainierten Modells Vorhersagen für das Testdataset zu generieren. Um das Ergebnis zu überprüfen, wählen Sie den Ausgabeport des **Score Model**-Moduls und dann **Visualize** (Visualisieren) aus.

Wir übergeben die Bewertungen dann an das **Evaluate Model**-Modul, um Auswertungsmetriken zu generieren. **Evaluate Model** (Modell auswerten) verfügt über zwei Eingabeports, sodass wir bewertete Datasets auswerten und vergleichen können, die mit unterschiedlichen Methoden generiert werden. In diesem Beispiel vergleichen wir die Leistung des Ergebnisses, das mit der Feature Hashing-Methode und der N-Gramm-Methode generiert wird.
Um das Ergebnis zu überprüfen, wählen Sie den Ausgabeport des **Evaluate Model**-Moduls und dann **Visualize** (Visualisieren) aus.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

Erkunden Sie die anderen Beispiele, die für den Designer zur Verfügung stehen:
- [Beispiel 1 – Regression: Vorhersagen des Preises eines Autos](service/how-to-designer-sample-regression-automobile-price-basic.md)
- [Beispiel 2 – Regression: Vergleichen von Algorithmen für die Vorhersage von Autopreisen](service/how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Beispiel 3 – Klassifizierung mit Featureauswahl: Vorhersage des Einkommens](service/how-to-designer-sample-classification-predict-income.md)
- [Beispiel 4 – Klassifizierung: Vorhersagen des Kreditrisikos (kostensensibel)](service/how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Beispiel 5 – Klassifizierung: Vorhersage der Kundenabwanderung](service/how-to-designer-sample-classification-churn.md)
- [Beispiel 6 – Klassifizierung: Vorhersage von Flugverspätungen](service/how-to-designer-sample-classification-flight-delay.md)
