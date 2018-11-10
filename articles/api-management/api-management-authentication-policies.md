---
title: Azure API Management-Authentifizierungsrichtlinien | Microsoft Docs
description: Erfahren Sie mehr über die Authentifizierungsrichtlinien, die für die Verwendung in Azure API Management verfügbar sind.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 4c4c03fffa5786bf3a50f4d2c03511f0a2de0f48
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51250950"
---
# <a name="api-management-authentication-policies"></a>API Management-Authentifizierungsrichtlinien
Dieses Thema enthält eine Referenz für die folgenden API Management-Richtlinien. Weitere Informationen zum Hinzufügen und Konfigurieren von Richtlinien finden Sie unter [Richtlinien in API Management](https://go.microsoft.com/fwlink/?LinkID=398186).  

##  <a name="AuthenticationPolicies"></a> Authentifizierungsrichtlinien  
  
-   [Standardauthentifizierung](api-management-authentication-policies.md#Basic) – Authentifizierung mit einem Back-End-Dienst unter Verwendung der Standardauthentifizierung.  
  
-   [Authentifizierung mit Clientzertifikat](api-management-authentication-policies.md#ClientCertificate) – Authentifizierung mit einem Back-End-Dienst unter Verwendung von Clientzertifikaten.  
  
##  <a name="Basic"></a> Standardauthentifizierung  
 Verwenden Sie die Richtlinie `authentication-basic` für die Authentifizierung mit einem Back-End-Dienst unter Verwendung der Standardauthentifizierung. Diese Richtlinie legt letztlich den HTTP-Autorisierungsheader auf den Wert fest, der den Anmeldeinformationen in der Richtlinie entspricht.  
  
### <a name="policy-statement"></a>Richtlinienanweisung  
  
```xml  
<authentication-basic username="username" password="password" />  
```  
  
### <a name="example"></a>Beispiel  
  
```xml  
<authentication-basic username="testuser" password="testpassword" />  
```  
  
### <a name="elements"></a>Elemente  
  
|NAME|BESCHREIBUNG|Erforderlich|  
|----------|-----------------|--------------|  
|authentication-basic|Stammelement|JA|  
  
### <a name="attributes"></a>Attribute  
  
|NAME|BESCHREIBUNG|Erforderlich|Standard|  
|----------|-----------------|--------------|-------------|  
|username|Gibt den Benutzernamen für die Standardanmeldeinformationen an.|JA|N/V|  
|password|Gibt das Kennwort für die Standardanmeldeinformationen an.|JA|N/V|  
  
### <a name="usage"></a>Verwendung  
 Diese Richtlinie kann in den folgenden [Abschnitten](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) und [Bereichen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) von Richtlinien verwendet werden.  
  
-   **Richtlinienabschnitte:** eingehend  
  
-   **Richtlinienbereiche:** API  
  
##  <a name="ClientCertificate"></a> Authentifizieren mit Clientzertifikat  
 Verwenden Sie die Richtlinie `authentication-certificate` für die Authentifizierung mit einem Back-End-Dienst unter Verwendung eines Clientzertifikats. Das Zertifikat muss zuerst [in API Management installiert](https://go.microsoft.com/fwlink/?LinkID=511599) werden, es wird durch seinen Fingerabdruck identifiziert.  
  
### <a name="policy-statement"></a>Richtlinienanweisung  
  
```xml  
<authentication-certificate thumbprint="thumbprint" />  
```  
  
### <a name="example"></a>Beispiel  
  
```xml  
<authentication-certificate thumbprint="....." />  
```  
  
### <a name="elements"></a>Elemente  
  
|NAME|BESCHREIBUNG|Erforderlich|  
|----------|-----------------|--------------|  
|authentication-certificate|Stammelement|JA|  
  
### <a name="attributes"></a>Attribute  
  
|NAME|BESCHREIBUNG|Erforderlich|Standard|  
|----------|-----------------|--------------|-------------|  
|thumbprint|Der Fingerabdruck für das Clientzertifikat.|JA|N/V|  
  
### <a name="usage"></a>Verwendung  
 Diese Richtlinie kann in den folgenden [Abschnitten](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) und [Bereichen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) von Richtlinien verwendet werden.  
  
-   **Richtlinienabschnitte:** eingehend  
  
-   **Richtlinienbereiche:** API  
  

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Verwendung von Richtlinien finden Sie unter:

+ [Richtlinien in Azure API Management](api-management-howto-policies.md)
+ [Transform and protect your API](transform-api.md) (Transformieren und Schützen von APIs)
+ Unter [Richtlinien für die API-Verwaltung](api-management-policy-reference.md) finden Sie eine komplette Liste der Richtlinienanweisungen und der zugehörigen Einstellungen.
+ [API Management policy samples](policy-samples.md) (API Management-Richtlinienbeispiele)   
