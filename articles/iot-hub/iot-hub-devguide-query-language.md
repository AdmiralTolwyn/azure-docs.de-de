---
title: Grundlegendes zur Azure IoT Hub-Abfragesprache | Microsoft Docs
description: 'Entwicklerhandbuch: Beschreibung der SQL-ähnlichen IoT Hub-Abfragesprache, die zum Abrufen von Informationen zu Geräte-/Modulzwillingen und Aufträgen von IoT Hub verwendet wird.'
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: elioda
ms.openlocfilehash: 321d70a04e3c524e578a01e8531d63733d088c3f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444183"
---
# <a name="iot-hub-query-language-for-device-and-module-twins-jobs-and-message-routing"></a>IoT Hub-Abfragesprache für Geräte- und Modulzwillinge, Aufträge und Nachrichtenrouting

IoT Hub bietet eine leistungsstarke, SQL-ähnliche Sprache zum Abrufen von Informationen zu [Gerätezwillingen][lnk-twins], [Aufträgen][lnk-jobs] und [Nachrichtenrouting][lnk-devguide-messaging-routes]. Dieser Artikel enthält Folgendes:

* Eine Einführung in die wichtigsten Features der IoT Hub-Abfragesprache
* Eine ausführliche Beschreibung der Sprache

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="device-and-module-twin-queries"></a>Abfragen von Geräte- und Modulzwillingen
[Gerätezwillinge][lnk-twins] und Modulzwillinge können beliebige JSON-Objekte als Tags und Eigenschaften enthalten. In IoT Hub können Sie Geräte- und Modulzwillinge als einzelnes JSON-Dokument abfragen, das alle Informationen zum Zwilling enthält.
Angenommen, Ihre IoT Hub-Gerätezwillinge weisen die folgende Struktur auf (Modulzwillinge sehen ähnlich aus, enthalten aber zusätzlich eine Modul-ID):

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00",    
    "connectionState": "Disconnected",    
    "lastActivityTime": "0001-01-01T00:00:00",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",    
    "x509Thumbprint": {    
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "tags": {
        "location": {
            "region": "US",
            "plant": "Redmond43"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300
            },
            "$metadata": {
            ...
            },
            "$version": 4
        },
        "reported": {
            "connectivity": {
                "type": "cellular"
            },
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300,
                "status": "Success"
            },
            "$metadata": {
            ...
            },
            "$version": 7
        }
    }
}
```

### <a name="device-twin-queries"></a>Gerätezwillingabfragen

IoT Hub macht die Gerätezwillinge als eine Dokumentensammlung namens **Geräte** verfügbar.
Die folgende Abfrage ruft also die gesamte Gruppe von Gerätezwillingen ab:

```sql
SELECT * FROM devices
```

> [!NOTE]
> [Azure IoT SDKs][lnk-hub-sdks] unterstützen das Paging von umfangreichen Ergebnissen.

IoT Hub ermöglicht beim Abrufen von Gerätezwillingen das Filtern mit beliebigen Bedingungen. Verwenden Sie z. B. die folgende Abfrage, um Gerätezwillinge zu erhalten, bei denen das Tag **location.region** auf **US** festgelegt ist:

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
```

Boolesche Operatoren und arithmetische Vergleiche werden ebenfalls unterstützt. Verwenden Sie z. B. die folgende Abfrage, um Gerätezwillinge in den USA abzurufen, die so konfiguriert sind, dass sie Telemetriedaten häufiger als jede Minute senden:

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
    AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60
