---
title: Includedatei
description: Includedatei
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/03/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 336e6e163178cd6d244460dbf9bee2a5bc9d714e
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935751"
---
# <a name="frequently-asked-questions-about-azure-iaas-vm-disks-and-managed-and-unmanaged-premium-disks"></a>Häufig gestellte Fragen zu Azure-IaaS-VM-Datenträgern sowie zu verwalteten und nicht verwalteten Premium-Datenträgern

In diesem Artikel gehen wir auf einige häufig gestellte Fragen zu Azure Managed Disks und Azure Premium-SSD-Datenträgern ein.

## <a name="managed-disks"></a>Managed Disks

**Was ist Azure Managed Disks?**

Das Managed Disks-Feature nimmt Ihnen die Speicherkontoverwaltung ab und vereinfacht so die Datenträgerverwaltung für virtuelle Azure-IaaS-Computer. Weitere Informationen finden Sie in der [Übersicht über Azure Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md).

**Welche Kosten fallen für mich an, wenn ich auf der Grundlage einer vorhandenen virtuellen Festplatte mit einer Größe von 80 GB einen verwalteten Standarddatenträger erstelle?**

Ein verwalteter Standarddatenträger, der auf Grundlage einer virtuellen Festplatte mit einer Größe von 80 GB erstellt wird, wird nach der nächsten verfügbaren Größe für Standarddatenträger abgerechnet (in diesem Fall also als S10-Datenträger). Ihnen wird der Preis für einen S10-Datenträger in Rechnung gestellt. Weitere Informationen hierzu finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/storage).

**Fallen bei verwalteten Standarddatenträgern Transaktionskosten an?**

Ja. Jede Transaktion wird Ihnen in Rechnung gestellt. Weitere Informationen hierzu finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/storage).

**Wird bei einem verwalteten Standarddatenträger die tatsächliche Größe der Daten auf dem Datenträger oder die bereitgestellte Kapazität des Datenträgers abgerechnet?**

Die Abrechnung erfolgt auf der Grundlage der bereitgestellten Kapazität des Datenträgers. Weitere Informationen hierzu finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/storage).

**Inwiefern unterscheiden sich die Preise für verwaltete Premium-Datenträger von den Preisen für nicht verwaltete Datenträger?**

Die Preise für verwaltete Premium-Datenträger unterscheiden sich nicht von den Preisen für nicht verwaltete Premium-Datenträger.

**Kann ich den Speicherkontotyp (Standard oder Premium) meiner verwalteten Datenträger ändern?**

Ja. Der Speicherkontotyp Ihrer verwalteten Datenträger kann über das Azure-Portal mithilfe von PowerShell oder über die Azure-Befehlszeilenschnittstelle geändert werden.

**Kann ich mithilfe einer VHD-Datei in einem Azure-Speicherkonto einen verwalteten Datenträger mit einem anderen Abonnement erstellen?**

Ja.

**Kann ich mithilfe einer VHD-Datei in einem Azure-Speicherkonto einen verwalteten Datenträger in einer anderen Region erstellen?**

Nein.

**Gelten für Kunden, die verwaltete Datenträger verwenden, Einschränkungen bei der Skalierung?**

Managed Disks beseitigt die Grenzwerte im Zusammenhang mit Speicherkonten. Der Grenzwert liegt jedoch bei 50.000 verwalteten Datenträgern pro Region und Datenträgertyp für ein Abonnement.

**Kann ich eine inkrementelle Momentaufnahme eines verwalteten Datenträgers erstellen?**

Nein. Die aktuelle Momentaufnahmefunktion erstellt eine vollständige Kopie eines verwalteten Datenträgers.

**Können virtuelle Computer in einer Verfügbarkeitsgruppe eine Kombination aus verwalteten und nicht verwalteten Datenträgern besitzen?**

Nein. Die virtuellen Computer in einer Verfügbarkeitsgruppe müssen entweder alle über verwaltete oder alle über nicht verwaltete Datenträger verfügen. Den gewünschten Datenträgertyp können Sie bei der Erstellung einer Verfügbarkeitsgruppe auswählen.

**Ist Managed Disks die Standardoption im Azure-Portal?**

Ja. 

**Kann ich einen leeren verwalteten Datenträger erstellen?**

Ja. Sie können einen leeren Datenträger erstellen. Ein verwalteter Datenträger kann unabhängig von einem virtuellen Computer erstellt werden, also ohne ihn z.B. an einen virtuellen Computer anzufügen.

**Wie viele Fehlerdomänen werden bei Verwendung von verwalteten Datenträgern standardmäßig für Verfügbarkeitsgruppen unterstützt?**

