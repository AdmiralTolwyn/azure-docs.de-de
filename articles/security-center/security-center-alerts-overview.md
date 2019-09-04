---
title: Sicherheitswarnungen in Azure Security Center | Microsoft-Dokumentation
description: In diesem Dokument erfahren Sie, was Sicherheitswarnungen sind und welche Arten von Sicherheitswarnungen in Azure Security Center zur Verfügung stehen.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/25/2019
ms.author: v-mohabe
ms.openlocfilehash: 1a3ce29750351d6037b376ebb93eb73e141c0727
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2019
ms.locfileid: "70047583"
---
# <a name="security-alerts-in-azure-security-center"></a>Sicherheitswarnungen in Azure Security Center

Für die vielen verschiedenen Ressourcentypen stehen in Azure Security Center unterschiedliche Warnungen zur Verfügung. Security Center generiert Warnungen sowohl für in Azure bereitgestellte Ressourcen als auch für Ressourcen, die lokal und in Hybrid Cloud-Umgebungen bereitgestellt wurden.

Im Standard-Tarif von Azure Security Center sind erweiterte Erkennungsverfahren verfügbar. Eine kostenlose Testversion ist verfügbar. Über die [Sicherheitsrichtlinie](security-center-pricing.md)können Sie die Tarifauswahl ändern. Weitere Informationen zu den Preisen finden Sie auf der [Security Center-Seite](https://azure.microsoft.com/pricing/details/security-center/) .

## <a name="respond-threats">Reagieren auf die heutigen Bedrohungen</a>

In den letzten 20 Jahren hat sich die Bedrohungslandschaft stark verändert. In der Vergangenheit mussten sich Unternehmen in der Regel nur Sorgen um eine mögliche Verunstaltung Ihrer Websites durch einzelne Angreifer machen, die häufig nur Ihre Fähigkeiten austesten wollten. Die Angreifer von heute sind dagegen viel besser vorbereitet und ausgerüstet. Sie verfolgen häufig bestimmte finanzielle oder strategische Ziele. Außerdem stehen ihnen mehr Ressourcen zur Verfügung, da sie von Staaten oder der organisierten Kriminalität finanziert werden.

Diese Veränderungen haben dazu geführt, dass Angreifer heutzutage sehr professionell vorgehen können. Sie sind nicht mehr daran interessiert, nur Websites zu verunstalten. Es geht vielmehr um das Stehlen von Informationen, Finanzkonten und privaten Daten. Diese Daten werden dann auf dem freien Markt zu Geld gemacht oder für bestimmte geschäftliche, politische oder militärische Zwecke genutzt. Noch gefährlicher als Angreifer mit finanziellen Motiven sind Angreifer, die in Netzwerke eindringen, um die Infrastruktur oder Personen zu schädigen.

Als Reaktion stellen Organisationen häufig verschiedene Punktlösungen bereit, die nach bekannten Angriffssignaturen suchen, um die Grenzen oder Endpunkte des Unternehmens abzusichern. Diese Lösungen neigen dazu, eine große Menge von so genannten Low-Fidelity-Warnungen zu generieren, die von einem Sicherheitsexperten selektiert und untersucht werden müssen. Die meisten Organisationen verfügen nicht über die Zeit und das Wissen, um auf diese Warnungen zu reagieren, sodass diese häufig nicht untersucht werden können.  

Darüber hinaus haben die Angreifer ihre Methoden weiterentwickelt und können viele signaturbasierte Verteidigungen umgehen und sich [an Cloudumgebungen anpassen](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). Neue Ansätze sind erforderlich, um neue Bedrohungen schnell identifizieren und die Erkennung und Reaktion beschleunigen zu können.

## <a name="what-are-security-alerts"></a>Was sind Sicherheitswarnungen?

Warnungen sind die Benachrichtigungen, die Security Center generiert, wenn Bedrohungen für Ihre Ressourcen erkannt werden. Security Center priorisiert die Warnungen und listet sie zusammen mit den Informationen auf, die erforderlich sind, um das Problem schnell zu untersuchen. Security Center stellt außerdem Empfehlungen zur Reaktion auf einen Angriff bereit.

## Wie funktioniert die Bedrohungserkennung von Security Center? <a name="detect-threats"></a>

Microsoft-Sicherheitsexperten suchen ständig nach neuen Bedrohungen. Aufgrund der globalen Präsenz von Microsoft in der Cloud und in lokalen Umgebungen basieren haben sie Zugriff auf umfassende Telemetriedaten. Diese weit reichende und verschiedenartige Sammlung von Datasets ermöglicht die Erkennung neuer Angriffsmuster und Trends für lokale Privatkunden- und Unternehmensprodukte sowie für seine Onlinedienste. Auf diese Weise können die Erkennungsalgorithmen von Security Center schnell aktualisiert werden, wenn Angreifer neue und immer anspruchsvollere Exploit-Verfahren nutzen. So können Sie mit der rasanten Entwicklung von Bedrohungen Schritt halten.

Security Center sammelt, analysiert und integriert Protokolldaten Ihrer Azure-Ressourcen und aus dem Netzwerk, um echte Bedrohungen zu erkennen und falsch positive Ergebnisse zu reduzieren. Dies funktioniert auch mit verbundenen Partnerlösungen wie Firewalls und Lösungen zum Schutz von Endpunkten. Security Center analysiert diese Informationen und korreliert sie häufig mit Informationen aus mehreren Quellen, um Bedrohungen zu identifizieren.

![Security Center – Datensammlung und -darstellung](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

Für Security Center werden professionelle Sicherheitsanalysen genutzt, die weit über signaturbasierte Ansätze hinausgehen. Bahnbrechende Weiterentwicklungen der Big Data- und [Machine Learning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/)-Technologie werden genutzt, um Ereignisse im gesamten Cloudfabric auszuwerten. So können Bedrohungen erkannt werden, die bei Verwendung von manuellen Konzepten nicht identifiziert werden können, und die Entwicklung von Angriffen kann vorhergesagt werden. Zu diesen Sicherheitsanalysen gehört Folgendes:

* **Integrierte Informationen zu Bedrohungen:** Es wird nach bekannten Angreifern gesucht, indem globale Bedrohungsinformationen (Threat Intelligence) von Microsoft-Produkten und -Diensten, von der Microsoft Digital Crimes Unit (DCU), vom Microsoft Security Response Center (MSRC) sowie von externen Feeds angewandt werden.
* **Verhaltensanalyse:** Es werden bekannte Muster angewandt, um schädliches Verhalten zu erkennen.
* **Anomalieerkennung:** Es werden statistische Profile erstellt, um typische Verlaufsdaten zu erhalten. Sie werden benachrichtigt, wenn es zu Abweichungen von der Baseline kommt, die einem potenziellen Angriffsvektor entsprechen.

In den folgenden Themen wird jede dieser Analysen ausführlicher erläutert.

### <a name="integrated-threat-intelligence"></a>Integrierte Informationen zu Bedrohungen

Microsoft verfügt über eine große Menge von Informationen zu globalen Bedrohungen. Die Telemetriedaten stammen aus mehreren Quellen, z.B. Azure, Office 365, Microsoft CRM Online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital Crimes Unit (DCU) und Microsoft Security Response Center (MSRC). Die Sicherheitsexperten erhalten außerdem Informationen zu Bedrohungen, die zwischen großen Cloudanbietern ausgetauscht werden, und haben Threat Intelligence-Feeds von Drittanbietern abonniert. Azure Security Center kann diese Informationen verwenden, um Sie vor Bedrohungen durch bekannte Angreifer zu warnen.

### <a name="behavioral-analytics"></a>Verhaltensanalyse

Die Verhaltensanalyse ist ein Verfahren, bei dem Daten mit einer Sammlung bekannter Muster analysiert und verglichen werden. Bei diesen Mustern handelt es sich aber nicht nur um einfache Signaturen. Sie werden anhand von komplexen Machine Learning-Algorithmen bestimmt, die auf große Datasets angewendet werden. Außerdem werden sie anhand einer sorgfältigen Analyse von schädlichem Verhalten durch erfahrene Analysten bestimmt. Azure Security Center kann die Verhaltensanalyse verwenden, um kompromittierte Ressourcen basierend auf der Analyse der Protokolle von virtuellen Computern, virtuellen Netzwerkgeräten, Fabric-Protokolle, Absturzabbilder und anderen Quellen zu identifizieren.

Außerdem ist eine Korrelation mit anderen Signalen vorhanden, damit weitere Beweise für eine größere Aktion ermittelt werden können. So können Ereignisse identifiziert werden, die mit vorhandenen Indikatoren für eine Kompromittierung übereinstimmen. 

### <a name="anomaly-detection"></a>Anomalieerkennung

Azure Security Center nutzt auch die Anomalieerkennung, um Bedrohungen zu identifizieren. Im Gegensatz zur Verhaltensanalyse (basiert auf bekannten Mustern, die aus großen Datasets abgeleitet werden) ist die Anomalieerkennung „personalisierter“ und nutzt Baselines, die speziell für Ihre Bereitstellungen gelten. Machine Learning wird angewendet, um die normale Aktivität für Ihre Bereitstellungen zu ermitteln. Anschließend werden Regeln generiert, um Ausreißerbedingungen zu definieren, die ein Sicherheitsereignis darstellen können.

## <a name="continuous-monitoring-and-assessments"></a>Kontinuierliche Überwachung und Bewertungen

Azure Security Center profitiert von Sicherheitsforschungs- und Data Science-Teams bei Microsoft, die ständig Änderungen der Bedrohungslandschaft überwachen. Dies umfasst Folgendes:

* **Threat Intelligence-Überwachung**: Informationen zu Bedrohungen (Threat Intelligence) umfassen Mechanismen, Indikatoren, Auswirkungen und nützliche Hinweise zu vorhandenen oder neuen Bedrohungen. Diese Informationen werden in der Sicherheitscommunity bereitgestellt, und Microsoft überwacht fortlaufend Threat Intelligence-Feeds von internen und externen Quellen.
* **Signalaustausch**: Die Erkenntnisse der Sicherheitsteams aus dem großen Microsoft-Portfolio mit Clouddiensten und lokalen Diensten, Servern und Clientendpunkt-Geräten werden ausgetauscht und analysiert.
* **Microsoft-Sicherheitsexperten**: Ständiger Austausch mit Teams von Microsoft, die sich mit speziellen Sicherheitsfeldern beschäftigen, z.B. Forensik und Erkennung von Webangriffen.
* **Erkennungsoptimierung**: Algorithmen werden für echte Kundendatasets ausgeführt, und Sicherheitsexperten arbeiten mit Kunden zusammen, um die Ergebnisse auszuwerten. Richtige und falsche Positivmeldungen werden verwendet, um Machine Learning-Algorithmen zu verfeinern.

Diese kombinierten Verfahren führen zu neuen und verbesserten Erkennungsergebnissen, von denen Sie sofort profitieren können. Sie müssen dabei nichts unternehmen.

## <a name="security-alert-types">Arten von Sicherheitswarnungen</a>

In den folgenden Themen finden Sie Informationen zu den verschiedenen Warnungen für bestimmte Ressourcentypen:

* [Warnungen für IaaS-VMs und -Server](security-center-alerts-iaas.md)
* [Warnungen für Native Compute](security-center-alerts-compute.md)
* [Warnungen für Datendienste](security-center-alerts-data-services.md)

In den folgenden Themen erfahren Sie, wie Security Center die verschiedenen Telemetriedaten nutzt, die im Rahmen der Azure-Infrastrukturintegration gesammelt werden, um zusätzliche Schutzebenen für in Azure bereitstellte Ressourcen zu implementieren:

* [Bedrohungserkennung für die Azure-Dienstebene in Azure Security Center](security-center-alerts-service-layer.md)
* [Integration mit Azure-Sicherheitsprodukten](security-center-alerts-integration.md)

## <a name="what-are-security-incidents"></a>Was sind Sicherheitsincidents?

Ein Sicherheitsincident ist eine Sammlung verwandter Warnungen (anstelle einer Auflistung der einzelnen Warnungen). In Security Center werden verschiedene Warnungen und Signale mit geringer Genauigkeit mittels [Korrelation von intelligenten Cloudwarnungen](security-center-alerts-cloud-smart.md) zu Sicherheitsincidents korreliert.

Dank Incidents bietet Ihnen Security Center eine zentrale Ansicht für einen Angriffsversuch und alle zugehörigen Warnungen. In dieser Ansicht können Sie schnell nachvollziehen, welche Aktionen der Angreifer ausgeführt hat und welche Ressourcen betroffen sind. Weitere Informationen hierzu finden Sie unter [Korrelation von intelligenten Cloudwarnungen](security-center-alerts-cloud-smart.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurden die unterschiedlichen Arten von Sicherheitswarnungen in Security Center beschrieben. Weitere Informationen finden Sie unter

* [Planungs- und Betriebshandbuch für Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Azure Security Center – häufig gestellte Fragen](https://docs.microsoft.com/azure/security-center/security-center-faq)

