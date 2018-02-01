---
title: "Einführung in Azure Storage | Microsoft-Dokumentation"
description: "Einführung in Azure Storage, den Datenspeicher von Microsoft in der Cloud."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: a4a1bc58-ea14-4bf5-b040-f85114edc1f1
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/21/2018
ms.author: tamram
ms.openlocfilehash: 9af4bfd5b5ae46a856b25a94cdbe55e098ea940e
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2018
---
# <a name="introduction-to-microsoft-azure-storage"></a>Einführung in Microsoft Azure Storage

Microsoft Azure Storage ist ein von Microsoft verwalteter Clouddienst, über den hoch verfügbarer, sicherer, stabiler, skalierbarer und redundanter Speicher bereitgestellt wird. Microsoft übernimmt die Wartung und behandelt kritische Probleme für Sie.

Azure Storage besteht aus drei Datendiensten: Blob Storage, File Storage und Queue Storage. Blob Storage unterstützt sowohl Standardspeicher als auch Storage Premium. Dabei werden von Storage Premium nur SSDs verwendet, um eine optimale Leistung zu erzielen. Ein weiteres Feature ist der kalte Speicher, in dem große Datenmengen, auf die selten zugegriffen wird, kostengünstiger gespeichert werden.

In diesem Artikel ist Folgendes näher erläutert:
* Azure Storage-Dienste
* Speicherkontentypen
* Zugreifen auf Blobs, Warteschlangen und Dateien
* Verschlüsselung
* Replikation
* Übertragen von Daten in oder aus Speicher
* Zahlreiche verfügbare Speicherclientbibliotheken

Informationen zur Herstellung der Betriebsbereitschaft für Azure Storage finden Sie unter [Erstellen eines Speicherkontos](storage-quickstart-create-account.md).

## <a name="introducing-the-azure-storage-services"></a>Einführung in die Azure Storage-Dienste

Damit Sie einen der von Azure Storage bereitgestellten Dienste (Blob Storage, File Storage und Queue Storage) verwenden können, erstellen Sie zunächst ein Speicherkonto. Anschließend können Sie Dateien in einen bzw. aus einem bestimmten Dienst in dieses Speicherkonto übertragen.

## <a name="blob-storage"></a>Blob Storage

Blobs ähneln im Grunde den Dateien, die Sie auf Ihrem Computer (oder Tablet, Mobilgerät usw.) speichern. Dabei kann es sich u.a. um Bilder, Microsoft Excel-Dateien, HTML-Dateien, virtuelle Festplatten (Virtual Hard Disks, VHDs) und Big Data wie Protokolle und Datenbanksicherungen handeln. Blobs werden in Containern gespeichert, die Ordnern ähneln.

Nach dem Speichern von Dateien in Blob Storage können Sie weltweit über URLs, die REST-Schnittstelle oder eine der Azure SDK-Speicherclientbibliotheken darauf zugreifen. Speicherclientbibliotheken stehen für mehrere Sprachen, u.a. Node.js, Java, PHP, Ruby, Python und .NET zur Verfügung.

Es gibt drei Arten von Blobs: Blockblobs, Seitenblobs (für VHD-Dateien) und Anfügeblobs.

* Blockblobs werden für gewöhnliche Dateien mit einer Größe von bis zu 4.7 TB verwendet.
* Seitenblobs werden für Random-Access-Dateien mit einer Größe von bis zu 8 TB verwendet. Diese werden für die VHD-Dateien genutzt, auf denen virtuelle Computer basieren.
* Anfügeblobs bestehen wie Blockblobs auch aus Blöcken, sind jedoch für Anfügevorgänge optimiert. Sie werden für Aufgaben wie die Protokollierung von Informationen von mehreren virtuellen Computern im gleichen Blob verwendet.

