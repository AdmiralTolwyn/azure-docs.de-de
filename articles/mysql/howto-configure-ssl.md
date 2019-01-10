---
title: Konfigurieren von SSL-Verbindungen für eine sichere Verbindung mit Azure Database for MySQL
description: Anweisungen zum ordnungsgemäßen Konfigurieren der Azure-Datenbank für MySQL und zugehörigen Anwendungen für die richtige Verwendung von SSL-Verbindungen
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 075f20027153eb9adf5c0daedea7cf5c0b515ee4
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53537034"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Konfigurieren von SSL-Verbindungen in der Anwendung für eine sichere Verbindung mit der Azure-Datenbank für MySQL
Azure-Datenbank für MySQL unterstützt die Verbindung Ihres Servers mit Azure-Datenbank für MySQL mit Clientanwendungen, die Secure Sockets Layer (SSL) verwenden. Das Erzwingen von SSL-Verbindungen zwischen dem Datenbankserver und Clientanwendungen trägt zum Schutz vor Man-in-the-Middle-Angriffen bei, indem der Datenstrom zwischen dem Server und der Anwendung verschlüsselt wird.

## <a name="step-1-obtain-ssl-certificate"></a>Schritt 1: Abrufen eines SSL-Zertifikats
Laden Sie das Zertifikat, das für die SSL-Kommunikation mit Ihrem Azure Database for MySQL-Server erforderlich ist, von [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) herunter, und speichern Sie die Zertifikatdatei auf dem lokalen Laufwerk. (In diesem Tutorial wird als Beispiel „c:\ssl“ verwendet.)
**Für Microsoft Internet Explorer und Microsoft Edge**: Benennen Sie nach dem Download das Zertifikat in „BaltimoreCyberTrustRoot.crt.pem“ um.

## <a name="step-2-bind-ssl"></a>Schritt 2: Binden von SSL
### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>Herstellen einer Verbindung mit dem Server mithilfe von MySQL Workbench über SSL
Konfigurieren Sie MySQL Workbench für eine sichere Verbindung über SSL. Navigieren Sie im Dialogfeld „Setup New Connection“ (Neue Verbindung einrichten) zur Registerkarte **SSL**. Geben Sie in das Feld für die **SSL-CA-Datei** den Speicherort der Datei **BaltimoreCyberTrustRoot.crt.pem** ein. 
![Speichern der benutzerdefinierten Kachel](./media/howto-configure-ssl/mysql-workbench-ssl.png): Für vorhandene Verbindungen können Sie SSL binden, indem Sie mit der rechten Maustaste auf das Verbindungssymbol klicken und dann „Bearbeiten“ auswählen. Navigieren Sie dann zur Registerkarte **SSL** und binden die Zertifikatsdatei.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Herstellen einer Verbindung mit dem Server mithilfe der MySQL-CLI über SSL
Eine weitere Möglichkeit zum Binden des SSL-Zertifikats besteht im Verwenden der MySQL-Befehlszeilenschnittstelle und Ausführen des folgenden Befehls:
```dos
mysql.exe -h mydemoserver.mysql.database.azure.com -u Username@mydemoserver -p --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>Schritt 3:  Erzwingen von SSL-Verbindungen in Azure 
### <a name="using-the-azure-portal"></a>Verwenden des Azure-Portals
Rufen Sie über das Azure-Portal Ihren Server mit Azure Database for MySQL auf, und klicken Sie auf **Verbindungssicherheit**. Verwenden Sie die Umschaltfläche, um die Einstellung **SSL-Verbindung erzwingen** zu aktivieren/deaktivieren, und klicken Sie dann auf **Speichern**. Microsoft empfiehlt, die Einstellung **SSL-Verbindung erzwingen** immer zu aktivieren, um die Sicherheit zu erhöhen.
![enable-ssl](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle
Sie können den **ssl-enforcement**-Parameter in der Azure-Befehlszeilenschnittstelle mit den entsprechenden Werten aktivieren oder deaktivieren.
```azurecli-interactive
az mysql server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>Schritt 4: Überprüfen der SSL-Verbindung
Führen Sie die MySQL-Befehl **status** aus, um sicherzustellen, dass die Verbindung mit dem MySQL-Server über SSL hergestellt wurde:
```dos
mysql> status
```
Um sicherzustellen, dass die Verbindung verschlüsselt ist, überprüfen Sie die Ausgabe, die Folgendes anzeigen sollte:  **SSL: Verwendete Verschlüsselung ist AES256-SHA** 

## <a name="sample-code"></a>Beispielcode
Um in Ihrer Anwendung über SSL eine sichere Verbindung mit Azure Database for MySQL einzurichten, können Sie die folgenden Codebeispiele verwenden:

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)
```python
try:
    conn=mysql.connector.connect(user='myadmin@mydemoserver', 
        password='yourpassword', 
        database='quickstartdb', 
        host='mydemoserver.mysql.database.azure.com', 
        ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```
### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user = 'myadmin@mydemoserver', 
        password = 'yourpassword', 
        database = 'quickstartdb', 
        host = 'mydemoserver.mysql.database.azure.com', 
        ssl = {'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}})
```
### <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com', 
        :username => 'myadmin@mydemoserver',      
        :password => 'yourpassword',    
        :database => 'quickstartdb',
        :ssl_ca => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
    )
```
### <a name="golang"></a>Golang
```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/BaltimoreCyberTrustRoot.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@mydemoserver' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')   
db, _ := sql.Open("mysql", connectionString)
```
### <a name="javajdbc"></a>JAVA(JDBC)
```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " + 
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " + 
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore 
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```
### <a name="javamariadb"></a>JAVA(MariaDB)
```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " + 
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " + 
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore 
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

## <a name="next-steps"></a>Nächste Schritte
Überprüfen verschiedener Anwendungsverbindungsoptionen gemäß [Datenverbindungsbibliotheken für die Azure-Datenbank für MySQL](concepts-connection-libraries.md)
