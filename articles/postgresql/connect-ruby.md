---
title: Herstellen einer Verbindung mit Azure Database for PostgreSQL per Ruby
description: Dieser Schnellstart enthält ein Ruby-Codebeispiel, das Sie nutzen können, um zu den Daten von Azure-Datenbank für PostgreSQL eine Verbindung herzustellen und Abfragen dafür durchzuführen.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc
ms.devlang: ruby
ms.topic: quickstart
ms.date: 02/28/2018
ms.openlocfilehash: 6748f168624a20e17491a2f84b63b966ce5ad4c6
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53539285"
---
# <a name="azure-database-for-postgresql-use-ruby-to-connect-and-query-data"></a>Azure Database for PostgreSQL: Verwenden von Ruby zum Herstellen einer Verbindung und Abfragen von Daten
Dieser Schnellstart zeigt, wie Sie mit einer [Ruby](https://www.ruby-lang.org)-Anwendung eine Verbindung mit einer Azure-Datenbank für PostgreSQL herstellen. Es wird veranschaulicht, wie Sie SQL-Anweisungen zum Abfragen, Einfügen, Aktualisieren und Löschen von Daten in der Datenbank verwenden. Bei den Schritten in diesem Abschnitt wird davon ausgegangen, dass Sie mit der Ruby-Entwicklung vertraut sind und noch keine Erfahrung mit Azure Database for PostgreSQL haben.

## <a name="prerequisites"></a>Voraussetzungen
In diesem Schnellstart werden die Ressourcen, die in den folgenden Anleitungen erstellt wurden, als Startpunkt verwendet:
- [Erstellen einer Datenbank – Portal](quickstart-create-server-database-portal.md)
- [Erstellen einer Datenbank – Azure CLI](quickstart-create-server-database-azure-cli.md)

## <a name="install-ruby"></a>Installieren von Ruby
Installieren Sie Ruby auf Ihrem eigenen Computer. 

### <a name="windows"></a>Windows
- Laden Sie die aktuelle Version von [Ruby](https://rubyinstaller.org/downloads/) herunter, und installieren Sie sie.
- Aktivieren Sie auf dem Abschlussbildschirm des MSI-Installers das Kontrollkästchen „Run 'ridk install' to install MSYS2 and development toolchain“ („ridk install“ ausführen, um MSYS2 und die Entwicklungstoolkette zu installieren). Klicken Sie anschließend auf **Finish** (Fertig stellen), um den nächsten Installer zu starten.
- Der RubyInstaller2-Installer für Windows wird gestartet. Geben Sie „2“ ein, um das MSYS2-Repositoryupdate zu installieren. Schließen Sie das Befehlsfenster, nachdem der Vorgang abgeschlossen wurde und die Installationseingabeaufforderung wieder angezeigt wird.
- Starten Sie über das Menü „Start“ eine neue Eingabeaufforderung (cmd).
- Testen Sie die Ruby-Installation per `ruby -v`, um die installierte Version anzuzeigen.
- Testen Sie die Gem-Installation per `gem -v`, um die installierte Version anzuzeigen.
- Erstellen Sie das PostgreSQL-Modul für Ruby, indem Sie Gem mit dem Befehl `gem install pg` ausführen.

### <a name="macos"></a>macOS
- Installieren Sie Ruby per Homebrew, indem Sie den Befehl `brew install ruby` ausführen. Weitere Installationsoptionen finden Sie in der [Installationsdokumentation](https://www.ruby-lang.org/en/documentation/installation/#homebrew) zu Ruby.
- Testen Sie die Ruby-Installation per `ruby -v`, um die installierte Version anzuzeigen.
- Testen Sie die Gem-Installation per `gem -v`, um die installierte Version anzuzeigen.
- Erstellen Sie das PostgreSQL-Modul für Ruby, indem Sie Gem mit dem Befehl `gem install pg` ausführen.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
- Installieren Sie Ruby, indem Sie den Befehl `sudo apt-get install ruby-full` ausführen. Weitere Installationsoptionen finden Sie in der [Installationsdokumentation](https://www.ruby-lang.org/en/documentation/installation/) zu Ruby.
- Testen Sie die Ruby-Installation per `ruby -v`, um die installierte Version anzuzeigen.
- Installieren Sie die neuesten Updates für Gem, indem Sie den Befehl `sudo gem update --system` ausführen.
- Testen Sie die Gem-Installation per `gem -v`, um die installierte Version anzuzeigen.
- Installieren Sie gcc, make und andere Buildtools, indem Sie den Befehl `sudo apt-get install build-essential` ausführen.
- Installieren Sie die PostgreSQL-Bibliotheken, indem Sie den Befehl `sudo apt-get install libpq-dev` ausführen.
- Erstellen Sie das Ruby-pg-Modul mithilfe von Gem, indem Sie den Befehl `sudo gem install pg` ausführen.

## <a name="run-ruby-code"></a>Ausführen von Ruby-Code 
- Speichern Sie den Code in einer Textdatei mit Dateierweiterung .rb, und speichern Sie die Datei in einem Projektordner wie z.B. `C:\rubypostgres\read.rb` oder `/home/username/rubypostgres/read.rb`.
- Starten Sie zum Ausführen des Codes die Eingabeaufforderung oder die Bash-Shell. Wechseln Sie mit `cd rubypostgres` in Ihren Projektordner, und geben Sie dann den Befehl `ruby read.rb` ein, um die Anwendung auszuführen.

## <a name="get-connection-information"></a>Abrufen von Verbindungsinformationen
Rufen Sie die Verbindungsinformationen ab, die zum Herstellen einer Verbindung mit der Azure-Datenbank für PostgreSQL erforderlich sind. Sie benötigen den vollqualifizierten Servernamen und die Anmeldeinformationen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)an.
2. Klicken Sie im Azure-Portal im linken Menü auf **Alle Ressourcen**, und suchen Sie dann nach dem soeben erstellten Server, z.B. **mydemoserver**.
3. Klicken Sie auf den Servernamen.
4. Notieren Sie sich im Bereich **Übersicht** des Servers den **Servernamen** und den **Anmeldenamen des Serveradministrators**. Wenn Sie Ihr Kennwort vergessen haben, können Sie es in diesem Bereich auch zurücksetzen.
 ![Azure Database for PostgreSQL-Servername](./media/connect-ruby/1-connection-string.png)

## <a name="connect-and-create-a-table"></a>Herstellen einer Verbindung und Erstellen einer Tabelle
Verwenden Sie den folgenden Code, um eine Verbindung herzustellen und eine Tabelle zu erstellen, indem Sie eine **CREATE TABLE**-SQL-Anweisung gefolgt von **INSERT INTO**-SQL-Anweisungen zum Hinzufügen von Zeilen zur Tabelle nutzen.

Der Code verwendet ein [PG::Connection](https://www.rubydoc.info/gems/pg/PG/Connection)-Objekt mit dem Konstruktor [new()](https://www.rubydoc.info/gems/pg/PG%2FConnection:initialize), um eine Verbindung mit Azure-Datenbank für PostgreSQL herzustellen. Anschließend wird die [exec()](https://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method)-Methode aufgerufen, um die Befehle DROP, CREATE TABLE und INSERT INTO auszuführen. Der Code führt mit der [PG::Error](https://www.rubydoc.info/gems/pg/PG/Error)-Klasse eine Überprüfung auf Fehler durch. Anschließend wird die [close()](https://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method)-Methode aufgerufen, um die Verbindung vor dem Beenden zu schließen.

Ersetzen Sie die Zeichenfolgen `host`, `database`, `user` und `password` durch Ihre eigenen Werte. 
```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin@mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database'

    # Drop previous table of same name if one exists
    connection.exec('DROP TABLE IF EXISTS inventory;')
    puts 'Finished dropping table (if existed).'

    # Drop previous table of same name if one exists.
    connection.exec('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);')
    puts 'Finished creating table.'

    # Insert some data into table.
    connection.exec("INSERT INTO inventory VALUES(1, 'banana', 150)")
    connection.exec("INSERT INTO inventory VALUES(2, 'orange', 154)")
    connection.exec("INSERT INTO inventory VALUES(3, 'apple', 100)")
    puts 'Inserted 3 rows of data.'

rescue PG::Error => e
    puts e.message 
    
ensure
    connection.close if connection
end
```

## <a name="read-data"></a>Lesen von Daten
Verwenden Sie den folgenden Code, um die Daten mit einer **SELECT**-SQL-Anweisung zu verbinden und zu lesen. 

Der Code verwendet ein [PG::Connection](https://www.rubydoc.info/gems/pg/PG/Connection)-Objekt mit dem Konstruktor [new()](https://www.rubydoc.info/gems/pg/PG%2FConnection:initialize), um eine Verbindung mit Azure-Datenbank für PostgreSQL herzustellen. Anschließend wird die [exec()](https://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method)-Methode aufgerufen, um den SELECT-Befehl auszuführen, und die Ergebnisse werden in einem Resultset vorgehalten. Die Resultset-Sammlung wird mit der Schleife `resultSet.each do` durchlaufen, wobei sich die aktuellen Zeilenwerte in der Variablen `row` befinden. Der Code führt mit der [PG::Error](https://www.rubydoc.info/gems/pg/PG/Error)-Klasse eine Überprüfung auf Fehler durch. Anschließend wird die [close()](https://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method)-Methode aufgerufen, um die Verbindung vor dem Beenden zu schließen.

Ersetzen Sie die Zeichenfolgen `host`, `database`, `user` und `password` durch Ihre eigenen Werte. 

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin@mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :database => dbname, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    resultSet = connection.exec('SELECT * from inventory;')
    resultSet.each do |row|
        puts 'Data row = (%s, %s, %s)' % [row['id'], row['name'], row['quantity']]
    end

rescue PG::Error => e
    puts e.message 
    
ensure
    connection.close if connection
end
```

## <a name="update-data"></a>Aktualisieren von Daten
Verwenden Sie den folgenden Code, um eine Verbindung herzustellen und die Daten per **UPDATE**-SQL-Anweisung zu aktualisieren.

Der Code verwendet ein [PG::Connection](https://www.rubydoc.info/gems/pg/PG/Connection)-Objekt mit dem Konstruktor [new()](https://www.rubydoc.info/gems/pg/PG%2FConnection:initialize), um eine Verbindung mit Azure-Datenbank für PostgreSQL herzustellen. Anschließend wird die [exec()](https://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method)-Methode aufgerufen, um den UPDATE-Befehl auszuführen. Der Code führt mit der [PG::Error](https://www.rubydoc.info/gems/pg/PG/Error)-Klasse eine Überprüfung auf Fehler durch. Anschließend wird die [close()](https://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method)-Methode aufgerufen, um die Verbindung vor dem Beenden zu schließen.

Ersetzen Sie die Zeichenfolgen `host`, `database`, `user` und `password` durch Ihre eigenen Werte. 

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin@mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    # Modify some data in table.
    connection.exec('UPDATE inventory SET quantity = %d WHERE name = %s;' % [200, '\'banana\''])
    puts 'Updated 1 row of data.'

rescue PG::Error => e
    puts e.message 
    
ensure
    connection.close if connection
end
```


## <a name="delete-data"></a>Löschen von Daten
Verwenden Sie den folgenden Code, um die Daten mit einer **DELETE**-SQL-Anweisung zu verbinden und zu lesen. 

Der Code verwendet ein [PG::Connection](https://www.rubydoc.info/gems/pg/PG/Connection)-Objekt mit dem Konstruktor [new()](https://www.rubydoc.info/gems/pg/PG%2FConnection:initialize), um eine Verbindung mit Azure-Datenbank für PostgreSQL herzustellen. Anschließend wird die [exec()](https://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method)-Methode aufgerufen, um den UPDATE-Befehl auszuführen. Der Code führt mit der [PG::Error](https://www.rubydoc.info/gems/pg/PG/Error)-Klasse eine Überprüfung auf Fehler durch. Anschließend wird die [close()](https://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method)-Methode aufgerufen, um die Verbindung vor dem Beenden zu schließen.

Ersetzen Sie die Zeichenfolgen `host`, `database`, `user` und `password` durch Ihre eigenen Werte. 

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin@mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    # Modify some data in table.
    connection.exec('DELETE FROM inventory WHERE name = %s;' % ['\'orange\''])
    puts 'Deleted 1 row of data.'

rescue PG::Error => e
    puts e.message 
    
ensure
    connection.close if connection
end
```

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Migrieren der Datenbank mit Export und Import](./howto-migrate-using-export-and-import.md)
