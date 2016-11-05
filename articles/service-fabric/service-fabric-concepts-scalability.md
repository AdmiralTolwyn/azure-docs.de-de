---
title: Skalierbarkeit von Service Fabric-Diensten | Microsoft Docs
description: Beschreibt, wie Sie die Service Fabric-Dienste skalieren.
services: service-fabric
documentationcenter: .net
author: appi101
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/10/2016
ms.author: aprameyr

---
# Skalieren von Service Fabric-Anwendungen
Azure Service Fabric erleichtert das Erstellen von skalierbaren Anwendungen durch den Lastenausgleich von Diensten, Partitionen und Replikaten auf allen Knoten in einem Cluster. Dies ermöglicht eine maximale Ressourcenverwendung.

Hohe Skalierbarkeit für Service Fabric-Anwendungen kann auf zwei Arten erreicht werden:

1. Skalieren auf Partitionsebene
2. Skalierung auf Dienstnamensebene

## Skalieren auf Partitionsebene
Service Fabric unterstützt das Partitionieren eines einzelnen Diensts in mehrere kleinere Partitionen. Die [Übersicht über die Partitionierung](service-fabric-concepts-partitioning.md) enthält Informationen zu den Typen von Partitionierungsschemas, die unterstützt werden. Die Replikate der einzelnen Partitionen sind auf den Knoten im Cluster verteilt. Angenommen, ein Dienst verwendet das Bereichspartitionierungsschema mit einem niedrigen Schlüssel 0, einem hohen Schlüssel 99 und 4 Partitionen. In einem Drei-Knoten-Cluster kann der Dienst mit vier Replikaten, welche die Ressourcen auf den einzelnen Knoten gemeinsam nutzen, wie in der folgenden Abbildung gezeigt ausgelegt werden.

![Partitionslayout mit drei Knoten](./media/service-fabric-concepts-scalability/layout-three-nodes.png)

Wenn Sie die Anzahl der Knoten erhöhen, verschiebt Service Fabric einige der Replikate auf leere Knoten, um die Ressourcen auf den neuen Knoten zu nutzen. Wenn die Anzahl der Knoten auf 4 erhöht wird, werden auf jedem Knoten (der verschiedenen Partitionen) 3 Replikate ausgeführt, wodurch Ressourcennutzung und Leistung verbessert werden.

![Partitionslayout mit vier Knoten](./media/service-fabric-concepts-scalability/layout-four-nodes.png)

## Skalierung auf Dienstnamensebene
Eine Dienstinstanz ist eine bestimmte Instanz eines Anwendungsnamens und eines Diensttypnamens (siehe [Service Fabric-Anwendungslebenszyklus](service-fabric-application-lifecycle.md)). Während der Erstellung eines Dienstes geben Sie das zu verwendende Partitionierungsschema an ([Partitionieren von Service Fabric-Diensten](service-fabric-concepts-partitioning.md)).

Die erste Ebene der Skalierung erfolgt nach Dienstnamen. Sie können neue Instanzen eines Diensts mit unterschiedlichen Partitionierungsebenen erstellen, wenn die älteren Instanzen ausgelastet sind. So können neue Dienstconsumer die Instanzen mit geringerer Auslastung anstelle der ausgelasteten Instanzen verwenden.

Eine Option, die Kapazität zu erhöhen, als auch die Anzahl der Partitionen zu erhöhen oder zu verringern, ist, eine neue Dienstinstanz mit einem neuen Partitionierungsschema zu erstellen. Dies steigert die Komplexität, da verarbeitende Clients wissen müssen, wann und wie sie einen Dienst mit einem anderen Namen verwenden sollen.

### Beispielszenario: Eingebettete Daten
Ein mögliches Szenario ist die Verwendung von Datumsinformationen im Dienstnamen. Sie können beispielsweise eine Dienstinstanz mit einem bestimmten Namen für Kunden verwenden, die in 2013 beigetreten sind, und eine Dienstinstanz mit einem anderen Namen für Kunden, die in 2014 beigetreten sind. Dieses Benennungsschema ermöglicht eine programmgesteuerte Erhöhung der Namen in Abhängigkeit des Datums (die Dienstinstanz für 2014 kann nach Bedarf erstellt werden, bevor 2014 beginnt).

Dieser Ansatz basiert jedoch auf Clients, die anwendungsspezifische Namensinformationen verwenden, die nicht zum Kenntnisbereich von Service Fabric gehören.

* *Mit einer Namenskonvention*: Erstellen Sie bei Bereitstellung Ihrer Anwendung in 2013 einen Dienst mit dem Namen „fabric:/app/service2013“. Erstellen Sie im zweiten Quartal 2013 einen weiteren Dienst mit dem Namen „fabric:/app/service2014“. Beide Dienste sind vom gleichen Diensttyp. Bei diesem Ansatz muss der Client Logik zum Erstellen des entsprechenden Dienstnamens basierend auf dem Jahr einsetzen.
* *Mit einem Suchdienst*: Ein anderer Ansatz ist die Bereitstellung eines sekundären „Suchdiensts“, der den Dienstnamen für einen gewünschten Schlüssel bereitstellen kann. Die neuen Dienstinstanzen können dann vom Suchdienst erstellt werden. Der Suchdienst selbst behält keine Anwendungsdaten bei, sondern nur Daten zu den Namen der Dienste, die der Suchdienst erstellt. Daher kontaktiert der Client im obigen Beispiel zuerst den Suchdienst, um den Namen des Diensts zu erhalten, der die Daten für ein bestimmtes Jahr verarbeitet. Anschließend verwendet der Client diesen Dienstnamen, um den eigentlichen Vorgang auszuführen. Das Ergebnis der ersten Suche kann zwischengespeichert werden.

## Nächste Schritte
Weitere Informationen zu den Service Fabric-Konzepten finden Sie hier:

* [Verfügbarkeit der Service Fabric-Dienste](service-fabric-availability-services.md)
* [Partitionieren von Service Fabric-Diensten](service-fabric-concepts-partitioning.md)
* [Definieren und Verwalten von Zuständen](service-fabric-concepts-state.md)

<!---HONumber=AcomDC_0810_2016-->