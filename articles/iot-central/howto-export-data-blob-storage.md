---
title: Exportieren von Daten in Azure Blob Storage | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Daten aus Ihrer Azure IoT Central-Anwendung in Azure Blob Storage exportiert werden.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 12/07/2018
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: ae1e71170952a2f05e371de68b519eba522e3298
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318411"
---
# <a name="export-your-data-to-azure-blob-storage"></a>Exportieren von Daten in Azure Blob Storage

*Dieses Thema gilt für Administratoren.*

In diesem Artikel wird detailliert beschrieben, wie Sie in Azure IoT Central das Feature für den fortlaufenden Datenexport verwenden, um Daten regelmäßig in Ihr **Azure Blob Storage-Konto** zu exportieren. Sie können **Messungen**, **Geräte** und **Gerätevorlagen** in Dateien in Dateien im Format „Apache AVRO“ exportieren. Die exportierten Daten können für die Analyse von kalten Datenpfaden verwendet werden, z.B. Trainingsmodelle in Azure Machine Learning oder die langfristige Trendanalyse in Microsoft Power BI.

> [!Note]
> Zur Erinnerung: Wenn Sie den fortlaufenden Datenexport aktivieren, erhalten Sie nur die Daten ab dem jeweiligen Aktivierungszeitpunkt. Derzeit können Daten nicht für Zeiten abgerufen werden, in denen der Datenexport deaktiviert war. Aktivieren Sie den fortlaufenden Datenexport frühzeitig, um umfassendere Verlaufsdaten zu erhalten.


## <a name="prerequisites"></a>Voraussetzungen

- Sie müssen Administrator in Ihrer IoT Central-Anwendung sein.

## <a name="export-to-azure-blob-storage"></a>Exportieren in Azure Blob Storage

