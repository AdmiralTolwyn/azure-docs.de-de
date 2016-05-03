<properties
	pageTitle="Erstellen und Hochladen einer Windows Server-VHD unter Verwendung von PowerShell | Microsoft Azure"
	description="Hier finden Sie Informationen Sie zum Erstellen und Hochladen einer Windows Server-basierten virtuellen Festplatte (VHD) mit dem klassischen Bereitstellungsmodell und Azure PowerShell."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/15/2016"
	ms.author="cynthn"/>

# Erstellen und Hochladen einer Windows Server-VHD nach Azure

Dieser Artikel erläutert, wie Sie eine virtuelle Festplatte (Virtual Hard Disk, VHD) mit einem Betriebssystem hochladen, um sie als Image für die Erstellung von virtuellen Computern zu nutzen. Weitere Details zu Datenträgern und VHDs in Microsoft Azure finden Sie unter [Informationen zu Datenträgern und VHDs für virtuelle Computer](virtual-machines-linux-about-disks-vhds.md).


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell. Sie können einen virtuellen Computer auch mit dem Resource Manager-Modell [erfassen](virtual-machines-windows-capture-image.md) und [hochladen](virtual-machines-windows-upload-image.md).

## Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie über Folgendes verfügen:

1. **Ein Azure-Abonnement** – wenn Sie nicht bereits eines besitzen, können Sie [ein Azure-Konto kostenlos erstellen](/pricing/free-trial/?WT.mc_id=A261C142F): Sie erhalten ein Guthaben, das Sie zum Ausprobieren der zahlungspflichtigen Azure-Dienste nutzen können, und Sie können das Konto selbst dann behalten und die kostenlosen Azure-Dienste wie Websites nutzen, wenn das Guthaben aufgebraucht ist. Ihre Kreditkarte wird nur dann belastet, wenn Sie Ihre Einstellungen explizit ändern und mit einer Zahlung einverstanden sind. Sie können Ihre [Vorteile für MSDN-Abonnenten aktivieren](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): Ihr MSDN-Abonnement beinhaltet ein monatliches Guthaben, das Sie für zahlungspflichtige Azure-Dienste verwenden können.

