---
title: Informationen zu den neuesten Azure-Gastbetriebssystemversionen | Microsoft-Dokumentation
description: Die neueste Releaseneuigkeiten und SDK-Kompatibilität für das Azure Cloud Services-Gastbetriebssystem.
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: ''
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 6/1/2018
ms.author: raiye
ms.openlocfilehash: 73771aaff59c3db46907ffd9f74c03f0ac0830cb
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736778"
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Azure-Gastbetriebssystemversionen und SDK-Kompatibilitätsmatrix
Bietet Ihnen aktuelle Informationen zu den neuesten Azure-Gastbetriebssystemreleases für Cloud Services. Anhand dieser Informationen können Sie Ihren Upgradepfad planen, bevor ein Gastbetriebssystem abgekündigt wird. Wenn Sie die Rollen so konfigurieren, dass die *automatischen* Gast-BS-Updates, wie unter [Updateeinstellungen für Azure-Gast-BS][Azure Guest OS Update Settings] beschrieben, verwendet werden, müssen Sie diese Seite nicht unbedingt lesen. 

> [!IMPORTANT]
> Diese Seite bezieht sich auf die Cloud Services-Web- und Workerrollen, die zusätzlich zu einem Gastbetriebssystem ausgeführt werden. Sie **gilt nicht** für IaaS Virtual Machines.
>
>


> [!TIP]
>  Abonnieren Sie den [RSS-Feed zu Gastbetriebssystemupdates], um möglichst schnell über alle Gastbetriebssystemänderungen informiert zu werden.
>
>

> [!IMPORTANT]
> Seit dem Rollout im November werden nur noch die letzten zwei Versionen des Gastbetriebssystems unterstützt, und es sind nur noch diese zwei Versionen im Azure-Portal verfügbar.
>
>

Sie wissen nicht genau, wie Sie Ihr Gastbetriebssystem aktualisieren sollen? Entsprechende Informationen finden Sie [hier][cloud updates].

## <a name="news-updates"></a>Neuigkeiten
###### <a name="june-1-2018"></a>**1. Juni 2018**
Das Gastbetriebssystem für Mai wurde veröffentlicht.

###### <a name="may-4-2018"></a>**4. Mai 2018**
Das Gastbetriebssystem für April wurde veröffentlicht.

###### <a name="april-6-2018"></a>**6. April 2018**
Das Gastbetriebssystem für März wurde veröffentlicht.

###### <a name="march-19-2018"></a>**19. März 2018**
Das Gast-BS für Februar wurde veröffentlicht.

###### <a name="january-29-2018"></a>**29. Januar 2018**
Das Januar-Gastbetriebssystem wurde für die Betriebssystemfamilien 2 (WA-GUEST-OS-2.70_201801-01) und 3 (WA-GUEST-OS-3.57_201801-01) veröffentlicht.

###### <a name="january-4-2018"></a>**4. Januar 2018**
Das Januar-Gastbetriebssystem wurde für Betriebssystemfamilien vom Typ 4 (WA-GUEST-OS-4.50_201801-01) und 5 (WA-GUEST-OS-5.15_201801-01) veröffentlicht und enthält wichtige Sicherheitspatches.  

###### <a name="january-4-2018"></a>**4. Januar 2018**
Das Gastbetriebssystem für Dezember wurde veröffentlicht.

###### <a name="december-14-2017"></a>**14. Dezember 2017**
Das Gastbetriebssystem für November wurde veröffentlicht.

###### <a name="november-8-2017"></a>**8. November 2017**
Das Gastbetriebssystem für Oktober wurde veröffentlicht.

###### <a name="october-6-2017"></a>**6. Oktober 2017**
Das Gastbetriebssystem für September wurde veröffentlicht. Für die Windows Server 2016-Version vom September ist netfx3 standardmäßig aktiviert. Kunden müssen „dism /online /disable-feature /featurename:netfx3“ zu „OnStart“ hinzufügen, wenn für ihren Workflow eine .NET 2.x-App mit einer 4.x-Runtime ausgeführt werden muss oder wenn eine .NET 2.x-App ausgeführt, ein Fehler behandelt und anschließend eine .NET 4.x-App ausgeführt wurde.



