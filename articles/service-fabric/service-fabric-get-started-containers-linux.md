---
title: Erstellen einer Azure Service Fabric-Containeranwendung unter Linux | Microsoft-Dokumentation
description: "Erstellen Sie Ihre erste Linux-Containeranwendung unter Azure Service Fabric.  Erstellen Sie ein Docker-Image mit Ihrer Anwendung, übertragen Sie es per Push an eine Containerregistrierung, erstellen Sie eine Service Fabric-Containeranwendung, und stellen Sie diese bereit."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/09/2018
ms.author: ryanwi
ms.openlocfilehash: 8ff3c60ea2e0e96a9ade2e1f2d711197bb3252ed
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2018
---
# <a name="create-your-first-service-fabric-container-application-on-linux"></a>Erstellen Ihrer ersten Service Fabric-Containeranwendung unter Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

Zum Ausführen einer vorhandenen Anwendung eines Linux-Containers in einem Service Fabric-Cluster sind keine Änderungen an Ihrer Anwendung erforderlich. In diesem Artikel wird schrittweise beschrieben, wie Sie ein Docker-Image mit einer Python [Flask](http://flask.pocoo.org/)-Webanwendung erstellen und in einem Service Fabric-Cluster bereitstellen.  Außerdem stellen Sie Ihre Containeranwendung per [Azure Container Registry](/azure/container-registry/) bereit.  In diesem Artikel werden grundlegende Kenntnisse von Docker vorausgesetzt. Weitere Informationen zu Docker finden Sie in der [Docker-Übersicht](https://docs.docker.com/engine/understanding-docker/).

## <a name="prerequisites"></a>Voraussetzungen
* Ein Entwicklungscomputer, auf dem Folgendes ausgeführt wird:
  * [Service Fabric-SDK und -Tools](service-fabric-get-started-linux.md)
  * [Docker CE für Linux](https://docs.docker.com/engine/installation/#prior-releases). 
  * [Service Fabric-Befehlszeilenschnittstelle](service-fabric-cli.md)

* Eine Registrierung in Azure Container Registry – erstellen Sie in Ihrem Azure-Abonnement eine [Containerregistrierung](../container-registry/container-registry-get-started-portal.md). 

## <a name="define-the-docker-container"></a>Definieren des Docker-Containers
Erstellen Sie ein Image auf Grundlage des [Python-Image](https://hub.docker.com/_/python/) in Docker Hub. 

Definieren Sie Ihren Docker-Container in einer Dockerfile-Datei. Die Dockerfile-Datei enthält eine Anleitung zum Einrichten der Umgebung in Ihrem Container, Laden der auszuführenden Anwendung und Zuordnen von Ports. Die Dockerfile-Datei ist die Eingabe für den Befehl `docker build`, der das Image erstellt. 

Erstellen Sie ein leeres Verzeichnis, und erstellen Sie die Datei *Dockerfile* (ohne Dateierweiterung). Fügen Sie der *Dockerfile* Folgendes hinzu, und speichern Sie Ihre Änderungen:

```
# Use an official Python runtime as a base image
FROM python:2.7-slim

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

Weitere Informationen finden Sie in der [Dockerfile-Referenz](https://docs.docker.com/engine/reference/builder/).

## <a name="create-a-simple-web-application"></a>Erstellen einer einfachen Webanwendung
Erstellen Sie eine Flask-Webanwendung, die über Port 80 lauscht und „Hello World!“ zurückgibt.  Erstellen Sie in demselben Verzeichnis die Datei *requirements.txt*.  Fügen Sie Folgendes hinzu, und speichern Sie die Änderungen:
```
Flask
```

Erstellen Sie auch die Datei *app.py*, und fügen Sie Folgendes hinzu:

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello():
    
    return 'Hello World!'

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

## <a name="build-the-image"></a>Erstellen des Image
Führen Sie den Befehl `docker build` aus, um das Image zu erstellen, mit dem Ihre Webanwendung ausgeführt wird. Öffnen Sie ein PowerShell-Fenster, und navigieren Sie zu *c:\temp\helloworldapp*. Führen Sie den folgenden Befehl aus:

```bash
docker build -t helloworldapp .
```

Dieser Befehl erstellt das neue Image mithilfe der Anweisungen in der Dockerfile-Datei. Das Image erhält den Namen „helloworldapp“ (-t tagging). Durch das Erstellen eines Image wird das Basisimage per Pullvorgang von Docker Hub abgerufen, und es wird ein neues Image erstellt, mit dem Ihre Anwendung zusätzlich zum Basisimage hinzugefügt wird.  

Wenn der Buildbefehl abgeschlossen ist, rufen Sie den Befehl `docker images` ab, um Informationen zum neuen Image anzuzeigen:

```bash
$ docker images
    
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              86838648aab6        2 minutes ago       194 MB
```

## <a name="run-the-application-locally"></a>Lokales Ausführen der Anwendung
Stellen Sie sicher, dass Ihre Containeranwendung lokal ausgeführt wird, bevor Sie sie an die Containerregistrierung übertragen.  

Führen Sie die Anwendung aus, und ordnen Sie den Port 4000 Ihres Computers dem verfügbar gemachten Port 80 des Containers zu:

```bash
docker run -d -p 4000:80 --name my-web-site helloworldapp
```

*name* vergibt einen Namen für den ausgeführten Container (anstelle der Container-ID).

Stellen Sie eine Verbindung mit dem ausgeführten Container her.  Öffnen Sie einen Webbrowser, und verweisen Sie auf die IP-Adresse, die über Port 4000 zurückgegeben wurde, z.B. http://localhost:4000 . Die Überschrift „Hello World!“ wird im Browser angezeigt.

![Hello World!][hello-world]

Führen Sie Folgendes aus, um den Container zu beenden:

```bash
docker stop my-web-site
```

Löschen Sie den Container von Ihrem Entwicklungscomputer:

```bash
docker rm my-web-site
```

## <a name="push-the-image-to-the-container-registry"></a>Übertragen des Images an die Containerregistrierung mithilfe von Push
Nachdem Sie sichergestellt haben, dass die Anwendung in Docker ausgeführt wird, können Sie das Image per Pushvorgang in Ihre Registrierung in der Azure Container Registry übertragen.

Führen Sie `docker login` aus, um sich mit Ihren [Registrierungsanmeldeinformationen](../container-registry/container-registry-authentication.md) an der Containerregistrierung anzumelden.

Im folgenden Beispiel werden die ID und das Kennwort eines Azure Active Directory-[Dienstprinzipals](../active-directory/active-directory-application-objects.md) übergeben. Angenommen, Sie haben Ihrer Registrierung für ein Automatisierungsszenario einen Dienstprinzipal zugewiesen.  Oder Sie können sich mit Ihrem Benutzernamen und Kennwort für die Registrierung anmelden.

```bash
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Mit dem folgenden Befehl wird ein Tag oder Alias des Images mit einem vollqualifizierten Pfad zur Registrierung erstellt. In diesem Beispiel wird das Image im `samples`-Namespace platziert, um den Stamm der Registrierung nicht zu überladen.

```bash
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

Übertragen des Image mithilfe von Push an Ihre Containerregistrierung:

```bash
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="package-the-docker-image-with-yeoman"></a>Packen des Docker-Image mit Yeoman
Das Service Fabric SDK für Linux enthält einen [Yeoman](http://yeoman.io/)-Generator, mit dem Sie problemlos die Anwendung erstellen und ein Containerimage hinzufügen können. Lassen Sie uns mit Yeoman eine Anwendung mit einem einzelnen Docker-Container erstellen, deren Name *SimpleContainerApp* lautet.

Öffnen Sie zum Erstellen einer Service Fabric-Containeranwendung ein Terminalfenster, und führen Sie `yo azuresfcontainer` aus.  

Benennen Sie Ihre Anwendung (Beispiel: mycontainer) und den Anwendungsdienst (Beispiel: myservice).

Geben Sie als Imagename die URL für das Containerimage in einer Containerregistrierung an (Beispiel: myregistry.azurecr.io/samples/helloworldapp). 

Da für dieses Image ein Workloadeinstiegspunkt definiert ist, müssen Sie nicht explizit Eingabebefehle angeben. (Befehle werden im Container ausgeführt, wodurch der Container nach dem Start weiter ausgeführt wird.) 

Geben Sie als Instanzanzahl „1“ an.

![Service Fabric-Yeoman-Generator für Container][sf-yeoman]

## <a name="configure-port-mapping-and-container-repository-authentication"></a>Konfigurieren der Portzuordnung und der Authentifizierung für das Containerrepository
Für Ihren im Container ausgeführten Dienst wird ein Endpunkt für die Kommunikation benötigt.  Fügen Sie als Nächstes der Datei „ServiceManifest.xml“ unter dem Tag „Resources“ einem `Endpoint` nun das Protokoll, den Port und den Typ hinzu. In diesem Artikel lauscht der im Container ausgeführte Dienst auf Port 4000: 

```xml

<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myServiceTypeEndpoint" UriScheme="http" Port="4000" Protocol="http"/>
    </Endpoints>
  </Resources>
 ```
 
Durch die Bereitstellung von `UriScheme` wird automatisch der Containerendpunkt im Service Fabric Naming Service registriert, damit er einfacher erkennbar ist. Eine vollständige „ServiceManifest.xml“-Beispieldatei finden Sie am Ende dieses Artikels. 

Konfigurieren Sie auch die Zuordnung vom Containerport zum Hostport, indem Sie in `ContainerHostPolicies` in der Datei „ApplicationManifest.xml“ eine `PortBinding`-Richtlinie angeben.  In diesem Artikel wird als `ContainerPort` 80 verwendet (der Container macht Port 80 verfügbar, wie in der Dockerfile-Datei angegeben), und `EndpointRef` lautet „myServiceTypeEndpoint“ (der im Dienstmanifest angegebene Endpunkt).  Über Port 4000 an den Dienst eingehende Anforderungen werden Port 80 im Container zugeordnet.  Wenn der Container eine Authentifizierung mit einem privaten Repository durchführen muss, fügen Sie `RepositoryCredentials` hinzu.  Fügen Sie für diesen Artikel den Kontonamen und das Kennwort für die Containerregistrierung „myregistry.azurecr.io“ hinzu. Stellen Sie sicher, dass die Richtlinie unter dem Tag „ServiceManifestImport“ gemäß dem richtigen Dienstpaket hinzugefügt wird.

```xml
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0" />
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="myServiceTypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
   </ServiceManifestImport>
``` 
## <a name="configure-docker-healthcheck"></a>Konfigurieren von „docker HEALTHCHECK“ 
Beim Starten von Version 6.1 integriert Service Fabric automatisch [docker HEALTHCHECK](https://docs.docker.com/engine/reference/builder/#healthcheck)-Ereignisse in seinen Bericht zur Systemintegrität. Wenn für Ihren Container **HEALTHCHECK** aktiviert ist, bedeutet dies Folgendes: Von Service Fabric wird die Dienstintegrität immer dann gemeldet, wenn sich der Integritätsstatus des Containers gemäß Meldung durch Docker ändert. Die Integritätsmeldung **OK** wird in [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) angezeigt, wenn für *health_status* die Meldung *healthy* erfolgt, und **WARNING**, wenn für *health_status* die Meldung *unhealthy* erfolgt. Die Anweisung **HEALTHCHECK**, die auf die tatsächliche Prüfung zur Überwachung der Containerintegrität verweist, muss in der **Dockerfile** enthalten sein, die beim Generieren des Containerimages verwendet wurde. 

![HealthCheckHealthy][1]

![HealthCheckUnealthyApp][2]

![HealthCheckUnhealthyDsp][3]

Sie können das **HEALTHCHECK**-Verhalten für jeden Container konfigurieren, indem Sie **HealthConfig**-Optionen im Rahmen von **ContainerHostPolicies** im Anwendungsmanifest angeben.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="ContainerServicePkg" ServiceManifestVersion="2.0.0" />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <HealthConfig IncludeDockerHealthStatusInSystemHealthReport="true" RestartContainerOnUnhealthyDockerHealthStatus="false" />
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
```
*IncludeDockerHealthStatusInSystemHealthReport* ist standardmäßig auf **true** und *RestartContainerOnUnhealthyDockerHealthStatus* auf **false** festgelegt. Wenn *RestartContainerOnUnhealthyDockerHealthStatus* auf **true** festgelegt ist, wird ein Container, für den wiederholt ein nicht fehlerfreier Zustand (unhealthy) gemeldet wird, neu gestartet (ggf. auf anderen Knoten).

Falls Sie die **HEALTHCHECK**-Integration für den gesamten Service Fabric-Cluster deaktivieren möchten, müssen Sie [EnableDockerHealthCheckIntegration](service-fabric-cluster-fabric-settings.md) auf **false** festlegen.

## <a name="build-and-package-the-service-fabric-application"></a>Erstellen und Packen der Service Fabric-Anwendung
Die Yeoman-Vorlagen von Service Fabric enthalten ein Buildskript für [Gradle](https://gradle.org/). Damit können Sie die Anwendung über das Terminal erstellen. Führen Sie Folgendes aus, um die Anwendung zu erstellen und zu packen:

```bash
cd mycontainer
gradle
```

## <a name="deploy-the-application"></a>Bereitstellen der Anwendung
Die erstellte Anwendung kann mithilfe der Service Fabric-Befehlszeilenschnittstelle im lokalen Cluster bereitgestellt werden.

Stellen Sie eine Verbindung mit dem lokalen Service Fabric-Cluster her.

```bash
sfctl cluster select --endpoint http://localhost:19080
```

Verwenden Sie das in der Vorlage bereitgestellte Installationsskript, um das Anwendungspaket in den Imagespeicher des Clusters zu kopieren, den Anwendungstyp zu registrieren und eine Instanz der Anwendung zu erstellen.

```bash
./install.sh
```

Navigieren Sie in einem Browser zu Service Fabric Explorer (http://localhost:19080/Explorer). Falls Sie Vagrant unter Mac OS X verwenden, ersetzen Sie „localhost“ durch die private IP-Adresse des virtuellen Computers. Erweitern Sie den Knoten „Anwendungen“. Hier finden Sie nun einen Eintrag für Ihren Anwendungstyp und einen weiteren für die erste Instanz dieses Typs.

Stellen Sie eine Verbindung mit dem ausgeführten Container her.  Öffnen Sie einen Webbrowser, und verweisen Sie auf die IP-Adresse, die über Port 4000 zurückgegeben wurde, z.B. http://localhost:4000 . Die Überschrift „Hello World!“ wird im Browser angezeigt.

![Hello World!][hello-world]


## <a name="clean-up"></a>Bereinigen
Verwenden Sie das in der Vorlage bereitgestellte Deinstallationsskript, um die Anwendungsinstanz aus dem lokalen Entwicklungscluster zu löschen und die Registrierung des Anwendungstyps aufzuheben.

```bash
./uninstall.sh
```

Wenn Sie das Image mithilfe von Push an die Containerregistrierung übertragen haben, können Sie das lokale Image von Ihrem Entwicklungscomputer löschen:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Vollständige Beispiele für Service Fabric-Anwendungs- und Dienstmanifeste
Dies sind die vollständigen Dienst- und Anwendungsmanifeste, die in diesem Artikel verwendet werden.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="myservicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="myserviceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers 
      to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
        <Commands></Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    
    <EnvironmentVariables>
      <!--
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
      -->
    </EnvironmentVariables>
    
  </CodePackage>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myServiceTypeEndpoint" UriScheme="http" Port="4000" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="mycontainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="myservicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="myServiceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="myservice">
      <!-- On a local development cluster, set InstanceCount to 1.  On a multi-node production 
      cluster, set InstanceCount to -1 for the container service to run on every node in 
      the cluster.
      -->
      <StatelessService ServiceTypeName="myserviceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="adding-more-services-to-an-existing-application"></a>Hinzufügen weiterer Dienste zu einer vorhandenen Anwendung

Führen Sie zum Hinzufügen eines weiteren Containerdiensts zu einer Anwendung, die bereits mit Yeoman erstellt wurde, die folgenden Schritte aus:

1. Legen Sie das Verzeichnis auf den Stamm der vorhandenen Anwendung fest.  Beispiel: `cd ~/YeomanSamples/MyApplication`, wenn `MyApplication` die von Yeoman erstellte Anwendung ist.
2. Führen Sie `yo azuresfcontainer:AddService` aus.

<a id="manually"></a>


## <a name="configure-time-interval-before-container-is-force-terminated"></a>Konfigurieren des Zeitintervalls vor Erzwingung der Containerbeendigung

Sie können für die Runtime konfigurieren, wie lange nach dem Starten der Dienstlöschung (oder der Verschiebung auf einen anderen Knoten) mit dem Entfernen des Containers gewartet werden soll. Durch die Konfiguration des Zeitintervalls wird der Befehl `docker stop <time in seconds>` an den Container gesendet.   Weitere Informationen finden Sie unter [docker stop](https://docs.docker.com/engine/reference/commandline/stop/). Die Wartezeit wird im Abschnitt `Hosting` angegeben. Im folgenden Codeausschnitt des Clustermanifests ist dargestellt, wie die Wartezeit festgelegt wird:


```json
{
        "name": "Hosting",
        "parameters": [
          {
                "name": "ContainerDeactivationTimeout",
                "value" : "10"
          },
          ...
        ]
}
```

Das Standardzeitintervall ist auf 10 Sekunden festgelegt. Da es sich hierbei um eine dynamische Konfiguration handelt, wird bei einem reinen Konfigurationsupgrade für den Cluster das Timeout aktualisiert. 

## <a name="configure-the-runtime-to-remove-unused-container-images"></a>Konfigurieren der Runtime für die Entfernung nicht verwendeter Containerimages

Sie können den Service Fabric-Cluster so konfigurieren, dass er nicht verwendete Containerimages aus dem Knoten entfernt. Diese Konfiguration ermöglicht die Freigabe von Speicherplatz, wenn zu viele Containerimages auf dem Knoten vorhanden sind.  Aktualisieren Sie zur Aktivierung dieses Features den Abschnitt `Hosting` im Clustermanifest, wie im folgenden Codeausschnitt gezeigt: 


```json
{
        "name": "Hosting",
        "parameters": [
          {
                "name": "PruneContainerImages",
                "value": "True"
          },
          {
                "name": "ContainerImagesToSkip",
                "value": "microsoft/windowsservercore|microsoft/nanoserver|microsoft/dotnet-frameworku|..."
          }
          ...
          }
        ]
} 
```

Images, die nicht gelöscht werden sollen, können Sie unter dem Parameter `ContainerImagesToSkip` angeben. 

## <a name="configure-container-image-download-time"></a>Konfigurieren der Downloadzeit für Containerimages

Standardmäßig wird von der Service Fabric-Runtime ein Zeitraum von 20 Minuten für das Herunterladen und Extrahieren von Containerimages zugeteilt. Dies ist für die meisten Containerimages ausreichend. Für große Images oder bei einer langsamen Netzwerkverbindung kann es erforderlich sein, die Wartezeit bis zu dem Zeitpunkt, zu dem das Herunterladen und Extrahieren des Images abgebrochen wird, zu erhöhen. Dies kann mit dem Attribut **ContainerImageDownloadTimeout** im Abschnitt **Hosting** des Clustermanifests festgelegt werden, wie im folgenden Codeausschnitt dargestellt:

```json
{
"name": "Hosting",
        "parameters": [
          {
              "name": " ContainerImageDownloadTimeout ",
              "value": "1200"
          }
]
}
```


## <a name="set-container-retention-policy"></a>Festlegen der Aufbewahrungsrichtlinie für Container

Als Hilfe bei der Diagnose von Startfehlern unterstützt Service Fabric (Version 6.1 oder höher) das Aufbewahren von Containern, die beendet wurden oder nicht gestartet werden konnten. Diese Richtlinie kann in der Datei **ApplicationManifest.xml** festgelegt werden, wie im folgenden Codeausschnitt gezeigt:

```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
```

Mit der Einstellung **ContainersRetentionCount** wird die Anzahl von Containern angegeben, die bei Fehlern beibehalten werden sollen. Wenn ein negativer Wert angegeben wird, werden alle fehlerhaften Container beibehalten. Wenn das Attribut **ContainersRetentionCount** nicht angegeben wird, werden keine Container beibehalten. Das Attribut **ContainersRetentionCount** unterstützt auch Anwendungsparameter, sodass Benutzer unterschiedliche Werte für Test- und Produktionscluster angeben können. Es wird empfohlen, bei Verwendung dieses Features Platzierungseinschränkungen zu nutzen, um den Containerdienst einem bestimmten Knoten zuzuordnen und zu verhindern, dass er auf andere Knoten verschoben wird. Alle Container, die mit diesem Feature beibehalten werden, müssen manuell entfernt werden.


## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zum Ausführen von [Containern in Service Fabric](service-fabric-containers-overview.md)
* Lesen Sie sich das Tutorial [Bereitstellen einer .NET-App in einem Container in Azure Service Fabric](service-fabric-host-app-in-a-container.md) durch.
* Weitere Informationen zum [Anwendungslebenszyklus](service-fabric-application-lifecycle.md) von Service Fabric
* [Codebeispiele zu Service Fabric-Containern](https://github.com/Azure-Samples/service-fabric-containers) in GitHub

[hello-world]: ./media/service-fabric-get-started-containers-linux/HelloWorld.png
[sf-yeoman]: ./media/service-fabric-get-started-containers-linux/YoSF.png

[1]: ./media/service-fabric-get-started-containers/HealthCheckHealthy.png
[2]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_App.png
[3]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_Dsp.png