```

Zur Vereinfachung können auch Arraykonstanten mit den Operatoren **IN** und **NIN** (nicht IN) verwendet werden. Verwenden Sie z. B. die folgende Abfrage, um Gerätezwillinge abzurufen, die WLAN- oder Kabelverbindungen melden:

```sql
SELECT * FROM devices
WHERE properties.reported.connectivity IN ['wired', 'wifi']
```

Es ist häufig erforderlich, alle Gerätezwillinge zu ermitteln, die eine bestimmte Eigenschaft enthalten. IoT Hub unterstützt zu diesem Zweck die Funktion `is_defined()`. Verwenden Sie z. B. die folgende Abfrage, um Gerätezwillinge abzurufen, die die `connectivity`-Eigenschaft definieren:

```SQL
SELECT * FROM devices
WHERE is_defined(properties.reported.connectivity)
```

Die vollständige Referenz zu den Filtermöglichkeiten finden Sie im Abschnitt zur [WHERE-Klausel][lnk-query-where].

Gruppierungen und Aggregationen werden ebenfalls unterstützt. Verwenden Sie z. B. die folgende Abfrage, um die Anzahl von Geräten mit dem jeweiligen Status für die Telemetriekonfiguration zu ermitteln:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

Diese Gruppierungsabfrage würde ein ähnliches Ergebnis wie im folgenden Beispiel zurückgegeben:

```json
[
    {
        "numberOfDevices": 3,
        "status": "Success"
    },
    {
        "numberOfDevices": 2,
        "status": "Pending"
    },
    {
        "numberOfDevices": 1,
        "status": "Error"
    }
]
```

In diesem Beispiel meldeten drei Geräte eine erfolgreiche Konfiguration, zwei wenden die Konfiguration noch an und ein Gerät hat einen Fehler gemeldet.

Mithilfe von Projektionsabfragen können Entwickler nur die interessierenden Eigenschaften zurückgeben. Um beispielsweise den Zeitpunkt der letzten Aktivität aller getrennten Geräte abzurufen, verwenden Sie die folgende Abfrage:

```sql
SELECT LastActivityTime FROM devices WHERE status = 'enabled'
```

### <a name="module-twin-queries"></a>Modulzwillingsabfragen

Das Abfragen von Modulzwillingen ähnelt dem Abfragen von Gerätezwillingen. Allerdings wird dabei eine andere Sammlung/ein anderer Namespace verwendet. Anstelle von „from devices“ können Sie folgende Abfrage verwenden:

```sql
SELECT * FROM devices.modules
```

Eine Verknüpfung zwischen den Sammlungen „devices“ und „devices.modules“ ist nicht zulässig. Wenn Sie Modulzwillinge geräteübergreifend abfragen möchten, müssen dazu Tags verwendet werden. Die folgende Abfrage gibt alle Modulzwillinge auf allen Geräten mit dem Status „scanning“ zurück:

```sql
Select * from devices.modules where properties.reported.status = 'scanning'
```

Die folgende Abfrage gilt alle Modulzwillinge mit dem Status „scanning“ nur für die angegebene Teilmenge der Geräte zurück:

```sql
Select * from devices.modules where properties.reported.status = 'scanning' and deviceId IN ('device1', 'device2')  
```

### <a name="c-example"></a>C#-Beispiel
Die Abfragefunktion wird durch das [C#-Dienst-SDK][lnk-hub-sdks] in der **RegistryManager**-Klasse verfügbar gemacht.
Es folgt ein Beispiel für eine einfache Abfrage:

```csharp
var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
while (query.HasMoreResults)
{
    var page = await query.GetNextAsTwinAsync();
    foreach (var twin in page)
    {
        // do work on twin object
    }
}
```

Das Objekt **query** wird mit einer Seitengröße (bis zu 100) instanziiert. Dann erfolgt der Abruf mehrerer Seiten, indem mehrmals die **GetNextAsTwinAsync**-Methoden aufgerufen werden.

Das Abfrageobjekt macht mehrere **Next**-Werte verfügbar, abhängig von der Deserialisierungsoption, die von der Abfrage benötigt werden. Beispielsweise Gerätezwillings- bzw. Auftragsobjekte oder einfachen JSON-Text bei der Verwendung von Projektionen.

### <a name="nodejs-example"></a>Node.js-Beispiel
Die Abfragefunktion wird durch das [Azure IoT-Dienst-SDK für Node.js][lnk-hub-sdks] im **Registry-Objekt** verfügbar gemacht.
Es folgt ein Beispiel für eine einfache Abfrage:

```nodejs
var query = registry.createQuery('SELECT * FROM devices', 100);
var onResults = function(err, results) {
    if (err) {
        console.error('Failed to fetch the results: ' + err.message);
    } else {
        // Do something with the results
        results.forEach(function(twin) {
            console.log(twin.deviceId);
        });

        if (query.hasMoreResults) {
            query.nextAsTwin(onResults);
        }
    }
};
query.nextAsTwin(onResults);
```

Das Objekt **query** wird mit einer Seitengröße (bis zu 100) instanziiert. Dann erfolgt der Abruf mehrerer Seiten, indem mehrmals die **nextAsTwin**-Methode aufgerufen wird.

Das Abfrageobjekt macht mehrere **Next**-Werte verfügbar, abhängig von der Deserialisierungsoption, die von der Abfrage benötigt werden. Beispielsweise Gerätezwillings- bzw. Auftragsobjekte oder einfachen JSON-Text bei der Verwendung von Projektionen.

### <a name="limitations"></a>Einschränkungen

> [!IMPORTANT]
> Abfrageergebnisse können mit einigen Minuten Verzögerung im Vergleich zu den aktuellen Werten in Gerätezwillingen ausgegeben werden. Wenn Sie einzelne Gerätezwillinge per ID abfragen, verwenden Sie die API zum Abrufen von Gerätezwillingen. Diese API enthält immer die aktuellen Werte und verfügt über höhere Einschränkungsgrenzwerte.

Derzeit werden Vergleiche nur zwischen primitiven Typen (keine Objekte) unterstützt. `... WHERE properties.desired.config = properties.reported.config` wird beispielsweise nur unterstützt, wenn diese Eigenschaften über primitive Werte verfügen.

## <a name="get-started-with-jobs-queries"></a>Erste Schritte mit Auftragsabfragen

[Aufträge][lnk-jobs] bieten eine Möglichkeit zum Ausführen von Vorgängen für Gerätegruppen. Jeder Gerätezwilling enthält die Informationen der auf ihn bezogenen Aufträge in einer Sammlung namens **jobs**.
Die logische Struktur entspricht der folgenden:

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "tags": {
        ...
    },
    "properties": {
        ...
    },
    "jobs": [
        {
            "deviceId": "myDeviceId",
            "jobId": "myJobId",
            "jobType": "scheduleTwinUpdate",
            "status": "completed",
            "startTimeUtc": "2016-09-29T18:18:52.7418462",
            "endTimeUtc": "2016-09-29T18:20:52.7418462",
            "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
            "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
            "outcome": {
                "deviceMethodResponse": null
            }
        },
        ...
    ]
}
```

