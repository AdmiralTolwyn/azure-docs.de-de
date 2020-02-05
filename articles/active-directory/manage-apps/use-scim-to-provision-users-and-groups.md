---
title: Erstellen eines SCIM-Endpunkts für die Benutzerbereitstellung in Apps aus Azure AD
description: Erfahren Sie, wie Sie einen SCIM-Endpunkt erstellen, Ihre SCIM-API in Azure Active Directory integrieren und mit der Automatisierung der Bereitstellung von Benutzern und Gruppen in Ihren Cloudanwendungen beginnen.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c2f0abeab31fc64fceb10bf17ef90924efefa22
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841215"
---
# <a name="build-a-scim-endpoint-and-configure-user-provisioning-with-azure-active-directory-azure-ad"></a>Erstellen eines SCIM-Endpunkts und Konfigurieren der Benutzerbereitstellung mit Azure Active Directory (Azure AD)

Als Anwendungsentwickler können Sie die SCIM-Benutzerverwaltungs-API (System for Cross-Domain Identity Management, System für die domänenübergreifende Identitätsverwaltung) verwenden, um die automatische Bereitstellung von Benutzern und Gruppen zwischen Ihrer Anwendung und Azure AD zu aktivieren. In diesem Artikel wird beschrieben, wie ein SCIM-Endpunkt erstellt und in den Azure AD-Bereitstellungsdienst integriert wird. Die SCIM-Spezifikation bietet ein allgemeines Benutzerschema für die Bereitstellung. Bei der Verwendung mit Verbundstandards wie SAML oder OpenID Connect bietet SCIM Administratoren eine auf Standards basierende End-to-End-Lösung für die Zugriffsverwaltung.

SCIM ist eine standardisierte Definition von zwei Endpunkten: einem „/Users“- und einem „/Group“-Endpunkt. Es verwendet allgemeine REST-Verben zum Erstellen, Aktualisieren und Löschen von Objekten und ein vordefiniertes Schema für allgemeine Attribute wie Gruppenname, Benutzername, Vorname, Nachname und E-Mail-Adresse. Apps, die eine SCIM 2.0 REST-API bieten, können den Aufwand für die Arbeit mit einer proprietären Benutzerverwaltungs-API reduzieren oder eliminieren. So ist z. B. jeder konforme SCIM-Client in der Lage, ein HTTP POST für ein JSON-Objekt an den „/Users“-Endpunkt zu senden, um einen neuen Benutzereintrag zu erstellen. Anstatt eine leicht abweichende API für dieselben grundlegenden Aktionen zu benötigen, können Apps, die dem SCIM-Standard entsprechen, sofort die Vorteile bereits vorhandener Clients, Tools und Codes nutzen. 

![Bereitstellen von Azure AD für eine App mit SCIM](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

Das in SCIM 2.0 definierte Standard-Benutzerobjektschema und die REST-APIs für die Verwaltung (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644)) ermöglichen eine einfachere Integration von Identitätsanbietern und Apps. Anwendungsentwickler, die einen SCIM-Endpunkt erstellen, können die Integration mit jedem SCIM-konformen Client durchführen, ohne selbst Anpassungen vornehmen zu müssen.

