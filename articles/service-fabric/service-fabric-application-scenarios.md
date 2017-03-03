---
title: Anwendungsszenarien und -entwurf | Microsoft Docs
description: "Übersicht über die Kategorien von Cloudanwendungen in Service Fabric. Informationen zum Anwendungsentwurf mit zustandsbehafteten und zustandslosen Diensten"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 3a8ca6ea-b8e9-4bc3-9e20-262437d2528e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/17/2017
ms.author: mfussell
translationtype: Human Translation
ms.sourcegitcommit: d1939e316efb00fb4980c57cbec28920a7475a47
ms.openlocfilehash: feeba5c8e663b9ea571e4410a2d3ad3505394127
ms.lasthandoff: 02/21/2017


---
# <a name="service-fabric-application-scenarios"></a>Service Fabric-Anwendungsszenarien
Azure Service Fabric bietet eine zuverlässige und flexible Plattform, mit der Sie verschiedenste Geschäftsanwendungen und -dienste erstellen und ausführen können. Diese Anwendungen und Microservices können zustandslos oder zustandsbehaftet sein, und ihre Ressourcen werden zur Maximierung der Effizienz auf die virtuellen Maschinen verteilt. In der einzigartigen Architektur von Service Fabric können Sie Echtzeitdatenanalysen, In-Memory-Berechnungen, parallele Transaktionen und die Ereignisverarbeitung in Ihren Anwendungen ausführen. Sie können Ihre Anwendungen je nach den sich ändernden Ressourcenanforderungen problemlos zentral (bzw. horizontal oder vertikal) hoch- oder herunterskalieren.

Die Service Fabric-Plattform in Azure ist ideal für die folgenden Kategorien von Anwendungen geeignet:

