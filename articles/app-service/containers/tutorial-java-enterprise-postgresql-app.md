---
title: 'Tutorial: Java Enterprise-App unter Linux'
description: Hier erfahren Sie, wie Sie eine Java Enterprise-App in Wildfly in Azure App Service für Linux ausführen, die mit einer PostgreSQL-Datenbank in Azure verbunden ist.
author: JasonFreeberg
ms.devlang: java
ms.topic: tutorial
ms.date: 11/13/2018
ms.author: jafreebe
ms.custom: seodec18
ms.openlocfilehash: 84f22d52e9a92707a26a4e64f194e82cca87757d
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687438"
---
# <a name="tutorial-build-a-java-ee-and-postgres-web-app-in-azure"></a>Tutorial: Erstellen einer Java EE- und Postgres-Web-App in Azure

In diesem Tutorial erfahren Sie, wie Sie in Azure App Service eine Java Enterprise Edition (EE)-Web-App erstellen und mit einer Postgres-Datenbank verbinden. Anschließend besitzen Sie eine [WildFly](https://www.wildfly.org/about/)-Anwendung, die Daten in [Azure-Datenbank für Postgres](https://azure.microsoft.com/services/postgresql/) speichert und mit Azure [App Service unter Linux](app-service-linux-intro.md) ausgeführt wird.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Bereitstellen einer Java EE-App in Azure mithilfe von Maven
> * Erstellen einer Postgres-Datenbank in Azure
> * Konfigurieren der WildFly-Server für die Verwendung von Postgres
> * Aktualisieren und erneutes Bereitstellen der App
> * Ausführen von Komponententests für WildFly

## <a name="prerequisites"></a>Voraussetzungen

1. [Herunterladen und Installieren von Git](https://git-scm.com/)
2. [Herunterladen und Installieren von Maven 3](https://maven.apache.org/install.html)
3. [Herunterladen und Installieren der Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)

## <a name="clone-and-edit-the-sample-app"></a>Klonen und Bearbeiten der Beispiel-App

In diesem Schritt klonen Sie die Beispielanwendung und konfigurieren das Maven-Projektobjektmodell (POM oder *pom.xml*) für die Bereitstellung.

### <a name="clone-the-sample"></a>Klonen des Beispiels

Navigieren Sie im Terminalfenster zu einem Arbeitsverzeichnis, und klonen Sie [das Beispielrepository](https://github.com/Azure-Samples/wildfly-petstore-quickstart).

```bash
git clone https://github.com/Azure-Samples/wildfly-petstore-quickstart.git
```

### <a name="update-the-maven-pom"></a>Aktualisieren der Maven-POM-Datei

Aktualisieren Sie das Maven-Azure-Plug-In mit dem gewünschten Namen und der Ressourcengruppe Ihrer App Service-Instanz. Sie müssen den App Service-Plan oder die Instanz nicht im Voraus erstellen. Das Maven-Plug-in erstellt die Ressourcengruppe und die App Service-Instanz, wenn sie nicht bereits vorhanden ist.

Sie können nach unten zum Abschnitt `<plugins>` von *pom.xml* in Zeile 200 scrollen, um die Änderungen vorzunehmen.

```xml
<!-- Azure App Service Maven plugin for deployment -->
<plugin>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-webapp-maven-plugin</artifactId>
  <version>${version.maven.azure.plugin}</version>
  <configuration>
    <appName>YOUR_APP_NAME</appName>
    <resourceGroup>YOUR_RESOURCE_GROUP</resourceGroup>
    <linuxRuntime>wildfly 14-jre8</linuxRuntime>
  ...
</plugin>  
```

Ersetzen Sie `YOUR_APP_NAME` und `YOUR_RESOURCE_GROUP` durch die Namen Ihrer App Service-Instanz und Ressourcengruppe.

## <a name="build-and-deploy-the-application"></a>Erstellen und Bereitstellen der Anwendung

Wir verwenden jetzt Maven, um unsere Anwendung zu erstellen und in App Service bereitzustellen.

### <a name="build-the-war-file"></a>Erstellen der WAR-Datei

Das POM in diesem Projekt ist so konfiguriert, dass die Anwendung in eine Webarchivdatei (WAR) gepackt wird. Erstellen der Anwendung mithilfe von Maven:

```bash
mvn clean install -DskipTests
```

Die Testfälle in dieser Anwendung sollen ausgeführt werden, wenn die Anwendung auf WildFly bereitgestellt wird. Wir überspringen die Tests, um lokal zu erstellen, und Sie führen die Tests aus, sobald die Anwendung in App Service bereitgestellt ist.

### <a name="deploy-to-app-service"></a>Bereitstellen für App Service

Da die WAR-Datei jetzt bereit ist, können wir mithilfe des Azure-Plug-Ins in App Service bereitstellen:

```bash
mvn azure-webapp:deploy
```

Wenn die Bereitstellung abgeschlossen ist, fahren Sie mit dem nächsten Schritt fort.

### <a name="create-a-record"></a>Erstellen eines Datensatzes

Öffnen Sie einen Browser, und navigieren Sie zu `https://<your_app_name>.azurewebsites.net/`. Herzlichen Glückwunsch! Sie haben eine Java EE-Anwendung in Azure App Service bereitgestellt.

An diesem Punkt verwendet die Anwendung eine In-Memory Database (H2). Klicken Sie auf der Navigationsleiste auf „Administrator“, und erstellen Sie eine neue Kategorie. Der Datensatz in Ihrer In-Memory Database geht verloren, wenn Sie Ihre App Service-Instanz neu starten. In den folgenden Schritten beheben Sie dieses Problem, indem Sie eine Postgres-Datenbank in Azure bereitstellen und WildFly für deren Verwendung konfigurieren.

![Position der Administrator-Schaltfläche](media/tutorial-java-enterprise-postgresql-app/admin_button.JPG)

## <a name="provision-a-postgres-database"></a>Bereitstellen einer Postgres-Datenbank

Um einen Postgres-Datenbankserver bereitzustellen, öffnen Sie ein Terminal, und verwenden Sie den Befehl [az postgres server create](https://docs.microsoft.com/cli/azure/postgres/server), wie im folgenden Beispiel gezeigt. Ersetzen Sie die Platzhalter (einschließlich der spitzen Klammern) unter Verwendung derselben Ressourcengruppe, die Sie zuvor für Ihre App Service-Instanz bereitgestellt haben, durch Werte Ihrer Wahl. Die von Ihnen angegebenen Administratoranmeldeinformationen ermöglichen den zukünftigen Zugriff, daher sollten Sie sich diese für die spätere Verwendung notieren.

```bash
az postgres server create \
    --name <server name> \
    --resource-group <resource group> \
    --location <location>
    --sku-name GP_Gen5_2 \
    --admin-user <administrator username> \
    --admin-password <administrator password> \
```

Nachdem Sie diesen Befehl ausgeführt haben, navigieren Sie zum Azure-Portal und zu Ihrer Postgres-Datenbank. Wenn das Blatt geladen ist, kopieren Sie die Werte von „Servername“ und „Anmeldename des Serveradministrators“. Diese benötigen Sie später.

### <a name="allow-access-to-azure-services"></a>Zugriff auf Azure-Dienste zulassen

Im Bereich **Verbindungssicherheit** des Blatts „Azure-Datenbank“ setzen Sie die Schaltfläche „Zugriff auf Azure-Dienste zulassen“ auf die Position **EIN**.

![Zulassen des Zugriffs auf Azure-Dienste](media/tutorial-java-enterprise-postgresql-app/postgress_button.JPG)

## <a name="update-your-java-app-for-postgres"></a>Aktualisieren Ihrer Java-App für Postgres

Wir nehmen jetzt einige Änderungen an der Java-Anwendung vor, damit sie unsere Postgres-Datenbank verwenden kann.

### <a name="add-postgres-credentials-to-the-pom"></a>Hinzufügen von Postgres-Anmeldeinformationen in der POM-Datei

Ersetzen Sie in *pom.xml* die Platzhalterwerte in Großbuchstaben durch Ihren Postgres-Servernamen, den Anmeldenamen und das Kennwort des Administrators. Diese Felder befinden sich innerhalb des Azure-Maven-Plug-Ins. (Achten Sie darauf `YOUR_SERVER_NAME`, `YOUR_PG_USERNAME` und `YOUR_PG_PASSWORD` in den `<value>`-Tags zu ersetzen, nicht in den `<name>`-Tags!)

```xml
<plugin>
      ...
      <appSettings>
      <property>
        <name>POSTGRES_CONNECTIONURL</name>
        <value>jdbc:postgresql://YOUR_SERVER_NAME:5432/postgres?ssl=true</value>
      </property>
      <property>
        <name>POSTGRES_USERNAME</name>
        <value>YOUR_PG_USERNAME</value>
      </property>
      <property>
        <name>POSTGRES_PASSWORD</name>
        <value>YOUR_PG_PASSWORD</value>
      </property>
    </appSettings>
  </configuration>
</plugin>
```

### <a name="update-the-java-transaction-api"></a>Aktualisieren der Java-Transaktions-API

Als Nächstes müssen wir die Konfiguration unserer Java-Transaktions-API so bearbeiten, dass unsere Java-Anwendung mit Postgres anstelle der In-Memory H2-Database kommuniziert, die wir zuvor verwendet haben. Öffnen Sie *src/main/resources/META-INF/persistence.xml* in einem Editor. Ersetzen Sie den Wert für `<jta-data-source>` durch `java:jboss/datasources/postgresDS`. Ihr JTA-XML sollte jetzt diese Einstellung aufweisen:

```xml
<jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
```

## <a name="configure-the-wildfly-application-server"></a>Konfigurieren des WildFly-Anwendungsservers

Vor der Bereitstellung der neu konfigurierten Anwendung müssen wir den WildFly-Anwendungsserver mit dem Postgres-Modul und den zugehörigen Abhängigkeiten aktualisieren. Weitere Informationen zur Konfiguration finden Sie im Artikel zur [Konfiguration des WildFly-Servers](configure-language-java.md#configure-java-ee-wildfly).

Zum Konfigurieren des Servers benötigen wir die vier Dateien im Verzeichnis *wildfly_config/* :

- **postgresql-42.2.5.jar:** Diese JAR-Datei ist der JDBC-Treiber für Postgres. Weitere Informationen finden Sie auf der [offiziellen Website](https://jdbc.postgresql.org/index.html).
- **postgres-module.xml:** Diese XML-Datei deklariert einen Namen für das Postgres-Modul (org.postgres). Sie gibt auch die erforderlichen Ressourcen und Abhängigkeiten für die Verwendung des Moduls an.
- **jboss_cli_commands.cli**: Diese Datei enthält Konfigurationsbefehle, die von der JBoss CLI ausgeführt werden. Die Befehle fügen das Postgres-Modul dem WildFly-Anwendungsserver hinzu, stellen die Anmeldeinformationen bereit, deklarieren einen JNDI-Namen, legen den Schwellenwert für das Zeitlimit fest usw. Wenn Sie nicht mit der JBoss CLI vertraut sind, informieren Sie sich in der [offiziellen Dokumentation](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- **startup_script.sh:** Dieses Shellskript wird bei jedem Starten Ihrer App Service-Instanz ausgeführt. Das Skript führt nur eine Funktion aus: Übergeben der Befehle in *jboss_cli_commands.cli* an die JBoss-CLI.

Es wird dringend empfohlen, die Inhalte dieser Dateien zu lesen, insbesondere *jboss_cli_commands.cli*.

### <a name="ftp-the-configuration-files"></a>Übertragen der Konfigurationsdateien per FTP

Wir müssen die Inhalte von *wildfly_config/* per FTP an unsere App Service-Instanz übertragen. Zum Abrufen Ihrer FTP-Anmeldeinformationen klicken Sie im Azure-Portal auf dem Blatt „App Service“ auf die Schaltfläche **Veröffentlichungsprofil abrufen**. Ihr FTP-Benutzername und das Kennwort sind im heruntergeladenen XML-Dokument enthalten. Weitere Informationen zum Veröffentlichungsprofil finden Sie in [diesem Dokument](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials).

Übertragen Sie mit einem FTP-Tool Ihrer Wahl die vier Dateien in *wildfly_config/* nach */home/site/deployments/tools/* . (Hinweis: Übertragen Sie nicht das Verzeichnis, nur die Dateien selbst.)

### <a name="finalize-app-service"></a>Abschließen von App Service

Navigieren Sie auf dem Blatt „App Service“ zum Bereich „Anwendungseinstellungen“. Legen Sie unter „Runtime“ für das Feld „Startdatei“ */home/site/deployments/tools/startup_script.sh* fest. Dadurch wird sichergestellt, dass das Shellskript nach der Erstellung der App Service-Instanz, aber vor dem Starten des WildFly-Server ausgeführt wird.

Starten Sie dann Ihre App Service-Instanz neu. Die Schaltfläche befindet sich im Bereich „Übersicht“.

## <a name="redeploy-the-application"></a>Erneutes Bereitstellen der Anwendung

Öffnen Sie ein Terminalfenster, um Ihre Anwendung neu zu erstellen und erneut bereitzustellen.

```bash
mvn clean install -DskipTests azure-webapp:deploy
```

Glückwunsch! Ihre Anwendung verwendet nun eine Postgres-Datenbank, und alle in der Anwendung erstellten Datensätze werden in Postgres anstatt wie zuvor in der H2-In-Memory Database gespeichert. Um dies zu überprüfen, können Sie einen Datensatz erstellen und Ihre App Service-Instanz neu starten. Die Datensätze sind nach einem Neustart Ihrer Anwendung immer noch vorhanden.

## <a name="clean-up"></a>Bereinigen

Wenn Sie diese Ressourcen nicht für ein anderes Tutorial benötigen (siehe „Nächste Schritte“), können Sie sie löschen, indem Sie den folgenden Befehl ausführen:

```bash
az group delete --name <your-resource-group>
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Bereitstellen einer Java EE-App in Azure mithilfe von Maven
> * Erstellen einer Postgres-Datenbank in Azure
> * Konfigurieren der WildFly-Server für die Verwendung von Postgres
> * Aktualisieren und erneutes Bereitstellen der App
> * Ausführen von Komponententests für WildFly

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Ihrer App einen benutzerdefinierten DNS-Namen zuordnen.

> [!div class="nextstepaction"]
> [Tutorial: Zuordnen eines benutzerdefinierten DNS-Namens zu Ihrer App](../app-service-web-tutorial-custom-domain.md)

Oder sehen Sie sich weitere Ressourcen an:

> [!div class="nextstepaction"]
> [Konfigurieren der Java-App](configure-language-java.md)
