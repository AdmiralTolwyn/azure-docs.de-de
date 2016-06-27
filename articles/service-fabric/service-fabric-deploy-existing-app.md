<properties
   pageTitle="Bereitstellen einer ausführbaren Gastanwendungsdatei in Azure Service Fabric | Microsoft Azure"
   description="Exemplarische Vorgehensweise beim Packen einer vorhandenen Anwendung als ausführbare Gastanwendungsdatei, um diese in einem Azure Service Fabric-Cluster bereitzustellen."
   services="service-fabric"
   documentationCenter=".net"
   authors="bmscholl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="06/06/2016"
   ms.author="bscholl;mikhegn"/>

# Bereitstellen einer ausführbaren Gastanwendungsdatei in Service Fabric

Sie können beliebige Anwendungen, z. B. Node.js-, Java- oder native Anwendungen, in Azure Service Fabric ausführen. Im Zusammenhang mit Service Fabric werden diese Anwendungen als ausführbare Gastanwendungsdateien bezeichnet. Ausführbare Gastanwendungsdateien werden von Service Fabric wie zustandslose Dienste behandelt. Folglich werden sie basierend auf Verfügbarkeit und anderen Metriken auf Knoten innerhalb eines Clusters platziert. In diesem Artikel wird beschrieben, wie Sie eine ausführbare Gastanwendungsdatei verpacken und in einem Service Fabric-Cluster bereitstellen, indem Sie Visual Studio oder ein Befehlszeilenprogramm verwenden.

## Vorteile der Ausführung einer ausführbaren Gastanwendungsdatei in Service Fabric

Das Ausführen einer ausführbaren Gastanwendungsdatei in einem Service Fabric-Cluster bietet mehrere Vorteile:

- Hohe Verfügbarkeit. Anwendungen, die in Service Fabric ausgeführt werden, sind standardmäßig hoch verfügbar. Service Fabric stellt sicher, dass stets eine Instanz einer Anwendung ausgeführt wird.
- Systemüberwachung. Die standardmäßige Service Fabric-Systemüberwachung erkennt, ob eine Anwendung ausgeführt wird, und bietet bei Fehlern Diagnoseinformationen.   
- Application Lifecycle Management. Service Fabric ermöglicht nicht nur Upgrades ohne Ausfallzeiten, sondern auch das Zurücksetzen auf die Vorversion, sollte während eines Upgrades ein Problem auftreten.    
- Dichte. Sie können mehrere Anwendungen in einem Cluster ausführen, sodass nicht mehr jede Anwendung auf eigener Hardware ausgeführt werden muss.

In diesem Artikel werden die grundlegenden Schritte zum Verpacken einer ausführbaren Gastanwendungsdatei sowie ihre Bereitstellung in Service Fabric beschrieben.

## Kurzübersicht über die Anwendungs- und Dienstmanifestdateien

Im Rahmen der Bereitstellung einer ausführbaren Gastanwendungsdatei sollten Sie das Service Fabric-Modell für das Verpacken und Bereitstellen von Anwendungen kennen. Das Verpackungs- und Bereitstellungsmodell von Service Fabric basiert hauptsächlich auf zwei XML-Dateien: dem Anwendungs- und dem Dienstmanifest. Die Schemadefinition für die Dateien „ApplicationManifest.xml“ und „ServiceManifest.xml“ wird über das Service Fabric-SDK und die Service Fabric-Tools unter *C:\\Programme\\Microsoft SDKs\\Service Fabric\\schemas\\ServiceFabricServiceModel.xsd* installiert.

* **Anwendungsmanifest**

  Das Anwendungsmanifest wird verwendet, um die Anwendung zu beschreiben. Es listet neben den Diensten, aus denen sie besteht, noch weitere Parameter auf, mit denen definiert wird, wie die Dienste bereitgestellt werden sollen (z.B. die Anzahl der Instanzen).

  In der Service Fabric-Terminologie ist eine Anwendung eine „aktualisierbare Einheit“. Eine Anwendung kann als eine Einheit aktualisiert werden, wobei potenzielle Fehler (und mögliche Zurücksetzungen) von der Plattform verwaltet werden. Durch die Plattform wird sichergestellt, dass der Upgradevorgang vollständig erfolgreich ist bzw. dass die Anwendung bei einem Upgradefehler nicht in einem unbekannten/instabilen Zustand belassen wird.

