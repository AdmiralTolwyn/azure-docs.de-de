---
title: Azure Service Fabric CLI – sfctl application | Microsoft-Dokumentation
description: Beschreibt die sfctl application-Befehle der Service Fabric-Befehlszeilenschnittstelle (Command Line Interface, CLI).
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
ms.date: 02/23/2018
ms.author: ryanwi
ms.openlocfilehash: fe0ef5c81b1ef6bef298e65cde3649c9464089d8
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2018
---
# <a name="sfctl-application"></a>sfctl application
Ermöglicht es, Anwendungen und Anwendungstypen zu erstellen, zu löschen und zu verwalten.

## <a name="commands"></a>Befehle

|Get-Help|BESCHREIBUNG|
| --- | --- |
| create       | Erstellt eine Service Fabric-Anwendung mithilfe der angegebenen Beschreibung.|
| delete       | Löscht eine vorhandene Service Fabric-Anwendung.|
| deployed     | Ruft die Informationen zu einer Anwendung ab, die auf einem Service Fabric-Knoten bereitgestellt wird.|
| deployed-health | Ruft die Informationen über die Integrität einer Anwendung ab, die auf einem Service Fabric-Knoten bereitgestellt ist.|
| deployed-list| Ruft die Liste der Anwendungen ab, die auf einem Service Fabric-Knoten bereitgestellt werden.|
| health       | Ruft die Integrität der Service Fabric-Anwendung ab.|
| info         | Ruft Informationen zu einer Service Fabric-Anwendung ab.|
| list         | Ruft die Liste der Anwendungen ab, die in dem Service Fabric-Cluster erstellt wurden und mit den Filtern übereinstimmen, die als Parameter angegeben sind.|
| load | Ruft Lastinformationen zu einer Service Fabric-Anwendung ab. |
| manifest     | Ruft das Manifest ab, das einen Anwendungstyp beschreibt.|
| provision    | Stellt einen Service Fabric-Anwendungstyp mithilfe des SFPKG-Pakets im externen Speicher oder mithilfe des Anwendungspakets im Imagespeicher im Cluster bereit oder registriert ihn.|
| report-health| Sendet einen Integritätsbericht zu der Service Fabric-Anwendung.|
| type         | Ruft die Liste der Anwendungstypen im Service Fabric-Cluster ab, die genau mit dem angegebenen Namen übereinstimmen.|
| type-list    | Ruft die Liste der Anwendungstypen im Service Fabric-Cluster ab.|
| unprovision  | Entfernt einen Service Fabric-Anwendungstyp aus dem Cluster oder hebt die Registrierung eines Service Fabric-Anwendungstyps für den Cluster auf.|
| Upgrade      | Startet ein Aktualisieren einer Anwendung im Service Fabric-Cluster.|
| upgrade-resume  | Setzt ein Aktualisieren einer Anwendung im Service Fabric-Cluster fort.|
| upgrade-rollback| Führt ein Rollback des derzeit laufenden Upgrades einer Anwendung im Service Fabric-Cluster aus.|
| upgrade-status  | Ruft Details über das neueste Upgrade ab, das für diese Anwendung ausgeführt wurde.|
| upload       | Kopiert ein Service Fabric-Anwendungspaket in den Imagespeicher.|

## <a name="sfctl-application-create"></a>sfctl application create
Erstellt eine Service Fabric-Anwendung mithilfe der angegebenen Beschreibung.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --app-name [erforderlich]| Der Name der Anwendung, einschließlich des URI-Schemas „fabric:“.|
| -- app-type [erforderlich]| Der Anwendungstypname, der im Anwendungsmanifest gefunden wurde.|
| -- app-version [erforderlich]| Die Version des Anwendungstyps, wie sie im Anwendungsmanifest definiert ist.|
| --max-node-count     | Die maximale Anzahl von Knoten, für die Service Fabric Kapazität für diese Anwendung reserviert. Dies bedeutet nicht, dass die Dienste dieser Anwendung auf allen dieser Knoten platziert werden.|
| --metrics            | Eine JSON-codierte Liste der Kapazitätsmetrikbeschreibungen einer Anwendung. Eine Metrik ist als ein Name definiert, der einem Satz von Kapazitäten für jeden Knoten zugeordnet ist, auf dem die Anwendung vorhanden ist.|
| --min-node-count     | Die Mindestanzahl von Knoten, für die Service Fabric Kapazität für diese Anwendung reserviert. Dies bedeutet nicht, dass die Dienste dieser Anwendung auf allen dieser Knoten platziert werden.|
| --parameters         | Eine JSON-codierte Liste mit Anwendungsparameterwerten, die beim Erstellen der Anwendung angewendet werden sollen.|
| --timeout -t         | Servertimeout in Sekunden.  Standardwert: 60.|

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug              | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden.|
| --help -h            | Zeigt diese Hilfemeldung an und beendet.|
| --output -o          | Das Ausgabeformat.  Zulässige Werte sind: json, jsonc, table, tsv.  Standardwert: json.|
| --query              | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter http://jmespath.org/.|
| --verbose            | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen.|

