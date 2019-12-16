---
title: 'Tutorial: Vorbereiten einer Spring-Anwendung für die Bereitstellung in Azure Spring Cloud'
description: In diesem Tutorial bereiten Sie eine Java Spring-Anwendung für die Bereitstellung vor.
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: e112fdc9e6f518e2ea3c72161e8978118cf19335
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74890313"
---
# <a name="tutorial-prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Tutorial: Vorbereiten einer Java Spring-Anwendung für die Bereitstellung in Azure Spring Cloud

In dieser Schnellstartanleitung wird gezeigt, wie Sie eine vorhandene Java Spring Cloud-Anwendung für die Bereitstellung in Azure Spring Cloud vorbereiten.  Bei einer ordnungsgemäßen Konfiguration bietet Azure Spring Cloud stabile Dienste zum Überwachen, Skalieren und Aktualisieren Ihrer Spring Cloud-Anwendung. 

## <a name="java-runtime-version"></a>Java Runtime-Version

In Azure Spring Cloud können nur Spring-/Java-Anwendungen ausgeführt werden.

Java 8 und Java 11 werden unterstützt. Die Hostingumgebung enthält die aktuelle Azul Zulu OpenJDK-Version für Azure. In [diesem Artikel](https://docs.microsoft.com/azure/java/jdk/java-jdk-install) finden Sie weitere Informationen zu Azul Zulu OpenJDK für Azure. 

## <a name="spring-boot-and-spring-cloud-versions"></a>Spring Boot- and Spring Cloud-Versionen

In Azure Spring Cloud werden nur Spring Boot-Apps unterstützt. Spring Boot 2.0 und 2.1 werden unterstützt. Die unterstützten Spring Boot- und Spring Cloud-Kombinationen sind in der folgenden Tabelle aufgeführt:

Spring Boot-Version | Spring Cloud-Version
---|---
2.0.x | Finchley.RELEASE
2.1.x | Greenwich.RELEASE

Überprüfen Sie, ob die Datei `pom.xml` die Spring Boot- und Spring Cloud-Abhängigkeiten basierend auf Ihrer Version enthält.

### <a name="version-20"></a>Version 2.0:

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.9.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Finchley.SR4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="version-21"></a>Version 2.1:

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.8.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.SR3</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

## <a name="azure-spring-cloud-client-dependency"></a>Azure Spring Cloud-Clientabhängigkeit

Azure Spring Cloud hostet und verwaltet Spring Cloud-Komponenten für Sie, etwa die Spring Cloud-Dienstregistrierung und den Spring Cloud-Konfigurationsserver. Fügen Sie die Clientbibliothek von Azure Spring Cloud in Ihre Abhängigkeiten ein, um die Kommunikation mit der Azure Spring Cloud-Dienstinstanz zu ermöglichen.

In der folgenden Tabelle sind die richtigen Versionen für Ihre Spring Boot-/Spring Cloud-App aufgeführt:

Spring Boot-Version | Spring Cloud-Version | Azure Spring Cloud-Version
---|---|---
2.0.x | Finchley.RELEASE | 2.0.x
2.1.x | Greenwich.RELEASE | 2.1.x

Fügen Sie einen der folgenden Codeausschnitte in Ihre `pom.xml` ein.  Wählen Sie den Codeausschnitt aus, dessen Version Ihrer eigenen entspricht.

### <a name="version-20x"></a>Version 2.0.x:
```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.0.0</version>
</dependency>
```

### <a name="version-21x"></a>Version 2.1.x:
```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>Andere erforderliche Abhängigkeiten

Zur Aktivierung der integrierten Features von Azure Spring Cloud muss Ihre Anwendung die folgenden Abhängigkeiten enthalten. Dadurch wird sichergestellt, dass Ihre Anwendung sich selbst korrekt mit den einzelnen Komponenten konfiguriert.  

### <a name="service-registry"></a>Dienstregistrierung

Fügen Sie zur Verwendung des verwalteten Diensts für die Azure-Dienstregistrierung `spring-cloud-starter-netflix-eureka-client` wie unten gezeigt in `POM.xml` ein.

Der Endpunkt des Dienstregistrierungsservers wird automatisch als Umgebungsvariablen in Ihre App eingefügt. Anwendungen können sich selbst beim Dienstregistrierungsserver registrieren und andere abhängige Microservices ermitteln.

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

### <a name="distributed-configuration"></a>Verteilte Konfiguration

Fügen Sie zum Aktivieren der verteilten Konfiguration `spring-cloud-config-client` im Abschnitt der Abhängigkeiten der Datei `pom.xml` ein.

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Geben Sie `spring.cloud.config.enabled=false` nicht in einer Bootstrapkonfiguration an, da die Anwendung sonst nicht mehr mit dem Konfigurationsserver verwendet werden kann.

### <a name="metrics"></a>metrics

Fügen Sie `spring-boot-starter-actuator` im Abschnitt der Abhängigkeiten der Datei „pom.xml“ ein. Metriken werden in regelmäßigen Abständen aus den JMX-Endpunkten gepullt und können über das Azure-Portal visualisiert werden.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

### <a name="distributed-tracing"></a>Verteilte Ablaufverfolgung

Fügen Sie `spring-cloud-starter-sleuth` und `spring-cloud-starter-zipkin` wie unten gezeigt im Abschnitt der Abhängigkeiten der Datei „pom.xml“ ein. Darüber hinaus müssen Sie eine Azure App Insights-Instanz für die Verwendung mit der Azure Spring Cloud-Dienstinstanz aktivieren. [Hier](spring-cloud-tutorial-distributed-tracing.md) finden Sie weitere Informationen zum Aktivieren von App Insights mit Azure Spring Cloud.

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie erfahren, wie Sie eine Java Spring-Anwendung für die Bereitstellung in Azure Spring Cloud konfigurieren.  Informationen zum Aktivieren des Konfigurationsservers finden Sie im nächsten Tutorial.

> [!div class="nextstepaction"]
> [Tutorial: Einrichten eines Spring Cloud-Konfigurationsservers für Ihren Dienst](spring-cloud-tutorial-config-server.md)

Weitere Beispiele finden Sie auf GitHub: [Azure Spring Cloud-Beispiele](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
