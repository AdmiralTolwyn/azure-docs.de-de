---
title: Includedatei
description: Includedatei
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ea616786d69d41435be2a46e90d4973b21270935
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/23/2018
---
1. Öffnen Sie die Seite Ihres Gateways für virtuelle Netzwerke. Dazu gibt es verschiedene Möglichkeiten. Im Beispiel wurde mit **TestVNet1 > Überblick > Verbundene Geräte > VNet1GW** zum Gateway „VNet1GW“ navigiert.
2. Klicken Sie auf der Seite für „VNet1GW“ auf **Verbindungen**. Klicken Sie oben auf der Seite „Verbindungen“ auf **+Hinzufügen**, um die Seite **Verbindung hinzufügen** zu öffnen.

    ![Einrichten einer Standort-zu-Standort-Verbindung](./media/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include/connection1.png)

3. Geben Sie auf der Seite **Verbindung hinzufügen** die Werte zum Erstellen der Verbindung ein.

  - **Name:** Geben Sie einen Namen für die Verbindung ein. Im Beispiel wird **VNet1toSite2** verwendet.
  - **Verbindungstyp:** Wählen Sie **Standort-zu-Standort (IPsec)** aus.
  - **Gateway für virtuelle Netzwerke:** Der Wert ist festgelegt, da Sie von diesem Gateway aus die Verbindung herstellen.
  - **Lokales Netzwerkgateway:** Klicken Sie auf **Lokales Netzwerkgateway auswählen**, und wählen Sie das lokale Netzwerkgateway aus, das Sie verwenden möchten. In unserem Beispiel verwenden wir **Site2**.
  - **Gemeinsam verwendeter Schlüssel:** Dieser Wert muss mit dem Wert übereinstimmen, den Sie für Ihr lokales VPN-Gerät verwenden. Im Beispiel wurde „abc123“ verwendet, aber Sie können (und sollten) einen komplexeren Wert verwenden. Entscheidend ist Folgendes: Der Wert, den Sie hier angeben, muss dem Wert entsprechen, den Sie beim Konfigurieren Ihres VPN-Geräts angegeben haben.
  - Die verbleibenden Werte für **Abonnement**, **Ressourcengruppe** und **Speicherort** wurden korrigiert.

4. Klicken Sie auf **OK** , um die Verbindung zu erstellen. Auf dem Bildschirm blinkt der Hinweis *Verbindung wird erstellt* .
5. Die Verbindung wird auf der Seite **Verbindungen** des Gateways für virtuelle Netzwerke angezeigt. Der Status wechselt von *Unbekannt* zu *Verbindung wird hergestellt* und dann zu *Erfolgreich*.