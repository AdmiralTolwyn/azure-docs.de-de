---
title: Lokale Entwicklung mit dem Azure Cosmos DB-Emulator | Microsoft-Dokumentation
description: Mit dem Azure Cosmos DB-Emulator können Sie Ihre Anwendung kostenlos lokal entwickeln und testen, ohne ein Azure-Abonnement zu erstellen.
services: cosmos-db
documentationcenter: ''
keywords: Azure Cosmos DB-Emulator
author: David-Noble-at-work
manager: kfile
editor: ''
ms.assetid: 90b379a6-426b-4915-9635-822f1a138656
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/20/2018
ms.author: danoble
ms.openlocfilehash: 109bd61963b918f2a20c48a5bf7bd89dc353db96
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2018
---
# <a name="use-the-azure-cosmos-db-emulator-for-local-development-and-testing"></a>Verwenden des Azure Cosmos DB-Emulators für lokale Entwicklungs- und Testvorgänge

<table>
<tr>
  <td><strong>Binärdateien</strong></td>
  <td>[MSI herunterladen](https://aka.ms/cosmosdb-emulator)</td>
</tr>
<tr>
  <td><strong>Docker</strong></td>
  <td>[Docker Hub](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/)</td>
</tr>
<tr>
  <td><strong>Docker-Quelle</strong></td>
  <td>[Github](https://github.com/Azure/azure-cosmos-db-emulator-docker)</td>
</tr>
</table>
  
Der Azure Cosmos DB-Emulator stellt eine lokale Umgebung bereit, die zu Entwicklungszwecken den Azure Cosmos DB-Dienst emuliert. Bei Verwendung des Azure Cosmos DB-Emulators können Sie die Anwendung lokal entwickeln und testen, ohne ein Azure-Abonnement zu erstellen oder sonstige Kosten zu verursachen. Wenn Sie mit der Funktion der Anwendung im Azure Cosmos DB-Emulator zufrieden sind, können Sie zur Verwendung eines Azure Cosmos DB-Kontos in der Cloud wechseln.

> [!NOTE]
> Zum aktuellen Zeitpunkt bietet der Daten-Explorer im Emulator nur für SQL-API-Sammlungen und MongoDB-Sammlungen vollständige Unterstützung. Table-, Graph- und Cassandra-Container werden nicht vollständig unterstützt. 

In diesem Artikel werden die folgenden Aufgaben behandelt: 

> [!div class="checklist"]
> * Installieren des Emulators
> * Authentifizieren von Anforderungen
> * Verwenden des Daten-Explorers im Emulator
> * Exportieren von SSL-Zertifikaten
> * Aufrufen des Emulators über die Befehlszeile
> * Ausführen des Emulators unter Docker für Windows
> * Sammeln von Ablaufverfolgungsdateien
> * Problembehandlung

Es wird empfohlen, als Einstieg das folgende Video anzusehen, in dem Kirill Gavrylyuk Ihnen die ersten Schritte mit dem Azure Cosmos DB-Emulator zeigt. Beachten Sie, dass sich das Video auf den DocumentDB-Emulator bezieht, das Tool selbst jedoch nach Aufzeichnung des Videos in Azure Cosmos DB-Emulator umbenannt wurde. Alle Informationen im Video für den Azure Cosmos DB-Emulator sind weiterhin korrekt. 

> [!VIDEO https://channel9.msdn.com/Events/Connect/2016/192/player]
> 
> 

## <a name="how-the-emulator-works"></a>Funktionsweise des Emulators
Der Azure Cosmos DB-Emulator stellt eine High-Fidelity-Emulation des Azure Cosmos DB-Diensts bereit. Er unterstützt die gleichen Funktionen wie Azure Cosmos DB, z.B. Erstellen und Abfragen von JSON-Dokumenten, Bereitstellen und Skalieren von Sammlungen und Ausführen von gespeicherten Prozeduren und Triggern. Sie können Anwendungen mit dem Azure Cosmos DB-Emulator entwickeln und testen und diese in Azure auf globaler Ebene bereitstellen, indem Sie nur eine einzige Konfigurationsänderung am Verbindungsendpunkt für Azure Cosmos DB vornehmen.

Wir haben zwar eine sehr detailgetreue Emulation des tatsächlichen Azure Cosmos DB-Diensts erstellt, allerdings unterscheidet sich die Implementierung des Azure Cosmos DB-Emulators von der des Diensts. Der Azure Cosmos DB-Emulator verwendet Standardkomponenten des Betriebssystems, z.B. das lokale Dateisystem für Persistenz und den HTTPS-Protokollstapel für Konnektivität. Dies bedeutet, dass einige Funktionen, die die Azure-Infrastruktur benötigen, über den Azure Cosmos DB-Emulator nicht verfügbar sind. Hierzu gehören beispielsweise die globale Replikation, Lese-/Schreibvorgänge mit Wartezeit im einstelligen Millisekundenbereich und optimierbare Konsistenzebenen.

## <a name="differences-between-the-emulator-and-the-service"></a>Unterschiede zwischen dem Emulator und dem Dienst 
Da der Azure Cosmos DB-Emulator eine emulierte Umgebung bereitstellt, die auf einer lokalen Entwicklerarbeitsstation ausgeführt wird, gibt es einige funktionelle Unterschiede zwischen dem Emulator und einem Azure Cosmos DB-Konto in der Cloud:

* Der Azure Cosmos DB-Emulator unterstützt nur ein einziges festgelegtes Konto und einen bekannten Hauptschlüssel.  Im Azure Cosmos DB-Emulator ist es nicht möglich, einen Schlüssel neu zu generieren.
* Der Azure Cosmos DB-Emulator ist kein skalierbarer Speicherdienst und unterstützt keine große Anzahl von Sammlungen.
* Mit dem Azure Cosmos DB-Emulator werden keine unterschiedlichen [Azure Cosmos DB-Konsistenzebenen](consistency-levels.md) simuliert.
* Der Azure Cosmos DB-Emulator simuliert keine [Replikation in mehreren Regionen](distribute-data-globally.md).
* Der Azure Cosmos DB-Emulator unterstützt nicht die Außerkraftsetzungen von Dienstkontingenten, die im Azure Cosmos DB-Dienst verfügbar sind (z.B. Dokumentgrößenlimits, größerer partitionierter Sammlungsspeicher).
* Unter Umständen ist Ihre Kopie des Azure Cosmos DB-Emulators nicht auf dem neuesten Stand der Änderungen im Azure Cosmos DB-Dienst. Verwenden Sie daher den [Azure Cosmos DB Capacity Planner](https://www.documentdb.com/capacityplanner) (Azure Cosmos DB-Kapazitätsplaner), um den erforderlichen Produktionsdurchsatz (RUs, Request Units = Anforderungseinheiten) für Ihre Anwendung richtig einzuschätzen.

## <a name="system-requirements"></a>Systemanforderungen
Für den Azure Cosmos DB-Emulator gelten folgende Hardware- und Softwareanforderungen:

* Softwareanforderungen
  * Windows Server 2012 R2, Windows Server 2016 oder Windows 10
*   Minimale Hardwareanforderungen
  * 2 GB RAM
  * 10 GB verfügbarer Festplattenspeicher

## <a name="installation"></a>Installation
Sie können den Azure Cosmos DB-Emulator aus dem [Microsoft Download Center](https://aka.ms/cosmosdb-emulator) herunterladen und installieren. Alternativ können Sie den Emulator auch unter Docker für Windows ausführen. Anweisungen zum Verwenden des Emulators unter Docker für Windows finden Sie unter [Ausführen unter Docker](#running-on-docker). 

> [!NOTE]
> Zum Installieren, Konfigurieren und Ausführen des Azure Cosmos DB-Emulators benötigen Sie Administratorrechte auf dem Computer.

## <a name="running-on-windows"></a>Ausführen unter Windows

Wählen Sie zum Starten des Azure Cosmos DB-Emulators die Schaltfläche „Start“, oder drücken Sie die WINDOWS-TASTE. Beginnen Sie mit der Eingabe von **Azure Cosmos DB-Emulator**, und wählen Sie den Emulator in der Liste mit den Anwendungen aus. 

![Wählen Sie die Schaltfläche „Start“, oder drücken Sie die WINDOWS-TASTE, beginnen Sie mit der Eingabe von **Azure Cosmos DB-Emulator**, und wählen Sie den Emulator aus der Liste mit den Anwendungen aus.](./media/local-emulator/database-local-emulator-start.png)

Wenn der Emulator ausgeführt wird, wird im Infobereich der Windows-Taskleiste ein Symbol angezeigt. ![Benachrichtigung des lokalen Azure Cosmos DB-Emulators in der Taskleiste](./media/local-emulator/database-local-emulator-taskbar.png)

Der Azure Cosmos DB-Emulator wird standardmäßig auf dem lokalen Computer („localhost“) ausgeführt und lauscht auf Port 8081.

Der Azure Cosmos DB-Emulator wird standardmäßig im Verzeichnis `C:\Program Files\Azure Cosmos DB Emulator` installiert. Sie können den Emulator auch über die Befehlszeile starten und beenden. Weitere Informationen finden Sie unter [Referenz zum Befehlszeilentool](#command-line).

## <a name="start-data-explorer"></a>Starten des Daten-Explorers

Wenn der Azure Cosmos DB-Emulator gestartet wird, wird er in Ihrem Browser automatisch im Azure Cosmos DB-Daten-Explorer geöffnet. Die Adresse wird als [https://localhost:8081/_explorer/index.html](https://localhost:8081/_explorer/index.html) angezeigt. Wenn Sie den Explorer schließen und ihn später erneut öffnen möchten, können Sie entweder die URL in Ihrem Browser öffnen oder den Explorer über den Azure Cosmos DB-Emulator im Windows-Taskleistensymbol starten. Dies ist unten dargestellt.

![Startprogramm für den Daten-Explorer des lokalen Azure Cosmos DB-Emulators](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>Überprüfen auf Updates
Der Daten-Explorer gibt an, ob ein neues Update zum Download zur Verfügung steht. 

> [!NOTE]
> Der Zugriff auf Daten, die in einer bestimmten Version des Azure Cosmos DB-Emulators erstellt wurden, ist bei Verwendung einer anderen Version nicht gewährleistet. Wenn Sie die Daten langfristig beibehalten müssen, wird empfohlen, sie in einem Azure Cosmos DB-Konto zu speichern, nicht im Azure Cosmos DB-Emulator. 

## <a name="authenticating-requests"></a>Authentifizieren von Anforderungen
Genau wie bei Azure Cosmos DB in der Cloud muss jede Anforderung, die Sie für den Azure Cosmos DB-Emulator durchführen, authentifiziert werden. Der Azure Cosmos DB-Emulator unterstützt ein einziges festgelegtes Konto und einen bekannten Authentifizierungsschlüssel für die Authentifizierung per Hauptschlüssel. Dieses Konto und dieser Schlüssel sind die einzigen Anmeldeinformationen, die für den Azure Cosmos DB-Emulator verwendet werden dürfen. Sie lauten wie folgt:

    Account name: localhost:<port>
    Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==

> [!NOTE]
> Der vom Azure Cosmos DB-Emulator unterstützte Hauptschlüssel ist nur für die Verwendung mit dem Emulator gedacht. Es ist nicht möglich, Ihr Azure Cosmos DB-Konto und den dazugehörigen Schlüssel mit dem Azure Cosmos DB-Emulator zu verwenden. 

> [!NOTE] 
> Wenn Sie den Emulator mit der Option „/Key“ gestartet haben, verwenden Sie den generierten Schlüssel anstelle von „C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==“.

Darüber hinaus unterstützt der Azure Cosmos DB-Emulator, genau wie der Azure Cosmos DB-Dienst, nur die sichere Kommunikation per SSL.

## <a name="running-on-a-local-network"></a>Ausführen im lokalen Netzwerk

Sie können den Emulator in einem lokalen Netzwerk ausführen. Um Netzwerkzugriff zu ermöglichen, geben Sie in der [Befehlszeile](#command-line-syntax) „/AllowNetworkAccess“ an, wodurch Sie gleichzeitig auch „/Key=key_string“ oder „/KeyFile=file_name“ angeben müssen. Sie können mit „/GenKeyFile=file_name“ im Vorfeld eine Datei mit einem zufälligen Schlüssel generieren.  Anschließend können Sie diese an „/KeyFile=file_name“ oder „/Key=contents_of_file“ übergeben.

Wenn der Netzwerkzugriff zum ersten Mal bereitgestellt wird, sollte der Benutzer den Emulator herunterfahren und das Datenverzeichnis des Emulators (C:\Benutzer\benutzername\AppData\Local\CosmosDBEmulator) löschen.

## <a name="developing-with-the-emulator"></a>Entwickeln mit dem Emulator
Wenn der Azure Cosmos DB-Emulator auf Ihrem Desktop ausgeführt wird, können Sie ein unterstütztes [Azure Cosmos DB-SDK](sql-api-sdk-dotnet.md) oder die [Azure Cosmos DB-REST-API](/rest/api/cosmos-db/) für die Interaktion mit dem Emulator verwenden. Der Azure Cosmos DB-Emulator enthält auch einen integrierten Daten-Explorer, mit dem Sie Sammlungen für die SQL- und MongoDB-APIs erstellen sowie Dokumente anzeigen und bearbeiten können, ohne Code schreiben zu müssen.   

    // Connect to the Azure Cosmos DB Emulator running locally
    DocumentClient client = new DocumentClient(
        new Uri("https://localhost:8081"), 
        "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

Geben Sie die folgende Verbindungszeichenfolge an, wenn Sie die [Azure Cosmos DB-Protokollunterstützung für MongoDB](mongodb-introduction.md) verwenden:

    mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true

Sie können vorhandene Tools wie [Azure DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio) verwenden, um eine Verbindung mit dem Azure Cosmos DB-Emulator herzustellen. Außerdem können Sie Daten zwischen dem Azure Cosmos DB-Emulator und dem Azure Cosmos DB-Dienst migrieren, indem Sie das [Migrationstool für Azure Cosmos DB-Daten](https://github.com/azure/azure-documentdb-datamigrationtool) verwenden.

> [!NOTE] 
> Wenn Sie den Emulator mit der Option „/Key“ gestartet haben, verwenden Sie den generierten Schlüssel anstelle von „C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==“.

Mithilfe des Azure Cosmos DB-Emulators können Sie standardmäßig bis zu 25 Sammlungen mit einer einzelnen Partition oder eine partitionierte Sammlung erstellen. Weitere Informationen zum Ändern dieses Werts finden Sie unter [Festlegen des PartitionCount-Werts](#set-partitioncount).

## <a name="export-the-ssl-certificate"></a>Exportieren des SSL-Zertifikats

.NET-Sprachen und -Laufzeit verwenden den Windows-Zertifikatspeicher, um sichere Verbindungen mit dem lokalen Azure Cosmos DB-Emulator herzustellen. Andere Sprachen bieten andere Methoden für die Verwaltung und Verwendung von Zertifikaten. Java verwendet einen eigenen [Zertifikatspeicher](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), Python dagegen verwendet [Wrapper für Socketobjekte](https://docs.python.org/2/library/ssl.html).

Um ein Zertifikat zur Verwendung mit Sprachen und Laufzeiten abzurufen, die nicht in den Windows-Zertifikatspeicher integriert sind, müssen Sie dieses mithilfe des Windows-Zertifikat-Managers exportieren. Starten Sie den Manager, indem Sie „certlm.msc“ ausführen oder die Schritt-für-Schritt-Anleitung unter [Exportieren der Azure Cosmos DB-Emulatorzertifikate](./local-emulator-export-ssl-certificates.md) befolgen. Sobald der Zertifikat-Manager ausgeführt wird, öffnen Sie die persönlichen Zertifikate, wie unten gezeigt, und exportieren Sie das Zertifikat mit dem Anzeigenamen „DocumentDBEmulatorCertificate“ als Base64-codierte X.509-CER-Datei.

![SSL-Zertifikat für den lokalen Azure Cosmos DB-Emulator](./media/local-emulator/database-local-emulator-ssl_certificate.png)

Importieren Sie das X.509-Zertifikat in den Java-Zertifikatspeicher, indem Sie die Anweisungen unter [Hinzufügen eines Zertifikats zum Java CA-Zertifikatspeicher](https://docs.microsoft.com/azure/java-add-certificate-ca-store) befolgen. Sobald das Zertifikat in den Zertifikatspeicher importiert wurde, können Java- und MongoDB-Anwendungen eine Verbindung mit dem Azure Cosmos DB-Emulator herstellen.

Beim Herstellen einer Verbindung mit dem Emulator in Python- und Node.js-SDKs ist die SSL-Überprüfung deaktiviert.

## <a id="command-line"></a>Referenz zum Befehlszeilentool
Sie können die Befehlszeile im Installationspfad verwenden, um den Emulator zu starten und zu beenden, Optionen zu konfigurieren und andere Vorgänge auszuführen.

### <a name="command-line-syntax"></a>Befehlszeilensyntax

    CosmosDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/?]

Geben Sie zum Anzeigen der Liste der Optionen an der Eingabeaufforderung `CosmosDB.Emulator.exe /?` ein.

<table>
<tr>
  <td><strong>Option</strong></td>
  <td><strong>Beschreibung</strong></td>
  <td><strong>Befehl</strong></td>
  <td><strong>Argumente</strong></td>
</tr>
<tr>
  <td>[Keine Argumente]</td>
  <td>Startet den Azure Cosmos DB-Emulator mit den Standardeinstellungen.</td>
  <td>CosmosDB.Emulator.exe</td>
  <td></td>
</tr>
<tr>
  <td>[Hilfe]</td>
  <td>Zeigt die Liste mit unterstützten Befehlszeilenargumenten an.</td>
  <td>CosmosDB.Emulator.exe /?</td>
  <td></td>
</tr>
<tr>
  <td>GetStatus</td>
  <td>Ruft den Status des Azure Cosmos DB-Emulators ab. Der Status wird durch den Exitcode angegeben: 1 = wird gestartet, 2 = wird ausgeführt, 3 = beendet. Ein negativer Exitcode gibt an, dass ein Fehler aufgetreten ist. Es wird keine andere Ausgabe erzeugt.</td>
  <td>CosmosDB.Emulator.exe /GetStatus</td>
  <td></td>
<tr>
  <td>Shutdown</td>
  <td>Fährt den Azure Cosmos DB-Emulator herunter.</td>
  <td>CosmosDB.Emulator.exe /Shutdown</td>
  <td></td>
</tr>
<tr>
  <td>DataPath</td>
  <td>Gibt den Pfad an, unter dem Datendateien gespeichert werden sollen. Der Standardwert ist „%LocalAppdata%\CosmosDBEmulator“.</td>
  <td>CosmosDB.Emulator.exe /DataPath=&lt;datapath&gt;</td>
  <td>&lt;datapath&gt;: Ein zugreifbarer Pfad.</td>
</tr>
<tr>
  <td>Port</td>
  <td>Gibt die für den Emulator zu verwendende Portnummer an.  Der Standardwert ist 8081.</td>
  <td>CosmosDB.Emulator.exe /Port=&lt;port&gt;</td>
  <td>&lt;port&gt;: Einzelne Portnummer.</td>
</tr>
<tr>
  <td>MongoPort</td>
  <td>Gibt die für die MongoDB-Kompatibilitäts-API zu verwendende Portnummer an. Der Standardwert ist 10255.</td>
  <td>CosmosDB.Emulator.exe /MongoPort=&lt;mongoport&gt;</td>
  <td>&lt;mongoport&gt;: Einzelne Portnummer.</td>
</tr>
<tr>
  <td>DirectPorts</td>
  <td>Gibt die für die direkte Konnektivität zu verwendenden Ports an. Die Standardwerte sind 10251, 10252, 10253, 10254.</td>
  <td>CosmosDB.Emulator.exe /DirectPorts:&lt;directports&gt;</td>
  <td>&lt;directports&gt;: Eine durch Trennzeichen getrennte Liste mit 4 Ports.</td>
</tr>
<tr>
  <td>Schlüssel</td>
  <td>Autorisierungsschlüssel für den Emulator. Der Schlüssel muss die Base64-Codierung eines 64-Byte-Vektors sein.</td>
  <td>CosmosDB.Emulator.exe /Key:&lt;key&gt;</td>
  <td>&lt;key&gt;: Der Schlüssel muss die Base64-Codierung eines 64-Bytes-Vektors sein.</td>
</tr>
<tr>
  <td>EnableRateLimiting</td>
  <td>Gibt an, dass das Verhalten für die Anforderungsratenbegrenzung aktiviert ist.</td>
  <td>CosmosDB.Emulator.exe /EnableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>DisableRateLimiting</td>
  <td>Gibt an, dass das Verhalten für die Anforderungsratenbegrenzung deaktiviert ist.</td>
  <td>CosmosDB.Emulator.exe /DisableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>NoUI</td>
  <td>Die Emulator-Benutzeroberfläche wird nicht angezeigt.</td>
  <td>CosmosDB.Emulator.exe /NoUI</td>
  <td></td>
</tr>
<tr>
  <td>NoExplorer</td>
  <td>Der Daten-Explorer wird beim Start nicht angezeigt.</td>
  <td>CosmosDB.Emulator.exe /NoExplorer</td>
  <td></td>
</tr>
<tr>
  <td>PartitionCount</td>
  <td>Legt die maximale Anzahl von partitionierten Sammlungen fest. Weitere Informationen finden Sie unter [Ändern der Anzahl von Sammlungen](#set-partitioncount).</td>
  <td>CosmosDB.Emulator.exe /PartitionCount=&lt;partitioncount&gt;</td>
  <td>&lt;partitioncount&gt;: Maximale Anzahl der zulässigen Sammlungen mit nur einer Partition. Der Standardwert ist 25. Der zulässige Höchstwert beträgt 250.</td>
</tr>
<tr>
  <td>DefaultPartitionCount</td>
  <td>Gibt die Standardanzahl von Partitionen für eine partitionierte Sammlung an.</td>
  <td>CosmosDB.Emulator.exe /DefaultPartitionCount=&lt;defaultpartitioncount&gt;</td>
  <td>&lt;defaultpartitioncount&gt; Der Standardwert ist 25.</td>
</tr>
<tr>
  <td>AllowNetworkAccess</td>
  <td>Ermöglicht Zugriff auf den Emulator über ein Netzwerk. Sie müssen auch „/Key=&lt;key_string&gt;“ oder „/KeyFile=&lt;file_name&gt;“ übergeben, um Netzwerkzugriff zu ermöglichen.</td>
  <td>CosmosDB.Emulator.exe /AllowNetworkAccess /Key=&lt;key_string&gt;<br><br>oder<br><br>CosmosDB.Emulator.exe /AllowNetworkAccess /KeyFile=&lt;file_name&gt;</td>
  <td></td>
</tr>
<tr>
  <td>NoFirewall</td>
  <td>Firewallregeln nicht anpassen, wenn „/AllowNetworkAccess“ verwendet wird.</td>
  <td>CosmosDB.Emulator.exe /NoFirewall</td>
  <td></td>
</tr>
<tr>
  <td>GenKeyFile</td>
  <td>Generiert einen neuen Autorisierungsschlüssel und speichert ihn in der angegebenen Datei. Der generierte Schlüssel kann mit den Optionen „/Key“ oder „/KeyFile“ verwendet werden.</td>
  <td>CosmosDB.Emulator.exe  /GenKeyFile=&lt;Pfad zur Schlüsseldatei&gt;</td>
  <td></td>
</tr>
<tr>
  <td>Konsistenz</td>
  <td>Legt die Standardkonsistenzebene des Kontos fest.</td>
  <td>CosmosDB.Emulator.exe /Consistency=&lt;consistency&gt;</td>
  <td>&lt;Konsistenz&gt;: Der Wert muss eine der folgenden [Konsistenzebenen](consistency-levels.md) aufweisen: „Sitzung“, „stark“, „letztlich“ oder „begrenzte Veraltung“.  Der Standardwert lautet „Sitzung“.</td>
</tr>
<tr>
  <td>?</td>
  <td>Zeigt die Hilfemeldung an.</td>
  <td></td>
  <td></td>
</tr>
</table>

## <a id="set-partitioncount"></a>Ändern der Anzahl von Sammlungen

Mithilfe des Azure Cosmos DB-Emulators können Sie standardmäßig bis zu 25 Sammlungen mit einer einzelnen Partition oder eine partitionierte Sammlung erstellen. Wenn Sie den Wert **PartitionCount** ändern, können Sie bis zu 250 Sammlungen mit nur einer Partition oder 10 partitionierte Sammlungen oder eine Kombination aus beidem erstellen, bei der insgesamt maximal 250 einzelne Partitionen vorhanden sind (wobei 1 partitionierte Sammlung = 25 Sammlungen mit einer Partition).

Wenn Sie versuchen, eine Sammlung zu erstellen, wenn die aktuelle Anzahl von Partitionen überschritten wurde, löst der Emulator eine ServiceUnavailable-Ausnahme mit der folgenden Meldung aus.

    Sorry, we are currently experiencing high demand in this region, 
    and cannot fulfill your request at this time. We work continuously 
    to bring more and more capacity online, and encourage you to try again. 
    Please do not hesitate to email askcosmosdb@microsoft.com at any time or
    for any reason. ActivityId: 29da65cc-fba1-45f9-b82c-bf01d78a1f91

Gehen Sie wie folgt vor, um die Anzahl von Sammlungen zu ändern, die für den Azure Cosmos DB-Emulator verfügbar sind:

1. Löschen Sie alle lokalen Azure Cosmos DB-Emulatordaten, indem Sie in der Taskleiste mit der rechten Maustaste auf das Symbol **Azure Cosmos DB-Emulator** klicken, und klicken Sie anschließend auf **Reset Data…** (Daten zurücksetzen...).
2. Löschen Sie alle Emulatordaten in diesem Ordner: „C:\Benutzer\Benutzername\AppData\Local\CosmosDBEmulator“.
3. Schließen Sie alle geöffneten Instanzen, indem Sie mit der rechten Maustaste auf der Taskleiste auf das Symbol **Azure Cosmos DB-Emulator** klicken, und klicken Sie anschließend auf **Beenden**. Bis alle Instanzen beendet wurden, kann unter Umständen eine Minute vergehen.
4. Installieren Sie die neueste Version des [Azure Cosmos DB-Emulators](https://aka.ms/cosmosdb-emulator).
5. Starten Sie den Emulator mit dem PartitionCount-Flag, indem Sie einen Wert <= 250 festlegen. Beispiel: `C:\Program Files\Azure CosmosDB Emulator>CosmosDB.Emulator.exe /PartitionCount=100`.

## <a name="controlling-the-emulator"></a>Steuern des Emulators

Der Emulator bietet ein PowerShell-Modul für das Starten, Beenden, Deinstallieren und Abrufen des Status des Diensts. Gehen Sie zur Verwendung wie folgt vor:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

oder legen Sie das `PSModules`-Verzeichnis auf `PSModulesPath`, und importieren Sie es wie folgt:

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Hier sehen Sie eine Zusammenfassung der Befehle zum Steuern des Emulators über PowerShell:

### `Get-CosmosDbEmulatorStatus`

#### <a name="syntax"></a>Syntax

`Get-CosmosDbEmulatorStatus`

#### <a name="remarks"></a>Anmerkungen

Gibt einen dieser ServiceControllerStatus-Werte aus: ServiceControllerStatus.StartPending, ServiceControllerStatus.Running oder ServiceControllerStatus.Stopped.

### `Start-CosmosDbEmulator`

#### <a name="syntax"></a>Syntax

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>]  [<CommonParameters>]`

#### <a name="remarks"></a>Anmerkungen

Startet den Emulator. Standardmäßig wartet der Befehl, bis der Emulator bereit ist, Anforderungen entgegenzunehmen. Verwenden Sie die Option -NoWait, wenn Sie möchten, dass das Cmdlet zurückgegeben wird, sobald der Emulator gestartet wurde.

### `Stop-CosmosDbEmulator`

#### <a name="syntax"></a>Syntax

 `Stop-CosmosDbEmulator [-NoWait]`

#### <a name="remarks"></a>Anmerkungen

Beendet den Emulator. Standardmäßig wartet dieser Befehl, bis der Emulator vollständig heruntergefahren ist. Verwenden Sie die Option -NoWait, wenn Sie möchten, dass das Cmdlet zurückgegeben wird, sobald der Emulator heruntergefahren wird.

### `Uninstall-CosmosDbEmulator`

#### <a name="syntax"></a>Syntax

`Uninstall-CosmosDbEmulator [-RemoveData]`

#### <a name="remarks"></a>Anmerkungen

Deinstalliert den Emulator und entfernt optional den gesamten Inhalt von $env:LOCALAPPDATA\CosmosDbEmulator.
Das Cmdlet stellt sicher, dass der Emulator vor der Deinstallation beendet wird.

## <a name="running-on-docker"></a>Ausführen unter Docker

Der Azure Cosmos DB-Emulator kann unter Docker für Windows ausgeführt werden. Der Emulator funktioniert nicht unter Docker für Oracle Linux.

Nachdem [Docker für Windows](https://www.docker.com/docker-windows) installiert ist, wechseln Sie zu den Windows-Containern, indem Sie mit der rechten Maustaste auf der Symbolleiste auf das Docker-Symbol klicken und **Switch to Windows containers** (Zu Windows-Containern wechseln) auswählen.

Führen Sie als Nächstes einen Pullvorgang für das Emulator-Image im Docker-Hub aus, indem Sie in Ihrer bevorzugten Shell den folgenden Befehl ausführen.

```     
docker pull microsoft/azure-cosmosdb-emulator 
```
Führen Sie die folgenden Befehle aus, um das Image zu starten:

Über die Befehlszeile:
```cmd 
md %LOCALAPPDATA%\CosmosDBEmulatorCert 2>null
docker run -v %LOCALAPPDATA%\CosmosDBEmulatorCert:c:\CosmosDBEmulator\CosmosDBEmulatorCert -P -t -i -m 2GB microsoft/azure-cosmosdb-emulator 
```

Über PowerShell:
```powershell
md $env:LOCALAPPDATA\CosmosDBEmulatorCert 2>null
docker run -v $env:LOCALAPPDATA\CosmosDBEmulatorCert:c:\CosmosDBEmulator\CosmosDBEmulatorCert -P -t -i -m 2GB microsoft/azure-cosmosdb-emulator 
```

Die Antwort kann wie folgt aussehen:

```
Starting Emulator
Emulator Endpoint: https://172.20.229.193:8081/
Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
Exporting SSL Certificate
You can import the SSL certificate from an administrator command prompt on the host by running:
cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
--------------------------------------------------------------------------------------------------
Starting interactive shell
``` 

Verwenden Sie jetzt den Endpunkt- und den Hauptschlüssel aus der Antwort im Client, und importieren Sie das SSL-Zertifikat in den Host. Führen Sie an einer Eingabeaufforderung mit Administratorberechtigungen Folgendes aus, um das SSL-Zertifikat zu importieren:

Über die Befehlszeile:
```cmd 
cd %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
```

Über PowerShell:
```powershell
cd $env:LOCALAPPDATA\CosmosDBEmulatorCert
.\importcert.ps1
```

Wenn Sie die interaktive Shell nach dem Starten des Emulators schließen, wird der Container des Emulators heruntergefahren.

Navigieren Sie in Ihrem Browser zu folgender URL, um den Daten-Explorer zu öffnen. Der Emulator-Endpunkt wird in der oben gezeigten Antwortnachricht bereitgestellt.

    https://<emulator endpoint provided in response>/_explorer/index.html


## <a name="troubleshooting"></a>Problembehandlung

Verwenden Sie die folgenden Tipps zum Behandeln von Problemen, die für den Azure Cosmos DB-Emulator auftreten:

- Wenn Sie eine neue Version des Emulators installiert haben und Fehler auftreten, setzen Sie Ihre Daten zurück. Klicken Sie hierzu mit der rechten Maustaste auf der Taskleiste auf das Symbol „Azure Cosmos DB-Emulator“ und anschließend auf „Daten zurücksetzen“. Wenn die Fehler hierdurch nicht behoben werden, kann die App nicht deinstalliert und neu installiert werden. Anweisungen finden Sie unter [Deinstallieren des lokalen Emulators](#uninstall).

- Bei einem Absturz des Azure Cosmos DB-Emulators sammeln Sie Dumpdateien aus dem Ordner „C:\Benutzer\<Benutzername>\AppData\Lokal\CrashDumps“, komprimieren sie und fügen sie einer E-Mail an [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) als Anlage hinzu.

- Wenn Abstürze von „CosmosDB.StartupEntryPoint.exe“ auftreten, führen Sie den folgenden Befehl an einer Administratoreingabeaufforderung aus: `lodctr /R` 

- Wenn ein Verbindungsproblem auftritt, [sammeln Sie Ablaufverfolgungsdateien](#trace-files), komprimieren sie und fügen sie einer E-Mail an [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) als Anhang hinzu.

- Wenn die Meldung **Dienst nicht verfügbar** angezeigt wird, bedeutet das, dass der Emulator den Netzwerkstapel möglicherweise nicht initialisieren kann. Überprüfen Sie, ob Sie den sicheren Pulse-Client oder den Juniper Networks-Client installiert haben. Die zugehörigen Treiber für Netzwerkfilter könnten das Problem verursachen. Durch Deinstallieren der Netzwerkfiltertreiber von Drittanbietern wird das Problem in der Regel behoben.

### <a id="trace-files"></a>Sammeln von Ablaufverfolgungsdateien

Zum Sammeln von Debugablaufverfolgungen führen Sie die folgenden Befehle an einer Administratoreingabeaufforderung aus:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `CosmosDB.Emulator.exe /shutdown`(Fixierte Verbindung) festgelegt ist(Fixierte Verbindung) festgelegt ist. Beobachten Sie die Taskleiste, um sicherzugehen, dass das Programm beendet wurde; dies kann eine Minute dauern. Sie können auch einfach auf der Benutzeroberfläche des Azure Cosmos DB-Emulators auf **Beenden** klicken.
3. `CosmosDB.Emulator.exe /starttraces`
4. `CosmosDB.Emulator.exe`
5. Reproduzieren des Problems Wenn der Daten-Explorer nicht funktioniert, müssen Sie nur einige Sekunden warten, bis der Browser geöffnet wird, um den Fehler zu erfassen.
5. `CosmosDB.Emulator.exe /stoptraces`
6. Navigieren Sie zu `%ProgramFiles%\Azure Cosmos DB Emulator`, und suchen Sie die Datei „docdbemulator_000001.etl“.
7. Senden die ETL-Datei zusammen mit Reproduktionsschritten zum Debuggen an [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com).

### <a id="uninstall"></a>Deinstallieren des lokalen Emulators

1. Schließen Sie alle geöffneten Instanzen des lokalen Emulators, indem Sie mit der rechten Maustaste auf der Taskleiste auf das Symbol „Azure Cosmos DB-Emulator“ klicken, und klicken Sie anschließend auf „Beenden“. Bis alle Instanzen beendet wurden, kann unter Umständen eine Minute vergehen.
2. Geben Sie in das Windows-Suchfeld **Apps & Features** ein, und klicken Sie auf das Ergebnis **Apps & Features (Systemeinstellungen)**.
3. Scrollen Sie in der Liste der Apps zu **Azure Cosmos DB-Emulator**, wählen ihn aus, und klicken Sie auf **Deinstallieren**. Bestätigen Sie den Vorgang, und klicken Sie erneut auf **Deinstallieren**.
4. Wenn die App deinstalliert wird, navigieren Sie zu „C:\Users\<Benutzer > \AppData\Local\CosmosDBEmulator“, und löschen Sie den Ordner. 

## <a name="change-list"></a>Änderungsliste

Sie können die Versionsnummer überprüfen, indem Sie mit der rechten Maustaste auf das lokale Emulatorsymbol in der Taskleiste und auf das Menüelement „Info“ klicken.

### <a name="1220-released-on-april-20-2018"></a>1.22.0. Veröffentlicht am 20. April 2018

Zusätzlich zum Aktualisieren von Emulatordiensten für die Parität mit Cosmos DB-Clouddiensten haben wir eine verbesserte PowerShell-Dokumentation und verschiedene Fehlerbehebungen mit einbezogen.

### <a name="12106-released-on-march-27-2018"></a>1.21.0.6, veröffentlicht am 27. März 2018

Zusätzlich zum Aktualisieren von Emulatordiensten für die Parität mit Cosmos DB-Clouddiensten haben wir ein neues Feature und zwei Fehlerbehebungen in diesem Release mit einbezogen.

#### <a name="features"></a>Features

1. Der Start-CosmosDbEmulator-Befehl enthält jetzt Startoptionen.

#### <a name="bug-fixes"></a>Fehlerbehebungen

1. Das Microsoft.Azure.CosmosDB.Emulator-PowerShell-Modul stellt nun sicher, dass die `ServiceControllerStatus`-Enumeration geladen wird.

2. Das Microsoft.Azure.CosmosDB.Emulator-PowerShell-Modul enthält nun ein Manifest; dies wurde im ersten Release ausgelassen.

### <a name="1201084-released-on-february-14-2018"></a>1.20.108.4, freigegeben am 14. Februar 2018

In dieser Version gibt es ein neues Feature und zwei Fehlerbehebungen. Vielen Dank an die Kunden, die uns geholfen haben, diese Probleme zu finden und zu beheben.

#### <a name="bug-fixes"></a>Fehlerbehebungen

1. Der Emulator funktioniert jetzt auf Computern mit einem Kern oder zwei Kernen (oder virtuellen CPUs).

   Cosmos DB weist Aufgaben zu, um verschiedene Dienste auszuführen. Die Anzahl der zugewiesenen Aufgaben ist ein Vielfaches der Anzahl von Kernen auf einem Host. Das Standardvielfache ist gut für Produktionsumgebungen geeignet, in denen viele Kerne vorhanden sind. Auf Computern mit höchstens zwei Prozessoren werden jedoch keine Aufgaben zugewiesen, um diese Dienste auszuführen, wenn dieses Vielfache angewendet wird.

   Dies wurde korrigiert, indem ein Feature zur Außerkraftsetzung der Konfiguration zum Emulator hinzugefügt wurde. Es wird jetzt ein Vielfaches von 1 angewendet. Die Anzahl der Aufgaben, die für die Ausführung verschiedener Dienste zugewiesen wurden, entspricht nun der Anzahl der Kerne auf einem Host.

   Wenn wir nichts anderes für diese Version getan hätten, dann hätten wir dieses Problem gelöst. Viele Entwicklungs-/Testumgebungen, die den Emulator hosten, haben einen Kern oder zwei Kerne.

2. Es ist nicht mehr erforderlich, für den Emulator Microsoft Visual C++ 2015 Redistributable zu installieren.

   Neuinstallationen von Windows (Desktop- und Server-Editionen) enthalten dieses weiterverteilbare Paket nicht. Daher sind die weiterverteilbaren Binärdateien nun direkt im Emulator enthalten.

#### <a name="features"></a>Features

Viele der befragten Kunden gaben an, dass es von Vorteil wäre, wenn der Emulator skriptfähig wäre. Daher haben wir in dieser Version einige Skriptfunktionen hinzugefügt. Der Emulator enthält nun ein PowerShell-Modul zum Starten, Beenden und Abrufen des Status sowie, um sich selbst zu deinstallieren: `Microsoft.Azure.CosmosDB.Emulator`. 

### <a name="120911-released-on-january-26-2018"></a>1.20.91.1, veröffentlicht am 26. Januar 2018

* Die Pipeline für die MongoDB-Aggregation wurde standardmäßig aktiviert.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie die folgenden Aufgaben ausgeführt:

> [!div class="checklist"]
> * Installieren des lokalen Emulators
> * Ausführen des Emulators unter Docker für Windows
> * Authentifizieren von Anforderungen
> * Verwenden des Daten-Explorers im Emulator
> * Exportieren von SSL-Zertifikaten
> * Aufrufen des Emulators über die Befehlszeile
> * Sammeln von Ablaufverfolgungsdateien

In diesem Tutorial haben Sie erfahren, wie Sie den lokalen Emulator für die kostenlose lokale Entwicklung verwenden. Sie können nun mit dem nächsten Tutorial fortfahren und sich darüber informieren, wie Sie SSL-Zertifikate für den Emulator exportieren. 

> [!div class="nextstepaction"]
> [Exportieren der Azure Cosmos DB-Emulatorzertifikate](local-emulator-export-ssl-certificates.md)
