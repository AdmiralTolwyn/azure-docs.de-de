---
title: Sammeln von Palo Alto-Daten in Azure Sentinel Preview | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Palo Alto-Daten in Azure Sentinel sammeln.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: 149b3b813091033bf5c1685e8b0793f955169808
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57841207"
---
# <a name="connect-your-palo-alto-networks-appliance"></a>Verbinden Ihrer Palo Alto Networks-Appliance

> [!IMPORTANT]
> Azure Sentinel ist aktuell als Public Preview verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Sie können Azure Sentinel mit jeder Palo Alto Networks-Appliance verbinden, indem Sie die Protokolldateien im Format „Syslog CEF“ speichern. Die Integration mit Azure Sentinel ermöglicht Ihnen die einfache Ausführung von Analysen und Abfragen von Daten der Protokolldateien von Palo Alto. Weitere Informationen dazu, wie Azure Sentinel CEF-Daten erfasst, finden Sie unter [Connect your external solution using Common Event Format](connect-common-event-format.md).

> [!NOTE]
> - Daten werden am geografischen Standort des Arbeitsbereichs gespeichert, in dem Sie Azure Sentinel ausführen.

## <a name="step-1-connect-your-palo-alto-appliance-using-an-agent"></a>Schritt 1: Verbinden Ihrer Palo Alto-Appliance mithilfe eines Agents

Sie müssen einen Agent auf einem dedizierten Computer (VM oder lokal) bereitstellen, um Ihre Palo Alto-Appliance mit Azure Sentinel zu verbinden und Kommunikation zwischen der Appliance und Azure Sentinel zu ermöglichen. Sie können den Agent automatisch oder manuell bereitstellen. Die automatische Bereitstellung ist nur verfügbar, wenn es sich bei Ihrem dedizierten Computer um einen neuen virtuellen Computer handelt, den Sie in Azure erstellen. 

Alternativ hierzu können Sie den Agent manuell auf einem vorhandenen virtuellen Azure-Computer, einem virtuellen Computer in einer anderen Cloud oder einem lokalen Computer bereitstellen.

