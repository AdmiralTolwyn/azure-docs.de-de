
1. Melden Sie sich bei Ihrem Azure-Abonnement an, indem Sie die Schritte unter [Herstellen einer Verbindung mit Azure von der Azure Befehlszeilenschnittstelle (Azure-CLI)](../articles/xplat-cli-connect.md) ausführen.

2. Stellen Sie folgendermaßen sicher, dass der Dienstverwaltungsmodus aktiviert ist:

        azure config mode asm

3. Wählen Sie aus den verfügbaren Images das Linux-Image aus, das Sie laden möchten:

        azure vm image list | grep "Linux"

   In einem Windows-Eingabeaufforderungsfenster verwenden Sie **find** anstelle von „grep“.

4. Erstellen Sie mithilfe von `azure vm create` einen neuen virtuellen Computer mit dem Linux-Image aus der obigen Liste. Dadurch werden ein neuer Clouddienst und ein neues Speicherkonto erstellt. Sie können auch mithilfe der Option `-c` eine Verbindung zwischen diesem virtuellen Computer und einem vorhandenen Clouddienst herstellen. Zudem wird ein SSH-Endpunkt zum Anmelden beim virtuellen Linux-Computer mit der Option `-e` erstellt.

        ~$ azure vm create "MyTestVM" b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-de-DE-30GB "adminUser" -z "Small" -e -l "West US"
        info:    Executing command vm create
        + Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-de-DE-30GB
        Enter VM 'adminUser' password:*********
        Confirm password: *********
        + Looking up cloud service
        info:    cloud service MyTestVM not found.
        + Creating cloud service
        + Retrieving storage accounts
        + Creating a new storage account 'mytestvm1437604756125'
        + Creating VM
        info:    vm create command OK

    >[AZURE.NOTE] Geben Sie bei einem virtuellen Linux-Computer die Option `-e` in `vm create` an. SSH kann nicht nach der Erstellung des virtuellen Computers aktiviert werden. Weitere Informationen zu SSH finden Sie unter [Verwenden von SSH mit Linux in Azure](virtual-machines-linux-ssh-from-linux.md).

    Beachten Sie, dass das Image *b39f27a8b8c64d52b05eac6a62ebad85\_\_Ubuntu-14\_04\_4-LTS-amd64-server-20160516-de-DE-30GB* dasjenige ist, das wir aus der Liste der Images im vorherigen Schritt ausgewählt haben. *MyTestVM* ist der Name des neuen virtuellen Computers, und *adminUser* ist der Benutzername, den wir für SSH auf dem virtuellen Computer verwenden. Sie können diese Variablen nach Bedarf ersetzen. Weitere Informationen zu diesem Befehl finden Sie unter [Verwenden der Azure-Befehlszeilenschnittstelle mit der Azure-Dienstverwaltung](virtual-machines-command-line-tools.md).

5. Der neu erstellte virtuelle Linux-Computer wird in der angegebenen Liste angezeigt:

        azure vm list

6. Sie können die Attribute des virtuellen Computers mithilfe des folgenden Befehls überprüfen:

        azure vm show MyTestVM

7. Der neu erstellte virtuelle Computer kann nun mit dem Befehl `azure vm start` gestartet werden.

## Nächste Schritte
Ausführliche Informationen zu diesen Azure-CLI-Befehlen für virtuelle Computer finden Sie unter [Verwenden der Azure-Befehlszeilenschnittstelle mit der Dienstverwaltungs-API](../articles/virtual-machines-command-line-tools.md).

<!---HONumber=AcomDC_0608_2016-->