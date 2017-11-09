---
title: "Bereitstellen des Connected Factory-Gateways – Azure | Microsoft-Dokumentation"
description: "Hier erfahren Sie, wie ein Gateway unter Windows oder Linux bereitgestellt wird, um Verbindungen mit der vorkonfigurierten Connected Factory-Lösung zu ermöglichen."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: dobett
ms.openlocfilehash: e99a7bc34ac5ed060100e5f5032513bf4b18b2eb
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2017
---
# <a name="deploy-a-gateway-on-windows-or-linux-for-the-connected-factory-preconfigured-solution"></a>Bereitstellen eines Gateways unter Windows oder Linux für die vorkonfigurierte Connected Factory-Lösung

Die Software, die für die Bereitstellung eines Gateways für die vorkonfigurierte Connected Factory-Lösung erforderlich ist, besteht aus zwei Komponenten:

* Der *OPC-Proxy* stellt eine Verbindung mit IoT Hub her. Der *OPC-Proxy* wartet dann auf Befehls- und Kontrollmeldungen vom integrierten OPC-Browser, der im Portal der Connected Factory-Lösung ausgeführt wird.
* *OPC Publisher* stellt eine Verbindung mit lokalen OPC UA-Servern her und leitet von diesen Telemetriemeldungen an IoT Hub weiter.

Beide Komponenten sind Open-Source-Lösungen, die als Quelle auf GitHub und als Docker-Container verfügbar sind:

| GitHub | DockerHub |
| ------ | --------- |
| [OPC Publisher][lnk-publisher-github] | [OPC Publisher][lnk-publisher-docker] |
| [OPC Proxy][lnk-proxy-github] | [OPC Proxy][lnk-proxy-docker] |

Für keine der beiden Komponenten ist eine öffentlich erreichbare IP-Adresse oder eine Lücke in der Gateway-Firewall erforderlich. OPC Proxy und OPC Publisher nutzen nur die ausgehenden Ports 443, 5671 und 8883.