Diese Sammlung kann derzeit in der IoT Hub-Abfragesprache über **devices.jobs** abgefragt werden.

> [!IMPORTANT]
> Die „jobs“-Eigenschaft wird derzeit nie zurückgegeben, wenn Gerätezwillinge abgefragt werden. Hierbei handelt es sich um Abfragen, die „FROM devices“ enthalten. Auf die „jobs“-Eigenschaft kann nur direkt mit Abfragen unter Verwendung von `FROM devices.jobs` zugegriffen werden.
>
>

Um alle Aufträge (vergangene und geplante) abzurufen, die ein einzelnes Gerät betreffen, können Sie z.B. die folgende Abfrage verwenden:

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.deviceId = 'myDeviceId'
```

Beachten Sie, wie diese Abfrage den gerätespezifischen Status (und möglicherweise die direkte Antwortmethode) jedes zurückgegebenen Auftrags bereitstellt.
Es ist auch möglich, mit beliebigen booleschen Bedingungen alle Objekteigenschaften in der Sammlung **devices.jobs** zu filtern.
Verwenden Sie z. B. die folgende Abfrage, um alle abgeschlossenen Aktualisierungsaufträge von Gerätezwillingen abzurufen, die nach September 2016 für ein bestimmtes Gerät erstellt wurden:

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.deviceId = 'myDeviceId'
    AND devices.jobs.jobType = 'scheduleTwinUpdate'
    AND devices.jobs.status = 'completed'
    AND devices.jobs.createdTimeUtc > '2016-09-01'
```

