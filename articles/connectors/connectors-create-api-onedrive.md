<properties
pageTitle="Hinzufügen der OneDrive-API zu PowerApps Enterprise- oder Logik-Apps | Microsoft Azure"
description="Übersicht über die OneDrive-API und REST-API-Parameter"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="03/16/2016"
ms.author="mandia"/>

# Erste Schritte mit der OneDrive-API

Stellen Sie eine Verbindung mit OneDrive her, um Ihre Dateien zu verwalten, d. h. diese hochzuladen, abzurufen, zu löschen usw. Die OneDrive-API kann in Folgendem verwendet werden:

- Logik-Apps 
- PowerApps

> [AZURE.SELECTOR]
- [Logik-Apps](../articles/connectors/connectors-create-api-onedrive.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-onedrive.md)

&nbsp;

>[AZURE.NOTE] Diese Version des Artikels gilt für die Schemaversion 2015-08-01-preview für Logik-Apps.

OneDrive ermöglicht Folgendes:

- Erstellen eines Geschäftsworkflows basierend auf den Daten, die aus OneDrive abgerufen werden. 
- Verwenden von Triggern, wenn eine Datei erstellt oder aktualisiert wird.
- Verwenden von Aktionen, um z. B. eine Datei zu kopieren oder zu löschen. Diese Aktionen erhalten eine Antwort und stellen anschließend die Ausgabe anderen Aktionen zur Verfügung. Wenn eine neue Datei in OneDrive erstellt wird, können Sie die Datei mit Office 365 per E-Mail senden.
- Hinzufügen der OneDrive-API zu PowerApps Enterprise. Die Benutzer können diese API anschließend in ihren Apps verwenden. 