Wenn sich die Daten besonders umfangreicher Datasets aufgrund von Netzwerkbeschränkungen nicht sinnvoll über eine Kabelverbindung in Blob Storage hochladen bzw. daraus herunterladen lassen, können Sie Festplatten an Microsoft schicken und Ihre Daten direkt in das Rechenzentrum importieren bzw. aus dem Rechenzentrum exportieren lassen. Weitere Informationen finden Sie unter [Verwenden des Microsoft Azure Import/Export-Diensts zum Übertragen von Daten in den Blobspeicher](../storage-import-export-service.md).

## <a name="azure-files"></a>Azure Files
[Azure Files](../files/storage-files-introduction.md) ermöglicht die Einrichtung hochverfügbarer Netzwerkdateifreigaben, auf die über das standardmäßige SMB-Protokoll (Server Message Block) zugegriffen werden kann. Dadurch können mehrere virtuelle Computer gemeinsam die gleichen Dateien mit Lese- und Schreibzugriff nutzen. Die Dateien können auch mithilfe der REST-Schnittstelle oder mithilfe der Speicherclientbibliotheken gelesen werden.

Azure Files unterscheidet sich in einem Punkt von Dateien auf einer Dateifreigabe eines Unternehmens: Über eine URL, die auf die gewünschte Datei verweist und ein SAS-Token (Shared Access Signature) enthält, kann von jedem Ort der Welt auf die Datei zugegriffen werden. Sie können SAS-Token generieren, um für einen bestimmten Zeitraum spezifischen Zugriff auf eine private Ressource zu ermöglichen.

Dateifreigaben können in zahlreichen Szenarien verwendet werden:

* Viele lokale Anwendungen verwenden Dateifreigaben. Dieses Feature erleichtert die Migration dieser Anwendungen mit gemeinsamen Daten zu Azure. Wenn Sie die Dateifreigabe unter dem gleichen Laufwerksbuchstaben einbinden, den auch die lokale Anwendung verwendet, müsste der Teil Ihrer Anwendung, der auf die Dateifreigabe zugreift, mit minimalen (oder sogar ganz ohne) Änderungen funktionieren.

* Konfigurationsdateien können auf einer Dateifreigabe gespeichert und von mehreren virtuellen Computern genutzt werden. Tools und Hilfsprogramme, die von mehreren Entwicklern in einer Gruppe verwendet werden, können auf einer Dateifreigabe gespeichert werden, um sicherzustellen, dass sie von allen gefunden werden und dass alle die gleiche Version verwenden.

* Diagnoseprotokolle, Metriken und Absturzabbilder sind nur drei Beispiele für Daten, die zur späteren Verarbeitung oder Analyse auf eine Dateifreigabe geschrieben werden können.

Active Directory-basierte Authentifizierung und Zugriffssteuerungslisten (Access Control Lists, ACLs) werden derzeit noch nicht unterstützt, wir arbeiten aber daran. Zur Authentifizierung für den Zugriff auf die Dateifreigabe werden die Speicherkonto-Anmeldeinformationen verwendet. Das bedeutet, dass jeder Benutzer, bei dem die Freigabe eingebunden ist, uneingeschränkten Lese-/Schreibzugriff auf die Freigabe hat.

## <a name="queue-storage"></a>Queue Storage

Der Azure Queue-Dienst wird zum Speichern und Abrufen von Nachrichten verwendet. Warteschlangennachrichten können eine Größe von bis zu 64 KB haben, und eine Warteschlange kann Millionen von Nachrichten enthalten. Warteschlangen dienen im Allgemeinen zum Speichern von Listen mit Nachrichten, die asynchron verarbeitet werden sollen.

Beispiel: Ihre Kunden sollen Bilder hochladen können, und Sie möchten Miniaturbilder für jedes Bild erstellen. Sie können Kunden warten lassen, bis die Miniaturbilder beim Hochladen der Bilder erstellt werden. Alternativ können Sie eine Warteschlange verwenden. Wenn der Kunde den Upload abgeschlossen hat, schreiben Sie eine Nachricht an die Warteschlange. Legen Sie dann eine Azure-Funktion fest, die die Nachricht aus der Warteschlange abruft und die Miniaturbilder erstellt. Alle Schritte dieses Verarbeitungsvorgangs können separat skaliert werden, sodass Sie ihn für Ihre Zwecke optimieren können.

