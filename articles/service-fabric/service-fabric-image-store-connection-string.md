---
title: Verbindungszeichenfolge für den Azure Service Fabric-Imagespeicher | Microsoft-Dokumentation
description: Grundlegendes zur Imagespeicher-Verbindungszeichenfolge
services: service-fabric
documentationcenter: .net
author: alexwun
manager: timlt
editor: ''
ms.assetid: 00f8059d-9d53-4cb8-b44a-b25149de3030
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2018
ms.author: alexwun
ms.openlocfilehash: 7d164fea62afac83c4fe2216c56a9980d9279f3a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207127"
---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>Grundlegendes zur ImageStoreConnectionString-Einstellung

In einigen Teilen unserer Dokumentation erwähnen wir kurz den Parameter „ImageStoreConnectionString“, ohne zu beschreiben, was es damit auf sich hat. Nach dem Lesen eines Artikels wie [Bereitstellen und Entfernen von Anwendungen mit PowerShell][10] gewinnt man den Eindruck, dass man den Wert so, wie er im Clustermanifest des Zielclusters angezeigt wird, nur kopieren/einfügen muss. Die Einstellung muss also pro Cluster konfigurierbar sein, aber bei der Erstellung eines Clusters über das [Azure-Portal][11] gibt es keine Option zum Konfigurieren dieser Einstellung, und sie lautet immer „fabric:ImageStore“. Wo liegt also der Zweck dieser Einstellung?

![Clustermanifest][img_cm]

Service Fabric begann als Plattform für den Microsoft-internen Gebrauch durch vielen verschiedene Teams, daher sind einige Aspekte extrem anpassbar – der „Imagespeicher“ ist ein solcher Aspekt. Im Wesentlichen ist der Imagespeicher ein austauschbares Repository zum Speichern von Anwendungspaketen. Wenn Ihre Anwendung für einen Knoten im Cluster bereitgestellt wird, lädt der jeweilige Knoten den Inhalt des Anwendungspakets aus dem Imagespeicher herunter. ImageStoreConnectionString ist eine Einstellung, die alle erforderlichen Informationen enthält, damit sowohl Clients als auch Knoten den richtigen Imagespeicher für einen bestimmten Cluster finden können.

Es gibt derzeit drei möglichen Arten von Imagespeicheranbietern, und ihre entsprechenden Verbindungszeichenfolgen lauten wie folgt:

1. Imagespeicherdienst: „fabric:ImageStore“

2. Dateisystem: „file:[file system path]“

3. Azure Storage: „xstore:DefaultEndpointsProtocol=https;AccountName=[...];AccountKey=[...];Container=[...]“

Der in der Produktion verwendete Anbietertyp ist der Imagespeicherdienst. Dabei handelt es sich um einen zustandsbehafteten persistenten Systemdienst, der Ihnen im Service Fabric Explorer angezeigt wird. 

![Imagespeicherdienst][img_is]

Durch das Hosten des Imagespeichers in einem Systemdienst innerhalb des Clusters selbst entfallen externe Abhängigkeiten für das Paketrepository, und wir erhalten mehr Kontrolle über den Speicherort. Künftige Verbesserungen rund um den Imagespeicher werden wahrscheinlich in erster Linie – wenn nicht ausschließlich – den Imagespeicheranbieter betreffen. Die Verbindungszeichenfolge für den Anbieter des Imagespeicherdiensts enthält keine eindeutigen Informationen, da der Client bereits mit dem Zielcluster verbunden ist. Dem Client muss nur bekannt sein, dass Protokolle für den Systemdienst verwendet werden müssen.

Der Dateisystemanbieter wird während der Entwicklung anstelle des Imagespeicherdiensts für lokale One-Box-Cluster verwendet, um den Cluster etwas schneller zu starten. Der Unterschied ist in der Regel gering, aber für die meisten Entwickler eine nützliche Optimierung. Es ist auch mit den anderen Speicheranbietertypen möglich, einen lokalen One-Box-Cluster bereitzustellen, aber dazu besteht normalerweise kein Grund, weil der Entwicklungs-/Testworkflow unabhängig vom Anbieter gleich bleibt. Der Azure Storage-Anbieter ist nur für die Unterstützung alter Cluster bestimmt, die vor der Einführung des Imagespeicher-Dienstanbieters bereitgestellt wurden.

Darüber hinaus sollten weder der Dateisystemanbieter noch der Azure Storage-Anbieter für die gemeinsame Verwendung eines Imagespeichers durch mehrere Cluster verwendet werden – dies führt zu einer Beschädigung der Clusterkonfigurationsdaten, da alle Cluster widersprüchliche Daten in den Imagespeicher schreiben können. Verwenden Sie zum Freigeben von bereitgestellten Anwendungspaketen zwischen mehreren Clustern stattdessen [SFPKG][12]-Dateien. Diese können in beliebige externe Speicher mit einem Download-URI hochgeladen werden.

Auch wenn ImageStoreConnectionString konfigurierbar ist, verwenden Sie daher in der Regel nur die Standardeinstellung. Beim Veröffentlichen in Azure mithilfe von Visual Studio wird der Parameter automatisch entsprechend für Sie festgelegt. Für die programmgesteuerte Bereitstellung für in Azure gehostete Cluster lautet die Verbindungszeichenfolge immer „fabric:ImageStore“. Im Zweifelsfall kann ihr Wert jedoch immer durch das Abrufen des Clustermanifests über [PowerShell](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricclustermanifest), [.NET](https://msdn.microsoft.com/library/azure/mt161375.aspx) oder [REST](https://docs.microsoft.com/rest/api/servicefabric/get-a-cluster-manifest) überprüft werden. Sowohl lokale Test- als auch Produktionscluster sollten immer auch für die Verwendung des Anbieters für den Imagespeicherdienst konfiguriert werden.

### <a name="next-steps"></a>Nächste Schritte
[Bereitstellen und Entfernen von Anwendungen mit PowerShell][10]

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md
[12]: service-fabric-package-apps.md#create-an-sfpkg
