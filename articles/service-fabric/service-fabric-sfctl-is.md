---
title: "Azure Service Fabric CLI – sfctl is | Microsoft-Dokumentation"
description: Beschreibt die sfctl is-Befehle der Service Fabric-Befehlszeilenschnittstelle (Command Line Interface, CLI).
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/22/2017
ms.author: ryanwi
ms.openlocfilehash: b611a447dd6669a09ca16c816de74acd7f3e8c7e
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/18/2018
---
# <a name="sfctl-is"></a>sfctl is
Ermöglicht es, Befehle an den Infrastrukturdienst abzufragen und zu senden.

## <a name="commands"></a>Befehle

|Get-Help|BESCHREIBUNG|
| --- | --- |
|    command| Ruft einen Verwaltungsbefehl für die angegebene Infrastrukturdienstinstanz auf.|
|    query  | Ruft eine schreibgeschützte Abfrage für die angegebene Infrastrukturdienstinstanz auf.|


## <a name="sfctl-is-command"></a>sfctl is command
Ruft einen Verwaltungsbefehl für die angegebene Infrastrukturdienstinstanz auf.

Für Cluster, für die mindestens eine Instanz des Infrastrukturdiensts konfiguriert ist, bietet diese API eine Möglichkeit, infrastrukturspezifische Befehle an eine bestimmte Instanz des Infrastrukturdiensts zu senden. Die verfügbaren Befehle und ihre entsprechenden Antwortformate variieren je nach der Infrastruktur, auf der der Cluster ausgeführt wird. Diese API unterstützt die Service Fabric-Plattform. Sie ist nicht dazu vorgesehen, direkt aus Ihrem Code verwendet zu werden. zu erstellen und zu verwalten.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --command [erforderlich]| Der Text des Befehls, der aufgerufen werden soll. Der Inhalt des Befehls ist infrastrukturspezifisch.  Standardwert: is command.|
| --service-id     | Die Identität des Infrastrukturdiensts. Dies ist der vollständige Name des Infrastrukturdiensts ohne das URI-Schema „fabric:“. Dieser Parameter ist nur für Cluster erforderlich, in denen mehrere Instanzen des Infrastrukturdiensts ausgeführt werden.|
| --timeout -t     | Servertimeout in Sekunden.  Standardwert: 60.|

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug          | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden.|
| --help -h        | Zeigt diese Hilfemeldung an und beendet.|
| --output -o      | Das Ausgabeformat.  Zulässige Werte sind: json, jsonc, table, tsv.  Standardwert: json.|
| --query          | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http://jmespath.org/“.|
| --verbose        | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen.|

## <a name="sfctl-is-query"></a>sfctl is query
Ruft eine schreibgeschützte Abfrage für die angegebene Infrastrukturdienstinstanz auf.

Für Cluster, für die mindestens eine Instanz des Infrastrukturdiensts konfiguriert ist, bietet diese API eine Möglichkeit, infrastrukturspezifische Abfragen an eine bestimmte Instanz des Infrastrukturdiensts zu senden. Die verfügbaren Befehle und ihre entsprechenden Antwortformate variieren je nach der Infrastruktur, auf der der Cluster ausgeführt wird. Diese API unterstützt die Service Fabric-Plattform. Sie ist nicht dazu vorgesehen, direkt aus Ihrem Code verwendet zu werden.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --command [erforderlich]| Der Text des Befehls, der aufgerufen werden soll. Der Inhalt des Befehls ist infrastrukturspezifisch.  Standardwert: is query.|
| --service-id     | Die Identität des Infrastrukturdiensts. Dies ist der vollständige Name des Infrastrukturdiensts ohne das URI-Schema „fabric:“. Dieser Parameter ist nur für Cluster erforderlich, in denen mehrere Instanzen des Infrastrukturdiensts ausgeführt werden.|
| --timeout -t     | Servertimeout in Sekunden.  Standardwert: 60.|

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug          | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden.|
| --help -h        | Zeigt diese Hilfemeldung an und beendet.|
| --output -o      | Das Ausgabeformat.  Zulässige Werte sind: json, jsonc, table, tsv.  Standardwert: json.|
| --query          | JMESPath-Abfragezeichenfolge. Weitere Informationen finden Sie unter „http://jmespath.org/“.|
| --verbose        | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen.|

## <a name="next-steps"></a>Nächste Schritte
- [Einrichten](service-fabric-cli.md) der Service Fabric-Befehlszeilenschnittstelle
- Informationen zum Verwenden der Service Fabric-Befehlszeilenschnittstelle mit den [Beispielskripts](/azure/service-fabric/scripts/sfctl-upgrade-application)