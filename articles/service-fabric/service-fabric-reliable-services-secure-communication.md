---
title: "Unterstützung der sicheren Kommunikation für Dienste in Azure Service Fabric | Microsoft-Dokumentation"
description: "Übersicht über die Möglichkeiten zur Unterstützung der sicheren Kommunikation für Reliable Services, die in einem Azure Service Fabric-Cluster ausgeführt werden."
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: timlt
editor: vturecek
ms.assetid: fc129c1a-fbe4-4339-83ae-0e69a41654e0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 04/20/2017
ms.author: suchiagicha
ms.openlocfilehash: 0804e43c3f1bb13bea92ebd661ca52c799ff2332
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/21/2017
---
# <a name="help-secure-communication-for-services-in-azure-service-fabric"></a>Unterstützung der Kommunikationssicherung für Dienste in Azure Service Fabric
> [!div class="op_single_selector"]
> * [C# unter Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java unter Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

Sicherheit ist einer der wichtigsten Aspekte der Kommunikation. Das Reliable Services-Anwendungsframework stellt einige fertige Kommunikationsstapel und Tools bereit, die Sie verwenden können, um die Sicherheit zu verbessern. In diesem Artikel wird erläutert, wie Sie die Sicherheit verbessern, wenn Sie Dienstremoting und den Windows Communication Foundation-Kommunikationsstapel (WCF) verwenden.

## <a name="help-secure-a-service-when-youre-using-service-remoting"></a>Unterstützung der Sicherung eines Diensts bei der Verwendung von Dienstremoting
Wir verwenden ein vorhandenes [Beispiel](service-fabric-reliable-services-communication-remoting.md), um zu erläutern, wie das Remoting für Reliable Services eingerichtet wird. Um die Sicherung eines Diensts bei der Verwendung von Dienstremoting zu unterstützen, befolgen Sie diese Schritte:

1. Erstellen Sie eine Schnittstelle, `IHelloWorldStateful`, die definiert, welche Methoden für einen Remoteprozeduraufruf Ihres Diensts verfügbar sind. Ihr Dienst verwendet `FabricTransportServiceRemotingListener`, der im Namespace `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` deklariert wird. Dies ist eine `ICommunicationListener` -Implementierung, die Remotingfunktionen bereitstellt.

    ```csharp
    public interface IHelloWorldStateful : IService
    {
        Task<string> GetHelloWorld();
    }

    internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
    {
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]{
                    new ServiceReplicaListener(
                        (context) => new FabricTransportServiceRemotingListener(context,this))};
        }

        public Task<string> GetHelloWorld()
        {
            return Task.FromResult("Hello World!");
        }
    }
    ```
2. Fügen Sie Listenereinstellungen und Sicherheitsanmeldeinformationen hinzu.

    Stellen Sie sicher, dass das Zertifikat, das Sie zum Schützen Ihrer Dienstkommunikation verwenden möchten, auf allen Knoten im Cluster installiert wird. Es gibt zwei Möglichkeiten, wie Sie Listenereinstellungen und Sicherheitsanmeldeinformationen bereitstellen können:

   1. Direkte Bereitstellung im Dienstcode:

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           FabricTransportRemotingListenerSettings  listenerSettings = new FabricTransportRemotingListenerSettings
           {
               MaxMessageSize = 10000000,
               SecurityCredentials = GetSecurityCredentials()
           };
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(context,this,listenerSettings))
           };
       }

       private static SecurityCredentials GetSecurityCredentials()
       {
           // Provide certificate details.
           var x509Credentials = new X509Credentials
           {
               FindType = X509FindType.FindByThumbprint,
               FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
               StoreLocation = StoreLocation.LocalMachine,
               StoreName = "My",
               ProtectionLevel = ProtectionLevel.EncryptAndSign
           };
           x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
           x509Credentials.RemoteCertThumbprints.Add("9FEF3950642138446CC364A396E1E881DB76B483");
           return x509Credentials;
       }
       ```
   2. Bereitstellung mithilfe eines [Konfigurationspakets](service-fabric-application-and-service-manifests.md):

       Fügen Sie in der Datei „settings.xml“ den Abschnitt `TransportSettings` hinzu.

       ```xml
       <Section Name="HelloWorldStatefulTransportSettings">
           <Parameter Name="MaxMessageSize" Value="10000000" />
           <Parameter Name="SecurityCredentialsType" Value="X509" />
           <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
           <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
           <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
           <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
           <Parameter Name="CertificateStoreName" Value="My" />
           <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
           <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
       </Section>
       ```

       In diesem Fall sieht die `CreateServiceReplicaListeners` -Methode wie folgt aus:

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(
                       context,this,FabricTransportRemotingListenerSettings .LoadFrom("HelloWorldStatefulTransportSettings")))
           };
       }
       ```

        Wenn Sie in der Datei „settings.xml“ den Abschnitt `TransportSettings` hinzufügen, lädt `FabricTransportRemotingListenerSettings ` standardmäßig alle Einstellungen aus diesem Abschnitt.

        ```xml
        <!--"TransportSettings" section .-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        In diesem Fall sieht die `CreateServiceReplicaListeners` -Methode wie folgt aus:

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]
            {
                return new[]{
                        new ServiceReplicaListener(
                            (context) => new FabricTransportServiceRemotingListener(context,this))};
            };
        }
        ```
