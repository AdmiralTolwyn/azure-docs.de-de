---
title: Schnellstart für Azure App Configuration mit ASP.NET Core | Microsoft-Dokumentation
description: Enthält eine Schnellstartanleitung für die Verwendung von Azure App Configuration mit ASP.NET Core-Apps.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET Core
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 6c9b257ab88b3564253b0f48d953094f84a1d71d
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59051438"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Schnellstart: Erstellen einer ASP.NET Core-App mit Azure App Configuration

Azure App Configuration ist ein verwalteter Konfigurationsdienst in Azure. Mit diesem Dienst können Sie Ihre gesamten Anwendungseinstellungen komfortabel an einem zentralen Ort speichern und verwalten, der von Ihrem Code getrennt ist. In dieser Schnellstartanleitung wird veranschaulicht, wie Sie den Dienst in eine ASP.NET Core-Web-App einbinden. 

Mit ASP.NET Core wird ein Konfigurationsobjekt basierend auf nur einem Schlüssel-Wert-Paar erstellt, indem Einstellungen einer oder mehrerer Datenbanken verwendet werden, die von einer Anwendung angegeben werden. Diese Datenquellen werden als *Konfigurationsanbieter* bezeichnet. Da der .NET Core-Client von App Configuration als ein Anbieter dieser Art implementiert wird, wird der Dienst wie jede andere Datenquelle angezeigt.

