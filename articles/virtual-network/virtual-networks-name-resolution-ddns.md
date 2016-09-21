<properties
   pageTitle="Registrieren von Hostnamen mithilfe von dynamischem DNS"
   description="Hier wird erläutert, wie Sie Dynamic DNS einrichten, um Hostnamen in Ihren eigenen DNS-Servern zu registrieren."
   services="dns"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="" />
<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="sewhee" />

# Registrieren von Hostnamen mithilfe von dynamischem DNS auf Ihrem eigenen DNS-Server

[Azure bietet eine Namensauflösung](virtual-networks-name-resolution-for-vms-and-role-instances.md) für virtuelle Maschinen (VMs) und Rolleninstanzen. Wenn Ihre Namensauflösung jedoch mehr leisten muss, als die Azure-Namensauflösung ermöglicht, können Sie eigene DNS-Server bereitstellen. Auf diese Weise können Sie eine maßgeschneiderte DNS-Lösung für Ihre speziellen Anforderungen erstellen. Beispielsweise kann es vorkommen, dass Sie eine DNS-Lösung für den Zugriff auf Ihren lokalen Active Directory-Domänencontroller benötigen.

Wenn Ihre benutzerdefinierten DNS-Server als virtuelle Azure-Computer gehostet werden, können Sie Abfragen des Hostnamens für das gleiche VNET in Azure zum Auflösen von Hostnamen weiterleiten. Wenn Sie nicht auf diese Weise vorgehen möchten, können Sie die Hostnamen Ihrer virtuellen Computer über Dynamic DNS in Ihrem DNS-Server registrieren. Azure verfügt nicht über die Mittel (z. B. Anmeldeinformationen), um Einträge direkt in Ihren DNS-Servern zu erstellen. Daher sind meist andere Vorkehrungen erforderlich. Hier sind einige allgemeinen Szenarien mit Alternativen angegeben.

## Windows-Clients

Nicht per Beitritt in eine Domäne eingebundene Windows-Clients versuchen beim Starten oder bei IP-Adressänderungen, ungesicherte DDNS-Aktualisierungen (Dynamic DNS, dynamisches DNS) vorzunehmen. Der DNS-Name besteht aus dem Hostnamen und dem primären DNS-Suffix. In Azure wird das primäre DNS-Suffix leer gelassen, Sie können es jedoch im virtuellen Computer über die [Benutzeroberfläche](https://technet.microsoft.com/library/cc794784.aspx) oder [durch Automatisierung](https://social.technet.microsoft.com/forums/windowsserver/3720415a-6a9a-4bca-aa2a-6df58a1a47d7/change-primary-dns-suffix) festlegen.

Per Beitritt in die Domäne eingebundene Windows-Clients registrieren ihre IP-Adressen mithilfe von sicherem Dynamic DNS beim Domänencontroller. Während des Domänenbeitritts wird das primäre DNS-Suffix auf dem Client festgelegt, und die Vertrauensstellung wird erstellt und verwaltet.

## Linux-Clients

Linux-Clients registrieren sich beim Start in der Regel nicht selbst beim DNS-Server. Es wird davon ausgegangen, dass der DHCP-Server dies übernimmt. Die DHCP-Server von Azure verfügen weder über die Funktion noch über die Anmeldeinformationen, um Datensätze im DNS-Server zu registrieren. Sie können ein Tool mit dem Namen *nsupdate* verwenden, das im Bind-Paket enthalten ist, um Dynamic DNS-Updates zu senden. Da das Dynamic DNS-Protokoll standardisiert ist, können Sie *nsupdate* auch dann verwenden, wenn Sie Bind nicht auf dem DNS-Server nutzen.

Sie können die vom DHCP-Client bereitgestellten Hooks verwenden, um den Hostnameneintrag im DNS-Server zu erstellen und zu verwalten. Während des DHCP-Zyklus führt der Client die Skripts in */etc/dhcp/dhclient-exit-hooks.d/* aus. Diese Vorgehensweise kann genutzt werden, um die neue IP-Adresse mit *nsupdate* zu registrieren. Beispiel:

        #!/bin/sh
        requireddomain=mydomain.local

        # only execute on the primary nic
        if [ "$interface" != "eth0" ]
        then
            return
        fi

        # when we have a new IP, perform nsupdate
        if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] ||
           [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
        then
            host=`hostname`
            nsupdatecmds=/var/tmp/nsupdatecmds
              echo "update delete $host.$requireddomain a" > $nsupdatecmds
              echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
              echo "send" >> $nsupdatecmds

              nsupdate $nsupdatecmds
        fi

        #done
        exit 0;

Sie können den Befehl *nsupdate* auch verwenden, um sichere Dynamic DNS-Updates durchzuführen. Wenn Sie beispielsweise einen Bind-DNS-Server nutzen, wird ein Schlüsselpaar aus öffentlichem und privatem Schlüssel [generiert](http://linux.yyz.us/nsupdate/). Der DNS-Server wird mit dem öffentlichen Teil des Schlüssels [konfiguriert](http://linux.yyz.us/dns/ddns-server.html), damit die Signatur der Anforderung überprüft werden kann. Sie müssen die Option *-k* verwenden, um das Schlüsselpaar für *nsupdate* bereitzustellen, damit die Dynamic DNS-Updateanforderung signiert werden kann.

Wenn Sie einen Windows-DNS-Server nutzen, können Sie die Kerberos-Authentifizierung mit dem Parameter *-g* in *nsupdate* verwenden (nicht verfügbar in der Windows-Version von *nsupdate*). Verwenden Sie hierfür *kinit*, um die Anmeldeinformationen zu laden (z. B. aus einer [keytab-Datei](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)). Mit *nsupdate -g* werden die Anmeldeinformationen dann aus dem Cache geladen.

Bei Bedarf können Sie Ihren VMs ein Suffix für die DNS-Suche hinzufügen. Das DNS-Suffix wird in der Datei */etc/resolv.conf* angegeben. In den meisten Linux-Distributionen wird der Inhalt dieser Datei automatisch verwaltet. Daher lässt sie sich in der Regel nicht bearbeiten. Sie können das Suffix aber außer Kraft setzen, indem Sie den Befehl *supersede* des DHCP-Clients verwenden. Fügen Sie in */etc/dhcp/dhclient.conf* hierfür Folgendes hinzu:

        supersede domain-name <required-dns-suffix>;

<!---HONumber=AcomDC_0907_2016-->