Messungen, Geräte und Gerätevorlagendaten werden einmal pro Minute in Ihr Speicherkonto exportiert, wobei jede Datei den Batch der Änderungen seit der letzten exportierten Datei enthält. Die exportierten Daten haben das Format [Apache AVRO](https://avro.apache.org/docs/current/index.html) und werden in drei Ordner exportiert. Die Standardpfade in Ihrem Speicherkonto sind:
    - Meldungen: {container}/measurements/{hubname}/{JJJJ}/{MM}/{TT}/{hh}/{mm}/{dateiname}.avro
    - Geräte: {container}/devices/{JJJJ}/{MM}/{TT}/{hh}/{mm}/{dateiname}.avro
    - Gerätevorlagen: {container}/deviceTemplates/{JJJJ}/{MM}/{TT}/{hh}/{mm}/{dateiname}.avro

### <a name="measurements"></a>Messungen

Die exportierten Messdaten umfassen alle neuen Nachrichten, die während dieses Zeitraums von IoT Central von allen Geräten abgerufen wurden. Für die exportierten Dateien wird dasselbe Format wie für die Nachrichtendateien verwendet, die bei der [IoT Hub-Nachrichtenweiterleitung](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) in Blobspeicher exportiert wurden.

> [!NOTE]
> Die Geräte, die die Messdaten senden, werden durch Geräte-IDs (siehe folgende Abschnitte) dargestellt. Exportieren Sie die Gerätemomentaufnahmen, um die Namen der Geräte zu erhalten. Korrelieren Sie die einzelnen Nachrichtendatensätze, indem Sie jeweils die **connectionDeviceId** verwenden, die mit der **deviceId** des Gerätedatensatzes übereinstimmt.

Im folgenden Beispiel wird ein Datensatz in einer decodierten AVRO-Datei angezeigt:

```json
{
    "EnqueuedTimeUtc": "2018-06-11T00:00:08.2250000Z",
    "Properties": {},
    "SystemProperties": {
        "connectionDeviceId": "<connectionDeviceId>",
        "connectionAuthMethod": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
        "connectionDeviceGenerationId": "<generationId>",
        "enqueuedTime": "2018-06-11T00:00:08.2250000Z"
    },
    "Body": "{\"humidity\":80.59100954598546,\"magnetometerX\":0.29451796907056726,\"magnetometerY\":0.5550332126050068,\"magnetometerZ\":-0.04116681874733441,\"connectivity\":\"connected\",\"opened\":\"triggered\"}"
}
```

### <a name="devices"></a>Geräte

Wenn der fortlaufende Datenexport zum ersten Mal aktiviert wird, wird eine einzelne Momentaufnahme mit allen Geräten exportiert. Jedes Gerät enthält Folgendes:
- `id` des Geräts in IoT Central
- `name` des Geräts
- `deviceId` von [Device Provisioning Service](https://aka.ms/iotcentraldocsdps)
- Gerätevorlageninformationen
- Eigenschaftswerte
- Einstellungswerte

Eine neue Momentaufnahme wird einmal pro Minute geschrieben. Die Momentaufnahme enthält Folgendes:

- Seit der letzten Momentaufnahme hinzugefügte neue Geräte
- Geräte mit geänderten Eigenschafts- und Einstellungswerten seit der letzten Momentaufnahme

> [!NOTE]
> Geräte, die seit der letzten Momentaufnahme gelöscht wurden, werden nicht exportiert. Derzeit verfügen die Momentaufnahmen nicht über Indikatoren für gelöschte Geräte.
>
> Die Gerätevorlage, der die einzelnen Geräte zugewiesen sind, wird durch eine Gerätevorlagen-ID dargestellt. Exportieren Sie die Momentaufnahmen von Gerätevorlagen, um den Namen der Gerätevorlage zu erhalten.

Ein Datensatz in der decodierten AVRO-Datei kann folgendermaßen aussehen:

```json
{
    "id": "<id>",
    "name": "Refrigerator 2",
    "simulated": true,
    "deviceId": "<deviceId>",
    "deviceTemplate": {
        "id": "<template id>",
        "version": "1.0.0"
    },
    "properties": {
        "cloud": {
            "location": "New York",
            "maintCon": true,
            "tempThresh": 20
        },
        "device": {
            "lastReboot": "2018-02-09T22:22:47.156Z"
        }
    },
    "settings": {
        "device": {
            "fanSpeed": 0
        }
    }
}
```

### <a name="device-templates"></a>Gerätevorlagen

Wenn der fortlaufende Datenexport zum ersten Mal aktiviert wird, wird eine einzelne Momentaufnahme mit allen Gerätevorlagen exportiert. Jede Gerätevorlage enthält Folgendes:
- `id` der Gerätevorlage
- `name` der Gerätevorlage
- `version` der Gerätevorlage
- Typen der Messdaten und Mindest- bzw. Maximalwerte
- Typen der Eigenschaftsdaten und Standardwerte
- Typen der Einstellungsdaten und Standardwerte

Eine neue Momentaufnahme wird einmal pro Minute geschrieben. Die Momentaufnahme enthält Folgendes:

- Neue Gerätevorlagen, die seit der letzten Momentaufnahme hinzugefügt wurden
- Gerätevorlagen, deren Definition für Messungen, Eigenschaften und Einstellungen seit der letzten Momentaufnahme geändert wurden

> [!NOTE]
> Gerätevorlagen, die seit der letzten Momentaufnahme gelöscht wurden, werden nicht exportiert. Derzeit verfügen die Momentaufnahmen nicht über Indikatoren für gelöschte Gerätevorlagen.

Ein Datensatz in der decodierten AVRO-Datei kann folgendermaßen aussehen:

```json
{
    "id": "<id>",
    "name": "Refrigerated Vending Machine",
    "version": "1.0.0",
    "measurements": {
        "telemetry": {
            "humidity": {
                "dataType": "double",
                "name": "Humidity"
            },
            "magnetometerX": {
                "dataType": "double",
                "name": "Magnetometer X"
            },
            "magnetometerY": {
                "dataType": "double",
                "name": "Magnetometer Y"
            },
            "magnetometerZ": {
                "dataType": "double",
                "name": "Magnetometer Z"
            }
        },
        "states": {
            "connectivity": {
                "dataType": "enum",
                "name": "Connectivity"
            }
        },
        "events": {
            "opened": {
                "name": "Door Opened",
                "category": "informational"
            }
        }
    },
    "settings": {
        "device": {
            "fanSpeed": {
                "dataType": "double",
                "name": "Fan Speed",
                "initialValue": 0
            }
        }
    },
    "properties": {
        "cloud": {
            "location": {
                "dataType": "string",
                "name": "Location",
                "initialValue": "Seattle"
            },
            "maintCon": {
                "dataType": "boolean",
                "name": "Maintenance Contract",
                "initialValue": true
            },
            "tempThresh": {
                "dataType": "double",
                "name": "Temperature Alert Threshold",
                "initialValue": 30
            }
        },
        "device": {
            "lastReboot": {
                "dataType": "dateTime",
                "name": "Last Reboot"
            }
        }
    }
}
```

## <a name="read-exported-avro-files"></a>Lesen von exportierten AVRO-Dateien

AVRO ist ein Binärformat, mit dem die Dateien im unformatierten Zustand gelesen werden können. Diese Dateien können in das JSON-Format decodiert werden. Die folgenden Beispiele zeigen, wie die AVRO-Dateien von Messungen, Geräten und Gerätevorlagen analysiert werden. Die Beispiele entsprechen den Beispielen, die im vorherigen Abschnitt beschrieben wurden.

### <a name="read-avro-files-by-using-python"></a>Lesen von AVRO-Dateien mit Python

#### <a name="install-pandas-and-the-pandavro-package"></a>Installieren des Pandas- und Pandavro-Pakets

```python
pip install pandas
pip install pandavro
```

#### <a name="parse-a-measurements-avro-file"></a>Analysieren einer AVRO-Datei für Messungen

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    measurements = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The SystemProperties column contains a dictionary
    # with the device ID located under the connectionDeviceId key.
    transformed["device_id"] = measurements["SystemProperties"].apply(lambda x: x["connectionDeviceId"])

    # The Body column is a series of UTF-8 bytes that is stringified
    # and parsed as JSON. This example pulls the humidity property
    # from each column to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, print the new DataFrame with our device IDs and humidities.
    print(transformed)

```

#### <a name="parse-a-devices-avro-file"></a>Analysieren einer AVRO-Datei für Geräte

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    devices = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The device ID is available in the id column.
    transformed["device_id"] = devices["deviceId"]

    # The template ID and version are present in a dictionary under
    # the deviceTemplate column.
    transformed["template_id"] = devices["deviceTemplate"].apply(lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = devices["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)

```

#### <a name="parse-a-device-templates-avro-file"></a>Analysieren einer AVRO-Datei für Gerätevorlagen

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    templates = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The template and version are available in the id and version columns.
    transformed["template_id"] = templates["id"]
    transformed["template_version"] = templates["version"]

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = templates["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

### <a name="read-avro-files-by-using-c"></a>Lesen von AVRO-Dateien mit C#

#### <a name="install-the-microsofthadoopavro-package"></a>Installieren des Microsoft.Hadoop.Avro-Pakets

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

#### <a name="parse-a-measurements-avro-file"></a>Analysieren einer AVRO-Datei für Messungen

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;
using Newtonsoft.Json;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    var systemProperties = record.GetField<IDictionary<string, object>>("SystemProperties");
                    var deviceId = systemProperties["connectionDeviceId"] as string;
                    Console.WriteLine("Device ID: {0}", deviceId);

                    using (var stream = new MemoryStream(record.GetField<byte[]>("Body")))
                    {
                        using (var streamReader = new StreamReader(stream, Encoding.UTF8))
                        {
                            var body = JsonSerializer.Create().Deserialize(streamReader, typeof(IDictionary<string, dynamic>)) as IDictionary<string, dynamic>;
                            var humidity = body["humidity"];
                            Console.WriteLine("Humidity: {0}", humidity);
                        }
                    }
                }
            }
        }
    }
}
```

#### <a name="parse-a-devices-avro-file"></a>Analysieren einer AVRO-Datei für Geräte

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var deviceId = record.GetField<string>("deviceId");

                    // The device template information is stored in a sub-record
                    // under the deviceTemplate field.
                    var deviceTemplateRecord = record.GetField<AvroRecord>("deviceTemplate");
                    var templateId = deviceTemplateRecord.GetField<string>("id");
                    var templateVersion = deviceTemplateRecord.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "Device ID: {0}, Template ID: {1}, Template Version: {2}, Fan Speed: {3}",
                        deviceId,
                        templateId,
                        templateVersion,
                        fanSpeed
                    );
                }
            }
        }
    }
}

```

