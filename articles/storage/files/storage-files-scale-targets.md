---
title: Skalierbarkeits- und Leistungsziele für Azure Files | Microsoft-Dokumentation
description: Informationen zu den Skalierbarkeits- und Leistungszielen für Azure Files, einschließlich Kapazität, Anforderungsrate sowie Grenzwerte für ein- und ausgehende Bandbreite.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 7/19/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: af738b655b4070da1cfe7555daff82c0e40ff91c
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53138584"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Skalierbarkeits- und Leistungsziele für Azure Files
[Azure Files](storage-files-introduction.md) bietet vollständig verwaltete Dateifreigaben in der Cloud, auf die über das Branchenstandardprotokoll SMB zugegriffen werden kann. Dieser Artikel behandelt die Skalierbarkeits- und Leistungsziele für Azure Files und Azure-Dateisynchronisierung.

Die hier aufgelisteten Ziele für Skalierbarkeit und Leistung sind hochgesteckt, können jedoch von anderen Variablen in Ihrer Bereitstellung beeinflusst werden. Beispielsweise kann der Durchsatz für eine Datei auch durch die verfügbare Netzwerkbandbreite und nicht nur durch die Server begrenzt sein, die den Azure Files-Dienst hosten. Wir empfehlen Ihnen ausdrücklich, Ihr Nutzungsverhalten zu testen, um festzustellen, ob Skalierbarkeit und Leistung von Azure Files Ihren Anforderungen entspricht. Wir verpflichten uns auch, diese Grenzwerte mit der Zeit zu erhöhen. Zögern Sie nicht, uns entweder in den Kommentaren unten oder über [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) Feedback dazu zu geben, welche Grenzwerte wir für Sie erhöhen sollen.

## <a name="azure-storage-account-scale-targets"></a>Skalierbarkeitsziele für das Azure-Speicherkonto
Die übergeordnete Ressource einer Azure-Dateifreigabe ist ein Azure-Speicherkonto. Ein Speicherkonto stellt einen Speicherpool in Azure dar, der von mehreren Speicherdiensten, einschließlich Azure Files, zum Speichern von Daten verwendet werden kann. Andere Dienste, die Daten in Speicherkonten speichern, sind Azure Blob Storage, Azure Queue Storage und Azure Table Storage. Die folgenden Ziele gelten für alle Speicherdienste, die Daten in einem Speicherkonto speichern:

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> Die Belegung von Speicherkonten durch andere Speicherdienste wirkt sich auf die Azure-Dateifreigaben in Ihrem Speicherkonto aus. Wenn Sie z.B. mit Azure Blob Storage die maximale Speicherkapazität erreichen, können Sie in Ihrer Azure-Dateifreigabe keine neuen Dateien erstellen, auch wenn Ihre Azure-Dateifreigabe kleiner als die maximale Freigabegröße ist.