Netzwerkdiagramme zu beiden Optionen finden Sie unter [Herstellen einer Verbindung mit Datenquellen](connect-data-sources.md#agent-options).


### <a name="deploy-the-agent-in-azure"></a>Bereitstellen des Agents in Azure

1. Klicken Sie im Azure Sentinel-Portal auf **Datensammlung**, und wählen Sie den Typ Ihrer Appliance aus. 

1. Gehen Sie unter **Linux Syslog agent configuration** (Linux-Syslog-Agent-Konfiguration) folgendermaßen vor:
   - Wählen Sie **Automatische Bereitstellung** (Automatic deployment) aus, wenn Sie einen neuen Computer erstellen möchten, auf dem der Azure Sentinel-Agent vorinstalliert ist und der die gesamte erforderliche Konfiguration (siehe Beschreibung oben) enthält. Wählen Sie **Automatische Bereitstellung** aus, und klicken Sie dann auf **Automatic agent deployment** (Automatische Bereitstellung des Agents). Daraufhin wird die Kaufseite für einen dedizierten virtuellen Computer geöffnet, der automatisch mit Ihrem Arbeitsbereich verbunden wird. Der virtuelle Computer gehört zur Serie **Standard D2s v3 (2 vCPUs, 8 GB Arbeitsspeicher)** und hat eine öffentliche IP-Adresse.
      1. Geben Sie auf der Seite **Benutzerdefinierte Bereitstellung** Ihre Details an, legen Sie einen Benutzernamen und ein Kennwort fest, und erwerben Sie den virtuellen Computer, wenn Sie die Nutzungsbedingungen akzeptieren.
      1. Konfigurieren Sie Ihre Appliance so, dass sie Protokolle mit den Einstellungen sendet, die auf der Verbindungsseite aufgeführt sind. Verwenden Sie für den Connector „Generic Common Event Format“ diese Einstellungen:
         - Protocol = UDP
         - Port = 514
         - Facility = Local-4
         - Format = CEF
   - Wählen Sie **Manuelle Bereitstellung** (Manual deployment) aus, wenn Sie einen vorhandenen virtuellen Computer als dedizierten Linux-Computer verwenden möchten, auf dem der Azure Sentinel-Agent installiert werden soll. 
      1. Wählen Sie unter **Download and install the Syslog agent** (Syslog-Agent herunterladen und installieren) die Option **Azure Linux virtual machine** (Virtueller Azure-Linux-Computer) aus. 
      1. Wählen Sie in der **Virtuelle Computer**-Anzeige, die geöffnet wird, den Computer aus, den Sie verwenden möchten, und klicken Sie dann auf **Verbinden**.
      1. Legen Sie in der Connector-Anzeige unter **Configure and forward Syslog** (Syslog konfigurieren und weiterleiten) fest, ob Sie den Syslog-Daemon **rsyslog.d** oder **syslog-ng** verwenden. 
      1. Kopieren Sie die folgenden Befehle, und führen Sie sie auf der Appliance aus:
          - Wenn Sie „rsyslog.d“ ausgewählt haben:
              
            1. Weisen Sie den Syslog-Daemon an, auf die Einrichtung „local_4“ zu lauschen und die Syslog-Nachrichten an den Azure Sentinel-Agent über Port 25226 zu senden. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
            2. Laden Sie die [Konfigurationsdatei „security_events“](https://aka.ms/asi-syslog-config-file-linux) herunter, mit der der Syslog-Agent zum Lauschen auf Port 25226 konfiguriert wird, und installieren Sie sie. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Ersetzen Sie hier {0} durch die GUID Ihres Arbeitsbereichs.
            
            1. Starten Sie den Syslog-Daemon neu: `sudo service rsyslog restart`
             
          - Wenn Sie „syslog-ng“ ausgewählt haben:

              1. Weisen Sie den Syslog-Daemon an, auf die Einrichtung „local_4“ zu lauschen und die Syslog-Nachrichten an den Azure Sentinel-Agent über Port 25226 zu senden. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              2. Laden Sie die [Konfigurationsdatei „security_events“](https://aka.ms/asi-syslog-config-file-linux) herunter, mit der der Syslog-Agent zum Lauschen auf Port 25226 konfiguriert wird, und installieren Sie sie. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Ersetzen Sie hier {0} durch die GUID Ihres Arbeitsbereichs.

              3. Starten Sie den Syslog-Daemon neu: `sudo service syslog-ng restart`
      2. Starten Sie den Syslog-Agent mit dem folgenden Befehl neu: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Bestätigen Sie, dass im Agent-Protokoll keine Fehler enthalten sind, indem Sie den folgenden Befehl ausführen: `tail /var/opt/microsoft/omsagent/log/omsagent.log`

### <a name="deploy-the-agent-on-an-on-premises-linux-server"></a>Bereitstellen des Agents auf einem lokalen Linux-Server

Wenn Sie Azure nicht verwenden, stellen Sie den Azure Sentinel-Agent manuell bereit, um ihn auf einem dedizierten Linux-Server auszuführen.


1. Klicken Sie im Azure Sentinel-Portal auf **Datensammlung**, und wählen Sie den Typ Ihrer Appliance aus.
1. Wählen Sie unter **Linux Syslog agent configuration** (Linux-Syslog-Agent-Konfiguration) die Option **Manuelle Bereitstellung** aus, um einen dedizierten virtuellen Linux-Computer zu erstellen.
   1. Wählen Sie unter **Download and install the Syslog agent** (Syslog-Agent herunterladen und installieren) die Option **Non-Azure Linux machine** (Nicht von Azure stammender virtueller Computer) aus. 
   1. Wählen Sie in der **Direkt-Agent**-Anzeige, die geöffnet wird, die Option **Agent für Linux** aus, um den Agent herunterzuladen, oder führen Sie den folgenden Befehl aus, um den Agent auf Ihren Linux-Computer herunterzuladen: `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w {workspace GUID} -s gehIk/GvZHJmqlgewMsIcth8H6VqXLM9YXEpu0BymnZEJb6mEjZzCHhZgCx5jrMB1pVjRCMhn+XTQgDTU3DVtQ== -d opinsights.azure.com`
      1. Legen Sie in der Connector-Anzeige unter **Configure and forward Syslog** (Syslog konfigurieren und weiterleiten) fest, ob Sie den Syslog-Daemon **rsyslog.d** oder **syslog-ng** verwenden. 
      1. Kopieren Sie die folgenden Befehle, und führen Sie sie auf der Appliance aus:
         - Wenn Sie „rsyslog“ ausgewählt haben:
           1. Weisen Sie den Syslog-Daemon an, auf die Einrichtung „local_4“ zu lauschen und die Syslog-Nachrichten an den Azure Sentinel-Agent über Port 25226 zu senden. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
           2. Laden Sie die [Konfigurationsdatei „security_events“](https://aka.ms/asi-syslog-config-file-linux) herunter, mit der der Syslog-Agent zum Lauschen auf Port 25226 konfiguriert wird, und installieren Sie sie. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Ersetzen Sie hier {0} durch die GUID Ihres Arbeitsbereichs.
           3. Starten Sie den Syslog-Daemon neu: `sudo service rsyslog restart`
         - Wenn Sie „syslog-ng“ ausgewählt haben:
            1. Weisen Sie den Syslog-Daemon an, auf die Einrichtung „local_4“ zu lauschen und die Syslog-Nachrichten an den Azure Sentinel-Agent über Port 25226 zu senden. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
            2. Laden Sie die [Konfigurationsdatei „security_events“](https://aka.ms/asi-syslog-config-file-linux) herunter, mit der der Syslog-Agent zum Lauschen auf Port 25226 konfiguriert wird, und installieren Sie sie. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Ersetzen Sie hier {0} durch die GUID Ihres Arbeitsbereichs.
            3. Starten Sie den Syslog-Daemon neu: `sudo service syslog-ng restart`
      1. Starten Sie den Syslog-Agent mit dem folgenden Befehl neu: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Bestätigen Sie, dass im Agent-Protokoll keine Fehler enthalten sind, indem Sie den folgenden Befehl ausführen: `tail /var/opt/microsoft/omsagent/log/omsagent.log`
 
## <a name="step-2-forward-palo-alto-logs-to-the-syslog-agent"></a>Schritt 2: Weiterleiten der Palo Alto-Protokolle an den Syslog-Agent

Konfigurieren Sie Palo Alto Networks, um Syslog-Nachrichten im CEF-Format über den Syslog-Agent an Ihren Azure-Arbeitsbereich weiterzuleiten.
1.  Wechseln Sie zu [Common Event Format (CEF) Configuration Guides](https://docs.paloaltonetworks.com/resources/cef), und laden Sie die PDF-Datei für den Typ Ihrer Appliance herunter. Führen Sie alle Anweisungen in der Anleitung aus, um Ihre Palo Alto-Appliance so einzurichten, dass sie CEF-Ereignisse erfasst. 

1.  Wechseln Sie zu [Configure Syslog monitoring](https://aka.ms/asi-syslog-paloalto-forwarding), und führen Sie die Schritte 2 und 3 aus, um CEF-Ereignisweiterleitung von Ihrer Palo Alto-Appliance an Azure Sentinel zu konfigurieren.

    1. Legen Sie die Option **Syslog server format** (Format des Syslog-Servers) auf **BSD** fest.
    1. Legen Sie die Option **Facility number** (Einrichtungsanzahl) auf genau den Wert fest, den Sie im Syslog-Agent festgelegt haben.

> [!NOTE]
> Bei den Kopieren/Einfügen-Vorgängen aus der PDF-Datei kann es vorkommen, dass Text geändert wird und zufällige Zeichen eingefügt werden. Um dies zu vermeiden, kopieren Sie den Text in einen Editor, und entfernen Sie, bevor Sie den Text einfügen, alle Zeichen, die das Protokollformat beeinträchtigen könnten, wie dies im folgenden Beispiel zu sehen ist.
 
  ![Problem beim Kopieren von CEF-Text](./media/connect-cef/paloalto-text-prob1.png)

## <a name="step-3-validate-connectivity"></a>Schritt 3: Überprüfen der Konnektivität

Es kann bis zu 20 Minuten dauern, bis Ihre Protokolle in Log Analytics angezeigt werden. 

1. Stellen Sie sicher, dass für Ihre Protokolle der richtige Port im Syslog-Agent verwendet wird. Führen Sie den folgenden Befehl auf dem Syslog-Agent-Computer aus: `tcpdump -A -ni any  port 514 -vv` Durch diesen Befehl werden Ihnen die Protokolle angezeigt, die vom Gerät an den Syslog-Computer gestreamt werden. Stellen Sie sicher, dass Protokolle von der Quellappliance über den richtigen Port und die richtige Einrichtung eingehen.
2. Stellen Sie sicher, dass der Syslog-Daemon und der Agent kommunizieren. Führen Sie den folgenden Befehl auf dem Syslog-Agent-Computer aus: `tcpdump -A -ni any  port 25226 -vv` Durch diesen Befehl werden Ihnen die Protokolle angezeigt, die vom Gerät an den Syslog-Computer übermittelt werden. Stellen Sie sicher, dass die Protokolle auch vom Agent empfangen werden.
3. Wenn beide diese Befehle erfolgreiche Ergebnisse zurückgegeben haben, überprüfen Sie in Log Analytics, ob Ihre Protokolle eingehen. Alle Ereignisse, die von diesen Appliances übermittelt werden, werden in Log Analytics unformatiert unter dem Typ `CommonSecurityLog ` angezeigt.
1. In `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log` können Sie überprüfen, ob Fehler aufgetreten sind oder die Protokolle nicht eingehen.
4. Stellen Sie sicher, dass die Standardgröße der Syslog-Nachrichten auf 2048 Bytes (2 KB) beschränkt ist. Wenn die Protokolle zu lang sind, aktualisieren Sie die Konfigurationsdatei „security_events.conf“ mithilfe des folgenden Befehls: `message_length_limit 4096`







## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie gelernt, wie Sie Palo Alto-Appliances mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [ Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats.md).

