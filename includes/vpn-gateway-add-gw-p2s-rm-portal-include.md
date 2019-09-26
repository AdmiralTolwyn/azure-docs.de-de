---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/24/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9ce9fa29a29559a1eaaff6173737159f11aa83d8
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268889"
---
1. Klicken Sie im Portal auf der linken Seite auf **+ Ressource erstellen**, und geben Sie für die Suche „Gateway für virtuelle Netzwerke“ ein. Suchen Sie in der Ausgabe nach **Virtuelles Netzwerkgateway**, und klicken Sie auf den Eintrag. Klicken Sie auf der Seite **Gateway für virtuelle Netzwerke** auf **Erstellen**. Die Seite **Gateway für virtuelle Netzwerke erstellen** wird geöffnet.

   ![Felder der Seite „Gateway für virtuelle Netzwerke erstellen“](./media/vpn-gateway-add-gw-p2s-rm-portal-include/p2sgw.png "Felder der Seite „Gateway für virtuelle Netzwerke erstellen“")

   ![Felder der Seite „Gateway für virtuelle Netzwerke erstellen“](./media/vpn-gateway-add-gw-p2s-rm-portal-include/p2sgw2.png "Felder der Seite „Gateway für virtuelle Netzwerke erstellen“")
2. Geben Sie auf der Seite **Gateway für virtuelle Netzwerke erstellen** die Werte für das Gateway für virtuelle Netzwerke an.

   **Projektdetails**

   - **Abonnement**: Wählen Sie in der Dropdownliste das zu verwendende Abonnement aus.
   - **Ressourcengruppe**: Diese Einstellung wird automatisch angegeben, wenn Sie auf dieser Seite Ihr virtuelles Netzwerk auswählen.

   **Instanzendetails**

   - **Name**: Benennen Sie Ihr Gateway. Das Benennen eines Gateways ist nicht das Gleiche wie das Benennen eines Gatewaysubnetzes. Hierbei handelt es sich um den Namen des Gatewayobjekts, das Sie erstellen.
   - **Region**: Wählen Sie die Region aus, in der Sie diese Ressource erstellen möchten. Die Region für das Gateway muss der des virtuellen Netzwerks entsprechen.
   - **Gatewaytyp**: Wählen Sie **VPN** aus. Bei VPN-Gateways wird ein virtuelles Netzwerkgateway vom Typ **VPN** verwendet. 
   - **VPN-Typ:** Wählen Sie den für Ihre Konfiguration angegebenen VPN-Typ aus. Bei den meisten Konfigurationen wird ein routenbasierter VPN-Typ benötigt.
   - **SKU**: Wählen Sie in der Dropdownliste die Gateway-SKU aus. Welche SKUs in der Dropdownliste aufgeführt werden, hängt vom ausgewählten VPN-Typ ab. Weitere Informationen zu Gateway-SKUs finden Sie unter [Gateway-SKUs](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).

      **Virtuelles Netzwerk:** Wählen Sie das virtuelle Netzwerk aus, dem Sie dieses Gateway hinzufügen möchten.

      **Adressbereich für Gatewaysubnetz**: Dieses Feld wird nur angezeigt, wenn das ausgewählte virtuelle Netzwerk nicht über ein Gatewaysubnetz verfügt. Füllen Sie den Bereich aus, wenn Sie noch nicht über ein Gatewaysubnetz verfügen. Legen Sie den Bereich möglichst als /27 oder größer (/26, /25 usw.) fest.

   **Öffentliche IP-Adresse:** Mit dieser Einstellung wird das Objekt für die öffentliche IP-Adresse angegeben, das dem VPN-Gateway zugeordnet wird. Die öffentliche IP-Adresse wird bei der Erstellung des VPN-Gateways diesem Objekt dynamisch zugewiesen. Die öffentliche IP-Adresse ändert sich nur, wenn das Gateway gelöscht und neu erstellt wird. Sie ändert sich nicht, wenn die Größe geändert wird, das VPN-Gateway zurückgesetzt wird oder andere interne Wartungs-/Upgradevorgänge für das VPN-Gateway durchgeführt werden.

     - **Öffentliche IP-Adresse:** Lassen Sie **Neu erstellen** aktiviert.
     - **Öffentliche IP-Adresse**: Geben Sie im Textfeld einen Namen für die öffentliche IP-Adressinstanz ein.
     - **Zuweisung**: VPN Gateway unterstützt nur die dynamische Zuweisung.

   **Aktiv/Aktiv-Modus:** Aktivieren Sie **Aktiv/Aktiv-Modus aktivieren** nur, wenn Sie eine Aktiv/Aktiv-Gatewaykonfiguration erstellen. Lassen Sie diese Einstellung andernfalls deaktiviert.

   Aktivieren Sie **BGP-ASN konfigurieren** nur, wenn diese Einstellung für Ihre Konfiguration speziell erforderlich ist. Sollte diese Einstellung erforderlich sein, lautet die Standard-ASN 65515. (Dieser Wert kann jedoch geändert werden.)

3. Klicken Sie zum Ausführen der Validierung auf **Bewerten + erstellen**. Klicken Sie nach Abschluss der Überprüfung auf **Erstellen**, um das VPN-Gateway bereitzustellen. Die gesamte Erstellung und Bereitstellung eines Gateways kann bis zu 45 Minuten dauern. Der Bereitstellungsstatus wird auf der Übersichtsseite für Ihr Gateway angezeigt.

Nach der Erstellung des Gateways können Sie die zugewiesene IP-Adresse unter dem Virtual Network im Portal anzeigen. Das Gateway wird als verbundenes Gerät angezeigt.