## <a name="sfctl-application-delete"></a>sfctl application delete
Löscht eine vorhandene Service Fabric-Anwendung.

Löscht eine vorhandene Service Fabric-Anwendung. Eine Anwendung muss erstellt worden sein, bevor sie gelöscht werden kann. Wird eine Anwendung gelöscht, werden alle Dienste gelöscht, die Bestandteil der Anwendung sind. Service Fabric versucht standardmäßig, Dienstreplikate auf ordnungsgemäße Weise zu schließen und den Dienst dann zu löschen. Wenn es aber Probleme mit dem ordnungsgemäßen Schließen eines Replikats gibt, kann der Löschvorgang sehr lange dauern oder abstürzen. Verwenden Sie das optionale Flag „ForceRemove“, um die ordnungsgemäße Schließsequenz zu überspringen und das Löschen der Anwendung samt aller ihrer Dienste zu erzwingen.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-id [erforderlich]| Die Identität (ID) der Anwendung. Dies ist üblicherweise der vollständige Name der Anwendung ohne das URI-Schema „fabric:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „~“ als Trennzeichen verwendet. Hat eine Anwendung beispielsweise den Namen „fabric://meineapp/app1“, hat die Anwendungsidentität in 6.0 und höher den Wert „meineapp~app1“ und in früheren Versionen den Wert „meineapp/app1“.|
| --force-remove          | Erzwingt ein Entfernen einer Service Fabric-Anwendung oder eines Service Fabric-Diensts, ohne den Ablauf für das ordnungsgemäße Herunterfahren zu durchlaufen. Dieser Parameter kann für ein erzwungenes Löschen einer Anwendung oder eines Diensts verwendet werden, für die oder den beim Löschen ein Timeout aufgetreten ist, weil Probleme im Dienstcode ein ordnungsgemäßes Schließen von Replikaten verhindern.|
| --timeout -t            | Servertimeout in Sekunden.  Standardwert: 60.|

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug                 | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden.|
| --help -h               | Zeigt diese Hilfemeldung an und beendet.|
| --output -o             | Das Ausgabeformat.  Zulässige Werte sind: json, jsonc, table, tsv.  Standardwert: json.|
| --query                 | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter http://jmespath.org/.|
| --verbose               | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen.|

## <a name="sfctl-application-deployed"></a>sfctl application deployed
Ruft die Informationen zu einer Anwendung ab, die auf einem Service Fabric-Knoten bereitgestellt wird.

Ruft die Informationen zu einer Anwendung ab, die auf einem Service Fabric-Knoten bereitgestellt wird.  Diese Abfrage gibt Systemanwendungsinformationen zurück, wenn die angegebene Anwendungs-ID für eine Systemanwendung steht. Die Ergebnisse umfassen bereitgestellte Anwendungen mit den Status „Aktiv“, „Aktiviert“ und „Herunterladen“. Für diese Abfrage muss der Knotenname einem Knoten im Cluster entsprechen. Die Abfrage führt zu einem Fehler, wenn der angegebene Knotenname auf keinen aktiven Service Fabric-Knoten im Cluster verweist.
     
### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-id [erforderlich]| Die Identität (ID) der Anwendung. Dies ist üblicherweise der vollständige Name der Anwendung ohne das URI-Schema „fabric:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „~“ als Trennzeichen verwendet. Hat eine Anwendung beispielsweise den Namen „fabric://meineapp/app1“, hat die Anwendungsidentität in 6.0 und höher den Wert „meineapp~app1“ und in früheren Versionen den Wert „meineapp/app1“.|
| --node-name [erforderlich]| Der Name des Knotens.|
| --timeout -t            | Servertimeout in Sekunden.  Standardwert: 60.|

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug                 | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden.|
| --help -h               | Zeigt diese Hilfemeldung an und beendet.|
| --output -o             | Das Ausgabeformat.  Zulässige Werte sind: json, jsonc, table, tsv.  Standardwert: json.|
| --query                 | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter http://jmespath.org/.|
| --verbose               | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen.|

## <a name="sfctl-application-health"></a>sfctl application health
Ruft die Integrität der Service Fabric-Anwendung ab.

