---
title: 'Azure Data Share (Vorschauversion): Terminologie'
description: 'Azure Data Share (Vorschauversion): Terminologie'
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 01025acfa2fd939db8134a1e61c7902034a1d2c3
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424212"
---
# <a name="azure-data-share-preview-concepts"></a>Konzepte von Azure Data Share (Vorschauversion) 

Die Vorschauversion von Azure Data Share führt neue Terminologie im Zusammenhang mit Datenfreigabe ein. Dieser Artikel beschreibt einige häufig verwendete Begriffe, die überall im Dienst verwendet werden. 

## <a name="data-provider"></a>Datenanbieter

Ein Datenanbieter ist die Organisation, die Daten für Kunden freigibt. Der Datenanbieter kann in der Regel der Besitzer oder ein Kurator der Daten sein. Datenanbieter möchten Daten verschiedener Typen freigeben. Einige Beispiele für Daten, die ein Datenanbieter freigeben möchte, sind Rohdaten, z.B. Point-of-Sale- oder Zeitreihendaten. Ein Datenanbieter kann auch vorverarbeitete, kuratierte Daten freigeben, die bereits Analysen und Erkenntnisse beinhalten. 

## <a name="data-consumer"></a>Datenconsumer 

Ein Datenconsumer ist die Organisation, die Daten von einem Datenanbieter empfängt. Der Datenconsumer möchte möglicherweise die freigegebenen Daten mit seinen eigenen Daten verknüpfen, um Erkenntnisse zu gewinnen. In einigen Fällen kann der Datenconsumer Daten empfangen, die bereits verarbeitet wurden. 

## <a name="data-share"></a>Datenfreigabe

Eine Datenfreigabe ist eine Gruppe von Datasets, die als einzelne Entität freigegeben werden. Die Datasets können aus einer Reihe von Azure-Datenquellen stammen, die von Azure Data Share unterstützt werden. Zurzeit unterstützt Azure Data Share Azure Blob Storage und Azure Data Lake Store. 

## <a name="share-subscription"></a>Freigabeabonnement 

Ein Freigabenabonnement wird erstellt, wenn ein Datenconsumer eine Datenfreigabeeinladung von einem Datenanbieter annimmt. Datenanbieter können aktive Freigabeabonnements anzeigen, indem Sie zu **Gesendete Freigaben** in ihrem Azure Data Share-Konto navigieren und **Freigabeabonnements** auswählen.

Ein Datenconsumer kann überprüfen, ob er über ein aktives Freigabeabonnement verfügt, indem er zu **Empfangene Freigaben** navigiert und den Status seiner empfangenen Freigaben anzeigt. 

## <a name="snapshot"></a>Momentaufnahme

Eine Momentaufnahme kann von einem Datenconsumer erstellt werden, wenn er eine Datenfreigabeeinladung annimmt. Wenn er eine Einladung angenommen hat, kann er eine vollständige Momentaufnahme der Daten auslösen, die für ihn freigegeben wurden. Die Momentaufnahme ist eine Kopie der Daten zu dem Zeitpunkt, zu dem der Datenconsumer die Momentaufnahme generiert hat. 

Es gibt zwei Arten von Momentaufnahmen: vollständige und inkrementelle. Eine vollständige Momentaufnahme enthält alle Daten in der Datenfreigabe. Eine inkrementelle Momentaufnahme enthält alle Daten, die seit der Auslösung der letzten Momentaufnahme aktualisiert/hinzugefügt wurden. 

## <a name="snapshot-settings-in-azure-data-share"></a>Momentaufnahmeeinstellungen in Azure Data Share
 
Ein Datenanbieter kann eine Momentaufnahmeeinstellung für eine Dateifreigabe aktivieren. Diese Einstellung ermöglicht es dem Datenconsumer, inkrementelle Aktualisieren zu empfangen, wenn sie auftreten. Diese Einstellung sollte aktiviert werden, wenn der Datenanbieter möchte, dass seine Datenconsumer Aktualisierungen der Daten empfangen, die freigegeben wurden. 

Wenn ein Datenanbieter diese Einstellung aktiviert, kann ein Wiederholungsintervall ausgewählt werden. Das Wiederholungsintervall kann stündlich oder täglich sein. 

Ein Datenconsumer hat die Möglichkeit, diesen Momentaufnahme-Zeitplan zu abonnieren, um inkrementelle Aktualisierungen zu erhalten, die alle Daten beinhalten, die sich geändert haben, seit er zum ersten Mal eine neue Momentaufnahme generiert hat. 

## <a name="invitation"></a>Einladung

Ein Datenanbieter kann mehrere Empfängern zu seiner Datenfreigabe einladen. Er kann zu diesem Zweck der Datenfreigabe Empfänger hinzufügen. Einladungen können auch hinzugefügt werden, nachdem eine Datenfreigabe erstellt wurde. 

Ein Datenanbieter kann eine Einladung löschen, nachdem sie gesendet wurde. Beachten Sie Folgendes: Wenn ein Datenanbieter eine Einladung löscht, nachdem sie angenommen wurde, kann der Dataconsumer noch immer über ein aktives Freigabeabonnement verfügen. Wenn der Datenanbieter eine Einladung löscht, die noch nicht angenommen wurde, ist der Datenconsumer nicht mehr in der Lage, die Einladung zu akzeptieren. 

## <a name="recipient"></a>Recipient

Ein Empfänger ist eine Person, die eine Einladung für eine Datenfreigabe empfängt. In der Regel wird ein Datenanbieter einer Datenfreigabe Empfänger hinzufügen, die er erstellt. Sobald der Empfänger einer Einladung die Einladung annimmt, wird er zu einem Datenconsumer.  

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Freigeben von Daten finden Sie im Tutorial zum [Freigeben Ihrer Daten](share-your-data.md).

