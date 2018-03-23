---
title: Schützen personenbezogener Daten in Microsoft Azure | Microsoft-Dokumentation
description: Dieser Artikel dient zur Unterstützung bei der Verwendung von Azure zum Schützen von personenbezogenen Daten und zur Einhaltung der Datenschutz-Grundverordnung (DSGVO).
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.custom: ''
ms.openlocfilehash: 741fb17be315faacef6483cbaaa565136622cb45
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/08/2018
---
# <a name="protect-personal-data-in-microsoft-azure"></a>Schützen personenbezogener Daten in Microsoft Azure

Dieser Artikel bildet die Einführung in eine Reihe von Artikeln zur Verwendung von Sicherheitstechnologien und -diensten von Azure zum Schützen personenbezogener Daten. Dies ist eine wesentliche Anforderung für viele unternehmens- und branchenspezifische Compliance- und Governanceinitiativen. Beispielsweise können Sie die Informationen in dieser Artikelreihe zur Einhaltung der Datenschutz-Grundverordnung (DSGVO). nutzen. Hier werden das Szenario, die Problembeschreibung und die Unternehmensziele beschrieben.

## <a name="scenario-and-problem-statement"></a>Szenario und Problembeschreibung

Ein großes Kreuzfahrtunternehmen mit Hauptsitz in den USA expandiert und bietet Routen im Mittelmeer, der Adria und der Ostsee sowie zu den Britischen Inseln an. Zu diesem Zweck hat das Unternehmen mehrere kleinere Kreuzfahrtunternehmen in Italien, Deutschland, Dänemark und Großbritannien erworben.

Das Unternehmen verwendet Microsoft Azure zum Speichern von Unternehmensdaten in der Cloud. Dies kann z.B. folgende Kunden- und/oder Mitarbeiterinformationen umfassen:

- Adressen
- Telefonnummern
- Steueridentifikationsnummern
- Kreditkarteninformationen

Das Unternehmen muss den Schutz der Kunden- und Mitarbeiterdaten gewährleisten und gleichzeitig den jeweiligen Abteilungen Zugriff auf die Daten ermöglichen (z.B. der Lohnbuchhaltung oder der Reservierungsabteilung).

## <a name="company-goals"></a>Unternehmensziele 

- Datenquellen, die personenbezogene Daten enthalten, werden während der Speicherung im Cloudspeicher verschlüsselt.

- Personenbezogene Daten, die zwischen verschiedenen Standorten übertragen werden, werden während der Übertragung verschlüsselt. Dies trifft sowohl dann zu, wenn die Daten über das virtuelle Netzwerk übertragen werden, als auch bei einer Übertragung über das Internet zwischen dem Rechenzentrum des Unternehmens und der Azure-Cloud.

- Die Vertraulichkeit und Integrität personenbezogener Daten wird vor nicht autorisiertem Zugriff durch Technologien zur sicheren Identitätsverwaltung und Zugriffssteuerung geschützt.

- Personenbezogene Daten werden durch Überwachung auf Sicherheitsrisiken und Bedrohungen vor Offenlegung durch Datenverletzung geschützt.

- Der Sicherheitsstatus von Azure-Diensten, mit denen personenbezogene Daten gespeichert oder übertragen werden, wird bewertet, um Möglichkeiten zum besseren Schutz personenbezogener Daten zu ermitteln.

## <a name="data-protection-guidance"></a>Anleitungen zum Schutz von Daten

Die folgenden Artikel enthalten technische Anleitungen, anhand derer die oben genannten Ziele zum Schutz personenbezogener Daten erreicht werden können:

- [Azure-Verschlüsselungstechnologien](protect-personal-data-at-rest.md)

- [Azure-Verschlüsselungstechnologien](protect-personal-data-in-transit-encryption.md)

- [Identitäts- und Zugriffstechnologien in Azure](protect-personal-data-identity-access-controls.md)

- [Netzwerksicherheitstechnologien in Azure](protect-personal-data-network-security.md)

- [Azure Security Center](protect-personal-data-azure-security-center.md)



## <a name="next-steps"></a>Nächste Schritte

- [Website für Informationen zur Azure-Sicherheit](https://aka.ms/AzureSecInfo)

- [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/default.aspx)

- [Azure Security Center](https://azure.microsoft.com/services/security-center/)

- [Blog des Azure-Sicherheitsteams](https://www.azuresecurityorg)

- [Azure.com-Blog – Sicherheit](https://azure.microsoft.com/blog/topics/security/)
