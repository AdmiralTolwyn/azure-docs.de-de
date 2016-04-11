In diesem Artikel werden die Entsprechungen für die Befehle der Microsoft Azure-Befehlszeilenschnittstelle (Azure CLI) zum Erstellen und Verwalten von virtuellen Azure-Computern in der Azure-Dienstverwaltung und im Azure-Ressourcen-Manager beschrieben. Verwenden Sie ihn als eine praktische Anleitung zum Migrieren von Skripts aus einem Befehlsmodus in einen anderen.

* Wenn Sie die Azure-Befehlszeilenschnittstelle noch nicht installiert und mit Ihrem Abonnement verbunden haben, finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle ](../articles/xplat-cli-install.md) und [Herstellen einer Verbindung mit einem Azure-Abonnement über die Azure-Befehlszeilenschnittstelle](../articles/xplat-cli-connect.md). Wenn Sie die Befehle des Ressourcen-Manager-Modus verwenden möchten, achten Sie darauf, dass eine Verbindung mit der Anmeldemethode besteht.

* Für die ersten Schritte mit dem Ressourcen-Manager-Modus in der Azure-Befehlszeilenschnittstelle müssen Sie möglicherweise die Befehlsmodi wechseln. Die Befehlszeilenschnittstelle wird standardmäßig im Dienstverwaltungsmodus gestartet. Wenn Sie in den Ressourcen-Manager-Modus wechseln möchten, führen Sie `azure config mode arm` aus. Wenn Sie zurück in den Dienstverwaltungsmodus wechseln möchten, führen Sie `azure config mode asm` aus.

* Onlinehilfe zu den Befehlen und Optionen erhalten Sie durch Eingabe von `azure <command> <subcommand> --help` oder `azure help <command> <subcommand>`.

## Aufgaben für virtuelle Computer
In der folgenden Tabelle werden allgemeine Aufgaben für virtuelle Computer verglichen, die Sie mit Befehlen der Azure-Befehlszeilenschnittstelle in der Dienstverwaltung und im Ressourcen-Manager ausführen können. Bei vielen Ressourcen-Manager-Befehlen müssen Sie den Namen einer vorhandenen Ressourcengruppe übergeben.

> [AZURE.NOTE] Diese Beispiele umfassen keine vorlagenbasierten Vorgänge, die im Allgemeinen für VM-Bereitstellungen im Ressourcen-Manager empfohlen werden. Informationen finden Sie unter [Verwenden der Azure-Befehlszeilenschnittstelle mit Azure-Ressourcen-Manager](../articles/xplat-cli-azure-resource-manager.md) und [Bereitstellen und Verwalten von virtuellen Computern mit Azure-Ressourcen-Manager-Vorlagen und der Azure-Befehlszeilenschnittstelle](../articles/virtual-machines/virtual-machines-linux-cli-deploy-templates.md).

Aufgabe | Dienstverwaltung | Ressourcen-Manager
-------------- | ----------- | -------------------------
Erstellen grundlegender virtueller Computer | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`<br/><br/>(Rufen Sie `image-urn` aus dem Befehl `azure vm image list` ab. Nähere Einzelheiten finden Sie [in diesem Artikel](../articles/virtual-machines/virtual-machines-linux-cli-ps-findimage.md).)
Erstellen eines virtuellen Linux-Computers | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Linux"`
Erstellen eines virtuellen Windows-Computers | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Windows"`
Auflisten der virtuellen Computer | `azure  vm list [options]` | `azure  vm list [options]`
Abrufen von Informationen zu einem virtuellen Computer | `azure  vm show [options] <vm_name>` | `azure  vm show [options] <resource_group> <name>`
Starten eines virtuellen Computers | `azure vm start [options] <name>` | `azure vm start [options] <resource_group> <name>`
Anhalten eines virtuellen Computers | `azure vm shutdown [options] <name>` | `azure vm stop [options] <resource_group> <name>`
Aufheben der Zuordnung eines virtuellen Computers | Nicht verfügbar | `azure vm deallocate [options] <resource-group> <name>`
Neustarten eines virtuellen Computers | `azure vm restart [options] <vname>` | `azure vm restart [options] <resource_group> <name>`
Löschen eines virtuellen Computers | `azure vm delete [options] <name>` | `azure vm delete [options] <resource_group> <name>`
Erfassen eines virtuellen Computers | `azure vm capture [options] <name>` | `azure vm capture [options] <resource_group> <name>`
Erstellen eines virtuellen Computers aus einem Benutzer-Image | `azure  vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>`
Erstellen eines virtuellen Computers von einem speziellen Datenträger | `azure  vm create [options]-d <custom-data-file> <dns-name> [userName] [password]` | `azue  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>`
Hinzufügen eines Datenträgers zu einem virtuellen Computer | `azure  vm disk attach [options] <vm-name> <disk-image-name>` – oder – <br/> `vm disk attach-new [options] <vm-name> <size-in-gb> [blob-url]` | `azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]`
Entfernen eines Datenträgers aus einem virtuellen Computer | `azure  vm disk detach [options] <vm-name> <lun>` | `azure  vm disk detach [options] <resource-group> <vm-name> <lun>`
Hinzufügen einer generischen Erweiterung zu einem virtuellen Computer | `azure  vm extension set [options] <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>`
Hinzufügen einer VMAccess-Erweiterung zu einem virtuellen Computer | Nicht verfügbar | `azure vm reset-access [options] <resource-group> <name>`
Hinzufügen einer Docker-Erweiterung zu einem virtuellen Computer | `azure  vm docker create [options] <dns-name> <image> <user-name> [password]` | `azure  vm docker create [options] <resource-group> <name> <location> <os-type>`
Hinzufügen einer Chef-Erweiterung zu einem virtuellen Computer | `azure  vm extension get-chef [options] <vm-name>` | Nicht verfügbar
Deaktivieren einer VM-Erweiterung | `azure  vm extension set [options] –b <vm-name> <extension-name> <publisher-name> <version>` | Nicht verfügbar
Entfernen einer VM-Erweiterung | `azure  vm extension set [options] –u <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>`
Auflisten von VM-Erweiterungen | `azure vm extension list [options]` | Nicht verfügbar
Anzeigen eines VM-Images | `azure vm image show [options]` | Nicht verfügbar
Abrufen der Nutzung virtueller Computerressourcen | Nicht verfügbar | `azure vm list-usage [options] <location>`
Abrufen Sie aller verfügbaren Größen von virtuellen Computern | Nicht verfügbar | `azure vm sizes [options]`


## Nächste Schritte

* Weitere Beispiele für die Befehle der Befehlszeilenschnittstelle finden Sie unter [Verwenden der Azure-Befehlszeilenschnittstelle mit der Azure-Dienstverwaltung](../articles/virtual-machines-command-line-tools.md) und [Verwenden der Azure-Befehlszeilenschnittstelle mit dem Azure-Ressourcen-Manager](../articles/azure-cli-arm-commands.md).

<!---HONumber=AcomDC_0330_2016-->