## <a name="releases"></a>Releases
## <a name="family-5-releases"></a>Releases von Familie 5
**Windows Server 2016**

Installierte .NET Framework-Versionen: 4.0, 4.5, 4.5.1, 4.5.2, 4.6, 4.6.1, 4.6.2

> [!NOTE]
> Änderungen bei Datumsangaben mit einem * bleiben vorbehalten.
>
> Das RDP-Kennwort für Betriebssystemfamilie 5 muss mindestens zehn Zeichen umfassen.
>

| Konfigurationszeichenfolge | Herausgabedatum | Deaktivierungsdatum | Abgelaufenes Datum |
| --- | --- | --- | --- |
| WA-GUEST-OS-5.19_201805-01 |1. Juni 2018 |Post 5.21 |TBD |
| WA-GUEST-OS-5.18_201804-01 |4. Mai 2018 |Post 5.20 |TBD |
|~~WA-GUEST-OS-5.17_201803-01~~ |6. April 2018 |1. Juni 2018|TBD |
|~~WA-GUEST-OS-5.16_201802-01~~ |12. März 2018 |4. Mai 2018 |TBD |
|~~WA-GUEST-OS-5.15_201801-01~~ |4. Januar 2018 |6. April 2018 |TBD |
|~~WA-GUEST-OS-5.14_201712-01~~ |4. Januar 2018 |12. März 2018 |TBD |
|~~WA-GUEST-OS-5.13_201711-01~~ |14. Dezember 2017 |4. Januar 2018|TBD |
|~~WA-GUEST-OS-5.12_201710-02~~ |8. November 2017 |4. Januar 2018 |TBD |
|~~WA-GUEST-OS-5.11_201709-01~~ |6. Oktober 2017 |14. Dezember 2017 |TBD |
|~~WA-GUEST-OS-5.10_201708-01~~ |24. August 2017 |14. Dezember 2017 |TBD |


## <a name="family-4-releases"></a>Releases von Familie 4
**Windows Server 2012 R2**

Installierte .NET Framework-Versionen: 4.0, 4.5, 4.5.1, 4.5.2

> [!NOTE]
> Änderungen bei Datumsangaben mit einem * bleiben vorbehalten
>
>

| Konfigurationszeichenfolge | Herausgabedatum | Deaktivierungsdatum | Abgelaufenes Datum |
| --- | --- | --- | --- |
| WA-GUEST-OS-4.54_201805-01 |1. Juni 2018 |Post 4.56 |TBD |
| WA-GUEST-OS-4.53_201804-01 |4. Mai 2018 |Post 4.55 |TBD |
|~~WA-GUEST-OS-4.52_201803-01~~ |6. April 2018 |1. Juni 2018 |TBD |
|~~WA-GUEST-OS-4.51_201802-01~~ |12. März 2018 |4. Mai 2018 |TBD |
|~~WA-GUEST-OS-4.50_201801-01~~ |4. Januar 2018 |6. April 2018 |TBD |
|~~WA-GUEST-OS-4.49_201712-01~~ |4. Januar 2018 |12. März 2018 |TBD |
|~~WA-GUEST-OS-4.48_201711-01~~ |14. Dezember 2017 |4. Januar 2018 |TBD |
|~~WA-GUEST-OS-4.47_201710-02~~ |8. November 2017 |4. Januar 2018 |TBD |
|~~WA-GUEST-OS-4.46_201709-01~~ |6. Oktober 2017 |14. Dezember 2017 |TBD |
|~~WA-GUEST-OS-4.45_201708-01~~ |24. August 2017 |14. Dezember 2017 |TBD |



## <a name="family-3-releases"></a>Releases von Familie 3
**Windows Server 2012**

Installierte .NET Framework-Versionen: 4.0, 4.5, 4.5.1, 4.5.2

> [!NOTE]
> Änderungen bei Datumsangaben mit einem * bleiben vorbehalten
>
>

