---
title: Zugriffsebenen „Heiß“, „Kalt“ und „Archiv“ für Blobs – Azure Storage
description: Zugriffsebenen „Heiß“, „Kalt“ und „Archiv“ für Azure Storage-Konten.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/23/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: 48c6d6ed60045d906fcb711bd07ab492b6bbf488
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543683"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Azure Blob Storage: Zugriffsebenen „Heiß“, „Kalt“ und „Archiv“

Azure Storage bietet unterschiedliche Zugriffsebenen, die Ihnen das Speichern von Blobobjektdaten auf die kostengünstigste Art ermöglichen. Die verfügbaren Zugriffsebenen sind:

- **Heiß** ist für die Speicherung von Daten optimiert, auf die häufig zugegriffen wird.
- **Kalt** ist für die Speicherung von Daten optimiert, auf die selten zugegriffen wird und die mindestens 30 Tage lang gespeichert werden.
- **Archiv** ist für die Speicherung von Daten optimiert, auf die äußerst selten zugegriffen wird und die mindestens 180 Tage lang mit flexiblen Latenzanforderungen (in der Größenordnung von Stunden) gespeichert werden.

Die folgenden Überlegungen gelten für die unterschiedlichen Zugriffsebenen:

- Nur die heißen und kalten Zugriffsebenen können auf Kontoebene festgelegt werden. Die Archivzugriffsebene ist auf der Kontoebene nicht verfügbar.
- Die Ebenen „Heiß“, „Kalt“ und „Archiv“ können auf der Blobebene festgelegt werden.
- Bei den Daten der kalten Zugriffsebene können geringfügige Abstriche bei der Verfügbarkeit gemacht werden, Dauerhaftigkeit, Abrufwartezeit und Durchsatz müssen sich jedoch auf einem ähnlich hohen Niveau befinden wie bei Daten der heißen Ebene. Daher kann bei Daten der kalten Ebene eine Kombination aus einer Servicelevelvereinbarung (Service-Level Agreement, SLA) mit etwas niedrigerer Verfügbarkeit und höheren Zugriffskosten im Vergleich zu Daten der heißen Ebene in Kauf genommen werden, um im Gegenzug die Speicherkosten zu verringern.
- Der Archivspeicher speichert Daten offline und zeichnet sich durch die niedrigsten Speicherkosten aus, verursacht aber gleichzeitig die höchsten Kosten für Datenaktivierung und Datenzugriff.

Die Speicherung von Daten in der Cloud nimmt immer mehr zu. Um die Kosten für die zunehmenden Speicheranforderungen im Blick zu behalten, ist es hilfreich, die Daten anhand von Attributen wie der Zugriffshäufigkeit und geplanten Aufbewahrungsdauer zu organisieren. So können die Kosten optimiert werden. In der Cloud gespeicherte Daten können sich darin unterscheiden, wie sie über ihre Lebensdauer hinweg generiert, verarbeitet und genutzt werden. Auf einen Teil der Daten wird aktiv zugegriffen, und sie werden während ihrer Lebensdauer geändert. Auf andere Daten wird zu Beginn ihrer Lebensdauer häufig zugegriffen, und die Zugriffe fallen dann deutlich ab, je älter die Daten werden. Außerdem gibt es Daten, die in der Cloud lediglich vorgehalten werden und auf die nach der Speicherung nur sehr selten oder gar nicht zugegriffen wird.

Jedes dieser Datenzugriffsszenarien profitiert von einer differenzierten Zugriffsebene, die für ein bestimmtes Zugriffsmuster optimiert ist. Mit den heißen, kalten und Archivzugriffsebenen bietet Azure Blob Storage unterschiedliche Zugriffsebenen mit separaten Preismodellen.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Speicherkonten mit Tiering-Unterstützung

Objektspeicher-Datentiering für „Heiß“, „Kalt“ und „Archiv“ wird nur in Blobspeicherkonten und in GPv2-Konten (universelle Konten der Version 2) unterstützt. Für GPv1-Konten (General Purpose v1) wird das Tiering nicht unterstützt. Kunden können ihre vorhandenen GPv1- oder Blobspeicherkonten aber leicht in GPv2-Konten konvertieren, indem sie im Azure-Portal einen Schritt ausführen, für den nur ein Klick erforderlich ist. GPv2 bietet eine neue Preisstruktur für Blobs, Dateien und Warteschlangen sowie Zugriff auf verschiedene neue Speicherfeatures. Zudem werden in Zukunft einige neue Features und Preisreduzierungen nur für GPv2-Konten angeboten. Aus diesem Grund sollten Kunden die Nutzung von GPv2-Konten evaluieren, nachdem sie sich umfassend mit den neuen Preisen vertraut gemacht haben, da einige Workloads mit GPv2 teurer sein können als mit GPv1. Weitere Informationen finden Sie unter [Azure-Speicherkonto – Übersicht](../common/storage-account-overview.md).

