---
title: 'Herstellen einer Point-to-Site-Verbindung zwischen einem Computer und einem virtuellen Netzwerk unter Verwendung der Zertifikatauthentifizierung: klassisches Azure-Portal | Microsoft-Dokumentation'
description: "Stellen Sie über das klassische Azure-Portal eine sichere P2S-VPN Gateway-Verbindung mit Ihrem Azure Virtual Network her."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 65e14579-86cf-4d29-a6ac-547ccbd743bd
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/12/2017
ms.author: cherylmc
ms.openlocfilehash: 00a9e580a324ded8e979c2a3c58d51319091b628
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2017
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-certificate-authentication-classic-azure-portal"></a>Konfigurieren einer Point-to-Site-Verbindung mit einem VNET unter Verwendung der Zertifikatauthentifizierung (klassisch): Azure-Portal

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

In diesem Artikel wird beschrieben, wie Sie über das Azure-Portal im Rahmen des klassischen Bereitstellungsmodells ein VNet mit einer P2S-Verbindung (Point-to-Site) erstellen. Bei dieser Konfiguration werden Clients, die eine Verbindung herstellen, mithilfe von Zertifikaten authentifiziert. Sie können diese Konfiguration auch mit einem anderen Bereitstellungstool oder -modell erstellen. Wählen Sie hierzu in der folgenden Liste eine andere Option:

