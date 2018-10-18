---
title: 'Schnellstart: Abfragen von Daten mit der Azure-Daten-Explorer-Bibliothek für Python'
description: In diesem Schnellstart erfahren Sie, wie Sie mit Python Daten vom Azure-Daten-Explorer abfragen.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: 17504cec6ddf98aca8ddfc6c91d21d34055902f6
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394851"
---
# <a name="quickstart-query-data-using-the-azure-data-explorer-python-library"></a>Schnellstart: Abfragen von Daten mit der Azure-Daten-Explorer-Bibliothek für Python

Azure-Daten-Explorer ist ein schneller und hochgradig skalierbarer Dienst zur Untersuchung von Daten (Protokoll- und Telemetriedaten). Der Azure-Daten-Explorer bietet eine [Datenclientbibliothek für Python](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data). Mit dieser Bibliothek können Sie Daten aus Ihrem Code abfragen. In diesem Schnellstart stellen Sie eine Verbindung mit einer Tabelle im *Hilfecluster* her, den wir zur Hilfestellung beim Lernen eingerichtet haben. Sie fragen dann eine Tabelle in diesem Cluster ab und geben die Ergebnisse zurück.

Dieser Schnellstart ist auch als [Azure-Notebook](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueryKusto.ipynb) verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Organisations-E-Mail-Konto, das Mitglied von Azure Active Directory (AAD) ist

* [Python](https://www.python.org/downloads/) auf dem Entwicklungscomputer

## <a name="install-the-data-library"></a>Installieren der Datenbibliothek

Installieren Sie *azure-kusto-data*.

```python
pip install azure-kusto-data==0.0.13
```

## <a name="add-import-statements-and-constants"></a>Hinzufügen von Importanweisungen und Konstanten

Importieren Sie Klassen aus der Bibliothek sowie *pandas*, eine Datenanalysebibliothek.

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.kusto.data.exceptions import KustoServiceError
from azure.kusto.data.helpers import dataframe_from_result_table
import pandas as pd
```

Für die Authentifizierung von Anwendungen verwendet der Azure-Daten-Explorer Ihre AAD-Mandanten-ID. Um Ihre Mandanten-ID zu suchen, verwenden Sie die folgende URL, und ersetzen Sie dabei *YourDomain* durch Ihre Domäne.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Wenn Ihre Domäne beispielsweise *contoso.com* ist, lautet die URL: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Klicken Sie auf diese URL, um die Ergebnisse anzuzeigen. Die erste Zeile lautet wie folgt.

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Die Mandanten-ID ist in diesem Fall `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. Legen Sie den Wert für AAD_TENANT_ID fest, bevor Sie diesen Code ausführen.

```python
AAD_TENANT_ID = "<TenantId>"
KUSTO_CLUSTER = "https://help.kusto.windows.net/"
KUSTO_DATABASE  = "Samples"
```

Erstellen Sie nun die Verbindungszeichenfolge. In diesem Beispiel wird die Geräteauthentifizierung zum Zugreifen auf den Cluster verwendet. Sie können auch das AAD-Anwendungszertifikat, den AAD-Anwendungsschlüssel und den AAD-Benutzer mit dem zugehörigen Kennwort verwenden.

```python
KCSB = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_CLUSTER)
KCSB.authority_id = AAD_TENANT_ID
```

## <a name="connect-to-azure-data-explorer-and-execute-a-query"></a>Herstellen einer Verbindung mit dem Azure-Daten-Explorer und Ausführen einer Abfrage

Führen Sie eine Abfrage auf dem Cluster aus, und speichern Sie die Ausgabe in einem Datenrahmen. Wenn dieser Code ausgeführt wird, gibt er eine Meldung wie die folgende zurück: *Verwenden Sie zum Anmelden einen Webbrowser, um die Seite https://microsoft.com/devicelogin zu öffnen, und geben Sie den Code F3W4VWZDM ein, um sich zu authentifizieren*. Befolgen Sie die Schritte für die Anmeldung, und kehren Sie dann zurück, um den Codeblock auszuführen.

```python
KUSTO_CLIENT  = KustoClient(KCSB)
KUSTO_QUERY  = "StormEvents | sort by StartTime desc | take 10"

RESPONSE = KUSTO_CLIENT.execute(KUSTO_DATABASE, KUSTO_QUERY)
```

## <a name="explore-data-in-dataframe"></a>Durchsuchen von Daten im Datenrahmen

Nachdem Sie eine Anmeldung eingeben, gibt die Abfrage Ergebnisse zurück, die daraufhin in einem Datenrahmen gespeichert werden. Sie können mit den Ergebnissen wie mit allen anderen Datenrahmen arbeiten.

```python
df = dataframe_from_result_table(RESPONSE.primary_results[0])
df
```

Es sollten die ersten zehn Ergebnisse aus der Tabelle „StormEvents“ angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schnellstart: Erfassen von Daten mit der Azure-Daten-Explorer-Bibliothek für Python](python-ingest-data.md)