## <a name="azure-files-scale-targets"></a>Skalierbarkeitsziele für Azure Files
[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Skalierbarkeitsziele für die Azure-Dateisynchronisierung
Bei der Azure-Dateisynchronisierung haben wir als Entwicklungsziel eine unbegrenzte Nutzung vorgesehen, was jedoch nicht immer möglich ist. Die folgende Tabelle zeigt die bei unseren Tests ermittelten Grenzwerte und für welche Ziele tatsächlich feste Grenzen gelten:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Leistungsmetriken der Azure-Dateisynchronisierung
Da der Azure-Dateisynchronisierungs-Agent auf einem Windows Server-Computer ausgeführt wird, der eine Verbindung mit den Azure-Dateifreigaben herstellt, hängt die effektive Synchronisierungsleistung von einer Reihe von Faktoren in Ihrer Infrastruktur ab: von Windows Server und der zugrunde liegenden Datenträgerkonfiguration, der Netzwerkbandbreite zwischen dem Server und dem Azure-Speicher, der Dateigröße, der Größe des gesamten Datasets und der Aktivität auf dem Dataset. Da die Azure-Dateisynchronisierung auf Dateiebene ausgeführt wird, werden die Leistungsmerkmale einer auf der Azure-Dateisynchronisierung basierenden Lösung besser in der Anzahl von Objekten (Dateien und Verzeichnisse) gemessen, die pro Sekunde verarbeitet werden. 
 
Bei der Azure-Dateisynchronisierung ist die Leistung in zwei Phasen entscheidend:
1. **Erste einmalige Bereitstellung**: Informationen zum Optimieren der Leistung bei der ersten Bereitstellung finden Sie unter [Onboarding bei der Azure-Dateisynchronisierung](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync).
2. **Laufende Synchronisierung**: Nachdem zunächst ein Seeding für die Daten in den Azure-Dateifreigaben ausgeführt wird, synchronisiert die Azure-Dateisynchronisierung fortlaufend mehrere Endpunkte.

Wenn Sie die Bereitstellung für jede der Phasen planen, sehen Sie sich im Folgenden die Ergebnisse an, die bei den internen Tests auf einem System mit einer Konfiguration zustande kamen.

| Systemkonfiguration |  |
|-|-|
| CPU | 64 virtuelle Kerne mit 64-MiB-L3-Cache |
| Arbeitsspeicher | 128 GB |
| Datenträger | SAS-Datenträger mit RAID 10 mit batteriegepuffertem Cache |
| Netzwerk | 1-GBit/s-Netzwerk |
| Workload | Allgemeiner Dateiserver|

| Erste einmalige Bereitstellung  |  |
|-|-|
| Anzahl der Objekte | 10 Millionen Objekte | 
| Datasetgröße| Ca. 4 TiB |
| Durchschnittliche Dateigröße | Ca. 500 KiB (größte Datei: 100 GiB) |
| Uploaddurchsatz | 15 Objekte pro Sekunde |
| Durchsatz beim Download von Namespaces* | 350 Objekte pro Sekunde |
 
*Wenn ein neuer Serverendpunkt erstellt wird, lädt der Azure-Dateisynchronisierungs-Agent keine Dateiinhalte herunter. Zuerst synchronisiert er den vollständigen Namespace und löst dann im Hintergrund einen Rückruf aus, um die Dateien herunterzuladen, entweder in ihrer Gesamtheit oder bei aktiviertem Cloudtiering in der Cloudtieringrichtliniengruppe für den Serverendpunkt.

| Laufende Synchronisierung  |   |
|-|--|
| Anzahl der synchronisierten Objekte| 125.000 Objekte (Änderungsumfang ca. 1 %) | 
| Datasetgröße| 50 GiB |
| Durchschnittliche Dateigröße | Ca. 500 KiB |
| Uploaddurchsatz | 20 Objekte pro Sekunde |
| Durchsatz bei vollständigen Downloads* | 30 Objekte pro Sekunde |
 
*Wenn Cloudtiering aktiviert ist, werden Sie wahrscheinlich eine bessere Leistung beobachten, da nur ein Teil der Dateidaten heruntergeladen wird. Die Azure-Dateisynchronisierung lädt die Daten zwischengespeicherter Dateien nur dann herunter, wenn sie auf einem der Endpunkte geändert werden. Bei mehrstufigen oder neu erstellten Dateien lädt der Agent nicht die Dateidaten herunter, sondern synchronisiert lediglich den Namespace auf allen Serverendpunkten. Der Agent unterstützt auch teilweise Downloads von mehrstufigen Dateien, wenn Benutzer auf diese zugreifen. 
 
> [!Note]  
> Die oben genannten Zahlen sind kein Hinweis auf die Leistung, die bei Ihnen zu erwarten ist. Die tatsächliche Leistung hängt von mehreren Faktoren ab, die am Anfang dieses Abschnitts beschrieben werden.

Als allgemeine Richtlinie für Ihre Bereitstellung sollten Sie einige Dinge berücksichtigen:
- Der Objektdurchsatz wird ungefähr relativ zur Anzahl der Synchronisierungsgruppen auf dem Server skaliert. Das Aufteilen von Daten in mehrere Synchronisierungsgruppen auf einem Server führt zu einem besseren Durchsatz, der zudem durch den Server und Netzwerk begrenzt wird.
- Der Objektdurchsatz ist umgekehrt proportional zum Durchsatz in MiB pro Sekunde. Bei kleineren Dateien tritt ein höherer Durchsatz im Hinblick auf die Anzahl der verarbeiteten Objekte pro Sekunde auf, jedoch einen niedrigeren Durchsatz in MiB pro Sekunde aufweist. Im Gegensatz dazu werden bei größeren Dateien weniger Objekte pro Sekunde verarbeitet, dafür jedoch ein höherer Durchsatz in MiB pro Sekunde erzielt. Der Durchsatz in MiB pro Sekunde wird durch die Azure Files-Skalierungsziele beschränkt. 

## <a name="see-also"></a>Weitere Informationen
- [Planung für eine Azure Files-Bereitstellung](storage-files-planning.md)
- [Planung für die Bereitstellung einer Azure-Dateisynchronisierung](storage-sync-files-planning.md)
- [Skalierbarkeits- und Leistungsziele für andere Speicherdienste](../common/storage-scalability-targets.md)
