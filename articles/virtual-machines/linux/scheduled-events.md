---
title: "Geplante Ereignisse für Linux-VMs in Azure | Microsoft-Dokumentation"
description: "Geplante Ereignisse mit dem Azure-Metadatendienst für Ihre virtuellen Linux-Computer."
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2017
ms.author: zivr
ms.openlocfilehash: 75e509a7e39f5b268ce550d0c4dea2261d4fe56f
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/18/2017
---
# <a name="azure-metadata-service-scheduled-events-preview-for-linux-vms"></a>Azure-Metadatendienst: Geplante Ereignisse (Vorschau) für Linux-VMs

> [!NOTE] 
> Die Vorschauen werden Ihnen zur Verfügung gestellt, wenn Sie die folgenden Nutzungsbedingungen akzeptieren. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

Geplante Ereignisse ist einer der untergeordneten Dienste des Azure-Metadatendiensts. Er liefert Informationen zu anstehenden Ereignissen (z.B. Neustart), damit sich Ihre Anwendung darauf vorbereiten und Unterbrechungen begrenzen kann. Der Dienst steht für sämtliche Arten virtueller Azure-Computer zur Verfügung, einschließlich PaaS und IaaS. Geplante Ereignisse räumt Ihrem virtuellen Computer Zeit ein, vorbeugende Maßnahmen zu ergreifen und die Auswirkungen eines Ereignisses zu minimieren. 

Geplante Ereignisse sind für virtuelle Windows- und Linux-Computer verfügbar. Informationen zu geplanten Ereignissen unter Windows finden Sie unter [Geplante Ereignisse für virtuelle Windows-Computer](../windows/scheduled-events.md).

## <a name="why-scheduled-events"></a>Warum geplante Ereignisse?

Mit Geplante Ereignissen können Sie Maßnahmen ergreifen, um die Auswirkungen von plattformseitig initiierten Wartungen oder benutzerseitig initiierten Aktionen auf Ihren Dienst einzuschränken. 

Workloads mit mehreren Instanzen, die Replikationstechniken zur Statusverwaltung verwenden, sind ggf. anfällig für Ausfälle, die in mehreren Instanzen auftreten. Solche Ausfälle können zu aufwendigen Aufgaben (z.B. Neuerstellung von Indizes) oder sogar zu einem Replikatsverlust führen. 

In vielen anderen Fällen kann die allgemeine Verfügbarkeit des Diensts verbessert werden, indem Sie eine ordnungsgemäße Herunterfahrsequenz ausführen, z.B. Abschließen (oder Abbrechen) von momentan ausgeführten Transaktionen, Neuzuweisen von Aufgaben an andere virtuelle Computer im Cluster (manuelles Failover) oder Entfernen des virtuellen Computers aus einem Netzwerk-Lastenausgleichspool. 

In bestimmten Fällen kann durch eine Benachrichtigung eines Administrators über ein anstehendes Ereignis oder die Protokollierung eines Ereignisses die Wartbarkeit von in der Cloud gehosteten Anwendungen verbessert werden.

Der Azure-Metadatendienst zeigt geplante Ereignisse in den folgenden Anwendungsfällen an:
-   Von der Plattform ausgelöste Wartungsaktionen (Beispiel: Rollout des Hostbetriebssystems)
-   Vom Benutzer initiierte Aufrufe (z.B. Neustart oder erneute Bereitstellung eines virtuellen Computers durch den Benutzer)


## <a name="the-basics"></a>Grundlagen  

Der Azure-Metadatendienst macht Informationen zu ausgeführten virtuellen Computern mithilfe eines innerhalb einer VM zugänglichen REST-Endpunkts verfügbar. Die Informationen stehen über eine nicht routbare IP-Adresse bereit, die außerhalb der VM nicht verfügbar gemacht wird.

### <a name="scope"></a>Umfang
Geplante Ereignisse werden allen virtuellen Computern in einem Clouddienst oder allen virtuellen Computern in einer Verfügbarkeitsgruppe angezeigt. Daher sollten Sie das Feld `Resources` in einem Ereignis überprüfen, um zu ermitteln, welche VMs betroffen sein werden. 