* **Hoch verfügbare Dienste:**Durch das Erstellen mehrerer sekundärer Dienstreplikate bieten Service Fabric-Dienste ein schnelles Failover. Wenn ein Knoten, Prozess oder individueller Dienst aufgrund eines hardwarebedingten oder anderen Fehlers ausfällt, wird eines der sekundären Replikate zu einem primären Replikat weitergeleitet – mit minimaler Dienstunterbrechung.
* **Skalierbare Dienste**: Einzelne Dienste können partitioniert werden, sodass der Zustand im Cluster horizontal hochskaliert werden kann. Darüber hinaus können einzelne Dienste im Handumdrehen erstellt und entfernt werden. Dienste können je nach Ressourcenanforderungen schnell und einfach horizontal von einigen wenigen Instanzen auf einigen wenigen Knoten auf Tausende Instanzen auf vielen Knoten hochskaliert und wieder herunterskaliert werden. Mithilfe von Service Fabric können Sie diese Dienste erstellen und ihren gesamten Lebenszyklus verwalten.
* **Berechnung nicht statischer Daten:** Mit Service Fabric können Sie zustandsbehaftete Anwendungen mit einer Vielzahl von Daten, Ein- und Ausgaben und hohem Rechenaufwand erstellen. Service Fabric ermöglicht die Zusammenlegung von Verarbeitung (Berechnung) und Daten in Anwendungen. Wenn Ihre Anwendung auf Daten zugreifen muss, ist in der Regel mit einer Netzwerklatenz zu rechnen, die auf die Verwendung eines externen Datencache oder einer externen Speicherebene zurückzuführen ist. Mit zustandsbehafteten Service Fabric-Diensten lässt sich diese Latenz beseitigen, was eine höhere Leistung bei Lese- und Schreibvorgängen ermöglicht. Beispiel: Angenommen, Ihre Anwendung unterbreitet Kunden nahezu in Echtzeit Vorschläge. Bei den Anforderungen muss eine Roundtrip-Zeit von unter 100 Millisekunden erreicht werden. Im Gegensatz zum standardmäßigen Implementierungsmodell, bei dem die benötigten Daten aus einem Remotespeicher abgerufen werden müssen, bieten die Latenz- und Leistungsmerkmale von Service Fabric-Diensten (bei denen die Berechnung der Vorschlagsauswahl mit den Daten und Regeln zusammengelegt wird) den Benutzern eine schnell reagierende Umgebung.  
* **Sitzungsbasierte interaktive Anwendungen:** Service Fabric ist sehr nützlich, wenn Ihre Anwendungen (beispielsweise Onlinespiele oder Instant Messaging) Lese- und Schreibvorgänge mit geringer Latenz erfordern. Mit Service Fabric können Sie diese interaktiven, zustandsbehafteten Anwendungen erstellen, ohne einen separaten Speicher oder Cache erstellen zu müssen, der für zustandslose Anwendungen erforderlich ist. (Dies erhöht die Latenz und kann zu Konsistenzproblemen führen.)
* **Verteilte Graphverarbeitung:** Durch den Ausbau sozialer Netzwerke ist der Bedarf, umfangreiche Graphen parallel zu analysieren, deutlich gestiegen. Die schnelle Skalierung und parallele Ladevorgänge machen Service Fabric zu einer idealen Plattform für die Verarbeitung umfangreicher Graphen. Mit Service Fabric können Sie hoch skalierbare Dienste für Gruppen erstellen, z. B. für soziale Netzwerke, Business Intelligence und wissenschaftliche Forschung.
* **Datenanalyse und Workflows**: Die schnellen Lese- und Schreibvorgänge von Service Fabric machen Anwendungen möglich, mit denen Ereignisse oder Datenströme zuverlässig verarbeitet werden müssen. Service Fabric ermöglicht darüber hinaus Anwendungen, die Verarbeitungspipelines beschreiben, bei denen Ergebnisse zuverlässig sein und ohne Datenverlust in die nächste Verarbeitungsphase weitergeleitet werden müssen. Dazu zählen Transaktions- und Finanzsysteme, bei denen Datenkonsistenz und Berechnungsgewährleistung von größter Wichtigkeit sind.
* **Datensammlung, -verarbeitung und IoT:** Da Service Fabric durch seine zustandsbehafteten Dienste große Datenmengen mit geringer Latenz verarbeitet, eignet es sich ideal für die Datenverarbeitung auf Millionen von Geräten, auf denen die Daten für das Gerät und die Berechnung zusammengestellt werden.
Wir hatten schon mehrere Kunden, die IoT-Systeme mit Service Fabric erstellt haben, darunter [BMW](https://blogs.msdn.microsoft.com/azureservicefabric/2016/08/24/service-fabric-customer-profile-bmw-technology-corporation/), [Schneider Electric](https://blogs.msdn.microsoft.com/azureservicefabric/2016/08/05/service-fabric-customer-profile-schneider-electric/) und [Mesh Systems](https://blogs.msdn.microsoft.com/azureservicefabric/2016/06/20/service-fabric-customer-profile-mesh-systems/).

## <a name="application-design-case-studies"></a>Fallstudien zum Anwendungsentwurf
Im [Service Fabric-Teamblog](https://blogs.msdn.microsoft.com/azureservicefabric/tag/customer-profile/) und auf der [Website zu Microservice-Lösungen](https://azure.microsoft.com/solutions/microservice-applications/) finden Sie eine Reihe von Fallstudien, die veranschaulichen, wie Anwendungen unter Verwendung von Service Fabric entworfen werden.

## <a name="design-applications-composed-of-stateless-and-stateful-microservices"></a>Entwerfen von Anwendungen, die aus zustandslosen und zustandsbehafteten Microservices bestehen
Die Erstellung von Anwendungen mit Azure-Clouddienst-Workerrollen ist ein Beispiel für zustandslose Dienste. Im Gegensatz dazu behalten zustandsbehaftete Microservices den autorisierenden Zustand über die Anforderung und ihre Antwort hinaus bei. Dadurch lässt sich über einfache APIs, die für replikationsgestützte Transaktionsgarantien sorgen, eine hohe Verfügbarkeit und Konsistenz erreichen. Zustandsbehaftete Service Fabric-Dienste bieten hohe Verfügbarkeit für alle Arten von Anwendungen – nicht nur für Datenbanken und andere Datenspeicher. Hierbei handelt es sich um einen natürlichen Prozess: Anwendungen haben bereits den Wandel von der Verwendung rein relationaler Datenbanken für hohe Verfügbarkeit zur Verwendung von NoSQL-Datenbanken vollzogen. Nun werden die Anwendung selbst hochverfügbar, und Daten werden zur Leistungsoptimierung innerhalb der Anwendung verwaltet, ohne dafür Kompromisse bei Zuverlässigkeit, Konsistenz und Verfügbarkeit einzugehen.

Bei der Erstellung von Anwendungen, die sich aus Microservices zusammensetzen, haben Sie es normalerweise mit einer Kombination aus zustandslosen Web-Apps (ASP.NET, Node.js usw.) zu tun, die zustandslose und zustandsbehaftete Geschäftsdienste der mittleren Ebene aufrufen, welche alle im gleichen Service Fabric-Cluster bereitgestellt werden und die Service Fabric-Bereitstellungsbefehle verwenden. Jeder dieser Dienste ist in puncto Skalierung, Zuverlässigkeit und Ressourcenverwendung unabhängig, was eine deutlich höhere Flexibilität bei der Entwicklung sowie bei der Verwaltung des Lebenszyklus bedeutet.

Zustandsbehaftete Microservices vereinfachen Anwendungsentwürfe, da durch sie die Notwendigkeit zusätzlicher Warteschlangen und Caches entfällt, die traditionell erforderlich waren, um die Verfügbarkeits- und Latenzanforderungen von rein zustandslosen Anwendungen zu erfüllen. Da zustandsbehaftete Dienste an sich bereits hochverfügbar sind und eine niedrige Latenz bieten, fällt der Verwaltungsaufwand in Ihrer Anwendung insgesamt geringer aus. In der folgenden Abbildung werden die Unterschiede zwischen einer zustandslosen und einer zustandsbehafteten Anwendung veranschaulicht. Zustandsbehaftete Dienste verringern durch Nutzung der Programmiermodelle [Reliable Services](service-fabric-reliable-services-introduction.md) und [Reliable Actors](service-fabric-reliable-actors-introduction.md) die Komplexität der Anwendung. Gleichzeitig wird eine hohe Durchsatzrate mit geringer Latenz erzielt.

## <a name="an-application-built-using-stateless-services"></a>Eine mit zustandslosen Diensten erstellte Anwendung
![Anwendung mit zustandslosen Diensten][Image1]

## <a name="an-application-built-using-stateful-services"></a>Eine mit zustandsbehafteten Diensten erstellte Anwendung
![Anwendung mit zustandslosen Diensten][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nächste Schritte
* Beginnen Sie mit der Erstellung zustandsloser und zustandsbehafteter Dienste mit den Service Fabric-Programmiermodellen [Reliable Services](service-fabric-reliable-services-quick-start.md) und [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Erfahren Sie mehr über [Muster und Szenarien](service-fabric-patterns-and-scenarios.md).
* Erfahren Sie mehr über [Kundenfallstudien](https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=qDJnf86yC_5206218965
).
* Weitere Informationen finden Sie in den folgenden Themen:
  * [Mehr über Microservices erfahren](service-fabric-overview-microservices.md)
  * [Definieren und Verwalten des Dienstzustands](service-fabric-concepts-state.md)
  * [Verfügbarkeit der Service Fabric-Dienste](service-fabric-availability-services.md)
  * [Skalieren von Service Fabric-Anwendungen](service-fabric-concepts-scalability.md)
  * [Partitionieren von Service Fabric-Diensten](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg

