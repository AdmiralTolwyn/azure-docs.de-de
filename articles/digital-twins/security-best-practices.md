---
title: Grundlegendes zu bewährten Methoden für die Sicherheit – Azure Digital Twins | Microsoft-Dokumentation
description: Erfahren Sie mehr über bewährte Methoden für die Azure Digital Twins-Sicherheit und das Internet der Dinge.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 5fc5ba447557aa89e8f0870c576d6d4c439f3353
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122558"
---
# <a name="azure-digital-twins-security-best-practices"></a>Bewährte Methoden für Azure Digital Twins-Sicherheit

Azure Digital Twins-Sicherheit ermöglicht präzisen Zugriff auf bestimmte Ressourcen und Aktionen in Ihrem IoT-Graphen. Dies erfolgt mithilfe einer detaillierten Rollen- und Rechteverwaltung namens [Rollenbasierte Zugriffssteuerung](./security-role-based-access-control.md).

Azure Digital Twins verwendet außerdem weitere Sicherheitsfunktionen, die in Azure IoT verfügbar sind, einschließlich Azure Active Directory (Azure AD). Aus diesem Grund beinhaltet ein Konfigurieren und Schützen von Anwendungen, die auf Basis von Azure Digital Twins erstellt wurden, ein Verwenden von vielen derselben [Sicherheitsmaßnahmen für Azure IoT](../iot-fundamentals/iot-security-best-practices.md), die derzeit empfohlen werden.

In diesem Artikel sind die wichtigsten bewährten Methoden zusammengefasst, die Sie befolgen sollten.

> [!IMPORTANT]
> Überprüfen Sie zusätzliche Sicherheitsressourcen, um die maximale Sicherheit für Ihren IoT-Raum zu gewährleisten. Achten Sie darauf, Ihre Gerätehersteller zu berücksichtigen.

> [!TIP]
> Verwenden Sie [Azure Security Center für IoT](https://docs.microsoft.com/azure/asc-for-iot/), um IoT-Sicherheitsbedrohungen und -Sicherheitslücken zu erkennen.

## <a name="iot-security-best-practices"></a>Bewährte Methoden für die IoT-Sicherheit

Einige der wichtigsten Methoden zum sicheren Schutz Ihrer IoT-Geräte sind:

> [!div class="checklist"]
> * Schützen Sie jedes Gerät, das mit Ihrem IoT-Raum verbunden ist, auf eine manipulationssichere Weise.
> * Begrenzen Sie die Rolle jedes Geräts, jedes Sensors und jeder Person in Ihrem IoT-Raum. Bei einer Kompromittierung werden die Auswirkungen minimiert.
> * Verwenden Sie ggf. Filterung der Geräte-IP-Adressen und Einschränkungen der Ports.
> * Begrenzen Sie E/A- und Gerätebandbreite, um die Leistung zu verbessern. Begrenzung der Übertragungsrate kann die Sicherheit verbessern, indem Denial-of-Service-Angriffe verhindert werden.
> * Halten Sie die Gerätefirmware, das Betriebssystem und die Software auf dem neuesten Stand.
> * Überprüfen Sie Ihre Best Practices für die Geräte-, Software-, Netzwerk- und Gatewaysicherheit in regelmäßigen Abständen, da diese sich im Lauf der Zeit weiterentwickeln und verbessern.
> * Verwenden Sie vertrauenswürdige, zertifizierte und kompatible Sicherheitssysteme, Software und Geräte. Sehen Sie sich beispielsweise [die Complianceangebote](https://azure.microsoft.com/overview/trusted-cloud/compliance/) für Azure Cloud an.

Einige der wichtigsten Methoden zum sicheren Schutz eines IoT-Raums sind:

> [!div class="checklist"]
> * Verschlüsseln Sie gespeicherte oder persistente Daten.
> * Erfordern Sie, dass Kennwörter oder Schlüssel in regelmäßigen Abständen geändert oder aktualisiert werden.
> * Schränken Sie den Zugriff und die Berechtigungen sorgfältig nach Rollen ein. Lesen Sie den Abschnitt [Rollenbasierte Zugriffssteuerung – bewährte Methoden](#role-based-access-control-best-practices) weiter unten.
> * Erwägen Sie die Einrichtung einer geteilten Netzwerktopologie, sodass die Geräte in den einzelnen Netzwerken von den anderen Netzwerken isoliert sind.
> * Verwenden Sie leistungsstarke Verschlüsselung. Schreiben Sie lange Kennwörter vor, und verwenden Sie sichere Protokolle sowie [zweistufige Authentifizierung](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).

[Überwachen](./how-to-configure-monitoring.md) Sie IoT-Ressourcen, und achten Sie auf Ausreißer, Bedrohungen und Ressourcenparameter, die außerhalb des üblichen Betriebsbereichs liegen. Verwenden Sie Azure Analytics zur Überwachung der Verwaltung.

> [!IMPORTANT]
> Lesen Sie Azure [Bewährte Methoden für die IoT-Sicherheit](../iot-fundamentals/iot-security-best-practices.md), um den Grundstein einer umfassenden IoT-Sicherheitsstrategie zu legen.

> [!NOTE]
> Wenn Sie weitere Informationen zum Verarbeiten und Überwachen von Ereignissen benötigen, lesen Sie [Weiterleiten von Ereignissen und Nachrichten mit Azure Digital Twins](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Azure Active Directory – bewährte Methoden

Azure Digital Twins verwendet [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/), um Benutzer zu authentifizieren und Anwendungen zu schützen. Azure Active Directory unterstützt die Authentifizierung für eine Vielzahl moderner Architekturen. Diese basieren alle auf branchenüblichen Protokollen wie OAuth 2.0 oder OpenID Connect. Einige wichtige Methoden zum Schützen Ihres IoT-Raums für Azure Active Directory sind:

> [!div class="checklist"]
> * Speichern Sie geheime Schlüssel von Azure Active Directory-Apps an einem sicheren Ort, z. B. [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).
> * Verwenden Sie zum Authentifizieren anstelle des App-Geheimnisses ein Zertifikat, das von einer vertrauenswürdigen [Zertifizierungsstelle](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) ausgestellt wurde.
> * Begrenzen Sie den OAuth 2.0-Zugriffsbereich für ein Token.
> * Überprüfen Sie, wie lange ein Token gültig ist und ob ein Token gültig bleibt.
> * Legen Sie entsprechende Zeitspannen fest, während denen Token gültig sind. Aktualisieren Sie abgelaufene Token.
> * Entfernen Sie nicht verwendete **Umleitungs-URIs** und Berechtigungen entsprechend [bewährte Methoden für die rollenbasierte Zugriffssteuerung](#role-based-access-control-best-practices).

## <a name="role-based-access-control-best-practices"></a>Rollenbasierte Zugriffssteuerung – bewährte Methoden

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu bewährten Methoden für Azure IoT finden Sie unter [Bewährte Methoden für die IoT-Sicherheit](../iot-fundamentals/iot-security-best-practices.md).

* Weitere Informationen zur rollenbasierten Zugriffssteuerung finden Sie unter [Rollenbasierte Zugriffssteuerung](./security-role-based-access-control.md).

* Wenn Sie mehr über das Authentifizieren erfahren möchten, lesen Sie [Authentifizieren mit APIs](./security-authenticating-apis.md).