Blobspeicher- und GPv2-Konten machen das **Zugriffsebene**-Attribut auf Kontoebene verfügbar. Mit diesem Attribut können Sie die Standardzugriffsebene für alle Blobs des Speicherkontos, für die keine explizite Ebene festgelegt ist, als „Heiß“ oder „Kalt“ angeben. Für Objekte, für die die Ebene auf Objektebene festgelegt ist, gilt die Kontoebene nicht. Die Archivebene kann nur auf der Objektebene angewendet werden. Sie können jederzeit zwischen diesen Zugriffsebenen wechseln.

## <a name="premium-performance-block-blob-storage"></a>Blockblobspeicher mit Premium-Leistung

Blockblobspeicher mit Premium-Leistung macht Daten, auf die häufig zugegriffen wird, über Hochleistungshardware verfügbar. Daten in dieser Leistungsstufe werden auf Festkörperlaufwerken (Solid State Drives, SSDs) gespeichert, die für niedrige und konsistente Latenz optimiert sind. Transaktionale Raten und Durchsatz sind bei SSDs im Vergleich zu herkömmlichen Festplatten höher.

Blockblobspeicher mit Premium-Leistung eignet sich ideal für Workloads, die schnelle und konsistente Antwortzeiten benötigen. Dies ist auf Workloads ausgelegt, bei denen viele kleine Transaktionen durchgeführt werden, z.B. die Erfassung von Telemetriedaten, die Nachrichtenübermittlung und die Datentransformation. Daten, an denen Endbenutzer beteiligt sind, wie interaktive Videobearbeitung, statische Webinhalte und Onlinetransaktionen sind auch gute Kandidaten.

Blockblobspeicher mit Premium-Leistung ist nur über den Blockblob-Speicherkontotyp verfügbar und unterstützt derzeit kein Tiering zu den Zugriffsebenen „Heiß“, „Kalt“ oder „Archiv“.

## <a name="hot-access-tier"></a>Zugriffsebene „Hot“

Bei der heißen Zugriffsebene fallen höhere Speicherkosten an als bei der kalten und der Archivebene, aber gleichzeitig sind auch die Zugriffskosten am niedrigsten. Beispielszenarien für die Verwendung der heißen Zugriffsebene:

- Daten, die aktiv verwendet werden oder bei denen eine hohe Zugriffshäufigkeit (Lese- und Schreibvorgänge) zu erwarten ist.
- Daten, die zur Verarbeitung und späteren Migration zur kalten Zugriffsebene bereitgestellt werden.

## <a name="cool-access-tier"></a>Zugriffsebene „Cool“

Für die kalte Zugriffsebene fallen im Vergleich mit dem heißen Speicher niedrigere Speicherkosten und höhere Zugriffskosten an. Diese Ebene ist für Daten bestimmt, die mindestens 30 Tage lang auf der Ebene „Cool“ verbleiben. Beispielszenarien für die Verwendung der kalten Zugriffsebene:

- Kurzfristige Sicherung und Datasets für die Notfallwiederherstellung
- Ältere Medieninhalte, die nur noch selten angezeigt werden, dann aber umgehend verfügbar sein müssen
- Große Datasets, die kostengünstig gespeichert werden müssen, während weitere Daten für die zukünftige Verarbeitung gesammelt werden (*Beispiel:* langfristige Speicherung von wissenschaftlichen Daten oder Telemetrie-Rohdaten einer Produktionsstätte)

## <a name="archive-access-tier"></a>Zugriffsebene „Archiv“

Die Archivzugriffsebene hat im Vergleich zur heißen und kalten Ebene die niedrigsten Speicherkosten und höhere Datenabrufkosten. Diese Ebene ist für Daten bestimmt, die mehrere Stunden Abrufwartezeit tolerieren und mindestens 180 Tage lang auf der Archivebene verbleiben.