Bei Verwendung von verwalteten Datenträgern werden für Verfügbarkeitsgruppen abhängig von der Region entweder zwei oder drei Fehlerdomänen unterstützt.

**Wie wird das Standardspeicherkonto für die Diagnose eingerichtet?**

Sie richten ein privates Speicherkonto für die VM-Diagnose ein.

**Welcher Support für rollenbasierte Zugriffssteuerung steht für verwaltete Datenträger zur Verfügung?**

Managed Disks unterstützt drei zentrale Standardrollen:

* Besitzer: Kann alles verwalten, einschließlich des Zugriffs
* Mitwirkende: Kann alles mit Ausnahme des Zugriffs verwalten
* Leser: Kann alles anzeigen, aber keine Änderungen vornehmen

**Kann ich einen verwalteten Datenträger in ein privates Speicherkonto kopieren oder exportieren?**

Sie können eine schreibgeschützte SAS-URI (Shared Access Signature) für den verwalteten Datenträger generieren und mit ihr den Inhalt in ein privates Speicherkonto oder einen lokalen Speicher kopieren. Sie können die SAS-URI über das Azure-Portal, Azure PowerShell, die Azure-Befehlszeilenschnittstelle oder [AzCopy](../articles/storage/common/storage-use-azcopy.md) verwenden.

**Kann ich eine Kopie meines verwalteten Datenträgers erstellen?**

Kunden können eine Momentaufnahme ihrer verwalteten Datenträger erstellen und anschließend auf der Grundlage der Momentaufnahme einen weiteren verwalteten Datenträger erstellen.

**Werden nicht verwaltete Datenträger weiterhin unterstützt?**

Ja, es werden verwaltete und nicht verwaltete Datenträger unterstützt. Allerdings wird empfohlen, für neue Workloads verwaltete Datenträger zu verwenden und aktuelle Workloads zu verwalteten Datenträgern zu migrieren.


**Wenn ich einen Datenträger mit einer Größe von 128 GB erstelle und die Größe anschließend auf 130 GB erhöhe, wird mir dann die nächsthöhere Datenträgergröße (256 GB) in Rechnung gestellt?**

Ja.

**Kann ich verwaltete Datenträger für lokal redundanten Speicher, georedundanten Speicher und zonenredundanten Speicher erstellen?**

Azure Managed Disks unterstützt momentan nur lokal redundanten Speicher (LRS).

**Kann ich meine verwalteten Datenträger verkleinern?**

Nein. Dies wird derzeit nicht unterstützt. 

**Kann ich auf meinen Datenträgern eine Lease unterbrechen?**

Nein. Dies wird derzeit nicht unterstützt, da eine Lease dafür da ist, versehentliches Löschen zu verhindern, wenn der Datenträger verwendet wird.

**Kann ich die Eigenschaft „Computername“ ändern, wenn ein spezialisierter Betriebssystemdatenträger (der nicht mit dem Systemvorbereitungstool erstellt wurde und nicht generalisiert ist) zur Bereitstellung einer VM verwendet wird?**

Nein. Sie können die Eigenschaft „Computername“ nicht aktualisieren. Die neue VM erbt diese von der übergeordneten VM, mit der der Betriebssystem-Datenträger erstellt wurde. 