3. Wenn Sie Methoden für einen gesicherten Dienst mit dem Remotingstapel aufrufen, statt mit der `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy`-Klasse einen Dienstproxy zu erstellen, verwenden Sie `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`. Übergeben Sie `FabricTransportRemotingSettings`, worin `SecurityCredentials` enthalten ist.

    ```csharp

    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "9FEF3950642138446CC364A396E1E881DB76B483",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
    x509Credentials.RemoteCertThumbprints.Add("4FEF3950642138446CC364A396E1E881DB76B48C");

    FabricTransportRemotingSettings transportSettings = new FabricTransportRemotingSettings
    {
        SecurityCredentials = x509Credentials,
    };

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Wenn der Clientcode als Teil eines Diensts ausgeführt wird, können Sie das `FabricTransportRemotingSettings` -Element aus der Datei „settings.xml“ laden. Erstellen Sie den Abschnitt „HelloWorldClientTransportSettings“, der dem obigen Dienstcode ähnelt. Nehmen Sie die folgenden Änderungen am Clientcode vor:

    ```csharp
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.LoadFrom("HelloWorldClientTransportSettings")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Wenn der Client nicht als Teil eines Diensts ausgeführt wird, können Sie die Datei „client_name.settings.xml“ an dem gleichen Speicherort erstellen, an dem sich auch die Datei „client_name.exe“ befindet. Erstellen Sie dann einen Abschnitt „TransportSettings“ in dieser Datei.

    Ähnlich wie beim Dienst gilt Folgendes: Wenn Sie in der Datei „client settings.xml/client_name.settings.xml“ den Abschnitt `TransportSettings` hinzufügen, lädt `FabricTransportRemotingSettings` standardmäßig alle Einstellungen aus diesem Abschnitt.

    In diesem Fall wird der frühere Code noch weiter vereinfacht:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

## <a name="help-secure-a-service-when-youre-using-a-wcf-based-communication-stack"></a>Unterstützung der Sicherung eines Diensts, wenn Sie einen WCF-basierten Kommunikationsstapel verwenden
Wir verwenden ein vorhandenes [Beispiel](service-fabric-reliable-services-communication-wcf.md), um zu erläutern, wie der WCF-basierte Kommunikationsstapel für Reliable Services eingerichtet wird. Um die Sicherung eines Diensts zu unterstützen, wenn Sie einen WCF-basierten Kommunikationsstapel verwenden, befolgen Sie diese Schritte:

