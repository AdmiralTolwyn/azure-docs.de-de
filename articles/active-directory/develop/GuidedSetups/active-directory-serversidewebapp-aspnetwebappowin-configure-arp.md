---
title: "Azure AD v2 – ASP.NET-Webserver: Erste Schritte – Konfiguration | Microsoft-Dokumentation"
description: "Informationen zum Implementieren einer Microsoft-Anmeldung in einer ASP.NET-Projektmappe mittels einer herkömmlichen webbrowserbasierten Anwendung unter Verwendung des Standards OpenID Connect"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 8a1650a65e7980f4a13fa4edc7918b0099bb5464
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2017
---
## <a name="configure-your-aspnet-web-app-with-the-applications-registration-information"></a>Konfigurieren Ihrer ASP.NET-Web-App mit den-Registrierungsinformationen der Anwendung

In diesem Schritt konfigurieren Sie Ihr Projekt für das Verwenden von SSL. Anschließend verwenden Sie die SSL-URL zum Konfigurieren der Registrierungsinformationen Ihrer Anwendung. Anschließend fügen Sie die Registrierungsinformationen Ihrer Anwendung der Projektmappe über *web.config* hinzu.

1.  Wählen Sie im Projektmappen-Explorer das Projekt aus. Untersuchen Sie das Fenster `Properties` (wenn es nicht angezeigt wird, drücken Sie F4).
2.  Ändern Sie `SSL Enabled` in `True`.
3.  Kopieren Sie den obigen Wert von `SSL URL`, und fügen Sie ihn oben auf dieser Seite in das Feld `Redirect URL` ein. Klicken Sie dann auf *Aktualisieren*:<br/><br/>![Projekteigenschaften](media/active-directory-serversidewebapp-aspnetwebappowin-configure/vsprojectproperties.png)<br />
4.  Fügen Sie im Stammordner der Datei `web.config` im Abschnitt `configuration\appSettings` Folgendes hinzu:

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