Gibt den Integritätsstatus der Service Fabric-Anwendung zurück. In der Antwort wird der Integritätsstatus als „Ok“, „Error“ oder „Warning“ gemeldet. Wird die Entität nicht im Integritätsspeicher gefunden, wird ein Fehler zurückgegeben.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-id [erforderlich]| Die Identität (ID) der Anwendung. Dies ist üblicherweise der vollständige Name der Anwendung ohne das URI-Schema „fabric:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „~“ als Trennzeichen verwendet. Hat eine Anwendung beispielsweise den Namen „fabric://meineapp/app1“, hat die Anwendungsidentität in 6.0 und höher den Wert „meineapp~app1“ und in früheren Versionen den Wert „meineapp/app1“.|
| --deployed-applications-health-state-filter| Ermöglicht es, die im Ergebnis der Anwendungsintegritätsabfrage zurückgegebenen Integritätsstatusobjekte der bereitgestellten Anwendungen anhand des Integritätsstatus zu filtern. Die möglichen Werte für diesen Parameter entsprechen dem jeweiligen ganzzahligen Wert von einem der folgenden Integritätsstatus. Es werden nur bereitgestellte Anwendungen zurückgegeben, die dem Filter entsprechen. Alle bereitgestellten Anwendungen werden verwendet, um den aggregierten Integritätsstatus auszuwerten. Ist kein Filter angegeben, werden alle Einträge zurückgegeben. Ein Statuswert ist eine kennzeichenbasierte Enumeration, sodass der Wert eine Kombination der Werte sein kann, die mit dem bitweisen ODER-Operator abgerufen werden. Ist der angegebene Wert beispielsweise gleich „6“, wird der Integritätsstatus der bereitgestellten Anwendungen zurückgegeben, für die „HealthState“ den Wert für OK (2) oder Warning (4) hat. – Default: Standardwert. Stimmt mit jedem Integritätsstatus (HealthState) überein. Der Wert ist gleich null. – None: Filter, der mit keinem Wert für „HealthState“ übereinstimmt. Wird verwendet, um keine Ergebnisse für eine angegebene Statussammlung zurückzugeben. Der Wert ist gleich „1“. – Ok: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Ok“ hat. Der Wert ist gleich „2“. – Warning: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Warning“ hat. Der Wert ist gleich „4“. – Error: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Error“ hat. Der Wert ist gleich „8“. – All: Filter, der mit Eingaben übereinstimmt, die einen beliebigen Wert für „HealthState“ haben. Der Wert ist gleich „65535“.|
| --events-health-state-filter            | Ermöglicht das Filtern der Collection zurückgegebener HealthEvent-Objekte anhand des Integritätsstatus. Die möglichen Werte für diesen Parameter entsprechen dem jeweiligen ganzzahligen Wert von einem der folgenden Integritätsstatus. Es werden nur Ereignisse zurückgegeben, die dem Filter entsprechen. Alle Ereignisse werden verwendet, um den aggregierten Integritätsstatus auszuwerten. Ist kein Filter angegeben, werden alle Einträge zurückgegeben. Ein Statuswert ist eine kennzeichenbasierte Enumeration, sodass der Wert eine Kombination der Werte sein kann, die mit dem bitweisen ODER-Operator abgerufen werden. Ist der angegebene Wert beispielsweise gleich „6“, werden alle Ereignisse zurückgegeben, für die „HealthState“ den Wert für OK (2) oder Warnung (4) hat. – Default: Standardwert. Stimmt mit jedem Integritätsstatus (HealthState) überein. Der Wert ist gleich null. – None: Filter, der mit keinem Wert für „HealthState“ übereinstimmt. Wird verwendet, um keine Ergebnisse für eine angegebene Statussammlung zurückzugeben. Der Wert ist gleich „1“. – Ok: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Ok“ hat. Der Wert ist gleich „2“. – Warning: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Warning“ hat. Der Wert ist gleich „4“. – Error: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Error“ hat. Der Wert ist gleich „8“. – All: Filter, der mit Eingaben übereinstimmt, die einen beliebigen Wert für „HealthState“ haben. Der Wert ist gleich „65535“.|
| --exclude-health-statistics | Gibt an, ob die Integritätsstatistiken als Bestandteil des Abfrageergebnisses zurückgegeben werden sollen. Der Standardwert ist „FALSE“. Die Statistiken zeigen die Anzahl von untergeordneten Entitäten, die einen der Integritätsstatus „OK“, „Warning“ oder „Error“ haben.|
| --services-health-state-filter          | Ermöglicht es, die Integritätsstatusobjekte der Dienste, die im Ergebnis einer Dienstintegritätsabfrage zurückgegeben werden, anhand des Integritätsstatus zu filtern. Die möglichen Werte für diesen Parameter entsprechen dem jeweiligen ganzzahligen Wert von einem der folgenden Integritätsstatus. Es werden nur Dienste zurückgegeben, die dem Filter entsprechen. Alle Dienste werden verwendet, um den aggregierten Integritätsstatus auszuwerten. Ist kein Filter angegeben, werden alle Einträge zurückgegeben. Ein Statuswert ist eine kennzeichenbasierte Enumeration, sodass der Wert eine Kombination der Werte sein kann, die mit dem bitweisen ODER-Operator abgerufen werden. Ist der angegebene Wert beispielsweise „6“, wird der Integritätsstatus der Dienste zurückgegeben, für die HealthState den Wert für OK (2) oder Warning (4) hat. – Default: Standardwert. Stimmt mit jedem Integritätsstatus (HealthState) überein. Der Wert ist gleich null. – None: Filter, der mit keinem Wert für „HealthState“ übereinstimmt. Wird verwendet, um keine Ergebnisse für eine angegebene Statussammlung zurückzugeben. Der Wert ist gleich „1“. – Ok: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Ok“ hat. Der Wert ist gleich „2“. – Warning: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Warning“ hat. Der Wert ist gleich „4“. – Error: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Error“ hat. Der Wert ist gleich „8“. – All: Filter, der mit Eingaben übereinstimmt, die einen beliebigen Wert für „HealthState“ haben. Der Wert ist gleich „65535“.|
| --timeout -t                            | Servertimeout in Sekunden.  Standardwert: 60.|

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug                                 | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden.|
| --help -h                               | Zeigt diese Hilfemeldung an und beendet.|
| --output -o                             | Das Ausgabeformat.  Zulässige Werte sind: json, jsonc, table, tsv.  Standardwert: json.|
| --query                                 | JMESPath-Abfragezeichenfolge. Weitere Informationen finden Sie unter http://jmespath.org/.|
| --verbose                               | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen.|

