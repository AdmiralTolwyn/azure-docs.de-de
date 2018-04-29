---
title: Azure Service Fabric CLI – sfctl node | Microsoft-Dokumentation
description: Beschreibt die sfctl node-Befehle der Service Fabric-Befehlszeilenschnittstelle (Command Line Interface, CLI).
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 02/22/2018
ms.author: ryanwi
ms.openlocfilehash: 50c7fe38d8bf7b14adf437f85c758e465e7d231d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/20/2018
---
# <a name="sfctl-node"></a>sfctl node
Ermöglicht es, Knoten zu verwalten, die einen Cluster bilden.

## <a name="commands"></a>Befehle

|Get-Help|BESCHREIBUNG|
| --- | --- |
|    disable       | Deaktiviert einen Service Fabric-Clusterknoten mit der angegebenen Deaktivierungsabsicht.|
|    enable        | Aktiviert einen Service Fabric-Clusterknoten, der momentan deaktiviert ist.|
|    health        | Ruft die Integrität eines Service Fabric-Knotens ab.|
|    info          | Ruft die Informationen zu einem bestimmten Knoten im Service Fabric-Cluster ab.|
|    list          | Ruft die Liste der Knoten im Service Fabric-Cluster ab.|
|    load          | Ruft die Ladeinformationen eines Service Fabric-Knotens ab.|
|    remove-state  | Benachrichtigt Service Fabric, dass der persistente Zustand auf einem Knoten dauerhaft entfernt wurde oder verloren gegangen ist.|
|    report-health | Sendet einen Integritätsbericht zu dem Service Fabric-Knoten.|
|    restart       | Startet einen Service Fabric-Clusterknoten neu.|
|    transition    | Startet oder beendet einen Clusterknoten.|
|    transition-status| Ruft den Status eines Vorgangs ab, der mit „StartNodeTransition“ gestartet wurde.|


## <a name="sfctl-node-disable"></a>sfctl node disable
Deaktivieren Sie einen Service Fabric-Clusterknoten mit der angegebenen Deaktivierungsabsicht.

Deaktivieren Sie einen Service Fabric-Clusterknoten mit der angegebenen Deaktivierungsabsicht. Sobald die Deaktivierung ausgeführt wird, kann die Deaktivierungsabsicht erhöht, aber nicht verringert werden (z. B. kann ein Knoten, der mit der „Pause“-Absicht deaktiviert ist, mit „Restart“ weiter deaktiviert werden, aber nicht umgekehrt). Knoten können über den Vorgang zum Aktivieren eines Knotens jederzeit erneut aktiviert werden, nachdem sie deaktiviert wurden. Ist die Deaktivierung nicht abgeschlossen, bricht dies die Deaktivierung ab. Ein Knoten, der ausgefallen und wieder in Betrieb gegangen ist, während er deaktiviert war, muss weiterhin erneut aktiviert werden, bevor Dienste auf ihm platziert werden können.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --node-name [erforderlich]| Der Name des Knotens.|
| --deactivation-intent | Beschreibt die Absicht oder den Grund für die Deaktivierung des Knotens. |
| --timeout -t       | Servertimeout in Sekunden.  Standardwert: 60.|

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug            | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden.|
| --help -h          | Zeigt diese Hilfemeldung an und beendet.|
| --output -o        | Das Ausgabeformat.  Zulässige Werte sind: json, jsonc, table, tsv.  Standardwert: json.|
| --query            | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter http://jmespath.org/.|
| --verbose          | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen.|

## <a name="sfctl-node-enable"></a>sfctl node enable
Aktiviert einen Service Fabric-Clusterknoten, der momentan deaktiviert ist.

Aktiviert einen Service Fabric-Clusterknoten, der momentan deaktiviert ist. Sobald der Knoten aktiviert ist, wird er wieder ein geeignetes Ziel zum Platzieren neuer Replikate, und alle deaktivierten Replikate, die auf dem Knoten verblieben sind, werden erneut aktiviert.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --node-name [erforderlich]| Der Name des Knotens.|
| --timeout -t       | Servertimeout in Sekunden.  Standardwert: 60.|

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug            | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden.|
| --help -h          | Zeigt diese Hilfemeldung an und beendet.|
| --output -o        | Das Ausgabeformat.  Zulässige Werte sind: json, jsonc, table, tsv.  Standardwert: json.|
| --query            | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter http://jmespath.org/.|
| --verbose          | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen.|

## <a name="sfctl-node-health"></a>sfctl node health
Ruft die Integrität eines Service Fabric-Knotens ab.

