---
title: Überwachen von in beliebiger Umgebung ausgeführten Java-Anwendungen – Azure Monitor Application Insights
description: Überwachen der Anwendungsleistung für Java-Anwendungen, die in einer beliebigen Umgebung ausgeführt werden, mit eigenständigem Java-Agent und ohne die App zu instrumentieren. Verteilte Ablaufverfolgung und Anwendungszuordnung.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 527f1eaf04be7b5e8c89c12912a06d2f5d50321f
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82508036"
---
# <a name="configuring-jvm-args-java-standalone-agent-for-azure-monitor-application-insights"></a>Konfigurieren eines eigenständigen Java-Agents für JVM-Argumente für Azure Monitor Application Insights



## <a name="azure-environments"></a>Azure-Umgebungen

Konfigurieren Sie [App Services](https://docs.microsoft.com/azure/app-service/configure-language-java#set-java-runtime-options).

## <a name="spring-boot"></a>Spring Boot

Fügen Sie das JVM-Argument `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` an beliebiger Stelle vor `-jar` hinzu. Beispiel:

```
java -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar -jar <myapp.jar>
```

## <a name="spring-boot-via-docker-entry-point"></a>Spring Boot über Docker-Einstiegspunkt

Wenn Sie die *exec*-Form verwenden, fügen Sie z. B. den Parameter `"-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"` der Parameterliste an einer Position vor dem Parameter `"-jar"` hinzu:

```
ENTRYPOINT ["java", "-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar", "-jar", "<myapp.jar>"]
```

Wenn Sie die *shell*-Form verwenden, fügen Sie z. B. das JVM-Argument `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` an einer Position vor `-jar` ein:

```
ENTRYPOINT java -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar -jar <myapp.jar>
```

## <a name="tomcat-8-linux"></a>Tomcat 8 (Linux)

### <a name="tomcat-installed-via-apt-get-or-yum"></a>Über `apt-get` oder `yum` installiertes Tomcat

Wenn Sie Tomcat über `apt-get` oder `yum` installiert haben, sollten Sie über eine Datei `/etc/tomcat8/tomcat8.conf` verfügen.  Fügen Sie die folgende Zeile am Ende dieser Datei hinzu:

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>Durch Herunterladen und Entpacken installiertes Tomcat

Wenn Sie Tomcat durch Herunterladen und Entpacken von [https://tomcat.apache.org](https://tomcat.apache.org) installiert haben, sollten Sie über eine Datei `<tomcat>/bin/catalina.sh` verfügen.  Erstellen Sie im gleichen Verzeichnis eine neue Datei mit dem Namen `<tomcat>/bin/setenv.sh` und folgendem Inhalt:

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Wenn die Datei `<tomcat>/bin/setenv.sh` bereits vorhanden ist, ändern Sie diese Datei, und fügen Sie `CATALINA_OPTS` den Eintrag `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` hinzu.


## <a name="tomcat-8-windows"></a>Tomcat 8 (Windows)

### <a name="running-tomcat-from-the-command-line"></a>Ausführen von Tomcat über die Befehlszeile

Suchen Sie die `<tomcat>/bin/catalina.bat`-Datei.  Erstellen Sie im gleichen Verzeichnis eine neue Datei mit dem Namen `<tomcat>/bin/setenv.bat` und folgendem Inhalt:

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```

Anführungszeichen sind nicht erforderlich, doch wenn Sie welche einfügen möchten, werden sie hier platziert:

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Wenn die Datei `<tomcat>/bin/setenv.bat` bereits vorhanden ist, ändern Sie einfach diese Datei, und fügen Sie `CATALINA_OPTS` den Eintrag `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` hinzu.

### <a name="running-tomcat-as-a-windows-service"></a>Ausführen von Tomcat als Windows-Dienst

Suchen Sie die `<tomcat>/bin/tomcat8w.exe`-Datei.  Führen Sie diese ausführbare Datei aus, und fügen Sie auf der Registerkarte `Java` für `Java Options` den Eintrag `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` hinzu.


## <a name="jboss-eap-7"></a>JBoss EAP 7

### <a name="standalone-server"></a>Eigenständiger Server

Fügen Sie in der Datei `JBOSS_HOME/bin/standalone.conf` (Linux) oder `JBOSS_HOME/bin/standalone.conf.bat` (Windows) der vorhandenen Umgebungsvariablen `JAVA_OPTS` den Eintrag `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` hinzu:

```java    ...
    JAVA_OPTS="<b>-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar</b> -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>Domänenserver

Fügen Sie in `JBOSS_HOME/domain/configuration/host.xml` den vorhandenen `jvm-options` den Eintrag `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` hinzu:

```xml
...
<jvms>
    <jvm name="default">
        <heap size="64m" max-size="256m"/>
        <jvm-options>
            <option value="-server"/>
            <!--Add Java agent jar file here-->
            <option value="-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"/>
            <option value="-XX:MetaspaceSize=96m"/>
            <option value="-XX:MaxMetaspaceSize=256m"/>
        </jvm-options>
    </jvm>
</jvms>
...
```

Wenn Sie mehrere verwaltete Server auf einem einzigen Host ausführen, müssen Sie den `system-properties` für jeden `server` den Eintrag `applicationinsights.agent.id` hinzufügen:

```xml
...
<servers>
    <server name="server-one" group="main-server-group">
        <!--Edit system properties for server-one-->
        <system-properties> 
            <property name="applicationinsights.agent.id" value="..."/>
        </system-properties>
    </server>
    <server name="server-two" group="main-server-group">
        <socket-bindings port-offset="150"/>
        <!--Edit system properties for server-two-->
        <system-properties>
            <property name="applicationinsights.agent.id" value="..."/> 
        </system-properties>
    </server>
</servers>
...
```

Der angegebene Wert für `applicationinsights.agent.id` muss eindeutig sein. Er wird verwendet, um ein Unterverzeichnis im Verzeichnis „applicationinsights“ zu erstellen, da für jeden JVM-Prozess eine eigene lokale „applicationinsights“-Konfigurationsdatei und eine lokale „applicationinsights“-Protokolldatei erforderlich ist. Außerdem wird bei der Berichterstattung an den zentralen Sammler die Datei `applicationinsights.properties` von mehreren verwalteten Servern gemeinsam genutzt, sodass die angegebene `applicationinsights.agent.id` benötigt wird, um die `agent.id`-Einstellung in dieser gemeinsam genutzten Datei zu überschreiben. `applicationinsights.agent.rollup.id` kann auf ähnliche Weise in `system-properties` des Servers angegeben werden, wenn Sie die `agent.rollup.id`-Einstellung für jeden verwaltetem Server überschreiben müssen.


## <a name="jetty-9"></a>Jetty 9

Fügen Sie `start.ini` die folgenden Zeilen hinzu:

```
--exec
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```


## <a name="payara-5"></a>Payara 5

Fügen Sie in `glassfish/domains/domain1/config/domain.xml` den vorhandenen `jvm-options` den Eintrag `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` hinzu:

```xml
...
<java-config ...>
    <!--Edit the JVM options here-->
    <jvm-options>
        -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar>
    </jvm-options>
        ...
</java-config>
...
```

## <a name="websphere-8"></a>WebSphere 8

Öffnen Sie die Verwaltungskonsole, navigieren Sie zu **Server > WebSphere-Anwendungsserver > Anwendungsserver**, wählen Sie die entsprechenden Anwendungsserver aus, und klicken Sie auf: 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
Fügen Sie unter den generischen JVM-Argumenten Folgendes hinzu:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
Anschließend speichern Sie den Anwendungsserver und starten ihn neu.


## <a name="openliberty-18"></a>OpenLiberty 18

Erstellen Sie eine neue Datei `jvm.options` im Serververzeichnis (z. B. `<openliberty>/usr/servers/defaultServer`), und fügen Sie die folgende Zeile hinzu:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
