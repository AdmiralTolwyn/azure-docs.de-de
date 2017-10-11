---
title: "Überwachen Sie Ihre lokalen Identitätsinfrastruktur in der Cloud."
description: "Auf der Seite \"Azure AD Connect Health\" wird der Dienst beschrieben, und es werden die Gründe für seine Verwendung erörtert."
services: active-directory
documentationcenter: 
author: karavar
manager: samueld
editor: curtand
ms.assetid: 82798ea6-5cd3-4f30-93ae-d56536f8d8e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 881ce13b6e4b10064294e590431434b29da3fb33
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/03/2017
---
# <a name="monitor-your-on-premises-identity-infrastructure-and-synchronization-services-in-the-cloud"></a>Überwachen Ihrer lokalen Identitätsinfrastruktur und Synchronisierung von Diensten in der Cloud
Azure Active Directory (Azure AD) Connect Health unterstützt Sie bei der Überwachung Ihrer lokalen Identitätsinfrastruktur und Synchronisierungsdienste und vermittelt Ihnen wichtige Einblicke. Sie können für eine zuverlässige Verbindung mit Office 365 und Microsoft Online Services sorgen, indem Sie Überwachungsfunktionen für Ihre wichtigen Identitätskomponenten bereitstellen, z. B. Active Directory Federation Services (AD FS), Azure AD Connect-Server (Synchronisierungsmodul), Active Directory-Domänencontroller usw. Außerdem sind die wichtigen Datenpunkte dieser Komponenten hierbei leicht zugänglich, sodass Sie Informationen zur Nutzung und andere wichtige Einblicke erhalten und fundierte Entscheidungen treffen können.

Die Informationen werden im [Azure AD Connect Health-Portal](https://aka.ms/aadconnecthealth)angezeigt. Im Azure AD Connect Health-Portal können Sie Warnungen, Leistungsüberwachungsdaten, Nutzungsanalysen und andere Informationen anzeigen. Azure AD Connect Health ermöglicht Ihnen an einem zentralen Ort einen Gesamtüberblick über die Integrität Ihrer wichtigsten Identitätskomponenten.

![Was ist Azure AD Connect Health?](./media/active-directory-aadconnect-health/aadconnecthealth2.png)

Azure AD Connect Health umfasst immer mehr Funktionen und ermöglicht Ihnen an einem zentralen Dashboard einen Gesamtüberblick über Ihre Identitätskomponenten. Sie sorgen für eine stabilere, fehlerfreie und besser integrierte Umgebung, damit Ihre Benutzer ihre Aufgaben schneller erledigen können.

## <a name="why-use-azure-ad-connect-health"></a>Gründe für die Verwendung von Azure AD Connect Health
Beim Integrieren Ihrer lokalen Verzeichnisse in Azure AD steigern Sie die Produktivität Ihrer Benutzer, da für den Zugriff auf die Cloud und lokale Ressourcen nur eine Identität benötigt wird. Allerdings erfordert diese Integration, dass diese Umgebung fehlerfrei bleibt, sodass Benutzer von jedem Gerät verlässlich auf lokale ebenso wie auf Cloudressourcen zugreifen können. Azure AD Connect Health unterstützt Sie beim Überwachen und Gewinnen von Einblicken in Ihre lokale Identitätsverwaltungsinfrastruktur, die für den Zugriff auf Office 365 oder andere Azure AD-Anwendungen genutzt wird. Dieser Ansatz ist ähnlich unkompliziert wie das Installieren eines Agents auf Ihren lokalen Identitätsservern.

## <a name="azure-ad-connect-health-for-ad-fsactive-directory-aadconnect-health-adfsmd"></a>[Azure AD Connect Health für AD FS](active-directory-aadconnect-health-adfs.md)
Azure AD Connect Health für AD FS unterstützt AD FS 2.0 unter Windows Server 2008 R2, Windows Server 2012 und Windows Server 2012 R2. Außerdem wird die Überwachung der AD FS-Proxy- oder Webanwendungsproxy-Server zur Authentifizierung für den Extranetzugriff unterstützt. Mit einer einfachen und kostengünstigen Installation des Health-Agents stellt Azure AD Connect Health für AD FS die folgenden Hauptfunktionen bereit:

* Überwachung mit Warnungen als Information, wenn die Integrität von AD FS und der AD FS-Proxyserver nicht gewährleistet ist
* E-Mail-Benachrichtigungen für kritische Warnungen
* Trends in Leistungsdaten, nützlich für die Kapazitätsplanung von AD FS
* Nutzungsanalysen für AD FS-Anmeldungen mit Pivotierung (Apps, Benutzer, Netzwerkspeicherort usw.), nützlich für das Verständnis der AD FS-Nutzung
* Berichte für AD FS, z. B. Top-50-Benutzer mit fehlerhaften Anmeldeversuchen per Benutzername/Kennwort über die letzte IP-Adresse

Das folgende Video bietet eine Übersicht über Azure AD Connect Health für AD FS.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health--Monitor-you-identity-bridge/player]
>
>