| Konfigurationszeichenfolge | Herausgabedatum | Deaktivierungsdatum | Abgelaufenes Datum |
| --- | --- | --- | --- |
| WA-GUEST-OS-3.61_201805-01 |1. Juni 2018 |Post 3.63 |TBD |
| WA-GUEST-OS-3.60_201804-01 |4. Mai 2018 |Post 3.62 |TBD |
|~~WA-GUEST-OS-3.59_201803-01~~ |6. April 2018 |1. Juni 2018 |TBD |
|~~WA-GUEST-OS-3.58_201802-01~~ |19. März 2018 |4. Mai 2018 |TBD |
|~~WA-GUEST-OS-3.57_201801-01~~ |29. Januar 2018 |6. April 2018 |TBD |
|~~WA-GUEST-OS-3.56_201712-01~~ |4. Januar 2018 |19. März 2018 |TBD |
|~~WA-GUEST-OS-3.55_201711-01~~ |14. Dezember 2017 |29. Januar 2018 |TBD |
|~~WA-GUEST-OS-3.54_201710-02~~ |8. November 2017 |4. Januar 2018 |TBD |
|~~WA-GUEST-OS-3.53_201709-01~~ |6. Oktober 2017 |14. Dezember 2017 |TBD |
|~~WA-GUEST-OS-3.52_201708-01~~ |24. August 2017 |14. Dezember 2017 |TBD |


## <a name="family-2-releases"></a>Releases von Familie 2
**Windows Server 2008 R2 SP1**

Installierte .NET Framework-Versionen: 3.5, 4.0, 4.5, 4.5.1, 4.5.2

> [!NOTE]
> Änderungen bei Datumsangaben mit einem * bleiben vorbehalten
>
>

| Konfigurationszeichenfolge | Herausgabedatum | Deaktivierungsdatum | Abgelaufenes Datum |
| --- | --- | --- | --- |
| WA-GUEST-OS-2.74_201805-01 |1. Juni 2018 |Post 2.76 |TBD |
| WA-GUEST-OS-2.73_201804-01 |4. Mai 2018 |Post 2.75 |TBD |
|~~WA-GUEST-OS-2.72_201803-01~~ |6. April 2018 |1. Juni 2018 |TBD |
|~~WA-GUEST-OS-2.71_201802-01~~ |12. März 2018 |4. Mai 2018 |TBD |
|~~WA-GUEST-OS-2.70_201801-01~~ |29. Januar 2018 |6. April 2018 |TBD |
|~~WA-GUEST-OS-2.69_201712-01~~ |4. Januar 2018 |12. März 2018 |TBD |
|~~WA-GUEST-OS-2.68_201711-01~~ |14. Dezember 2017 |29. Januar 2018 |TBD |
|~~WA-GUEST-OS-2.67_201710-02~~ |8. November 2017 |4. Januar 2018 |TBD |
|~~WA-GUEST-OS-2.66_201709-01~~ |6. Oktober 2017 |14. Dezember 2017 |TBD |
|~~WA-GUEST-OS-2.65_201708-01~~ |24. August 2017 |14. Dezember 2017 |TBD |


## <a name="msrc-patch-updates"></a>MSRC-Patch-Updates
Die Liste der Patches, die in den einzelnen monatlichen Gastbetriebssystemreleases enthalten sind, steht [hier][patches] zur Verfügung.

## <a name="sdk-support"></a>SDK-Unterstützung
Obwohl die [Deaktivierungsrichtlinie für das Azure SDK][retire policy sdk] angibt, dass nur Versionen ab 2.2 unterstützt werden, können bei bestimmten Gastbetriebssystem-Familien frühere Versionen verwendet werden. Sie sollten immer das neueste unterstützte SDK verwenden.

| Gastbetriebssystemfamilie | Kompatible SDK-Versionen |
| --- | --- |
| 5 |Version 2.9.5.1+ |
| 4 |Version 2.1+ |
| 3 |Version 1.8+ |
| 2 |Version 1.3+ |
| 1 |Version 1.0+ |