* **Dienstmanifest**

  Das Dienstmanifest beschreibt die Komponenten eines Diensts. Es enthält Daten, z. B. den Namen und den Typ des Diensts (die Informationen, die Service Fabric zur Verwaltung des Diensts verwendet), und seine Code-, Konfigurations- und Datenkomponenten. Das Dienstmanifest enthält auch einige zusätzliche Parameter, die verwendet werden können, um den Dienst zu konfigurieren, nachdem er bereitgestellt wurde.

  Hier werden nicht die Details aller unterschiedlichen Parameter beschrieben, die im Dienstmanifest verfügbar sind. Wir werden auf den relevanten Abschnitt eingehen, um eine ausführbare Gastanwendungsdatei in Service Fabric auszuführen.

## Dateistruktur des Anwendungspakets
Damit eine Anwendung in Service Fabric bereitgestellt werden kann, muss die Anwendung einer vordefinierten Verzeichnisstruktur folgen. Es folgt ein Beispiel dieser Struktur.

```
|-- ApplicationPackage
	|-- code
		|-- existingapp.exe
	|-- config
		|-- Settings.xml
  |-- data    
  |-- ServiceManifest.xml
|-- ApplicationManifest.xml
```

Das Stammverzeichnis enthält die Datei „applicationmanifest.xml“, welche die Anwendung definiert. Für jeden Dienst, der in der Anwendung enthalten ist, gibt es ein Unterverzeichnis, das alle für den Dienst erforderlichen Artefakte enthält: die Datei „ServiceManifest.xml“ und in der Regel die folgenden drei Verzeichnisse:

- *Code*. Dieses Verzeichnis enthält den Code des Diensts.
- *Config*. Dieses Verzeichnis enthält die Datei „Settings.xml“ (sowie andere Dateien, falls erforderlich), auf die der Dienst zur Laufzeit zugreifen kann, um bestimmte Konfigurationseinstellungen abzurufen.
- *Data*. Dies ist ein zusätzliches Verzeichnis zum Speichern zusätzlicher lokaler Daten, die der Dienst möglicherweise benötigt. Hinweis: Das Verzeichnis „data“ sollte nur verwendet werden, um kurzlebige Daten zu speichern. Service Fabric kopiert/repliziert keine Änderungen in das Verzeichnis „data“, wenn der Dienst z. B. bei einem Failover verschoben werden muss.

Hinweis: Sie müssen die Verzeichnisse `config` und `data` nur erstellen, falls Sie sie benötigen.

## Prozess zum Packen einer vorhandenen Anwendung

Beim Packen einer ausführbaren Gastanwendungsdatei können Sie wählen, ob Sie eine Visual Studio-Projektvorlage verwenden oder das Anwendungspaket manuell erstellen. Mit Visual Studio werden die Anwendungspaketstruktur und Manifestdateien mit dem neuen Projekt-Assistenten für Sie erstellt. Unten ist eine Schritt-für-Schritt-Anleitung zum Packen einer ausführbaren Gastanwendungsdatei mit Visual Studio angegeben.

Der Vorgang zum manuellen Verpacken einer ausführbaren Gastanwendungsdatei basiert auf folgenden Schritten:

1. Erstellen der Verzeichnisstruktur des Pakets.
2. Hinzufügen von Anwendungscode und Konfigurationsdateien.
3. Bearbeiten der Dienstmanifestdatei.
4. Bearbeiten der Anwendungsmanifestdatei.