Ruft die Integrität eines Service Fabric-Knotens ab. Verwenden Sie „EventsHealthStateFilter“, um die Sammlung der Integritätsereignisse (HealthEvents), die für den Knoten gemeldet wurden, anhand des Integritätsstatus zu filtern. Ist der Knoten, dessen Name Sie angegeben haben, im Integritätsspeicher nicht vorhanden, wird ein Fehler zurückgegeben.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --node-name [erforderlich]| Der Name des Knotens.|
| --events-health-state-filter| Ermöglicht das Filtern der Sammlung zurückgegebener HealthEvent-Objekte anhand des Integritätsstatus. Die möglichen Werte für diesen Parameter entsprechen dem jeweiligen ganzzahligen Wert von einem der folgenden Integritätsstatus. Es werden nur Ereignisse zurückgegeben, die dem Filter entsprechen. Alle Ereignisse werden verwendet, um den aggregierten Integritätsstatus auszuwerten. Ist kein Filter angegeben, werden alle Einträge zurückgegeben. Ein Statuswert ist eine kennzeichenbasierte Enumeration, sodass der Wert eine Kombination der Werte sein kann, die mit dem bitweisen ODER-Operator abgerufen werden. Ist der angegebene Wert beispielsweise gleich „6“, werden alle Ereignisse zurückgegeben, für die „HealthState“ den Wert für OK (2) oder Warnung (4) hat. – Default: Standardwert. Stimmt mit jedem Integritätsstatus (HealthState) überein. Der Wert ist gleich null. – None: Filter, der mit keinem Wert für „HealthState“ übereinstimmt. Wird verwendet, um keine Ergebnisse für eine angegebene Statussammlung zurückzugeben. Der Wert ist gleich „1“. – Ok: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Ok“ hat. Der Wert ist gleich „2“. – Warning: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Warning“ hat. Der Wert ist gleich „4“. – Error: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Error“ hat. Der Wert ist gleich „8“. – All: Filter, der mit Eingaben übereinstimmt, die einen beliebigen Wert für „HealthState“ haben. Der Wert ist gleich „65535“.|
| --timeout -t             | Servertimeout in Sekunden.  Standardwert: 60.|

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug                  | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden.|
| --help -h                | Zeigt diese Hilfemeldung an und beendet.|
| --output -o              | Das Ausgabeformat.  Zulässige Werte sind: json, jsonc, table, tsv.  Standardwert: json.|
| --query                  | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter http://jmespath.org/.|
| --verbose                | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen.|

## <a name="sfctl-node-info"></a>sfctl node info
Ruft die Informationen zu einem bestimmten Knoten im Service Fabric-Cluster ab.

Ruft die Informationen zu einem bestimmten Knoten im Service Fabric-Cluster ab. Die Antwort enthält den Namen, den Status, die ID, die Integrität, die Betriebszeit und weitere Details über den Knoten.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --node-name [erforderlich]| Der Name des Knotens.|
| --timeout -t       | Servertimeout in Sekunden.  Standardwert: 60.|

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug            | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden.|
| --help -h          | Zeigt diese Hilfemeldung an und beendet.|
| --output -o        | Das Ausgabeformat.  Zulässige Werte sind: json, jsonc, table, tsv.  Standardwert: json.|
| --query            | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter http://jmespath.org/.|
| --verbose          | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen.|

## <a name="sfctl-node-list"></a>sfctl node list
Ruft die Liste der Knoten im Service Fabric-Cluster ab.

Ruft die Liste der Knoten im Service Fabric-Cluster ab. Die Antwort enthält den Namen, den Status, die ID, die Integrität, die Betriebszeit und weitere Details über den Knoten.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --continuation-token| Der Parameter „continuation-token“ (Fortsetzungstoken) wird dazu verwendet, den nächsten Satz von Ergebnissen abzurufen. Ein Fortsetzungstoken mit einem nicht leeren Wert wird in die Antwort der API eingefügt, wenn die Ergebnisse aus dem System nicht in eine einzige Antwort passen.      Wird dieser Wert an den nächsten API-Aufruf übergeben, gibt die API den nächsten Satz von Ergebnissen zurück. Gibt es keine weiteren Ergebnisse, enthält das Fortsetzungstoken keinen Wert. Der Wert dieses Parameters darf nicht als URL codiert sein.|
| --node-status-filter| Ermöglicht ein Filtern des Knotens anhand des Knotenstatus (NodeStatus). Es werden nur die Knoten zurückgegeben, die dem angegebenen Filterwert entsprechen. Der Filterwert kann einer der folgenden Werte sein. Standardwert: default.|
| --timeout -t     | Servertimeout in Sekunden.  Standardwert: 60.|

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug          | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden.|
| --help -h        | Zeigt diese Hilfemeldung an und beendet.|
| --output -o      | Das Ausgabeformat.  Zulässige Werte sind: json, jsonc, table, tsv.  Standardwert: json.|
| --query          | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter http://jmespath.org/.|
| --verbose        | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen.|

