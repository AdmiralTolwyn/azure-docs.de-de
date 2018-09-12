---
title: FAQ zum Azure Import/Export-Dienst | Microsoft-Dokumentation
description: Lesen Sie Antworten auf häufig gestellte Fragen zum Azure Import/Export-Dienst.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/22/2018
ms.author: alkohli
ms.component: common
ms.openlocfilehash: edaff86531a9c40064d25a046bbbb70f48b75c84
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/06/2018
ms.locfileid: "44027128"
---
# <a name="azure-importexport-service-frequently-asked-questions"></a>Azure Import/Export-Dienst: Häufig gestellte Fragen 
Es folgen Fragen, die Sie möglicherweise stellen, wenn Sie Ihren Azure Import/Export-Dienst zum Übertragen von Daten in Azure Storage verwenden, und die zugehörigen Antworten. Die Fragen und Antworten sind in folgende Kategorien unterteilt:

- Info zum Azure Import/Export-Dienst
- Vorbereiten der Datenträger für den Import/Export
- Import-/Exportaufträge
- Datenträgerversand
- Verschiedenes 

## <a name="about-importexport-service"></a>Info zum Azure Import/Export-Dienst

### <a name="can-i-copy-azure-file-storage-using-the-azure-importexport-service"></a>Kann ich den Azure Import/Export-Dienst verwenden, um in Azure File Storage zu kopieren?

Ja. Der Azure Import/Export-Dienst unterstützt den Import in Azure File Storage. Derzeit wird der Export von Azure-Dateien nicht unterstützt.

### <a name="is-the-azure-importexport-service-available-for-csp-subscriptions"></a>Ist der Azure Import/Export-Dienst für CSP-Abonnements verfügbar?

Ja. Der Azure Import/Export-Dienst unterstützt Cloud Solution Provider-Abonnements (CSP).

### <a name="can-i-use-the-azure-importexport-service-to-copy-pst-mailboxes-and-sharepoint-data-to-o365"></a>Kann ich den Azure Import/Export-Dienst zum Kopieren von PST-Postfächern und SharePoint-Daten in O365 verwenden?

