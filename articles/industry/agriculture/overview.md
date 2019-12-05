---
title: Übersicht über Azure FarmBeats
description: In diesem Artikel erhalten Sie eine Übersicht über Azure FarmBeats.
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: ff28ae7dbff40910ac0431fb47f7f0be3e1569c0
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538143"
---
# <a name="overview-of-azure-farmbeats"></a>Übersicht über Azure FarmBeats

Azure FarmBeats ist eine Sammlung von Azure-Diensten und -Funktionen, mit denen Sie im Handumdrehen intelligente, datengesteuerte Lösungen für den Agrarsektor entwickeln können. Azure FarmBeats ist ein Azure Marketplace-Angebot, mit dem Sie landwirtschaftliche Daten aus verschiedenen Quellen wie z. B. Sensoren, Drohnen, Kameras und Satelliten erfassen, aggregieren und verarbeiten können, ohne in Ressourcen für eine umfassende Datenaufbereitung investieren zu müssen.

> [!NOTE]
> Azure FarmBeats befindet sich zurzeit in der öffentlichen Vorschau. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Azure FarmBeats wird ohne Vereinbarung zum Servicelevel bereitgestellt. Unterstützung erhalten Sie über das [Azure FarmBeats-Forum](https://aka.ms/FarmBeatsMSDN ).

Mithilfe von Azure FarmBeats können Sie Daten aus verschiedenen Quellen im Kontext eines landwirtschaftlichen Betriebs (eines relevanten geografischen Bereichs) abrufen – z. B. von Sensoren, Satelliten oder Drohnen.

Sie haben folgende Möglichkeiten:

- Aggregieren landwirtschaftsbezogener Datasets von verschiedenen Anbietern
- Schnelles Erstellen von KI/ML-Modellen (künstliche Intelligenz/Machine Learning) durch Zusammenführung unterschiedlicher Datasets

Azure FarmBeats bietet eine robuste und einfache Möglichkeit zum Ausführen der folgenden Aufgaben:

- Erstellen einer Karte für den landwirtschaftlichen Betrieb unter Verwendung einer GeoJSON-Datei
- Bewerten des Betriebszustands anhand von Vegetationsindex und Wasserindex auf der Grundlage von Satellitenbildern
- Abrufen von Empfehlungen dazu, wie viele Sensoren verwendet und wo diese platziert werden sollten
- Verfolgen des Betriebszustands durch Visualisierung von Bodendaten, die von Sensoren verschiedener Sensorhersteller gesammelt wurden
- Abrufen einer Karte zur Bodenfeuchte, die auf der Zusammenführung von Satelliten- und Sensordaten basiert
- Gewinnen handlungsrelevanter Erkenntnisse durch das Erstellen von KI/ML-Modellen, die auf aggregierten Datasets aufbauen
- Erstellen oder Erweitern Ihrer digitalen Lösung für den landwirtschaftlichen Betrieb durch Bereitstellen von Hinweisen zum Betriebszustand

In den folgenden Abschnitten dieses Artikels werden die Azure FarmBeats-Komponenten vorgestellt.

## <a name="data-hub"></a>Datenhub

Der Azure FarmBeats-Datenhub ist eine API-Ebene, die eine anbieterübergreifende Aggregation, Normalisierung und Kontextualisierung verschiedener Datasets ermöglicht, die landwirtschaftliche Daten enthalten. Im Rahmen dieser Vorschau können Sie zwei Sensorenanbieter ([Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/) und [Teralytic](https://teralytic.com/)), einen Anbieter für Satellitenbilder ([Sentinel-2](https://sentinel.esa.int/web/sentinel/home)) und drei Anbieter für Drohnenbilder ([senseFly](https://www.sensefly.com/), [SlantRange](https://slantrange.com/) und [DJI](https://dji.com/)) nutzen. Der Datenhub ist als API-Plattform konzipiert, und wir arbeiten mit zahlreichen weiteren Anbietern zusammen, um eine Integration in Azure FarmBeats zu ermöglichen und Ihnen so bei der Entwicklung Ihrer Lösung eine größere Auswahl zu bieten.

## <a name="accelerator"></a>Accelerator

Eine Beispiellösung, die auf dem Datenhub aufbaut und Ihre Benutzeroberflächen- und Modellentwicklung beschleunigt. Diese Webanwendung nutzt APIs, um die Visualisierung von erfassten Sensordaten in Form von Diagrammen und die Visualisierung der Modellausgabe als Karten zu veranschaulichen. Beispielsweise können Sie mit dem Accelerator umgehend einen Agrarbetrieb erstellen und mühelos eine Karte zum Vegetationsindex oder eine Karte zur Sensorplatzierung für diesen Betrieb erstellen.

## <a name="resources"></a>Ressourcen

Weitere Informationen finden Sie im [Blog](https://aka.ms/AzureFarmBeats) und in den [Foren](https://aka.ms/FarmBeatsMSDN) von Azure FarmBeats.

## <a name="next-steps"></a>Nächste Schritte

Besuchen Sie den [Azure Marketplace](https://aka.ms/FarmBeatsMarketplace), um in Azure FarmBeats einzusteigen und mit der Bereitstellung zu beginnen.
