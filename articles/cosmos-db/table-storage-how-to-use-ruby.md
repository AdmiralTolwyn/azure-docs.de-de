---
title: Verwenden von Azure Table Storage mit Ruby | Microsoft-Dokumentation
description: Speichern Sie strukturierte Daten mit Azure Table Storage, einem NoSQL-Datenspeicher, in der Cloud.
services: cosmos-db
documentationcenter: ruby
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 047cd9ff-17d3-4c15-9284-1b5cc61a3224
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: decc6ffb38a4358d3593642f9cedb59d08f6bfef
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/18/2018
---
# <a name="how-to-use-azure-table-storage-with-ruby"></a>Verwenden von Azure Table Storage mit Ruby
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-langsoon-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

## <a name="overview"></a>Übersicht
In diesem Leitfaden wird die Durchführung häufiger Szenarien mit dem Azure-Tabellenspeicherdienst demonstriert. Die Beispiele wurden mit der Ruby-API erstellt. Die behandelten Szenarien umfassen das **Erstellen und Löschen einer Tabelle sowie das Einfügen und Abfragen von Tabellenentitäten**.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Erstellen einer Ruby-Anwendung
Anweisungen zum Erstellen einer Ruby-Anwendung finden Sie unter [Ruby on Rails-Webanwendung auf Azure VM](../virtual-machines/linux/classic/ruby-rails-web-app.md).

## <a name="configure-your-application-to-access-storage"></a>Konfigurieren der Anwendung für den Zugriff auf Storage
Um Azure Storage zu verwenden, müssen Sie das Ruby-Azure-Paket, das eine Reihe von Bibliotheken für die Kommunikation mit den Speicher-REST-Diensten herunterladen und verwenden.

### <a name="use-rubygems-to-obtain-the-package"></a>Verwenden von RubyGems zum Abrufen des Pakets
1. Verwenden Sie eine Befehlszeilenschnittstelle wie **PowerShell** (Windows), **Terminal** (Mac) oder **Bash** (Unix).
2. Geben Sie im Befehlsfenster **gem install azure** ein, um das Gem und Abhängigkeiten zu installieren.

### <a name="import-the-package"></a>Importieren des Pakets
Fügen Sie mit Ihrem bevorzugten Texteditor Folgendes oben in die Ruby-Datei an der Stelle ein, an der Sie Storage verwenden möchten:

```ruby
require "azure"
```

## <a name="set-up-an-azure-storage-connection"></a>Einrichten einer Azure-Speicherverbindung
Das Azure-Modul liest die Umgebungsvariablen **AZURE\_STORAGE\_ACCOUNT** und **AZURE\_STORAGE\_ACCESS\_KEY** nach Informationen aus, die erforderlich sind, um eine Verbindung mit dem Azure Storage-Konto herzustellen. Wenn diese Umgebungsvariablen nicht festgelegt werden, müssen Sie die Kontoinformationen vor dem Verwenden von **Azure::TableService** mit dem folgenden Code angeben:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your azure storage access key>"
```

So rufen Sie diese Werte aus einem klassischen oder Resource Manager-Speicherkonto im Azure-Portal ab

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.
2. Navigieren Sie zum Speicherkonto, das Sie verwenden möchten.
3. Klicken Sie auf dem Blatt „Einstellungen“ auf der rechten Seite auf **Zugriffsschlüssel**.
4. Auf dem angezeigten Blatt „Zugriffsschlüssel“ sehen Sie Zugriffsschlüssel 1 und Zugriffsschlüssel 2. Sie können beide verwenden.
5. Klicken Sie auf das Symbol „Kopieren“, um den Schlüssel in die Zwischenablage zu kopieren.

## <a name="create-a-table"></a>Erstellen einer Tabelle
Mit dem Objekt **Azure::TableService** können Sie mit Tabellen und Entitäten arbeiten. Verwenden Sie die **create\_table()**-Methode, um eine Tabelle zu erstellen. Im folgenden Beispiel wird eine Tabelle erstellt oder ggf. ein Fehler ausgegeben.

```ruby
azure_table_service = Azure::TableService.new
begin
    azure_table_service.create_table("testtable")
rescue
    puts $!