Solange ein Blob im Archivspeicher ist, sind die Blobdaten offline und können nicht gelesen, kopiert, überschrieben oder geändert werden. Sie können keine Momentaufnahmen von einem Blob im Archivspeicher erstellen. Die Blobmetadaten bleiben jedoch online und verfügbar, sodass Sie das Blob und seine Eigenschaften auflisten können. Für Blobs im Archiv sind GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier und DeleteBlob die einzigen gültigen Vorgänge.

Beispielszenarien für die Verwendung der Archivzugriffsebene:

- Langfristige Sicherung, sekundäre Sicherung und Archivierungsdatasets
- Originaldaten (Rohdaten), die auch nach der Umwandlung in ein endgültiges verwendbares Format erhalten bleiben müssen (*Beispiel:* Medien-Rohdateien nach der Transcodierung in andere Formate)
- Compliance- und Archivdaten, die über einen langen Zeitraum gespeichert werden müssen und selten verwendet werden (*Beispiele:* Überwachungsvideos, alte Röntgenbilder/MRT-Scans für Unternehmen aus dem Gesundheitswesen, Audioaufnahmen und Protokolle von Kundenanrufen bei Finanzdienstleistern)

### <a name="blob-rehydration"></a>Blobaktivierung

[!INCLUDE [storage-blob-rehydrate-include](../../../includes/storage-blob-rehydrate-include.md)]
Weitere Informationen finden Sie unter [Aktivieren von Blobdaten aus der Archivzugriffsebene](storage-blob-rehydration.md).  

## <a name="account-level-tiering"></a>Tiering auf Kontoebene

Ein Konto kann Blobs aus allen drei Zugriffsebenen enthalten. Blobs, denen keine explizite Ebene zugewiesen ist, leiten die Ebene von der Zugriffsebeneneinstellung des Kontos ab. Wenn die Zugriffsebene vom Konto abgeleitet wird, sehen Sie, dass die Blobeigenschaft **Access Tier Inferred** (Abgeleitete Zugriffsebene) auf TRUE festgelegt ist und die Blobeigenschaft **Access Tier** (Zugriffsebene) des Blobs mit der Kontoebene übereinstimmt. Im Azure-Portal wird die Eigenschaft _access tier inferred_ (Abgeleitete Zugriffsebene) zusammen mit der Blobzugriffsebene als **Heiß (abgeleitet)** oder **Kalt (abgeleitet)** angezeigt.

Die Änderung der Kontozugriffsebene gilt für alle im Konto gespeicherten Objekte vom Typ _access tier inferred_ (Abgeleitete Zugriffsebene), für die keine explizite Ebene festgelegt ist. Wenn Sie für die Kontoebene von „Hot“ zu „Cool“ wechseln, werden Ihnen Schreibvorgänge (pro 10.000) für alle Blobs ohne festgelegte Ebene nur für GPv2-Konten berechnet. Für diese Änderung bei Blobspeicherkonten fallen keine Gebühren an. Ihnen werden sowohl Gebühren für Lesevorgänge (pro 10.000) als auch für den Datenabruf (pro GB) berechnet, wenn Sie für Ihr Blobspeicher- oder GPv2-Konto von „Kalt“ zu „Heiß“ wechseln.

## <a name="blob-level-tiering"></a>Blobebenentiering

Mit dem Blobebenentiering können Sie mithilfe eines einzelnen Vorgangs namens [Set Blob Tier](/rest/api/storageservices/set-blob-tier) (Blobebene festlegen) die Ebene Ihrer Daten auf der Objektebene ändern. So können Sie flexibel auf Nutzungsänderungen reagieren und problemlos zwischen den Blobzugriffsebenen „Hot“, „Cool“ und „Archiv“ wechseln, ohne Daten zwischen Konten zu verschieben. Alle Ebenenänderungen erfolgen sofort. Die Aktivierung eines BLOBs aus dem Archiv kann jedoch mehrere Stunden dauern.

Der Zeitpunkt der letzten Änderung der Blobebene wird über die Blobeigenschaft **Access Tier Change Time** (Änderungszeitpunkt der Zugriffsebene) verfügbar gemacht. Wenn sich ein Blob auf der Archivebene befindet, kann es nicht überschrieben werden. Daher ist das Hochladen desselben Blobs in diesem Szenario nicht zulässig. Sie können ein Blob auf der heißen und kalten Ebene überschreiben. In diesem Fall erbt das neue Blob die Ebene des Blobs, das überschrieben wurde.

