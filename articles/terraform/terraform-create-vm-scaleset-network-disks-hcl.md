---
title: Erstellen einer Azure-VM-Skalierungsgruppe mithilfe von Terraform
description: Tutorial zum Verwenden von Terraform, um eine Azure-VM-Skalierungsgruppe zu konfigurieren und mit einer Version zu versehen – inklusive eines virtuellen Netzwerks und verwalteten angefügten Datenträgern
services: terraform
ms.service: azure
keywords: Terraform DevOps, virtueller Computer, Azure, Skalierungsgruppe, Netzwerk, Speicher, Module
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: a6bc0879d07cadc6c5b0b1a21b11b3075ec69719
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169879"
---
# <a name="use-terraform-to-create-an-azure-virtual-machine-scale-set"></a>Erstellen einer Azure-VM-Skalierungsgruppe mithilfe von Terraform

Mithilfe von [Azure-VM-Skalierungsgruppen](/azure/virtual-machine-scale-sets) können Sie eine Gruppe identischer, virtueller Computer mit Lastenausgleich erstellen und verwalten, wobei sich die Zahl der VM-Instanzen je nach Bedarf oder definiertem Zeitplan automatisch erhöhen oder verringern kann.

In diesem Tutorial erfahren Sie, wie Sie [Azure Cloud Shell](/azure/cloud-shell/overview) nutzen, um folgende Aufgaben ausführen zu können:

> [!div class="checklist"]
> * Einrichten einer Terraform-Bereitstellung
> * Verwenden von Variablen und Ausgaben für die Terraform-Bereitstellung
> * Erstellen und Bereitstellen der Netzwerkinfrastruktur
> * Erstellen und Bereitstellen einer VM-Skalierungsgruppe und Anfügen an das Netzwerk
> * Erstellen und Bereitstellen einer Jumpbox zum Herstellen einer SSH-Verbindung mit den virtuellen Computern

> [!NOTE]
> Die neueste Version der in diesem Artikel verwendeten Terraform-Konfigurationsdateien befinden sich im [Repository „awesome-terraform“](https://github.com/Azure/awesome-terraform/tree/master/codelab-vmss) auf GitHub.

## <a name="prerequisites"></a>Voraussetzungen

- **Azure-Abonnement**: Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) erstellen, bevor Sie beginnen.

- **Installieren von Terraform**: Befolgen Sie die Anweisungen im Artikel [Installieren und Konfigurieren von Terraform zum Bereitstellen von VMs und sonstiger Infrastruktur in Azure](/azure/virtual-machines/linux/terraform-install-configure).

- **Erstellen eines SSH-Schlüsselpaars**: Falls Sie noch kein SSH-Schlüsselpaar besitzen, folgen Sie den Anweisungen im Artikel [Schnelle Schritte: Erstellen und Verwenden eines SSH-Schlüsselpaars (öffentlich und privat) für virtuelle Linux-Computer in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys).

## <a name="create-the-directory-structure"></a>Erstellen der Verzeichnisstruktur

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).

1. Öffnen Sie [Azure Cloud Shell](/azure/cloud-shell/overview). Falls Sie zuvor noch keine Umgebung ausgewählt haben, wählen Sie **Bash** als Umgebung aus.

    ![Cloud Shell-Eingabeaufforderung](./media/terraform-create-vm-scaleset-network-disks-hcl/azure-portal-cloud-shell-button-min.png)

