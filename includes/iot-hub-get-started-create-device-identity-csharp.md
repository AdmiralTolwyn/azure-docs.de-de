## <a name="create-a-device-identity"></a>Erstellen einer Geräteidentität

In diesem Abschnitt erstellen Sie eine .NET-Konsolen-App, mit der eine Geräteidentität in der Identitätsregistrierung Ihres IoT-Hubs erstellt wird. Ein Gerät kann nur eine Verbindung mit IoT Hub herstellen, wenn in der Identitätsregistrierung ein Eintrag für dieses Gerät vorhanden ist. Weitere Informationen finden Sie im [IoT Hub-Entwicklerhandbuch][lnk-devguide-identity] im Abschnitt zur Identitätsregistrierung. Wenn Sie diese Konsolen-App ausführen, generiert sie eine eindeutige Geräte-ID und einen eindeutigen Schlüssel. Ihr Gerät verwendet diese Werte, um sich beim Senden von D2C-Nachrichten an IoT Hub zu identifizieren. Bei Geräte-IDs wird die Groß-/Kleinschreibung beachtet.

1. Fügen Sie in Visual Studio einer neuen Projektmappe mithilfe der Projektvorlage **Konsolenanwendung (.NET Framework** ein Visual C#-Projekt für den klassischen Windows-Desktop hinzu. Stellen Sie sicher, dass .NET-Framework-Version 4.5.1 oder höher verwendet wird. Nennen Sie das Projekt **CreateDeviceIdentity** und die Projektmappe **IoTHubGetStarted**.

    ![Neues Visual C#-Projekt für den klassischen Windows-Desktop][10]

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **CreateDeviceIdentity**, und klicken Sie dann auf **NuGet-Pakete verwalten**.

1. Wählen Sie im Fenster **NuGet-Paket-Manager** die Option **Durchsuchen** aus, suchen Sie nach **microsoft.azure.devices**, wählen Sie zum Installieren des Pakets **Microsoft.Azure.Devices** die Option **Installieren** aus, und akzeptieren Sie die Nutzungsbedingungen. Bei diesem Verfahren wird das NuGet-Paket [Azure IoT-Dienst-SDK][lnk-nuget-service-sdk] heruntergeladen und installiert und ein Verweis auf das Paket und seine Abhängigkeiten hinzugefügt.

    ![Fenster „NuGet-Paket-Manager“][11]

1. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:

    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Common.Exceptions;
    ```

1. Fügen Sie der **Program** -Klasse die folgenden Felder hinzu. Ersetzen Sie den Platzhalterwert durch die IoT Hub-Verbindungszeichenfolge für den Hub, den Sie im vorherigen Abschnitt erstellt haben.

    ```csharp
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

1. Fügen Sie der **Program** -Klasse die folgende Methode hinzu:

    ```csharp
    private static async Task AddDeviceAsync()
    {
      string deviceId = "myFirstDevice";
      Device device;
      try
      {
          device = await registryManager.AddDeviceAsync(new Device(deviceId));
      }
      catch (DeviceAlreadyExistsException)
      {
          device = await registryManager.GetDeviceAsync(deviceId);
      }
      Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
    }
    ```

    Mit dieser Methode wird eine Geräteidentität mit der ID **myFirstDevice** erstellt. (Falls diese Geräte-ID in der Identitätsregistrierung bereits vorhanden ist, werden mit dem Code lediglich die vorhandenen Geräteinformationen abgerufen.) Anschließend zeigt die App den Primärschlüssel für diese Identität an. Sie verwenden diesen Schlüssel in der simulierten Geräte-App, um eine Verbindung mit Ihrem IoT-Hub herzustellen.
[!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Fügen Sie abschließend der **Main**-Methode die folgenden Zeilen hinzu:

    ```csharp
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddDeviceAsync().Wait();
    Console.ReadLine();
    ```

1. Führen Sie diese Anwendung aus, und notieren Sie den Geräteschlüssel.

    ![Von der Anwendung generierter Geräteschlüssel][12]

> [!NOTE]
> Die Identitätsregistrierung in IoT Hub speichert nur Geräteidentitäten, um einen sicheren Zugriff auf den IoT-Hub zu ermöglichen. In der Identitätsregistrierung werden Geräte-IDs und -schlüssel für die Verwendung als Sicherheitsanmeldeinformationen gespeichert. Darüber hinaus wird in der Identitätsregistrierung ein Flag für den Aktivierungszustand des jeweiligen Geräts gespeichert, mit dem Sie den Zugriff für das betreffende Gerät deaktivieren können. Wenn Ihre Anwendung das Speichern weiterer gerätespezifischer Metadaten erfordert, sollte dafür ein anwendungsspezifischer Speicher verwendet werden. Weitere Informationen finden Sie im [IoT Hub-Entwicklerhandbuch][lnk-devguide-identity].

<!-- Images. -->
[10]: ./media/iot-hub-get-started-create-device-identity-csharp/create-identity-csharp1.png
[11]: ./media/iot-hub-get-started-create-device-identity-csharp/create-identity-csharp2.png
[12]: ./media/iot-hub-get-started-create-device-identity-csharp/create-identity-csharp3.png

<!-- Links -->
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
