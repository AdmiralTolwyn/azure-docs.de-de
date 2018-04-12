---
title: 'VS Code: Herstellen der Verbindung und Abfragen von Daten in Azure SQL-Datenbank | Microsoft-Dokumentation'
description: Es wird beschrieben, wie Sie mit Visual Studio Code eine Verbindung mit SQL-Datenbank in Azure herstellen. Führen Sie anschließend T-SQL-Anweisungen (Transact-SQL) aus, um Daten abzufragen und zu bearbeiten.
keywords: Herstellen einer Verbindung mit SQL-Datenbank
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,DBs & servers
ms.topic: quickstart
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 43cdbf84b40a577732787b06b4dd6a936009dd92
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2018
---
# <a name="azure-sql-database-use-visual-studio-code-to-connect-and-query-data"></a>Azure SQL-Datenbank: Verwenden von Visual Studio Code zum Herstellen einer Verbindung mit und Abfragen von Daten

[Visual Studio Code](https://code.visualstudio.com/docs) ist ein grafischer Code-Editor für Linux, Mac OS und Windows, der Erweiterungen unterstützt, z.B. die [mssql-Erweiterung](https://aka.ms/mssql-marketplace) zum Abfragen von Microsoft SQL Server, Azure SQL-Datenbank und SQL Data Warehouse. In diesem Schnellstarttutorial wird veranschaulicht, wie Sie Visual Studio Code zum Herstellen einer Verbindung mit einer Azure SQL-Datenbank verwenden und anschließend Transact-SQL-Anweisungen nutzen, um Daten in der Datenbank abzufragen, zu aktualisieren und zu löschen bzw. in diese einzufügen.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Schnellstart werden als Ausgangspunkt die Ressourcen verwendet, die in einem der folgenden Schnellstarts erstellt wurden:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

#### <a name="install-vs-code"></a>Installieren von VSCode

Stellen Sie zunächst sicher, dass Sie die neueste Version von [Visual Studio Code](https://code.visualstudio.com/Download) installiert und die [mssql-Erweiterung](https://aka.ms/mssql-marketplace) geladen haben. Eine Installationsanleitung für die mssql-Erweiterung finden Sie unter [Install VS Code](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-vs-code) (Installieren von VS Code) und [mssql for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql) (mssql für Visual Studio Code). 

## <a name="configure-vs-code"></a>Konfigurieren von VS Code 

### <a name="mac-os"></a>**Mac OS**
Für Mac OS müssen Sie OpenSSL installieren, da dies eine Voraussetzung für DotNet Core ist. DotNet Core wird von der mssql-Erweiterung verwendet. Öffnen Sie Ihr Terminal, und geben Sie die folgenden Befehle ein, um **brew** und **OpenSSL** zu installieren. 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install openssl
mkdir -p /usr/local/lib
ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**

Keine besondere Konfiguration erforderlich.

### <a name="windows"></a>**Windows**

Keine besondere Konfiguration erforderlich.

## <a name="sql-server-connection-information"></a>SQL Server-Verbindungsinformationen

Rufen Sie die Verbindungsinformationen ab, die zum Herstellen einer Verbindung mit der Azure SQL-Datenbank erforderlich sind. In den weiteren Verfahren benötigen Sie den vollqualifizierten Servernamen, den Datenbanknamen und die Anmeldeinformationen.

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="set-language-mode-to-sql"></a>Festlegen des Sprachmodus auf SQL

Legen Sie den Sprachmodus in Visual Studio Code auf **SQL** fest, um die Verwendung von mssql-Befehlen und T-SQL IntelliSense zu ermöglichen.

1. Öffnen Sie ein neues Visual Studio Code-Fenster. 

2. Klicken Sie in der unteren rechten Ecke der Statusleiste auf **Nur Text**.
3. Geben Sie im angezeigten Dropdownmenü **Sprachmodus auswählen** den Modus **SQL** ein, und drücken Sie anschließend die **EINGABETASTE**, um das Festlegen des Sprachmodus auf „SQL“ zu bestätigen. 

   ![Sprachmodus „SQL“](./media/sql-database-connect-query-vscode/vscode-language-mode.png)

## <a name="connect-to-your-database"></a>Verbinden mit der Datenbank

Verwenden Sie Visual Studio Code, um eine Verbindung mit Ihrem Azure SQL-Datenbankserver herzustellen.

> [!IMPORTANT]
> Stellen Sie vor dem Fortfahren sicher, dass Sie über alle Informationen zu Server, Datenbank und Anmeldung verfügen. Wenn Sie mit dem Eingeben der Informationen für das Verbindungsprofil begonnen haben und zu Visual Studio Code wechseln, müssen Sie mit dem Erstellen des Verbindungsprofils neu beginnen.
>

1. Drücken Sie in VS Code die Tastenkombination **STRG+UMSCHALT+P** (oder **F1**), um die Befehlspalette zu öffnen.

2. Geben Sie **sqlcon** ein, und drücken Sie die **EINGABETASTE**.

3. Drücken Sie die **EINGABETASTE**, um die Option **Create Connection Profile** (Verbindungsprofil erstellen) auszuwählen. Ein Verbindungsprofil für Ihre SQL Server-Instanz wird erstellt.

4. Befolgen Sie die Eingabeaufforderungen, um die Verbindungseigenschaften für das neue Verbindungsprofil anzugeben. Drücken Sie nach dem Angeben der einzelnen Werte die **EINGABETASTE**, um fortzufahren. 

   | Einstellung       | Empfohlener Wert | BESCHREIBUNG |
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Servername | Der vollqualifizierte Servername | Der Name sollte etwa wie folgt lauten: **mynewserver20170313.database.windows.net**. |
   | **Datenbankname** | mySampleDatabase | Der Name der Datenbank, mit der eine Verbindung hergestellt werden soll. |
   | **Authentifizierung** | SQL-Anmeldung| In diesem Tutorial haben wir als einzigen Authentifizierungstyp die SQL-Authentifizierung konfiguriert. |
   | **Benutzername** | Das Serveradministratorkonto | Hierbei handelt es sich um das Konto, das Sie beim Erstellen des Servers angegeben haben. |
   | **Kennwort (SQL-Anmeldung)** | Das Kennwort für das Serveradministratorkonto | Hierbei handelt es sich um das Kennwort, das Sie beim Erstellen des Servers angegeben haben. |
   | **Kennwort speichern?** | Ja oder nein | Wählen Sie „Ja“ aus, wenn Sie das Kennwort nicht jedes Mal erneut eingeben möchten. |
   | **Geben Sie einen Namen für dieses Profil ein.** | Ein Profilname, z.B. **mySampleDatabase** | Ein gespeicherter Profilname beschleunigt die Verbindungsherstellung bei nachfolgenden Anmeldungen. | 

5. Drücken Sie die Taste **ESC**, um die Meldung mit dem Hinweis, dass das Profil erstellt und die Verbindung dafür hergestellt wurde, zu schließen.

6. Überprüfen Sie die Verbindung in der Statusleiste.

   ![Verbindungsstatus](./media/sql-database-connect-query-vscode/vscode-connection-status.png)

## <a name="query-data"></a>Abfragen von Daten

Verwenden Sie den folgenden Code, um mit der Transact-SQL-Anweisung [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) die 20 wichtigsten Produkte nach Kategorie abzufragen.

1. Geben Sie im Fenster **Editor** die folgende Abfrage in das leere Abfragefenster ein:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2. Drücken Sie **STRG+UMSCHALT+E**, um Daten aus den Tabellen „Product“ und „ProductCategory“ abzurufen.

    ![Abfragen](./media/sql-database-connect-query-vscode/query.png)

## <a name="insert-data"></a>Einfügen von Daten

Verwenden Sie den folgenden Code, um mit der Transact-SQL-Anweisung [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) ein neues Produkt in die Tabelle „SalesLT.Product“ einzufügen.

1. Löschen Sie im Fenster **Editor** die vorherige Abfrage, und geben Sie die folgende Abfrage ein:

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. Drücken Sie **STRG+UMSCHALT+E**, um eine neue Zeile in die Tabelle „Product“ einzufügen.

## <a name="update-data"></a>Aktualisieren von Daten

Verwenden Sie den folgenden Code, um das neue Produkt zu aktualisieren, das Sie zuvor mit Transact-SQL-Anweisung [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) hinzugefügt haben.

1.  Löschen Sie im Fenster **Editor** die vorherige Abfrage, und geben Sie die folgende Abfrage ein:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Drücken Sie **STRG+UMSCHALT+E**, um die angegebene Zeile in der Tabelle „Product“ zu aktualisieren.

## <a name="delete-data"></a>Löschen von Daten

Verwenden Sie den folgenden Code, um mit der Transact-SQL-Anweisung [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) das neue Produkt zu löschen, das Sie zuvor hinzugefügt haben.

1. Löschen Sie im Fenster **Editor** die vorherige Abfrage, und geben Sie die folgende Abfrage ein:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Drücken Sie **STRG+UMSCHALT+E**, um die angegebene Zeile in der Tabelle „Product“ zu löschen.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Herstellen einer Verbindung und Durchführen von Abfragen mit SQL Server Management Studio finden Sie unter [Verwenden von SQL Server Management Studio zum Herstellen der Verbindung und Abfragen von Daten](sql-database-connect-query-ssms.md).
- Informationen zum Herstellen einer Verbindung sowie zum Abfragen mithilfe des Azure-Portals finden Sie unter [Verbinden und Abfragen mit dem SQL-Abfrage-Editor des Azure-Portals](sql-database-connect-query-portal.md).
- Im [Blogpost zum Erstellen einer Datenbank-IDE mit der MSSQL-Erweiterung](https://msdn.microsoft.com/magazine/mt809115) finden Sie einen MSDN Magazine-Artikel zum Verwenden von Visual Studio Code.
