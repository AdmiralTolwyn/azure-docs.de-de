---
title: Stream Analytics Data Lake Store-Ausgabe | Microsoft-Dokumente
description: Konfigurieren der Authentifizierung und Autorisierung eines Azure Data Lake-Speichers in einem Stream Analytics-Auftrag
keywords: 
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: ea5baafa-0054-4c70-973a-6a3a8c6eaffc
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: e2010e86e56c1ce7a98fae97a8f6f00c30b61035
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2017
---
# <a name="stream-analytics-data-lake-store-output"></a>Stream Analytics Data Lake-Speicherausgabe
Stream Analytics-Aufträge unterstützen mehrere Ausgabemethoden, darunter auch [Azure Data Lake-Speicher](https://azure.microsoft.com/services/data-lake-store/). Azure Data Lake-Speicher ist ein unternehmensweites riesiges Repository für Big Data-Analyseworkloads. Data Lake-Speicher ermöglicht es Ihnen, Daten von beliebiger Größe, Art und Erfassungsgeschwindigkeit zur Durchführung operativer und explorativer Analysen zu speichern.

## <a name="authorize-a-data-lake-store-account"></a>Autorisieren eines Data Lake-Speicherkontos
1. Wenn Data Lake Store als Ausgabe im Azure-Portal ausgewählt ist, werden Sie aufgefordert, die Verwendung Ihrer vorhandenen Data Lake Store-Instanz zu autorisieren oder Zugriff auf den Data Lake Store anzufordern.
   
   ![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-authorization.png)  
   
2. Falls Sie bereits Zugriff auf Data Lake Store haben, klicken Sie auf „Jetzt autorisieren“. Daraufhin erscheint für einen kurzen Moment eine Seite mit „Umleitung an die Autorisierung...“. Diese Seite wird automatisch geschlossen, und Ihnen wird die Seite zum Konfigurieren der Data Lake-Speicherausgabe angezeigt.

Falls Sie sich nicht für Data Lake Store angemeldet haben, können Sie dem Link „Jetzt registrieren“ folgen, um die Anfrage zu initiieren. Oder Sie folgen den Anweisungen für [erste Schritte](../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="configure-the-data-lake-store-output-properties"></a>Konfigurieren der Data Lake-Speicherausgabeeigenschaften
Sobald Sie das Data Lake-Speicherkonto authentifiziert haben, können Sie die Eigenschaften für die Data Lake-Speicherausgabe konfigurieren. Die folgende Tabelle enthält eine Liste von Eigenschaftennamen und deren Beschreibung zum Konfigurieren der Data Lake-Speicherausgabe.

<table>
<tbody>
<tr>
<td><B>EIGENSCHAFTENNAME</B></td>
<td><B>BESCHREIBUNG</B></td>
</tr>
<tr>
<td>Ausgabealias</td>
<td>Dies ist ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an diesen Data Lake-Speicher weiterzuleiten.</td>
</tr>
<tr>
<td>Data Lake-Speicherkonto</td>
<td>Der Name des Speicherkontos, an das Sie die Ausgabe senden. Es wird eine Liste mit Data Lake Store-Konten angezeigt, auf die der angemeldete Benutzer Zugriff hat.</td>
</tr>
<tr>
<td>Präfixmuster des Pfads [<I>optional</I>]</td>
<td>Der Dateipfad, mit dem Ihre Dateien im angegebenen Data Lake-Speicherkonto geschrieben werden. <BR>{date}, {time}<BR>Beispiel 1: folder1/logs / {date} / {time}<BR>Beispiel 2: folder1/logs / {date}</td>
</tr>
<tr>
<td>Datumsformat [<I>optional</I>]</td>
<td>Wenn das date-Token im Pfadpräfix verwendet wird, können Sie das Datumsformat auswählen, unter dem die Dateien gespeichert werden. Beispiel: YYYY/MM/TT</td>
</tr>
<tr>
<td>Zeitformat [<I>optional</I>]</td>
<td>Wenn das time-Token im Pfadpräfix verwendet wird, können Sie das Zeitformat auswählen, unter dem die Dateien gespeichert werden. Der einzige derzeit unterstützte Wert ist HH</td>
</tr>
<tr>
<td>Ereignisserialisierungsformat</td>
<td>Das Serialisierungsformat für Ausgabedaten. Es werden JSON, CSV und Avro unterstützt.</td>
</tr>
<tr>
<td>Codieren</td>
<td>Beim CSV- oder JSON-Format muss eine Codierung angegeben werden. Das einzige derzeit unterstützte Codierungsformat ist UTF-8.</td>
</tr>
<tr>
<td>Trennzeichen</td>
<td>Gilt nur für die CSV-Serialisierung. Stream Analytics unterstützt eine Reihe von üblichen Trennzeichen zum Serialisieren der CSV-Daten. Unterstützte Werte sind Komma, Semikolon, Leerzeichen, Tabulator und senkrechter Strich.</td>
</tr>
<tr>
<td>Format</td>
<td>Gilt nur für die JSON-Serialisierung. "Separate Zeile" gibt an, dass die Ausgabe so formatiert wird, dass jedes JSON-Objekt in einer neuen Zeile enthalten ist. "Array" gibt an, dass die Ausgabe als Array aus JSON-Objekten formatiert wird.</td>
</tr>
</tbody>
</table>

## <a name="renew-data-lake-store-authorization"></a>Erneuern der Data Lake-Speicherautorisierung
Derzeit besteht die Einschränkung, dass das Authentifizierungstoken alle 90 Tage manuell für sämtliche Aufträge mit der Data Lake-Speicherausgabe aktualisiert werden muss. Darüber hinaus muss Ihr Data Lake-Speicherkonto erneut authentifiziert werden, wenn Sie Ihr Kennwort seit der Erstellung oder letzten Authentifizierung Ihres Auftrags geändert haben. Ein Symptom dieses Problems ist, dass es keine Auftragsausgabe gibt, und ein Fehler in den Vorgangsprotokollen auftritt, der die erforderliche Neuautorisierung anzeigt.

Um dieses Problem zu beheben, halten Sie den laufenden Auftrag an, und wechseln Sie zu Ihrer Data Lake-Speicherausgabe. Klicken Sie auf „Autorisierung erneuern“, und für einen kurzen Zeitraum wird die Seite „Umleitung an die Autorisierung...“ angezeigt. Die Seite wird automatisch geschlossen und im Erfolgsfall wird „Autorisierung wurde erfolgreich erneuert“ angezeigt. Sie müssen dann unten auf der Seite auf „Speichern“ klicken und können fortfahren, indem Sie Ihren Auftrag von der letzten Beendigungszeit aus neu starten, um Datenverlust zu vermeiden.

![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-renew-authorization.png)