1. Wechseln Sie in das Verzeichnis `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Erstellen Sie ein Verzeichnis namens `vmss`.

    ```bash
    mkdir vmss
    ```

1. Wechseln Sie zum neuen Verzeichnis:

    ```bash
    cd vmss
    ```

## <a name="create-the-variables-definitions-file"></a>Erstellen der Variablendefinitionsdatei
In diesem Abschnitt definieren Sie die Variablen zur Anpassung der durch Terraform erstellten Ressourcen.

Führen Sie in der Azure Cloud Shell-Instanz die folgenden Schritte aus:

1. Erstellen Sie eine Datei mit dem Namen `variables.tf`.

    ```bash
    vi variables.tf
    ```

1. Drücken Sie die I-TASTE, um den Einfügemodus zu starten.

1. Fügen Sie den folgenden Code in den Editor ein:

   ```hcl
   variable "location" {
    description = "The location where resources will be created"
   }

   variable "tags" {
    description = "A map of the tags to use for the resources that are deployed"
    type        = "map"

    default = {
      environment = "codelab"
    }
   }

   variable "resource_group_name" {
    description = "The name of the resource group in which the resources will be created"
    default     = "myResourceGroup"
   }
   ```

1. Drücken Sie die ESC-TASTE, um den Anfügemodus zu beenden.

1. Speichern Sie die Datei, und geben Sie den folgenden Befehl ein, um den vi-Editor zu schließen:

    ```bash
    :wq
    ```

## <a name="create-the-output-definitions-file"></a>Erstellen der Ausgabedefinitionsdatei
In diesem Abschnitt erstellen Sie die Datei, die die Ausgabe nach der Bereitstellung definiert.

Führen Sie in der Azure Cloud Shell-Instanz die folgenden Schritte aus:

1. Erstellen Sie eine Datei mit dem Namen `output.tf`.

    ```bash
    vi output.tf
    ```

1. Drücken Sie die I-TASTE, um den Einfügemodus zu starten.

1. Fügen Sie den folgenden Code in den Editor ein, um den vollqualifizierten Domänennamen (FQDN) für die virtuellen Computer verfügbar zu machen.
   :

   ```hcl
    output "vmss_public_ip" {
        value = "${azurerm_public_ip.vmss.fqdn}"
    }
   ```

1. Drücken Sie die ESC-TASTE, um den Anfügemodus zu beenden.

1. Speichern Sie die Datei, und geben Sie den folgenden Befehl ein, um den vi-Editor zu schließen:

    ```bash
    :wq
    ```

## <a name="define-the-network-infrastructure-in-a-template"></a>Definieren der Netzwerkinfrastruktur in einer Vorlage
In diesem Abschnitt erstellen Sie in einer neuen Azure-Ressourcengruppe die folgende Netzwerkinfrastruktur:

  - Ein virtuelles Netzwerk (VNET) mit dem Adressraum 10.0.0.0/16
  - Ein Subnetz mit dem Adressraum 10.0.2.0/24
  - Zwei öffentliche IP-Adressen: eine für den Lastenausgleich der VM-Skalierungsgruppe, eine für die Verbindungsherstellung mit der SSH-Jumpbox

Führen Sie in der Azure Cloud Shell-Instanz die folgenden Schritte aus:

1. Erstellen Sie eine Datei mit dem Namen `vmss.tf` zur Beschreibung der Infrastruktur der VM-Skalierungsgruppe.

    ```bash
    vi vmss.tf
    ```

1. Drücken Sie die I-TASTE, um den Einfügemodus zu starten.

1. Fügen Sie den folgenden Code am Ende der Datei ein, um den vollqualifizierten Domänennamen (FQDN) für die virtuellen Computer verfügbar zu machen.

   ```hcl
   resource "azurerm_resource_group" "vmss" {
    name     = "${var.resource_group_name}"
    location = "${var.location}"
    tags     = "${var.tags}"
   }

   resource "random_string" "fqdn" {
    length  = 6
    special = false
    upper   = false
    number  = false
   }

   resource "azurerm_virtual_network" "vmss" {
    name                = "vmss-vnet"
    address_space       = ["10.0.0.0/16"]
    location            = "${var.location}"
    resource_group_name = "${azurerm_resource_group.vmss.name}"
    tags                = "${var.tags}"
   }

   resource "azurerm_subnet" "vmss" {
    name                 = "vmss-subnet"
    resource_group_name  = "${azurerm_resource_group.vmss.name}"
    virtual_network_name = "${azurerm_virtual_network.vmss.name}"
    address_prefix       = "10.0.2.0/24"
   }

   resource "azurerm_public_ip" "vmss" {
    name                         = "vmss-public-ip"
    location                     = "${var.location}"
    resource_group_name          = "${azurerm_resource_group.vmss.name}"
    allocation_method = "Static"
    domain_name_label            = "${random_string.fqdn.result}"
    tags                         = "${var.tags}"
   }
   ```

1. Drücken Sie die ESC-TASTE, um den Anfügemodus zu beenden.

1. Speichern Sie die Datei, und geben Sie den folgenden Befehl ein, um den vi-Editor zu schließen:

   ```bash
   :wq
   ```

## <a name="provision-the-network-infrastructure"></a>Bereitstellen der Netzwerkinfrastruktur
Führen Sie unter Verwendung der Azure Cloud Shell-Instanz aus dem Verzeichnis, in dem Sie die Konfigurationsdateien (.tf) erstellt haben, die folgenden Schritte aus:

1. Initialisieren Sie Terraform.

   ```bash
   terraform init
   ```

1. Führen Sie den folgenden Befehl aus, um die definierte Infrastruktur in Azure bereitzustellen.

   ```bash
   terraform apply
   ```

   Terraform fordert Sie auf, einen „Speicherort“-Wert nach dem Muster der **Speicherort**-Variable in `variables.tf` einzugeben, auch wenn dieser nie festgelegt wird. Sie können also einen beliebigen gültigen Speicherort – z.B. „USA, Westen“ eingeben und dann die EINGABETASTE drücken. (Bei allen Werten mit Leerzeichen sind Klammern zu verwenden.)

1. Terraform gibt die Daten aus, wie sie in der `output.tf`-Datei definiert sind. Auf dem folgenden Screenshot ist zu sehen, dass der FQDN dieses Format annimmt: &lt;id>.&lt;location>.cloudapp.azure.com. Der „id“-Wert ist ein berechneter Wert und „location“ entspricht dem Wert, den Sie bei der Ausführung von Terraform bereitstellen.

   ![Vollqualifizierter Domänenname der VM-Skalierungsgruppe für öffentliche IP-Adresse](./media/terraform-create-vm-scaleset-network-disks-hcl/fqdn.png)

1. Wählen Sie im Hauptmenü des Azure-Portals **Ressourcengruppen** aus.

1. Wählen Sie auf der Registerkarte **Ressourcengruppen** **myResourceGroup** aus, damit die von Terraform erstellten Ressourcen angezeigt werden.
   ![Netzwerkressourcen der VM-Skalierungsgruppe](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-resources.png)

## <a name="add-a-virtual-machine-scale-set"></a>Hinzufügen einer VM-Skalierungsgruppe

In diesem Abschnitt erfahren Sie, wie sich der Vorlage die folgenden Ressourcen hinzufügen lassen:

- Einen Azure-Lastenausgleich und Regeln, um die Anwendung zu versorgen und an die öffentliche IP-Adresse anzufügen, deren Konfiguration oben im Artikel beschrieben wurde
- Einen Azure-Back-End-Adresspool, der dem Lastenausgleich zugewiesen werden muss
- Einen Integritätstestport, der von der Anwendung verwendet und für den Lastenausgleich konfiguriert wird
- Eine VM-Skalierungsgruppe, die sich hinter dem Lastenausgleich befindet und in dem VNET ausgeführt wird, dessen Bereitstellung oben im Artikel beschrieben wurde
- [Nginx](https://nginx.org/) auf den Knoten der VM-Skalierungsgruppe mit [Cloud-Init](https://cloudinit.readthedocs.io/en/latest/)

Führen Sie in Cloud Shell die folgenden Schritte aus:

1. Öffnen Sie die `vmss.tf`-Konfigurationsdatei.

   ```bash
   vi vmss.tf
   ```

1. Gehen Sie ans Ende der Datei und drücken Sie die A-TASTE, um den Anfügemodus zu starten.

1. Fügen Sie am Ende der Datei den folgenden Code hinzu:

   ```hcl
   resource "azurerm_lb" "vmss" {
    name                = "vmss-lb"
    location            = "${var.location}"
    resource_group_name = "${azurerm_resource_group.vmss.name}"

    frontend_ip_configuration {
      name                 = "PublicIPAddress"
      public_ip_address_id = "${azurerm_public_ip.vmss.id}"
    }

    tags = "${var.tags}"
   }

   resource "azurerm_lb_backend_address_pool" "bpepool" {
    resource_group_name = "${azurerm_resource_group.vmss.name}"
    loadbalancer_id     = "${azurerm_lb.vmss.id}"
    name                = "BackEndAddressPool"
   }

   resource "azurerm_lb_probe" "vmss" {
    resource_group_name = "${azurerm_resource_group.vmss.name}"
    loadbalancer_id     = "${azurerm_lb.vmss.id}"
    name                = "ssh-running-probe"
    port                = "${var.application_port}"
   }

   resource "azurerm_lb_rule" "lbnatrule" {
      resource_group_name            = "${azurerm_resource_group.vmss.name}"
      loadbalancer_id                = "${azurerm_lb.vmss.id}"
      name                           = "http"
      protocol                       = "Tcp"
      frontend_port                  = "${var.application_port}"
      backend_port                   = "${var.application_port}"
      backend_address_pool_id        = "${azurerm_lb_backend_address_pool.bpepool.id}"
      frontend_ip_configuration_name = "PublicIPAddress"
      probe_id                       = "${azurerm_lb_probe.vmss.id}"
   }

   resource "azurerm_virtual_machine_scale_set" "vmss" {
    name                = "vmscaleset"
    location            = "${var.location}"
    resource_group_name = "${azurerm_resource_group.vmss.name}"
    upgrade_policy_mode = "Manual"

    sku {
      name     = "Standard_DS1_v2"
      tier     = "Standard"
      capacity = 2
    }

    storage_profile_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_profile_os_disk {
      name              = ""
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    storage_profile_data_disk {
      lun          = 0
      caching        = "ReadWrite"
      create_option  = "Empty"
      disk_size_gb   = 10
    }

    os_profile {
      computer_name_prefix = "vmlab"
      admin_username       = "${var.admin_user}"
      admin_password       = "${var.admin_password}"
      custom_data          = "${file("web.conf")}"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    network_profile {
      name    = "terraformnetworkprofile"
      primary = true

      ip_configuration {
        name                                   = "IPConfiguration"
        subnet_id                              = "${azurerm_subnet.vmss.id}"
        load_balancer_backend_address_pool_ids = ["${azurerm_lb_backend_address_pool.bpepool.id}"]
        primary = true
      }
    }

    tags = "${var.tags}"
   }
   ```

1. Drücken Sie die ESC-TASTE, um den Anfügemodus zu beenden.

1. Speichern Sie die Datei, und geben Sie den folgenden Befehl ein, um den vi-Editor zu schließen:

    ```bash
    :wq
    ```

1. Erstellen Sie eine Datei mit dem Namen `web.conf`. Sie dient als Cloud-Init-Konfiguration für die virtuellen Computer, die zur Skalierungsgruppe gehören.

    ```bash
    vi web.conf
    ```

1. Drücken Sie die I-TASTE, um den Einfügemodus zu starten.

1. Fügen Sie den folgenden Code in den Editor ein:

   ```hcl
   #cloud-config
   packages:
    - nginx
   ```

1. Drücken Sie die ESC-TASTE, um den Anfügemodus zu beenden.

1. Speichern Sie die Datei, und geben Sie den folgenden Befehl ein, um den vi-Editor zu schließen:

     ```bash
     :wq
     ```

1. Öffnen Sie die `variables.tf`-Konfigurationsdatei.

    ```bash
    vi variables.tf
    ```

1. Gehen Sie ans Ende der Datei und drücken Sie die A-TASTE, um den Anfügemodus zu starten.

1. Passen Sie die Bereitstellung an, indem Sie am Ende der Datei den folgenden Code einfügen:

    ```hcl
    variable "application_port" {
       description = "The port that you want to expose to the external load balancer"
       default     = 80
    }

    variable "admin_user" {
       description = "User name to use as the admin account on the VMs that will be part of the VM Scale Set"
       default     = "azureuser"
    }

    variable "admin_password" {
       description = "Default password for admin account"
    }
    ```

1. Drücken Sie die ESC-TASTE, um den Anfügemodus zu beenden.

1. Speichern Sie die Datei, und geben Sie den folgenden Befehl ein, um den vi-Editor zu schließen:

     ```bash
     :wq
     ```

1. Erstellen Sie einen Terraform-Plan, um die Bereitstellung der VM-Skalierungsgruppe zu visualisieren. (Hierbei ist die Angabe eines Kennworts Ihrer Wahl und eines Speicherorts für Ihre Ressourcen erforderlich.)

    ```bash
    terraform plan
    ```

    Die Ausgabe des Befehls sollte ungefähr so wie auf dem folgenden Screenshot aussehen:

    ![Ausgabe des Erstellens der VM-Skalierungsgruppe](./media/terraform-create-vm-scaleset-network-disks-hcl/add-mvss-plan.png)

1. Stellen Sie die neuen Ressourcen in Azure bereit.

    ```bash
    terraform apply
    ```

    Die Ausgabe des Befehls sollte ungefähr so wie auf dem folgenden Screenshot aussehen:

    ![Terraform-Ressourcengruppe für VM-Skalierungsgruppe](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents.png)

1. Öffnen Sie einen Browser, und stellen Sie eine Verbindung mit dem FQDN her, der durch den Befehl zurückgegeben wurde.

    ![Ergebnisse des Navigierens zum FQDN](./media/terraform-create-vm-scaleset-network-disks-hcl/browser-fqdn.png)

## <a name="add-an-ssh-jumpbox"></a>Hinzufügen einer SSH-Jumpbox
Bei einer SSH-*Jumpbox* handelt es sich um einen Einzelserver, durch den Sie sozusagen „springen“, um auf andere Server im Netzwerk zuzugreifen. In diesem Schritt konfigurieren Sie die folgenden Ressourcen:

- Eine Netzwerkschnittstelle (oder Jumpbox), die mit dem gleichen Subnetz verbunden ist wie die VM-Skalierungsgruppe

- Einen virtuellen Computer, der mit dieser Netzwerkschnittstelle verbunden ist. Auf diese Jumpbox kann remote zugegriffen werden. Nach der Verbindungsherstellung können Sie eine SSH-Verbindung mit einem beliebigen virtuellen Computer in der Skalierungsgruppe herstellen.

1. Öffnen Sie die `vmss.tf`-Konfigurationsdatei.

   ```bash
   vi vmss.tf
   ```

1. Gehen Sie ans Ende der Datei und drücken Sie die A-TASTE, um den Anfügemodus zu starten.

1. Fügen Sie am Ende der Datei den folgenden Code hinzu:

   ```hcl
   resource "azurerm_public_ip" "jumpbox" {
    name                         = "jumpbox-public-ip"
    location                     = "${var.location}"
    resource_group_name          = "${azurerm_resource_group.vmss.name}"
    allocation_method = "Static"
    domain_name_label            = "${random_string.fqdn.result}-ssh"
    tags                         = "${var.tags}"
   }

   resource "azurerm_network_interface" "jumpbox" {
    name                = "jumpbox-nic"
    location            = "${var.location}"
    resource_group_name = "${azurerm_resource_group.vmss.name}"

    ip_configuration {
      name                          = "IPConfiguration"
      subnet_id                     = "${azurerm_subnet.vmss.id}"
      private_ip_address_allocation = "dynamic"
      public_ip_address_id          = "${azurerm_public_ip.jumpbox.id}"
    }

    tags = "${var.tags}"
   }

   resource "azurerm_virtual_machine" "jumpbox" {
    name                  = "jumpbox"
    location              = "${var.location}"
    resource_group_name   = "${azurerm_resource_group.vmss.name}"
    network_interface_ids = ["${azurerm_network_interface.jumpbox.id}"]
    vm_size               = "Standard_DS1_v2"

    storage_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_os_disk {
      name              = "jumpbox-osdisk"
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    os_profile {
      computer_name  = "jumpbox"
      admin_username = "${var.admin_user}"
      admin_password = "${var.admin_password}"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags = "${var.tags}"
   }
   ```

1. Öffnen Sie die `output.tf`-Konfigurationsdatei.

   ```bash
   vi output.tf
   ```

1. Gehen Sie ans Ende der Datei und drücken Sie die A-TASTE, um den Anfügemodus zu starten.

1. Fügen Sie am Ende der Datei den folgenden Code hinzu, um nach Abschluss der Bereitstellung den Hostnamen der Jumpbox anzuzeigen:

   ```hcl
   output "jumpbox_public_ip" {
      value = "${azurerm_public_ip.jumpbox.fqdn}"
   }
   ```

1. Drücken Sie die ESC-TASTE, um den Anfügemodus zu beenden.

1. Speichern Sie die Datei, und geben Sie den folgenden Befehl ein, um den vi-Editor zu schließen:

    ```bash
    :wq
    ```

1. Stellen Sie die Jumpbox bereit.

   ```bash
   terraform apply
   ```

Nach Abschluss der Bereitstellung sieht der Inhalt der Ressourcengruppe wie derjenige auf dem folgenden Screenshot aus:

![Terraform-Ressourcengruppe für VM-Skalierungsgruppe](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents-final.png)

> [!NOTE]
> Die Anmeldung mit einem Kennwort ist für die bereitgestellte Jumpbox und für die bereitgestellte VM-Skalierungsgruppe deaktiviert. Melden Sie sich mit SSH an, um auf die virtuellen Computer zuzugreifen.

## <a name="environment-cleanup"></a>Bereinigen der Umgebung

Wenn Sie die in diesem Tutorial erstellten Terraform-Ressourcen löschen möchten, geben Sie in Cloud Shell den folgenden Befehl ein:

```bash
terraform destroy
```

Die Bereinigung kann einige Minuten dauern.

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie gelernt, wie sich mithilfe von Terraform eine Azure-VM-Skalierungsgruppe erstellen lässt. In folgenden zusätzlichen Ressourcen können Sie mehr über Terraform in Azure erfahren:

[Dokumentation zu Terraform in Azure](https://docs.microsoft.com/azure/terraform/)
[Dokumentation zum Azure Provider von Terraform](https://aka.ms/terraform)
[Quelle zum Azure Provider von Terraform](https://aka.ms/tfgit) 
 [Terraform Azure-Module](https://aka.ms/tfmodules)