**Wo finde ich Beispielvorlagen von Azure Resource Manager, um virtuelle Computer mit verwalteten Datenträgern zu erstellen?**
* [Liste der Vorlagen die Managed Disks verwenden](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* https://github.com/chagarw/MDPP

**Kann ich verwaltete und nicht verwaltete Datenträger zusammen auf derselben VM platzieren?**

Nein.

## <a name="standard-ssd-disks-preview"></a>Standard-SSD-Datenträger (Vorschau)

**Was sind Azure-Standard-SSD-Datenträger?**
Standard-SSD-Datenträger sind Standarddatenträger mit Unterstützung durch SSD-Medien, die als kostengünstige Speicher für Workloads optimiert sind, bei denen eine konsistente Leistung auf niedrigeren IOPS-Ebenen benötigt wird. In der Vorschau sind sie in einer begrenzten Anzahl von Regionen und mit eingeschränkter Verwaltbarkeit (über Resource Manager-Vorlagen) verfügbar.

<a id="standard-ssds-azure-regions"></a>**Welche Regionen werden derzeit für Standard-SSD-Datenträger (Vorschau) unterstützt?**
* Nordeuropa
* Frankreich, Mitte
* USA (Ost) 2
* USA (Mitte)
* Kanada, Mitte
* Asien, Osten
* Korea, Süden
* Australien (Osten)

**Wie erstelle ich Standard-SSD-Datenträger?**
Derzeit können Sie Standard-SSD-Datenträger mithilfe von Azure Resource Manager-Vorlagen erstellen. Im Folgenden werden die in der Resource Manager-Vorlage zum Erstellen von Standard-SSD-Datenträgern erforderlichen Parameter aufgeführt:

* *apiVersion* für Microsoft.Compute muss auf `2018-04-01` (oder höher) festgelegt werden.
* Geben Sie unter *managedDisk.storageAccountType* den Typ `StandardSSD_LRS` an.

Das folgende Beispiel zeigt den Abschnitt *properties.storageProfile.osDisk* für eine VM, die Standard-SSD-Datenträger verwendet:

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

Eine vollständige Beispielvorlage zum Erstellen eines Standard-SSD-Datenträgers mit einer Vorlage finden Sie unter [Create a Virtual Machine from a Windows Image with multiple empty Standard SSD Data Disks](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/) (Erstellen eines virtuellen Computers aus einem Windows-Image mit mehreren leeren Standard-SSD-Datenträgern).

**Kann ich meine vorhandenen Laufwerke in Standard-SSD konvertieren?**
Ja, das ist möglich. Allgemeine Richtlinien zum Konvertieren von verwalteten Datenträgern finden Sie unter [Konvertieren zwischen dem Standardspeicher und Storage Premium für verwaltete Azure-Datenträger](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/convert-disk-storage). Verwenden Sie außerdem den folgenden Wert, um den Datenträgertyp auf Standard-SSD zu aktualisieren.
-AccountType StandardSSD_LRS

**Kann ich Standard-SSDs als nicht verwaltete Datenträger verwenden?**
Nein, Standard-SSD-Datenträger sind nur als verwaltete Datenträger verfügbar.

**Unterstützen Standard-SSD-Datenträger SLAs für Einzelinstanz-VMs?**
Nein, Standard-SSDs weisen keine SLA für Einzelinstanz-VMs auf. Verwenden Sie Premium-SSD-Datenträger für eine SLA für Einzelinstanz-VMs.

## <a name="migrate-to-managed-disks"></a>Migrieren zu Managed Disks 

**Welche Änderungen sind in einer bereits vorhandenen Azure Backup-Dienstkonfiguration vor/nach der Migration zu Managed Disks erforderlich?**

Es sind keine Änderungen erforderlich. 

**Werden meine VM-Sicherungen über den Azure Backup-Dienst erstellt, bevor die Migration weiterhin funktioniert?**

Ja, Sicherungen funktionieren reibungslos.

**Welche Änderungen sind in einer bereits vorhandenen Azure Disk Encryption-Konfiguration vor/nach der Migration zu Managed Disks erforderlich?**

Es sind keine Änderungen erforderlich. 

**Wird die automatisierte Migration einer vorhandenen VM-Skalierungsgruppe von nicht verwalteten Datenträgern zu Managed Disks unterstützt?**

Nein. Sie können eine neue Skalierungsgruppe mit Managed Disks mithilfe des Images von Ihrer alten Skalierungsgruppe mit nicht verwalteten Datenträgern erstellen. 

**Kann ich verwaltete Datenträger über eine Seitenblob-Momentaufnahme erstellen, die vor der Migration zu Managed Disks erstellt wurde?**

Nein. Sie können eine Seitenblob-Momentaufnahme als Seitenblob exportieren und anschließend den verwalteten Datenträger aus dem exportierten Seitenblob erstellen. 

**Kann ich für meine lokalen Computer, die mit Azure Site Recovery geschützt sind, ein Failover auf eine VM mit Managed Disks ausführen?**

Ja, Sie können ein Failover auf einem virtuellen Computer mit Managed Disks auswählen.

**Hat die Migration zu Azure-VMs, die mit Azure Site Recovery geschützt sind, über die Azure-zu-Azure-Replikation irgendwelche Auswirkungen?**

Ja. Derzeit ist Azure-zu-Azure-Schutz mit Azure Site Recovery für virtuelle Computer mit verwalteten Datenträgern nur als Dienst in der Public Preview verfügbar.

**Kann ich VMs mit nicht verwalteten Datenträgern, die sich auf Speicherkonten befinden, die verschlüsselt sind oder dies waren, zu verwalteten Datenträgern migrieren?**

Ja

## <a name="managed-disks-and-storage-service-encryption"></a>Managed Disks und Storage Service Encryption 

**Ist Azure Storage Service Encryption standardmäßig beim Erstellen eines verwalteten Datenträgers aktiviert?**

Ja.

**Wer verwaltet die Verschlüsselungsschlüssel?**

Microsoft verwaltet die Verschlüsselungsschlüssel.

**Kann ich für meine verwalteten Datenträger Storage Service Encryption deaktivieren?**

Nein.

**Ist Storage Service Encryption nur in bestimmten Regionen verfügbar?**

Nein. Sie ist in allen Regionen verfügbar, in denen Managed Disks verfügbar ist. Managed Disks ist in allen öffentlichen Regionen und Deutschland verfügbar. Es ist auch in China verfügbar, jedoch nur für von Microsoft verwaltete Schlüssel, nicht für vom Kunden verwaltete Schlüssel.

**Wie finde ich heraus, ob mein verwalteter Datenträger verschlüsselt ist?**

Sie können im Azure-Portal, der Azure-CLI und in PowerShell herausfinden, wann ein verwaltetet Datenträger erstellt wurde. Wenn der Zeitpunkt nach dem 9. Juni 2017 liegt, ist Ihr Datenträger verschlüsselt.

**Wie kann ich meine vorhandenen Laufwerke verschlüsseln, die vor dem 10. Juni 2017 erstellt wurden?**

Ab dem 10. Juni 2017 werden neue Daten, die in vorhandene Datenträger geschrieben werden, automatisch verschlüsselt. Darüber hinaus ist geplant, dass vorhandene Daten verschlüsselt werden, wobei die Verschlüsselung asynchron im Hintergrund abläuft. Wenn Sie jetzt schon vorhandene Daten verschlüsseln müssen, erstellen Sie eine Kopie Ihres Datenträgers. Neue Datenträger werden verschlüsselt.

* [Kopieren von verwalteten Datenträger mithilfe der Azure-CLI](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)
* [Kopieren von verwalteten Datenträger mithilfe von PowerShell](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)

**Werden verwaltete Momentaufnahmen und Bilder verschlüsselt?**

Ja. Alle verwalteten Momentaufnahmen und Bilder, die nach dem 9. Juni 2017 erstellt werden, werden automatisch verschlüsselt. 

**Kann ich VMs mit nicht verwalteten Datenträgern, die sich auf Speicherkonten befinden, die verschlüsselt sind oder dies waren, in verwaltete Datenträger konvertieren?**

Ja

**Wird eine exportierte VHD-Datei von einem verwalteten Datenträger oder eine Momentaufnahme auch verschlüsselt?**

Nein. Wenn Sie allerdings eine VHD-Datei von einem verschlüsselten, verwalteten Datenträger oder einer Momentaufnahme in ein verschlüsseltes Speicherkonto exportieren, ist sie verschlüsselt. 

## <a name="premium-disks-managed-and-unmanaged"></a>Premium-Datenträger (verwaltet und nicht verwaltet)

**Kann ich sowohl Premium- als auch Standard-Datenträger anfügen, wenn ein virtueller Computer eine Größenserie mit Unterstützung für Premium-SSD-Datenträger (beispielsweise DSv2) verwendet?** 

Ja.

**Kann ich an eine Größenserie ohne Unterstützung für Premium-SSD-Datenträger (also beispielsweise an die D-, Dv2-, G- oder F-Serie) sowohl Premium- als auch Standard-Datenträger anfügen?**

Nein. An virtuelle Computer, die keine Größenserie mit Unterstützung für Premium-SSD-Datenträger verwenden, können nur Standard-Datenträger angefügt werden.

**Welche Kosten fallen an, wenn ich einen Storage Premium-Datenträger von einer vorhandenen virtuellen Festplatte mit einer Größe von 80 GB erstelle?**

Ein Premiumdatenträger, der auf der Grundlage einer virtuellen Festplatte mit einer Größe von 80 GB erstellt wird, wird nach der nächsten verfügbaren Größe für Premiumdatenträger abgerechnet (in diesem Fall also als P10-Datenträger). Ihnen wird der Preis für einen P10-Datenträger in Rechnung gestellt.

**Fallen bei Verwendung von Premium-SSD-Datenträgern Transaktionskosten an?**

Es fallen feste Kosten für die Datenträgergröße an, und es gelten bestimmte Grenzwerte für IOPS und Durchsatz. Weitere Kosten fallen gegebenenfalls für die ausgehende Bandbreite und die Momentaufnahmekapazität an. Weitere Informationen hierzu finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/storage).

