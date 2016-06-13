<properties 
	pageTitle="Lösungs-Playbook zur Fahrzeugtelemetrieanalyse | Microsoft Azure" 
	description="Verwenden Sie die Funktionen von Cortana Intelligence zur Echtzeitgewinnung von prädiktiven Einblicken in Fahrzeugzustand und Fahrgewohnheiten." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2016" 
	ms.author="bradsev" />


# Lösungs-Playbook zur Fahrzeugtelemetrieanalyse

Dieses **Menü** bietet Links zu den Kapiteln dieses Playbooks.

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## Übersicht
Supercomputer stehen nicht mehr im Labor, sondern sind jetzt in Ihrer Garage geparkt! Diese modernen Fahrzeuge enthalten unzählige Sensoren, sodass sie Millionen von Ereignissen pro Sekunde verfolgen und überwachen können. Wir gehen davon aus, dass die meisten dieser Fahrzeuge bis 2020 mit dem Internet verbunden sein werden. Stellen Sie sich vor, wie Sie aus dieser riesigen Datensammlung erstklassige Funktionen für Sicherheit, Zuverlässigkeit und ein besseres Fahrgefühl gewährleisten können. Microsoft hat diese Idee mit Cortana Intelligence Wirklichkeit werden lassen.

Cortana Intelligence von Microsoft ist eine vollständig verwaltete Big Data- und erweiterte Analyse-Suite, die es Ihnen ermöglicht, dank Ihrer Daten intelligente Maßnahmen zu ergreifen. Wir möchten Ihnen die Cortana Intelligence Lösungsvorlage für Vehicle Telemetry Analytics (Fahrzeugtelemetrieanalyse) vorstellen. Diese Lösung veranschaulicht, wie Automobilhändler, Automobilhersteller und Versicherungsgesellschaften die Funktionen von Cortana Intelligence verwenden können, um prädiktive Einblicke zum Fahrzeugzustand und den Fahrgewohnheiten in Echtzeit zu gewinnen.

Die Lösung ist als ein [Lambda-Architekturmuster](https://en.wikipedia.org/wiki/Lambda_architecture) implementiert und verfügt dadurch über das vollständige Potential der Cortana Intelligence-Plattform für Echtzeit- und Batchverarbeitung. Sie umfasst einen Fahrzeugtelematiksimulator, nutzt Event Hubs für die Erfassung von Millionen von simulierten Fahrzeugtelemetrieereignissen in Azure, verwendet anschließend Stream Analytics für Echtzeiteinblicke in den Fahrzeugzustand und speichert diese Daten in einem beständigen Speicher für umfangreichere Batchanalysen. Sie nutzt Machine Learning für die Erkennung von Anomalien in Echtzeit und Batchverarbeitung zum Gewinnen prädiktiver Einblicke. HDInsight wird für Datentransformationen verwendet, und Data Factory übernimmt die Orchestrierung, Planung, Ressourcenverwaltung und Überwachung der Batchverarbeitungs-Pipeline. Power BI bietet für diese Lösung abschließend ein umfassendes Dashboard für Visualisierungen von Echtzeitdaten und Predictive Analytics.

## Architektur

![](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png) *Abbildung 1 – Lösungsarchitektur für die Fahrzeugtelemetrieanalyse*

Diese Lösung umfasst die folgenden **Cortana Intelligence-Komponenten** und zeigt ihre End-to-End-Integration:


- **Event Hubs** für das Erfassen von Millionen von Telemetrie-Ereignissen in Azure.
- **Stream Analytics** für das Gewinnen von Einblicken in Echtzeit hinsichtlich des Fahrzeugzustands und Speichern dieser Daten in beständigem Speicher zur umfangreicheren Batchanalyse.
- **Machine Learning** für die Erkennung von Anomalien in Echtzeit und Batchverarbeitung zum Gewinnen prädiktiver Einblicke.
- **HDInsight** wird verwendet, um Daten nach Maß zu transformieren.
- **Data Factory** übernimmt die Orchestrierung, Planung, Ressourcenverwaltung und Überwachung der Batchverarbeitungs-Pipeline.
- **Power BI** bietet dieser Lösung ein umfassendes Dashboard für Visualisierungen von Echtzeitdaten und Predictive Analytics.

Diese Lösung greift auf die folgenden zwei unterschiedlichen **Datenquellen** zu:

- **Simulierte Fahrzeugsignale und -diagnose:** Ein Fahrzeugtelematiksimulator gibt Diagnoseinformationen und Signale aus, die dem Zustand des Fahrzeugs und dem Fahrverhalten zu einem bestimmten Zeitpunkt entsprechen. 
- **Fahrzeugkatalog:** Der Fahrzeugkatalog ist ein Referenzdataset mit der Fahrgestellnummer zur Modellierung der Zuordnung.

<!---HONumber=AcomDC_0601_2016-->