>[AZURE.NOTE] Wir stellen ein Packtool bereit, mit dem Sie das Anwendungspaket automatisch erstellen können. Das Tool befindet sich derzeit in der Vorschauphase. Sie können es [hier](http://aka.ms/servicefabricpacktool) herunterladen.

### Erstellen der Verzeichnisstruktur des Pakets
Sie können zunächst die Verzeichnisstruktur wie zuvor beschrieben erstellen.

### Hinzufügen von Anwendungscode und Konfigurationsdateien
Nachdem Sie die Verzeichnisstruktur erstellt haben, können Sie den Anwendungscode und die Konfigurationsdateien den Verzeichnissen „code“ und „config“ hinzufügen. In den Verzeichnissen „code“ und „config“ können Sie auch weitere Verzeichnisse bzw. Unterverzeichnisse erstellen.

Service Fabric erstellt eine XCopy des Inhalts des Stammverzeichnisses der Anwendung. Außer dem Erstellen der beiden übergeordneten Verzeichnisse „code“ und „settings“ gibt es also keine vordefinierte Struktur, die verwendet werden muss. (Sie können auch andere Namen verwenden. Weitere Informationen finden Sie im nächsten Abschnitt.)

>[AZURE.NOTE] Stellen Sie sicher, dass Sie alle Dateien/Abhängigkeiten einbeziehen, die für die Anwendung erforderlich sind. Service Fabric kopiert den Inhalt des Anwendungspakets auf alle Knoten im Cluster, auf denen die Anwendungsdienste bereitgestellt werden sollen. Das Paket muss den gesamten Code enthalten, den die Anwendung zur Ausführung benötigt. Es ist nicht empfehlenswert, davon auszugehen, dass die Abhängigkeiten bereits installiert sind.

### Bearbeiten der Dienstmanifestdatei
Im nächsten Schritt wird die Dienstmanifestdatei so bearbeitet, dass sie folgende Informationen enthält:

- Den Namen des Diensttyps. Dies ist eine ID, die von Service Fabric zum Identifizieren eines Diensts verwendet wird.
- Den Befehl zum Starten der Anwendung (ExeHost).
- Alle Skripts, die ausgeführt werden müssen, um die Anwendung einzurichten oder zu konfigurieren (SetupEntrypoint).

Nachstehend sehen Sie ein Beispiel der Datei `ServiceManifest.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

Sehen wir uns nun die anderen Teile der Datei an, die Sie aktualisieren müssen:

### ServiceTypes

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

- Sie können für `ServiceTypeName` einen beliebigen Namen auswählen. Der Wert wird in der Datei `ApplicationManifest.xml` zum Identifizieren des Diensts verwendet.
- Sie müssen `UseImplicitHost="true"` angeben. Dieses Attribut informiert Service Fabric, dass der Dienst auf einer eigenständigen Anwendung beruht. Service Fabric muss ihn also lediglich als Prozess starten und seine Integrität überwachen.

### CodePackage
Das Element „CodePackage“ gibt den Speicherort (und die Version) des Dienstcodes an.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

Das Element `Name` wird verwendet, um den Namen des Verzeichnisses im Anwendungspaket anzugeben, das den Dienstcode enthält. `CodePackage` weist auch das `version`-Attribut auf. Dies kann verwendet werden, um die Version des Codes anzugeben. Es könnte auch dazu genutzt werden, den Dienstcode mithilfe der Service Fabric-Infrastruktur für das Application Lifecycle Management zu aktualisieren.
### SetupEntryPoint

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
Der Element „SetupEntryPoint“ wird verwendet, um eine ausführbare Datei oder eine Batchdatei anzugeben, die vor dem Starten des Dienstcodes ausgeführt werden soll. Dies ist ein optionales Element, das nicht angegeben werden muss, wenn keine Initialisierung/kein Setup erforderlich ist. Der „SetupEntryPoint“ wird bei jedem Neustart des Diensts ausgeführt.

Es gibt nur ein Element „SetupEntryPoint“. Daher müssen Setup-/Konfigurationsskripts in einer Batchdatei gebündelt werden, wenn für das Setup bzw. die Konfiguration der Anwendung mehrere Skripts erforderlich sind. Wie das Element „SetupEntryPoint“ kann auch „SetupEntryPoint“ jeden beliebigen Dateityp ausführen: ausführbare Dateien, Batchdateien und PowerShell-Cmdlets. Im obigen Beispiel basiert „SetupEntryPoint“ auf der Batchdatei „LaunchConfig.cmd“, die sich im Unterverzeichnis `scripts` des Verzeichnisses „code“ befindet (sofern das Element „WorkingFolder“ auf „code“ festgelegt ist).

### Entrypoint

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

Mit dem Element `Entrypoint` in der Dienstmanifestdatei wird angegeben, wie der Dienst gestartet werden soll. Das Element `ExeHost` gibt die ausführbare Datei (und die Argumente) an, die zum Starten des Diensts verwendet werden soll.

- `Program` gibt den Namen der ausführbaren Datei an, die zum Starten des Diensts ausgeführt werden soll.
- `Arguments` gibt die Argumente an, die an die ausführbare Datei übergeben werden sollen. Dies kann eine Liste von Parametern mit Argumenten sein.
- `WorkingFolder` gibt das Arbeitsverzeichnis für den Prozess an, der gestartet werden soll. Sie können zwei Werte angeben:
	- `CodeBase` gibt an, dass das Arbeitsverzeichnis auf das Verzeichnis „code“ im Anwendungspaket festgelegt wird (das Verzeichnis `Code` in der unten abgebildeten Struktur).
	- `CodePackage` gibt an, dass das Arbeitsverzeichnis auf das Stammverzeichnis des Anwendungspakets (`MyServicePkg`) festgelegt wird.
- `WorkingFolder` ist nützlich, um das richtige Arbeitsverzeichnis festzulegen, damit von der Anwendung oder von Initialisierungsskripts relative Pfade verwendet werden können.

### Endpunkte

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
Das Element `Endpoint` gibt die Endpunkte an, an denen die Anwendung lauschen kann. In diesem Beispiel lauscht die Node.js-Anwendung an Port 3000.

## Bearbeiten der Anwendungsmanifestdatei

Nach der Konfiguration der Datei `Servicemanifest.xml` müssen Sie einige Änderungen an der Datei `ApplicationManifest.xml` vornehmen, um sicherzustellen, dass der richtige Diensttyp und der richtige Name verwendet werden.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

### ServiceManifestImport

Im Element `ServiceManifestImport` können Sie einen oder mehrere Dienste angeben, die in der Anwendung enthalten sein sollen. `ServiceManifestName` verweist auf die Dienste. Dieses Element gibt den Namen des Verzeichnisses an, in dem sich die Datei `ServiceManifest.xml` befindet.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

### Einrichten der Protokollierung
Bei ausführbaren Gastanwendungsdateien ist es äußerst nützlich, Konsolenprotokolle anzeigen zu können, um festzustellen, ob die Anwendungs- und Konfigurationskripts Fehler aufweisen. In der Datei `ServiceManifest.xml` kann mit dem Element `ConsoleRedirection` eine Konsolenumleitung konfiguriert werden.

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

* Mit `ConsoleRedirection` kann die Konsolenausgabe (stdout und stderr) in ein Arbeitsverzeichnis umgeleitet werden, um sicherzustellen, dass bei der Konfiguration oder Ausführung der Anwendung im Service Fabric-Cluster keine Fehler aufgetreten sind.

	* `FileRetentionCount` legt fest, wie viele Dateien im Arbeitsverzeichnis gespeichert werden. Der Wert 5 bedeutet beispielsweise, dass die Protokolldateien für die letzten fünf Ausführungsvorgänge im Arbeitsverzeichnis gespeichert werden.
	* `FileMaxSizeInKb` gibt die maximale Größe der Protokolldateien an.

Protokolldateien werden in einem der Arbeitsverzeichnisse des Diensts gespeichert. Um zu bestimmen, wo sich die Dateien befinden, müssen Sie den Service Fabric-Explorer verwenden. Damit können Sie ermitteln, auf welchem Knoten der Dienst ausgeführt wird und welches Arbeitsverzeichnis verwendet wird. Dieser Vorgang wird weiter unten in diesem Artikel erläutert.

### Bereitstellung
Der letzte Schritt ist das Bereitstellen der Anwendung. Das folgende PowerShell-Skript veranschaulicht, wie Sie die Anwendung im lokalen Entwicklungscluster bereitstellen und einen neuen Service Fabric-Dienst starten.

```PowerShell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```
Ein Service Fabric-Dienst kann mit verschiedenen „Konfigurationen“ bereitgestellt werden. Als eine Instanz, als mehrere Instanzen oder als eine Instanz des Diensts auf jedem Knoten des Service Fabric-Clusters, um nur einige Beispiele zu nennen.

Mit dem `InstanceCount`-Parameter des Cmdlets `New-ServiceFabricService` wird angegeben, wie viele Instanzen des Diensts im Service Fabric-Cluster gestartet werden sollen. Sie können den Wert `InstanceCount` abhängig vom Typ der bereitzustellenden Anwendung festlegen. Die beiden häufigsten Szenarien:

* `InstanceCount = "1"`. In diesem Fall wird nur eine Instanz des Diensts im Cluster bereitgestellt. Der Service Fabric-Scheduler bestimmt den Knoten, auf dem der Dienst bereitgestellt werden soll.

* `InstanceCount ="-1"`. In diesem Fall wird eine Instanz des Diensts auf jedem Knoten im Service Fabric-Cluster bereitgestellt. Das Endergebnis ist eine (und nur eine) Instanz des Diensts für jeden Knoten im Cluster.

Dies ist eine praktische Konfiguration für Front-End-Anwendungen (z. B. REST-Endpunkte), da Clientanwendungen nur eine Verbindung mit einem Knoten im Cluster herstellen müssen, um den Endpunkt zu verwenden. Diese Konfiguration kann z. B. auch verwendet werden, wenn alle Knoten des Service Fabric-Clusters mit einem Lastenausgleichsmodul verbunden sind, damit der Datenverkehr der Clients über den Dienst verteilt werden kann, der auf allen Knoten im Cluster ausgeführt wird.

### Überprüfen der ausgeführten Anwendung

Bestimmen Sie im Service Fabric-Explorer den Knoten, auf dem der Dienst ausgeführt wird. In diesem Beispiel ist es „Node1“:

![Knoten, auf dem der Dienst ausgeführt wird](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Wenn Sie zum Knoten navigieren und zur Anwendung wechseln, sehen Sie die wesentlichen Knoteninformationen, einschließlich des Speicherorts auf dem Datenträger.

![Speicherort auf dem Datenträger](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Wenn Sie in Server-Explorer zum Verzeichnis wechseln, sehen Sie das Arbeitsverzeichnis und den Protokollordner des Diensts, wie nachstehend gezeigt.

![Speicherort des Protokolls](./media/service-fabric-deploy-existing-app/loglocation.png)

## Verwenden von Visual Studio zum Verpacken einer vorhandenen Anwendung

In Visual Studio wird eine Service Fabric-Dienstvorlage bereitgestellt, um Sie beim Bereitstellen einer ausführbaren Gastanwendung für einen Service Fabric-Cluster zu unterstützen. Sie müssen Folgendes durchführen, um die Veröffentlichung abzuschließen:

1. Wählen Sie „Datei“ > „Neues Projekt“, und erstellen Sie eine neue Service Fabric-Anwendung.
2. Wählen Sie als Dienstvorlage die Option „Guest Executable“ (Ausführbare Gastanwendungsdatei) aus.
3. Klicken Sie auf „Durchsuchen“, um den Ordner mit der ausführbaren Datei auszuwählen, und geben Sie die restlichen Parameter an, um den neuen Dienst zu erstellen.
  - Sie können das *Codepaketverhalten* so festlegen, dass der gesamte Inhalt Ihres Ordners in das Visual Studio-Projekt kopiert wird. Dies ist hilfreich, wenn sich die ausführbare Datei nicht ändert. Wenn Sie erwarten, dass sich die ausführbare Datei ändert, und neue Builds dynamisch übernehmen möchten, können Sie stattdessen auch einen Link zum Ordner angeben.
  - Mit *Program* wird die ausführbare Datei ausgewählt, die zum Starten des Diensts ausgeführt werden soll.
  - Mit *Arguments* werden die Argumente angegeben, die an die ausführbare Datei übergeben werden sollen. Dies kann eine Liste von Parametern mit Argumenten sein.
  - *WorkingFolder* gibt das Arbeitsverzeichnis für den Prozess an, der gestartet werden soll. Sie können zwei Werte angeben:
  	- *CodeBase* gibt an, dass das Arbeitsverzeichnis auf das Verzeichnis „code“ im Anwendungspaket festgelegt wird (das Verzeichnis `Code` in der unten abgebildeten Struktur).
    - *CodePackage* gibt an, dass das Arbeitsverzeichnis auf das Stammverzeichnis des Anwendungspakets (`MyServicePkg`) festgelegt wird.
4. Geben Sie dem Dienst einen Namen, und klicken Sie auf „OK“.
5. Wenn der Dienst einen Endpunkt für die Kommunikation benötigt, können Sie das Protokoll, den Port und den Typ der Datei „ServiceManifest.xml“ hinzufügen. Beispiel: ```<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />```
6. Sie können die Aktion zum Verpacken und Veröffentlichen jetzt für Ihren lokalen Cluster ausprobieren, indem Sie die Projektmappe in Visual Studio debuggen. Wenn Sie bereit sind, können Sie die Anwendung in einem Remotecluster veröffentlichen oder die Projektmappe in die Quellcodeverwaltung einchecken.

>[AZURE.NOTE] Sie können verknüpfte Ordner verwenden, wenn Sie das Anwendungsprojekt in Visual Studio erstellen. Im Projekt wird ein Link zum Quellspeicherort erstellt, damit Sie die ausführbare Gastanwendungsdatei an der Quelle aktualisieren können und diese Updates bei der Erstellung Teil des Anwendungspakets werden.

## Nächste Schritte
In diesem Artikel wurden das Verpacken einer ausführbaren Gastanwendungsdatei sowie ihre Bereitstellung in Service Fabric beschrieben. Als nächsten Schritt können Sie weitere Informationen zu diesem Thema lesen.

- [Beispiel für das Verpacken und Bereitstellen einer ausführbaren Gastanwendungsdatei auf GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/GuestExe/SimpleApplication), einschließlich eines Links zur Vorabversion des Packtools
- [Bereitstellen mehrerer ausführbarer Gastanwendungsdateien](service-fabric-deploy-multiple-apps.md)
- [Erstellen Ihrer ersten Service Fabric-Anwendung in Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)

<!---HONumber=AcomDC_0615_2016-->