Die Schritte in diesem Artikel veranschaulichen, wie ein Gateway mit Docker unter [Windows](#windows-deployment) oder [Linux](#linux-deployment) bereitgestellt wird. Das Gateway ermöglicht Verbindungen mit der vorkonfigurierten Connected Factory-Lösung.

> [!NOTE]
> Die Gatewaysoftware, die im Docker-Container ausgeführt wird, ist [Azure IoT Edge].

## <a name="windows-deployment"></a>Windows-Bereitstellung

> [!NOTE]
> Falls Sie noch kein Gatewaygerät haben, empfiehlt Microsoft, ein kommerzielles Gateway von einem unserer Partner zu kaufen. Im [Katalog der Azure IoT-Geräte] finden Sie eine Liste der Gatewaygeräte, die mit der Connected Factory-Lösung kompatibel sind. Befolgen Sie die für das Gerät geltenden Anweisungen, um das Gateway einzurichten. Alternativ können Sie folgendermaßen vorgehen, um eines Ihrer vorhandenen Gateways manuell einzurichten.

### <a name="install-docker"></a>Installieren von Docker

Installieren Sie [Docker für Windows] auf Ihrem Windows-basierten Gatewaygerät. Wählen Sie während der Installation von Docker für Windows ein Laufwerk auf dem Hostcomputer aus, das für Docker freigegeben werden soll. Der folgende Screenshot zeigt das Freigeben von Laufwerk D auf Ihrem Windows-System:

![Installieren von Docker][img-install-docker]

Erstellen Sie dann einen Ordner namens **docker** im Stammverzeichnis des freigegebenen Laufwerks.
Sie können diesen Schritt auch nach der Installation von Docker über das Menü **Einstellungen** ausführen.

### <a name="configure-the-gateway"></a>Konfigurieren des Gateways

1. Sie benötigen die **iothubowner**-Verbindungszeichenfolge Ihrer Connected Factory-Bereitstellung der Azure IoT Suite, um die Gatewaybereitstellung abzuschließen. Navigieren Sie im [Azure-Portal] zu Ihrem IoT Hub in der Ressourcengruppe, die bei der Bereitstellung der Connected Factory-Lösung erstellt wurde. Klicken Sie auf **SAS-Richtlinien**, um auf die **iothubowner**-Verbindungszeichenfolge zuzugreifen:

    ![Suchen der IoT Hub-Verbindungszeichenfolge][img-hub-connection]

    Kopieren Sie den Wert von **Verbindungszeichenfolge – Primärschlüssel**.

1. Konfigurieren Sie das Gateway für Ihren IoT Hub, indem Sie die zwei Gatewaymodule **einmal** mit folgendem Befehl über eine Eingabeaufforderung ausführen:

    `docker run -it --rm -h <ApplicationName> -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v //D/docker:/root/.dotnet/corefx/cryptography/x509stores microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName> "<IoTHubOwnerConnectionString>"`

    `docker run -it --rm -v //D/docker:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db`

    * **&lt;ApplicationName&gt;** ist der Name, den Sie Ihrem OPC UA Publisher im Format **publisher.&lt;Ihr vollqualifizierter Domänenname&gt;** geben. Wenn Ihr Factory-Netzwerk beispielsweise den Namen **myfactorynetwork.com** hat, weist **ApplicationName** den Wert **publisher.myfactorynetwork.com** auf.
    * **&lt;IoTHubOwnerConnectionString&gt;** ist die **iothubowner**-Verbindungszeichenfolge, die Sie im vorherigen Schritt kopiert haben. Diese Verbindungszeichenfolge wird nur in diesem Schritt verwendet. Für die folgenden Schritte ist diese nicht erforderlich:

    Verwenden Sie den zugeordneten Ordner „D:\\docker“ (das `-v`-Argument) später, um die beiden X.509-Zertifikate zu speichern, die von den Gatewaymodulen verwendet werden.

### <a name="run-the-gateway"></a>Ausführen des Gateways

1. Starten Sie das Gateway mit den folgenden Befehlen neu:

    `docker run -it --rm -h <ApplicationName> --expose 62222 -p 62222:62222 -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/Logs -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v //D/docker:/shared -v //D/docker:/root/.dotnet/corefx/cryptography/x509stores -e _GW_PNFP="/shared/publishednodes.JSON" microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName>`

    `docker run -it --rm -v //D/docker:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -D /mapped/cs.db`

1. Aus Gründen der Sicherheit enthalten die beiden X.509-Zertifikate, die im Ordner „D:\\docker“ gespeichert werden, den privaten Schlüssel. Beschränken Sie den Zugriff auf diesen Ordner mit den Anmeldeinformationen (in der Regel **Administratoren**), die Sie zum Ausführen des Docker-Containers verwenden. Klicken Sie mit der rechten Maustaste auf den Ordner „D:\\docker“, und wählen Sie **Eigenschaften**, dann **Sicherheit** und anschließend **Bearbeiten** aus. Gewähren Sie **Administratoren** Vollzugriff, und entfernen Sie alle anderen Benutzer:

    ![Erteilen von Berechtigungen für die Docker-Freigabe][img-docker-share]

1. Überprüfen Sie die Netzwerkkonnektivität. Geben Sie an einer Eingabeaufforderung den Befehl `ping publisher.<your fully qualified domain name>` ein, um Ihr Gateway zu pingen. Wenn das Ziel nicht erreichbar ist, fügen Sie die IP-Adresse und den Namen Ihres Gateways der Datei „hosts“ auf Ihrem Gateway hinzu. Die Datei „hosts“ befindet sich im Ordner **Windows\\System32\\drivers\\etc**.

1. Versuchen Sie danach, mithilfe eines lokalen OPC UA-Clients, der auf dem Gateway ausgeführt wird, eine Verbindung mit dem Herausgeber herzustellen. Die OPC UA-Endpunkt-URL lautet `opc.tcp://publisher.<your fully qualified domain name>:62222`. Wenn Sie nicht über einen OPC UA-Client verfügen, können Sie einen [OPC UA-Open-Source-Client] herunterladen und verwenden.

1. Wenn Sie diese lokalen Tests erfolgreich abgeschlossen haben, navigieren Sie zur Seite **Verbinden mit dem eigenen OPC UA-Server** im Portal der Connected Factory-Lösung. Geben Sie die Herausgeberendpunkt-URL (`tcp://publisher.<your fully qualified domain name>:62222`) ein, und klicken Sie auf **Verbinden**. Sie erhalten eine Zertifikatwarnung, klicken Sie dann auf **Fortsetzen**. Als Nächstes erhalten Sie eine Fehlermeldung, die besagt, dass der Herausgeber dem UA-Webclient nicht vertraut. Kopieren Sie zum Beheben dieses Fehlers das **UA-Webclientzertifikat** aus dem Ordner **D:\\docker\\Rejected Certificates\\certs** in den Ordner **D:\\docker\\UA Applications\\certs** auf dem Gateway. Sie müssen das Gateway nicht neu starten. Wiederholen Sie diesen Schritt. Sie können jetzt über die Cloud eine Verbindung mit dem Gateway herstellen, und Sie sind bereit, OPC UA-Server zur Lösung hinzuzufügen.

### <a name="add-your-opc-ua-servers"></a>Hinzufügen von OPC UA-Servern

1. Navigieren Sie zur Seite **Verbinden mit dem eigenen OPC UA-Server** im Portal der Connected Factory-Lösung. Führen Sie die gleichen Schritte wie im vorherigen Abschnitt aus, um eine Vertrauensstellung zwischen dem Connected Factory-Portal und dem OPC UA-Server herzustellen. Mit diesem Schritt wird eine gegenseitige Vertrauensstellung für die Zertifikate aus dem Connected Factory-Portal und dem OPC UA-Server eingerichtet und eine Verbindung erstellt.

1. Wechseln Sie zur OPC UA-Knotenstruktur Ihres OPC UA-Servers, klicken Sie mit der rechten Maustaste auf die OPC-Knoten, und wählen **Veröffentlichen** aus. Damit die Veröffentlichung auf diese Weise funktioniert, müssen sich der OPC UA-Server und der Herausgeber im selben Netzwerk befinden. Anders ausgedrückt: Wenn der vollqualifizierte Domänenname des Herausgebers **herausgeber.meinedomäne.com** ist, muss der vollqualifizierte Domänenname des OPC UA-Servers z.B. **meinopcuaserver.meinedomäne.com** sein. Wenn Ihr Setup davon abweicht, können Sie Knoten manuell zur Datei „publishesnodes.json“ im Ordner **D:\\docker** hinzufügen. Die Datei „publishesnodes.json“ wird automatisch beim ersten erfolgreichen Veröffentlichen eines OPC-Knotens generiert.

1. Telemetriedaten werden jetzt vom Gatewaygerät übertragen. Sie können die Telemetriedaten in der Ansicht **Factoryspeicherorte** des Connected Factory-Portals unter **Neue Factory** anzeigen.

## <a name="linux-deployment"></a>Linux-Bereitstellung

> [!NOTE]
> Falls Sie noch kein Gatewaygerät haben, empfiehlt Microsoft, ein kommerzielles Gateway von einem unserer Partner zu kaufen. Im [Katalog der Azure IoT-Geräte] finden Sie eine Liste der Gatewaygeräte, die mit der Connected Factory-Lösung kompatibel sind. Befolgen Sie die für das Gerät geltenden Anweisungen, um das Gateway einzurichten. Alternativ können Sie folgendermaßen vorgehen, um eines Ihrer vorhandenen Gateways manuell einzurichten.

[Installieren Sie Docker] auf Ihrem Linux-Gatewaygerät.

### <a name="configure-the-gateway"></a>Konfigurieren des Gateways

1. Sie benötigen die **iothubowner**-Verbindungszeichenfolge Ihrer Connected Factory-Bereitstellung der Azure IoT Suite, um die Gatewaybereitstellung abzuschließen. Navigieren Sie im [Azure-Portal] zu Ihrem IoT Hub in der Ressourcengruppe, die bei der Bereitstellung der Connected Factory-Lösung erstellt wurde. Klicken Sie auf **SAS-Richtlinien**, um auf die **iothubowner**-Verbindungszeichenfolge zuzugreifen:

    ![Suchen der IoT Hub-Verbindungszeichenfolge][img-hub-connection]

    Kopieren Sie den Wert von **Verbindungszeichenfolge – Primärschlüssel**.

1. Konfigurieren Sie das Gateway für Ihren IoT Hub, indem Sie die zwei Gatewaymodule **einmal** über eine Shell mit folgendem Befehl ausführen:

    `sudo docker run -it --rm -h <ApplicationName> -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/ -v /shared:/root/.dotnet/corefx/cryptography/x509stores microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName> "<IoTHubOwnerConnectionString>"`

    `sudo docker run --rm -it -v /shared:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db`

    * **&lt;ApplicationName&gt;** ist der Name der OPC UA-Anwendung, den das Gateway im Format **Herausgeber.&lt;Ihr vollqualifizierter Domänenname&gt;** erstellt. Beispiel: **herausgeber.microsoft.com**.
    * **&lt;IoTHubOwnerConnectionString&gt;** ist die **iothubowner**-Verbindungszeichenfolge, die Sie im vorherigen Schritt kopiert haben. Diese Verbindungszeichenfolge wird nur in diesem Schritt verwendet. Für die folgenden Schritte ist diese nicht erforderlich:

    Verwenden Sie den Ordner **/shared** (das `-v`-Argument) später, um die beiden X.509-Zertifikate zu speichern, die von den Gatewaymodulen verwendet werden.

### <a name="run-the-gateway"></a>Ausführen des Gateways

1. Starten Sie das Gateway mit den folgenden Befehlen neu:

    `sudo docker run -it -h <ApplicationName> --expose 62222 -p 62222:62222 --rm -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/Logs -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v /shared:/shared -v /shared:/root/.dotnet/corefx/cryptography/x509stores -e _GW_PNFP="/shared/publishednodes.JSON" microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName>`

    `sudo docker run -it -v /shared:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -D /mapped/cs.db`

1. Aus Gründen der Sicherheit enthalten die beiden X.509-Zertifikate, die im Ordner **/shared** gespeichert werden, den privaten Schlüssel. Beschränken Sie den Zugriff auf diesen Ordner mit den Anmeldeinformationen, die Sie zum Ausführen des Docker-Containers verwenden. Um die Berechtigungen ausschließlich für **root** festzulegen, verwenden Sie den `chmod`-Shellbefehl für den Ordner.

1. Überprüfen Sie die Netzwerkkonnektivität. Geben Sie über eine Shell den Befehl `ping publisher.<your fully qualified domain name>` ein, um Ihr Gateway zu pingen. Wenn das Ziel nicht erreichbar ist, fügen Sie die IP-Adresse und den Namen Ihres Gateways der Datei „hosts“ auf Ihrem Gateway hinzu. Die Datei „hosts“ befindet sich im Ordner **/etc**.

1. Versuchen Sie danach, mithilfe eines lokalen OPC UA-Clients, der auf dem Gateway ausgeführt wird, eine Verbindung mit dem Herausgeber herzustellen. Die OPC UA-Endpunkt-URL lautet `opc.tcp://publisher.<your fully qualified domain name>:62222`. Wenn Sie nicht über einen OPC UA-Client verfügen, können Sie einen [OPC UA-Open-Source-Client] herunterladen und verwenden.

1. Wenn Sie diese lokalen Tests erfolgreich abgeschlossen haben, navigieren Sie zur Seite **Verbinden mit dem eigenen OPC UA-Server** im Portal der Connected Factory-Lösung. Geben Sie die Herausgeberendpunkt-URL (`tcp://publisher.<your fully qualified domain name>:62222`) ein, und klicken Sie auf **Verbinden**. Sie erhalten eine Zertifikatwarnung, klicken Sie dann auf **Fortsetzen**. Als Nächstes erhalten Sie eine Fehlermeldung, die besagt, dass der Herausgeber dem UA-Webclient nicht vertraut. Kopieren Sie zum Beheben dieses Fehlers das **UA-Webclientzertifikat** aus dem Ordner **/shared/Rejected Certificates/certs** in den Ordner **/shared/UA Applications/certs** auf dem Gateway. Sie müssen das Gateway nicht neu starten. Wiederholen Sie diesen Schritt. Sie können jetzt über die Cloud eine Verbindung mit dem Gateway herstellen, und Sie sind bereit, OPC UA-Server zur Lösung hinzuzufügen.

### <a name="add-your-opc-ua-servers"></a>Hinzufügen von OPC UA-Servern

1. Navigieren Sie zur Seite **Verbinden mit dem eigenen OPC UA-Server** im Portal der Connected Factory-Lösung. Führen Sie die gleichen Schritte wie im vorherigen Abschnitt aus, um eine Vertrauensstellung zwischen dem Connected Factory-Portal und dem OPC UA-Server herzustellen. Mit diesem Schritt wird eine gegenseitige Vertrauensstellung für die Zertifikate aus dem Connected Factory-Portal und dem OPC UA-Server eingerichtet und eine Verbindung erstellt.

1. Wechseln Sie zur OPC UA-Knotenstruktur Ihres OPC UA-Servers, klicken Sie mit der rechten Maustaste auf die OPC-Knoten, und wählen **Veröffentlichen** aus. Damit die Veröffentlichung auf diese Weise funktioniert, müssen sich der OPC UA-Server und der Herausgeber im selben Netzwerk befinden. Anders ausgedrückt: Wenn der vollqualifizierte Domänenname des Herausgebers **herausgeber.meinedomäne.com** ist, muss der vollqualifizierte Domänenname des OPC UA-Servers z.B. **meinopcuaserver.meinedomäne.com** sein. Wenn Ihr Setup davon abweicht, können Sie Knoten manuell zur Datei „publishesnodes.json“ im Ordner **/shared** hinzufügen. Die Datei „publishesnodes.json“ wird automatisch beim ersten erfolgreichen Veröffentlichen eines OPC-Knotens generiert.

1. Telemetriedaten werden jetzt vom Gatewaygerät übertragen. Sie können die Telemetriedaten in der Ansicht **Factoryspeicherorte** des Connected Factory-Portals unter **Neue Factory** anzeigen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen über die Architektur der vorkonfigurierten Connected Factory-Lösung finden Sie unter [Vorkonfigurierte Connected Factory-Lösung – Exemplarische Vorgehensweise][lnk-walkthrough].

Informieren Sie sich über die [OPC Publisher-Referenzimplementierung](iot-suite-connected-factory-publisher.md).

[img-install-docker]: ./media/iot-suite-connected-factory-gateway-deployment/image1.png
[img-hub-connection]: ./media/iot-suite-connected-factory-gateway-deployment/image2.png
[img-docker-share]: ./media/iot-suite-connected-factory-gateway-deployment/image3.png

[Docker für Windows]: https://www.docker.com/docker-windows
[Katalog der Azure IoT-Geräte]: https://catalog.azureiotsuite.com/?q=opc
[Azure-Portal]: http://portal.azure.com/
[OPC UA-Open-Source-Client]: https://github.com/OPCFoundation/UA-.NETStandardLibrary/tree/master/SampleApplications/Samples/Client.Net4
[Installieren Sie Docker]: https://www.docker.com/community-edition#/download
[lnk-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[Azure IoT Edge]: https://github.com/Azure/iot-edge

[lnk-publisher-github]: https://github.com/Azure/iot-edge-opc-publisher
[lnk-publisher-docker]: https://hub.docker.com/r/microsoft/iot-gateway-opc-ua
[lnk-proxy-github]: https://github.com/Azure/iot-edge-opc-proxy
[lnk-proxy-docker]: https://hub.docker.com/r/microsoft/iot-gateway-opc-ua-proxy