**Welche IOPS- und Durchsatzgrenzwerte gelten für den Datenträgercache?**

Die kombinierte Beschränkung für den Cache und die lokale SSD für virtuelle Computer der DS-Serie liegt bei 4000 IOPS pro Kern und 33 MB pro Sekunde und Kern. Die GS-Serie bietet 5000 IOPS pro Kern und 50 MB pro Sekunde und Kern.

**Wird für virtuelle Computer mit Managed Disks die lokale SSD unterstützt?**

Bei der lokalen SSD handelt es sich um einen temporären Speicher, der in einem virtuellen Computer mit Managed Disks enthalten ist. Für diesen temporären Speicher fallen keine zusätzlichen Kosten an. Es empfiehlt sich, auf dieser lokalen SSD keine Anwendungsdaten zu speichern, da die Daten nicht dauerhaft in Azure Blob Storage gespeichert werden.

**Gibt es Konsequenzen für die Verwendung von TRIM auf Premium-Datenträgern?**

Es gibt keinen Nachteil bei der Verwendung von TRIM auf Azure-Datenträger auf Premium- oder Standard-Datenträgern.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Neue Datenträgergrößen: verwaltet und nicht verwaltet

**Was ist die größte Datenträgergröße, die für Betriebssystem- und Datenträger unterstützt wird?**

