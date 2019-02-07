---
title: 'Schnellstart: Sprachübersetzungs-API (Node.js)'
titlesuffix: Azure Cognitive Services
description: Hier erhalten Sie Informationen und Codebeispiele für einen schnellen Einstieg in die Sprachübersetzungs-API.
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: quickstart
ms.date: 3/5/2018
ms.author: v-jaswel
ms.openlocfilehash: 8f2adf743760c85b8c2a75c0f398bc5b61fd1061
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55754234"
---
# <a name="quickstart-translator-speech-api-with-nodejs"></a>Schnellstart: Sprachübersetzungs-API mit Node.js 
<a name="HOLTop"></a>

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

In diesem Artikel wird gezeigt, wie Sie mit der Sprachübersetzungs-API gesprochene Sprache in einer WAV-Datei übersetzen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Ausführen dieses Code benötigen Sie [Node.js 6](https://nodejs.org/en/download/).

Sie müssen das [Websocket-Paket](https://www.npmjs.com/package/websocket) für Node.js installieren.

Sie benötigen eine WAV-Datei namens „speak.wav“, die im gleichen Ordner gespeichert ist wie die ausführbare Datei, die Sie aus dem unten angegebenen Code kompilieren. Diese WAV-Datei muss im PCM-Standardformat mit 16 Bit, 16 kHz und in mono vorliegen. Eine solche WAV-Datei können Sie über die [Text-to-Speech-API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) abrufen.

Sie müssen über ein [Cognitive Services-API-Konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) mit **Microsoft-Sprachübersetzungs-API** verfügen. Sie benötigen einen kostenpflichtigen Abonnementschlüssel aus Ihrem [Azure-Dashboard](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Übersetzen von gesprochener Sprache

Mit dem folgenden Code wird gesprochene Sprache von einer Sprache in eine andere übersetzt.

1. Erstellen Sie in Ihrer bevorzugten IDE ein neues Node.js-Projekt.
2. Fügen Sie den unten stehenden Code hinzu.
3. Ersetzen Sie den `key`-Wert durch einen für Ihr Abonnement gültigen Zugriffsschlüssel.
4. Führen Sie das Programm aus.

```nodejs
/* To install this dependency, run:
npm install websocket
*/
var wsClient = require('websocket').client;
var fs = require('fs');
/* To install this dependency, run:
npm install stream-buffers
*/
var streamBuffers = require('stream-buffers');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
let key = 'ENTER KEY HERE';

let host = 'wss://dev.microsofttranslator.com';
let path = '/speech/translate';
let params = '?api-version=1.0&from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa';
let uri = host + path + params;

/* The input .wav file is in PCM 16bit, 16kHz, mono format.
You can obtain such a .wav file using the Text to Speech API. See:
https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis
*/
let input_path = 'speak.wav';

let output_path = 'speak2.wav';

function receive(message) {
    console.log ("Received message of type: " + message.type + ".");

    if (message.type == 'utf8') {
        var result = JSON.parse(message.utf8Data)
        console.log("Response type: " + result.type + ".");
        console.log("Recognized input as: " + result.recognition);
        console.log("Translation: " + result.translation);
    }
    else if (message.type == 'binary') {
/* This is needed to make sure message.binaryData is defined. See:
https://stackoverflow.com/questions/17546953/cant-access-object-property-even-though-it-exists-returns-undefined
*/
        let binary_data = JSON.parse (JSON.stringify (message.binaryData));

        if (binary_data.type == 'Buffer') {
/* Put the binary data in a Buffer - do not write it directly to the file. */
            let buffer = new Buffer(binary_data.data, 'binary');
/* Write the contents of the Buffer to the file. */
            fs.writeFile (output_path, buffer, 'binary', function (error) {
/* fs.writeFile calls the error-handling function even if there is no error, in which case error === null. See:
https://stackoverflow.com/questions/44473868/node-js-fs-writefile-err-returns-null
*/
                if (error !== null) {
                    console.log ("Error: " + error);
                }
            });
        }
    }
}

function send(connection, filename) {
    
    var myReadableStreamBuffer = new streamBuffers.ReadableStreamBuffer({
        frequency: 100,
        chunkSize: 32000
    });

/* Make sure the audio file is followed by silence.
This lets the service know that the audio file is finished.
At 32 bytes per millisecond, this is 100 seconds of silence. */
    myReadableStreamBuffer.put(fs.readFileSync(filename));
    myReadableStreamBuffer.put(new Buffer(3200000));
    myReadableStreamBuffer.stop();

    myReadableStreamBuffer.on('data', function (data) {
        connection.sendBytes(data);
    });

    myReadableStreamBuffer.on('end', function () {
        console.log('Closing connection.');
        connection.close(1000);
    });
}

function connect() {
    var ws = new wsClient();

    ws.on('connectFailed', function (error) {
        console.log('Connection error: ' + error.toString());
    });
                            
    ws.on('connect', function (connection) {
        console.log('Connected.');

        connection.on('message', receive);
        
        connection.on('close', function (reasonCode, description) {
            console.log('Connection closed: ' + reasonCode);
        });

        connection.on('error', function (error) {
            console.log('Connection error: ' + error.toString());
        });
        
        send(connection, input_path);
    });

    ws.connect(uri, null, null, { 'Ocp-Apim-Subscription-Key' : key });
}

connect();
```

**Ergebnis der Sprachübersetzung**

Bei erfolgreicher Ausführung wird eine Datei namens „speak2.wav“ erstellt. Die Datei enthält die Übersetzung der in „speak.wav“ gesprochenen Sprache.

[Nach oben](#HOLTop)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial zur Sprachübersetzung](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>Weitere Informationen 

[Übersicht über die Sprachübersetzungs-API](../overview.md)
[API-Referenz](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
