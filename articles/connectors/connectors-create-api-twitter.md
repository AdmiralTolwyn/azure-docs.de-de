<properties
	pageTitle="Hinzufügen des Twitter-Connectors in PowerApps Enterprise- und Logik-Apps | Microsoft Azure"
	description="Übersicht über den Twitter-Connector mit REST-API-Parametern"
	services=""
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="05/18/2016"
   ms.author="mandia"/>


# Erste Schritte mit dem Twitter-Connector
Verbinden Sie sich mit Twitter, um einen Tweet zu posten, die Timeline eines Benutzers abzurufen und mehr. Der Twitter-Connector kann verwendet werden in:

- Logik-Apps 
- PowerApps

> [AZURE.SELECTOR]
- [Logik-Apps](../articles/connectors/connectors-create-api-twitter.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-twitter.md)

&nbsp;

>[AZURE.NOTE] Diese Version des Artikels gilt für die Schemaversion 2015-08-01-preview für Logik-Apps.

Twitter ermöglicht Folgendes:

- Einen Geschäftsworkflow basierend auf den Daten erstellen, die aus Twitter abgerufen werden. 
- Verwenden von Triggern, wenn ein neuer Tweet vorhanden ist.
- Verwenden von Aktionen, um z. B. einen Tweet zu posten oder Tweets zu suchen. Diese Aktionen erhalten eine Antwort und stellen anschließend die Ausgabe anderen Aktionen zur Verfügung. Wenn ein neuer Tweet angezeigt wird, können Sie z. B. diesen Tweet auf Facebook posten.
- Fügen Sie den Twitter-Connector in PowerApps Enterprise hinzu. Die Benutzer können diesen Connector anschließend in ihren Apps verwenden. 

Informationen zum Hinzufügen eines Connectors in PowerApps Enterprise finden Sie unter [Registrieren einer Microsoft-verwalteten API oder einer IT-verwalteten API](../power-apps/powerapps-register-from-available-apis.md).

Informationen zum Hinzufügen eines Vorgangs in Logik-Apps finden Sie unter [Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).


## Trigger und Aktionen
Twitter weist die folgenden Trigger und Aktionen auf.

Trigger | Aktionen
--- | ---
<ul><li>Wenn ein neuer Tweet angezeigt wird</li></ul>| <ul><li>Einen neuen Tweet posten</li><li>Wenn ein neuer Tweet angezeigt wird</li><li>Eigene Timeline abrufen</li><li>Benutzer abrufen</li><li>Benutzertimeline abrufen</li><li>Tweet suchen</li><li>Follower abrufen</li><li>Meine Follower abrufen</li><li>Gefolge abrufen</li><li>Mein Gefolge</li></ul>

Alle Connectors unterstützen Daten im JSON- und XML-Format.


## Herstellen der Verbindung mit Twitter

Wenn Sie diesen Connector Ihren Logik-Apps hinzufügen, müssen Sie ihnen das Herstellen einer Verbindung mit Ihrem Twitter-Konto erlauben.

1. Melden Sie sich bei Ihrem Twitter-Konto an.
2. Wählen Sie **Autorisieren**, um zu erlauben, dass Ihre Logik-Apps sich mit Ihrem Twitter-Konto verbinden und es nutzen. 

>[AZURE.INCLUDE [Schritte zum Herstellen einer Verbindung mit Twitter](../../includes/connectors-create-api-twitter.md)]

>[AZURE.TIP] Sie können dieselbe Twitter-Verbindung in anderen Logik-Apps verwenden.


## Swagger-REST-API – Referenz
Gilt für Version: 1.0.

### Neuen Tweet posten 
Tweeten. ```POST: /posttweet```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|tweetText|string|no|query|(Keine)|Text, der gepostet werden soll|
|body| string (binary) |no|body|(Keine)|Zu veröffentlichende Medien|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|400|Ungültige Anforderung|
|401|Nicht autorisiert|
|403|Verboten (403)|
|404|Nicht gefunden|
|500|Interner Serverfehler. Unbekannter Fehler aufgetreten|
|default|Fehler beim Vorgang.|