### <a name="discovering-the-endpoint"></a>Ermitteln des Endpunkts
In Fällen, in denen ein virtueller Computer innerhalb eines virtuellen Netzwerks (VNET) erstellt wird, ist der Metadatendienst über eine nicht routbare IP-Adresse verfügbar: `169.254.169.254`.
Wenn der virtuelle Computer nicht innerhalb eines virtuellen Netzwerks erstellt wird (Standard für Clouddienste und klassische virtuelle Computer), ist zusätzliche Logik erforderlich, um den zu verwendenden Endpunkt zu ermitteln. In diesem Beispiel erfahren Sie, wie Sie [den Hostendpunkt ermitteln](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="versioning"></a>Versionsverwaltung 
Für den Instanzmetadatendienst wird die Versionsverwaltung genutzt. Versionen sind obligatorisch, und die aktuelle Version ist `2017-03-01`.

> [!NOTE] 
> In früheren Vorschauversionen von geplanten Ereignissen wird {latest} als „api-version“ unterstützt. Dieses Format wird nicht mehr unterstützt und wird zukünftig veraltet sein.

### <a name="using-headers"></a>Verwenden von Headern
Beim Abfragen des Metadatendiensts müssen Sie den Header `Metadata: true` angeben, um sicherzustellen, dass die Anforderung nicht unbeabsichtigt umgeleitet wurde.

### <a name="enabling-scheduled-events"></a>Aktivieren von Geplante Ereignisse
Wenn Sie Geplante Ereignisse erstmals aufrufen, aktiviert Azure dieses Feature auf Ihrem virtuellen Computer implizit. Daher ist beim ersten Aufruf eine um bis zu zwei Minuten verzögerte Antwort zu erwarten.

### <a name="user-initiated-maintenance"></a>Vom Benutzer initiierte Wartung
Eine vom Benutzer über das Azure-Portal, die API, die Befehlszeilenschnittstelle oder PowerShell initiierte Wartung eines virtuellen Computers führt zu einem geplanten Ereignis. So können Sie die Logik zur Vorbereitung auf Wartungsmaßnahmen in Ihrer Anwendung testen und Ihre Anwendung auf die vom Benutzer initiierte Wartung vorbereiten.

Das Neustarten eines virtuellen Computers plant ein Ereignis vom Typ `Reboot`. Das erneute Bereitstellen eines virtuellen Computers plant ein Ereignis vom Typ `Redeploy`.

> [!NOTE] 
> Zurzeit können maximal 10 vom Benutzer initiierte Wartungsvorgänge gleichzeitig geplant werden. Dieses Limit wird vor der allgemeinen Verfügbarkeit von geplanten Ereignissen erhöht.

> [!NOTE] 
> Zurzeit ist die vom Benutzer initiierte Wartung, die zu geplanten Ereignissen führt, nicht konfigurierbar. Die Konfigurierbarkeit ist für ein zukünftiges Release geplant.

## <a name="using-the-api"></a>Verwenden der API

### <a name="query-for-events"></a>Abfragen von Ereignissen
Sie können geplante Ereignisse abfragen, indem Sie einfach folgenden Aufruf ausführen:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01
```

Eine Antwort enthält ein Array geplanter Ereignisse. Ein leeres Array bedeutet, dass derzeit keine Ereignisse geplant sind.
Sofern geplante Ereignisse vorliegen, enthält die Antwort ein Array mit Ereignissen: 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},              
        }
    ]
}
```

### <a name="event-properties"></a>Ereigniseigenschaften
|Eigenschaft  |  Beschreibung |
| - | - |
| EventId | Global eindeutiger Bezeichner für dieses Ereignis <br><br> Beispiel: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Auswirkungen dieses Ereignisses <br><br> Werte: <br><ul><li> `Freeze`: Das Anhalten des virtuellen Computers für einige Sekunden ist geplant. Der Prozessor wird angehalten, aber es gibt keine Auswirkungen auf Arbeitsspeicher, geöffnete Dateien oder Netzwerkverbindungen. <li>`Reboot`: Der Neustart des virtuellen Computers ist geplant (der Arbeitsspeicher geht verloren). <li>`Redeploy`: Das Verschieben des virtuellen Computers auf einen anderen Knoten ist geplant (kurzlebige Datenträger gehen verloren). |
| ResourceType | Typ der Ressource, auf die sich dieses Ereignis auswirkt <br><br> Werte: <ul><li>`VirtualMachine`|
| Ressourcen| Liste von Ressourcen, auf die sich dieses Ereignis auswirkt. Diese Liste enthält garantiert Computer aus maximal einer [Updatedomäne](manage-availability.md), muss jedoch nicht alle Computer in dieser Domäne enthalten. <br><br> Beispiel: <br><ul><li> [„FrontEnd_IN_0“, „BackEnd_IN_0“] |
| Ereignisstatus | Status dieses Ereignisses <br><br> Werte: <ul><li>`Scheduled`: Dieses Ereignis erfolgt nach dem in der `NotBefore`-Eigenschaft angegebenen Zeitpunkt.<li>`Started`: Dieses Ereignis wurde gestartet.</ul> `Completed` oder ähnliche Statusangaben werden niemals bereitgestellt. Das Ereignis wird nicht mehr zurückgegeben, nachdem es abgeschlossen ist.
| NotBefore| Zeit, nach der dieses Ereignis gestartet werden kann <br><br> Beispiel: <br><ul><li> 2016-09-19T18:29:47Z  |

### <a name="event-scheduling"></a>Ereignisplanung
Jedes Ereignis erfolgt dem Zeitplan nach, basierend auf dem Ereignistyp, eine Mindestzeitspanne in der Zukunft. Diese Zeit ist in der `NotBefore`-Eigenschaft eines Ereignisses angegeben. 

|EventType  | Mindestzeitspanne |
| - | - |
| Freeze| 15 Minuten |
| Neustart | 15 Minuten |
| Erneute Bereitstellung | 10 Minuten |

### <a name="starting-an-event"></a>Starten eines Ereignisses 

Sobald Sie von einem anstehenden Ereignis erfahren und Ihre Logik für ein ordnungsgemäßes Herunterfahren vervollständigt haben, können Sie ein ausstehendes Ereignis genehmigen, indem Sie einen Aufruf des Typs `POST` an den Metadatendienst mit der `EventId` ausführen. Dies zeigt Azure an, dass die minimale Benachrichtigungszeit verkürzt werden kann (wenn möglich). 

```
curl -H Metadata:true -X POST -d '{"DocumentIncarnation":"5", "StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01
```

> [!NOTE] 
> Durch das Bestätigen eines Ereignisses kann dieses für alle `Resources` im Ereignis fortgesetzt werden, nicht nur für den virtuellen Computer, der das Ereignis bestätigt. Daher sollten Sie möglicherweise einen Leiter zur Koordinierung der Bestätigung auswählen, beispielsweise einfach den ersten Computer im Feld `Resources`.




## <a name="python-sample"></a>Python-Beispiel 

Im folgenden Beispiel fragt der Metadatenserver geplante Ereignisse ab und genehmigt jedes ausstehende Ereignis.

```python
#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01"
headers = "{Metadata:true}"
this_host = socket.gethostname()

def get_scheduled_events():
   req = urllib2.Request(metadata_url)
   req.add_header('Metadata', 'true')
   resp = urllib2.urlopen(req)
   data = json.loads(resp.read())
   return data

def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid = evt['EventId']
        status = evt['EventStatus']
        resources = evt['Resources']
        eventtype = evt['EventType']
        resourcetype = evt['ResourceType']
        notbefore = evt['NotBefore'].replace(" ","_")
        if this_host in resources:
            print "+ Scheduled Event. This host is scheduled for " + eventype + " not before " + notbefore
            # Add logic for handling events here

def main():
   data = get_scheduled_events()
   handle_scheduled_events(data)
   
if __name__ == '__main__':
  main()
  sys.exit(0)
```

## <a name="next-steps"></a>Nächste Schritte 

- Erfahren Sie mehr über die APIs im [Instanz-Metadatendienst](instance-metadata-service.md).
- Erfahren Sie mehr über [Geplante Wartung für virtuelle Linux-Computer in Azure](planned-maintenance.md).