Sie können auch die Ergebnisse pro Gerät eines einzelnen Auftrags abrufen.

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.jobId = 'myJobId'
```

### <a name="limitations"></a>Einschränkungen
Derzeit wird für Abfragen von **devices.jobs** Folgendes nicht unterstützt:

* Projektionen, sodass nur `SELECT *` möglich ist.
* Bedingungen, die zusätzlich zu Auftragseigenschaften auf einen Gerätezwilling verweisen (siehe vorausgehender Abschnitt).
* Durchführung von Aggregationen, z.B. Zählen, Durchschnittsbildung, Gruppieren.

## <a name="device-to-cloud-message-routes-query-expressions"></a>D2C-Nachrichtenrouten-Abfrageausdrücke

Mit [Gerät-zu-Cloud-Routen][lnk-devguide-messaging-routes] können Sie IoT Hub so konfigurieren, dass Gerät-zu-Cloud-Nachrichten an verschiedene Endpunkte verteilt werden. Die Verteilung basiert auf Ausdrücken, die für einzelne Nachrichten ausgewertet werden.

Die [Routenbedingung][lnk-query-expressions] verwendet die gleiche IoT Hub-Abfragesprache wie Bedingungen in Zwillings- und Auftragsabfragen. Routenbedingungen werden anhand von Nachrichtenheadern und Nachrichtentext ausgewertet. Ihr Abfrageausdruck für das Routing kann nur Nachrichtenköpfe, nur den Nachrichtentext oder beides umfassen. IoT Hub geht von einem bestimmten Schema für Header und Nachrichtentext aus, um Nachrichten zu routen. In den folgenden Abschnitten wird beschrieben, was für IoT Hub für das ordnungsgemäße Routing erforderlich ist.

### <a name="routing-on-message-headers"></a>Routing nach Nachrichtenheader

IoT Hub geht für das Nachrichtenrouting von der folgenden JSON-Darstellung des Nachrichtenheader aus:

```json
{
  "message": {
    "systemProperties": {
      "contentType": "application/json",
      "contentEncoding": "utf-8",
      "iothub-message-source": "deviceMessages",
      "iothub-enqueuedtime": "2017-05-08T18:55:31.8514657Z"
    },
    "appProperties": {
      "processingPath": "<optional>",
      "verbose": "<optional>",
      "severity": "<optional>",
      "testDevice": "<optional>"
    },
    "body": "{\"Weather\":{\"Temperature\":50}}"
  }
}
```

Nachrichtensystemeigenschaften ist das Symbol `'$'` vorangestellt.
Auf Benutzereigenschaften wird stets über deren Namen zugegriffen. Wenn der Name einer Benutzereigenschaft mit dem einer Systemeigenschaft übereinstimmt (wie z.B. `$contentType`), wird die Benutzereigenschaft mit dem Ausdruck `$contentType` abgerufen.
Sie können stets mithilfe von Klammern `{}` auf die Systemeigenschaft zugreifen. Sie können beispielsweise über den Ausdruck `{$contentType}` auf die Systemeigenschaft `contentType` zuweisen. Über Eigenschaftsnamen in Klammern wird stets die entsprechende Systemeigenschaft abgerufen.

Beachten Sie, dass bei Eigenschaftennamen nicht zwischen Groß- und Kleinschreibung unterschieden wird.

> [!NOTE]
> Alle Nachrichteneigenschaften sind Zeichenfolgen. Systemeigenschaften, wie im [Entwicklerhandbuch][lnk-devguide-messaging-format] beschrieben, sind derzeit nicht zur Verwendung in Abfragen verfügbar.
>

Wenn Sie z.B. eine `messageType`-Eigenschaft verwenden, möchten Sie vielleicht alle Telemetrie zu einem bestimmten Endpunkt weiterleiten und alle Warnungen zu einem anderen Endpunkt. Sie können den folgenden Ausdruck schreiben, um die Telemetrie weiterzuleiten:

```sql
messageType = 'telemetry'
```

Schreiben Sie dann den folgenden Ausdruck zum Weiterleiten der Warnmeldungen:

```sql
messageType = 'alert'
```

Boolesche Ausdrücke und Funktionen werden ebenfalls unterstützt. Anhand dieses Features können Sie Schweregrade unterscheiden, z.B.:

```sql
messageType = 'alerts' AND as_number(severity) <= 2
```

Im Abschnitt [Ausdrücke und Bedingungen][lnk-query-expressions] finden Sie die vollständige Liste der unterstützten Operatoren und Funktionen.

### <a name="routing-on-message-bodies"></a>Routing nach Nachrichtentext

IoT Hub kann nur basierend auf dem Inhalt des Nachrichtentexts weiterleiten, wenn der Nachrichtentext ordnungsgemäß in dem Format UTF-8, UTF-16 oder UTF-32 JSON-codiert wurde. Setzen Sie den Inhaltstyp der Nachricht auf `application/json`. Legen Sie die Inhaltscodierung im Nachrichtenkopf auf eine der unterstützten UTF-Codierungen fest. Wenn einer der Nachrichtenköpfe nicht angegeben wurde, versucht IoT Hub nicht, etwaige Abfrageausdrücke im Zusammenhang mit dem Text für die Nachricht auszuwerten. Wenn Ihre Nachricht keine JSON-Nachricht ist, oder wenn die Nachricht den Inhaltstyp und die Inhaltscodierung nicht angibt, können Sie trotzdem das Nachrichtenrouting für das Weiterleiten der Nachricht basierend auf den Nachrichtenköpfen verwenden.

Das folgende Beispiel zeigt, wie eine Nachricht mit einem ordnungsgemäß formatierten und codierten JSON-Text erstellt wird:

```csharp
string messageBody = @"{ 
                            ""Weather"":{ 
                                ""Temperature"":50, 
                                ""Time"":""2017-03-09T00:00:00.000Z"", 
                                ""PrevTemperatures"":[ 
                                    20, 
                                    30, 
                                    40 
                                ], 
                                ""IsEnabled"":true, 
                                ""Location"":{ 
                                    ""Street"":""One Microsoft Way"", 
                                    ""City"":""Redmond"", 
                                    ""State"":""WA"" 
                                }, 
                                ""HistoricalData"":[ 
                                    { 
                                    ""Month"":""Feb"", 
                                    ""Temperature"":40 
                                    }, 
                                    { 
                                    ""Month"":""Jan"", 
                                    ""Temperature"":30 
                                    } 
                                ] 
                            } 
                        }"; 
 