## <a name="table-storage"></a>Table Storage

Azure Table Storage ist jetzt Teil von Azure Cosmos DB. Diese Azure Table Storage-Dokumentation finden Sie unter [Übersicht über Azure Table Storage](../../cosmos-db/table-storage-overview.md). Zusätzlich zum vorhandenen Azure Table Storage-Dienst ist das neue Angebot „Azure Cosmos DB-Tabellen-API“ mit durchsatzoptimierten Tabellen, globaler Verteilung und automatischen sekundären Indizes verfügbar. Unter [Einführung in die Tabellen-API von Azure Cosmos DB](https://aka.ms/premiumtables) erfahren Sie mehr und können das neue Premiumangebot testen.

## <a name="disk-storage"></a>Datenträgerspeicher

Azure Storage umfasst auch die Funktionen für verwaltete und nicht verwaltete Datenträger, die von virtuellen Computern verwendet werden. Weitere Informationen zu diesen Features finden Sie in der [Microsoft Azure-Dokumentation](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).

## <a name="types-of-storage-accounts"></a>Speicherkontentypen

Die folgende Tabelle enthält die verschiedenen Arten von Speicherkonten sowie die Objekte, die jeweils mit den Konten verwendet werden können.

|**Speicherkontotyp**|**Standard (allgemein)**|**Premium (allgemein)**|**Blobspeicher, Zugriffsebenen „Heiß“ und „Kalt“**|
|-----|-----|-----|-----|
|**Unterstützte Dienste**| Blob-, File- und Queue-Dienst | Blob-Dienst | Blob-Dienst|
|**Unterstützte Blobtypen**|Blockblobs, Seitenblobs und Anfügeblobs | Seitenblobs | Blockblobs und Anfügeblobs|

### <a name="general-purpose-storage-accounts"></a>Allgemeine Speicherkonten

Es gibt zwei Arten von allgemeine Speicherkonten.

#### <a name="standard-storage"></a>Standardspeicher

Die am häufigsten genutzten Speicherkonten sind Standardspeicherkonten, die für alle Datentypen verwendet werden können. Standardspeicherkonten verwenden Datenträger zum Speichern von Daten.

#### <a name="premium-storage"></a>Storage Premium

Storage Premium bietet Hochleistungsspeicher für Seitenblobs, die in erster Linie für VHD-Dateien verwendet werden. Storage Premium-Konten nutzen SSD für die Datenspeicherung. Microsoft empfiehlt die Verwendung von Storage Premium für alle virtuellen Computer.

### <a name="blob-storage-accounts"></a>Blob Storage-Konten

Beim Blob Storage-Konto handelt es sich um ein spezielles Speicherkonto für die Speicherung von Blockblobs und Anfügeblobs. Seitenblobs können in diesen Konten nicht gespeichert werden, daher ist das Speichern von VHD-Dateien nicht möglich. Mit diesen Konten können Sie die Zugriffsebene „Heiß“ oder „Kalt“ festlegen. Die Ebene kann jederzeit geändert werden.

Die Zugriffsebene „Heiß“ wird für Dateien verwendet, auf die häufig zugegriffen wird: Die Speicherkosten sind höher, die Kosten für den Zugriff auf die Blobs jedoch viel niedriger. Für Blobs, die in der Zugriffsebene „Kalt“ gespeichert sind, fallen höhere Kosten für den Zugriff auf die Blobs an. Die Kosten für die Speicherung sind jedoch viel niedriger.

## <a name="accessing-your-blobs-files-and-queues"></a>Zugreifen auf Blobs, Dateien und Warteschlangen

Jedes Speicherkonto verfügt über zwei Authentifizierungsschlüssel, die beide für alle Vorgänge verwendet werden können. Da es zwei Schlüssel gibt, können Sie gelegentlich ein Rollover für die Schlüssel durchführen, um die Sicherheit zu erhöhen. Es ist sehr wichtig, diese Schlüssel sicher aufzubewahren, da sie zusammen mit dem Kontonamen den unbegrenzten Zugriff auf alle Daten im Speicherkonto ermöglichen.

In diesem Abschnitt werden zwei Optionen zum Schützen der Speicherkonten und der dazugehörigen Daten erläutert. Ausführliche Informationen zum Schützen der Speicherkonten und Daten finden Sie im [Azure Storage-Sicherheitsleitfaden](storage-security-guide.md).

### <a name="securing-access-to-storage-accounts-using-azure-ad"></a>Schützen des Zugriffs auf Speicherkonten mithilfe von Azure AD

Eine Möglichkeit zum Schützen der Speicherdaten besteht darin, den Zugriff auf die Speicherkontoschlüssel zu steuern. Mit der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) von Resource Manager können Sie Benutzern, Gruppen oder Anwendungen Rollen zuweisen. Diese Rollen sind an einen bestimmten Satz von Aktionen gebunden, die zulässig bzw. unzulässig sind. Wenn Sie den Zugriff auf ein Speicherkonto mithilfe von RBAC gewähren, betrifft dies nur die Verwaltungsvorgänge für dieses Speicherkonto (beispielsweise die Änderung der Zugriffsebene). Mit RBAC können Sie keinen Zugriff auf Datenobjekte wie einen bestimmten Container oder eine Dateifreigabe gewähren. Sie können jedoch RBAC verwenden, um Zugriff auf die Speicherkontoschlüssel zu erteilen, mit deren Hilfe anschließend die Datenobjekte gelesen werden können.