Sie können einen beliebigen Code-Editor nutzen, um die Schritte dieser Schnellstartanleitung auszuführen. [Visual Studio Code](https://code.visualstudio.com/) ist eine hervorragende Option, die auf Windows-, macOS- und Linux-Plattformen verfügbar ist.

## <a name="prerequisites"></a>Voraussetzungen

Installieren Sie das [.NET Core SDK](https://dotnet.microsoft.com/download), um diesen Schnellstart durchführen zu können.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Erstellen eines App-Konfigurationsspeichers

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>Erstellen einer ASP.NET Core-Web-App

Sie verwenden die [.NET Core-Befehlszeilenschnittstelle (CLI)](https://docs.microsoft.com/dotnet/core/tools/), um ein neues Projekt vom Typ „ASP.NET Core MVC-Web-App“ zu erstellen. Der Vorteil bei Verwendung der .NET Core-CLI gegenüber Visual Studio ist, dass sie für alle Windows-, macOS- und Linux-Plattformen verfügbar ist.

1. Erstellen Sie einen neuen Ordner für Ihr Projekt. Für diese Schnellstartanleitung verwenden wir den Namen *TestAppConfig*.

2. Führen Sie im neuen Ordner den folgenden Befehl aus, um ein neues Projekt vom Typ „ASP.NET Core MVC-Web-App“ zu erstellen:

        dotnet new mvc

## <a name="add-secret-manager"></a>Hinzufügen des Geheimnis-Managers

Fügen Sie Ihrem Projekt das [Geheimnis-Manager-Tool](https://docs.microsoft.com/aspnet/core/security/app-secrets) hinzu. Im Geheimnis-Manager-Tool werden sensible Daten für die Entwicklungsarbeit außerhalb Ihrer Projektstruktur gespeichert. Mit diesem Ansatz können Sie verhindern, dass App-Geheimnisse versehentlich im Quellcode angegeben werden.

- Öffnen Sie die Datei mit der Endung *.csproj*. Fügen Sie, wie hier gezeigt, ein `UserSecretsId`-Element hinzu, und ersetzen Sie dessen Wert durch Ihren eigenen Wert. Normalerweise ist dies eine GUID. Speichern Sie die Datei .

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.App" />
        <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
    </ItemGroup>

    </Project>
    ```

## <a name="connect-to-an-app-configuration-store"></a>Herstellen einer Verbindung mit einem App-Konfigurationsspeicher

1. Fügen Sie einen Verweis auf das NuGet-Paket `Microsoft.Extensions.Configuration.AzureAppConfiguration` hinzu, indem Sie den folgenden Befehl ausführen:

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration --version 1.0.0-preview-007830001

2. Führen Sie den folgenden Befehl aus, um Pakete für Ihr Projekt wiederherzustellen:

        dotnet restore

3. Fügen Sie dem Geheimnis-Manager ein Geheimnis mit dem Namen *ConnectionStrings:AppConfig* hinzu.

    Dieses Geheimnis enthält die Verbindungszeichenfolge für den Zugriff auf Ihren App-Konfigurationsspeicher. Ersetzen Sie den Wert im folgenden Befehl durch die Verbindungszeichenfolge für Ihren App-Konfigurationsspeicher.

    Dieser Befehl muss in dem Verzeichnis ausgeführt werden, in dem die *.csproj*-Datei enthalten ist.

        dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>

    Der Geheimnis-Manager wird nur verwendet, um die Web-App lokal zu testen. Nach dem Bereitstellen der App (z. B. für [Azure App Service](https://azure.microsoft.com/services/app-service/web)) verwenden Sie eine Anwendungseinstellung, z. B. **Verbindungszeichenfolgen** in App Service. Sie verwenden diese Einstellung, anstatt die Verbindungszeichenfolge im Geheimnis-Manager zu speichern.

    Auf dieses Geheimnis wird mit der Konfigurations-API zugegriffen. Ein Doppelpunkt (:) kann im Konfigurationsnamen mit der Konfigurations-API auf allen unterstützten Plattformen verwendet werden. Siehe [Konfiguration nach Umgebung](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0).

4. Öffnen Sie *Program.cs*, und fügen Sie einen Verweis auf einen App Configuration-.NET Core-Konfigurationsanbieter hinzu.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

5. Aktualisieren Sie die `CreateWebHostBuilder`-Methode für die Verwendung von App Configuration, indem Sie die Methode `config.AddAzureAppConfiguration()` aufrufen.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .SetOfflineCache(new OfflineFileCache());
                });
            })
            .UseStartup<Startup>();
    ```

6. Öffnen Sie die Datei „Index.cshtml“ im Verzeichnis „Views > Home“, und ersetzen Sie dessen Inhalt durch den folgenden Code:

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@Configuration["TestApp:Settings:Message"]</h1>
    </body>
    </html>
    ```

7. Öffnen Sie die Datei „_Layout.cshtml“ im Verzeichnis „Views > Shared“, und ersetzen Sie dessen Inhalt durch den folgenden Code:

    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>@ViewData["Title"] - hello_world</title>

        <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
        <link rel="stylesheet" href="~/css/site.css" />
    </head>
    <body>
        <div class="container body-content">
            @RenderBody()
        </div>

        <script src="~/lib/jquery/dist/jquery.js"></script>
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>

        @RenderSection("Scripts", required: false)
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Lokales Erstellen und Ausführen der App

1. Führen Sie den folgenden Befehl in der Befehlsshell aus, um die App mithilfe der .NET Core-CLI zu erstellen:

        dotnet build

2. Führen Sie nach der erfolgreichen Erstellung den folgenden Befehl aus, um die Web-App lokal auszuführen:

        dotnet run

3. Öffnen Sie ein Browserfenster, und navigieren Sie zu `http://localhost:5000`. Dies ist die Standard-URL für die lokal gehostete Web-App.

    ![Schnellstartanleitung: Lokales Starten der App](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie über den [App-Konfigurationsanbieter](https://go.microsoft.com/fwlink/?linkid=2074664) einen neuen App-Konfigurationsspeicher erstellt und mit einer ASP.NET Core-Web-App verwendet. Weitere Informationen zur Verwendung von App Configuration finden Sie im nächsten Tutorial, in dem es um die Authentifizierung geht.

> [!div class="nextstepaction"]
> [Tutorial: Integrieren mit verwalteten Azure-Identitäten](./integrate-azure-managed-service-identity.md)