> [!NOTE]
> Für die Archivspeicherebene und das Blobebenentiering werden nur Blockblobs unterstützt. Außerdem ist es derzeit nicht möglich, die Ebene eines Blockblobs zu ändern, für das Momentaufnahmen vorhanden sind.

### <a name="blob-lifecycle-management"></a>Lebenszyklusverwaltung für Blobs

Die Blob Storage-Lebenszyklusverwaltung bietet eine umfassende regelbasierte Richtlinie, mit deren Hilfe Sie den Übergang Ihrer Daten in die optimale Zugriffsebene und den Ablauf der Daten am Ende ihres Lebenszyklus umsetzen können. Weitere Informationen finden Sie unter [Verwalten des Azure Blob Storage-Lebenszyklus](storage-lifecycle-management-concepts.md).  

> [!NOTE]
> Für in einem Blockblob-Speicherkonto mit Premium-Leistung gespeicherte Daten ist derzeit weder mithilfe von [Blobtarif festlegen](/rest/api/storageservices/set-blob-tier) noch unter Verwendung der Azure Blob Storage-Lebenszyklusverwaltung ein Tiering zu „Heiß“, „Kalt“ oder „Archiv“ möglich.
> Um Daten zu verschieben, müssen Sie Blobs synchron vom Blockblob-Speicherkonto auf die heiße Zugriffsebene in einem anderen Konto kopieren und hierzu die [Put Block From URL-API](/rest/api/storageservices/put-block-from-url) oder eine AzCopy-Version, die diese API unterstützt, verwenden.
> Die *Put Block From URL*-API kopiert Daten synchron auf den Server, was heißt, dass der Aufruf erst abgeschlossen wird, nachdem alle Daten vom ursprünglichen Serverspeicherort an den Zielspeicherort verschoben wurden.

### <a name="blob-level-tiering-billing"></a>Abrechnung für das Blobebenentiering

Wenn ein Blob in eine „kältere“ Ebene verschoben wird (von „Heiß“ zu „Kalt“, von „Heiß“ zu „Archiv“ oder von „Kalt“ zu „Archiv“), wird der Vorgang als Schreibvorgang auf die Zielebene berechnet, und es gelten die Zielebenengebühren für Schreibvorgänge (pro 10.000) und das Schreiben von Daten (pro GB).

Wenn ein Blob in eine „wärmere“ Ebene verschoben wird (von „Archiv“ zu „Kalt“, von „Archiv“ zu „Heiß“ oder von „Kalt“ zu „Heiß“), wird der Vorgang als Lesevorgang aus der Quellebene berechnet, und es gelten die Quellebenengebühren für Lesevorgänge (pro 10.000) und Datenabruf (pro GB). Für alle Blobs, die aus der Ebene „Cool“ oder „Archiv“ verschoben werden, können auch Gebühren für das frühe Löschen anfallen. Die folgende Tabelle gibt Aufschluss darüber, wie Ebenenänderungen abgerechnet werden.

| | **Schreibgebühren (Vorgang + Zugriff)** | **Lesegebühren (Vorgang + Zugriff)**
| ---- | ----- | ----- |
| **SetBlobTier-Richtung** | Heiß > Kalt<br> Heiß > Archiv<br> Kalt > Archiv | Archiv > Kalt<br> Archiv > Heiß<br> Kalt > Heiß

### <a name="cool-and-archive-early-deletion"></a>Frühzeitiges Löschen für kalte Speicher und Archivspeicher

Zusätzlich zur Gebühr, die pro GB und pro Monat berechnet wird, gilt Folgendes: Alle Blobs, die in die Ebene „Cool“ (nur GPv2-Konten) verschoben werden, unterliegen einem Zeitraum von 30 Tagen für das frühe Löschen für „Cool“, und alle Blobs, die in die Ebene „Archiv“ verschoben werden, unterliegen einem Zeitraum von 180 Tagen für das frühe Löschen für „Archiv“. Diese Gebühr fällt anteilig an. Wenn ein Blob beispielsweise in das Archiv verschoben und dann nach 45 Tagen gelöscht oder in die Ebene „Hot“ verschoben wird, wird Ihnen eine Gebühr für das frühe Löschen berechnet, die 135 Speichertagen (180 minus 45) dieses Blobs im Archiv entspricht.