Die Automatisierung der Bereitstellung für eine Anwendung erfordert die Erstellung und Integration eines SCIM-Endpunkts mit dem SCIM-konformen Azure AD-Client. Führen Sie die folgenden Schritte aus, um die Bereitstellung von Benutzern und Gruppen in Ihrer Anwendung zu starten. 
    
  * **[Schritt 1: Entwerfen Ihres Benutzer- und Gruppenschemas](#step-1-design-your-user-and-group-schema)** Identifizieren Sie die Objekte und Attribute, die Ihre Anwendung benötigt, und legen Sie fest, wie sie dem von der Azure AD SCIM-Implementierung unterstützten Benutzer- und Gruppenschema zugeordnet werden.

  * **[Schritt 2: Verstehen der Azure AD-SCIM-Implementierung](#step-2-understand-the-azure-ad-scim-implementation)** Erfahren Sie, wie der Azure AD SCIM-Client implementiert wird, und modellieren Sie die Behandlung sowie die Antworten Ihrer SCIM-Protokollanforderung.

  * **[Schritt 3: Erstellen eines SCIM-Endpunkts](#step-3-build-a-scim-endpoint)** Ein Endpunkt muss SCIM 2.0-kompatibel sein, damit er in den Azure AD-Bereitstellungsdienst integriert werden kann. Optional können Sie Bibliotheken und Codebeispiele der Microsoft Common Language Infrastructure (CLI) verwenden, um Ihren Endpunkt zu erstellen. Diese Beispiele dienen nur als Referenz und für Tests. Es wird nicht empfohlen, Ihre Produktionsanwendung so zu codieren, dass sie von ihnen abhängig wird.

  * **[Schritt 4: Integrieren Ihres SCIM-Endpunkts mit dem Azure AD SCIM-Client](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** Wenn Ihre Organisation eine Drittanbieteranwendung verwendet, die das von Azure AD unterstützte Profil von SCIM 2.0 implementiert, können Sie sofort mit der Automatisierung der Bereitstellung und der Aufhebung der Bereitstellung von Benutzern und Gruppen beginnen.

  * **[Schritt 5: Veröffentlichen Ihrer Anwendung im Azure AD-Anwendungskatalog](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** Erleichtern Sie es Ihren Kunden, Ihre Anwendung zu entdecken und die Bereitstellung einfach zu konfigurieren. 

![Schritte zur Integration eines SCIM-Endpunkts mit Azure AD](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>Schritt 1: Entwerfen Ihres Benutzer- und Gruppenschemas

Jede Anwendung erfordert unterschiedliche Attribute, um einen Benutzer oder eine Gruppe zu erstellen. Starten Sie Ihre Integration, indem Sie die Objekte (Benutzer, Gruppen) und Attribute (Name, Vorgesetzter, Positionsbezeichnung, usw.) identifizieren, die Ihre Anwendung benötigt. Sie können dann die folgende Tabelle verwenden, um zu verstehen, wie die von Ihrer Anwendung benötigten Attribute einem Attribut in Azure AD und dem SCIM RFC zugeordnet werden können. Beachten Sie, dass Sie [anpassen](customize-application-attributes.md) können, wie Attribute zwischen Azure AD und Ihrem SCIM-Endpunkt zugeordnet werden. 

Benutzerressourcen werden durch den Schemabezeichner `urn:ietf:params:scim:schemas:extension:enterprise:2.0:User` identifiziert, der in dieser Protokollspezifikation enthalten ist: https://tools.ietf.org/html/rfc7643.  Die Standardzuordnung der Attribute von Benutzern in Azure AD zu den Attributen von Benutzerressourcen ist in Tabelle 1 angegeben.  

Gruppenressourcen werden durch die Schema-ID, `urn:ietf:params:scim:schemas:core:2.0:Group`, identifiziert. Die Standardzuordnung der Attribute von Gruppen in Azure AD zu den Attributen von Gruppenressourcen ist in Tabelle 2 angegeben.

Beachten Sie, dass Sie nicht sowohl Benutzer als auch Gruppen oder alle unten aufgeführten Attribute unterstützen müssen. Sie sind eine Referenz dafür, wie Attribute in Azure AD oft zu Eigenschaften im SCIM-Protokoll zugeordnet werden.  

### <a name="table-1-default-user-attribute-mapping"></a>Tabelle 1: Standardzuordnung von Benutzerattributen

| Azure Active Directory-Benutzer | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |aktiv |
|department|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|
| displayName |displayName |
|employeeId|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |title |
| mail |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager |
| mobile |phoneNumbers[type eq "mobile"].value |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |


### <a name="table-2-default-group-attribute-mapping"></a>Tabelle 2: Standardzuordnung von Gruppenattributen

| Azure Active Directory-Gruppe | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |displayName |
| mail |emails[type eq "work"].value |
| mailNickname |displayName |
| members |members |
| objectId |externalId |
| proxyAddresses |emails[type eq "other"].Value |

## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>Schritt 2: Verstehen der Azure AD-SCIM-Implementierung
> [!IMPORTANT]
> Das Verhalten der Azure AD-SCIM-Implementierung wurde zuletzt am 18. Dezember 2018 aktualisiert. Informationen zu den Änderungen finden Sie unter [Einhaltung des SCIM 2.0-Protokolls des Azure AD-Benutzerbereitstellungsdiensts](application-provisioning-config-problem-scim-compatibility.md).

Wenn Sie eine Anwendung erstellen, die eine SCIM 2.0-Benutzerverwaltungs-API unterstützt, ist dieser Abschnitt hilfreich. Darin wird detailliert beschrieben, wie der Azure AD-SCIM-Client implementiert wird. Außerdem wird gezeigt, wie Sie die Verarbeitung von SCIM-Protokollanforderungen und -antworten modellieren sollten. Nachdem Sie den SCIM-Endpunkt implementiert haben, können Sie ihn durch Ausführen der im vorherigen Abschnitt beschriebenen Schritte testen.

Im Rahmen der [SCIM 2.0-Protokollspezifikation](http://www.simplecloud.info/#Specification) muss Ihre Anwendung die folgenden Anforderungen erfüllen:

* Unterstützt das Erstellen von Benutzern (und optional auch von Gruppen) gemäß [Abschnitt 3.3 des SCIM-Protokolls](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Unterstützt das Ändern von Benutzern bzw. Gruppen mit PATCH-Anforderungen gemäß [Abschnitt 3.5.2 des SCIM-Protokolls](https://tools.ietf.org/html/rfc7644#section-3.5.2).  
* Unterstützt das Abrufen einer bekannten Ressource für einen zuvor erstellten Benutzer oder eine Gruppe gemäß [Abschnitt 3.4.1 des SCIM-Protokolls](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Unterstützt das Abfragen von Benutzern bzw. Gruppen gemäß [Abschnitt 3.4.2 des SCIM-Protokolls](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Standardmäßig werden Benutzer anhand ihrer `id` abgerufen und nach `username` und `externalid` abgefragt. Gruppen werden nach `displayName` abgefragt.  
* Unterstützt das Abfragen von Benutzern nach ID und nach Manager gemäß Abschnitt 3.4.2 des SCIM-Protokolls.  
* Unterstützt das Abfragen von Gruppen nach ID und nach Mitglied gemäß Abschnitt 3.4.2 des SCIM-Protokolls.  
* Akzeptiert ein einzelnes Bearertoken für die Authentifizierung und Autorisierung von Azure AD für Ihre Anwendung.

Befolgen Sie bei der Implementierung eines SCIM-Endpunkts die folgenden allgemeinen Richtlinien, um die Kompatibilität mit Azure AD sicherzustellen:

* `id` ist eine erforderliche Eigenschaft für alle Ressourcen. Für jede Antwort, die eine Ressource zurückgibt, muss sichergestellt werden, dass jede Ressource diese Eigenschaft aufweist. Eine Ausnahme ist `ListResponse` mit 0 (null) Elementen.
* Eine Antwort auf eine Abfrage-/Filteranforderung muss immer eine `ListResponse` sein.
* Gruppen sind optional, werden jedoch nur unterstützt, wenn die SCIM-Implementierung PATCH-Anforderungen unterstützt.
* Die PATCH-Antwort muss nicht die gesamte Ressource enthalten.
* Microsoft Azure AD verwendet nur die folgenden Operatoren:  
    - `eq`
    - `and`
* Unterscheiden Sie bei Strukturelementen in SCIM nicht zwischen Groß- und Kleinschreibung, insbesondere bei `op`-PATCH-Vorgangswerten gemäß der Definition unter https://tools.ietf.org/html/rfc7644#section-3.5.2. Azure AD gibt die Werte von „op“ mit `Add`, `Replace` und `Remove` aus.
* Microsoft Azure AD sendet Anforderungen zum Abrufen eines zufälligen Benutzers und einer zufälligen Gruppe, um sicherzustellen, dass der Endpunkt und die Anmeldeinformationen gültig sind. Dies erfolgt auch im Rahmen des **Testverbindungsflows** im [Azure-Portal](https://portal.azure.com). 
* Das Attribut, nach dem die Ressourcen abgefragt werden können, sollte als entsprechendes Attribut für die Anwendung im [Azure-Portal](https://portal.azure.com) festgelegt werden. Weitere Informationen finden Sie unter [Anpassen von Attributzuordnungen für die Benutzerbereitstellung für SaaS-Anwendungen in Azure Active Directory](customize-application-attributes.md).

### <a name="user-provisioning-and-deprovisioning"></a>Benutzerbereitstellung und Aufheben der Bereitstellung

In der folgenden Abbildung sind die Nachrichten dargestellt, die Azure Active Directory an einen SCIM-Dienst sendet, um den Lebenszyklus eines Benutzers im Identitätsspeicher Ihrer Anwendung zu verwalten.  

![Zeigt die Abfolge der Durchführung und Aufhebung einer Benutzerbereitstellung](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*Abfolge der Durchführung und Aufhebung einer Benutzerbereitstellung*

### <a name="group-provisioning-and-deprovisioning"></a>Gruppenbereitstellung und Aufheben der Bereitstellung

Die Gruppenbereitstellung und die Aufhebung einer Gruppenbereitstellung sind optional. In der folgenden Abbildung sind die Nachrichten dargestellt, die Azure AD bei entsprechender Implementierung und Aktivierung an einen SCIM-Dienst sendet, um den Lebenszyklus einer Gruppe im Identitätsspeicher Ihrer Anwendung zu verwalten.  Diese Nachrichten unterscheiden sich von den Nachrichten zu Benutzern auf zwei Arten:

* Für Anforderungen zum Abrufen von Gruppen wird angegeben, dass das members-Attribut aus allen Ressourcen ausgeschlossen wird, die als Antwort auf die Anforderung bereitgestellt werden.  
* Bei Anforderungen für die Ermittlung, ob ein Referenzattribut einen bestimmten Wert hat, handelt es sich um Anforderungen zum members-Attribut.  

![Zeigt die Abfolge der Durchführung und Aufhebung einer Gruppenbereitstellung](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Abfolge der Durchführung und Aufhebung einer Gruppenbereitstellung*

### <a name="scim-protocol-requests-and-responses"></a>SCIM-Protokollanforderungen und -antworten
Dieser Abschnitt enthält vom Azure AD-SCIM-Client ausgegebene SCIM-Beispielanforderungen und erwartete Beispielantworten. Die besten Ergebnisse erzielen Sie, wenn Sie Ihre App so codieren, dass diese Anforderungen in diesem Format verarbeitet und die erwarteten Antworten ausgegeben werden.

> [!IMPORTANT]
> Um zu verstehen, wie und wann der Azure AD-Benutzerbereitstellungsdienst die unten beschriebenen Vorgänge ausgibt, lesen Sie [Vorgänge während der Bereitstellung: Startzyklus und Inkrementell](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) in [Funktionsweise der Bereitstellung](how-provisioning-works.md).

[Vorgänge für Benutzer](#user-operations)
  - [Benutzer erstellen](#create-user) ([Anforderung](#request) / [Antwort](#response))
  - [Benutzer abrufen](#get-user) ([Anforderung](#request-1) / [Antwort](#response-1))
  - [Benutzer nach Abfrage abrufen](#get-user-by-query) ([Anforderung](#request-2) / [Antwort](#response-2))
  - [Benutzer nach Abfrage abrufen – keine Ergebnisse](#get-user-by-query---zero-results) ([Anforderung](#request-3)
/ [Antwort](#response-3))
  - [Benutzer aktualisieren [mehrwertige Eigenschaften]](#update-user-multi-valued-properties) ([Anforderung](#request-4) /  [Antwort](#response-4))
  - [Benutzer aktualisieren [einwertige Eigenschaften]](#update-user-single-valued-properties) ([Anforderung](#request-5)
/ [Antwort](#response-5)) 
  - [Benutzer deaktivieren](#disable-user) ([Anforderung](#request-14) / 
[Antwort](#response-14))
  - [Benutzer löschen](#delete-user) ([Anforderung](#request-6) / 
[Antwort](#response-6))


[Vorgänge für Gruppen](#group-operations)
  - [Gruppe erstellen](#create-group) ([Anforderung](#request-7) / [Antwort](#response-7))
  - [Gruppe abrufen](#get-group) ([Anforderung](#request-8) / [Antwort](#response-8))
  - [Gruppe nach „displayName“ abrufen](#get-group-by-displayname) ([Anforderung](#request-9) / [Antwort](#response-9))
  - [Gruppe aktualisieren [Nichtmitglieder-Attribute]](#update-group-non-member-attributes) ([Anforderung](#request-10) /
 [Antwort](#response-10))
  - [Gruppe aktualisieren [Mitglieder hinzufügen]](#update-group-add-members) ([Anforderung](#request-11) /
[Antwort](#response-11))
  - [Gruppe aktualisieren [Mitglieder entfernen]](#update-group-remove-members) ([Anforderung](#request-12) /
[Antwort](#response-12))
  - [Gruppe löschen](#delete-group) ([Anforderung](#request-13) /
[Antwort](#response-13))

### <a name="user-operations"></a>Vorgänge für Benutzer

* Benutzer können anhand des Attributs `userName` oder `email[type eq "work"]` abgefragt werden.  

#### <a name="create-user"></a>Benutzer erstellen

###### <a name="request"></a>Anforderung

*POST /Users*
```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "active": true,
    "emails": [{
        "primary": true,
        "type": "work",
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com"
    }],
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName"
    },
    "roles": []
}
```

##### <a name="response"></a>Antwort

*HTTP/1.1 201 Created*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "48af03ac28ad4fb88478",
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="get-user"></a>Benutzer abrufen

###### <a name="request-1"></a>Anforderung
*GET /Users/5d48a0a8e9f04aa38008* 

###### <a name="response-1"></a>Antwort (Benutzer gefunden)
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5d48a0a8e9f04aa38008",
    "externalId": "58342554-38d6-4ec8-948c-50044d0a33fd",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_feed3ace-693c-4e5a-82e2-694be1b39934",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_22370c1a-9012-42b2-bf64-86099c2a1c22@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

###### <a name="request"></a>Anforderung
*GET /Users/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>Antwort (Benutzer nicht gefunden. Beachten Sie, dass das Detail nicht erforderlich ist, sondern nur den Status angibt.)

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>Benutzer nach Abfrage abrufen

##### <a name="request-2"></a>Anforderung

*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response-2"></a>Antwort

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
        "id": "2441309d85324e7793ae",
        "externalId": "7fce0092-d52e-4f76-b727-3955bd72c939",
        "meta": {
            "resourceType": "User",
            "created": "2018-03-27T19:59:26.000Z",
            "lastModified": "2018-03-27T19:59:26.000Z"
            
        },
        "userName": "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081",
        "name": {
            "familyName": "familyName",
            "givenName": "givenName"
        },
        "active": true,
        "emails": [{
            "value": "Test_User_91b67701-697b-46de-b864-bd0bbe4f99c1@testuser.com",
            "type": "work",
            "primary": true
        }]
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="get-user-by-query---zero-results"></a>Benutzer nach Abfrage abrufen – keine Ergebnisse

##### <a name="request-3"></a>Anforderung

*GET /Users?filter=userName eq "non-existent user"*

##### <a name="response-3"></a>Antwort

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>Benutzer aktualisieren [mehrwertige Eigenschaften]

##### <a name="request-4"></a>Anforderung

*PATCH /Users/6764549bef60420686bc HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [
            {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "updatedEmail@microsoft.com"
            },
            {
            "op": "Replace",
            "path": "name.familyName",
            "value": "updatedFamilyName"
            }
    ]
}
```

##### <a name="response-4"></a>Antwort

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "6764549bef60420686bc",
    "externalId": "6c75de36-30fa-4d2d-a196-6bdcdb6b6539",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_fbb9dda4-fcde-4f98-a68b-6c5599e17c27",
    "name": {
        "formatted": "givenName updatedFamilyName",
        "familyName": "updatedFamilyName",
        "givenName": "givenName"
    },
    "active": true,
    "emails": [{
        "value": "updatedEmail@microsoft.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="update-user-single-valued-properties"></a>Benutzer aktualisieren [einwertige Eigenschaften]

##### <a name="request-5"></a>Anforderung

*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "userName",
        "value": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com"
    }]
}
```

##### <a name="response-5"></a>Antwort

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5171a35d82074e068ce2",
    "externalId": "aa1eca08-7179-4eeb-a0be-a519f7e5cd1a",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "userName": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_49dc1090-aada-4657-8434-4995c25a00f7@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

### <a name="disable-user"></a>Benutzer deaktivieren

##### <a name="request-14"></a>Anforderung

*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```

##### <a name="response-14"></a>Antwort

```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User"
    ],
    "id": "CEC50F275D83C4530A495FCF@834d0e1e5d8235f90a495fda",
    "userName": "deanruiz@testuser.com",
    "name": {
        "familyName": "Harris",
        "givenName": "Larry"
    },
    "active": false,
    "emails": [
        {
            "value": "gloversuzanne@testuser.com",
            "type": "work",
            "primary": true
        }
    ],
    "addresses": [
        {
            "country": "ML",
            "type": "work",
            "primary": true
        }
    ],
    "meta": {
        "resourceType": "Users",
        "location": "/scim/5171a35d82074e068ce2/Users/CEC50F265D83B4530B495FCF@5171a35d82074e068ce2"
    }
}
```
#### <a name="delete-user"></a>Benutzer löschen

##### <a name="request-6"></a>Anforderung

*DELETE /Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response-6"></a>Antwort

*HTTP/1.1 204 No Content*

### <a name="group-operations"></a>Vorgänge für Gruppen

* Gruppen sollen immer mit einer leeren Mitgliederliste erstellt werden.
* Gruppen können anhand des Attributs `displayName` abgefragt werden.
* Bei einer Aktualisierung der Gruppe mit PATCH-Anforderung sollte in der Antwort *HTTP 204 No Content* angezeigt werden. Es ist nicht ratsam, einen Text mit einer Liste aller Mitglieder zurückzugeben.
* Die Rückgabe aller Mitglieder der Gruppe muss nicht unterstützt werden.

#### <a name="create-group"></a>Erstellen einer Gruppe

##### <a name="request-7"></a>Anforderung

*POST /Groups HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response-7"></a>Antwort

*HTTP/1.1 201 Created*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "927fa2c08dcb4a7fae9e",
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "displayName": "displayName",
    "members": []
}
```

#### <a name="get-group"></a>Gruppe abrufen

##### <a name="request-8"></a>Anforderung

*GET /Groups/40734ae655284ad3abcc?excludedAttributes=members HTTP/1.1*

##### <a name="response-8"></a>Antwort
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "40734ae655284ad3abcc",
    "externalId": "60f1bb27-2e1e-402d-bcc4-ec999564a194",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "displayName": "displayName",
}
```

#### <a name="get-group-by-displayname"></a>Gruppe nach „displayName“ abrufen

##### <a name="request-9"></a>Anforderung
*GET /Groups?excludedAttributes=members&filter=displayName eq "displayName" HTTP/1.1*

##### <a name="response-9"></a>Antwort

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
        "id": "8c601452cc934a9ebef9",
        "externalId": "0db508eb-91e2-46e4-809c-30dcbda0c685",
        "meta": {
            "resourceType": "Group",
            "created": "2018-03-27T22:02:32.000Z",
            "lastModified": "2018-03-27T22:02:32.000Z",
            
        },
        "displayName": "displayName",
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="update-group-non-member-attributes"></a>Gruppe aktualisieren [Nichtmitglieder-Attribute]

##### <a name="request-10"></a>Anforderung

*PATCH /Groups/fa2ce26709934589afc5 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "displayName",
        "value": "1879db59-3bdf-4490-ad68-ab880a269474updatedDisplayName"
    }]
}
```

##### <a name="response-10"></a>Antwort

*HTTP/1.1 204 No Content*

### <a name="update-group-add-members"></a>Gruppe aktualisieren [Mitglieder hinzufügen]

##### <a name="request-11"></a>Anforderung

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Add",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response-11"></a>Antwort

*HTTP/1.1 204 No Content*

#### <a name="update-group-remove-members"></a>Gruppe aktualisieren [Mitglieder entfernen]

##### <a name="request-12"></a>Anforderung

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Remove",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response-12"></a>Antwort

*HTTP/1.1 204 No Content*

#### <a name="delete-group"></a>Gruppe löschen

##### <a name="request-13"></a>Anforderung

*DELETE /Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response-13"></a>Antwort

*HTTP/1.1 204 No Content*

## <a name="step-3-build-a-scim-endpoint"></a>Schritt 3: Erstellen eines SCIM-Endpunkts

Wenn Sie einen SCIM-Webdienst mit Schnittstelle zu Azure Active Directory erstellen, können Sie die automatische Benutzerbereitstellung für nahezu alle Anwendungen oder Identitätsspeicher aktivieren.

Funktionsweise:

1. Azure AD stellt eine Common Language Infrastructure-Bibliothek (CLI-Bibliothek) namens „Microsoft.SystemForCrossDomainIdentityManagement“ mit den unten beschriebenen Codebeispielen bereit. Systemintegratoren und Entwickler können diese Bibliothek verwenden, um einen SCIM-basierten Webdienstendpunkt zu erstellen und bereitzustellen, über den Azure AD mit dem Identitätsspeicher einer beliebigen Anwendung verbunden werden kann.
2. Zuordnungen werden in den Webdienst implementiert, um das standardisierte Benutzerschema dem erforderlichen Benutzerschema und Protokoll für die Anwendung zuzuordnen. 
3. Die Endpunkt-URL wird in Azure AD als Teil einer benutzerdefinierten Anwendung im Anwendungskatalog registriert.
4. Benutzer und Gruppen werden dieser Anwendung in Azure AD zugewiesen. Bei der Zuweisung werden sie in eine Warteschlange eingereiht, um mit der Zielanwendung synchronisiert zu werden. Der Synchronisierungsvorgang, der die Warteschlange verarbeitet, wird alle 40 Minuten ausgeführt.

### <a name="code-samples"></a>Codebeispiele

Um diesen Prozess zu vereinfachen, werden [Codebeispiele](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) bereitgestellt, in denen ein SCIM-Webdienstendpunkt erstellt und die automatische Bereitstellung demonstriert wird. In dem Beispiel wird von einem Anbieter eine Datei mit Zeilen kommagetrennter Werte verwendet, die für Benutzer und Gruppen stehen.

**Voraussetzungen**

* Visual Studio 2013 oder höher
* [Azure SDK für .NET](https://azure.microsoft.com/downloads/)
* Windows-Computer, der die Verwendung von ASP.NET Framework 4.5 als SCIM-Endpunkt unterstützt. Auf diesen Computer muss aus der Cloud zugegriffen werden können.
* [Azure-Abonnement mit Testversion oder lizenzierter Version von Azure AD Premium](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>Erste Schritte

Die einfachste Möglichkeit zum Implementieren eines SCIM-Endpunkts, der Bereitstellungsanforderungen von Azure AD akzeptiert, ist das Erstellen und Bereitstellen des Codebeispiels, bei dem die bereitgestellten Benutzer in eine Datei mit kommagetrennten Werten (CSV) ausgegeben werden.

#### <a name="to-create-a-sample-scim-endpoint"></a>So erstellen Sie einen SCIM-Beispielendpunkt

1. Laden Sie das Beispielpaket aus [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) herunter.
1. Entzippen Sie das Paket, und speichern Sie die Daten auf Ihrem Windows-Computer, z.B. unter „C:\AzureAD-BYOA-Provisioning-Samples“.
1. Starten Sie in diesem Ordner das Projekt „FileProvisioning\Host\FileProvisioningService.csproj“ in Visual Studio.
1. Klicken Sie auf **Extras** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole**, und führen Sie die folgenden Befehle für das Projekt „FileProvisioningAgent“ aus, um die Projektmappenverweise aufzulösen:

   ```powershell
    Update-Package -Reinstall
   ```

1. Erstellen Sie das Projekt „FileProvisioningAgent“.
1. Starten Sie die Anwendung „Eingabeaufforderung“ in Windows als Administrator, und verwenden Sie den Befehl **cd**, um zum Ordner **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug** zu wechseln.
1. Führen Sie den folgenden Befehl aus, und ersetzen Sie dabei `<ip-address>` durch die IP-Adresse oder den Domänennamen des Windows-Computers:

   ```
    FileSvc.exe http://<ip-address>:9000 TargetFile.csv
   ```

1. Wählen Sie in Windows unter **Windows-Einstellungen** > **Netzwerk- und Interneteinstellungen** die Option **Windows-Firewall** > **Erweiterte Einstellungen** aus, und erstellen Sie eine **Eingangsregel**, mit der der eingehende Zugriff auf Port 9000 zugelassen wird.
1. Falls sich der Windows-Computer hinter einem Router befindet, muss der Router konfiguriert werden, um die Netzwerkadressübersetzung zwischen seinem Port 9000, der gegenüber dem Internet offen ist, und Port 9000 auf dem Windows-Computer durchzuführen. Diese Konfiguration ist erforderlich, damit Azure AD auf diesen Endpunkt in der Cloud zugreifen kann.

#### <a name="to-register-the-sample-scim-endpoint-in-azure-ad"></a>So registrieren Sie den SCIM-Beispielendpunkt in Azure AD

1. Melden Sie sich beim [Azure Active Directory-Portal](https://aad.portal.azure.com) an. 
1. Wählen Sie im linken Bereich die Option **Unternehmensanwendungen** aus. Eine Liste mit allen konfigurierten Apps wird angezeigt, einschließlich Apps, die aus dem Katalog hinzugefügt wurden.
1. Wählen Sie **+ Neue Anwendung** > **Alle** > **Nicht-Kataloganwendung**.
1. Geben Sie einen Namen für Ihre Anwendung ein, und wählen Sie **Hinzufügen**, um ein App-Objekt zu erstellen. Mit dem erstellten Anwendungsobjekt soll die Ziel-App dargestellt werden, für die Sie einmaliges Anmelden bereitstellen und implementieren möchten, und nicht nur der SCIM-Endpunkt.
1. Wählen Sie auf dem App-Verwaltungsbildschirm im linken Bereich die Option **Bereitstellung**.
1. Wählen Sie im Menü **Bereitstellungsmodus** die Option **Automatisch** aus.    
1. Geben Sie im Feld **Mandanten-URL** die URL des SCIM-Endpunkts der Anwendung ein. Beispiel: https://api.contoso.com/scim/

1. Wenn der SCIM-Endpunkt ein OAuth-Bearertoken benötigt, das von einem anderen Aussteller als Azure AD stammt, kopieren Sie das erforderliche OAuth-Bearertoken in das optionale Feld **Geheimes Token**. Wird dieses Feld leer gelassen, fügt Azure AD in jede Anforderung ein von Azure AD ausgestelltes OAuth-Bearertoken ein. Apps, die Azure AD als Identitätsanbieter verwenden, können dieses von Azure AD ausgestellte Token überprüfen.
1. Wählen Sie die Option **Verbindung testen**, damit Azure Active Directory versucht, eine Verbindung mit dem SCIM-Endpunkt herzustellen. Wenn der Versuch nicht erfolgreich ist, werden Fehlerinformationen angezeigt.  

    > [!NOTE]
    > Die Option **Verbindung testen** fragt den SCIM-Endpunkt nach einem Benutzer ab, der nicht vorhanden ist, und verwendet dabei einen zufälligen global eindeutigen Bezeichner (Globally Unique Identifier, GUID) als entsprechende Eigenschaft, die in der Azure AD-Konfiguration ausgewählt wurde. Die erwartete richtige Antwort ist „HTTP 200 OK“ mit einer leeren SCIM ListResponse-Meldung.

1. Wählen Sie bei einer erfolgreichen Verbindungsherstellung mit der Anwendung die Option **Speichern**, um die Administratoranmeldeinformationen zu speichern.
1. Im Abschnitt **Zuordnungen** stehen zwei Sätze von Attributzuordnungen zur Verfügung: eine für Benutzerobjekte und eine für Gruppenobjekte. Wählen Sie beide nacheinander aus, um die Attribute zu überprüfen, die von Azure Active Directory mit Ihrer App synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzer und Gruppen in Ihrer App für Updatevorgänge verwendet werden. Wählen Sie **Speichern** aus, um Ihre Änderungen zu committen.
1. Im Feld **Bereich** unter **Einstellungen** wird festgelegt, welche Benutzer und Gruppen synchronisiert werden. Wählen Sie **Nur zugewiesene Benutzer und Gruppen synchronisieren** (empfohlen) aus, damit nur Benutzer und Gruppen synchronisiert werden, die auf der Registerkarte **Benutzer und Gruppen** zugewiesen sind.
1. Legen Sie den **Bereitstellungsstatus** nach Abschluss der Konfiguration auf **Ein** fest.
1. Wählen Sie **Speichern**, um den Azure AD-Bereitstellungsdienst zu starten.
1. Wenn nur zugewiesene Benutzer und Gruppen synchronisiert werden (empfohlen), sollten Sie darauf achten, dass die Registerkarte **Benutzer und Gruppen** ausgewählt ist und die Benutzer bzw. Gruppen zugewiesen sind, die synchronisiert werden sollen.

Nachdem der erste Zyklus gestartet wurde, können Sie im linken Bereich die Option **Überwachungsprotokolle** wählen, um den Fortschritt zu überwachen. Hier werden alle Aktionen angezeigt, die vom Bereitstellungsdienst für Ihre App durchgeführt werden. Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](check-status-user-account-provisioning.md).

Der letzte Schritt bei der Überprüfung des Beispiels besteht darin, die Datei „TargetFile.csv“ im Ordner „\AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug“ auf Ihrem Windows-Computer zu öffnen. Wenn der Bereitstellungsprozess ausgeführt wird, werden in dieser Datei die Details aller zugewiesenen und bereitgestellten Benutzer und Gruppen angezeigt.

### <a name="development-libraries"></a>Entwicklungsbibliotheken

Um Ihren eigenen Webdienst zu entwickeln, der mit der SCIM-Spezifikation übereinstimmt, sollten Sie sich zuerst mit den folgenden Bibliotheken von Microsoft vertraut machen, mit denen der Entwicklungsprozess beschleunigt wird:

* Common Language Infrastructure-Bibliotheken (CLI) werden zur Verwendung mit Sprachen angeboten, die auf der betreffenden Infrastruktur basieren, z.B. C#. Eine dieser Bibliotheken („Microsoft.SystemForCrossDomainIdentityManagement.Service“) deklariert eine Schnittstelle („Microsoft.SystemForCrossDomainIdentityManagement.IProvider“), die in der folgenden Abbildung dargestellt ist. Ein Entwickler, der die Bibliotheken verwendet, würde diese Schnittstelle mit einer Klasse implementieren, die generisch als „Anbieter“ bezeichnet werden kann. Die Bibliotheken ermöglichen dem Entwickler die Bereitstellung eines Webdiensts, der die Vorgaben der SCIM-Spezifikation erfüllt. Der Webdienst kann entweder in Internetinformationsdienste oder einer beliebigen ausführbaren CLI-Assembly gehostet werden. Anforderungen werden in Aufrufe der Anbietermethoden übersetzt, die vom Entwickler für den Betrieb über einen Identitätsspeicher programmiert werden.
  
   ![Aufschlüsselung: Eine Anforderung, die in Aufrufe der Methoden des Anbieters übersetzt wurde](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)
  
* [ExpressRouten-Handler](https://expressjs.com/guide/routing.html) sind zum Analysieren von Node.js-Anforderungsobjekten verfügbar, die für Aufrufe an einen Node.js-Webdienst stehen (gemäß SCIM-Spezifikation).

### <a name="building-a-custom-scim-endpoint"></a>Erstellen eines benutzerdefinierten SCIM-Endpunkts

Entwickler, die die CLI-Bibliotheken nutzen, können ihre Dienste in einer ausführbaren CLI-Assembly oder in Internetinformationsdienste hosten. Dies ist ein Beispielcode zum Hosten eines Diensts in einer ausführbaren Assembly unter der Adresse http://localhost:9000: 

```csharp
 private static void Main(string[] arguments)
 {
 // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
 // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
 // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
 // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

 Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
   new DevelopersMonitor();
 Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
   new DevelopersProvider(arguments[1]);
 Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
 try
 {
     webService = new WebService(monitor, provider);
     webService.Start("http://localhost:9000");

     Console.ReadKey(true);
 }
 finally
 {
     if (webService != null)
     {
         webService.Dispose();
         webService = null;
     }
 }
 }

 public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
 {
 private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
 private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

 public WebService(
   Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
   Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
 {
     this.monitor = monitoringBehavior;
     this.provider = providerBehavior;
 }

 public override IMonitor MonitoringBehavior
 {
     get
     {
         return this.monitor;
     }

     set
     {
         this.monitor = value;
     }
 }

 public override IProvider ProviderBehavior
 {
     get
     {
         return this.provider;
     }

     set
     {
         this.provider = value;
     }
 }
 }
```

Dieser Dienst muss über eine HTTP-Adresse und über ein Serverauthentifizierungszertifikat mit einer der folgenden Stammzertifizierungsstellen verfügen: 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

Ein Serverauthentifizierungszertifikat kann an einen Port auf einem Windows-Host gebunden werden, indem das Netzwerk-Shell-Hilfsprogramm verwendet wird:

```
netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}
```

Hier ist der für das certhash-Argument angegebene Wert der Fingerabdruck des Zertifikats, und der für das appid-Argument bereitgestellte Wert ist ein beliebiger eindeutiger Bezeichner (Globally Unique Identifier, GUID).  

Um den Dienst in Internetinformationsdienste zu hosten, würde ein Entwickler eine CLI-Codebibliothekassembly mit einer Klasse mit dem Namen „Startup“ im Standardnamespace der Assembly erstellen.  Dies ist ein Beispiel für eine Klasse dieser Art: 

```csharp
 public class Startup
 {
 // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
 // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
 // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
 // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

 Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

 public Startup()
 {
     Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
       new DevelopersMonitor();
     Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
       new DevelopersProvider();
     this.starter = 
       new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
         provider, 
         monitor);
 }

 public void Configuration(
   Owin.IAppBuilder builder) // Defined in Owin.dll.  
 {
     this.starter.ConfigureApplication(builder);
 }
 }
```

### <a name="handling-endpoint-authentication"></a>Behandeln der Endpunktauthentifizierung

Anforderungen aus Azure Active Directory enthalten ein OAuth 2.0-Bearertoken.   Alle Dienste, die die Anforderung empfangen, sollten den Aussteller als Azure Active Directory-Instanz für den erwarteten Azure Active Directory-Mandanten authentifizieren, was den Zugriff auf den Graph-Webdienst von Azure Active Directory betrifft.  Im Token wird der Aussteller durch einen iss-Anspruch, z. B. „iss“, identifiziert: „https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/ “.  In diesem Beispiel wird die Basisadresse des Anspruchswerts (https://sts.windows.net ) zum Identifizieren von Azure Active Directory als Aussteller verwendet, und das Segment mit der relativen Adresse (cbb1a5ac-f33b-45fa-9bf5-f37db0fed422) ist ein eindeutiger Bezeichner des Azure Active Directory-Mandanten, für den das Token ausgestellt wurde. Die Zielgruppe für das Token ist die Anwendungsvorlagen-ID für die App im Katalog. Die Anwendungsvorlagen-ID für alle benutzerdefinierten Apps lautet „8adf8e6e-67b2-4cf2-a259-e3dc5476c621“. Für Apps im Katalog variiert die Anwendungsvorlagen-ID jeweils. Sollten Sie Fragen zur Anwendungsvorlagen-ID für eine Kataloganwendung haben, wenden Sie sich an ProvisioningFeedback@microsoft.com. Jede der in einem einzelnen Mandanten registrierten Anwendungen kann den gleichen `iss`-Anspruch mit SCIM-Anforderungen empfangen.

Entwickler, die die von Microsoft bereitgestellten CLI-Bibliotheken zum Erstellen eines SCIM-Diensts verwenden, können Anforderungen von Azure Active Directory authentifizieren, indem sie das Microsoft.Owin.Security.ActiveDirectory-Paket verwenden. Hierzu müssen die folgenden Schritte ausgeführt werden: 

Implementieren Sie zunächst in einem Anbieter die Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior-Eigenschaft so, dass eine Methode zurückgegeben wird, die bei jedem Start des Diensts aufgerufen werden soll: 

```csharp
  public override Action<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration> StartupBehavior
  {
    get
    {
      return this.OnServiceStartup;
    }
  }

  private void OnServiceStartup(
    Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
    System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
  {
  }
```

Fügen Sie dann dieser Methode den folgenden Code hinzu, damit alle Anforderungen an die Endpunkte des Diensts so authentifiziert werden, dass sie über ein Token mit folgenden Eigenschaften verfügen: Es wird von Azure Active Directory für einen angegebenen Mandanten ausgestellt und dient dem Zugriff auf den Graph-Webdienst von Azure AD: 

```csharp
  private void OnServiceStartup(
    Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
    System.Web.Http.HttpConfiguration HttpConfiguration configuration)
  {
    // IFilter is defined in System.Web.Http.dll.  
    System.Web.Http.Filters.IFilter authorizationFilter = 
      new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

    // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
    // System.IdentityModel.Token.Jwt.dll.
    SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
      new TokenValidationParameters()
      {
        ValidAudience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621"
      };

    // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
    // Microsoft.Owin.Security.ActiveDirectory.dll
    Microsoft.Owin.Security.ActiveDirectory.
    WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
      new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
      TokenValidationParameters = tokenValidationParameters,
      Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                    // identifier for this one.  
    };

    applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
  }
```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Vorgehensweise beim Bereitstellen und beim Aufheben der Bereitstellung von Benutzern

***Beispiel 1: Abfragen des Diensts nach einem passenden Benutzer***

Azure Active Directory fragt den Dienst nach einem Benutzer mit einem externalId-Attributwert ab, der mit dem mailNickname-Attributwert eines Benutzers in Azure AD übereinstimmt. Die Abfrage wird als Hypertext Transfer-Protokoll-Anforderung (HTTP-Anforderung) wie in diesem Beispiel ausgedrückt, wobei „jyoung“ ein Beispiel für ein mailNickname-Attribut eines Benutzers in Azure Active Directory ist.

>[!NOTE]
> Dies ist nur ein Beispiel. Nicht alle Benutzer verfügen über ein mailNickname-Attribut, und der Wert eines Benutzers ist möglicherweise im Verzeichnis nicht eindeutig. Das für den Abgleich verwendete Attribut (in diesem Fall „externalId“) kann auch in den [Azure AD-Attributzuordnungen](customize-application-attributes.md) konfiguriert werden.

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

Wenn der Dienst mit den von Microsoft bereitgestellten CLI-Bibliotheken zum Implementieren von SCIM-Diensten erstellt wurde, wird die Anforderung in einen Aufruf der Query-Methode des Dienstanbieters übersetzt.  Dies ist die Signatur dieser Methode: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
 // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
 // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
 // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

 System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
   Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
   string correlationIdentifier);
```

Dies ist die Definition der Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters-Schnittstelle: 

```csharp
 public interface IQueryParameters: 
   Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
 {
     System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
     { get; }
 }

 public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
 {
   system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
   { get; }
   System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
   { get; }
   string SchemaIdentifier 
   { get; }
 }
```

```
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
```

Wenn der Dienst mit den Common Language Infrastructure-Bibliotheken von Microsoft zum Implementieren von SCIM-Diensten erstellt wurde, wird die Anforderung in einen Aufruf der Query-Methode des Dienstanbieters übersetzt.  Dies ist die Signatur dieser Methode: 

```csharp
  // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
  // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
  // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
  // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
  // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

  System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]>  Query(
    Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
    string correlationIdentifier);
```

Dies ist die Definition der Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters-Schnittstelle: 

```csharp
  public interface IQueryParameters: 
    Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
  {
      System.Collections.Generic.IReadOnlyCollection  <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
      { get; }
  }

  public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
  {
    system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
    { get; }
    System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
    { get; }
    string SchemaIdentifier 
    { get; }
  }

  public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
  {
      Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
        { get; set; }
      string AttributePath 
        { get; } 
      Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
        { get; }
      string ComparisonValue 
        { get; }
  }

  public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
  {
      Equals
  }
```

Im folgenden Beispiel für eine Abfrage für einen Benutzer mit einem bestimmten Wert für das externalId-Attribut lauten die Werte der Argumente, die an die Query-Methode übergeben werden, wie folgt: 
* parameters.AlternateFilters.Count: 1
* parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
* parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"
* correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

***Beispiel 2: Bereitstellen eines Benutzers***

Wenn in der Antwort auf eine Abfrage an den Webdienst für einen Benutzer mit einem externalId-Attributwert, der mit dem mailNickname-Attributwert eines Benutzers übereinstimmt, keine Benutzer zurückgegeben werden, stellt Azure Active Directory die folgende Anforderung: Der Dienst muss einen Benutzer bereitstellen, der dem Benutzer in Azure Active Directory entspricht.  Dies ist ein Beispiel für eine Anforderung dieser Art: 

```
 POST https://.../scim/Users HTTP/1.1
 Authorization: Bearer ...
 Content-type: application/scim+json
 {
   "schemas":
   [
     "urn:ietf:params:scim:schemas:core:2.0:User",
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
   "externalId":"jyoung",
   "userName":"jyoung",
   "active":true,
   "addresses":null,
   "displayName":"Joy Young",
   "emails": [
     {
       "type":"work",
       "value":"jyoung@Contoso.com",
       "primary":true}],
   "meta": {
     "resourceType":"User"},
    "name":{
     "familyName":"Young",
     "givenName":"Joy"},
   "phoneNumbers":null,
   "preferredLanguage":null,
   "title":null,
   "department":null,
   "manager":null}
```

Mit den von Microsoft bereitgestellten CLI-Bibliotheken zum Implementieren von SCIM-Diensten wird diese Anforderung in einen Aufruf der Create-Methode des Dienstanbieters übersetzt.  Die Create-Methode hat die folgende Signatur:

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
 // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

 System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
   Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
   string correlationIdentifier);
```

In einer Anforderung zum Bereitstellen eines Benutzers ist der Wert des Ressourcenarguments eine Instanz von „Microsoft.SystemForCrossDomainIdentityManagement“. Die Core2EnterpriseUser-Klasse, die in der Microsoft.SystemForCrossDomainIdentityManagement.Schemas-Bibliothek definiert ist.  Wenn die Anforderung zum Bereitstellen des Benutzers erfolgreich ist, wird von der Implementierung der Methode erwartet, dass eine Instanz der Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser-Klasse zurückgegeben wird, für die der Wert der Identifier-Eigenschaft auf den eindeutigen Bezeichner des neu bereitgestellten Benutzers festgelegt ist.  

***Beispiel 3: Abfragen des aktuellen Status eines Benutzers*** 

Zum Aktualisieren eines Benutzers, der in einem Identitätsspeicher mit vorgelagertem SCIM vorhanden ist, geht Azure Active Directory so vor, dass der aktuelle Status dieses Benutzers vom Dienst per Anforderung abgefragt wird. Die Anforderung hierzu sieht wie folgt aus: 

```
 GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
 Authorization: Bearer ...
```

Bei einem mit den von Microsoft bereitgestellten CLI-Bibliotheken zum Implementieren von SCIM-Diensten erstellten Dienst wird die Anforderung in einen Aufruf der Retrieve-Methode des Dienstanbieters übersetzt.  Dies ist die Signatur der Retrieve-Methode:

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
 // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
 // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
 System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
    Retrieve(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
        parameters, 
        string correlationIdentifier);

 public interface 
   Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
     IRetrievalParameters
     {
       Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
         ResourceIdentifier 
           { get; }
 }
 public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
 {
     string Identifier 
       { get; set; }
     string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
       { get; set; }
 }
```

Im Beispiels für eine Anforderung zum Abrufen des aktuellen Status eines Benutzers lauten die Werte der Eigenschaften des Objekts, das als Wert des parameters-Arguments angegeben wird, wie folgt: 
  
* Bezeichner: „54D382A4-2050-4C03-94D1-E769F1D15682“
* SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

***Beispiel 4: Abfragen des Werts eines zu aktualisierenden Referenzattributs*** 

Wenn ein Verweisattribut aktualisiert werden soll, fragt Azure Active Directory den Dienst ab, um zu ermitteln, ob der aktuelle Wert des Verweisattributs im Identitätsspeicher mit vorgelagertem Dienst bereits mit dem Wert dieses Attributs in Azure Active Directory übereinstimmt. Bei Benutzern ist das einzige Attribut, für das der aktuelle Wert auf diese Weise abgefragt wird, das manager-Attribut. Dies ist ein Beispiel für eine Anforderung, mit der ermittelt wird, ob das manager-Attribut eines bestimmten Benutzerobjekts derzeit über einen bestimmten Wert verfügt: 

Wenn der Dienst mit den von Microsoft bereitgestellten CLI-Bibliotheken zum Implementieren von SCIM-Diensten erstellt wurde, wird die Anforderung in einen Aufruf der Query-Methode des Dienstanbieters übersetzt. Der Wert der Eigenschaften des Objekts, das als Wert des parameters-Arguments angegeben wird, lautet wie folgt: 
  
* parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: „ID“
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue: „54D382A4-2050-4C03-94D1-E769F1D15682“
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue: „2819c223-7f76-453a-919d-413861904646“
* parameters.RequestedAttributePaths.ElementAt(0): „ID“
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

Hier kann der Wert von Index x „0“ und der Wert von Index y „1“ lauten, oder der Wert von x kann „1“ und der Wert von y „0“ lauten. Dies hängt von der Reihenfolge bei den Ausdrücken des Filterabfrageparameters ab.   

***Beispiel 5: Anforderung von Azure AD an einen SCIM-Dienst zur Aktualisierung eines Benutzers*** 

Dies ist ein Beispiel für eine Anforderung von Azure Active Directory an einen SCIM-Dienst zum Aktualisieren eines Benutzers: 

```
  PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
  Content-type: application/scim+json
  {
    "schemas": 
    [
      "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations":
    [
      {
        "op":"Add",
        "path":"manager",
        "value":
          [
            {
              "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
              "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
```

Mit den Common Language Infrastructure-Bibliotheken von Microsoft zum Implementieren von SCIM-Diensten wird die Anforderung in einen Aufruf der Update-Methode des Dienstanbieters übersetzt. Dies ist die Signatur der Update-Methode: 

```csharp
  // System.Threading.Tasks.Tasks and 
  // System.Collections.Generic.IReadOnlyCollection<T>
  // are defined in mscorlib.dll.  
  // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
  // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
  // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
  // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
  // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
  // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
  // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
  // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

  System.Threading.Tasks.Task Update(
    Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
    string correlationIdentifier);

  public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
  {
  Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
    PatchRequest 
      { get; set; }
  Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
    ResourceIdentifier 
      { get; set; }        
  }

  public class PatchRequest2: 
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
  {
  public System.Collections.Generic.IReadOnlyCollection
    <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
      Operations
      { get;}

  public void AddOperation(
    Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
  }

  public class PatchOperation
  {
  public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
    Name
    { get; set; }

  public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
    Path
    { get; set; }

  public System.Collections.Generic.IReadOnlyCollection
    <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
    { get; }

  public void AddValue(
    Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
  }

  public enum OperationName
  {
    Add,
    Remove,
    Replace
  }

  public interface IPath
  {
    string AttributePath { get; }
    System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
    Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
  }

  public class OperationValue
  {
    public string Reference
    { get; set; }

    public string Value
    { get; set; }
  }
```

Im Beispiel für eine Anforderung zum Aktualisieren eines Benutzers verfügt das Objekt, das als Wert des patch-Arguments angegeben wird, über diese Eigenschaftswerte: 
  
* ResourceIdentifier.Identifier: „54D382A4-2050-4C03-94D1-E769F1D15682“
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
* (PatchRequest as PatchRequest2).Operations.Count: 1
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName: OperationName.Add
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Path.AttributePath: "manager"
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count: 1
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Reference: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value: 2819c223-7f76-453a-919d-413861904646

***Beispiel 6: Aufheben der Bereitstellung eines Benutzers***

Um die Bereitstellung für einen Benutzer aus einem Identitätsspeicher mit vorgelagertem SCIM-Dienst aufzuheben, sendet Azure AD eine Anforderung der folgenden Art:

```
  DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
```

Wenn der Dienst mit den Common Language Infrastructure-Bibliotheken von Microsoft zum Implementieren von SCIM-Diensten erstellt wurde, wird die Anforderung in einen Aufruf der Delete-Methode des Dienstanbieters übersetzt.   Diese Methode verfügt über folgende Signatur: 

```csharp
  // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
  // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
  // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
  System.Threading.Tasks.Task Delete(
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
      resourceIdentifier, 
    string correlationIdentifier);
```

Im Beispiel für eine Anforderung zum Aufheben der Bereitstellung eines Benutzers verfügt das Objekt, das als Wert des resourceIdentifier-Arguments angegeben wird, über diese Eigenschaftswerte: 

* ResourceIdentifier.Identifier: „54D382A4-2050-4C03-94D1-E769F1D15682“
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>Schritt 4: Integrieren Ihres SCIM-Endpunkts mit dem Azure AD SCIM-Client

Azure AD kann für das automatische Bereitstellen von zugewiesenen Benutzern und Gruppen für Anwendungen konfiguriert werden, die ein bestimmtes Profil des [SCIM 2.0-Protokolls](https://tools.ietf.org/html/rfc7644) implementieren. Die Details des Profils sind in [Schritt 2: Verstehen der Azure AD SCIM-Implementierung](#step-2-understand-the-azure-ad-scim-implementation) dokumentiert.

Überprüfen Sie beim Anbieter Ihrer Anwendung oder in der Dokumentation zu Ihrer Anwendung, ob diese Anforderungen voll erfüllt werden.

> [!IMPORTANT]
> Die Azure AD-SCIM-Implementierung basiert auf dem Azure AD-Benutzerbereitstellungsdienst, der auf die ständige Synchronisierung der Benutzer zwischen Azure AD und der Zielanwendung ausgelegt ist, und implementiert eine ganz bestimmte Reihe von Standardvorgängen. Es ist wichtig, diese Verhaltensweisen zu verstehen, um das Verhalten des Azure AD-SCIM-Clients nachvollziehen zu können. Weitere Informationen finden Sie in [Vorgänge während der Bereitstellung: Startzyklus und Inkrementell](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) in [Funktionsweise der Bereitstellung](how-provisioning-works.md).

### <a name="getting-started"></a>Erste Schritte

Anwendungen, die das SCIM-Profil wie in diesem Artikel beschrieben erfüllen, können über das Feature „Nicht-Kataloganwendung“ im Azure AD-Anwendungskatalog mit Azure Active Directory verbunden werden. Nachdem die Verbindung hergestellt wurde, führt Azure AD alle 40 Minuten einen Synchronisierungsprozess aus, bei dem der SCIM-Endpunkt der Anwendung auf zugewiesene Benutzer und Gruppen abgefragt wird. Entsprechend den Details der Zuweisung werden Benutzer und Gruppen dann erstellt oder geändert.

**So verbinden Sie eine Anwendung, die SCIM unterstützt:**

1. Melden Sie sich beim [Azure Active Directory-Portal](https://aad.portal.azure.com) an. Beachten Sie, dass Sie auf eine kostenlose Testversion für Azure Active Directory mit P2-Lizenzen zugreifen können, indem Sie sich für das [Entwicklerprogramm](https://developer.microsoft.com/office/dev-program) registrieren.
2. Wählen Sie im linken Bereich die Option **Unternehmensanwendungen** aus. Eine Liste mit allen konfigurierten Apps wird angezeigt, einschließlich Apps, die aus dem Katalog hinzugefügt wurden.
3. Wählen Sie **+ Neue Anwendung** > **Alle** > **Nicht-Kataloganwendung**.
4. Geben Sie einen Namen für Ihre Anwendung ein, und wählen Sie **Hinzufügen**, um ein App-Objekt zu erstellen. Die neue App wird der Liste mit den Unternehmensanwendungen hinzugefügt und mit dem App-Verwaltungsbildschirm geöffnet.

   ![Screenshot des Azure AD-Anwendungskatalogs](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   *Azure AD-Anwendungskatalog*

5. Wählen Sie auf dem App-Verwaltungsbildschirm im linken Bereich die Option **Bereitstellung**.
6. Wählen Sie im Menü **Bereitstellungsmodus** die Option **Automatisch** aus.

   ![Beispiel: Bereitstellungsseite einer App im Azure-Portal](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Konfigurieren der Bereitstellung im Azure-Portal*

7. Geben Sie im Feld **Mandanten-URL** die URL des SCIM-Endpunkts der Anwendung ein. Beispiel: https://api.contoso.com/scim/
8. Wenn der SCIM-Endpunkt ein OAuth-Bearertoken benötigt, das von einem anderen Aussteller als Azure AD stammt, kopieren Sie das erforderliche OAuth-Bearertoken in das optionale Feld **Geheimes Token**. Wird dieses Feld leer gelassen, fügt Azure AD in jede Anforderung ein von Azure AD ausgestelltes OAuth-Bearertoken ein. Apps, die Azure AD als Identitätsanbieter verwenden, können dieses von Azure AD ausgestellte Token überprüfen. 
   > [!NOTE]
   > Es wird ***nicht*** empfohlen, dieses Feld leer zu lassen und sich auf ein von Azure AD generiertes Token zu verlassen. Diese Option steht in erster Linie zu Testzwecken zur Verfügung.
9. Wählen Sie die Option **Verbindung testen**, damit Azure Active Directory versucht, eine Verbindung mit dem SCIM-Endpunkt herzustellen. Wenn der Versuch nicht erfolgreich ist, werden Fehlerinformationen angezeigt.  

    > [!NOTE]
    > Die Option **Verbindung testen** fragt den SCIM-Endpunkt nach einem Benutzer ab, der nicht vorhanden ist, und verwendet dabei einen zufälligen global eindeutigen Bezeichner (Globally Unique Identifier, GUID) als entsprechende Eigenschaft, die in der Azure AD-Konfiguration ausgewählt wurde. Die erwartete richtige Antwort ist „HTTP 200 OK“ mit einer leeren SCIM ListResponse-Meldung.

10. Wählen Sie bei einer erfolgreichen Verbindungsherstellung mit der Anwendung die Option **Speichern**, um die Administratoranmeldeinformationen zu speichern.
11. Im Abschnitt **Zuordnungen** stehen zwei Sätze von [Attributzuordnungen](customize-application-attributes.md) zur Verfügung: eine für Benutzerobjekte und eine für Gruppenobjekte. Wählen Sie beide nacheinander aus, um die Attribute zu überprüfen, die von Azure Active Directory mit Ihrer App synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzer und Gruppen in Ihrer App für Updatevorgänge verwendet werden. Wählen Sie **Speichern** aus, um Ihre Änderungen zu committen.

    > [!NOTE]
    > Sie können die Synchronisierung von Gruppenobjekten optional deaktivieren. Deaktivieren Sie dazu die Zuordnung „Gruppen“.

12. Im Feld **Bereich** unter **Einstellungen** wird festgelegt, welche Benutzer und Gruppen synchronisiert werden. Wählen Sie **Nur zugewiesene Benutzer und Gruppen synchronisieren** (empfohlen) aus, damit nur Benutzer und Gruppen synchronisiert werden, die auf der Registerkarte **Benutzer und Gruppen** zugewiesen sind.
13. Legen Sie den **Bereitstellungsstatus** nach Abschluss der Konfiguration auf **Ein** fest.
14. Wählen Sie **Speichern**, um den Azure AD-Bereitstellungsdienst zu starten.
15. Wenn nur zugewiesene Benutzer und Gruppen synchronisiert werden (empfohlen), sollten Sie darauf achten, dass die Registerkarte **Benutzer und Gruppen** ausgewählt ist und die Benutzer bzw. Gruppen zugewiesen sind, die synchronisiert werden sollen.

Nachdem der erste Zyklus gestartet wurde, können Sie im linken Bereich die Option **Bereitstellungsprotokolle** auswählen, um den Fortschritt zu überwachen. Hier werden alle Aktionen angezeigt, die vom Bereitstellungsdienst für Ihre App durchgeführt werden. Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](check-status-user-account-provisioning.md).

> [!NOTE]
> Der erste Zyklus dauert länger als spätere Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Dienst ausgeführt wird.

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>Schritt 5: Veröffentlichen Ihrer Anwendung im Azure AD-Anwendungskatalog

Wenn Sie eine Anwendung erstellen, die von mehreren Mandanten verwendet wird, können Sie sie im Azure AD-Anwendungskatalog zur Verfügung stellen. Dies erleichtert Organisationen das Auffinden der Anwendung und das Konfigurieren der Bereitstellung. Das Veröffentlichen Ihrer App im Azure AD-Katalog und das Verfügbarmachen der Bereitstellung für andere ist einfach. Die entsprechenden Schritte sind [hier](../develop/howto-app-gallery-listing.md) angegeben. Microsoft wird mit Ihnen zusammenarbeiten, um Ihre Anwendung in unseren Katalog zu integrieren, Ihren Endpunkt zu testen und die [Dokumentation](../saas-apps/tutorial-list.md) zum Onboarding für Kunden freizugeben. 


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>Autorisierung für Bereitstellungsconnectors im Anwendungskatalog
Die SCIM-Spezifikation definiert kein SCIM-spezifisches Schema für die Authentifizierung und Autorisierung. Sie stützt sich auf die Verwendung bestehender Branchenstandards. Der Azure AD-Bereitstellungsclient unterstützt zwei Autorisierungsmethoden für Anwendungen im Katalog. 

**Ablauf der OAuth-Autorisierungscodegenehmigung:** Der Bereitstellungsdienst unterstützt die [Autorisierungscodegenehmigung](https://tools.ietf.org/html/rfc6749#page-24). Nachdem Sie Ihre Anforderung zur Veröffentlichung Ihrer App im Katalog übermittelt haben, wird unser Team mit Ihnen zusammenarbeiten, um die folgenden Informationen zu sammeln:
*  URL für Autorisierung: Eine URL des Clients, um die Autorisierung des Ressourcenbesitzers über die Umleitung des Benutzer-Agents zu erhalten. Der Benutzer wird zu dieser URL umgeleitet, um den Zugriff zu autorisieren. 
*  URL für den Tokenaustausch: Eine URL des Clients, um eine Autorisierungsgenehmigung für ein Zugriffstoken auszutauschen, typischerweise mit Clientauthentifizierung.
*  Client-ID: Der Autorisierungsserver stellt dem registrierten Client eine Client-ID aus, die eine eindeutige Zeichenfolge ist, die die vom Client bereitgestellten Registrierungsinformationen darstellt.  Die Client-ID ist kein Geheimnis. Sie wird dem Ressourcenbesitzer verfügbar gemacht und **darf nicht** allein für die Clientauthentifizierung verwendet werden.  
*  Geheimer Clientschlüssel: Der geheime Clientschlüssel ist ein Geheimnis, das vom Autorisierungsserver generiert wird. Es sollte ein eindeutiger Wert sein, der nur dem Autorisierungsserver bekannt ist. 

Beachten Sie, dass OAuth v1 aufgrund der Gefährdung des geheimen Clientschlüssels nicht unterstützt wird. OAuth v2 dagegen wird unterstützt.  

Bewährte Methoden (empfohlen, aber nicht erforderlich):
* Unterstützen Sie mehrere Umleitungs-URLs. Administratoren können die Bereitstellung sowohl über „portal.azure.com“ als auch über „aad.portal.azure.com“ konfigurieren. Die Unterstützung mehrerer Umleitungs-URLs stellt sicher, dass Benutzer den Zugriff von beiden Portalen aus autorisieren können.
* Unterstützen Sie mehrere Geheimnisse, um eine reibungslose Erneuerung der Geheimnisse ohne Ausfallzeiten sicherzustellen. 

**Langlebige OAuth-Bearertoken:** Wenn Ihre Anwendung den Ablauf der OAuth-Autorisierungscodegenehmigung nicht unterstützt, können Sie auch ein langlebiges OAuth-Bearertoken generieren, mit dem ein Administrator die Integration der Bereitstellung einrichten kann. Das Token sollte unbefristet sein, andernfalls wird der Bereitstellungsauftrag nach Ablauf des Token [unter Quarantäne gestellt](application-provisioning-quarantine-status.md). Dieses Token muss eine Größe von weniger als 1 KB aufweisen.  

Teilen Sie uns über [UserVoice](https://aka.ms/appprovisioningfeaturerequest) mit, wenn weitere Methoden zur Authentifizierung und Autorisierung erforderlich sind.

### <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>Zulassen, dass vom Azure AD-Bereitstellungsdienst verwendete IP-Adressen SCIM-Anforderungen senden

Bestimmte Apps lassen eingehenden Datenverkehr für ihre App zu. Damit der Azure AD-Bereitstellungsdienst erwartungsgemäß funktioniert, müssen die verwendeten IP-Adressen zulässig sein. Eine Liste der IP-Adressen für die einzelnen Diensttags/Regionen finden Sie in der JSON-Datei unter [Azure-IP-Bereiche und -Diensttags – öffentliche Cloud](https://www.microsoft.com/download/details.aspx?id=56519). Sie können diese IP-Adressen herunterladen und bei Bedarf für Ihre Firewall programmieren. Die reservierten IP-Adressbereiche für die Azure AD-Bereitstellung finden Sie unter „AzureActiveDirectoryDomainServices“.

## <a name="related-articles"></a>Verwandte Artikel

* [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Apps](user-provisioning.md)
* [Anpassen von Attributzuordnungen für die Benutzerbereitstellung für SaaS-Anwendungen in Azure Active Directory](customize-application-attributes.md)
* [Schreiben von Ausdrücken für Attributzuordnungen](functions-for-customizing-application-data.md)
* [Bereichsfilter für die Benutzerbereitstellung](define-conditional-rules-for-provisioning-user-accounts.md)
* [Kontobereitstellungsbenachrichtigungen](user-provisioning.md)
* [Liste der Tutorials zur Integration von SaaS-Apps](../saas-apps/tutorial-list.md)