Informationen zum Hinzufügen einer API in PowerApps Enterprise finden Sie unter [Registrieren einer API in PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Informationen zum Hinzufügen eines Vorgangs in Logik-Apps finden Sie unter [Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger und Aktionen
Die OneDrive-API weist die folgenden Trigger und Aktionen auf.

| Trigger | Aktionen|
| --- | --- |
|<ul><li>Wenn eine Datei erstellt wird</li><li>Wenn eine Datei geändert wird</li></ul> | <ul><li>Datei erstellen</li><li>Dateien in einem Ordner auflisten</li><li>Wenn eine Datei erstellt wird</li><li>Datei kopieren</li><li>Datei löschen</li><li>Ordner extrahieren</li><li>Dateiinhalt anhand der ID abrufen</li><li>Dateiinhalt anhand des Pfads abrufen</li><li>Dateimetadaten anhand der ID abrufen</li><li>Dateimetadaten anhand des Pfads abrufen</li><li>Stammordner auflisten</li><li>Datei aktualisieren</li><li>Wenn eine Datei geändert wird</li></ul>

Alle APIs unterstützen Daten im JSON- und XML-Format.

## Herstellen einer Verbindung mit OneDrive

Wenn Sie diese API Ihren Logik-Apps hinzufügen, müssen Sie ihnen das Herstellen einer Verbindung mit OneDrive erlauben.

1. Melden Sie sich bei Ihrem OneDrive-Konto an.
2. Erlauben Sie, dass Ihre Logik-Apps sich mit Ihrem OneDrive verbinden und es nutzen. 

Nachdem Sie eine Verbindung hergestellt haben, geben Sie die OneDrive-Eigenschaften ein, z. B. Ordnerpfad oder Dateiname. In der **REST-API-Referenz** in diesem Thema werden diese Eigenschaften beschrieben.

>[AZURE.TIP] Sie können dieselbe Verbindung in anderen Logik-Apps verwenden.

## Swagger-REST-API – Referenz
Gilt für Version: 1.0.


### Dateimetadaten anhand der ID abrufen
Ruft Metadaten einer Datei in OneDrive anhand der ID ab. ```GET: /datasets/default/files/{id}```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|id|string|Ja|path|(Keine)|Eindeutiger Bezeichner der Datei in OneDrive|

### Antworten
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Datei aktualisieren
Aktualisiert eine Datei auf OneDrive. ```PUT: /datasets/default/files/{id}```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|id|string|Ja|path|(Keine)|Eindeutiger Bezeichner der Datei, die in OneDrive aktualisiert werden soll|
|body| |Ja|body|(Keine)|Inhalt der Datei, die in OneDrive aktualisiert werden soll|


### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|

### Datei löschen
Löscht eine Datei aus OneDrive. ```DELETE: /datasets/default/files/{id}```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|id|string|Ja|path|(Keine)|Eindeutiger Bezeichner der aus OneDrive zu löschenden Datei|


### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Dateimetadaten anhand des Pfads abrufen
Ruft Metadaten einer Datei auf OneDrive anhand des Pfads ab. ```GET: /datasets/default/GetFileByPath```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|path|string|Ja|query|(Keine)|Eindeutiger Pfad zur Datei in OneDrive|


### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|




### Dateiinhalt anhand des Pfads abrufen
Ruft den Inhalt einer Datei auf OneDrive anhand des Pfads ab. ```GET: /datasets/default/GetFileContentByPath```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|path|string|Ja|query|(Keine)|Eindeutiger Pfad zur Datei in OneDrive|


### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|




### Dateiinhalt anhand der ID abrufen
Ruft den Inhalt einer Datei auf OneDrive anhand der ID ab. ```GET: /datasets/default/files/{id}/content```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|id|string|Ja|path|(Keine)|Eindeutiger Bezeichner der Datei in OneDrive|


### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|




### Datei erstellen
Lädt eine Datei auf OneDrive hoch. ```POST: /datasets/default/files```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|folderPath|string|Ja|query|(Keine)|Ordnerpfad zum Hochladen der Datei in OneDrive|
|name|string|Ja|query|(Keine)|Name der Datei, die in OneDrive erstellt werden soll|
|body| |Ja|body|(Keine)|Inhalt der Datei, die in OneDrive hochgeladen werden soll|


### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|



### Datei kopieren
Kopiert eine Datei auf OneDrive. ```POST: /datasets/default/copyFile```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Quelle|string|Ja|query|(Keine)|URL zur Quelldatei|
|Ziel|string|Ja|query|(Keine)|Zieldateipfad in OneDrive, einschließlich Zieldateiname|
|overwrite|Boolescher Wert|no|query|false|Überschreibt die Zieldatei, falls auf „True“ festgelegt|


### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|



### Wenn eine Datei erstellt wird
Löst einen Datenfluss aus, wenn in einem OneDrive-Ordner eine neue Datei erstellt wird. ```GET: /datasets/default/triggers/onnewfile```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|folderId|string|Ja|query|(Keine)|Eindeutiger Bezeichner des Ordners in OneDrive|


### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|



### Löst einen Datenfluss aus, wenn in einem OneDrive-Ordner eine Datei geändert wird.
Löst einen Datenfluss aus, wenn in einem OneDrive-Ordner eine Datei geändert wird. ```GET: /datasets/default/triggers/onupdatedfile```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|folderId|string|Ja|query|(Keine)|Eindeutiger Bezeichner des Ordners in OneDrive|


### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|



### Ordner extrahieren
Extrahiert einen Ordner auf OneDrive. ```POST: /datasets/default/extractFolderV2```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Quelle|string|Ja|query|(Keine)|Pfad zur Archivdatei|
|Ziel|string|Ja|query|(Keine)|Pfad in OneDrive, in den der Archivinhalt extrahiert wird|
|overwrite|Boolescher Wert|no|query|false|Überschreibt die Zieldateien, falls auf „True“ festgelegt|


### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|



## Objektdefinitionen

#### DataSetsMetadata

|Eigenschaftenname | Datentyp | Erforderlich|
|---|---|---|
|tabular|nicht definiert|no|
|Blob|nicht definiert|no|


#### TabularDataSetsMetadata

|Eigenschaftenname | Datentyp |Erforderlich|
|---|---|---|
|Quelle|string|no|
|displayName|string|no|
|urlEncoding|string|no|
|tableDisplayName|string|no|
|tablePluralName|string|no|


#### BlobDataSetsMetadata

|Eigenschaftenname | Datentyp |Erforderlich|
|---|---|---|
|Quelle|string|no|
|displayName|string|no|
|urlEncoding|string|no|



#### BlobMetadata

|Eigenschaftenname | Datentyp |Erforderlich|
|---|---|---|
|ID|string|no|
|Name|string|no|
|DisplayName|string|no|
|Path|string|no|
|LastModified|string|no|
|Größe|integer|no|
|MediaType|string|no|
|IsFolder|Boolescher Wert|no|
|ETag|string|no|
|FileLocator|string|no|


## Nächste Schritte

[Erstellen Sie eine Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).

Gehen Sie zur [Liste der APIs](apis-list.md) zurück.

[5]: https://account.live.com/developers/applications/create
[6]: ./media/connectors-create-api-onedrive/onedrive-new-app.png
[7]: ./media/connectors-create-api-onedrive/onedrive-app-api-settings.png

<!---HONumber=AcomDC_0413_2016-->