// Encode message body using UTF-8 
byte[] messageBytes = Encoding.UTF8.GetBytes(messageBody); 
 
using (var message = new Message(messageBytes)) 
{ 
    // Set message body type and content encoding. 
    message.ContentEncoding = "utf-8"; 
    message.ContentType = "application/json"; 
 
    // Add other custom application properties.  
    message.Properties["Status"] = "Active";    
 
    await deviceClient.SendEventAsync(message); 
}
```

Sie können im Abfrageausdruck `$body` für das Weiterleiten der Nachricht verwenden. Sie können einen einfachen Textverweis, einen Textarrayverweis oder mehrere Textverweise in den Abfrageausdruck einfügen. Der Abfrageausdruck kann auch einen Textverweis mit einem Verweis auf den Nachrichtenheader kombinieren. Die folgenden Abfrageausdrücke sind beispielsweise sämtlich gültig:

```sql
$body.Weather.HistoricalData[0].Month = 'Feb'
$body.Weather.Temperature = 50 AND $body.Weather.IsEnabled
length($body.Weather.Location.State) = 2
$body.Weather.Temperature = 50 AND Status = 'Active'
```

## <a name="basics-of-an-iot-hub-query"></a>Grundlagen von IoT Hub-Abfragen
Jede IoT Hub-Abfrage besteht aus einer SELECT- und einer FROM-Klausel mit optionalen WHERE- und GROUP BY-Klauseln. Jede Abfrage wird für eine Sammlung von JSON-Dokumenten ausgeführt, z.B. Gerätezwillinge. Die FROM-Klausel zeigt die Dokumentsammlung an, die durchlaufen werden soll (**devices** oder **devices.jobs**). Anschließend wird der Filter in der WHERE-Klausel angewendet. Mit Aggregationen werden die Ergebnisse dieses Schritts gruppiert, wie in der GROUP BY-Klausel angegeben. Für jede Gruppe wird eine Zeile generiert, wie in der SELECT-Klausel angegeben.

```sql
SELECT <select_list>
FROM <from_specification>
[WHERE <filter_condition>]
[GROUP BY <group_specification>]
```

## <a name="from-clause"></a>Die FROM-Klausel
Die **FROM <Spezifikation>**-Klausel kann nur zwei Werte annehmen: **FROM devices** zum Abfragen von Gerätezwillingen oder **FROM devices.jobs** zum Abfragen der Details pro Gerät für den Auftrag.

## <a name="where-clause"></a>WHERE-Klausel
Die **WHERE <Filterbedingung>**-Klausel ist optional. Sie gibt eine oder mehrere Bedingungen an, die von den in der FROM-Sammlung enthaltenen JSON-Dokumenten erfüllt werden müssen, um als Teil des Ergebnisses zurückgegeben zu werden. Jedes JSON-Dokument muss die angegebenen Bedingungen erfüllen, um in das Ergebnis einbezogen zu werden.

Die zulässigen Bedingungen werden im Abschnitt [Ausdrücke und Bedingungen][lnk-query-expressions] beschrieben.

## <a name="select-clause"></a>Die SELECT-Klausel
Die **SELECT <Liste>**-Klausel ist obligatorisch und gibt an, welche Werte von der Abfrage abgerufen werden. Sie gibt die JSON-Werte an, mit denen die neuen JSON-Objekte erstellt werden sollen.
Für jedes Element der gefilterten (und optional gruppierten) Teilmenge der FROM-Sammlung wird in der Projektionsphase ein neues JSON-Objekt generiert. Dieses Objekt wird mit den in der SELECT-Klausel angegebenen Werten erstellt.

Dies ist die Grammatik der SELECT-Klausel:

```
SELECT [TOP <max number>] <projection list>