### <a name="securing-access-using-shared-access-signatures"></a>Schützen des Zugriffs mit Shared Access Signatures

Sie können Datenobjekte mithilfe von Shared Access Signatures und gespeicherten Zugriffsrichtlinien schützen. Eine Shared Access Signature (SAS) ist eine Zeichenfolge mit einem Sicherheitstoken, die an den URI für ein Objekt angefügt werden kann. Mit diesem URI können Sie den Zugriff auf bestimmte Speicherobjekte delegieren und Einschränkungen festlegen, beispielsweise Berechtigungen und den Datums- und Uhrzeitbereich für den Zugriff. Dieses Feature bietet umfangreiche Funktionen. Ausführliche Informationen finden Sie unter [Using Shared Access Signatures (SAS)](storage-dotnet-shared-access-signature-part-1.md) (Verwenden von Shared Access Signatures (SAS)).

### <a name="public-access-to-blobs"></a>Öffentlicher Zugriff auf Blobs

Mit dem Blob-Dienst können Sie öffentlichen Zugriff auf einen Container und seine Blobs oder auf ein bestimmtes Blob erteilen. Wenn Sie einen Container oder ein Blob als öffentliche Ressource konfigurieren, kann jeder Benutzer anonym und ohne Authentifizierung drauf zugreifen. Diese Vorgehensweise käme beispielsweise bei einer Website mit Bildern, Videos oder Dokumenten aus Blob Storage zum Einsatz. Weitere Informationen finden Sie unter [Manage anonymous read access to containers and blobs](../blobs/storage-manage-access-to-resources.md) (Verwalten des anonymen Lesezugriffs auf Container und Blobs).

## <a name="encryption"></a>Verschlüsselung

Für die Storage-Dienste stehen verschiedene grundlegende Verschlüsselungsarten zur Verfügung.

### <a name="encryption-at-rest"></a>Verschlüsselung ruhender Daten

Bei einem Azure Storage-Konto können Sie die Speicherdienstverschlüsselung (Storage Service Encryption, SSE) entweder für den Files-Dienst (Vorschauversion) oder den Blob-Dienst aktivieren. Wenn SSE aktiviert ist, werden alle Daten, die in den entsprechenden Dienst geschrieben werden, vor dem Schreibvorgang verschlüsselt. Beim Lesen der Daten werden sie vor der Rückgabe entschlüsselt.

### <a name="client-side-encryption"></a>Clientseitige Verschlüsselung