1. Für den Dienst müssen Sie die Sicherung des WCF-Kommunikationslisteners (`WcfCommunicationListener`) unterstützen, den Sie erstellen. Ändern Sie dazu die `CreateServiceReplicaListeners` -Methode.

    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[]
        {
            new ServiceReplicaListener(
                this.CreateWcfCommunicationListener)
        };
    }

    private WcfCommunicationListener<ICalculator> CreateWcfCommunicationListener(StatefulServiceContext context)
    {
       var wcfCommunicationListener = new WcfCommunicationListener<ICalculator>(
            serviceContext:context,
            wcfServiceObject:this,
            // For this example, we will be using NetTcpBinding.
            listenerBinding: GetNetTcpBinding(),
            endpointResourceName:"WcfServiceEndpoint");

        // Add certificate details in the ServiceHost credentials.
        wcfCommunicationListener.ServiceHost.Credentials.ServiceCertificate.SetCertificate(
            StoreLocation.LocalMachine,
            StoreName.My,
            X509FindType.FindByThumbprint,
            "9DC906B169DC4FAFFD1697AC781E806790749D2F");
        return wcfCommunicationListener;
    }

    private static NetTcpBinding GetNetTcpBinding()
    {
        NetTcpBinding b = new NetTcpBinding(SecurityMode.TransportWithMessageCredential);
        b.Security.Message.ClientCredentialType = MessageCredentialType.Certificate;
        return b;
    }
    ```
2. Auf dem Client bleibt die `WcfCommunicationClient` -Klasse, die wir im vorherigen [Beispiel](service-fabric-reliable-services-communication-wcf.md) erstellt haben, unverändert. Sie müssen aber die `CreateClientAsync`-Methode von `WcfCommunicationClientFactory` überschreiben:

    ```csharp
    public class SecureWcfCommunicationClientFactory<TServiceContract> : WcfCommunicationClientFactory<TServiceContract> where TServiceContract : class
    {
        private readonly Binding clientBinding;
        private readonly object callbackObject;
        public SecureWcfCommunicationClientFactory(
            Binding clientBinding,
            IEnumerable<IExceptionHandler> exceptionHandlers = null,
            IServicePartitionResolver servicePartitionResolver = null,
            string traceId = null,
            object callback = null)
            : base(clientBinding, exceptionHandlers, servicePartitionResolver,traceId,callback)
        {
            this.clientBinding = clientBinding;
            this.callbackObject = callback;
        }

        protected override Task<WcfCommunicationClient<TServiceContract>> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
        {
            var endpointAddress = new EndpointAddress(new Uri(endpoint));
            ChannelFactory<TServiceContract> channelFactory;
            if (this.callbackObject != null)
            {
                channelFactory = new DuplexChannelFactory<TServiceContract>(
                this.callbackObject,
                this.clientBinding,
                endpointAddress);
            }
            else
            {
                channelFactory = new ChannelFactory<TServiceContract>(this.clientBinding, endpointAddress);
            }
            // Add certificate details to the ChannelFactory credentials.
            // These credentials will be used by the clients created by
            // SecureWcfCommunicationClientFactory.  
            channelFactory.Credentials.ClientCertificate.SetCertificate(
                StoreLocation.LocalMachine,
                StoreName.My,
                X509FindType.FindByThumbprint,
                "9DC906B169DC4FAFFD1697AC781E806790749D2F");
            var channel = channelFactory.CreateChannel();
            var clientChannel = ((IClientChannel)channel);
            clientChannel.OperationTimeout = this.clientBinding.ReceiveTimeout;
            return Task.FromResult(this.CreateWcfCommunicationClient(channel));
        }
    }
    ```

    Verwenden Sie `SecureWcfCommunicationClientFactory`, um einen WCF-Kommunikationsclient (`WcfCommunicationClient`) zu erstellen. Verwenden Sie den Client, um Dienstmethoden aufzurufen.

    ```csharp
    IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();

    var wcfClientFactory = new SecureWcfCommunicationClientFactory<ICalculator>(clientBinding: GetNetTcpBinding(), servicePartitionResolver: partitionResolver);

    var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
        wcfClientFactory,
        ServiceUri,
        ServicePartitionKey.Singleton);

    var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
        client => client.Channel.Add(2, 3)).Result;
    ```

## <a name="next-steps"></a>Nächste Schritte
* [Web-API mit OWIN in Reliable Services](service-fabric-reliable-services-communication-webapi.md)