## <a name="azure-ad-connect-health-for-syncactive-directory-aadconnect-health-syncmd"></a>[Azure AD Connect Health für die Synchronisierung](active-directory-aadconnect-health-sync.md)
Azure AD Connect Health für die Synchronisierung überwacht die Synchronisierungen zwischen Ihrem lokalen Active Directory und Azure AD. Azure AD Connect Health für die Synchronisierung stellt die folgenden wichtigen Funktionen bereit:

* Überwachung mit Warnungen als Information, wenn die Integrität von Azure AD Connect-Servern (Synchronisierungsmodul) nicht gewährleistet ist
* E-Mail-Benachrichtigungen für kritische Warnungen
* Synchronisierung von betrieblichen Informationen, z.B. Latenzdiagramme für Synchronisierungsvorgänge und Trends von unterschiedlichen Vorgängen wie Hinzufügungen, Aktualisierungen und Löschungen
* Schnellübersicht über Synchronisierungseigenschaften und letzten erfolgreichen Export zu Azure AD
* Für Berichte zu Fehlern bei der Synchronisierung auf Objektebene \(ist Azure AD Premium nicht erforderlich\)

Das folgende Video enthält eine Übersicht über Azure AD Connect Health für die Synchronisierung.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Health-Monitoring-the-sync-engine/player]
>
>

## <a name="azure-ad-connect-health-for-ad-dsactive-directory-aadconnect-health-addsmd"></a>[Azure AD Connect Health für AD DS](active-directory-aadconnect-health-adds.md)
Azure AD Connect Health for Active Directory Domain Services (AD DS) ermöglicht die Überwachung für Domänencontroller, die unter Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 und Windows Server 2016 installiert sind. Mit der Health-Agent-Installation können Sie Ihre lokale AD DS-Umgebung direkt über die Cloud überwachen. Azure AD Connect Health für AD DS stellt die folgenden wichtigen Funktionen bereit:

* Überwachung von Warnungen, um zu erkennen, wann Domänencontroller Fehler aufweisen, und von E-Mail-Benachrichtigungen für kritische Warnungen
* Das Domänencontroller-Dashboard mit einer Kurzübersicht über den Integritäts- und Betriebsstatus Ihrer Domänencontroller
* Das Replikationsstatus-Dashboard mit den aktuellen Replikationsinformationen und Links zu Problembehandlungsanleitungen, wenn Fehler erkannt werden
* Schneller Zugriff von jedem Ort auf Leistungsdaten-Graphen gängiger Leistungsindikatoren für Problembehandlung und Überwachung

Das folgende Video enthält eine Übersicht über Azure AD Connect Health für AD DS.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health-monitors-on-premises-AD-Domain-Services/player]
>
>

## <a name="get-started-with-azure-ad-connect-health"></a>Erste Schritte mit Azure AD Connect Health
Der Einstieg in Azure AD Connect Health ist einfach:

