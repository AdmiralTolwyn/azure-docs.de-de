---
title: Includedatei
description: Includedatei
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/06/2018
ms.author: cherylmc
ms.openlocfilehash: 01a62fe7abb8a79f9afc08c0ff707cdfbb97ddac
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44343192"
---
Auf jedem Clientcomputer, der per Punkt-zu-Standort eine Verbindung mit einem VNet herstellt, muss ein Clientzertifikat installiert sein. Das Clientzertifikat wird über das Stammzertifikat generiert und auf jedem Clientcomputer installiert. Wenn kein gültiges Clientzertifikat installiert ist und der Client versucht, eine Verbindung mit dem VNet herzustellen, tritt bei der Authentifizierung ein Fehler auf.

Sie können entweder ein eindeutiges Zertifikat für jeden Client generieren, oder Sie können dasselbe Zertifikat für mehrere Clients verwenden. Der Vorteil beim Generieren von eindeutigen Clientzertifikaten besteht darin, dass Sie ein einzelnes Zertifikat widerrufen können. Falls mehrere Clients das gleiche Clientzertifikat verwenden und Sie dieses Zertifikat widerrufen müssen, müssen Sie für alle Clients, die das Zertifikat zur Authentifizierung verwenden, neue Zertifikate generieren und installieren.

Sie können mithilfe der folgenden Methoden Clientzertifikate generieren:

- **Unternehmenszertifikat:**

  - Generieren Sie bei Verwendung einer Unternehmenszertifikatlösung ein Clientzertifikat mit dem gängigen Name-Wert-Format „name@yourdomain.com“ (anstatt des Formats „Domänenname\Benutzername“).
  - Stellen Sie sicher, dass das Clientzertifikat auf der Zertifikatvorlage „Benutzer“ basiert, die in der Nutzungsliste als ersten Eintrag „Clientauthentifizierung“ enthält (anstatt „Smartcard-Anmeldung“ usw.). Sie können das Zertifikat überprüfen, indem Sie auf das Clientzertifikat doppelklicken und **Details > Erweiterte Schlüsselverwendung** anzeigen.

- **Selbstsigniertes Stammzertifikat:** Führen Sie unbedingt die Schritte in einem der unten aufgeführten Artikel zu P2S-Zertifikaten aus. Andernfalls sind die erstellten Clientzertifikate nicht mit P2S-Verbindungen kompatibel, und an Clients wird beim Herstellen einer Verbindung ein Fehler zurückgegeben. Mit den Schritten in einem der folgenden Artikeln wird ein kompatibles Clientzertifikat generiert: 

  * [Generieren und Exportieren von Zertifikaten für Punkt-zu-Site-Verbindungen mithilfe von PowerShell unter Windows 10](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert): Für diese Anweisungen sind Windows 10 und PowerShell erforderlich, um Zertifikate zu generieren. Die generierten Zertifikate können auf jedem unterstützten P2S-Client installiert werden.
  * [Generieren und Exportieren von Zertifikaten für Point-to-Site-Verbindungen mithilfe von MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): Verwenden Sie MakeCert, wenn Sie keinen Zugriff auf einen Windows 10-Computer haben, um Zertifikate zu generieren. MakeCert ist zwar veraltet, kann aber trotzdem zum Generieren von Zertifikaten verwendet werden. Die generierten Zertifikate können auf jedem unterstützten P2S-Client installiert werden.
  * [Linux-Anweisungen](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)

  Wenn Sie gemäß der weiter oben angegebenen Anleitung ein Clientzertifikat auf der Grundlage eines selbstsignierten Stammzertifikats generieren, wird es automatisch auf dem Computer installiert, den Sie für die Generierung verwendet haben. Falls Sie ein Clientzertifikat auf einem anderen Clientcomputer installieren möchten, müssen Sie es zusammen mit der gesamten Zertifikatkette als PFX-Datei exportieren. Dadurch wird eine PFX-Datei mit Angaben zum Stammzertifikat erstellt, die der Client für eine erfolgreiche Authentifizierung benötigt. Eine Anleitung zum Exportieren eines Zertifikats finden Sie unter [Exportieren eines Clientzertifikats](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport).