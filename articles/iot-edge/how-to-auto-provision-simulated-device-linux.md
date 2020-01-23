---
title: Bereitstellen von Geräten mit einem virtuellen TPM auf Linux-VMs – Azure IoT Edge
description: Verwenden eines simulierten TPM auf einem virtuellen Linux-Computer, um den Azure Device Provisioning Service für Azure IoT Edge zu testen
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6bb1282212ccff45f179b8750e3ed8aec27d129e
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76511058"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Erstellen und Bereitstellen eines IoT Edge-Geräts mit einem virtuellen TPM auf einem virtuellen Linux-Computer

Azure IoT Edge-Geräte können über den [Device Provisioning Service](../iot-dps/index.yml) automatisch bereitgestellt werden. Wenn Sie mit der automatischen Bereitstellung nicht vertraut sind, lesen Sie die Informationen unter [Konzepte für die automatische Bereitstellung](../iot-dps/concepts-auto-provisioning.md), bevor Sie fortfahren.

In diesem Artikel wird beschrieben, wie Sie die automatische Bereitstellung auf einem simulierten IoT Edge-Gerät mit den folgenden Schritten testen:

* Erstellen Sie einen virtuellen Linux-Computer (VM) in Hyper-V mit einem simulierten Trusted Platform Module (TPM) für die Hardwaresicherheit.
* Erstellen Sie eine neue Instanz für den IoT Hub Device Provisioning-Dienst (DPS).
* Erstellen Sie eine individuelle Registrierung für das Gerät
* Installieren Sie die IoT Edge-Runtime, und verbinden Sie das Gerät mit einem IoT Hub

> [!TIP]
> In diesem Artikel wird beschrieben, wie Sie die DPS-Bereitstellung mithilfe eines TPM-Simulators testen. Viele Informationen darin gelten aber auch für physische TPM-Hardware, wie z.B. das [Infineon OPTIGA&trade; TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), ein Azure Certified for IoT-Gerät.
>
> Wenn Sie ein physisches Gerät verwenden, können Sie mit dem Abschnitt [Abrufen von Bereitstellungsinformationen von einem physischen Gerät](#retrieve-provisioning-information-from-a-physical-device) in diesem Artikel fortfahren.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Windows-Entwicklungscomputer mit [aktiviertem Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). In diesem Artikel wird das Betriebssystem Windows 10 verwendet, auf dem ein Ubuntu Server-VM ausgeführt wird.
* Aktiver IoT Hub.
* Wenn Sie ein simuliertes TPM verwenden, [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 oder höher mit aktivierter Workload [‚Desktopentwicklung mit C++‘](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/).

> [!NOTE]
> TPM 2.0 ist erforderlich, wenn der TPM-Nachweis mit DPS verwendet wird. Es kann nur zum Erstellen von einzelnen Registrierungen (keinen Gruppenregistrierungen) verwendet werden.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Erstellen eines virtuellen Linux-Computers mit virtuellem TPM

In diesem Abschnitt erstellen Sie einen neuen virtuellen Linux-Computer unter Hyper-V. Sie haben diesen virtuellen Computer mit einem simulierten TPM konfiguriert, damit Sie damit testen können, wie die automatische Bereitstellung mit IoT Edge funktioniert.

### <a name="create-a-virtual-switch"></a>Erstellen eines virtuellen Switches

Mit einem virtuellen Switch können Sie Ihren virtuellen Computer mit einem physischen Netzwerk verbinden.

1. Öffnen Sie Hyper-V-Manager auf Ihrem Windows-Computer.

2. Klicken Sie im Menü **Aktionen** auf **Manager für virtuelle Switches**.

3. Wählen Sie einen **externen** virtuellen Switch, und klicken Sie anschließend auf **Virtuellen Switch erstellen**.

4. Geben Sie Ihrem neuen virtuellen Switch einen Namen, z.B. **EdgeSwitch**. Stellen Sie sicher, dass der Verbindungstyp auf **Externes Netzwerk**, festgelegt ist, und klicken Sie anschließend auf **OK**.

5. Ein Popup warnt Sie, dass die Netzwerkkonnektivität möglicherweise unterbrochen wird. Wählen Sie **Yes** (Ja), um fortzufahren.

Wenn beim Erstellen des neuen Switches Fehler angezeigt werden, stellen Sie sicher, dass keine anderen Switches den Ethernet-Adapter verwenden und dass keine anderen Switches den gleichen Namen verwenden.

### <a name="create-virtual-machine"></a>Erstellen eines virtuellen Computers

1. Laden Sie eine für Ihren virtuellen Computer zu verwendende Festplatten-Imagedatei herunter, und speichern Sie sie lokal. Beispiel: [Ubuntu-Server](https://www.ubuntu.com/download/server)

2. Wählen Sie in Hyper-V-Manager im Menü **Aktionen** die Option **Neu** > **Virtueller Computer**.

3. Geben Sie im **Assistenten für neue virtuelle Computer** die folgenden speziellen Konfigurationen ein:

   1. **Generation angeben**: Wählen Sie **Generation 2** aus. Für virtuelle Computer der zweiten Generation ist die geschachtelte Virtualisierung aktiviert, die zum Ausführen von IoT Edge auf einem virtuellen Computer erforderlich ist.
   2. **Konfigurieren der Netzwerkeinstellungen**: Legen Sie den Wert der **Verbindung** auf den virtuellen Switch fest, den Sie im vorherigen Abschnitt erstellt haben.
   3. **Installationsoptionen**: Wählen Sie **Betriebssystem von einer startbaren Imagedatei installieren** aus, und navigieren Sie zu der Datenträger-Imagedatei, die Sie lokal gespeichert haben.

4. Wählen Sie im Assistenten die Option **Fertig stellen**, um den virtuellen Computer zu erstellen.

Die Erstellung der neuen VM kann einige Minuten dauern.

### <a name="enable-virtual-tpm"></a>Virtuelles TPM aktivieren

Öffnen Sie nach der Erstellung Ihrer VM die zugehörigen Einstellungen, um das virtuelle Trusted Platform Module (TPM) zu aktivieren, mit dem Sie das Gerät automatisch bereitstellen lassen können.

1. Wählen Sie den virtuellen Computer aus, und öffnen Sie dann die **Einstellungen**.

2. Navigieren zu **Sicherheit**.

3. Deaktivieren Sie **Sicheren Start aktivieren**.

4. Aktivieren Sie **Trusted Platform Module aktivieren**.

5. Klicken Sie auf **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Starten des virtuellen Computers und Erfassen von TPM-Daten

Erstellen Sie auf dem virtuellen Computer ein Tool, mit dem Sie die **Registrierungs-ID** und den **Endorsement Key** für das Gerät abrufen können.

1. Starten Sie Ihren virtuellen Computer, und stellen Sie eine Verbindung damit her.

1. Befolgen Sie die Anweisungen auf dem virtuellen Computer, um die Installation abzuschließen und den Computer zu starten.

1. Melden Sie sich am virtuellen Computer an, und führen Sie dann die Schritte unter [Einrichten einer Linux-Entwicklungsumgebung](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) aus, um das Azure IoT-Geräte-SDK für C zu installieren und zu erstellen.

   >[!TIP]
   >Im Verlauf dieses Artikels führen Sie das Kopieren und Einfügen auf dem virtuellen Computer durch. Dies ist mit der Hyper-V-Manager-Verbindungsanwendung kein einfacher Vorgang. Es kann ratsam sein, dass Sie über den Hyper-V-Manager eine Verbindung mit dem virtuellen Computer herstellen, nachdem Sie seine IP-Adresse abgerufen haben: `ifconfig`. Anschließend können Sie die IP-Adresse verwenden, um eine Verbindung per SSH herzustellen: `ssh <username>@<ipaddress>`.

1. Führen Sie die folgenden Befehle zum Erstellen des SDK-Tools aus, das Ihre Gerätebereitstellungsinformationen aus dem TPM-Simulator abruft.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Navigieren Sie in einem Befehlsfenster zum Verzeichnis `azure-iot-sdk-c`, und führen Sie den TPM-Simulator aus. Dieser lauscht über einen Socket an den Ports 2321 und 2322. Lassen Sie das Befehlsfenster geöffnet. Es wird benötigt, um den Simulator weiterhin auszuführen.

   Führen Sie aus dem Verzeichnis `azure-iot-sdk-c` den folgenden Befehl zum Starten des Simulators aus:

   ```bash
   ./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe
   ```

1. Öffnen Sie mithilfe von Visual Studio die im Verzeichnis `cmake` generierte Projektmappe `azure_iot_sdks.sln`, und erstellen Sie sie mithilfe des Befehls **Projektmappe erstellen** im Menü **Build**.

1. Navigieren Sie in Visual Studio im **Projektmappen-Explorer** zum Ordner **Provision\_Tools**. Klicken Sie mit der rechten Maustaste auf das Projekt **tpm_device_provision**, und wählen Sie **Als Startprojekt festlegen** aus.

1. Führen Sie die Projektmappe über einen der **Start**befehle im Menü **Debuggen** aus. Im Ausgabefenster werden die **Registrierungs-ID** und der **Endorsement Key** für den TPM-Simulator angezeigt, die Sie zur späteren Verwendung kopieren sollten. Wenn Sie eine individuelle Registrierung für Ihr Gerät in erstellen, können Sie dieses Fenster (mit Registrierungs-ID und Endorsement Key) schließen. Lassen Sie aber das Fenster geöffnet, in dem der TPM-Simulator ausgeführt wird.

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Abrufen von Bereitstellungsinformationen von einem physischen Gerät

Erstellen Sie auf Ihrem Gerät ein Tool, das Sie zum Abrufen der Bereitstellungsinformationen für das Gerät verwenden können.

1. Führen Sie die Schritte in [Einrichten einer Linux-Entwicklungsumgebung](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) aus, um das Azure IoT-Geräte-SDK für C zu installieren und zu erstellen.

1. Führen Sie die folgenden Befehle zum Erstellen des SDK-Tools aus, das Ihre Gerätebereitstellungsinformationen vom TPM-Gerät abruft.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Kopieren Sie die Werte für **Registrierungs-ID** und **Endorsement Key**. Sie können diese Werte verwenden, um eine individuelle Registrierung für Ihr Gerät im DPS zu erstellen.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Einrichten des IoT Hub Device Provisioning Service

Erstellen Sie eine neue Instanz des IoT Hub Device Provisioning Service in Azure, und verknüpfen Sie sie mit Ihrem IoT-Hub. Befolgen Sie die Anweisungen unter [Einrichten des IoT Hub Device Provisioning Service](../iot-dps/quick-setup-auto-provision.md).

Nachdem Sie den Device Provisioning-Dienst ausgeführt haben, kopieren Sie den Wert von **ID-Bereich** von der Seite „Übersicht“. Sie können diesen Wert verwenden, wenn Sie IoT Edge-Runtime konfigurieren.

## <a name="create-a-dps-enrollment"></a>Erstellen einer DPS-Registrierung

Rufen Sie die Bereitstellungsinformationen von Ihrem virtuellen Computer ab, und verwenden Sie diese, um eine individuelle Registrierung im Device Provisioning-Dienst zu erstellen.

Wenn Sie eine Registrierung im DPS erstellen, haben Sie die Möglichkeit zum Angeben von **Anfänglicher Status von Gerätezwilling**. Im Gerätezwilling können Sie Tags zum Gruppieren von Geräten nach jeder beliebigen Metrik, z.B. Region, Umgebung, Speicherort oder Geräte, festlegen, die Sie in Ihrer Projektmappe benötigen. Diese Tags werden zum Erstellen von [automatischen Bereitstellungen](how-to-deploy-monitor.md) verwendet.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Instanz des IoT Hub Device Provisioning Service.

2. Klicken Sie in **Einstellungen** auf **Registrierungen verwalten**.

3. Klicken Sie auf **Individuelle Registrierung hinzufügen**, und führen Sie dann die folgenden Schritte aus, um die Registrierung zu konfigurieren:  

   1. Klicken Sie unter **Mechanismus** auf die Option **TPM**.

   2. Geben Sie den **Endorsement Key** und die **Registrierungs-ID** an, den bzw. die Sie von Ihrem virtuellen Computer kopiert haben.

      > [!TIP]
      > Wenn Sie ein physisches TPM-Gerät verwenden, müssen Sie den **Endorsement Key** ermitteln, der für jeden TPM-Chip eindeutig ist und von dem ihm zugeordneten TPM-Chiphersteller abgerufen wird. Sie können eine eindeutige **Registrierungs-ID** für Ihr TPM-Gerät ableiten, indem Sie beispielsweise einen SHA-256-Hash des Endorsement Keys erstellen.

   3. Wählen Sie **True**, um anzugeben, dass dieser virtuelle Computer ein IoT Edge-Gerät ist.

   4. Wählen Sie den verknüpften **IoT-Hub**, mit dem Sie Ihr Gerät verbinden möchten. Sie können mehrere Hubs auswählen, und das Gerät wird dann je nach gewählter Zuteilungsrichtlinie einem dieser Hubs zugewiesen.

   5. Stellen Sie nach Wunsch eine ID für Ihr Gerät bereit. Sie können mithilfe von Geräte-IDs ein einzelnes Gerät als Ziel für die Modulbereitstellung festlegen. Wenn Sie keine Geräte-ID angeben, wird die Registrierungs-ID verwendet.

   6. Fügen Sie nach Wunsche einen Tagwert zu **Anfänglicher Status von Gerätezwilling** hinzu. Sie können mithilfe von Tags Gruppen von Geräten als Ziel für die Modulbereitstellung festlegen. Beispiel:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   7. Wählen Sie **Speichern** aus.

Nachdem nun eine Registrierung für dieses Gerät vorhanden ist, kann die IoT Edge-Runtime das Gerät während der Installation automatisch bereitstellen.

## <a name="install-the-iot-edge-runtime"></a>Installieren der IoT Edge-Runtime

Die IoT Edge-Runtime wird auf allen IoT Edge-Geräten bereitgestellt. Die Komponenten werden in Containern ausgeführt, und Sie können weitere Container auf dem Gerät bereitstellen, um Code im Edge-Bereich auszuführen. Installieren Sie die IoT Edge-Runtime auf Ihrem virtuellen Computer.

Sie müssen Ihren DPS-**ID-Bereich** und die Geräte-**Registrierungs-ID** kennen, bevor Sie mit dem Artikel beginnen, der Ihrem Gerätetyp entspricht. Wenn Sie den Ubuntu-Beispielserver installiert haben, verwenden Sie die **x64**-Anweisungen. Stellen Sie sicher, dass Sie die IoT Edge-Runtime für die automatische und nicht für die manuelle Bereitstellung konfigurieren.

[Installieren der Azure IoT Edge-Runtime unter Linux](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>Gewähren von IoT Edge-Zugriff für das TPM

Damit die IoT Edge-Runtime Ihr Gerät automatisch bereitstellt, benötigt es Zugriff auf das TPM.

Sie können TPM-Zugriff auf die IoT Edge-Runtime gewähren, indem Sie die Systemeinstellungen überschreiben, sodass der Dienst **iotedge** über Stammrechte verfügt. Wenn Sie die Dienstberechtigungen nicht erhöhen möchten, können Sie auch die folgenden Schritte ausführen, um den TPM-Zugriff manuell bereitzustellen.

1. Suchen Sie den Pfad zum TPM-Hardwaremodul auf Ihrem Gerät, und speichern Sie ihn als lokale Variable.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Erstellen Sie eine neue Regel, damit die IoT Edge-Runtime Zugriff auf tpm0 erhält.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Öffnen Sie die Regeldatei.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Kopieren Sie die folgenden Informationen, in die Regeldatei.

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", GROUP="iotedge", MODE="0660"
   ```

5. Speichern und beenden Sie die Datei.

6. Lösen Sie das Udev-System aus, um die neue Regel ausgewertet.

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Vergewissern Sie sich, dass die Regel erfolgreich angewendet wurde.

   ```bash
   ls -l /dev/tpm0
   ```

   Die erfolgreiche Ausgabe sieht wie folgt aus:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Wenn Sie nicht sehen, dass die richtigen Berechtigungen angewendet wurden, versuchen Sie, Ihren Computer neu zu starten, um Udev zu aktualisieren.

## <a name="restart-the-iot-edge-runtime"></a>Neustart der IoT Edge-Runtime

Starten Sie die IoT Edge-Runtime neu, damit alle am Gerät vorgenommenen Konfigurationsänderungen erfasst werden.

   ```bash
   sudo systemctl restart iotedge
   ```

Überprüfen Sie, ob die IoT Edge-Runtime ausgeführt wird.

   ```bash
   sudo systemctl status iotedge
   ```

Wenn Bereitstellungsfehler angezeigt werden, sind die Konfigurationsänderungen möglicherweise noch nicht wirksam. Starten Sie den IoT Edge-Daemon erneut.

   ```bash
   sudo systemctl daemon-reload
   ```

Oder starten Sie Ihren virtuellen Computer erneut, um festzustellen, ob die Änderungen bei einem sauberen Start wirksam werden.

## <a name="verify-successful-installation"></a>Bestätigen einer erfolgreichen Installation

Wenn die Runtime erfolgreich gestartet wurde, können Sie zum IoT Hub wechseln und sehen, dass Ihr neues Gerät automatisch bereitgestellt wurde. Es ist jetzt bereit für die Ausführung von IoT Edge-Modulen.

Überprüfen Sie den Status des IoT Daemons.

```cmd/sh
systemctl status iotedge
```

Untersuchen Sie die Daemonprotokolle.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Führen Sie ausgeführte Module auf.

```cmd/sh
iotedge list
```

Sie können überprüfen, ob die individuelle Registrierung, die Sie im Device Provisioning Service erstellt haben, verwendet wurde. Navigieren Sie zu Ihrer Device Provisioning Service-Instanz im Azure-Portal. Öffnen Sie die Registrierungsdetails für die von Ihnen erstellte individuelle Registrierung. Beachten Sie, dass der Status der Registrierung **Zugewiesen** lautet und die Geräte-ID aufgeführt ist.

## <a name="next-steps"></a>Nächste Schritte

Der Registrierungsprozess des Device Provisioning-Diensts ermöglicht Ihnen, die Geräte-ID und die Tags von Gerätezwillingen beim Bereitstellen des neuen Geräts zu sehen. Sie können diese Werte verwenden, um einzelne Geräte oder Gruppen von Geräten über die automatische Geräteverwaltung als Ziel festzulegen. Weitere Informationen finden Sie unter [Bedarfsgerechtes Bereitstellen und Überwachen von IoT Edge-Modulen mithilfe des Azure-Portals](how-to-deploy-monitor.md) oder [Verwenden der Azure CLI](how-to-deploy-monitor-cli.md).