## <a name="sfctl-node-load"></a>sfctl node load
Ruft die Lastinformationen eines Service Fabric-Knotens ab.

Ruft die Informationen zum Laden eines Service Fabric-Knotens für alle Metriken ab, für die Last oder Kapazität definiert wurden.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --node-name [erforderlich]| Der Name des Knotens.|
| --timeout -t       | Servertimeout in Sekunden.  Standardwert: 60.|

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug            | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden.|
| --help -h          | Zeigt diese Hilfemeldung an und beendet.|
| --output -o        | Das Ausgabeformat.  Zulässige Werte sind: json, jsonc, table, tsv.  Standardwert: json.|
| --query            | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter http://jmespath.org/.|
| --verbose          | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen.|

## <a name="sfctl-node-restart"></a>sfctl node restart
Startet einen Service Fabric-Clusterknoten neu.

Startet einen Service Fabric-Clusterknoten neu, der bereits gestartet ist.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --node-name [erforderlich]| Der Name des Knotens.|
| --create-fabric-dump  | Geben Sie „True“ an, um ein Speicherabbild des Fabric-Knotenprozesses zu erstellen. Hierbei muss die Groß-/Kleinschreibung beachtet werden.  Standardwert: False.|
| --node-instance-id | Die Instanz-ID des Zielknotens. Ist die Instanz-ID angegeben, wird der Knoten nur dann neu gestartet, wenn die ID mit der aktuellen Instanz des Knotens übereinstimmt. Der Standardwert „0“ stimmt mit jeder Instanz-ID überein. Die Instanz-ID kann mithilfe einer Abfrage zum Abrufen des Knotens (get node query) abgerufen werden.  Standard: 0.|
| --timeout -t       | Servertimeout in Sekunden.  Standardwert: 60.|

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug            | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden.|
| --help -h          | Zeigt diese Hilfemeldung an und beendet.|
| --output -o        | Das Ausgabeformat.  Zulässige Werte sind: json, jsonc, table, tsv.  Standardwert: json.|
| --query            | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter http://jmespath.org/.|
| --verbose          | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen.|

## <a name="sfctl-node-transition"></a>sfctl node transition
Startet oder beendet einen Clusterknoten.

Startet oder beendet einen Clusterknoten.  Ein Clusterknoten ist ein Prozess, nicht die Betriebssysteminstanz selbst.
Um einen Knoten zu starten, übergeben Sie „Start“ an den Parameter „NodeTransitionType“. Um einen Knoten zu beenden, übergeben Sie „Stop“ an den Parameter „NodeTransitionType“. Diese API startet den Vorgang. Wenn die API zurückkehrt, ist der Übergang des Knotens möglicherweise noch nicht abgeschlossen. Rufen Sie „GetNodeTransitionProgress“ mit derselben „OperationId“ auf, um den Fortschritt des Vorgangs abzurufen. 

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --node-instance-id [erforderlich]| Die Knoteninstanz-ID des Zielknotens. Diese ID kann über die GetNodeInfo-API bestimmt werden.|
| --node-name [erforderlich]| Der Name des Knotens.|
| --node-transition-type [erforderlich]| Gibt den Typ des auszuführenden Übergangs an.                       „NodeTransitionType.Start“ startet einen angehaltenen Knoten.                       NodeTransitionType. Stop beendet einen Knoten, der ausgeführt wird. |
| --operation-id [erforderlich]| Eine GUID, die einen Aufruf dieser API kennzeichnet.  Diese GUID wird in die entsprechende GetProgress-API übergeben.|
| --stop-duration-in-seconds [erforderlich]| Die Dauer in Sekunden, die der Knoten angehalten bleiben soll.  Der Mindestwert ist 600, der Höchstwert 14400. Nach Ablauf dieser Zeit wird der Knoten automatisch wieder betriebsbereit.|
| --timeout -t                      | Servertimeout in Sekunden.  Standardwert: 60.|

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug                           | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden.|
| --help -h                         | Zeigt diese Hilfemeldung an und beendet.|
| --output -o                       | Das Ausgabeformat.  Zulässige Werte sind: json, jsonc, table, tsv.                       Standardwert: json.|
| --query                           | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter http://jmespath.org/.|
| --verbose                         | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen.|

## <a name="next-steps"></a>Nächste Schritte
- [Einrichten](service-fabric-cli.md) der Service Fabric-Befehlszeilenschnittstelle
- Informationen zum Verwenden der Service Fabric-Befehlszeilenschnittstelle mit den [Beispielskripts](/azure/service-fabric/scripts/sfctl-upgrade-application)