## <a name="sfctl-application-info"></a>sfctl application info
Ruft Informationen zu einer Service Fabric-Anwendung ab.

Gibt die Informationen zu der Anwendung zurück, die im Service Fabric-Cluster erstellt wurde oder gerade erstellt wird und deren Name mit dem übereinstimmt, der als Parameter angegebenen wurde. Die Antwort enthält den Namen, den Typ, den Status, die Parameter und weitere Details über die Anwendung.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-id [erforderlich]| Die Identität (ID) der Anwendung. Dies ist üblicherweise der vollständige Name der Anwendung ohne das URI-Schema „fabric:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „~“ als Trennzeichen verwendet. Hat eine Anwendung beispielsweise den Namen „fabric://meineapp/app1“, hat die Anwendungsidentität in 6.0 und höher den Wert „meineapp~app1“ und in früheren Versionen den Wert „meineapp/app1“.|
| --exclude-application-parameters| Das Flag, das angibt, ob die Anwendungsparameter aus dem Ergebnis ausgeschlossen werden sollen.|
| --timeout -t                 | Servertimeout in Sekunden.  Standardwert: 60.|

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug                      | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden.|
| --help -h                    | Zeigt diese Hilfemeldung an und beendet.|
| --output -o                  | Das Ausgabeformat.  Zulässige Werte sind: json, jsonc, table, tsv.             Standardwert: json.|
| --query                      | JMESPath-Abfragezeichenfolge. Weitere Informationen finden Sie unter http://jmespath.org/.|
| --verbose                    | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen.|

## <a name="sfctl-application-list"></a>sfctl application list
Ruft die Liste der Anwendungen ab, die in dem Service Fabric-Cluster erstellt wurden und mit den Filtern übereinstimmen, die als Parameter angegeben sind.

Ruft die Informationen zu den Anwendung ab, die im Service Fabric-Cluster erstellt wurden oder gerade erstellt werden und mit den Filtern übereinstimmen, die als Parameter angegebenen sind. Die Antwort enthält den Namen, den Typ, den Status, die Parameter und weitere Details über die Anwendung. Wenn die Anwendungen nicht auf eine Seite passen, werden sowohl eine Seite mit Ergebnissen als auch ein Fortsetzungstoken zurückgegeben, über das zur nächsten Seite gewechselt werden kann. Die Filter ApplicationTypeName und ApplicationDefinitionKindFilter können nicht gleichzeitig angegeben werden.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
|--application-definition-kind-filter| Wird zum Filtern nach ApplicationDefinitionKind verwendet, um eine Service Fabric-Anwendung zu definieren. - Standard: Standardwert, der die gleiche Funktion hat wie die Auswahl von „All“. Der Wert ist „0“. – All: Filter, der mit Eingaben eines beliebigen Werts „ApplicationDefinitionKind“ übereinstimmt. Der Wert ist gleich „65535“. - ServiceFabricApplicationDescription: Filter, der mit Eingaben übereinstimmt, für die ApplicationDefinitionKind den Wert „ServiceFabricApplicationDescription“ hat. Der Wert ist gleich „1“. – Compose: Filter, der mit Eingaben übereinstimmt, für die „ApplicationDefinitionKind“ den Wert „Compose“ hat. Der Wert ist gleich „2“.|
| --application-type-name      | Der Anwendungstypname, der zum Filtern der Anwendungen verwendet wird, die abgefragt werden sollen. Dieser Wert darf die Version des Anwendungstyps nicht enthalten.|
| --continuation-token         | Der Parameter „continuation-token“ (Fortsetzungstoken) wird dazu verwendet, den nächsten Satz von Ergebnissen abzurufen. Ein Fortsetzungstoken mit einem nicht leeren Wert wird in die Antwort der API eingefügt, wenn die Ergebnisse aus dem System nicht in eine einzige Antwort passen. Wird dieser Wert an den nächsten API-Aufruf übergeben, gibt die API den nächsten Satz von Ergebnissen zurück. Gibt es keine weiteren Ergebnisse, enthält das Fortsetzungstoken keinen Wert. Der Wert dieses Parameters darf nicht als URL codiert sein.|
| --exclude-application-parameters| Das Flag, das angibt, ob die Anwendungsparameter aus dem Ergebnis ausgeschlossen werden sollen.|
| --max-results|Die maximale Anzahl von Ergebnissen, die als Teil der seitenweisen Abfragen zurückgegeben werden sollen. Dieser Parameter definiert die obere Grenze für die Anzahl von zurückgegebenen Ergebnissen. Es können weniger Ergebnisse zurückgegeben werden, als dieser maximalen Anzahl entspricht. Dies ist der Fall, wenn die Ergebnisse wegen der Größenbeschränkungen, die für Meldungen in der Konfiguration definiert sind, nicht in die jeweilige Meldung passen. Ist dieser Parameter gleich null oder nicht angegeben, enthalten die seitenweisen Abfragen so viele Ergebnisse, wie in die Rückgabemeldung passen.|
| --timeout -t                 | Servertimeout in Sekunden.  Standardwert: 60.|

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug                      | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden.|
| --help -h                    | Zeigt diese Hilfemeldung an und beendet.|
| --output -o                  | Das Ausgabeformat.  Zulässige Werte sind: json, jsonc, table, tsv.             Standardwert: json.|
| --query                      | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter http://jmespath.org/.|
| --verbose                    | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen.|