### Wenn ein neuer Tweet angezeigt wird 
Löst einen Workflow aus, wenn ein neuer Tweet gepostet wird, der mit der Suchabfrage übereinstimmt. ```GET: /onnewtweet```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|searchQuery|string|Ja|query|(Keine)|Abfragetext (Sie können alle von Twitter unterstützten Abfrageoperatoren verwenden: http://www.twitter.com/search)|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|202|Zulässig|
|400|Ungültige Anforderung|
|401|Nicht autorisiert|
|403|Verboten (403)|
|404|Nicht gefunden|
|500|Interner Serverfehler. Unbekannter Fehler aufgetreten|
|default|Fehler beim Vorgang.|


### Eigene Timeline abrufen 
Ruft die neuesten Tweets und Retweets ab, die von mir und meinen Followern gepostet wurden. ```GET: /hometimeline```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|maxResults|integer|no|query|20|Maximale Anzahl abzurufender Tweets|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|400|Ungültige Anforderung|
|401|Nicht autorisiert|
|403|Verboten (403)|
|404|Nicht gefunden|
|500|Interner Serverfehler. Unbekannter Fehler aufgetreten|
|default|Fehler beim Vorgang.|


### Benutzer abrufen 
Ruft Details zum angegebenen Benutzer ab (Beispiel: Benutzername, Beschreibung, Anzahl der Follower usw.). ```GET: /user```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|userName|string|Ja|query|(Keine)|Twitter-Handle des Benutzers|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|400|Ungültige Anforderung|
|401|Nicht autorisiert|
|403|Verboten (403)|
|404|Nicht gefunden|
|500|Interner Serverfehler. Unbekannter Fehler aufgetreten|
|default|Fehler beim Vorgang.|


### Timeline des Benutzers abrufen 
Ruft eine Auflistung der neuesten Tweets ab, die vom angegebenen Benutzer gepostet wurden. ```GET: /usertimeline```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|userName|string|Ja|query|(Keine)|Twitter-Handle|
|maxResults|integer|no|query|20|Maximale Anzahl abzurufender Tweets|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|400|Ungültige Anforderung|
|401|Nicht autorisiert|
|403|Verboten (403)|
|404|Nicht gefunden|
|500|Interner Serverfehler. Unbekannter Fehler aufgetreten|
|default|Fehler beim Vorgang.|


### Tweet suchen 
Ruft eine Auflistung relevanter Tweets ab, die mit einer angegebenen Abfrage übereinstimmen. ```GET: /searchtweets```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|searchQuery|string|Ja|query|(Keine)|Abfragetext (Sie können alle von Twitter unterstützten Abfrageoperatoren verwenden: http://www.twitter.com/search)|
|maxResults|integer|no|query|20|Maximale Anzahl abzurufender Tweets|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|400|Ungültige Anforderung|
|401|Nicht autorisiert|
|403|Verboten (403)|
|404|Nicht gefunden|
|500|Interner Serverfehler. Unbekannter Fehler aufgetreten|
|default|Fehler beim Vorgang.|


### Follower abrufen 
Ruft die Benutzer, die dem angegebenen Benutzer folgen. ```GET: /followers```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|userName|string|Ja|query|(Keine)|Twitter-Handle des Benutzers|
|maxResults|integer|no|query|20|Maximale Anzahl abzurufender Benutzer|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|400|Ungültige Anforderung|
|401|Nicht autorisiert|
|403|Verboten (403)|
|404|Nicht gefunden|
|500|Interner Serverfehler. Unbekannter Fehler aufgetreten|
|default|Fehler beim Vorgang.|


### Meine Follower abrufen 
Ruft Benutzer ab, die mir folgen. ```GET: /myfollowers```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|maxResults|integer|no|query|20|Maximale Anzahl abzurufender Benutzer|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|400|Ungültige Anforderung|
|401|Nicht autorisiert|
|403|Verboten (403)|
|404|Nicht gefunden|
|500|Interner Serverfehler. Unbekannter Fehler aufgetreten|
|default|Fehler beim Vorgang.|


### Gefolge abrufen 
Ruft Benutzer ab, denen der angegebene Benutzer folgt. ```GET: /friends```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|userName|string|Ja|query|(Keine)|Twitter-Handle des Benutzers|
|maxResults|integer|no|query|20|Maximale Anzahl abzurufender Benutzer|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|400|Ungültige Anforderung|
|401|Nicht autorisiert|
|403|Verboten (403)|
|404|Nicht gefunden|
|500|Interner Serverfehler. Unbekannter Fehler aufgetreten|
|default|Fehler beim Vorgang.|


### Mein Gefolge abrufen 
Ruft Benutzer ab, denen ich folge. ```GET: /myfriends```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|maxResults|integer|no|query|20|Maximale Anzahl abzurufender Benutzer|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|400|Ungültige Anforderung|
|401|Nicht autorisiert|
|403|Verboten (403)|
|404|Nicht gefunden|
|500|Interner Serverfehler. Unbekannter Fehler aufgetreten|
|default|Fehler beim Vorgang.|


## Objektdefinitionen

### TweetModel: Darstellung des Tweet-Objekts

| Eigenschaftenname | Datentyp | Erforderlich |
|---|---| --- | 
|TweetText|string|Ja|
|TweetId|string|no|
|CreatedAt|string|no|
|RetweetCount|integer|Ja|
|TweetedBy|string|Ja|
|MediaUrls|array|no|

### UserDetailsModel: Darstellung der Details eines Twitter-Benutzers

|Eigenschaftenname | Datentyp | Erforderlich |
|---|---|---|
|FullName|string|Ja|
|Standort|string|Ja|
|ID|integer|no|
|UserName|string|Ja|
|FollowersCount|integer|no|
|Beschreibung|string|Ja|
|StatusesCount|integer|no|
|FriendsCount|integer|no|

### TweetResponseModel: Modell zur Darstellung eines geposteten Tweets

| Name | Datentyp | Erforderlich |
|---|---|---|
|TweetId|string|Ja|

### TriggerBatchResponse[TweetModel]

|Eigenschaftenname | Datentyp |Erforderlich |
|---|---|---|
|value|array|no|


## Nächste Schritte

[Erstellen Sie eine Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).

Gehen Sie zur [Liste der APIs](apis-list.md) zurück.

<!--References-->

[6]: ./media/connectors-create-api-twitter/twitter-apps-page.png
[7]: ./media/connectors-create-api-twitter/twitter-app-create.png

<!---HONumber=AcomDC_0525_2016-->