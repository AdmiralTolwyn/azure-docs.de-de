---
title: Grundlegendes zur Azure Digital Twins-API-Authentifizierung | Microsoft-Dokumentation
description: Durchführen der Verbindungsherstellung und Authentifizierung für APIs mithilfe von Azure Digital Twins
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: lyrana
ms.openlocfilehash: 4ea4479d77e06940bed50859341952ffbcbbda46
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60533826"
---
# <a name="connect-and-authenticate-to-apis"></a>Durchführen der Verbindungsherstellung und Authentifizierung für APIs

Für Azure Digital Twins wird Azure Active Directory (Azure AD) verwendet, um Benutzer zu authentifizieren und Anwendungen zu schützen. Azure AD unterstützt die Authentifizierung für eine Vielzahl moderner Architekturen. Diese basieren alle auf den branchenüblichen Protokollen OAuth 2.0 oder OpenID Connect. Darüber hinaus können Entwickler Azure AD dazu verwenden, Anwendungen mit einem Mandanten sowie Branchenanwendungen zu erstellen. Entwickler können Azure AD auch für die Entwicklung mehrinstanzenfähiger Anwendungen nutzen.

Die Seite mit der [Dokumentation mit grundlegenden Informationen zu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/index) enthält eine Übersicht über Azure AD. Dort finden Sie beispielsweise Schritt-für-Schritt-Anleitungen, Informationen zu Konzepten sowie Schnellstartanleitungen.

Um eine Anwendung oder einen Dienst in Azure AD zu integrieren, muss ein Entwickler die Anwendung zuerst bei Azure AD registrieren. Ausführliche Anweisungen und Screenshots finden Sie in [dieser Schnellstartanleitung](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

Azure AD unterstützt die folgenden [fünf Hauptanwendungsszenarien](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types):

* Single-Page-Webanwendung (SPA): Ein Benutzer muss sich bei einer durch Azure AD geschützten Single-Page-Webanwendung anmelden.
* Webbrowser zu Webanwendung: Ein Benutzer muss sich bei einer durch Azure AD geschützten Webanwendung anmelden.
* Native Anwendung zu Web-API: Eine native Anwendung auf einem Smartphone, Tablet oder PC muss einen Benutzer authentifizieren, um Ressourcen von einer durch Azure AD geschützten Web-API abzurufen.
* Webanwendung zu Web-API: Eine Webanwendung muss Ressourcen von einer durch Azure AD geschützten Web-API abrufen.
* Daemon- oder Serveranwendung zu Web-API: Eine Daemon- oder Serveranwendung ohne Webbenutzeroberfläche muss Ressourcen von einer durch Azure AD geschützten Web-API abrufen.

Die Microsoft Azure-Authentifizierungsbibliothek bietet viele Möglichkeiten zum Beziehen von Active Directory-Token. Ausführliche Informationen zur Bibliothek und zu den Codebeispielen finden Sie in [diesem Artikel](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Aufrufen von Digital Twins über eine Web-API der mittleren Ebene

Wenn Entwickler Digital Twins-Lösungen entwerfen, erstellen Sie in der Regel eine Anwendung oder API der mittleren Ebene. Die App oder API ruft dann im weiteren Verlauf die Digital Twins-API auf. Zur Unterstützung dieser standardmäßigen Weblösungsarchitektur ist Folgendes erforderlich:

1. Benutzer müssen sich zunächst bei der Anwendung der mittleren Ebene authentifizieren.

1. Im Rahmen der Authentifizierung muss ein OAuth 2.0-Token vom Typ „Im Auftrag von“ bezogen werden.

1. Das abgerufene Token wird dann im Rahmen des Ablaufs vom Typ „Im Auftrag von“ für die Authentifizierung oder zum Aufrufen von Downstream-APIs verwendet.

Eine Anleitung für die Orchestrierung des Ablaufs vom Typ „Im Auftrag von“ finden Sie unter [Azure Active Directory v2.0- und Im-Auftrag-von-Ablauf von OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Unter [Calling a downstream web API from a web API using Azure AD](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/) (Aufrufen einer Downstream-Web-API über eine Web-API unter Verwendung von Azure AD) stehen außerdem Codebeispiele zur Verfügung.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Konfigurieren und Testen von Azure Digital Twins unter Verwendung des impliziten OAuth 2.0-Gewährungsablaufs finden Sie unter [Vorgehensweise: Konfigurieren von Postman für Azure Digital Twins](./how-to-configure-postman.md).

Informationen zur Sicherheit von Azure Digital Twins finden unter [Erstellen und Verwalten von Rollenzuweisungen](./security-create-manage-role-assignments.md).