## <a name="sfctl-application-load"></a>sfctl application load
Ruft Lastinformationen zu einer Service Fabric-Anwendung ab.

Gibt die Lastinformationen der Anwendung zurück, die im Service Fabric-Cluster erstellt wurde oder gerade erstellt wird und deren Name mit dem Namen übereinstimmt, der als Parameter angegebenen wurde. Die Antwort enthält den Namen, die minimale Anzahl der Knoten, die maximale Anzahl der Knoten, die Anzahl der Knoten, die die App aktuell belegt sowie die Metrikinformation der Anwendungslast der Anwendung.

### <a name="arguments"></a>Argumente
|Argument|BESCHREIBUNG|
| --- | --- |
|--application-id [erforderlich]| Die Identität (ID) der Anwendung. Dies ist üblicherweise der vollständige Name der Anwendung ohne das URI-Schema „fabric:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „~“ als Trennzeichen verwendet. Hat eine Anwendung beispielsweise den Namen „fabric://meineapp/app1“, hat die Anwendungsidentität in 6.0 und höher den Wert „meineapp~app1“ und in früheren Versionen den Wert „meineapp/app1“. |
| --timeout -t               | Servertimeout in Sekunden.  Standardwert: 60.|

### <a name="global-arguments"></a>Globale Argumente
|Argument|BESCHREIBUNG|
| --- | --- |
|--debug                    | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden.|
    --help -h                  | Zeigt diese Hilfemeldung an und beendet.|
    --output -o                | Das Ausgabeformat.  Zulässige Werte sind: json, jsonc, table, tsv.  Standardwert: json.|
    --query                    | JMESPath-Abfragezeichenfolge. Weitere Informationen finden Sie unter http://jmespath.org/.|
    --verbose                  | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen.|

## <a name="sfctl-application-manifest"></a>sfctl application manifest
Ruft das Manifest ab, das einen Anwendungstyp beschreibt.
        
Ruft das Manifest ab, das einen Anwendungstyp beschreibt. Die Antwort enthält den XML-Code des Anwendungsmanifests als Zeichenfolge.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-type-name [erforderlich]| Der Name des Anwendungstyps.|
| --application-type-version [erforderlich]| Die Version des Anwendungstyps.|
| --timeout -t                      | Servertimeout in Sekunden.  Standardwert: 60.|

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug                           | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden.|
| --help -h                         | Zeigt diese Hilfemeldung an und beendet.|
| --output -o                       | Das Ausgabeformat.  Zulässige Werte sind: json, jsonc, table, tsv.                  Standardwert: json.|
| --query                           | JMESPath-Abfragezeichenfolge. Weitere Informationen finden Sie unter http://jmespath.org/.|
| --verbose                         | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen.|

## <a name="sfctl-application-provision"></a>sfctl application provision
Stellt einen Service Fabric-Anwendungstyp mithilfe des SFPKG-Pakets im externen Speicher oder mithilfe des Anwendungspakets im Imagespeicher im Cluster bereit oder registriert ihn.

