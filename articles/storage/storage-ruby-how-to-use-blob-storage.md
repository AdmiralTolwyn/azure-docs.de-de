<properties
	pageTitle="Verwenden des Blobspeichers (Objektspeicher) mit Ruby | Microsoft Azure"
	description="Speichern Sie nicht strukturierte Daten in der Cloud mit Azure Blob Storage (Objektspeicher)."
	services="storage"
	documentationCenter="ruby"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="ruby"
	ms.topic="article"
    ms.date="06/24/2016"
	ms.author="robmcm"/>


# Verwenden des Blob-Speichers mit Ruby

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Übersicht

Der Azure-BLOB-Speicher ist ein Dienst, bei dem unstrukturierte Daten in der Cloud als Objekte/Blobs gespeichert werden. In Blob Storage können alle Arten von Text- oder Binärdaten gespeichert werden, z. B. ein Dokument, eine Mediendatei oder ein Installer einer Anwendung. Der Blobspeicher wird auch als Objektspeicher bezeichnet.

In diesem Leitfaden wird die Durchführung gängiger Szenarien mit Blob Storage demonstriert. Die Beispiele wurden mit der Ruby-API erstellt. Die behandelten Szenarien umfassen **Hochladen, Auflisten, Herunterladen** und **Löschen** von Blobs.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Erstellen einer Ruby-Anwendung

Erstellen Sie eine Ruby-Anwendung. Anweisungen finden Sie unter [Ruby on Rails-Webanwendung auf Azure VM](../virtual-machines/virtual-machines-linux-classic-ruby-rails-web-app.md).

## Konfigurieren der Anwendung für den Zugriff auf Storage

Um Azure Storage zu verwenden, müssen Sie das Ruby-Azure-Paket herunterladen und verwenden. Dieses Paket enthält eine Reihe von Bibliotheken, die mit den REST-Speicherdiensten kommunizieren.

### Verwenden von RubyGems zum Abrufen des Pakets

1. Verwenden Sie eine Befehlszeilenschnittstelle wie **PowerShell** (Windows), **Terminal** (Mac) oder **Bash** (Unix).

2. Geben Sie "gem install azure" in das Befehlsfenster ein, um das Gem und Abhängigkeiten zu installieren.

### Importieren des Pakets

Fügen Sie mit Ihrem bevorzugten Texteditor Folgendes oben in die Ruby-Datei an der Stelle ein, an der Sie den Speicher verwenden möchten:

	require "azure"

## Einrichten einer Azure-Speicherverbindung

Das Azure-Modul liest die Umgebungsvariablen **AZURE\_STORAGE\_ACCOUNT** und **AZURE\_STORAGE\_ACCESS\_KEY** nach Informationen aus, die erforderlich sind, um eine Verbindung zum Azure-Speicherkonto herzustellen. Wenn diese Umgebungsvariablen nicht festgelegt werden, müssen Sie die Kontoinformationen vor dem Verwenden von **Azure::Blob::BlobService** mit dem folgenden Code angeben:

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your azure storage access key>"


So rufen Sie diese Werte aus einem klassischen oder Resource Manager-Speicherkonto im Azure-Portal ab

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Navigieren Sie zum Speicherkonto, das Sie verwenden möchten.
3. Klicken Sie auf dem Blatt „Einstellungen“ auf der rechten Seite auf **Zugriffsschlüssel**.
4. Auf dem angezeigten Blatt „Zugriffsschlüssel“ sehen Sie Zugriffsschlüssel 1 und Zugriffsschlüssel 2. Sie können beide verwenden.
5. Klicken Sie auf das Symbol „Kopieren“, um den Schlüssel in die Zwischenablage zu kopieren.

So rufen Sie diese Werte aus einem klassischen Speicherkonto im klassischen Azure-Portal ab