Die Speicherclientbibliotheken enthalten Methoden, die Sie zum programmgesteuerten Verschlüsseln von Daten aufrufen können, bevor Sie sie vom Client an Azure senden. Sie werden verschlüsselt gespeichert, d.h. sie sind auch im Ruhezustand verschlüsselt. Beim Einlesen der Daten entschlüsseln Sie die Informationen nach ihrem Erhalt.

### <a name="encryption-in-transit-with-azure-file-shares"></a>Verschlüsselung während der Übertragung mit Azure-Dateifreigaben

Weitere Informationen zu Shared Access Signatures finden Sie unter [Shared Access Signatures, Teil 1: Grundlagen zum SAS-Modell](../storage-dotnet-shared-access-signature-part-1.md) . Weitere Informationen zum sicheren Zugriff auf Ihr Speicherkonto finden Sie unter [Verwalten des anonymen Lesezugriffs auf Container und Blobs](../blobs/storage-manage-access-to-resources.md) sowie unter [Authentication for the Azure Storage Services](https://msdn.microsoft.com/library/azure/dd179428.aspx) (Authentifizierung für die Azure Storage-Dienste).

Ausführlichere Informationen zum Schützen der Speicherkonten und zur Verschlüsselung finden Sie im [Azure Storage-Sicherheitsleitfaden](storage-security-guide.md).

## <a name="replication"></a>Replikation

Um die Stabilität Ihrer Daten sicherzustellen, werden unter Azure Storage mehrere Kopien Ihrer Daten gespeichert (und verwaltet). Dieses Konzept wird Replikation oder manchmal auch Redundanz genannt. Beim Einrichten Ihres Speicherkontos wählen Sie einen Replikationstyp aus. In den meisten Fällen kann diese Einstellung nach der Einrichtung des Speicherkontos angepasst werden.

**Lokal redundanter Speicher (LRS)**

Lokal redundanter Speicher (LRS) ist darauf ausgelegt, für Objekte eine Dauerhaftigkeit von mindestens 99,999999999% (elf Neunen) in einem bestimmten Jahr bereitzustellen. Das bedeutet, dass mehrere Kopien Ihrer Daten von Azure Storage in dem Rechenzentrum verwaltet werden, das bei der Einrichtung des Speicherkontos angegeben wurde. Wenn Änderungen an einer Kopie committet werden, werden alle anderen Kopien aktualisiert, bevor eine Erfolgsmeldung zurückgegeben wird. Das bedeutet, dass die Replikate stets synchron sind. Darüber hinaus befinden sich die Kopien in separaten Fehler- und Upgradedomänen. Ihre Daten sind daher verfügbar, auch wenn bei einem Speicherknoten, in dem Ihre Daten gespeichert sind, ein Fehler auftritt oder der Knoten für die Aktualisierung offline geschaltet wird.

**Zonenredundanter Speicher (ZRS) (Vorschauversion)**

Der zonenredundante Speicher (ZRS) soll die Entwicklung hochverfügbarer Anwendungen vereinfachen. ZRS bietet eine Dauerhaftigkeit für Speicherobjekte von mindestens 99,9999999999% (zwölf Neunen) über einen Zeitraum von einem Jahr. Bei ZRS werden Ihre Daten synchron über mehrere Verfügbarkeitszonen hinweg repliziert. Ziehen Sie ZRS für Szenarien wie Transaktionsanwendungen in Betracht, in denen Ausfallzeiten nicht zulässig sind. Mithilfe von ZRS können Kunden Daten lesen und schreiben, auch wenn eine Zone nicht verfügbar oder nicht wiederherstellbar sein sollte. Einfügungen und Updates für Daten werden synchron vorgenommen und sind äußerst konsistent.    

Die vorherige ZRS-Funktion wird nun als „ZRS Classic“ bezeichnet. ZRS Classic-Konten sind nur für Blockblobs in allgemeinen V1-Speicherkonten verfügbar. ZRS Classic repliziert Daten asynchron zwischen Rechenzentren in einer Region oder in zwei Regionen. Ein Replikat ist ggf. erst wieder verfügbar, wenn von Microsoft das Failover in der sekundären Region initiiert wird. Ein ZRS Classic-Konto kann nicht in bzw. aus LRS oder GRS konvertiert werden und verfügt nicht über Metriken oder Protokollierungsfunktionen.

**Georedundanter Speicher (GRS)**

Georedundanter Speicher (Geo-Redundant Storage, GRS) ist darauf ausgelegt, für Objekte eine Dauerhaftigkeit von 99,99999999999999 Prozent (16 Neunen) in einem bestimmten Jahr bereitzustellen, indem die lokalen Kopien ihrer Daten in einer primären Region sowie ein weiterer Satz mit Kopien Ihrer Daten in einer sekundären Region gespeichert werden, die Hunderte Kilometer von der primären Region entfernt liegt. Im Falle eines Ausfalls in der primären Region wird für Azure Storage ein Failover auf die sekundäre Region ausgeführt.

**Georedundanter Speicher mit Lesezugriff (RA-GRS)**

Georedundanter Speicher mit Lesezugriff verhält sich genau wie GRS, mit der Ausnahme, dass Sie Lesezugriff auf die Daten am sekundären Standort erhalten. Wenn das primäre Rechenzentrum vorübergehend nicht verfügbar ist, können die Daten weiterhin am sekundären Standort gelesen werden. Dies kann sehr nützlich sein. Beispiel: Sie besitzen eine Webanwendung, die in den schreibgeschützten Modus wechselt und auf die sekundäre Kopie verweist. Dadurch ist ein gewisser Zugriff möglich, auch wenn keine Updates verfügbar sind.

> [!IMPORTANT]
> Sie können ändern, wie Ihre Daten repliziert werden, nachdem Ihr Speicherkonto erstellt wurde. Unter Umständen können zusätzlich einmalige Datenübertragungskosten anfallen, wenn Sie von LRS oder ZRS zu GRS oder RA-GRS wechseln.
>

Weitere Informationen zu Replikationsoptionen finden Sie unter [Azure Storage-Replikation](storage-redundancy.md).

Informationen zur Notfallwiederherstellung finden Sie unter [What to do if an Azure Storage outage occurs](storage-disaster-recovery-guidance.md) (Vorgehensweise beim Ausfall von Azure Storage).

Ein Beispiel für die Sicherstellung der Hochverfügbarkeit mithilfe von RA-GRS-Speicher finden Sie unter [Entwerfen hochverfügbarer Anwendungen mithilfe von RA-GRS](storage-designing-ha-apps-with-ragrs.md).

## <a name="transferring-data-to-and-from-azure-storage"></a>Übertragen von Daten in und aus Azure Storage

Über das Befehlszeilenprogramm AzCopy können Sie Blob- und Dateidaten innerhalb Ihres Speicherkontos oder zwischen Speicherkonten kopieren. Weitere Anweisungen finden Sie in den folgenden Artikeln:

* [Transfer data with the AzCopy on Windows](storage-use-azcopy.md) (Übertragen von Daten mit AzCopy unter Windows)
* [Transfer data with AzCopy on Linux](storage-use-azcopy-linux.md) (Übertragen von Daten mit AzCopy unter Linux)

AzCopy baut auf der [Azure Storage Data Movement Library](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)auf, die derzeit als Vorschau verfügbar ist.

Mit dem Azure Import/Export-Dienst können große Mengen Blobdaten in das Speicherkonto importiert bzw. aus dem Speicherkonto exportiert werden. Sie bereiten mehrere Festplatten vor und senden Sie an ein Azure-Rechenzentrum. Dort werden die Daten von den bzw. auf die Festplatten übertragen und die Festplatten anschließend an sie zurückgesendet. Weitere Informationen zu diesem Dienst finden Sie unter [Verwenden des Microsoft Azure Import/Export-Diensts zum Übertragen von Daten in den Blobspeicher](../storage-import-export-service.md).

## <a name="pricing"></a>Preise

Ausführliche Informationen zu den Preisen für Azure Storage finden Sie auf der Seite mit den [Azure Storage-Preisen](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="storage-apis-libraries-and-tools"></a>Storage-APIs, -Bibliotheken und -Tools
Auf Azure Storage-Ressourcen kann über jede Sprache zugegriffen werden, die für HTTP/HTTPS-Anforderungen geeignet ist. Zusätzlich bietet Azure Storage Programmierbibliotheken für einige beliebte Sprachen. Diese Bibliotheken vereinfachen viele Aspekte der Arbeit mit Azure Storage und behandeln bestimmte Details wie synchrone und asynchrone Aufrufe, die Zusammenfassung von Vorgängen, die Ausnahmeverwaltung, automatische Wiederholungen, das Betriebsverhalten und Ähnliches. Bibliotheken sind aktuell für die folgenden Sprachen und Plattformen erhältlich. Weitere sind in Planung.

### <a name="azure-storage-data-services"></a>Azure Storage-Datendienste
* [REST-API für Speicherdienste](/rest/api/storageservices/)
* [Speicherclientbibliothek für .NET](https://docs.microsoft.com/dotnet/api/?view=azurestorage-8.1.1)
* [Speicherclientbibliothek für C++](https://github.com/Azure/azure-storage-cpp)
* [Speicherclientbibliothek für Java/Android](https://azure.microsoft.com/develop/java/)
* [Speicherclientbibliothek für Node.js](http://dl.windowsazure.com/nodestoragedocs/index.html)
* [Speicherclientbibliothek für PHP](https://azure.microsoft.com/develop/php/)
* [Speicherclientbibliothek für Python](https://azure.microsoft.com/develop/python/)
* [Speicherclientbibliothek für Ruby](https://azure.microsoft.com/develop/ruby/)
* [Storage-Cmdlets für PowerShell](/powershell/module/azure.storage/?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)
* [Storage-Befehle für CLI 2.0](/cli/azure/storage)

## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen zu Blob Storage](../blobs/storage-blobs-introduction.md)
* [Weitere Informationen zu File Storage](../storage-files-introduction.md)
* [Weitere Informationen zu Queue Storage](../queues/storage-queues-introduction.md)

Informationen zur Herstellung der Betriebsbereitschaft für Azure Storage finden Sie unter [Erstellen eines Speicherkontos](storage-quickstart-create-account.md).

<!-- FIGURE OUT WHAT TO DO WITH ALL THESE LINKS.

Azure Storage resources can be accessed by any language that can make HTTP/HTTPS requests. Additionally, Azure Storage offers programming libraries for several popular languages. These libraries simplify many aspects of working with Azure Storage by handling details such as synchronous and asynchronous invocation, batching of operations, exception management, automatic retries, operational behavior and so forth. Libraries are currently available for the following languages and platforms, with others in the pipeline:

### Azure Storage data services
* [Storage Services REST API](https://docs.microsoft.com/rest/api/storageservices/)
* [Storage Client Library for .NET](https://docs.microsoft.com/dotnet/api/?view=azurestorage-8.1.1)
* [Storage Client Library for C++](https://github.com/Azure/azure-storage-cpp)
* [Storage Client Library for Java/Android](https://azure.microsoft.com/develop/java/)
* [Storage Client Library for Node.js](http://dl.windowsazure.com/nodestoragedocs/index.html)
* [Storage Client Library for PHP](https://azure.microsoft.com/develop/php/)
* [Storage Client Library for Python](https://azure.microsoft.com/develop/python/)
* [Storage Client Library for Ruby](https://azure.microsoft.com/develop/ruby/)
* [Storage Cmdlets for PowerShell](/powershell/module/azure.storage/?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)

### Azure Storage management services
* [Storage Resource Provider REST API Reference](/rest/api/storagerp/)
* [Storage Resource Provider Client Library for .NET](/dotnet/api/microsoft.azure.management.storage)
* [Storage Resource Provider Cmdlets for PowerShell 1.0](/powershell/module/azure.storage)
* [Storage Service Management REST API (Classic)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### Azure Storage data movement services
* [Storage Import/Export Service REST API](../storage-import-export-service.md)
* [Storage Data Movement Client Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)

### Tools and utilities
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) is a free, standalone app from Microsoft that enables you to work visually with Azure Storage data on Windows, macOS, and Linux.
* [Azure Storage Client Tools](../storage-explorers.md)
* [Azure SDKs and Tools](https://azure.microsoft.com/tools/)
* [Azure Storage Emulator](http://www.microsoft.com/download/details.aspx?id=43709)
* [Azure PowerShell](/powershell/azure/overview)
* [AzCopy Command-Line Utility](http://aka.ms/downloadazcopy)

## Next steps
To learn more about Azure Storage, explore these resources:

### Documentation
* [Azure Storage Documentation](https://azure.microsoft.com/documentation/services/storage/)
* [Create a storage account](../storage-create-storage-account.md)

-->

### <a name="for-administrators"></a>Für Administratoren
* [Verwenden von Azure PowerShell mit Azure Storage](storage-powershell-guide-full.md)
* [Verwenden der Azure-Befehlszeilenschnittstelle mit Azure Storage](../storage-azure-cli.md)

### <a name="for-net-developers"></a>Für .NET-Entwickler
* [Erste Schritte mit Azure Blob Storage mit .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Entwickeln für Azure Files mit .NET](../files/storage-dotnet-how-to-use-files.md)
* [Erste Schritte mit Azure Table Storage mit .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Erste Schritte mit Azure Queue Storage mit .NET](../storage-dotnet-how-to-use-queues.md)

### <a name="for-javaandroid-developers"></a>Für Java-/Android-Entwickler
* [Gewusst wie: Verwenden von Blob Storage mit Java](../blobs/storage-java-how-to-use-blob-storage.md)
* [Entwickeln für Azure Files mit Java](../files/storage-java-how-to-use-file-storage.md)
* [Gewusst wie: Verwenden von Table Storage mit Java](../../cosmos-db/table-storage-how-to-use-java.md)
* [Gewusst wie: Verwenden von Queue Storage mit Java](../storage-java-how-to-use-queue-storage.md)

### <a name="for-nodejs-developers"></a>Für Node.js-Entwickler
* [Gewusst wie: Verwenden von Blob Storage mit Node.js](../blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Gewusst wie: Verwenden von Table Storage mit Node.js](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [Gewusst wie: Verwenden von Queue Storage mit Node.js](../storage-nodejs-how-to-use-queues.md)

### <a name="for-php-developers"></a>Für PHP-Entwickler
* [Gewusst wie: Verwenden von Blob Storage mit PHP](../blobs/storage-php-how-to-use-blobs.md)
* [Gewusst wie: Verwenden von Table Storage mit PHP](../../cosmos-db/table-storage-how-to-use-php.md)
* [Gewusst wie: Verwenden von Queue Storage mit PHP](../storage-php-how-to-use-queues.md)

### <a name="for-ruby-developers"></a>Für Ruby-Entwickler
* [Gewusst wie: Verwenden von Blob Storage mit Ruby](../blobs/storage-ruby-how-to-use-blob-storage.md)
* [Gewusst wie: Verwenden von Table Storage mit Ruby](../../cosmos-db/table-storage-how-to-use-ruby.md)
* [Gewusst wie: Verwenden von Queue Storage mit Ruby](../storage-ruby-how-to-use-queue-storage.md)

### <a name="for-python-developers"></a>Für Python-Entwickler
* [Gewusst wie: Verwenden von Blob Storage mit Python](../blobs/storage-python-how-to-use-blob-storage.md)
* [Entwickeln für Azure Files mit Python](../files/storage-python-how-to-use-file-storage.md)
* [Gewusst wie: Verwenden von Table Storage mit Python](../../cosmos-db/table-storage-how-to-use-python.md)
* [Gewusst wie: Verwenden von Queue Storage mit Python](../storage-python-how-to-use-queue-storage.md)