Ja. Weitere Informationen hierzu finden Sie unter [Importieren von PST-Dateien oder SharePoint-Daten in Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

### <a name="can-i-use-the-azure-importexport-service-to-copy-my-backups-offline-to-the-azure-backup-service"></a>Kann ich den Azure Import/Export-Dienst zum Offlinekopieren meiner Sicherungen in den Azure Backup-Dienst verwenden?

Ja. Weitere Informationen finden Sie unter [Workflow zur Offlinesicherung in Azure Backup](../../backup/backup-azure-backup-import-export.md).

### <a name="can-i-purchase-drives-for-importexport-jobs-from-microsoft"></a>Kann ich bei Microsoft Laufwerke für Import-/Exportaufträge kaufen?

Nein. Sie müssen sowohl für den Import als auch Export Ihre eigenen Laufwerke einsenden.


## <a name="preparing-disks-for-importexport"></a>Vorbereiten der Datenträger für den Import/Export

### <a name="can-i-skip-the-drive-preparation-step-for-an-import-job-can-i-prepare-a-drive-without-copying"></a>Kann ich für einen Importauftrag den Schritt der Laufwerkvorbereitung überspringen? Kann ich ein Laufwerk ohne Kopieren vorbereiten?

Nein. Alle zum Importieren von Daten verwendeten Festplatten müssen mit dem Azure WAImportExport-Tool vorbereitet werden. Verwenden Sie das Tool auch, um Daten auf das Laufwerk zu kopieren.

### <a name="do-i-need-to-perform-any-disk-preparation-when-creating-an-export-job"></a>Muss ich den Datenträger beim Erstellen eines Exportauftrags vorbereiten?

Nein. Einige Vorabüberprüfungen sollten Sie durchführen. Überprüfen Sie die Anzahl der erforderlichen Datenträger mithilfe des PreviewExport-Befehls aus dem WAImportExport-Tool. Weitere Informationen finden Sie unter [Vorschau der Laufwerknutzung für einen Exportauftrag](https://msdn.microsoft.com/library/azure/dn722414.aspx). Sie können mit dem Befehl basierend auf der Größe der Laufwerke, die Sie verwenden möchten, eine Vorschau der Festplattenverwendung für ausgewählte Blobs anzeigen. Überprüfen Sie außerdem, ob Sie Lese-/Schreibzugriff auf die Festplatte haben, die für den Exportauftrag versendet werden soll.

## <a name="importexport-jobs"></a>Import-/Exportaufträge

### <a name="can-i-cancel-my-job"></a>Kann ich meinen Auftrag stornieren?
Ja. Sie können einen Auftrag stornieren, solange dieser den Status **Wird erstellt** oder **Wird versendet** hat. Nach diesen Phasen kann der Auftrag nicht abgebrochen werden und wird bis zur letzten Phase fortgesetzt.

### <a name="how-long-can-i-view-the-status-of-completed-jobs-in-the-azure-portal"></a>Wie lange kann ich den Status abgeschlossener Aufträge im Azure-Portal anzeigen?
Den Status abgeschlossener Aufträge können Sie bis zu 90 Tage lang anzeigen. Abgeschlossene Aufträge werden nach 90 Tagen gelöscht.

### <a name="if-i-want-to-import-or-export-more-than-10-drives-what-should-i-do"></a>Was soll ich tun, wenn ich mehr als 10 Laufwerke importieren oder exportieren möchte?
Jeder Import- oder Exportauftrag kann auf maximal 10 Laufwerke verweisen. Um mehr als zehn Laufwerke zu verschicken, sollten Sie mehrere Aufträge erstellen. Laufwerke, die demselben Auftrag zugeordnet sind, müssen zusammen in einem Paket verschickt werden. Weitere Informationen und Anleitungen für den Fall, dass die Datenkapazität mehrere Datenträgerimportaufträge umfasst, erhalten Sie von Microsoft unter bulkimport@microsoft.com.                                                              

## <a name="shipping-disks"></a>Datenträgerversand

### <a name="what-is-the-maximum-number-of-hdd-for-in-one-shipment"></a>Wie viele HDDs kann eine Lieferung maximal enthalten?
Es gibt keine Beschränkung für die Anzahl der HDDs in einer Lieferung. Wenn die Datenträger zu mehreren Aufträgen gehören, sollten Sie auf Folgendes achten: 
- Bezeichnen Sie die Datenträger mit entsprechenden Auftragsnamen.
- Aktualisieren Sie die Aufträge mit einer Nachverfolgungsnummer und dem Suffix „-1“, „-2“ usw.

### <a name="should-i-include-anything-other-than-the-hdd-in-my-package"></a>Sollte mein Paket noch andere Dinge als die Festplatte enthalten?
Senden Sie nur die Festplatten im Versandpaket. Legen Sie keine Gegenstände wie z. B. Strom- oder USB-Kabel bei.

### <a name="do-i-have-to-ship-my-drives-using-fedex-or-dhl"></a>Muss ich meine Festplatten mit FedEx oder DHL schicken?
Sie können Festplatten mit allen gängigen Kurierdiensten wie FedEx, DHL, UPS oder US Postal Service an das Azure-Rechenzentrum schicken. Allerdings müssen Sie für die Rücksendung der Laufwerke vom Rechenzentrum an Sie Folgendes angeben:

- Eine FedEx-Kontonummer in den USA und Europa, oder
- eine DHL-Kontonummer in den Regionen Asien und Australien.

### <a name="are-there-any-restrictions-with-shipping-my-drive-internationally"></a>Gelten für den internationalen Versand meiner Festplatte bestimmte Einschränkungen?
Beachten Sie, dass die physischen Medien beim Versand unter Umständen Ländergrenzen überqueren. Sie müssen sicherstellen, dass Ihre physischen Medien und Daten gemäß geltender Gesetze importiert bzw. exportiert werden. Prüfen Sie vor dem Versand der physischen Medien mit Ihren Rechtsberatern, ob Medien und Daten laut Gesetz an das entsprechende Rechenzentrum verschickt werden dürfen. So stellen Sie sicher, dass Ihre Daten zeitnah bei Microsoft eintreffen.

### <a name="when-creating-a-job-the-shipping-address-is-a-location-that-is-different-from-my-storage-account-location-what-should-i-do"></a>Beim Erstellen des Auftrags habe ich festgestellt, dass die Versandadresse ein Standort ist, der sich von meinem Speicherkontostandort unterscheidet. Wie sollte ich vorgehen?

Einige Speicherkontostandorte sind alternativen Standorten für den Versand zugeordnet. Versandstandorte, die bisher verfügbar waren, können vorübergehend auch anderen Standorten zugeordnet werden. Überprüfen Sie immer die Versandadresse, die bei der Erstellung des Auftrags angegeben wurde, bevor Sie die Festplatten verschicken.

### <a name="when-shipping-my-drive-the-carrier-asks-for-the-data-center-contact-address-and-phone-number-what-should-i-provide"></a>Beim Versand der Festplatte fragt der Kurierdienst nach der Anschrift und der Telefonnummer des Rechenzentrums. Was muss ich hier angeben?

Die Telefonnummer und die Adresse des Rechenzentrums werden beim Erstellen des Auftrags angegeben.


## <a name="miscellaneous"></a>Verschiedenes

### <a name="what-happens-if-i-accidentally-send-an-hdd-that-does-not-conform-to-the-supported-requirements"></a>Was geschieht, wenn ich aus Versehen eine HDD verschicke, die den unterstützten Anforderungen nicht entspricht?

Das Azure-Datacenter schickt das Laufwerk, das den unterstützten Anforderungen nicht entspricht, an Sie zurück. Wenn nur einige der Laufwerke in dem Paket die Anforderungen erfüllen, werden diese Laufwerke verarbeitet, und die Laufwerke, die die Anforderungen nicht erfüllen, werden an Sie zurückgeschickt.

### <a name="does-the-service-format-the-drives-before-returning-them"></a>Werden die Festplatten vom Dienst formatiert, bevor sie zurückgeschickt werden?

Nein. Alle Festplatten werden mit BitLocker verschlüsselt.

### <a name="how-can-i-access-data-that-is-imported-by-this-service"></a>Wie kann ich auf Daten zugreifen, die von diesem Dienst importiert werden?

Greifen Sie über das Azure-Portal oder den [Storage-Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) auf die Daten unter Ihrem Azure-Speicherkonto zu.  

### <a name="after-the-import-is-complete-what-does-my-data-look-like-in-the-storage-account-is-my-directory-hierarchy-preserved"></a>Wie sehen meine Daten nach Abschluss des Imports im Speicherkonto aus? Wird meine Verzeichnishierarchie beibehalten?

Beim Vorbereiten einer Festplatte für einen Importauftrag wird das Ziel mit dem Feld „DstBlobPathOrPrefix“ in der Dataset-CSV-Datei angegeben. Dies ist der Zielcontainer im Speicherkonto, in den Daten von der Festplatte kopiert werden. In diesem Zielcontainer werden virtuelle Verzeichnisse für Ordner von der Festplatte und Blobs für Dateien erstellt. 

### <a name="if-a-drive-has-files-that-already-exist-in-my-storage-account-does-the-service-overwrite-existing-blobs-or-files"></a>Wenn eine Festplatte Dateien enthält, die in meinem Speicherkonto bereits vorhanden sind, werden die entsprechenden Blobs oder Dateien dann vom Dienst überschrieben?

Das ist unterschiedlich. Beim Vorbereiten der Festplatte können Sie angeben, ob die Zieldateien überschrieben oder ignoriert werden sollen. Hierfür verwenden Sie das Feld „Disposition:<rename|no-overwrite|overwrite>“ (umbenennen, nicht überschreiben, überschreiben) in der Dataset-CSV-Datei. In der Standardeinstellung benennt der Dienst die neuen Dateien um, anstatt vorhandene Blobs oder Dateien zu überschreiben.

### <a name="is-the-waimportexport-tool-compatible-with-32-bit-operating-systems"></a>Ist das WAImportExport-Tool mit dem 32-Bit-Windows-Betriebssystem kompatibel?
Nein. Das WAImportExport-Tool ist nur mit dem 64-Bit-Windows-Betriebssystem kompatibel. Eine vollständige Liste der unterstützten Betriebssysteme finden Sie unter [Unterstützte Betriebssysteme](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements). 


### <a name="what-is-the-maximum-block-blob-and-page-blob-size-supported-by-azure-importexport"></a>Welche maximale Blockblob- und Seitenblobgröße wird von Azure Import/Export unterstützt?

Die maximale Blockblobgröße beträgt ungefähr 4,768 TB oder 5.000.000 MB.
Die maximale Seitenblobgröße beträgt 1 TB.


### <a name="does-azure-importexport-support-aes-256-encryption"></a>Unterstützt Azure Import/Export die AES-256-Verschlüsselung?
Der Azure Import/Export-Dienst verwendet standardmäßig die AES-128-Bitlocker-Verschlüsselung. Bevor die Daten kopiert werden, können Sie dies durch manuelle Verschlüsselung mit Bitlocker in AES-256 ändern. 

- Wenn Sie [WAImportExport V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip) verwenden, sehen Sie sich folgenden Beispielbefehl an:
    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>] 
    ```
- Geben Sie bei Verwendung von [WAImportExport V2](https://www.microsoft.com/en-us/download/details.aspx?id=55280) „AlreadyEncrypted“ an, und geben Sie den Schlüssel in der Driveset-CSV-Datei an.
    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
    ```

## <a name="next-steps"></a>Nächste Schritte

* [Was ist Azure Import/Export?](storage-import-export-service.md)