Der Partitionstyp, den Azure für einen Betriebssystemdatenträger unterstützt, ist der Master Boot Record (MBR). Das MBR-Format unterstützt eine Datenträgergröße bis zu 2 TB. Die maximale Größe, die Azure für einen Betriebssystemdatenträger unterstützt beträgt 2 TB. Azure unterstützt für Datenträger bis zu 4 TB. 

**Was ist die größte Seitenblobgröße, die unterstützt wird?**

Die größte Seitenblobgröße, die von Azure unterstützt wird, ist 8 TB (8.191 GB). Die maximale Seitenblobgröße beim Anfügen an einen virtuellen Computer als Daten- oder Betriebssystem-Datenträger beträgt 4 TB (4.095 GB).

**Benötige ich eine neue Version der Azure-Tools, um Datenträger, die größer als 1TB sind, erstellen, anfügen, anpassen und hochladen zu können?**

Sie müssen Ihrer vorhandenen Azure-Tools nicht aktualisieren, um Datenträger, die größer als 1 TB sind, erstellen, anfügen oder anpassen zu können. Um eine VHD-Datei von einem lokalen Speicherort als Seitenblob oder als nicht verwalteten Datenträger direkt in Azure hochladen zu können, müssen Sie die neuesten Tools verwenden:

|Azure-Tools      | Unterstützte Versionen                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Versionsnummer 4.1.0: Version Juni 2017 oder höher|
|Azure-CLI V1     | Versionsnummer 0.10.13: Version Mai 2017 oder höher|
|AzCopy           | Versionsnummer 6.1.0: Version Juni 2017 oder höher|

Die Unterstützung für Azure CLI V2 und den Azure Storage-Explorer ist bald verfügbar. 

**Werden P4- und P6-Datenträgergrößen für nicht verwaltete Datenträger oder Seitenblobs unterstützt?**

Nein. P4-Datenträger (32 GB) und P6-Datenträgergrößen (64 GB) werden nur für verwaltete Datenträger unterstützt. Die Unterstützung für nicht verwaltete Datenträger und Seitenblobs ist bald verfügbar.

**Wenn mein vorhandener verwalteter Premium-Datenträger mit weniger als 64 GB erstellt wurde, bevor der kleine Datenträger aktiviert wurde (circa 15. Juni 2017), wie wird dies berechnet?**

Vorhandene kleinen Premium-Datenträger mit weniger als 64 GB werden weiterhin gemäß dem P10-Tarif in Rechnung gestellt. 

**Wie kann ich den Datenträgertarif von kleinen Premium-Datenträgern mit weniger als 64 GB von P10 in P4 oder P6 ändern?**

Machen Sie eine Momentaufnahme Ihres kleinen Datenträgers, und erstellen Sie dann einen Datenträger, damit der Tarif automatisch in P4 oder P6 basierend auf der bereitgestellten Größe geändert wird. 


## <a name="what-if-my-question-isnt-answered-here"></a>Was kann ich tun, wenn meine Frage hier nicht beantwortet wird?

Wenn Ihre Frage hier nicht aufgeführt wird, informieren Sie uns, und wir helfen Ihnen dabei, eine Antwort zu finden. Sie können in den Kommentaren am Ende dieses Artikels Fragen stellen. Im [Azure Storage-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata) von MSDN können Sie sich mit dem Azure Storage-Team und anderen Mitgliedern der Community über diesen Artikel austauschen.

Funktionsvorschläge und -ideen können über das [Azure Storage-Feedbackforum](https://feedback.azure.com/forums/217298-storage) eingereicht werden.