Stellt einen Service Fabric-Anwendungstyp im Cluster bereit. Dies ist erforderlich, bevor neue Anwendungen instanziiert werden können. Der Bereitstellungsvorgang kann mit dem Anwendungspaket, das durch RelativePathInImageStore angegeben wird, oder mithilfe des URI des externen SFPKG-Pakets ausgeführt werden. Mit diesem Befehl wird das Anwendungspaket nur dann aus dem Imagespeicher bereitgestellt, wenn nicht „--external-provision“ festgelegt ist.
        


### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-package-download-uri| Der Pfad zum SFPKG-Anwendungspaket, unter dem das Anwendungspaket über das HTTP- oder HTTPS-Protokoll heruntergeladen werden kann. Gilt nur für die Bereitstellung aus einem externen Speicher. Das Anwendungspaket kann in einem externen Speicher gespeichert werden, der den GET-Vorgang zum Herunterladen der Datei bereitstellt. Es werden die Protokolle HTTP und HTTPS unterstützt, und der Pfad muss Lesezugriff zulassen.|
| --application-type-build-path       | Gilt nur für Imagespeicher zur Bereitstellung. Der relative Pfad für das Anwendungspaket im Imagespeicher, der während des vorherigen Uploadvorgangs angegeben wurde. |
| --application-type-name| Gilt nur für die Bereitstellung aus einem externen Speicher. Der Anwendungstypname stellt den Namen des Anwendungstyps im Anwendungsmanifest dar.|
| --application-type-version| Gilt nur für die Bereitstellung aus einem externen Speicher. Die Anwendungstypversion stellt die Version des Anwendungstyps im Anwendungsmanifest dar.|
| --external-provision| Der Speicherort, von dem das Anwendungspaket registriert oder bereitgestellt werden kann. Gibt an, dass die Bereitstellung über ein Anwendungspaket erfolgt, das zuvor in einen externen Speicher hochgeladen wurde. Das Anwendungspaket endet mit der Erweiterung *.sfpkg.|
| --no-wait| Gibt an, ob die Bereitstellung asynchron erfolgen soll.  Bei Festlegung auf „true“ wird der Bereitstellungsvorgang beendet, wenn die Anforderung vom System akzeptiert wurde. Der Bereitstellungsvorgang wird dann ohne Zeitlimit fortgesetzt. Der Standardwert ist „false“. Bei großen Anwendungspaketen wird empfohlen, den Wert auf „true“ festzulegen.|
| --timeout -t                      | Servertimeout in Sekunden.  Standardwert: 60.|

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug                              | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden.|
| --help -h                            | Zeigt diese Hilfemeldung an und beendet.|
| --output -o                          | Das Ausgabeformat.  Zulässige Werte sind: json, jsonc, table, tsv.  Standardwert: json.|
| --query                              | JMESPath-Abfragezeichenfolge. Weitere Informationen finden Sie unter http://jmespath.org/.|
| --verbose                            | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen.|

## <a name="sfctl-application-type"></a>sfctl application type

Ruft die Liste der Anwendungstypen im Service Fabric-Cluster ab, die genau mit dem angegebenen Namen übereinstimmen.

Gibt die Informationen über die Anwendungstypen zurück, die im Service Fabric-Cluster bereitgestellt oder zum Bereitstellen vorbereitet werden. Diese Ergebnisse bestehen aus Anwendungstypen, deren Namen genau mit dem übereinstimmen, der als Parameter angegeben ist, und die den angegebenen Abfrageparametern entsprechen. Es werden alle Versionen des Anwendungstyps zurückgegeben, der mit dem Namen des Anwendungstyps übereinstimmt. Dabei wird jede Version als ein Anwendungstyp zurückgegeben. Die Antwort enthält den Namen, die Version, den Status und weitere Details über den Anwendungstyp. Dies ist eine seitenweise Abfrage, d. h., wenn nicht alle Anwendungstypen auf eine Seite passen, werden sowohl eine Seite mit Ergebnissen als auch ein Fortsetzungstoken zurückgegeben, über das zur nächsten Seite gewechselt werden kann. Wenn es beispielsweise 10 Anwendungstypen gibt, auf eine Seite aber nur die ersten 3 Anwendungstypen passen, oder die maximale Anzahl von Ergebnissen auf „3“ festgelegt wurde, wird „3“ zurückgegeben. Wenn Sie auf den Rest der Ergebnisse zugreifen möchten, rufen Sie die anschließenden Seiten ab, indem Sie in der nächsten Abfrage das zurückgegebene Fortsetzungstoken verwenden. Es wird ein leeres Fortsetzungstoken zurückgegeben, wenn keine anschließenden Seiten vorhanden sind.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-type-name [erforderlich]| Der Name des Anwendungstyps.|
| --application-type-version        | Die Version des Anwendungstyps.|
| --continuation-token           | Der Parameter „continuation-token“ (Fortsetzungstoken) wird dazu verwendet, den nächsten Satz von Ergebnissen abzurufen. Ein Fortsetzungstoken mit einem nicht leeren Wert wird in die Antwort der API eingefügt, wenn die Ergebnisse aus dem System nicht in eine einzige Antwort passen. Wird dieser Wert an den nächsten API-Aufruf übergeben, gibt die API den nächsten Satz von Ergebnissen zurück. Gibt es keine weiteren Ergebnisse, enthält das Fortsetzungstoken keinen Wert. Der Wert dieses Parameters darf nicht als URL codiert sein.|
| --exclude-application-parameters  | Das Flag, das angibt, ob die Anwendungsparameter aus dem Ergebnis ausgeschlossen werden sollen.|
| --max-results                  | Die maximale Anzahl von Ergebnissen, die als Teil der seitenweisen Abfragen zurückgegeben werden sollen. Dieser Parameter definiert die obere Grenze für die Anzahl von zurückgegebenen Ergebnissen. Es können weniger Ergebnisse zurückgegeben werden, als es dieser maximalen Anzahl entspricht. Dies ist der Fall, wenn die Ergebnisse wegen der Größenbeschränkungen, die für Meldungen in der Konfiguration definiert sind, nicht in die jeweilige Meldung passen. Ist dieser Parameter gleich null oder nicht angegeben, enthält die seitenweise Abfrage so viele Ergebnisse, wie in die Rückgabemeldung passen.|
| --timeout -t                   | Servertimeout in Sekunden.  Standardwert: 60.|

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug                        | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden.|
| --help -h                      | Zeigt diese Hilfemeldung an und beendet.|
| --output -o                    | Das Ausgabeformat.  Zulässige Werte sind: json, jsonc, table, tsv.               Standardwert: json.|
| --query                        | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter http://jmespath.org/.|
| --verbose                      | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen.|