1. [Rufen Sie Azure AD Premium ab](../active-directory-get-started-premium.md), oder [beginnen Sie mit einer Testaktivierung](https://azure.microsoft.com/trial/get-started-active-directory/).
2. [Laden Sie Azure AD Connect Health-Agents herunter, und installieren Sie sie](#download-and-install-azure-ad-connect-health-agent) auf Ihren Identitätsservern.
3. Zeigen Sie das Azure AD Connect Health-Dashboard unter [https://aka.ms/aadconnecthealth](https://aka.ms/aadconnecthealth) an.

> [!NOTE]
> Denken Sie daran, dass Sie die Azure AD Connect Health-Agents auf Ihren Zielservern installieren müssen, um in Ihrem Azure AD Connect Health-Dashboard Daten anzeigen zu können.
>
>

## <a name="download-and-install-azure-ad-connect-health-agent"></a>Herunterladen und Installieren des Azure AD Connect Health-Agents
* Vergewissern Sie sich, dass Sie [die Anforderungen für Azure AD Connect Health erfüllen](active-directory-aadconnect-health-agent-install.md#requirements).
* Erste Schritte mit Azure AD Connect Health für AD FS
    * [Laden Sie den Azure AD Connect Health-Agent für AD FS herunter.](http://go.microsoft.com/fwlink/?LinkID=518973)
    * [Zeigen Sie die Installationsanweisungen an](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs).
* Erste Schritte mit Azure AD Connect Health für die Sychronisierung
    * [Laden Sie die aktuelle Version von Azure AD Connect herunter, und installieren Sie sie](http://go.microsoft.com/fwlink/?linkid=615771). Der Health-Agent für die Synchronisierung wird im Rahmen der Installation von Azure AD Connect installiert (Version 1.0.9125.0 oder höher).
* Erste Schritte mit Azure AD Connect Health für AD DS
    * [Laden Sie den Azure AD Connect Health-Agent für AD DS herunter](http://go.microsoft.com/fwlink/?LinkID=820540).
    * [Zeigen Sie die Installationsanweisungen an](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-ds).

## <a name="azure-ad-connect-health-portal"></a>Azure AD Connect Health-Portal
Das Azure AD Connect Health-Portal zeigt Warnungen, Leistungsüberwachungsdaten und Nutzungsanalysen an. Mit der URL „https://aka.ms/aadconnecthealth“ gelangen Sie zum Hauptblatt von Azure AD Connect Health. Sie können es sich wie ein Fenster vorstellen. Auf dem Hauptblatt werden die Option **Schnellstart**, die Dienste von Azure AD Connect Health und weitere Konfigurationsoptionen angezeigt. Im folgenden Screenshot sehen Sie diese Elemente und darunter finden Sie ihre Beschreibung. Nachdem Sie die Agents bereitgestellt haben, wird der Integritätsdienst für die Dienste automatisch identifiziert, die von Azure AD Connect Health überwacht werden.

> [!NOTE]
> Informationen zur Lizenzierung finden Sie unter [Häufig gestellte Fragen zu Azure AD Connect](active-directory-aadconnect-health-faq.md) oder [Seite zur Preisgestaltung von Azure AD](https://aka.ms/aadpricing).
    
![Azure AD Connect Health-Portal](./media/active-directory-aadconnect-health/portal4.png)

* **Schnellstart**: Bei Auswahl dieser Option wird das Blatt **Schnellstart** geöffnet. Sie können Sie den Azure AD Connect Health-Agent herunterladen, indem Sie **Tools abrufen** auswählen. Sie können auch auf die Dokumentation zugreifen und Feedback geben.
* **Active Directory-Verbunddienste**: Diese Option zeigt alle AD FS-Dienste, die aktuell von Azure AD Connect Health überwacht werden. Wenn Sie eine Instanz auswählen, wird ein Blatt mit Informationen zu dieser Dienstinstanz geöffnet, darunter beispielsweise eine Übersicht, Eigenschaften, Warnungen, Überwachungsinformationen und eine Nutzungsanalyse. Weitere Informationen zu den Funktionen finden Sie unter [Verwenden von Azure AD Connect Health mit AD FS](active-directory-aadconnect-health-adfs.md).
* **Azure Active Directory Connect (Sync)**: Diese Option zeigt Ihre Azure AD Connect-Server, die von Azure AD Connect Health derzeit überwacht werden. Wenn Sie eine der Gesamtstrukturen auswählen, wird ein Blatt mit Informationen zu Ihren Azure AD Connect-Servern geöffnet. Weitere Informationen zu den Funktionen finden Sie unter [Verwenden von Azure AD Connect Health für die Synchronisierung](active-directory-aadconnect-health-sync.md).
* **Active Directory Domain Services**: Diese Option zeigt alle AD DS-Gesamtstrukturen, die von Azure AD Connect Health derzeit überwacht werden. Wenn Sie eine Gesamtstruktur auswählen, wird ein Blatt mit Informationen zu dieser Gesamtstruktur geöffnet. Diese Informationen umfassen eine Übersicht mit den wichtigsten Informationen, das Domänencontroller-Dashboard, das Replikationsstatus-Dashboard, Warnungen und Überwachungsdaten. Weitere Informationen zu den Funktionen finden Sie unter [Verwenden von Azure AD Connect Health mit AD DS](active-directory-aadconnect-health-adds.md).
* **Konfigurieren**: In diesem Abschnitt können Sie folgende Optionen aktivieren oder deaktivieren:

  - Automatische Aktualisierung des Azure AD Connect Health-Agents auf die aktuelle Version: Eine automatische Aktualisierung auf die aktuelle Version des Azure AD Connect Health-Agents wird durchgeführt, sobald diese verfügbar ist. Diese Einstellung ist standardmäßig aktiviert.
  - Microsoft zu Zwecken der Problembehandlung den Zugriff auf die Integritätsdaten für Azure AD-Verzeichnis erlauben: Wenn Sie diese Einstellung aktivieren, kann Microsoft dieselben Daten anzeigen wie Sie. Diese Informationen können bei der Problembehandlung und zur Unterstützung bei der Fehlerbeseitigung von Nutzen sein. Diese Einstellung ist standardmäßig deaktiviert.

## <a name="related-links"></a>Verwandte Links
* [Installieren des Azure AD Connect Health-Agents](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health-Vorgänge](active-directory-aadconnect-health-operations.md)
* [Verwenden von Azure AD Connect Health mit AD FS](active-directory-aadconnect-health-adfs.md)
* [Verwenden von Azure AD Connect Health für die Synchronisierung](active-directory-aadconnect-health-sync.md)
* [Verwenden von Azure AD Connect Health mit AD DS](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health – FAQ](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect Health: Versionsverlauf](active-directory-aadconnect-health-version-history.md)