> [!div class="op_single_selector"]
> * [Azure-Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Azure-Portal (klassisch)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

Mit einem P2S-VPN-Gateway (Point-to-Site) können Sie von einem einzelnen Clientcomputer aus eine sichere Verbindung mit Ihrem virtuellen Netzwerk herstellen. Eine P2S-VPN-Verbindung ist nützlich, wenn Sie an einem Remotestandort (beispielsweise bei der Telearbeit zu Hause oder in einer Konferenz) eine Verbindung mit Ihrem VNET herstellen möchten. Wenn nur einige wenige Clients eine Verbindung mit einem VNET herstellen müssen, ist ein P2S-VPN (und nicht ein S2S-VPN) ebenfalls eine nützliche Lösung. Eine P2S-Verbindung wird hergestellt, indem Sie die Verbindung vom Clientcomputer aus starten.

> [!IMPORTANT]
> Das klassische Bereitstellungsmodell unterstützt nur Windows-VPN-Clients und verwendet das Secure Socket Tunneling-Protokolls (SSTP), ein SSL-basiertes VPN-Protokoll. Zur Unterstützung anderer VPN-Clients muss Ihr VNET mit dem Ressourcen-Manager-Bereitstellungsmodell erstellt werden. Das Resource Manager-Bereitstellungsmodell unterstützt neben SSTP auch IKEv2-VPN. Weitere Informationen finden Sie unter [Informationen zu P2S-Verbindungen](point-to-site-about.md).
>
>

![P2S-Diagramm](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)


Für Point-to-Site-Verbindungen mit Zertifikatauthentifizierung wird Folgendes benötigt:

* Ein dynamisches VPN-Gateway.
* Der öffentliche Schlüssel (CER-Datei) für ein Stammzertifikat (in Azure hochgeladen). Dies wird als vertrauenswürdiges Zertifikat betrachtet und für die Authentifizierung verwendet.
* Ein Clientzertifikat, das über das Stammzertifikat generiert und auf jedem Clientcomputer installiert wurde, der eine Verbindung herstellen wird. Dieses Zertifikat wird für die Clientauthentifizierung verwendet.
* Ein VPN-Clientkonfigurationspaket muss generiert und auf jedem Clientcomputer installiert werden, der eine Verbindung herstellen wird. Das Clientkonfigurationspaket konfiguriert den nativen VPN-Client, der sich bereits im Betriebssystem befindet, mit den notwendigen Informationen für die Herstellung einer Verbindung mit dem VNET.

Point-to-Site-Verbindungen erfordern weder ein VPN-Gerät noch eine lokale öffentliche IP-Adresse. Die VPN-Verbindung wird über das Secure Socket Tunneling-Protokoll (SSTP) hergestellt. Auf Serverseite werden die SSTP-Versionen 1.0, 1.1 und 1.2 unterstützt. Der Client entscheidet, welche Version verwendet wird. Unter Windows 8.1 und höher wird standardmäßig SSTP 1.2 verwendet. 

Weitere Informationen zu Point-to-Site-Verbindungen finden Sie unter [Point-to-Site – Häufig gestellte Fragen](#faq) am Ende dieses Artikels.

### <a name="example-settings"></a>Beispieleinstellungen

Sie können die folgenden Werte zum Erstellen einer Testumgebung oder zum besseren Verständnis der Beispiele in diesem Artikel nutzen:

* **Name: VNet1**
* **Adressraum: 192.168.0.0/16**<br>In diesem Beispiel verwenden wir nur einen einzelnen Adressraum. Sie können für Ihr VNet aber auch mehrere Adressräume verwenden.
* **Subnetzname: FrontEnd**
* **Subnetzadressbereich: 192.168.1.0/24**
* **Abonnement:** Falls Sie über mehrere Abonnements verfügen, vergewissern Sie sich, dass Sie das richtige Abonnement verwenden.
* **Ressourcengruppe: TestRG**
* **Standort: USA, Osten**
* **Verbindungstyp: Point-to-Site**
* **Clientadressraum: 172.16.201.0/24**. VPN-Clients, die über diese Point-to-Site-Verbindung eine Verbindung mit dem VNet herstellen, erhalten eine IP-Adresse aus dem angegebenen Pool.
* **GatewaySubnet: 192.168.200.0/24**. Das Gatewaysubnetz muss den Namen „GatewaySubnet“ haben.
* **Größe:** Wählen Sie die gewünschte Gateway-SKU aus.
* **Routingtyp: Dynamisch**

## <a name="vnetvpn"></a>1. Erstellen eines virtuelles Netzwerks und eines VPN-Gateways

Stellen Sie zunächst sicher, dass Sie über ein Azure-Abonnement verfügen. Wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial) registrieren.

### <a name="createvnet"></a>Teil 1: Erstellen eines virtuellen Netzwerks

Falls Sie noch nicht über ein virtuelles Netzwerk verfügen, erstellen Sie eines. Die Screenshots dienen lediglich zur Veranschaulichung. Achten Sie darauf, dass Sie die Werte durch Ihre eigenen Werte ersetzen. Gehen Sie wie folgt vor, um ein VNet über das Azure-Portal zu erstellen:

1. Navigieren Sie in einem Browser zum [Azure-Portal](http://portal.azure.com) , und melden Sie sich, falls erforderlich, mit Ihrem Azure-Konto an.
2. Klicken Sie auf **Neu**. Geben Sie im Feld **Marketplace durchsuchen** die Zeichenfolge „Virtual Network“ ein. Klicken Sie in der zurückgegebenen Liste auf **Virtual Network**, um die Seite **Virtual Network** zu öffnen.

  ![Seite für die Suche nach einem virtuellen Netzwerk](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvnetportal700.png)
3. Wählen Sie im unteren Bereich der Seite „Virtual Network“ in der Liste **Bereitstellungsmodell auswählen** die Option **Klassisch** aus, und klicken Sie dann auf **Erstellen**.

  ![Bereitstellungsmodell auswählen](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/selectmodel.png)
4. Konfigurieren Sie auf der Seite **Virtuelles Netzwerk erstellen** die VNET-Einstellungen. Auf dieser Seite fügen Sie Ihren ersten Adressraum und einen Adressbereich des Subnetzes hinzu. Nachdem Sie die Erstellung des VNet abgeschlossen haben, können Sie zurückgehen und weitere Subnetze und Adressräume hinzufügen.

  ![Seite „Virtuelles Netzwerk erstellen“](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)
5. Überprüfen Sie, ob es sich um das richtige **Abonnement** handelt. Das Abonnement kann über die Dropdownliste geändert werden.
6. Klicken Sie auf **Ressourcengruppe** , und wählen Sie entweder eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue, indem Sie einen Namen für die neue Ressourcengruppe eingeben. Falls Sie eine neue Ressourcengruppe erstellen, geben Sie ihr einen Namen, der zu den geplanten Konfigurationswerten passt. Weitere Informationen zu Ressourcengruppen finden Sie unter [Übersicht über Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. Wählen Sie als Nächstes für das VNet die Einstellungen für **Standort** aus. Der Standort gibt an, wo sich die in diesem VNet bereitgestellten Ressourcen befinden.
8. Wählen Sie **An Dashboard anheften** aus, wenn das VNET komfortabel auf dem Dashboard zur Verfügung stehen soll, und klicken Sie dann auf **Erstellen**.

  ![An Dashboard anheften](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/pintodashboard150.png)
9. Nach dem Klicken auf „Erstellen“ wird auf dem Dashboard eine Kachel mit dem Status des VNETs angezeigt. Die Kachel verändert sich, wenn das VNet erstellt wird.

  ![Kachel "Erstellen eines virtuellen Netzwerks"](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png)
10. Sobald Ihr virtuelles Netzwerk erstellt wurde, wird **Erstellt** angezeigt.
11. Fügen Sie einen DNS-Server hinzu (optional). Nach der Erstellung des virtuellen Netzwerks können Sie für die Namensauflösung die IP-Adresse eines DNS-Servers hinzufügen. Bei der angegebenen IP-Adresse des DNS-Servers muss es sich um die Adresse eines DNS-Servers handeln, der die Namen für die Ressourcen in Ihrem VNET auflösen kann.<br>Öffnen Sie zum Hinzufügen eines DNS-Servers die Einstellungen für Ihr virtuelles Netzwerk, klicken Sie auf „DNS-Server“, und fügen Sie die IP-Adresse des gewünschten DNS-Servers hinzu.

### <a name="gateway"></a>Teil 2: Erstellen eines Gatewaysubnetzes und eines Gateways mit dynamischem Routing

In diesem Schritt erstellen Sie ein Gatewaysubnetz und ein Gateway mit dynamischem Routing. Im Azure-Portal für das klassische Bereitstellungsmodell können Gatewaysubnetz und Gateway über die gleichen Konfigurationsseiten erstellt werden.

1. Navigieren Sie im Portal zu dem virtuellen Netzwerk, für das Sie ein Gateway erstellen möchten.
2. Klicken Sie auf der Seite für Ihr virtuelles Netzwerk auf der Seite **Übersicht** im Abschnitt mit den VPN-Verbindungen auf **Gateway**.

  ![Klicken zum Erstellen eines Gateways](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. Wählen Sie auf der Seite **Neue VPN-Verbindung** die Option **Punkt-zu-Standort** aus.

  ![Point-to-Site-Verbindungstyp](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. Fügen Sie unter **Clientadressraum** den IP-Adressbereich hinzu. Hierbei handelt es sich um den Bereich, aus dem die VPN-Clients bei der Verbindungsherstellung eine IP-Adresse erhalten. Verwenden Sie einen privaten IP-Adressbereich, der sich nicht mit dem lokalen Standort überschneidet, aus dem Sie Verbindungen herstellen möchten. Der Bereich darf sich auch nicht mit dem VNET überschneiden, mit dem Sie Verbindungen herstellen möchten. Sie können den automatisch ausgefüllten Bereich löschen und dann den privaten IP-Adressbereich hinzufügen, den Sie verwenden möchten.

  ![Clientadressraum](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. Aktivieren Sie das Kontrollkästchen **Gateway sofort erstellen**.

  ![Gateway sofort erstellen](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/creategwimm.png)
6. Klicken Sie auf **Optionale Gatewaykonfiguration**, um die Seite **Gatewaykonfiguration** zu öffnen.

  ![Klicken Sie auf „Optionale Gatewaykonfiguration“.](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)
7. Klicken Sie auf **Subnetz > Erforderliche Einstellungen konfigurieren**, um das **Gatewaysubnetz** hinzuzufügen. Es ist zwar möglich, ein Gatewaysubnetz von /29 zu erstellen, es wird jedoch empfohlen, ein größeres Subnetz mit mehr Adressen zu erstellen und mindestens /28 oder /27 auszuwählen. Dadurch steht eine ausreichend hohe Anzahl von Adressen für mögliche zusätzliche Konfigurationen zur Verfügung, die Sie zukünftig vielleicht benötigen. Vermeiden Sie bei der Verwendung von Gatewaysubnetzen die Zuordnung einer Netzwerksicherheitsgruppe (NSG) zum Gatewaysubnetz. Das Zuordnen einer Netzwerksicherheitsgruppe zu diesem Subnetz kann dazu führen, dass das VPN-Gateway nicht mehr wie erwartet funktioniert.

  ![Hinzufügen von „GatewaySubnet“](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
8. Wählen Sie die Gatewaygröße **aus** . Bei der Größe handelt es sich um die Gateway-SKU für Ihr virtuelles Netzwerkgateway. Im Portal ist standardmäßig die SKU **Basic** ausgewählt. Weitere Informationen zu Gateway-SKUs finden Sie unter [Informationen zu VPN Gateway-Einstellungen](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

  ![Gatewaygröße](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
9. Wählen Sie den **Routingtyp** für Ihr Gateway aus. Für P2S-Konfigurationen wird der Routingtyp **Dynamisch** benötigt. Klicken Sie abschließend auf **OK**.

  ![Konfigurieren des Routingtyps](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)
10. Klicken Sie am unteren Rand der Seite **Neue VPN-Verbindung** auf **OK**, um mit der Erstellung Ihres Gateways für virtuelle Netzwerke zu beginnen. Je nach ausgewählter Gateway-SKU kann die Erstellung eines VPN-Gateways bis zu 45 Minuten dauern.

## <a name="generatecerts"></a>2. Erstellen von Zertifikaten

Zertifikate werden von Azure zur Authentifizierung von VPN-Clients für Point-to-Site-VPNs verwendet. Sie laden die Informationen des öffentlichen Schlüssels des Stammzertifikats in Azure hoch. Der öffentliche Schlüssel wird dann als „vertrauenswürdig“ betrachtet. Clientzertifikate müssen über das vertrauenswürdige Stammzertifikat erstellt und dann auf jedem Clientcomputer installiert werden, der sich im persönlichen Zertifikatspeicher des aktuellen Benutzers befindet. Mit diesem Zertifikat wird der Client authentifiziert, wenn er eine Verbindung mit dem VNET initiiert. 

Wenn Sie selbstsignierte Zertifikate verwenden, müssen diese anhand bestimmter Parameter erstellt werden. Sie können ein selbstsigniertes Zertifikat anhand der Anweisungen für [PowerShell und Windows 10](vpn-gateway-certificates-point-to-site.md) oder [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) erstellen. Führen Sie unbedingt die Schritte in diesen Anweisungen aus, wenn Sie mit selbstsignierten Stammzertifikaten arbeiten und Clientzertifikate aus dem selbstsignierten Stammzertifikat erstellen. Andernfalls sind die von Ihnen erstellten Zertifikate nicht mit P2S-Verbindungen kompatibel, und Ihnen wird ein Verbindungsfehler angezeigt.

### <a name="cer"></a>Teil 1: Beschaffen des öffentlichen Schlüssels (CER-Format) für das Stammzertifikat

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="genclientcert"></a>Teil 2: Generieren eines Clientzertifikats

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload"></a>3. Hochladen der CER-Datei des Stammzertifikats

Nachdem das Gateway erstellt wurde, laden Sie die CER-Datei (mit den Informationen zum öffentlichen Schlüssel) für ein vertrauenswürdiges Stammzertifikat in Azure hoch. Der private Schlüssel für das Stammzertifikat wird nicht in Azure hochgeladen. Nach dem Hochladen der CER-Datei kann Azure die Datei verwenden, um Clients zu authentifizieren, auf denen ein aus dem vertrauenswürdigen Stammzertifikat generiertes Clientzertifikat installiert ist. Sie können später bei Bedarf weitere vertrauenswürdige Stammzertifikate hochladen – bis zu 20 insgesamt.  

1. Klicken Sie auf dem Blatt für Ihr VNET im Abschnitt **VPN-Verbindungen** auf die **Clientgrafik**, um die Seite **Punkt-zu-Standort-VPN-Verbindung** zu öffnen.

  ![Clients](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. Klicken Sie auf der Seite **Punkt-zu-Standort-Verbindung** auf **Zertifikate verwalten**, um die Seite **Zertifikate** zu öffnen.<br>

  ![Seite "Zertifikate"](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. Klicken Sie auf der Seite **Zertifikate** auf **Hochladen**, um die Seite **Zertifikat hochladen** zu öffnen.<br>

    ![Seite zum Hochladen von Zertifikaten](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)<br>
4. Klicken Sie auf die Ordnergrafik, um zur CER-Datei zu navigieren. Wählen Sie die Datei aus, und klicken Sie anschließend auf **OK**. Aktualisieren Sie die Seite, damit das hochgeladene Zertifikat auf der Seite **Zertifikate** angezeigt wird.

  ![Hochladen des Zertifikats](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)<br>

## <a name="vpnclientconfig"></a>4. Konfigurieren des Clients

Um über ein Point-to-Site-VPN eine Verbindung mit einem VNET herstellen zu können, muss auf jedem Client ein Paket zum Konfigurieren des nativen Windows VPN-Clients installiert werden. Das Konfigurationspaket konfiguriert den nativen Windows-VPN-Client mit den Einstellungen, die zum Herstellen einer Verbindung mit dem virtuellen Netzwerk erforderlich sind.

Sie können auf jedem Clientcomputer das gleiche VPN-Clientkonfigurationspaket verwenden – vorausgesetzt, es handelt sich dabei um die passende Version für die Architektur des jeweiligen Clients. Die Liste mit den unterstützten Clientbetriebssystemen finden Sie unter [Point-to-Site – Häufig gestellte Fragen](#faq) am Ende dieses Artikels.

### <a name="generateconfigpackage"></a>Teil 1: Generieren und Installieren des Konfigurationspakets für VPN-Clients

1. Klicken Sie im Azure-Portal auf der Seite **Übersicht** für Ihr VNET im Abschnitt **VPN-Verbindungen** auf die Clientgrafik, um die Seite **Punkt-zu-Standort-VPN-Verbindung** zu öffnen.
2. Klicken Sie im oberen Bereich der Seite **Punkt-zu-Standort-VPN-Verbindung** auf das Downloadpaket für das Clientbetriebssystem, unter dem es installiert wird:

  * Wählen Sie für 64-Bit-Clients das Paket **VPN-Client (64 Bit)** aus.
  * Wählen Sie für 32-Bit-Clients das Paket **VPN-Client (32 Bit)** aus.

  ![Herunterladen des VPN-Clientkonfigurationspakets](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)<br>
3. Laden Sie das generierte Paket herunter, und installieren Sie es auf Ihrem Clientcomputer. Sollte ein SmartScreen-Popup erscheinen, klicken Sie auf **Weitere Informationen** und anschließend auf **Trotzdem ausführen**. Sie können das Paket auch speichern und auf anderen Clientcomputern installieren.

### <a name="installclientcert"></a>Teil 2: Installieren des Clientzertifikats

Wenn Sie eine P2S-Verbindung mit einem anderen Clientcomputer als dem für die Generierung der Clientzertifikate verwendeten Computer herstellen möchten, müssen Sie ein Clientzertifikat installieren. Beim Installieren eines Clientzertifikats benötigen Sie das Kennwort, das beim Exportieren des Clientzertifikats erstellt wurde. Normalerweise müssen Sie hierfür nur auf das Zertifikat doppelklicken und dann die Installation durchführen. Weitere Informationen finden Sie unter [Installieren eines exportierten Clientzertifikats](vpn-gateway-certificates-point-to-site.md#install).

## <a name="connect"></a>5. Herstellen einer Verbindung mit Azure

### <a name="connect-to-your-vnet"></a>Herstellen der Verbindung mit Ihrem VNet

1. Um eine Verbindung mit Ihrem VNet herzustellen, navigieren Sie auf dem Clientcomputer zu „VPN-Verbindungen“ und suchen nach der VPN-Verbindung, die Sie erstellt haben. Sie hat den gleichen Namen wie das virtuelle Netzwerk. Klicken Sie auf **Verbinden**. Möglicherweise wird eine Popupmeldung angezeigt, die sich auf die Verwendung des Zertifikats bezieht. Klicken Sie in diesem Fall auf **Weiter** , um erhöhte Rechte zu verwenden.
2. Klicken Sie auf der Statusseite **Verbindung** auf **Verbinden**, um die Verbindung herzustellen. Wenn der Bildschirm **Zertifikat auswählen** angezeigt wird, vergewissern Sie sich, dass das angezeigte Clientzertifikat dem Zertifikat entspricht, die Sie zum Herstellen der Verbindung verwenden möchten. Wenn dies nicht der Fall ist, verwenden Sie den Dropdownpfeil, um das richtige Zertifikat auszuwählen, und klicken Sie dann auf **OK**.

  ![VPN-Clientverbindung](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientconnect.png)
3. Die Verbindung wurde hergestellt.

  ![Verbindung hergestellt](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/connected.png)

#### <a name="troubleshooting-p2s-connections"></a>Problembehandlung für P2S-Verbindungen

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="verifyvpnconnect"></a>Überprüfen der VPN-Verbindung

1. Um sicherzustellen, dass die VPN-Verbindung aktiv ist, öffnen Sie eine Eingabeaufforderung mit Administratorrechten, und führen Sie *Ipconfig/all*aus.
2. Zeigen Sie die Ergebnisse an. Beachten Sie, dass die IP-Adresse, die Sie erhalten, eine Adresse aus dem Adressbereich der P2S-Verbindung ist, den Sie beim Erstellen des virtuellen Netzwerks angegeben haben. Die Ergebnisse sollten in etwa wie folgt aussehen:

  ```
    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled
  ```

## <a name="connectVM"></a>Herstellen einer Verbindung mit einem virtuellem Computer

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="add"></a>Hinzufügen oder Entfernen vertrauenswürdiger Stammzertifikate

Sie können vertrauenswürdige Stammzertifikate hinzufügen und aus Azure entfernen. Wenn Sie ein Stammzertifikat entfernen, können Clients, für die über diesen Stamm ein Zertifikat generiert wurde, nicht authentifiziert werden und somit auch keine Verbindung herstellen. Wenn Sie für einen Client die Authentifizierung und Verbindungsherstellung durchführen möchten, müssen Sie ein neues Clientzertifikat installieren, das aus einem für Azure vertrauenswürdigen (hochgeladenen) Stammzertifikat generiert wurde.

### <a name="addtrustedroot"></a>So fügen Sie ein vertrauenswürdiges Stammzertifikat hinzu

Sie können Azure bis zu 20 vertrauenswürdige CER-Stammzertifikatdateien hinzufügen. Eine entsprechende Anleitung finden Sie in [Abschnitt 3: Hochladen der CER-Datei des Stammzertifikats](#upload).

### <a name="removetrustedroot"></a>So entfernen Sie ein vertrauenswürdiges Stammzertifikat

1. Klicken Sie auf dem Blatt für Ihr VNET im Abschnitt **VPN-Verbindungen** auf die **Clientgrafik**, um die Seite **Punkt-zu-Standort-VPN-Verbindung** zu öffnen.

  ![Clients](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. Klicken Sie auf der Seite **Punkt-zu-Standort-Verbindung** auf **Zertifikate verwalten**, um die Seite **Zertifikate** zu öffnen.<br>

  ![Seite "Zertifikate"](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. Klicken Sie auf der Seite **Zertifikate** neben dem Zertifikat, das Sie entfernen möchten, auf die Auslassungspunkte, und klicken Sie anschließend auf **Löschen**.

  ![Löschen eines Stammzertifikats](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)<br>

## <a name="revokeclient"></a>Sperren eines Clientzertifikats

Sie können Clientzertifikate sperren. Anhand der Zertifikatsperrliste können Sie basierend auf einzelnen Clientzertifikaten selektiv Punkt-zu-Standort-Verbindungen verweigern. Das ist nicht dasselbe wie das Entfernen eines vertrauenswürdigen Stammzertifikats. Wenn Sie ein vertrauenswürdiges Stammzertifikat (CER-Datei) aus Azure entfernen, wird der Zugriff für alle Clientzertifikate gesperrt, die mit dem gesperrten Stammzertifikat generiert oder signiert wurden. Wenn Sie anstelle des Stammzertifikats ein Clientzertifikat sperren, können die anderen Zertifikate, die auf der Grundlage des Stammzertifikats generiert wurden, weiterhin zur Authentifizierung für die P2S-Verbindung verwendet werden.

Üblicherweise wird das Stammzertifikat zum Verwalten des Zugriffs auf Team- oder Organisationsebene verwendet. Eine genauer abgestufte Steuerung des Zugriffs für einzelne Benutzer erfolgt hingegen mit gesperrten Clientzertifikaten.

### <a name="revokeclientcert"></a>So sperren Sie ein Clientzertifikat

Sie können ein Clientzertifikat sperren, indem Sie den Fingerabdruck der Sperrliste hinzufügen.

1. Rufen Sie den Fingerabdruck des Clientzertifikats ab. Weitere Informationen finden Sie unter [Vorgehensweise: Abrufen des Fingerabdrucks eines Zertifikats](https://msdn.microsoft.com/library/ms734695.aspx).
2. Kopieren Sie ihn in einen Text-Editor, und entfernen Sie alle Leerzeichen, sodass eine fortlaufende Zeichenfolge entsteht.
3. Navigieren Sie zu **„Name des klassischen virtuellen Netzwerks“ > Punkt-zu-Standort-VPN-Verbindung > Zertifikate**, und klicken Sie anschließend auf **Sperrliste**, um die Seite mit der Sperrliste zu öffnen. 
4. Klicken Sie auf der Seite **Sperrliste** auf **+Zertifikat hinzufügen**, um die Seite **Zertifikat der Sperrliste hinzufügen** zu öffnen.
5. Fügen Sie auf der Seite **Zertifikat der Sperrliste hinzufügen** den Zertifikatfingerabdruck als durchgehende Textzeile (ohne Leerzeichen) ein. Klicken Sie unten auf der Seite auf **OK**.
6. Nach Abschluss der Aktualisierung kann das Zertifikat nicht mehr für die Verbindungsherstellung verwendet werden. Clients, die versuchen, unter Verwendung dieses Zertifikats eine Verbindung herzustellen, erhalten eine Meldung mit dem Hinweis, dass das Zertifikat nicht mehr gültig ist.

## <a name="faq"></a>Point-to-Site – Häufig gestellte Fragen

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-include.md)]

## <a name="next-steps"></a>Nächste Schritte
Sobald die Verbindung hergestellt ist, können Sie Ihren virtuellen Netzwerken virtuelle Computer hinzufügen. Weitere Informationen finden Sie unter [Virtuelle Computer](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) . Weitere Informationen zu Netzwerken und virtuellen Computern finden Sie unter [Azure- und Linux-VM-Netzwerke (Übersicht)](../virtual-machines/linux/azure-vm-network-overview.md).