## <a name="guest-os-release-information"></a>Informationen zu den Gastbetriebssystemreleases
Es gibt drei wichtige Daten für Gastbetriebssystemreleases: **Freigabedatum**, **Deaktivierungsdatum** und **Ablaufdatum**. Ein Gast-BS wird als verfügbar betrachtet, wenn es im Portal verfügbar ist und als Zielgastbetriebssystem ausgewählt werden kann. Wenn das **Deaktivierungsdatum** für ein Gast-BS erreicht wird, wird es aus Azure entfernt. Jedoch funktioniert jeder Clouddienst, der auf dieses Gast-BS abzielt, wie gewohnt.

Das Fenster zwischen dem **Deaktivierungsdatum** und dem **Ablaufdatum** bietet Ihnen einen Puffer, um problemlos von einem Gast-BS zu einem neueren zu wechseln. Wenn Sie die Option *automatisch* für Ihr Gastbetriebssystem festgelegt haben, verwenden Sie immer die neueste Version. In diesem Fall müssen Sie sich keine Gedanken über das Ablaufdatum machen.

Wenn das **Ablaufdatum** überschritten ist, wird jeder Clouddienst, der dieses Gastbetriebssystem weiterhin verwendet, angehalten oder gelöscht bzw. es wird ein Upgrade erzwungen. Erfahren Sie [hier][retirepolicy] mehr über die Deaktivierungsrichtlinie.

## <a name="guest-os-family-version-explanation"></a>Erläuterung zu den Versionen der Gast-BS-Familie
Die Gastbetriebssystemfamilien basieren auf veröffentlichten Versionen von Microsoft Windows Server. Das Gastbetriebssystem ist das zugrunde liegende Betriebssystem, auf dem Azure Cloud Services ausgeführt wird. Jedes Gastbetriebssystem verfügt über eine Familien-, eine Versions- und eine Releasenummer.

* **Guest OS family**  
  entspricht einem Windows Server-Betriebssystemrelease, auf dem ein Gastbetriebssystem basiert. Die *Familie 3* basiert beispielsweise auf Windows Server 2012.
* **Gastbetriebssystem-Version**  
  Gilt für das Image einer Gastbetriebssystemfamilie sowie relevante [Microsoft Security Response Center-Patches (MSRC)][msrc], die zum Zeitpunkt der Herstellung der neuen Gastbetriebssystemversion verfügbar sind. Möglicherweise sind nicht alle Patches enthalten.

    Die Zahlen beginnen bei 0 und werden jedes Mal, wenn ein neuer Satz von Updates hinzugefügt wird, um 1 erhöht. Nachfolgende Nullen werden nur angezeigt, wenn sie von Bedeutung sind. Version 2.10 ist beispielsweise eine andere, viel höhere Version als Version 2.1.
* **Gastbetriebssystemrelease**  
  Eine Neuveröffentlichung einer Gastbetriebssystemversion. Eine Neuveröffentlichung kommt vor, wenn Microsoft beim Testen Probleme feststellt, die Änderungen erfordern. Das neueste Release ersetzt immer alle vorherigen Releases, unabhängig davon, ob diese veröffentlicht wurden oder nicht. Das Azure-Portal lässt Benutzer nur das neueste Release für eine bestimmte Version auswählen. Bereitstellungen, die auf einem früheren Release ausgeführt werden, werden normalerweise nicht zwangsweise aktualisiert. Dies ist abhängig vom Schweregrad des Fehlers.

Im folgenden Beispiel steht 2 für die Familie, 12 für die Version, und "rel2" für das Release.

**Gastbetriebssystemrelease** – 2.12 rel2

**Konfigurationszeichenfolge für dieses Release** – WA-GUEST-OS-2.12_201208-02

In der Konfigurationszeichenfolge für ein Gastbetriebssystem sind dieselben Informationen eingebettet sowie ein Datum, das zeigt, welche MSRC-Patches in diesem Release berücksichtigt wurden. In diesem Beispiel wurden MSRC-Patches für Windows Server 2008 R2 bis einschließlich August 2012 integriert. Es werden nur Patches einbezogen, die ausdrücklich für diese Version von Windows Server gelten. Wenn beispielsweise ein MSRC-Patch für Microsoft Office gilt, wird er nicht berücksichtigt, da dieses Produkt kein Bestandteil des Windows Server-Basisimage ist.