end
```

## <a name="add-an-entity-to-a-table"></a>Hinzufügen einer Entität zu einer Tabelle
Um eine Entität hinzuzufügen, erstellen Sie zunächst ein Hashobjekt, das die Entitätseigenschaften definiert. Beachten Sie, dass Sie für jede Entität **PartitionKey** und **RowKey** angeben müssen. Hierbei handelt es sich um die eindeutigen Bezeichner der Entität und Werte, die viel schneller abgerufen werden können als andere Eigenschaften. Azure Storage verwendet **PartitionKey** , um die Entitäten der Tabelle automatisch über viele Speicherknoten zu verteilen. Entitäten mit dem gleichen **PartitionKey** werden auf dem gleichen Knoten gespeichert. Der **RowKey** -Wert ist eine eindeutige ID der Entität innerhalb der Partition, zu der sie gehört.

```ruby
entity = { "content" => "test entity",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.insert_entity("testtable", entity)
```

## <a name="update-an-entity"></a>Aktualisieren einer Entität
Es sind mehrere Methoden zum Aktualisieren einer vorhandenen Entität vorhanden:

* **update\_entity()**: Aktualisiert eine vorhandene Entität, indem sie ersetzt wird.
* **merge\_Entity()**: Aktualisiert eine vorhandene Entität durch Zusammenführen neuer Eigenschaftswerte mit der vorhandenen Entität.
* **insert\_or\_merge\_entity()**: Aktualisiert eine vorhandene Entität, indem sie ersetzt wird. Wenn keine Entität vorhanden ist, wird eine neue eingefügt:
* **insert\_or\_replace\_entity():** Aktualisiert eine vorhandene Entität durch Zusammenführen neuer Eigenschaftswerte mit der vorhandenen Entität. Wenn keine Entität vorhanden ist, wird eine neue eingefügt.

Das folgende Beispiel zeigt, wie eine Entität mit **update\_entity()** aktualisiert wird:

```ruby
entity = { "content" => "test entity with updated content",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.update_entity("testtable", entity)
```

Mit **update\_entity()** und **merge\_entity()** schlägt der Aktualisierungsvorgang fehl, wenn die zu aktualisierende Entität nicht vorhanden ist. Daher sollten Sie **insert\_or\_replace\_entity()** oder **insert\_or\_merge\_entity()** verwenden, wenn Sie eine Entität unabhängig davon speichern möchten, ob sie bereits vorhanden ist.

## <a name="work-with-groups-of-entities"></a>Arbeiten mit Gruppen von Entitäten
Gelegentlich ist es sinnvoll, mehrere Vorgänge zusammen in einem Batch zu senden, um die atomische Verarbeitung durch den Server sicherzustellen. Dazu erstellen Sie zunächst ein **Batch**-Objekt und verwenden dann die **execute\_batch()**-Methode für **TableService**. Das folgende Beispiel demonstriert das Senden von zwei Entitäten mit RowKey 2 und 3 in einem Batch. Beachten Sie, dass dies nur für Entitäten mit dem gleichen PartitionKey funktioniert.

```ruby
azure_table_service = Azure::TableService.new
batch = Azure::Storage::Table::Batch.new("testtable",
    "test-partition-key") do
    insert "2", { "content" => "new content 2" }
    insert "3", { "content" => "new content 3" }
end
results = azure_table_service.execute_batch(batch)
```

## <a name="query-for-an-entity"></a>Abfragen einer Entität
Um eine Entität in einer Tabelle abzufragen, verwenden Sie die **get\_entity()**-Methode und übergeben ihr **PartitionKey** und **RowKey**.

```ruby
result = azure_table_service.get_entity("testtable", "test-partition-key",
    "1")
```

## <a name="query-a-set-of-entities"></a>Abfragen einer Gruppe von Entitäten
Um eine Gruppe von Entitäten in einer Tabelle abzufragen, erstellen Sie ein Hashobjekt und verwenden die **query\_entities()**-Methode. Das folgende Beispiel demonstriert das Abrufen aller Entitäten mit dem gleichen **PartitionKey**:

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'" }
result, token = azure_table_service.query_entities("testtable", query)
```

> [!NOTE]
> Wenn die Ergebnisgruppe zu groß für die Rückgabe einer einzelnen Abfrage ist, wird ein Fortsetzungstoken zurückgegeben, mit dem Sie nachfolgende Seiten abrufen können.
>
>

## <a name="query-a-subset-of-entity-properties"></a>Abfragen einer Teilmenge von Entitätseigenschaften
Mit einer Abfrage einer Tabelle können nur einige wenige Eigenschaften einer Entität aufgerufen werden. Bei dieser Methode, der so genannten Projektion, wird die Bandbreite reduziert und die Abfrageleistung gesteigert, vor allem bei großen Entitäten. Verwenden Sie die select-Klausel, und übergeben Sie die Namen der Eigenschaften, die an den Client übermittelt werden sollen.

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'",
    :select => ["content"] }
result, token = azure_table_service.query_entities("testtable", query)
```

## <a name="delete-an-entity"></a>Löschen einer Entität
Verwenden Sie die **delete\_entity()**-Methode, um eine Entität zu löschen. Sie müssen den Namen der Tabelle mit der Entität, dem PartitionKey und dem RowKey der Entität übergeben.

```ruby
azure_table_service.delete_entity("testtable", "test-partition-key", "1")
```

## <a name="delete-a-table"></a>Löschen einer Tabelle
Um eine Tabelle zu löschen, verwenden Sie die **delete\_table()**-Methode, und übergeben Sie den Namen der zu löschenden Tabelle.

```ruby
azure_table_service.delete_table("testtable")
```

## <a name="next-steps"></a>Nächste Schritte

* Beim [Microsoft Azure-Speicher-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) handelt es sich um eine kostenlose eigenständige App von Microsoft, über die Sie ganz einfach visuell mit Azure Storage-Daten arbeiten können – unter Windows, MacOS und Linux.
* [Azure SDK for Ruby](http://github.com/WindowsAzure/azure-sdk-for-ruby) -Repository auf GitHub

