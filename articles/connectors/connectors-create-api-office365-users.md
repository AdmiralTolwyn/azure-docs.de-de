<properties
    pageTitle="Hinzufügen des Office 365-Benutzer-Connectors zu PowerApps Enterprise oder Logik-Apps | Microsoft Azure"
    description="Übersicht über den Office 365-Benutzer-Connector mit REST-API-Parametern"
    services=""    
    documentationCenter=""     
    authors="msftman"    
    manager="erikre"    
    editor="" 
    tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="05/18/2016"
ms.author="deonhe"/>

# Erste Schritte mit dem Office 365-Benutzer-Connector

Stellen Sie eine Verbindung mit der Office 365-Benutzer-API her, um Profile abzurufen, Benutzer zu suchen und vieles mehr. Der Office 365 Benutzer-Connector kann verwendet werden in:

- Logik-Apps 
- PowerApps

> [AZURE.SELECTOR]
- [Logik-Apps](../articles/connectors/connectors-create-api-office365-users.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-office365-users.md)

&nbsp;

>[AZURE.NOTE] Diese Version des Artikels gilt für die Schemaversion 2015-08-01-preview für Logik-Apps.


Mit der Office 365-Benutzer-API können Sie folgende Aktionen ausführen:

- Erstellen eines Geschäftsworkflows basierend auf den Daten, die aus der Office 365-Benutzer-API abgerufen werden. 
- Verwenden von Aktionen zum Abrufen von direkt unterstellten Mitarbeitern oder Benutzerprofilen von Vorgesetzten und vieles mehr. Diese Aktionen erhalten eine Antwort und stellen anschließend die Ausgabe anderen Aktionen zur Verfügung. Rufen Sie beispielsweise die direkt unterstellten Mitarbeiter einer Person ab, und aktualisieren Sie mit diesen Informationen eine Azure SQL-Datenbank. 
- Fügen Sie den Office 365-Benutzer-Connector in PowerApps Enterprise hinzu. Die Benutzer können diesen Connector anschließend in ihren Apps verwenden. 

Informationen zum Hinzufügen eines Connectors in PowerApps Enterprise finden Sie unter [Registrieren einer Microsoft-verwalteten API oder einer IT-verwalteten API](../power-apps/powerapps-register-from-available-apis.md).

Informationen zum Hinzufügen eines Vorgangs in Logik-Apps finden Sie unter [Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger und Aktionen

Der Office 365-Benutzer-Connector verfügt über die folgenden Aktionen. Es gibt keine Trigger.

| Trigger | Aktionen|
| --- | --- |
|Keine | <ul><li>Vorgesetzten abrufen</li><li>Mein Profil abrufen</li><li>Direkt unterstellte Mitarbeiter abrufen</li><li>Benutzerprofil abrufen</li><li>Nach Benutzern suchen</li></ul>|

Alle Connectors unterstützen Daten im JSON- und XML-Format.


## Herstellen einer Verbindung mit Office 365-Benutzern

Wenn Sie Ihren Logik-Apps diesen Connector hinzufügen, müssen Sie sich bei Ihrem Office 365-Benutzer-Konto anmelden und den Logik-Apps das Herstellen einer Verbindung mit Ihrem Konto erlauben.

>[AZURE.INCLUDE [Schritte zum Herstellen einer Verbindung mit Office 365-Benutzer](../../includes/connectors-create-api-office365users.md)]

Nachdem Sie eine Verbindung hergestellt haben, geben Sie die Eigenschaften für die Office 365-Benutzer-API ein, z. B. die Benutzer-ID. In der **REST-API-Referenz** in diesem Thema werden diese Eigenschaften beschrieben.

>[AZURE.TIP] Sie können dieselbe Verbindung der Office 365-Benutzer-API in anderen Logik-Apps verwenden.


## Office 365-Benutzer-REST-API – Referenz
Gilt für Version: 1.0.

### Mein Profil abrufen 
Ruft das Benutzerprofil für den aktuellen Benutzer ab. ```GET: /users/me```

Es gibt keine Parameter für diesen Aufruf.

#### Antwort

|Name|Beschreibung|
|---|---|
|200|Vorgang erfolgreich|
|202|Vorgang erfolgreich|
|400|BadRequest|
|401|Nicht autorisiert|
|403|Verboten (403)|
|500|Interner Serverfehler|
|default|Fehler beim Vorgang.|


### Benutzerprofil abrufen 
Ruft ein bestimmtes Benutzerprofil ab. ```GET: /users/{userId}```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|bei der ersten|string|Ja|path|(Keine)|Benutzerprinzipalname oder E-Mail-ID|

#### Antwort

|Name|Beschreibung|
|---|---|
|200|Vorgang erfolgreich|
|202|Vorgang erfolgreich|
|400|BadRequest|
|401|Nicht autorisiert|
|403|Verboten (403)|
|500|Interner Serverfehler|
|default|Fehler beim Vorgang.|


### Vorgesetzten abrufen 
Ruft das Benutzerprofil des Vorgesetzten des angegebenen Benutzers ab. ```GET: /users/{userId}/manager```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|bei der ersten|string|Ja|path|(Keine)|Benutzerprinzipalname oder E-Mail-ID|

#### Antwort

|Name|Beschreibung|
|---|---|
|200|Vorgang erfolgreich|
|202|Vorgang erfolgreich|
|400|BadRequest|
|401|Nicht autorisiert|
|403|Verboten (403)|
|500|Interner Serverfehler|
|default|Fehler beim Vorgang.|



### Direkt unterstellte Mitarbeiter abrufen 
Direkt unterstellte Mitarbeiter abrufen. ```GET: /users/{userId}/directReports```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|bei der ersten|string|Ja|path|(Keine)|Benutzerprinzipalname oder E-Mail-ID|

#### Antwort

|Name|Beschreibung|
|---|---|
|200|Vorgang erfolgreich|
|202|Vorgang erfolgreich|
|400|BadRequest|
|401|Nicht autorisiert|
|403|Verboten (403)|
|500|Interner Serverfehler|
|default|Fehler beim Vorgang.|



### Nach Benutzern suchen 
Ruft die Suchergebnisse für Benutzerprofile ab. ```GET: /users```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|searchTerm|string|no|query|(Keine)|Suchzeichenfolge (gilt für: Anzeigename, Vorname, Nachname, E-Mail, E-Mail-Spitzname und Benutzerprinzipalname)|

#### Antwort

|Name|Beschreibung|
|---|---|
|200|Vorgang erfolgreich|
|202|Vorgang erfolgreich|
|400|BadRequest|
|401|Nicht autorisiert|
|403|Verboten (403)|
|500|Interner Serverfehler|
|default|Fehler beim Vorgang.|



## Objektdefinitionen

#### Benutzer: Modellklasse „User“.

|Eigenschaftenname | Datentyp |Erforderlich
|---|---|---|
|DisplayName|string|no|
|GivenName|string|no|
|Surname|string|no|
|Mail|string|no|
|MailNickname|string|no|
|TelephoneNumber|string|no|
|AccountEnabled|Boolescher Wert|no|
|ID|string|Ja
|UserPrincipalName|string|no|
|Abteilung|string|no|
|JobTitle|string|no|
|mobilePhone|string|no|


## Nächste Schritte

[Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).

Gehen Sie zur [Liste der APIs](apis-list.md) zurück.

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/connectors-create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/connectors-create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/connectors-create-api-office365-users/contoso-aad-app-configure.PNG

<!---HONumber=AcomDC_0525_2016-->