## <a name="guest-os-system-update-process"></a>Updateprozess des Gastbetriebssystems
Diese Seite enthält Informationen zu anstehenden Gastbetriebssystemreleases. Kunden haben uns mitgeteilt, dass sie wissen möchten, wann ein Release stattfindet, da ihre Clouddienstrollen neu gestartet werden, wenn sie auf "Automatisches Update" festgelegt sind. Gastbetriebssystemreleases werden in der Regel mindestens fünf (5) Tage nach dem MSRC-Updaterelease veröffentlicht, das am zweiten Dienstag jedes Monats auftritt. Neue Releases enthalten alle relevanten MSRC-Patches für jede Gastbetriebssystemfamilie.

Für Microsoft Azure werden ständig Updates veröffentlicht. Das Gastbetriebssystem ist nur ein solches Update in der Pipeline. Ein Release kann durch viele Faktoren beeinflusst werden, die zu zahlreich sind, um hier aufgeführt zu werden. Darüber hinaus wird Azure auf Hunderttausenden von Computern ausgeführt. Daher ist es unmöglich, ein genaues Datum und eine Uhrzeit anzugeben, zu der Ihre Rollen neu gestartet werden. Wir arbeiten an einem Plan zur Begrenzung oder zeitlichen Planung von Neustarts.

Wenn eine neue Version des Gastbetriebssystems veröffentlicht wird, kann die vollständige Verteilung über Azure einige Zeit in Anspruch nehmen. Während Dienste auf das neue Gastbetriebssystem aktualisiert werden, werden sie den Updatedomänen entsprechend neu gestartet. Dienste, für die automatische Updates festgelegt wurden, erhalten zuerst ein Release. Nach dem Update wird die neue Gastbetriebssystemversion für Ihren Dienst im Azure-Portal aufgeführt. Erneute Releases können während dieses Zeitraums auftreten. Einige Versionen können über einen längeren Zeitraum bereitgestellt werden, und es finden möglicherweise für viele Wochen nach dem offiziellen Veröffentlichungsdatum keine automatischen Upgradeneustarts statt. Sobald ein Gastbetriebssystem verfügbar ist, können Sie diese Version explizit im Portal oder in der Konfigurationsdatei auswählen.

Viele wertvolle Informationen zu Neustarts und Verweise auf weitere technische Informationen zu Gast- und Hostbetriebssystemupdates finden Sie im MSDN-Blogbeitrag [Role Instance Restarts Due to OS Upgrades][restarts] (Neustarts von Rolleninstanzen aufgrund von Betriebssystemupgrades).

Wenn Sie Ihr Gastbetriebssystem manuell aktualisieren, finden Sie in der [Deaktivierungsrichtlinie für Gastbetriebssysteme][retirepolicy] weitere Informationen.

## <a name="guest-os-supportability-and-retirement-policy"></a>Unterstützungs- und Deaktivierungsrichtlinie für Gastbetriebssysteme
Die Unterstützungs- und Deaktivierungsrichtlinie für Gastbetriebssysteme wird [hier][retirepolicy] erläutert.

[cloud updates]: https://docs.microsoft.com/en-us/azure/cloud-services/cloud-services-update-azure-service
[RSS-Feed zu Gastbetriebssystemupdates]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Azure Guest OS Update Settings]: cloud-services-how-to-configure-portal.md
[ssl3 announcement]: http://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft Security Advisory 3009008]: https://technet.microsoft.com/library/security/3009008.aspx
[ssl3-fixit]: http://go.microsoft.com/?linkid=9863266
[MS14-066]: https://technet.microsoft.com/library/security/ms14-066.aspx
[MS14-046]: https://technet.microsoft.com/library/security/ms14-046.aspx
[retire policy sdk]: https://msdn.microsoft.com/library/dn479282.aspx
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: http://azure.microsoft.com/support/options/
[net install pkg]: http://www.microsoft.com/download/details.aspx?id=42643
[msrc]: http://www.microsoft.com/security/msrc/default.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md
[fix]: https://technet.microsoft.com/library/security/ms17-010.aspx