1. Melden Sie sich beim [klassischen Azure-Portal](https://manage.windowsazure.com) an.
2. Navigieren Sie zum Speicherkonto, das Sie verwenden möchten.
3. Klicken Sie unten im Navigationsbereich auf **ZUGRIFFSSCHLÜSSEL VERWALTEN**.
4. Im eingeblendeten Dialog wird der Name des Speicherkontos, der primäre Zugriffsschlüssel und der sekundäre Zugriffsschlüssel angezeigt. Verwenden Sie den primären oder sekundären Zugriffsschlüssel.
5. Klicken Sie auf das Symbol „Kopieren“, um den Schlüssel in die Zwischenablage zu kopieren.

## Erstellen eines Containers

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Mit dem **Azure::Blob::BlobService**-Objekt können Sie mit Containern und Blobs arbeiten. Verwenden Sie die **create\_container()**-Methode, um einen Container zu erstellen.

Im folgenden Codebeispiel wird ein Container erstellt oder ggf. ein Fehler ausgegeben.

	azure_blob_service = Azure::Blob::BlobService.new
	begin
	  container = azure_blob_service.create_container("test-container")
	rescue
	  puts $!
	end

Wenn Sie die Dateien im Container öffentlich machen möchten, können Sie die Berechtigungen des Containers festlegen.

Sie können einfach den <strong>create\_container()</strong>-Aufruf ändern, um die **:public\_access\_level**-Option zu übergeben:

	container = azure_blob_service.create_container("test-container",
	  :public_access_level => "<public access level>")


Gültige Werte für die **:public\_access\_level**-Option sind:

* **blob**: Gibt vollständigen öffentlichen Lesezugriff für Container- und Blob-Daten an. Clients können Blobs innerhalb des Containers über eine anonyme Anforderung aufzählen, können aber keine Container innerhalb des Speicherkontos aufzählen.

* **container**: Gibt öffentlichen Lesezugriff für Blobs an. Blob-Daten innerhalb dieses Containers können über anonyme Anforderungen gelesen werden, Containerdaten sind aber nicht verfügbar. Clients können keine Blobs innerhalb des Containers über anonyme Anforderungen aufzählen.

Sie können aber auch die öffentliche Zugriffsstufe eines Container mithilfe der **set\_container\_acl()**-Methode ändern, um die öffentliche Zugriffsstufe anzugeben.

Im folgenden Codebeispiel wird die öffentliche Zugriffsstufe in **container** geändert:

	azure_blob_service.set_container_acl('test-container', "container")

## Hochladen eines Blobs in einen Container

Um Inhalte in einen Blob hochzuladen, verwenden Sie die **create\_block\_blob()**-Methode, um den Blob zu erstellen, und verwenden Sie eine Datei oder Zeichenfolge als Inhalt des Blobs.

Durch den folgenden Code wird die Datei **test.png** als neuer Blob namens "image-blob" in den Container hochgeladen.

	content = File.open("test.png", "rb") { |file| file.read }
	blob = azure_blob_service.create_block_blob(container.name,
	  "image-blob", content)
	puts blob.name

## Auflisten der Blobs in einem Container

Verwenden Sie die **list\_containers()**-Methode, um die Container aufzulisten. Verwenden Sie die **list\_blobs()**-Methode, um die Blobs innerhalb eines Containers aufzulisten.

Auf diese Weise werden die URLs aller Blobs in allen Containern des Kontos ausgegeben.

	containers = azure_blob_service.list_containers()
	containers.each do |container|
	  blobs = azure_blob_service.list_blobs(container.name)
	  blobs.each do |blob|
	    puts blob.name
	  end
	end

## Herunterladen von Blobs

Um Blobs herunterzuladen, verwenden Sie die **get\_blob()**-Methode zum Abrufen des Inhalts.

Das folgende Codebeispiel zeigt, wie Sie **get\_blob()** verwenden, um die Inhalte von "image-blob" herunterzuladen und in eine lokale Datei zu schreiben.

	blob, content = azure_blob_service.get_blob(container.name,"image-blob")
	File.open("download.png","wb") {|f| f.write(content)}

## Löschen eines Blobs
Verwenden Sie schließlich die **delete\_blob()**-Methode, um einen Blob zu löschen. Das folgende Codebeispiel demonstriert das Löschen eines Blobs.

	azure_blob_service.delete_blob(container.name, "image-blob")

## Nächste Schritte

Unter den folgenden Links erhalten Sie weitere Informationen zu komplexeren Speicheraufgaben:

- [Azure Storage-Teamblog](http://blogs.msdn.com/b/windowsazurestorage/)
- [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby)-Repository auf GitHub
- [Übertragen von Daten mit dem Befehlszeilenprogramm AzCopy](storage-use-azcopy.md)

<!---HONumber=AcomDC_0629_2016-->