<projection_list> ::=
    '*'
    | <projection_element> AS alias [, <projection_element> AS alias]+

<projection_element> :==
    attribute_name
    | <projection_element> '.' attribute_name
    | <aggregate>

<aggregate> :==
    count()
    | avg(<projection_element>)
    | sum(<projection_element>)
    | min(<projection_element>)
    | max(<projection_element>)
```

**Attribute_name** bezieht sich auf jede Eigenschaft des JSON-Dokuments in der FROM-Sammlung. Im Abschnitt [Erste Schritte mit Gerätezwillingsabfragen][lnk-query-getstarted] finden Sie einige Beispiele für SELECT-Klauseln.

Derzeit werden andere Auswahlklauseln als **SELECT*** nur in Aggregatabfragen für Gerätezwillinge unterstützt.

## <a name="group-by-clause"></a>GROUP BY-Klausel
Die **GROUP BY <Gruppierungsspezifikation>**-Klausel ist ein optionaler Schritt, der nach dem in der WHERE-Klausel angegebenen Filter und vor der in der SELECT-Klausel angegebenen Projektion ausgeführt wird. Sie gruppiert Dokumente anhand des Werts eines Attributs. Diese Gruppen werden verwendet, um aggregierte Werte gemäß der SELECT-Klausel zu generieren.

Ein Beispiel für eine Abfrage mit GROUP BY:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

Die formale Syntax für GROUP BY lautet:

```
GROUP BY <group_by_element>
<group_by_element> :==
    attribute_name
    | < group_by_element > '.' attribute_name
```

**Attribute_name** bezieht sich auf jede Eigenschaft des JSON-Dokuments in der FROM-Sammlung.

Die GROUP BY-Klausel wird derzeit nur für Abfragen von Gerätezwillingen unterstützt.

## <a name="expressions-and-conditions"></a>Ausdrücke und Bedingungen
Auf hoher Ebene wird ein *Ausdruck*:

* in eine Instanz eines JSON-Typs (z. B. boolescher Wert, Zahl, Zeichenfolge, Array oder Objekt) ausgewertet.
* durch das Ändern von Daten aus dem JSON-Dokument des Geräts und Konstanten mit integrierten Operatoren und Funktionen definiert.

*Bedingungen* sind Ausdrücke, die als boolescher Wert ausgewertet werden. Jede Konstante, die sich vom booleschen Ausdruck **true** unterscheidet, wird als **false** betrachtet. Diese Regel umfasst **null**, **undefined**, jede Objekt- oder Arrayinstanz, jede Zeichenfolge und den booleschen Ausdruck **false**.

Die Syntax für Ausdrücke lautet:

```
<expression> ::=
    <constant> |
    attribute_name |
    <function_call> |
    <expression> binary_operator <expression> |
    <create_array_expression> |
    '(' <expression> ')'