## <a name="sfctl-application-unprovision"></a>sfctl application unprovision
Entfernt einen Service Fabric-Anwendungstyp aus dem Cluster oder hebt die Registrierung eines Service Fabric-Anwendungstyps für den Cluster auf.

Entfernt einen Service Fabric-Anwendungstyp aus dem Cluster oder hebt die Registrierung eines Service Fabric-Anwendungstyps für den Cluster auf. Dieser Vorgang kann nur ausgeführt werden, wenn alle Anwendungsinstanzen des Anwendungstyps gelöscht wurden. Sobald die Registrierung des Anwendungstyps aufgehoben ist, kann keine neue Anwendungsinstanz mehr für diesen bestimmten Anwendungstyp erstellt werden.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-type-name [erforderlich]| Der Name des Anwendungstyps.|
| --application-type-version [erforderlich]| Die Version des Anwendungstyps, wie sie im Anwendungsmanifest definiert ist.|
|--async-parameter                    | Das Flag gibt an, ob das Aufheben der Bereitstellung asynchron erfolgen soll. Bei Festlegung auf „true“ wird der Aufhebungsvorgang der Bereitstellung beendet, wenn die Anforderung vom System akzeptiert wurde. Der Vorgang der Aufhebung der Bereitstellung wird dann ohne Zeitlimit fortgesetzt. Der Standardwert ist „false“. Es wird jedoch empfohlen, den Wert für große bereitgestellte Anwendungspakete auf „true“ festzulegen.|
| --timeout -t                      | Servertimeout in Sekunden.  Standardwert: 60.|

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug                           | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden.|
| --help -h                         | Zeigt diese Hilfemeldung an und beendet.|
| --output -o                       | Das Ausgabeformat.  Zulässige Werte sind: json, jsonc, table, tsv.                  Standardwert: json.|
| --query                           | JMESPath-Abfragezeichenfolge. Weitere Informationen finden Sie unter http://jmespath.org/.|
| --verbose                         | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen.|

## <a name="sfctl-application-upgrade"></a>sfctl application upgrade
Startet ein Aktualisieren einer Anwendung im Service Fabric-Cluster.

