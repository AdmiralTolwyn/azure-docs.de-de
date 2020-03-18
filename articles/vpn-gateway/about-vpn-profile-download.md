---
title: 'Azure-VPN Gateway: Informationen zu P2S VPN-Clientprofilen'
description: Diese Informationen unterstützen Sie bei der Arbeit mit der Clientprofildatei.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/11/2020
ms.author: cherylmc
ms.openlocfilehash: 762f62fa0901672c447da42f416e5b003e7419b2
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127295"
---
# <a name="about-p2s-vpn-client-profiles"></a>Informationen zu P2S VPN-Clientprofilen

Die heruntergeladene Profildatei enthält Informationen, die zum Konfigurieren einer VPN-Verbindung erforderlich sind. Dieser Artikel hilft Ihnen beim Abrufen und Verstehen der Informationen, die für ein VPN-Clientprofil erforderlich sind.

## <a name="1-download-the-file"></a>1. Herunterladen der Datei

Führen Sie die folgenden Befehle aus: Kopieren Sie die Ergebnis-URL in Ihren Browser, um die ZIP-Datei für das Profil herunterzuladen.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. Extrahieren der ZIP-Datei

Extrahieren Sie die ZIP-Datei. Die Datei enthält die folgenden Ordner:

* AzureVPN
* Allgemein
* OpenVPN (wenn Sie für das Gateway die OpenVPN- und Azure AD Authentifizierungseinstellungen aktiviert haben. Siehe [Erstellen eines Azure Active Directory-Mandanten ](openvpn-azure-ad-tenant.md).)

## <a name="3-retrieve-information"></a>3. Abrufen von Informationen

Navigieren Sie im Ordner **AzureVPN** zur Datei ***azurevpnconfig.xml***, und öffnen Sie sie mit Editor. Notieren Sie sich den Text zwischen den folgenden Tags.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Profildetails

Wenn Sie eine Verbindung hinzufügen, verwenden Sie die im vorherigen Schritt auf der Seite mit den Profildetails gesammelten Informationen. Die Felder entsprechen den folgenden Informationen:

   * **Audience:** Die Empfängerressource, für die das Token vorgesehen ist
   * **Issuer:** Der Azure AD-Mandant und der Sicherheitstokendienst (STS), der das Token ausgegeben hat
   * **Tenant:** Ein unveränderlicher, eindeutiger Bezeichner des Verzeichnismandanten, der das Token ausgestellt hat
   * **FQDN:** Der vollqualifizierte Domänenname (FQDN) in Azure VPN Gateway
   * **ServerSecret:** Der vorinstallierte Schlüssel des VPN-Gateways

## <a name="folder-contents"></a>Ordnerinhalt

* Der Ordner **OpenVPN** enthält das Profil *ovpn*, das geändert werden muss, um den Schlüssel und das Zertifikat einzuschließen. Weitere Informationen finden Sie unter [Konfigurieren von OpenVPN-Clients für Azure VPN Gateway](vpn-gateway-howto-openvpn-clients.md#windows). Wenn Azure AD Authentifizierung auf dem VPN-Gateway ausgewählt ist, ist dieser Ordner in der ZIP-Datei nicht vorhanden. Stattdessen befindet sich die Datei „azurevpnconfig.xml“ im Ordner „AzureVPN“.

* Der Ordner **generic** enthält das öffentliche Serverzertifikat und die Datei „VpnSettings.xml“. Die Datei „VpnSettings.xml“ enthält Informationen, die zum Konfigurieren eines generischen Clients benötigt werden.

* Die heruntergeladene ZIP-Datei kann auch noch die Ordner **WindowsAmd64** und **WindowsX86** enthalten. Diese Ordner enthalten das Installationsprogramm für SSTP und IKEv2 für Windows-Clients. Sie benötigen Administratorrechte auf dem Client, um diese zu installieren.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Point-to-Site-VPN-Verbindungen finden Sie unter [Informationen zu Point-to-Site-VPN](point-to-site-about.md).
