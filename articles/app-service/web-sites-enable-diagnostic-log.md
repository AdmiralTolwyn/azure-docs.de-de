---
title: "Aktivieren der Diagnoseprotokollierung für Web-Apps in Azure App Service"
description: "Erfahren Sie, wie Sie die Diagnoseprotokollierung aktivieren und Instrumentierung zu Ihrer Anwendung hinzufügen und wie Sie auf die von Azure protokollierten Informationen zugreifen."
services: app-service
documentationcenter: .net
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2016
ms.author: cephalin
ms.openlocfilehash: 1366cd79248b2e0008234a5da0d87552e6530d80
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2017
---
# <a name="enable-diagnostics-logging-for-web-apps-in-azure-app-service"></a>Aktivieren der Diagnoseprotokollierung für Web-Apps in Azure App Service
## <a name="overview"></a>Übersicht
Azure bietet integrierte Diagnosefunktionen zur Unterstützung beim Debuggen einer [App Service-Web-App](http://go.microsoft.com/fwlink/?LinkId=529714). In diesem Artikel erfahren Sie, wie Sie die Diagnoseprotokollierung aktivieren und Ihrer Anwendung Instrumentierung hinzufügen sowie wie Sie die von Azure protokollierten Informationen abrufen.

In diesem Artikel wird die Verwendung von Diagnoseprotokollen mit dem [Azure-Portal](https://portal.azure.com), Azure PowerShell und der Azure-Befehlszeilenschnittstelle (Azure-CLI) beschrieben. Informationen zum Arbeiten mit Diagnoseprotokollen in Visual Studio finden Sie unter [Problembehandlung von Azure in Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="whatisdiag"></a>Webserver- und Anwendungsdiagnose
App Service-Web-Apps bieten Diagnosefunktionen zum Protokollieren von Informationen sowohl über den Webserver als auch über die Webanwendung. Diese sind logisch in **Webserverdiagnose** und **Anwendungsdiagnose** unterteilt.

### <a name="web-server-diagnostics"></a>Webserverdiagnose
Sie können die folgenden Protokollarten aktivieren oder deaktivieren:

* **Detaillierte Fehlerprotokollierung** - Detaillierte Fehlerinformationen für HTTP-Statuscodes, die auf einen Fehler hinweisen (Statuscode 400 oder höher). Diese können Informationen enthalten, mit deren Hilfe sich bestimmen lässt, warum der Server den Fehlercode zurückgegeben hat.
* **Ablaufverfolgung von Anforderungsfehlern** - Detaillierte Informationen zu fehlgeschlagenen Anforderungen, einschließlich der Verfolgung von IIS-Komponenten, die zur Verarbeitung der Anforderung verwendet wurden, sowie die in jeder Komponente benötigte Zeit. Dies kann nützlich sein, wenn Sie versuchen, die Leistung von Websites zu verbessern oder herauszufinden, warum ein bestimmter HTTP-Fehler zurückgegeben wird.
* **Webserverprotokollierung** - Informationen über HTTP-Transaktionen im erweiterten [W3C-Protokolldateiformat](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Dies ist hilfreich, wenn Sie allgemeine Website-Kennzahlen ermitteln möchten, wie die Anzahl der verarbeiteten Anfragen oder wie viele Anforderungen von einer bestimmten IP-Adresse stammen.

### <a name="application-diagnostics"></a>Anwendungsdiagnose
Mit der Option "Application Diagnostics" können Sie die von einer Webanwendung erzeugten Informationen erfassen. ASP.NET-Anwendungen können die Klasse [System.Diagnostics.Trace](http://msdn.microsoft.com/library/36hhw2t6.aspx) verwenden, um Informationen im Anwendungs-Diagnoseprotokoll aufzuzeichnen. Beispiel:

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

Sie können diese Protokolle zur Laufzeit abrufen und bei der Problembehandlung nutzen. Weitere Informationen finden Sie unter [Problembehandlung von Azure-Web-Apps in Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

App Service-Web-Apps protokollieren auch Bereitstellungsinformationen, wenn Sie Inhalte in einer Web-App veröffentlichen. Dies erfolgt automatisch, und es gibt keine Konfigurationseinstellung für die Bereitstellungsprotokollierung. Anhand der Bereitstellungsprotokollierung können Sie bestimmen, warum eine Bereitstellung fehlgeschlagen ist. Wenn Sie beispielsweise ein benutzerdefiniertes Bereitstellungsskript verwenden, können Sie die Bereitstellungsprotokollierung verwenden, um festzustellen, warum das Skript fehlgeschlagen ist.

## <a name="enablediag"></a>Aktivieren der Diagnose
Zum Aktivieren der Diagnose im [Azure-Portal](https://portal.azure.com) navigieren Sie zum Blatt für Ihre Web-App und klicken auf **Einstellungen > Diagnoseprotokolle**.

<!-- todo:cleanup dogfood addresses in screenshot -->
![Protokolle Teil](./media/web-sites-enable-diagnostic-log/logspart.png)

Wenn Sie die **Anwendungsdiagnose** aktivieren, können Sie auch die **Ebene** auswählen. Diese Einstellung ermöglicht das Filtern der aufgezeichneten Informationen nach **Informationen**, **Warnungen** oder **Fehlern**. Sie können auch **verbose** auswählen, um alle von der Anwendung erzeugten Informationen zu protokollieren.

> [!NOTE]
> Im Gegensatz zum Ändern der Datei "web.config" wird durch das Aktivieren der Anwendungsdiagnose oder durch das Ändern des Diagnoseprotokollumfangs nicht die Anwendungsdomäne neu gestartet, in der die Anwendung ausgeführt wird.
>
>

Im [klassischen Portal](https://manage.windowsazure.com) der Web-App auf der Registerkarte **Konfigurieren** können Sie **Storage** oder **Dateisystem** für die **Webserverprotokollierung** auswählen. Durch Auswahl von **storage** können Sie ein Speicherkonto festlegen und dann einen Blob-Container hinzufügen, in den die Protokolle geschrieben werden. Alle anderen Protokolle für **site diagnostics** werden nur in das Dateisystem geschrieben.

Die Registerkarte [Konfigurieren](https://manage.windowsazure.com) im **klassischen Portal** der Web-App verfügt außerdem über zusätzliche Einstellungen für die Anwendungsdiagnose:

* **Dateisystem** - Speichert die Anwendungsdiagnoseinformationen im Dateisystem der Web-App. Diese Dateien können über FTP aufgerufen werden oder als ZIP-Archiv mit Azure PowerShell oder mit der Azure-Befehlszeilenschnittstelle (Azure-CLI) heruntergeladen werden.
* **Table storage** - Speichert die Anwendungsdiagnoseinformationen im angegebenen Azure-Speicherkonto unter dem Tabellennamen.
* **Blob storage** - Speichert die Anwendungsdiagnoseinformationen im angegebenen Azure-Speicherkonto im Blob-Container.
* **Vermerkungszeitraum:** Standardmäßig werden Protokolle nicht automatisch aus **Blob Storage** gelöscht. Falls die Protokolle automatisch gelöscht werden sollen, wählen Sie **set retention** aus, und geben Sie die Anzahl der Tage ein, für die Protokolle aufbewahrt werden sollen.

> [!NOTE]
> Wenn Sie [den Zugriffsschlüssel für Ihr Speicherkonto neu generieren](../storage/common/storage-create-storage-account.md), müssen Sie die jeweilige Protokollierungskonfiguration zur Verwendung der aktualisierten Schlüssel zurücksetzen. Gehen Sie dazu folgendermaßen vor:
>
> 1. Stellen Sie auf der Registerkarte **Konfigurieren** das jeweilige Protokollierungsfeature auf **Aus**. Speichern Sie die Einstellungen.
> 2. Aktivieren Sie die Protokollierung für den Speicherkonto-Blob oder die -Tabelle erneut. Speichern Sie die Einstellungen.
>
>

Sie können gleichzeitig eine Kombination aus Dateisystem-, Tabellen- und BLOB-Speicher aktivieren und jeweils individuelle Konfigurationen der Protokollierungsebene festlegen. Sie können beispielsweise Fehler und Warnmeldungen als langfristige Protokollierungslösung im Blob-Speicher protokollieren, während die Dateisystemprotokollierung ausführlich erfolgt.

Obwohl alle drei Speicherorte dieselben grundlegenden Informationen zu protokollierten Ereignissen bieten, zeichnen **Table Storage** und **Blob Storage** mehr Informationen, wie Instanz-ID, Thread-ID und einen ausführlicheren Zeitstempel (Tick-Format), als die Protokollierung im **Dateisystem** auf.

> [!NOTE]
> In **Table Storage** oder **Blob Storage** gespeicherte Informationen können nur über Storage-Clients oder Anwendungen aufgerufen werden, die direkt mit diesen Speichersystemen arbeiten. Beispielsweise enthält Visual Studio 2013 einen Speicher-Explorer, mit dem Tabellen- oder Blob-Speicher erkundet werden können, und HDInsight kann auf die im Blob-Speicher gespeicherten Daten zugreifen. Sie können auch mithilfe der [Azure SDKs](/downloads/#)eine Anwendung schreiben, die auf den Azure-Speicher zugreift.
>
> [!NOTE]
> Diagnosen können auch über die Azure PowerShell mit dem Cmdlet **Set-AzureWebsite** aktiviert werden. Wenn Sie Azure PowerShell nicht installiert haben oder nicht zur Verwendung des Azure-Abonnements konfiguriert haben, finden Sie weitere Informationen unter [Verwenden von Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).
>
>

## <a name="download"></a> Vorgehensweise: Herunterladen von Blobs
Im Dateisystem der Web-App gespeicherte Diagnoseinformationen können direkt über FTP aufgerufen werden. Außerdem können sie als ZIP-Archiv mit Azure PowerShell oder mit der Azure-Befehlszeilenschnittstelle (Azure-CLI) heruntergeladen werden.

Protokolle werden in der folgenden Verzeichnisstruktur gespeichert:

* **Anwendungsprotokolle** - /LogFiles/Application/. Dieser Ordner enthält eine oder mehrere Textdateien mit Informationen, die bei der Anwendungsprotokollierung erzeugt wurden.
* **Protokolle für fehlgeschlagene Anforderungen** - /LogFiles/W3SVC#########/. Dieser Ordner enthält eine XSL-Datei und eine oder mehrere XML-Dateien. Vergewissern Sie sich, dass Sie die XSL-Datei in dasselbe Verzeichnis wie die XML-Datei(en) herunterladen, da die XSL-Datei die Funktionalität zum Formatieren und Filtern des Inhalts der XML-Datei(en) zur Anzeige in Internet Explorer zur Verfügung stellt.
* **Detaillierte Fehlerprotokolle** - /LogFiles/DetailedErrors/. Dieser Ordner enthält eine oder mehrere HTM-Dateien, die umfangreiche Informationen zu aufgetretenen HTTP-Fehlern bereitstellen.
* **Webserverprotokolle** - /LogFiles/http/RawLogs. Dieser Ordner enthält eine oder mehrere Textdateien im [erweiterten W3C-Protokolldateiformat](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx).
* **Bereitstellungsprotokolle** - /LogFiles/Git. Dieser Ordner enthält Protokolle, die von den internen Bereitstellungsprozessen der Azure-Web-Apps erzeugt werden, sowie Protokolle für Git-Bereitstellungen.

### <a name="ftp"></a>FTP
Um Diagnoseinformationen über FTP aufzurufen, wechseln Sie im **klassischen Portal** zur Seite [Dashboard](https://manage.windowsazure.com)Ihrer Web-App. Im Abschnitt **Auf einen Blick** können Sie den Link **FTP-Diagnoseprotokolle** verwenden, um über FTP auf die Protokolldateien zuzugreifen. Der Eintrag **Deployment/FTP User** listet den Benutzernamen auf, der zum Zugriff auf die FTP-Site verwendet werden sollte.

> [!NOTE]
> Falls der Eintrag **Bereitstellung/FTP-Benutzer** nicht festgelegt ist oder Sie das Kennwort für diesen Benutzer vergessen haben, können Sie einen neuen Benutzer mit zugehörigem Kennwort über den Link **Anmeldeinformationen für die Bereitstellung zurücksetzen** im Abschnitt **Auf einen Blick** im **Dashboard** erstellen.
>
>

### <a name="download-with-azure-powershell"></a>Herunterladen mit Azure PowerShell
Starten Sie zum Herunterladen der Protokolldateien eine neue Instanz von Azure PowerShell, und führen Sie den folgenden Befehl aus:

    Save-AzureWebSiteLog -Name webappname

Daraufhin werden die Protokolle für die Web-App, die durch den **-Name**-Parameter angegeben wird, in der Datei **logs.zip** im aktuellen Verzeichnis gespeichert.

> [!NOTE]
> Wenn Sie Azure PowerShell nicht installiert haben oder nicht zur Verwendung des Azure-Abonnements konfiguriert haben, finden Sie weitere Informationen unter [Verwenden von Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).
>
>

### <a name="download-with-azure-command-line-interface"></a>Herunterladen mit der Azure-Befehlszeilenschnittstelle
Zum Herunterladen der Protokolldateien mit der Azure-Befehlszeilenschnittstelle öffnen Sie eine neue Eingabeaufforderung, PowerShell-, Bash- oder Terminalsitzung, und geben Sie den folgenden Befehl ein:

    azure site log download webappname

Damit werden die Protokolle für die Web-App namens 'webappname' in der Datei **diagnostics.zip** im aktuellen Verzeichnis gespeichert.

> [!NOTE]
> Wenn Sie die Azure-Befehlszeilenschnittstelle (Azure-CLI) nicht installiert oder nicht für Ihr Azure-Abonnement konfiguriert haben, lesen Sie unter [Installieren der Azure-Befehlszeilenschnittstelle](../cli-install-nodejs.md)weiter.
>
>

## <a name="how-to-view-logs-in-application-insights"></a>Vorgehensweise: Anzeigen von Protokollen in Application Insights
Visual Studio Application Insights bietet Tools zum Filtern und Suchen von Protokollen sowie zu Korrelation der Protokolle mit Anforderungen und anderen Ereignissen.

1. Fügen Sie Ihrem Projekt in Visual Studio Application Insights SDK hinzu.
   * Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie "Application Insights hinzufügen" aus. Sie werden durch Schritte geführt, zu denen auch die Erstellung einer Application Insights-Ressource gehört. [Weitere Informationen](../application-insights/app-insights-asp-net.md)
2. Fügen Sie Ihrem Projekt das Ablaufverfolgungs-Listener-Paket hinzu.
   * Klicken Sie mit der rechten Maustaste auf Ihr Projekt, und wählen Sie "NuGet-Pakete verwalten" aus. Falls die Protokolle automatisch gelöscht werden sollen, wählen Sie `Microsoft.ApplicationInsights.TraceListener` [Weitere Informationen](../application-insights/app-insights-asp-net-trace-logs.md)
3. Laden Sie das Projekt hoch, und führen sie es zum Generieren von Protokolldaten aus.
4. Navigieren Sie im [Azure-Portal](https://portal.azure.com/)zu der neuen Application Insights-Ressource, und öffnen Sie **Suche**. Ihre Protokolldaten werden zusammen mit der Anforderung, Nutzung und sonstiger Telemetrie angezeigt. Einige Telemetrie kann einige Minuten in Anspruch nehmen, bis sie angezeigt wird: Klicken Sie auf "Aktualisieren". [Weitere Informationen](../application-insights/app-insights-diagnostic-search.md)

[Erfahren Sie mehr über die Leistungsüberwachung mit Application Insights](../application-insights/app-insights-azure-web-apps.md)

## <a name="streamlogs"></a> Vorgehensweise: Streaming von Protokollen
Beim Entwickeln einer Anwendung ist es häufig nützlich, Protokollinformationen nahezu in Echtzeit zu sehen. Dies kann durch das Streaming von Protokollinformationen in die Entwicklungsumgebung entweder mit Azure PowerShell oder mit der Azure-Befehlszeilenschnittstelle erfolgen.

> [!NOTE]
> Einige Protokolltypen puffern die Schreibvorgänge in die Protokolldatei, was zu Störereignissen im Stream führen kann. Beispielsweise kann ein Anwendungsprotokolleintrag für den Besuch einer Seite im Stream vor dem zugehörigen HTTP-Protokolleintrag für die Seitenanforderung angezeigt werden.
>
> [!NOTE]
> Das Protokollstreaming zeigt auch Informationen an, die in eine Textdatei im Ordner **D:\\home\\LogFiles\\** geschrieben werden.
>
>

### <a name="streaming-with-azure-powershell"></a>Streaming mit Azure PowerShell
Starten Sie zum Streaming der Protokollinformationen eine neue Instanz von Azure PowerShell, und verwenden Sie den folgenden Befehl:

    Get-AzureWebSiteLog -Name webappname -Tail

Dadurch wird eine Verbindung zu der Web-App hergestellt, die durch den Parameter **-Name** angegeben ist, und das Streaming von Informationen in das PowerShell-Fenster wird gestartet, sobald Protokollereignisse in der Web-App auftreten. Alle Informationen, die in Dateien mit der Erweiterung TXT, LOG oder HTM geschrieben werden, die sich im Verzeichnis "/LogFiles" (d:/home/logfiles) befinden, werden an die lokale Konsole gestreamt.

Um bestimmte Ereignisse wie beispielsweise Fehler zu filtern, verwenden Sie den Parameter **-Message** . Beispiel:

    Get-AzureWebSiteLog -Name webappname -Tail -Message Error

Um bestimmte Protokolltypen wie HTTP zu filtern, verwenden Sie den Parameter **-Path** . Beispiel:

    Get-AzureWebSiteLog -Name webappname -Tail -Path http

Eine Liste der verfügbaren Pfade wird mit dem Parameter "-ListPath" angezeigt.

> [!NOTE]
> Wenn Sie Azure PowerShell nicht installiert haben oder nicht zur Verwendung des Azure-Abonnements konfiguriert haben, finden Sie weitere Informationen unter [Verwenden von Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).
>
>

### <a name="streaming-with-azure-command-line-interface"></a>Streaming mit der Azure-Befehlszeilenschnittstelle
Zum Streaming der Protokollinformationen öffnen Sie eine neue Eingabeaufforderung, PowerShell, Bash- oder Terminalsitzung und geben den folgenden Befehl ein:

    az webapp log tail --name webappname --resource-group myResourceGroup

Dadurch wird eine Verbindung zu der Web-App namens 'webappname' hergestellt, und das Streaming von Informationen in das Fenster wird gestartet, sobald Protokollereignisse in der Web-App auftreten. Alle Informationen, die in Dateien mit der Erweiterung TXT, LOG oder HTM geschrieben werden, die sich im Verzeichnis "/LogFiles" (d:/home/logfiles) befinden, werden an die lokale Konsole gestreamt.

Um bestimmte Ereignisse wie beispielsweise Fehler zu filtern, verwenden Sie den Parameter **--Filter** . Beispiel:

    az webapp log tail --name webappname --resource-group myResourceGroup --filter Error

Um bestimmte Protokolltypen wie HTTP zu filtern, verwenden Sie den Parameter **--Path** . Beispiel:

    az webapp log tail --name webappname --resource-group myResourceGroup --path http

> [!NOTE]
> Wenn Sie die Azure-Befehlszeilenschnittstelle nicht installiert oder nicht für Ihr Azure-Abonnement konfiguriert haben, lesen Sie unter [Installieren der Azure-Befehlszeilenschnittstelle](../cli-install-nodejs.md)weiter.
>
>

## <a name="understandlogs"></a> Vorgehensweise: Verstehen von Diagnoseprotokollen
### <a name="application-diagnostics-logs"></a>Anwendungs-Diagnoseprotokolle
Die Anwendungsdiagnose speichert Informationen in einem bestimmten Format für .NET-Anwendungen, je nachdem, ob Sie Protokolle im Dateisystem, im Tabellenspeicher oder im Blob-Speicher speichern. Der Grunddatensatz ist für alle drei Speichertypen gleich – Datum und Zeit des Ereignisses, Prozess-ID, die das Ereignis erzeugt hat, sowie Ereignistyp (Info, Warnung, Fehler) und Ereignismeldung.

**Dateisystem**

Jede im Dateisystem protokollierte oder per Streaming empfangene Zeile hat das folgende Format:

    {Date}  PID[{process id}] {event type/level} {message}

Beispielsweise würde ein Fehlerereignis in etwa wie folgt aussehen:

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

Bei der Protokollierung im Dateisystem erhalten Sie nur die grundlegendsten Informationen der drei verfügbaren Methoden, nämlich Zeit, Prozess-ID, Ereignistyp und Meldung.

**Table Storage**

Bei der Protokollierung im Tabellenspeicher werden zusätzliche Eigenschaften verwendet, um die Suche nach Daten in der Tabelle zu erleichtern und ausführlichere Informationen zum Ereignis anzugeben. Die folgenden Eigenschaften (Spalten) werden für jedes Element (Zeile) in der Tabelle angegeben.

| Eigenschaftenname | Wert/Format |
| --- | --- |
| PartitionKey |Datum/Zeit des Ereignisses im Format JJJJMMTTHH |
| Zeilenschlüssel |Ein GUID-Wert, der das Element eindeutig identifiziert |
| Zeitstempel |Datum und Zeit des Auftretens des Ereignisses |
| EventTickCount |Datum und Zeit des Auftretens des Ereignisses im Tick-Format (höhere Präzision) |
| ApplicationName |Der Name der Web-App |
| Ebene |Ereignistyp (z. B. Info, Warnung, Fehler) |
| EventId |Die Ereignis-ID<p><p>Bei fehlender Angabe standardmäßig 0 |
| InstanceId |Instanz der Web-App, in der das Ereignis auftrat |
| Pid |Prozess-ID |
| Tid |Thread-ID des Threads, der das Ereignis erzeugt hat |
| Nachricht |Meldung zu den Ereignisdetails |

**Blob Storage**

Bei der Protokollierung in einem Blob-Speicher werden die Daten im CSV-Format (durch Kommas getrennte Werte) gespeichert. Ähnlich wie beim Tabellenspeicher werden zusätzliche Felder protokolliert, um ausführlichere Informationen zum Ereignis anzugeben. Die folgenden Eigenschaften werden für jede Zeile in der CSV-Datei angegeben:

| Eigenschaftenname | Wert/Format |
| --- | --- |
| Datum |Datum und Zeit des Auftretens des Ereignisses |
| Ebene |Ereignistyp (z. B. Info, Warnung, Fehler) |
| ApplicationName |Der Name der Web-App |
| InstanceId |Instanz der Web-App, in der das Ereignis auftrat |
| EventTickCount |Datum und Zeit des Auftretens des Ereignisses im Tick-Format (höhere Präzision) |
| EventId |Die Ereignis-ID<p><p>Bei fehlender Angabe standardmäßig 0 |
| Pid |Prozess-ID |
| Tid |Thread-ID des Threads, der das Ereignis erzeugt hat |
| Nachricht |Meldung zu den Ereignisdetails |

In einem Blob gespeicherte Daten sehen in etwa wie folgt aus:

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [!NOTE]
> Die erste Zeile des Protokolls enthält die Spaltentitel, wie in diesem Beispiel angegeben.
>
>

### <a name="failed-request-traces"></a>Protokolle für fehlgeschlagene Anforderungen
Die Verfolgungsprotokolle für fehlerhafte Anforderungen werden in XML-Dateien mit dem Namen **fr######.xml** gespeichert. Um das Anzeigen der protokollierten Informationen zu erleichtern, wird ein XSL-Stylesheet namens **freb.xsl** im selben Verzeichnis wie die XML-Dateien bereitgestellt. Wenn Sie eine der XML-Dateien in Internet Explorer öffnen, wird das XSL-Stylesheet verwendet, um eine formatierte Ansicht der Verfolgungsinformationen anzuzeigen. Diese sieht in etwa wie folgt aus:

![Anzeige fehlgeschlagener Anforderungen im Browser](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

### <a name="detailed-error-logs"></a>Detaillierte Fehlerprotokolle
Detaillierte Fehlerprotokolle sind HTML-Dokumente, die ausführlichere Informationen zu aufgetretenen HTTP-Fehlern bereitstellen. Da es sich um einfache HTML-Dokumente handelt, können sie in einem Webbrowser angezeigt werden.

### <a name="web-server-logs"></a>Webserverprotokolle
Webserverprotokolle werden im [erweiterten W3C-Protokolldateiformat](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx)gespeichert. Die Informationen können in einem Texteditor gelesen oder mit einem Dienstprogramm wie [Log Parser](http://go.microsoft.com/fwlink/?LinkId=246619)analysiert werden.

> [!NOTE]
> Die von den Azure-Web-Apps erzeugten Protokolle unterstützen nicht die Felder **s-computername**, **s-ip** oder **cs-version**.
>
>

## <a name="nextsteps"></a> Nächste Schritte
* [Überwachen von Web-Apps](web-sites-monitor.md)
* [Problembehandlung von Azure-Web-Apps in Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md)
* [Analyse von Web-App-Protokollen in HDInsight](http://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)

> [!NOTE]
> Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](https://azure.microsoft.com/try/app-service/)sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.
>
>

## <a name="whats-changed"></a>Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714)
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715)