Überprüft die angegebenen Upgradeparameter der Anwendung und beginnt mit dem Aktualisieren der Anwendung, wenn die Parameter gültig sind. Beim Aktualisieren wird die vorhandene Anwendungsbeschreibung durch die Upgradebeschreibung ersetzt. Dies bedeutet, dass die vorhandenen Parameter mit der leeren Liste der Parameter überschrieben werden, wenn die Parameter nicht angegeben sind. Dies führt dazu, dass für die Anwendung die Standardwerte der Parameter aus dem Anwendungsmanifest verwendet werden.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --app-id [erforderlich]| Die Identität (ID) der Anwendung. Dies ist üblicherweise der vollständige Name der Anwendung ohne das URI-Schema „fabric:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „~“ als Trennzeichen verwendet. Hat eine Anwendung beispielsweise den Namen „fabric://meineapp/app1“, hat die Anwendungsidentität in 6.0 und höher den Wert „meineapp~app1“ und in früheren Versionen den Wert „meineapp/app1“.|
| -- app-version [erforderlich]| Zielanwendungsversion|
| --parameters [erforderlich]| Eine JSON-codierte Liste mit Anwendungsparameterwerten, die beim Upgrade der Anwendung angewendet werden sollen.|
| --default-service-health-policy| Eine JSON-codierte Spezifikation der Integritätsrichtlinie, die standardmäßig zur Auswertung der Integrität eines Diensttyps verwendet wird.|
| --failure-action            | Die Aktion, die ausgeführt werden soll, wenn ein überwachtes Upgrade Verstöße gegen die Überwachungsrichtlinie oder Integritätsrichtlinie erkennt.|
| --force-restart             | Ein erzwungener Neustart wird während eines Upgrades durchgeführt, auch wenn sich die Codeversion nicht geändert hat.|
| --health-check-retry-timeout| Die Zeitspanne, in der Integritätsauswertungen wiederholt werden, wenn die Anwendung oder der Cluster fehlerhaft ist. Danach wird die Fehleraktion durchgeführt. Gemessen in Millisekunden.  Standardwert: PT0H10M0S.|
| --health-check-stable-duration | Die Zeitspanne, während der die Anwendung oder der Cluster fehlerfrei bleiben muss, bevor das Upgrade mit der nächsten Upgradedomäne fortgesetzt wird.            Gemessen in Millisekunden.  Standardwert: PT0H2M0S.|
| --health-check-wait-duration| Die Zeitspanne, während der nach dem Abschließen einer Upgradedomäne gewartet werden soll, bevor Integritätsrichtlinien angewendet werden. Gemessen in Millisekunden.            Standard: 0.|
| --max-unhealthy-apps        | Der maximal zulässige Prozentsatz von bereitgestellten Anwendungen, die fehlerhaft sind. Dieser wird als Zahl zwischen 0 und 100 dargestellt.|
| --mode                      | Der Modus, der zum Überwachen der Integrität während eines parallelen Upgrades verwendet wird.            Standardwert: UnmonitoredAuto.|
| --replica-set-check-timeout | Die maximale Zeitspanne, während der die Verarbeitung einer Upgradedomäne blockiert und Verfügbarkeitsverlust verhindert wird, wenn es unerwartete Probleme gibt. Dieser Wert wird in Sekunden gemessen.|
| --service-health-policy     | JSON-codierter Überblick mit einer Diensttypintegritätsrichtlinie pro Diensttypname. Der Überblick ist standardmäßig leer.|
| --timeout -t                | Servertimeout in Sekunden.  Standardwert: 60.|
| --upgrade-domain-timeout    | Die Zeitspanne, während der jede Upgradedomäne abgeschlossen werden muss, bevor die Fehleraktion (FailureAction) ausgeführt wird. Gemessen in Millisekunden.  Standardwert: P10675199DT02H48M05.4775807S.|
| --upgrade-timeout           | Die Zeitspanne, während der das gesamte Upgrade abgeschlossen werden muss, bevor die Fehleraktion (FailureAction) ausgeführt wird. Gemessen in Millisekunden.  Standardwert: P10675199DT02H48M05.4775807S.|
| --warning-as-error          | Integritätsauswertungswarnungen werden mit demselben Schweregrad behandelt wie Fehler.|

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug                     | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden.|
| --help -h                   | Zeigt diese Hilfemeldung an und beendet.|
| --output -o                 | Das Ausgabeformat.  Zulässige Werte sind: json, jsonc, table, tsv.            Standardwert: json.|
| --query                     | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter http://jmespath.org/.|
| --verbose                   | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen.|

## <a name="sfctl-application-upload"></a>sfctl application upload
Kopiert ein Service Fabric-Anwendungspaket in den Imagespeicher.

Optional kann der Uploadfortschritt für jede Datei im Paket angezeigt werden. Der jeweilige Uploadfortschritt wird an `stderr` gesendet.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --path [erforderlich]| Pfad zum lokalen Anwendungspaket.|
|--imagestore-string| Zielimagespeicher, in den das Anwendungspaket hochgeladen werden soll.  Standardwert: fabric:ImageStore.|
| --show-progress  | Zeigt den Fortschritt des Dateiuploads für große Pakete an.|

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug       | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden.|
| --help -h     | Zeigt diese Hilfemeldung an und beendet.|
| --output -o   | Das Ausgabeformat.  Zulässige Werte sind: json, jsonc, table, tsv.  Standardwert: json.|
| --query       | JMESPath-Abfragezeichenfolge. Weitere Informationen finden Sie unter http://jmespath.org/.|
| --verbose     | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen.|

## <a name="next-steps"></a>Nächste Schritte
- [Einrichten](service-fabric-cli.md) der Service Fabric-Befehlszeilenschnittstelle
- Informationen zum Verwenden der Service Fabric-Befehlszeilenschnittstelle mit den [Beispielskripts](/azure/service-fabric/scripts/sfctl-upgrade-application)