#### <a name="parse-a-device-templates-avro-file"></a>Analysieren einer AVRO-Datei für Gerätevorlagen

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var id = record.GetField<string>("id");
                    var version = record.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "ID: {1}, Version: {2}, Fan Speed: {3}",
                        id,
                        version,
                        fanSpeed
                    );
                }
            }
        }
    }
}
```

### <a name="read-avro-files-by-using-javascript"></a>Lesen von AVRO-Dateien mit JavaScript

#### <a name="install-the-avsc-package"></a>Installieren des AVSC-Pakets

```javascript
npm install avsc
```

#### <a name="parse-a-measurements-avro-file"></a>Analysieren einer AVRO-Datei für Messungen

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device ID and humidity from each record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the system properties.
        const deviceId = record.SystemProperties.connectionDeviceId;

        // Convert the body from a buffer to a string and parse it.
        const body = JSON.parse(record.Body.toString());

        // Get the humidty property from the body.
        const humidity = body.humidity;

        // Log the retrieved device ID and humidity.
        console.log(`Device ID: ${deviceId}`);
        console.log(`Humidity: ${humidity}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-devices-avro-file"></a>Analysieren einer AVRO-Datei für Geräte

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the deviceId property.
        const deviceId = record.deviceId;

        // Fetch the template ID and version from the deviceTemplate property.
        const deviceTemplateId = record.deviceTemplate.id;
        const deviceTemplateVersion = record.deviceTemplate.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device ID and humidity.
        console.log(`deviceID: ${deviceId}, Template ID: ${deviceTemplateId}, Template Version: ${deviceTemplateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-device-templates-avro-file"></a>Analysieren einer AVRO-Datei für Gerätevorlagen

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the template ID and version from the id and verison properties.
        const templateId = record.id;
        const templateVersion = record.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device id and humidity.
        console.log(`Template ID: ${templateId}, Template Version: ${templateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie jetzt mit dem Exportieren Ihrer Daten vertraut sind, können Sie mit dem nächsten Schritt fortfahren:

> [!div class="nextstepaction"]
> [Visualisieren Ihrer Daten in Power BI](howto-connect-powerbi.md)