<function_call> ::=
    <function_name> '(' expression ')'

<constant> ::=
    <undefined_constant>
    | <null_constant>
    | <number_constant>
    | <string_constant>
    | <array_constant>

<undefined_constant> ::= undefined
<null_constant> ::= null
<number_constant> ::= decimal_literal | hexadecimal_literal
<string_constant> ::= string_literal
<array_constant> ::= '[' <constant> [, <constant>]+ ']'
```

Informationen dazu, wofür die einzelnen Symbole in der Ausdruckssyntax stehen, finden Sie in der folgenden Tabelle:

| Symbol | Definition |
| --- | --- |
| attribute_name | Eine beliebige Eigenschaft des JSON-Dokuments in der **FROM**-Sammlung. |
| binary_operator | Ein beliebiger binärer, im Abschnitt [Operatoren](#operators) aufgelisteter Operator. |
| function_name| Eine beliebige im Abschnitt [Funktionen](#functions) aufgelistete Funktion. |
| decimal_literal |Ein Gleitkommawert, ausgedrückt in Dezimalschreibweise. |
| hexadecimal_literal |Eine Zahl, ausgedrückt durch die Zeichenfolge „0x“ gefolgt von einer Zeichenfolge von Hexadezimalzahlen. |
| string_literal |Zeichenfolgenliterale sind Unicode-Zeichenfolgen, die durch eine Sequenz aus null oder mehr Unicode-Zeichen oder Escapesequenzen dargestellt werden. Zeichenfolgenliterale werden in einfache Anführungszeichen oder doppelte Anführungszeichen eingeschlossen. Zulässige Escapezeichen: `\'`, `\"`, `\\`, `\uXXXX` für Unicode-Zeichen, die durch 4 Hexadezimalstellen definiert werden. |

### <a name="operators"></a>Operatoren
Die folgenden Operatoren werden unterstützt:

| Familie | Operatoren |
| --- | --- |
| Arithmetik |+, -, *, /, % |
| Logisch |AND, OR, NOT |
| Vergleich |=, !=, <, >, <=, >=, <> |

### <a name="functions"></a>Functions
Bei Abfragen von Zwillingen und Aufträgen wird nur folgende Funktion unterstützt:

| Funktion | BESCHREIBUNG |
| -------- | ----------- |
| IS_DEFINED(Eigenschaft) | Gibt einen booleschen Wert zurück, um anzugeben, ob der Eigenschaft ein Wert zugewiesen wurde (inklusive `null`). |

In Routenbedingungen werden die folgenden mathematischen Funktionen unterstützt:

| Funktion | BESCHREIBUNG |
| -------- | ----------- |
| ABS(x) | Gibt den absoluten (positiven) Wert des angegebenen numerischen Ausdrucks zurück. |
| EXP(x) | Gibt den Exponentialwert des angegebenen numerischen Ausdrucks (e^x) zurück. |
| POWER(x,y) | Gibt den Wert des angegebenen Ausdrucks gemäß der angegebenen Potenz (x^y) zurück.|
| SQUARE(x) | Gibt das Quadrat des angegebenen numerischen Werts zurück. |
| CEILING(x) | Gibt den kleinsten ganzzahligen Wert zurück, der größer oder gleich dem angegebenen numerischen Ausdruck ist. |
| FLOOR(x) | Gibt den größten ganzzahligen Wert zurück, der kleiner oder gleich dem angegebenen numerischen Ausdruck ist. |
| SIGN(x) | Gibt das positive Vorzeichen (+1), null (0) oder das negative Vorzeichen (-1) des angegebenen numerischen Ausdrucks zurück.|
| SQRT(x) | Gibt die Quadratwurzel des angegebenen numerischen Werts zurück. |

In Routenbedingungen werden die folgenden Typüberprüfungs- und Umwandlungsfunktionen unterstützt:

| Funktion | BESCHREIBUNG |
| -------- | ----------- |
| AS_NUMBER | Konvertiert die Eingabezeichenfolge in eine Zahl. `noop`, wenn die Eingabe eine Zahl ist; `Undefined`, wenn die Zeichenfolge keine Zahl darstellt.|
| IS_ARRAY | Gibt einen booleschen Wert zurück, der angibt, ob der angegebene Ausdruck vom Typ „Array“ ist. |
| IS_BOOL | Gibt einen booleschen Wert zurück, der angibt, ob der angegebene Ausdruck vom Typ „boolesch“ ist. |
| IS_DEFINED | Gibt einen booleschen Wert zurück, um anzugeben, ob der Eigenschaft ein Wert zugewiesen wurde. |
| IS_NULL | Gibt einen booleschen Wert zurück, der angibt, ob der angegebene Ausdruck vom Typ „NULL“ ist. |
| IS_NUMBER | Gibt einen booleschen Wert zurück, der angibt, ob der angegebene Ausdruck vom Typ „Zahl“ ist. |
| IS_OBJECT | Gibt einen booleschen Wert zurück, der angibt, ob der angegebene Ausdruck vom Typ „JSON-Objekt“ ist. |
| IS_PRIMITIVE | Gibt einen booleschen Wert zurück, der angibt, ob der angegebene Ausdruck ein Grundtyp ist (Zeichenfolge, boolesch, numerisch oder `null`). |
| IS_STRING | Gibt einen booleschen Wert zurück, der angibt, ob der angegebene Ausdruck vom Typ „Zeichenfolge“ ist. |

In Routenbedingungen werden die folgenden Zeichenfolgenfunktionen unterstützt:

| Funktion | BESCHREIBUNG |
| -------- | ----------- |
| CONCAT(x, y, …) | Gibt eine Zeichenfolge zurück, die das Ergebnis der Verkettung von zwei oder mehr Zeichenfolgenwerten darstellt. |
| LENGTH(x) | Gibt die Anzahl der Zeichen im angegebenen Zeichenfolgenausdruck zurück.|
| LOWER(x) | Gibt eine Zeichenfolge zurück, nachdem Großbuchstaben in Kleinbuchstaben konvertiert wurden. |
| UPPER(x) | Gibt eine Zeichenfolge zurück, nachdem Kleinbuchstaben in Großbuchstaben konvertiert wurden. |
| SUBSTRING(Zeichenfolge, Start [, Länge]) | Gibt einen Teil eines Zeichenfolgenausdrucks zurück. Das angegebene Zeichen ist der Nullpunkt, von dem ab die Teilzeichenfolge in angegebener Länge bzw. bis zum Ende der Zeichenfolge zurückgegeben wird. |
| INDEX_OF(Zeichenfolge, Fragment) | Gibt die Anfangsposition des ersten Vorkommens des zweiten Zeichenfolgenausdrucks innerhalb des ersten angegebenen Zeichenfolgenausdrucks zurück, oder -1, wenn die Zeichenfolge nicht gefunden wird.|
| STARTS_WITH(x, y) | Gibt einen booleschen Wert zurück, um anzugeben, ob der erste Zeichenfolgenausdruck mit dem zweiten beginnt. |
| ENDS_WITH(x, y) | Gibt einen booleschen Wert zurück, um anzugeben, ob der erste Zeichenfolgenausdruck mit dem zweiten endet. |
| CONTAINS(x,y) | Gibt einen booleschen Wert zurück, um anzugeben, ob der erste Zeichenfolgenausdruck den zweiten enthält. |

## <a name="next-steps"></a>Nächste Schritte
Informieren Sie sich darüber, wie Sie Abfragen in Ihren Apps mit [Azure IoT SDKs][lnk-hub-sdks] ausführen.

[lnk-query-where]: iot-hub-devguide-query-language.md#where-clause
[lnk-query-expressions]: iot-hub-devguide-query-language.md#expressions-and-conditions
[lnk-query-getstarted]: iot-hub-devguide-query-language.md#get-started-with-device-twin-queries

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-devguide-messaging-routes]: iot-hub-devguide-messages-read-custom.md
[lnk-devguide-messaging-format]: iot-hub-devguide-messages-construct.md
[lnk-devguide-messaging-routes]: ./iot-hub-devguide-messages-read-custom.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