Sie können das frühe Löschen mithilfe der Blobeigenschaft **Last-Modified** berechnen, sofern keine Änderungen an der Zugriffsebene vorgenommen wurden. Andernfalls können Sie den Zeitpunkt der letzten Änderung der Zugriffsebene auf „Kalt“ oder „Archiv“ verwenden, indem Sie die Blobeigenschaft **access-tier-change-time** anzeigen. Weitere Informationen zu Blobeigenschaften finden Sie unter [Abrufen von Blobeigenschaften](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Vergleichen von Blockblobspeicher-Optionen

Die folgende Tabelle enthält eine Gegenüberstellung des Premium-Leistungsblockblobspeichers und der Zugriffsebenen „Heiß“, „Kalt“ und „Archiv“.

|                                           | **Premium-Leistung**   | **Zugriffsebene „Heiß“** | **Zugriffsebene „Kalt“**       | **Zugriffsebene „Archiv“**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **Verfügbarkeit**                          | 99,9 %                     | 99,9 %        | 99 %                 | Offline           |
| **Verfügbarkeit** <br> **(RA-GRS-Lesevorgänge)**  | –                       | 99,99 %       | 99,9 %               | Offline           |
| **Nutzungsgebühren**                         | Höhere Speicherkosten, geringere Zugriffs- und Transaktionskosten | Höhere Speicherkosten, geringere Zugriffs- und Transaktionskosten | Geringere Speicherkosten, höhere Zugriffs- und Transaktionskosten | Niedrigste Speicherkosten, höchste Zugriffs- und Transaktionskosten |
| **Mindestobjektgröße**                   | –                       | –          | –                 | –               |
| **Mindestspeicherdauer**              | –                       | –          | 30 Tage<sup>1</sup> | 180 Tage
| **Latenz** <br> **(Zeit bis zum ersten Byte)** | Einstellige Millisekunden | Millisekunden | Millisekunden        | Stunden<sup>2</sup> |

<sup>1</sup> Bei GPv2-Konten beträgt die Aufbewahrungsdauer für Objekte der kalten Ebene mindestens 30 Tage. Bei Blobspeicherkonten gibt es keine Mindestaufbewahrungsdauer für die kalte Ebene.

<sup>2</sup> Archivspeicher unterstützt derzeit zwei Aktivierungsprioritäten („Hoch“ und „Standard“) mit unterschiedlichen Abrufwartezeiten. Weitere Informationen finden Sie unter [Blobaktivierung](#blob-rehydration).

> [!NOTE]
> Blobspeicherkonten unterstützen die gleichen Leistungs- und Skalierbarkeitsziele wie universelle Speicherkonten der Version 2. Weitere Informationen finden Sie unter [Skalierbarkeits- und Leistungszielen für Azure Storage](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="quickstart-scenarios"></a>Schnellstartszenarien

In diesem Abschnitt werden unter Verwendung des Azure-Portals die folgenden Szenarien veranschaulicht:

- Ändern der Standard-Kontozugriffsebene für ein GPv2- oder Blobspeicherkonto
- Ändern der Ebene eines Blobs in einem GPv2- oder Blobspeicherkonto

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Ändern der Standard-Kontozugriffsebene für ein GPv2- oder Blob Storage-Konto

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Navigieren Sie zu Ihrem Speicherkonto, wählen Sie „Alle Ressourcen“, und wählen Sie dann Ihr Speicherkonto aus.

1. Klicken Sie auf dem Blatt „Einstellungen“ auf **Konfiguration**, um die Kontokonfiguration anzuzeigen bzw. zu ändern.

1. Wählen Sie die passende Zugriffsebene für Ihre Anforderungen aus: Legen Sie die **Zugriffsebene** entweder auf **kalte Ebene** oder auf **heiße Ebene** fest.

1. Klicken Sie oben auf dem Blatt auf **Speichern** .

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Ändern der Ebene eines Blobs in einem GPv2- oder Blobspeicherkonto

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie zum Navigieren zu Ihrem Blob in Ihrem Speicherkonto die Option „Alle Ressourcen“, und wählen Sie Ihr Speicherkonto, Ihren Container und dann Ihr Blob aus.

1. Klicken Sie auf dem Blatt **Blob-Eigenschaften** auf die Schaltfläche **Ebene ändern**, um das Blatt für Ebenen zu öffnen.

1. Wählen Sie die Zugriffsebene **Heiß**, **Kalt** oder **Archiv** aus. Wenn sich Ihr Blob derzeit im Archiv befindet und Sie ihn auf einer Onlineebene aktivieren möchten, können Sie auch als Aktivierungspriorität **Standard** oder **Hoch** auswählen.

1. Klicken Sie unten auf dem Blatt auf **OK**.

## <a name="pricing-and-billing"></a>Preise und Abrechnung

Für alle Speicherkonten wird ein Blockblobspeicher-Preismodell verwendet, das auf der Ebene der einzelnen Blobs basiert. Beachten Sie dabei die folgenden Abrechnungsaspekte:

- **Speicherkosten:** Die Kosten für die Datenspeicherung hängen nicht nur von der gespeicherten Datenmenge ab, sondern auch von der Zugriffsebene. Je „cooler“ die Ebene, desto geringer die Kosten pro GB.
- **Kosten für den Datenzugriff:** Je „kälter“ die Ebene, desto höher die Gebühren für den Datenzugriff. Bei den Zugriffsebenen „Kalt“ und „Archiv“ fallen Zugriffsgebühren für Lesevorgänge pro Gigabyte an.
- **Transaktionskosten:** Für alle Ebenen fällt eine Gebühr pro Transaktion an, die sich erhöht, je „kälter“ die Ebene ist.
- **Datenübertragungskosten bei Georeplikation:** Diese Gebühr gilt nur für Konten mit konfigurierter Georeplikation (einschließlich GRS und RA-GRS). Die Datenübertragung für die Georeplikation wird pro Gigabyte abgerechnet.
- **Kosten für ausgehende Datenübertragungen:** Ausgehende Datenübertragungen (Daten, die aus einer Azure-Region übertragen werden) werden genau wie bei allgemeinen Speicherkonten nach Bandbreitennutzung pro Gigabyte abgerechnet.
- **Änderung der Zugriffsebene:** Bei Änderungen der Kontozugriffsebene fallen entsprechende Änderungsgebühren für alle im Konto gespeicherten Blobs vom Typ _access tier inferred_ (Abgeleitete Zugriffsebene) an, für die keine explizite Ebene festgelegt ist. Informationen zum Ändern der Zugriffsebene für ein einzelnes Blob finden Sie unter [Abrechnung für das Blobebenentiering](#blob-level-tiering-billing).

> [!NOTE]
> Weitere Informationen zu den Preisen für Blockblobs finden Sie auf der Seite [Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/). Weitere Informationen zu den Kosten für ausgehende Datenübertragungen finden Sie auf der Seite [Datenübertragungen – Preisdetails](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>Häufig gestellte Fragen

**Sollte ich Blobspeicher- oder GPv2-Konten verwenden, wenn ich für meine Daten das Tiering durchführen möchte?**

Wir empfehlen Ihnen, für das Tiering anstelle von Blobspeicherkonten GPv2-Konten zu nutzen. Für GPv2 werden alle Features von Blobspeicherkonten sowie noch viele weitere Features unterstützt. Die Preise für Blobspeicher und GPv2 sind nahezu identisch, aber einige neue Features und Preisreduzierungen gelten nur für GPv2-Konten. Für GPv1-Konten wird das Tiering nicht unterstützt.

Die Preisstruktur von GPv1 und GPv2 unterscheidet sich, und Kunden sollten dies jeweils sorgfältig evaluieren, bevor sie sich für die Nutzung von GPv2-Konten entscheiden. Sie können ein vorhandenes Blobspeicher- oder GPv1-Konto leicht in ein GPv2-Konto konvertieren, indem Sie im Azure-Portal einen Schritt ausführen, für den nur ein Klick erforderlich ist. Weitere Informationen finden Sie unter [Azure-Speicherkonto – Übersicht](../common/storage-account-overview.md).

**Kann ich Objekte unter demselben Konto auf allen drei Zugriffsebenen („Heiß“, „Kalt“ und „Archiv“) speichern?**

Ja. Das Attribut **Zugriffsebene** auf der Kontoebene ist die Standardkontoebene, die für alle Objekte des Kontos gilt, für die keine explizite Ebene festgelegt ist. Mit dem Blobebenentiering können Sie die Zugriffsebene auf dieser Objektebene unabhängig davon festlegen, welche Zugriffsebene für das Konto eingestellt ist. In einem Konto können Blobs in allen drei Zugriffsebenen („Heiß“, „Kalt“ oder „Archiv“) vorhanden sein.

**Kann ich die Standardzugriffsebene meines Blob Storage- oder GPv2-Kontos ändern?**

Ja. Sie können die Standardkontoebene ändern, indem Sie für das Speicherkonto das Attribut **Zugriffsebene** festlegen. Die Änderung der Kontoebene gilt für alle im Konto gespeicherten Objekte, für die keine explizite Ebene festgelegt ist (beispielsweise **Heiß (abgeleitet)** oder **Kalt (abgeleitet)** ). Wenn Sie bei der Kontoebene von „Heiß“ zu „Kalt“ wechseln, fallen Gebühren für Schreibvorgänge (pro 10.000) nur für alle Blobs ohne festgelegte Ebene in GPv2-Konten an. Wenn Sie von „Kalt“ zu „Heiß“ wechseln, fallen Gebühren für Lesevorgänge (pro 10.000) und den Datenabruf (pro GB) für alle Blobs in Blobspeicher- und GPv2-Konten an.

**Kann ich meine Standard-Kontozugriffsebene auf „Archiv“ festlegen?**

Nein. Nur die Zugriffsebenen „Heiß“ und „Kalt“ können als Standard-Kontozugriffsebene festgelegt werden. „Archiv“ kann nur auf Objektebene festgelegt werden.

**In welchen Regionen sind die Zugriffsebenen „Heiß“, „Kalt“ und „Archiv“ verfügbar?**

Die Zugriffsebenen „Heiß“ und „Kalt“ sind zusammen mit dem Blobebenentiering in allen Regionen verfügbar. Die Speicherebene „Archiv“ ist anfänglich nur in ausgewählten Regionen verfügbar. Eine vollständige Liste finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/regions/services/).

**Verhalten sich die Blobs der Zugriffsebene „Kalt“ anders als die Blobs der Zugriffsebene „Heiß“?**

Blobs der Zugriffsebene „Heiß“ weisen die gleiche Latenz wie Blobs in GPv1-, GPv2- und Blobspeicherkonten auf. Blobs der Zugriffsebene „Kalt“ verfügen über eine ähnliche Latenz (in Millisekunden) wie Blobs in GPv1-, GPv2- und Blobspeicherkonten. In GPv1-, GPv2- und Blobspeicherkonten gilt für Blobs auf der Zugriffsebene „Archiv“ eine Latenzzeit von mehreren Stunden.

Für Blobs der Zugriffsebene „Kalt“ gilt in Bezug auf die Verfügbarkeit ein etwas geringerer Servicelevel (SLA) als für Blobs, die auf der Zugriffsebene „Heiß“ gespeichert sind. Weitere Informationen finden Sie unter [SLA für Speicher](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**Sind die Vorgänge für die Ebenen „Hot“, „Cool“ und „Archiv“ identisch?**

Alle Vorgänge zwischen „Hot“ und „Cool“ sind zu 100% konsistent. Alle gültigen Archivvorgänge (einschließlich „GetBlobProperties“, „GetBlobMetadata“, „ListBlobs“, „SetBlobTier“ und „DeleteBlob“) sind mit „Heiß“ und „Kalt“ vollständig konsistent. Blobdaten auf der Archivebene müssen erst aktiviert werden, damit sie gelesen oder geändert werden können. Im Archiv werden nur Lesevorgänge für Blobmetadaten unterstützt.

**Wie kann ich beim Aktivieren eines Blobs von der Ebene „Archiv“ auf die Ebene „Hot“ oder „Cool“ erkennen, wann die Aktivierung abgeschlossen ist?**

Während der Aktivierung können Sie den Vorgang „Get Blob Properties“ verwenden, um das Attribut **Archive Status** (Archivstatus) abzufragen und so zu ermitteln, wann die Ebenenänderung abgeschlossen ist. Je nach Zielebene lautet der Status entweder „rehydrate-pending-to-hot“ (Aktivierung für Ebene „Hot“ ausstehend) oder „rehydrate-pending-to-cool“ (Aktivierung für Ebene „Cool“ ausstehend). Nach Abschluss des Vorgangs wird die Eigenschaft „Archive Status“ (Archivstatus) entfernt, und die Eigenschaft **Access Tier** (Zugriffsebene) des Blobs spiegelt die neue Ebene vom Typ „Hot“ oder „Cool“ wider.  

**Ab wann werden mir nach dem Festlegen der Ebene eines Blobs die entsprechenden Gebühren berechnet?**

Die Berechnung für ein Blob basiert immer auf der Ebene, die in der Eigenschaft **Access Tier** (Zugriffsebene) des Blobs angegeben ist. Wenn Sie eine neue Ebene für ein Blob festlegen, übernimmt die Eigenschaft **Access Tier** (Zugriffsebene) sofort die neue Ebene für alle Übergänge. Die Aktivierung eines Blobs von der Archivebene in eine heiße oder kalte Ebene kann jedoch mehrere Stunden dauern. In diesem Fall werden Ihnen die Kosten für „Archiv“ berechnet, bis die Aktivierung abgeschlossen ist, wonach dann die neue Ebene in der Eigenschaft **Access Tier** (Zugriffsebene) angezeigt wird. Ab diesem Zeitpunkt erfolgt die Abrechnung für dieses Blob mit der Rate für die Ebene „Heiß“ oder „Kalt“.

**Wie kann ich ermitteln, ob eine Gebühr für das frühe Löschen anfällt, wenn ich ein Blob lösche oder aus der Ebene „Cool“ oder „Archiv“ verschiebe?**

Für alle Blobs, die vor Ablauf von 30 bzw. 180 Tagen gelöscht oder aus der Ebene „Cool“ (nur GPv2-Konten) oder „Archiv“ verschoben werden, fällt eine anteilige Gebühr für frühes Löschen an. Sie können ermitteln, wie lange sich ein Blob auf der Ebene „Kalt“ oder „Archiv“ befunden hat, indem Sie in der Blobeigenschaft **Access Tier Change Time** (Änderungszeitpunkt der Zugriffsebene) den Zeitstempel der letzten Ebenenänderung überprüfen. Falls die Blobebene noch nie geändert wurde, können Sie die Blobeigenschaft **Letzte Änderung** überprüfen. Weitere Informationen finden Sie unter [Frühzeitiges Löschen für kalte Speicher und Archivspeicher](#cool-and-archive-early-deletion).

**Welche Azure-Tools und SDKs unterstützen das Blobebenentiering und die Speicherebene „Archiv“?**

Für das Azure-Portal, PowerShell und CLI-Tools sowie .NET-, Java-, Python- und Node.js-Clientbibliotheken werden das Blobebenentiering und die Speicherebene „Archiv“ unterstützt.  

**Wie viele Daten kann ich auf den Ebenen „Hot“, „Cool“ und „Archiv“ speichern?**

Die Datenspeicherung wird zusammen mit anderen Beschränkungen auf Kontoebene und nicht einzeln pro Zugriffsebene festgelegt. Daher können Sie wählen, ob Sie Ihr gesamtes Kontingent auf einer Ebene nutzen oder auf alle drei Ebenen verteilen möchten. Weitere Informationen finden Sie unter [Skalierbarkeits- und Leistungsziele für Azure Storage](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Nächste Schritte

### <a name="evaluate-hot-cool-and-archive-in-gpv2-and-blob-storage-accounts"></a>Evaluieren von „Heiß“, „Kalt“ und „Archiv“ in GPv2- und Blobspeicherkonten

Prüfen der Verfügbarkeit von „Hot“, „Cool“ und „Archiv“ nach Region unter [Azure-Regionen](https://azure.microsoft.com/regions/#services)

[Verwalten des Azure Blob Storage-Lebenszyklus](storage-lifecycle-management-concepts.md)

[Informationen zum Aktivieren von Blobdaten aus der Archivzugriffsebene](storage-blob-rehydration.md)

[Auswerten der Nutzung vorhandener Speicherkonten durch Aktivierung von Azure Storage-Metriken](../common/storage-enable-and-view-metrics.md)

Prüfen der Preise für „Heiß“, „Kalt“ und „Archiv“ in Blobspeicher- und GPv2-Konten nach Region unter [Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage/)

[Überprüfen der Preise für Datenübertragungen unter Datenübertragungen – Preisdetails](https://azure.microsoft.com/pricing/details/data-transfers/)