2. **Microsoft Azure PowerShell** – Sie haben das Microsoft Azure PowerShell-Modul installiert und für die Verwendung Ihres Abonnements konfiguriert. Informationen zum Herunterladen dieses Moduls finden Sie unter [Microsoft Azure-Downloads](https://azure.microsoft.com/downloads/). Ein Lernprogramm zum Installieren und Konfigurieren des Moduls ist [hier](../powershell-install-configure.md) verfügbar. Sie verwenden das Cmdlet [Add-AzureVHD](http://msdn.microsoft.com/library/azure/dn495173.aspx) zum Hochladen der VHD.

3. **Ein unterstütztes Windows-Betriebssystem, das in einer VHD-Datei gespeichert und an einen virtuellen Computer angefügt ist**: Es stehen verschiedene Tools zum Erstellen von VHD-Dateien zur Verfügung. Sie können eine Virtualisierungslösung wie etwa Hyper-V verwenden, um den virtuellen Computer zu erstellen und das Betriebssystem zu installieren. Anweisungen hierzu finden Sie unter [Installieren der Hyper-V-Rolle und Konfigurieren eines virtuellen Computers](http://technet.microsoft.com/library/hh846766.aspx). Ausführliche Informationen zu Betriebssystemen finden Sie unter [Microsoft Server Software-Support für Microsoft Azure virtuelle Maschinen](http://go.microsoft.com/fwlink/p/?LinkId=393550).

> [AZURE.IMPORTANT] Das VHDX-Format wird in Microsoft Azure nicht unterstützt. Sie können den Datenträger mit dem Hyper-V-Manager oder dem [Convert-VHD-Cmdlet](http://technet.microsoft.com/library/hh848454.aspx) in das VHD-Format konvertieren. Ausführliche Informationen finden Sie in diesem [Blogbeitrag](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).

## Schritt 1: Vorbereiten der VHD-Datei 

Bevor Sie die VHD-Datei nach Azure hochladen, muss sie mit dem Sysprep-Tool generalisiert werden. Durch diesen Vorgang wird die VHD-Datei auf eine Verwendung als Image vorbereitet. Weitere Informationen zu Sysprep finden Sie unter [How to Use Sysprep: An Introduction](http://technet.microsoft.com/library/bb457073.aspx) (in englischer Sprache).

Führen Sie auf dem virtuellen Computer, auf dem das Betriebssystem installiert wurde, die folgende Prozedur aus:

1. Melden Sie sich beim Betriebssystem an.

2. Öffnen Sie ein Eingabeaufforderungsfenster als Administrator. Wechseln Sie in das Verzeichnis **%windir%\\system32\\sysprep**, und führen Sie dann `sysprep.exe` aus.

	![Öffnen eines Eingabeaufforderungsfensters](./media/virtual-machines-windows-classic-createupload-vhd/sysprep_commandprompt.png)

3.	Das Dialogfeld **Systemvorbereitungstool** wird angezeigt.

	![Starten von Sysprep](./media/virtual-machines-windows-classic-createupload-vhd/sysprepgeneral.png)

4.  Wählen Sie unter **Systemvorbereitungstool** die Option **Out-of-Box-Experience (OOBE) für System aktivieren**, und stellen Sie sicher, dass **Verallgemeinern** aktiviert ist.

5.  Wählen Sie unter **Optionen für Herunterfahren** die Option **Herunterfahren**.

6.  Klicken Sie auf **OK**.

## Schritt 2: Erstellen eines Azure-Speicherkontos oder Abrufen von Informationen zu Ihrem Azure-Speicherkonto

Sie benötigen ein Speicherkonto in Azure, um über einen Ort zum Hochladen der VHD-Datei zu verfügen. In diesem Schritt wird gezeigt, wie Sie ein Konto erstellen oder Informationen zu einem vorhandenen Konto abrufen.

### Option 1: Erstellen eines Speicherkontos

1. Melden Sie sich beim [klassischen Azure-Portal](https://manage.windowsazure.com) an.

2. Klicken Sie in der Befehlsleiste auf **Neu**.

3. Klicken Sie auf **Datendienste** > **Speicher** > **Schnellerfassung**.

	![Speicherkonto schnell erfassen](./media/virtual-machines-windows-classic-createupload-vhd/Storage-quick-create.png)

4. Füllen Sie die Felder wie folgt aus:

 - Geben Sie unter **URL** einen Unterdomänennamen ein, der im URL für das Speicherkonto verwendet werden soll. Der Eintrag kann drei bis 24 Kleinbuchstaben und Zahlen enthalten. Dieser Name wird der Hostname im URL, der zum Adressieren von Blob-, Warteschlangen- oder Tabellenspeicherressourcen für das Abonnement verwendet wird.
 - Wählen Sie den **Standort oder die Affinitätsgruppe** für das Speicherkonto aus. Mit einer Affinitätsgruppe können Sie Ihre Clouddienste und Speicher im selben Datencenter platzieren.
 - Sie können wählen, ob Sie **Georeplikation** für das Speicherkonto verwenden möchten. Georeplikation ist als Standard voreingestellt. Mit dieser Option werden Ihre Daten kostenfrei an einem sekundären Speicherort repliziert. So wird die Speicherung auf jenen Speicherort umgeschaltet, wenn am primären Speicherort ein größerer Ausfall auftritt. Der sekundäre Speicherort wird automatisch zugewiesen und kann nicht verändert werden. Wenn Sie aufgrund gesetzlicher Vorschriften oder Unternehmensrichtlinien mehr Kontrolle über den Speicherort des cloudbasierten Speichers benötigen, können Sie die Georeplikation deaktivieren. Beachten Sie jedoch, dass bei einem späteren Aktivieren der Georeplikation eine einmalige Datenübertragungsgebühr fällig wird, um Ihre vorhandenen Daten im sekundären Speicherort zu replizieren. Die Speicherdienste ohne Georeplikation werden mit einem Rabatt angeboten. Ausführliche Informationen finden Sie unter [Erstellen, Verwalten oder Löschen eines Speicherkontos](../storage-create-storage-account/#replication-options).

      ![Speicherkontodetails eingeben](./media/virtual-machines-windows-classic-createupload-vhd/Storage-create-account.png)

5. Klicken Sie auf **Speicherkonto erstellen**. Das Konto wird nun unter **Speicher** angezeigt.

	![Speicherkonto erfolgreich erstellt](./media/virtual-machines-windows-classic-createupload-vhd/Storagenewaccount.png)

6. Als Nächstes erstellen Sie einen Container für die hochgeladenen VHDs. Klicken Sie auf den Namen des Speicherkontos und dann auf **Container**.

	![Speicherkontodetails](./media/virtual-machines-windows-classic-createupload-vhd/storageaccount_detail.png)

7. Klicken Sie auf **Container erstellen**.

	![Speicherkontodetails](./media/virtual-machines-windows-classic-createupload-vhd/storageaccount_container.png)

8. Geben Sie einen **Namen** für den Container ein, und wählen Sie die **Zugriffsrichtlinie** aus.

	![Containername](./media/virtual-machines-windows-classic-createupload-vhd/storageaccount_containervalues.png)

	> [AZURE.NOTE] Der Container ist standardmäßig privat, und der Containerzugriff ist auf den Kontobesitzer eingeschränkt. Um öffentliche Lesezugriffe auf die im Container enthaltenen BLOBs, jedoch nicht auf die Containereigenschaften und -metadaten zuzulassen, verwenden Sie die Option **Öffentlicher BLOB**. Verwenden Sie die Option **Öffentlicher Container**, um vollständigen öffentlichen Lesezugriff auf den Container und die BLOBs zuzulassen.

### Option 2: Abrufen von Informationen zum Speicherkonto

1.	Melden Sie sich beim [klassischen Azure-Portal](https://manage.windowsazure.com) an.

2.	Klicken Sie im Navigationsbereich auf **Speicher**.

3.	Klicken Sie auf den Namen des Speicherkontos, und klicken Sie dann auf **Dashboard**.

4.	Bewegen Sie den Mauszeiger im Dashboard unterhalb von **Dienste** über die BLOB-URL, und klicken Sie auf das Zwischenablagesymbol, um die URL zu kopieren. Fügen Sie die URL ein, und speichern Sie sie. Sie verwenden die URL später, um den Befehl zum Hochladen der VHD-Datei zu erstellen.

## Schritt 3: Herstellen einer Verbindung mit dem Abonnement über Azure PowerShell

Bevor Sie eine .vhd-Datei hochladen können, müssen Sie eine sichere Verbindung zwischen dem Computer und Ihrem Abonnement in Azure herstellen. Zu diesem Zweck können Sie die Microsoft Azure Active Directory-Methode oder die Zertifikatmethode verwenden.

> [AZURE.TIP] Informationen zum Einstieg in Azure PowerShell finden Sie unter [Installieren und Konfigurieren von Microsoft Azure PowerShell](../powershell-install-configure.md). Allgemeine Informationen finden Sie unter [Erste Schritte mit Microsoft Azure-Cmdlets](https://msdn.microsoft.com/library/azure/jj554332.aspx).

### Option 1: Verwenden von Microsoft Azure AD

1. Öffnen Sie die Azure PowerShell-Konsole.

2. Geben Sie Folgendes ein: `Add-AzureAccount`

3.	Geben Sie im Fenster für die Anmeldung den Benutzernamen und das Kennwort für Ihr Geschäfts- oder Schulkonto ein.

4. Die Anmeldeinformationen werden von Azure authentifiziert und gespeichert, dann wird das Fenster geschlossen.

### Option 2: Verwenden eines Zertifikats

1. Öffnen Sie die Azure PowerShell-Konsole.

2.	Geben Sie Folgendes ein: `Get-AzurePublishSettingsFile`.

3. Es wird ein Browserfenster geöffnet, und Sie werden aufgefordert, eine PUBLISHSETTINGS-Datei herunterzuladen. Sie enthält Informationen und ein Zertifikat für Ihr Microsoft Azure-Abonnement.

	![Downloadseite des Browsers](./media/virtual-machines-windows-classic-createupload-vhd/Browser_download_GetPublishSettingsFile.png)

3. Speichern Sie die .publishsettings-Datei.

4. Geben Sie Folgendes ein: `Import-AzurePublishSettingsFile <PathToFile>`

	Dabei stellt `<PathToFile>` den vollständigen Pfad zur PUBLISHSETTINGS-Datei dar.

## Schritt 4: Hochladen der VHD-Datei

Wenn Sie die .vhd-Datei hochladen, können Sie diese .vhd-Datei an einem beliebigen Speicherort innerhalb des Blobspeichers ablegen.

1. Geben Sie über das Azure PowerShell-Fenster, welches Sie im vorherigen Schritt verwendet haben, einen dem folgenden ähnlichen Befehl ein:

	`Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>`

	Hierbei gilt:
	- **BlobStorageURL** ist die URL für das Speicherkonto.
	- Bei **YourImagesFolder** handelt es sich um den Container innerhalb des Blobspeichers, in dem Sie Ihre Images speichern möchten.
	- **VHDName** steht für den Namen, der im klassischen Azure-Portal zur Identifizierung der virtuellen Festplatte angezeigt werden soll.
	- **PathToVHDFile** stellt den vollständigen Pfad und den Namen der VHD-Datei dar.

	![PowerShell Add-AzureVHD](./media/virtual-machines-windows-classic-createupload-vhd/powershell_upload_vhd.png)

Weitere Informationen zum Add-AzureVhd-Cmdlet finden Sie unter [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx).

## Schritt 5: Hinzufügen des Images zur Liste der benutzerdefinierten Images

> [AZURE.TIP] Wenn Sie das Image nicht über das klassische Azure-Portal, sondern mithilfe von Azure PowerShell hinzufügen möchten, verwenden Sie das Cmdlet **Add-AzureVMImage**. Beispiel:

>	`Add-AzureVMImage -ImageName <ImageName> -MediaLocation <VHDLocation> -OS <OSType>`

1. Klicken Sie im klassischen Azure-Portal unter **Alle Elemente** auf **Virtuelle Computer**.

2. Klicken Sie unter "Virtuelle Computer" auf **Images**.

3. Klicken Sie auf **Image erstellen**.

	![PowerShell Add-AzureVHD](./media/virtual-machines-windows-classic-createupload-vhd/Create_Image.png)

4. Gehen Sie im Fenster **Ein Image aus einer VHD erstellen** folgendermaßen vor:

	- Geben Sie einen Wert für **Name** ein.

	- Geben Sie die **Beschreibung** ein.

	- Klicken Sie unter **VHD-URL** auf die Ordnerschaltfläche, um das Fenster **Im Cloud-Speicher navigieren** zu öffnen. Suchen Sie nach der VHD-Datei, und klicken Sie auf **Öffnen**.

    ![VHD auswählen](./media/virtual-machines-windows-classic-createupload-vhd/Select_VHD.png)

5.	Wählen Sie im Fenster **Ein Image aus einer VHD erstellen** unterhalb von **Betriebssystemfamilie** Ihr Betriebssystem aus. Aktivieren Sie die Option **Ich habe Sysprep auf dem virtuellen Computer ausgeführt, der dieser VHD zugeordnet ist**, um zu bestätigen, dass Sie das Betriebssystem generalisiert haben, und klicken Sie dann auf **OK**.

    ![Image hinzufügen](./media/virtual-machines-windows-classic-createupload-vhd/Create_Image_From_VHD.png)

6. Nachdem Sie die vorherigen Schritte abgeschlossen haben, wird das neue Image aufgelistet, sobald Sie die Registerkarte **Images** auswählen.

	![Benutzerdefiniertes Image](./media/virtual-machines-windows-classic-createupload-vhd/vm_custom_image.png)

	Dieses neue Image steht jetzt beim Erstellen eines virtuellen Computers unter **Eigene Images** zur Verfügung. Anweisungen hierzu finden Sie unter [Erstellen eines benutzerdefinierten virtuellen Computers](virtual-machines-windows-classic-createportal.md).

	![VM von benutzerdefiniertem Image erstellen](./media/virtual-machines-windows-classic-createupload-vhd/create_vm_custom_image.png)

	> [AZURE.TIP] Wenn Sie beim Erstellen eines virtuellen Computers einen Fehler mit der folgenden Fehlermeldung erhalten: "Die VHD https://XXXXX... weist eine nicht unterstützte virtuelle Größe von YYYY Bytes auf. Die Größe muss eine ganze Zahl (in MB) sein", bedeutet dies, dass die virtuelle Festplatte keine ganze Zahl von MBs hat und eine VHD mit fester Größe sein muss. Versuchen Sie, das Image nicht über das klassische Azure-Portal, sondern mit dem PowerShell-Cmdlet **Add-AzureVMImage** hinzuzufügen (siehe Schritt 5 weiter oben). Die Azure-Cmdlets sorgen dafür, dass die virtuelle Festplatte die Anforderungen für Azure erfüllt.



[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Create a storage account in Azure]: #createstorage
[Step 3: Prepare the connection to Azure]: #prepAzure
[Step 4: Upload the .vhd file]: #upload

<!---HONumber=AcomDC_0420_2016-->