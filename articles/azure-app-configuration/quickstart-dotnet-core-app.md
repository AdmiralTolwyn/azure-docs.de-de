---
title: Schnellstart für Azure App Configuration mit .NET Core | Microsoft-Dokumentation
description: Schnellstartanleitung für die Verwendung von Azure App Configuration mit .NET Core-Apps
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET Core
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 34eafe8a13d126ebdbb08084d8820646ca43534a
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2019
ms.locfileid: "59998089"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Schnellstart: Erstellen einer .NET Core-App mit App Configuration

Azure App Configuration ist ein verwalteter Konfigurationsdienst in Azure. Mit diesem Dienst können Sie Ihre gesamten Anwendungseinstellungen komfortabel an einem zentralen Ort speichern und verwalten, der von Ihrem Code getrennt ist. In dieser Schnellstartanleitung erfahren Sie, wie Sie den Dienst in eine .NET Core-Konsolen-App integrieren.

Für die Ausführung der Schritte in diesem Schnellstart können Sie einen beliebigen Code-Editor verwenden. [Visual Studio Code](https://code.visualstudio.com/) ist eine hervorragende Option, die auf Windows-, macOS- und Linux-Plattformen verfügbar ist.

![Schnellstart: App-Ausführung](./media/quickstarts/dotnet-core-app-run.png)

## <a name="prerequisites"></a>Voraussetzungen

Installieren Sie das [.NET Core SDK](https://dotnet.microsoft.com/download), um diesen Schnellstart durchführen zu können.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Erstellen eines App-Konfigurationsspeichers

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Wählen Sie **Schlüssel/Wert-Explorer** > **+ Erstellen** aus, um die folgenden Schlüssel-Wert-Paare hinzuzufügen:

    | Schlüssel | Wert |
    |---|---|
    | TestApp:Settings:Message | Daten aus Azure App Configuration |

    Lassen Sie **Bezeichnung** und **Inhaltstyp** vorerst leer.

## <a name="create-a-net-core-console-app"></a>Erstellen einer .NET Core-Konsolen-App

Sie verwenden die [.NET Core-Befehlszeilenschnittstelle (CLI)](https://docs.microsoft.com/dotnet/core/tools/), um ein neues Projekt für eine .NET Core-Konsolen-App zu erstellen. Der Vorteil bei Verwendung der .NET Core-CLI gegenüber Visual Studio ist, dass sie für alle Windows-, macOS- und Linux-Plattformen verfügbar ist.

1. Erstellen Sie einen neuen Ordner für Ihr Projekt.

2. Führen Sie im neuen Ordner den folgenden Befehl aus, um ein neues Projekt vom Typ „ASP.NET Core MVC-Web-App“ zu erstellen:

        dotnet new console

## <a name="connect-to-an-app-configuration-store"></a>Herstellen einer Verbindung mit einem App-Konfigurationsspeicher

1. Fügen Sie einen Verweis auf das NuGet-Paket `Microsoft.Extensions.Configuration.AzureAppConfiguration` hinzu, indem Sie den folgenden Befehl ausführen:

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration --version 1.0.0-preview-007830001

2. Führen Sie den folgenden Befehl aus, um Pakete für Ihr Projekt wiederherzustellen:

        dotnet restore

3. Öffnen Sie *Program.cs*, und fügen Sie einen Verweis auf einen App Configuration-.NET Core-Konfigurationsanbieter hinzu.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

4. Aktualisieren Sie die `Main`-Methode für die Verwendung von App Configuration, indem Sie die Methode `builder.AddAzureAppConfiguration()` aufrufen.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));

        var config = builder.Build();
        Console.WriteLine(config["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Lokales Erstellen und Ausführen der App

1. Legen Sie eine Umgebungsvariable mit dem Namen **ConnectionString** fest, und geben Sie dafür den Zugriffsschlüssel für Ihren App-Konfigurationsspeicher an. Führen Sie bei Verwendung einer Windows-Eingabeaufforderung den folgenden Befehl aus, und starten Sie die Eingabeaufforderung neu, damit die Änderung wirksam wird:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Führen Sie bei Verwendung von Windows PowerShell den folgenden Befehl aus:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Führen Sie bei Verwendung von macOS oder Linux den folgenden Befehl aus:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Führen Sie den folgenden Befehl aus, um die Konsolen-App zu erstellen:

        dotnet build

3. Führen Sie nach der erfolgreichen Erstellung den folgenden Befehl aus, um die App lokal auszuführen:

        dotnet run

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie über den [App-Konfigurationsanbieter](https://go.microsoft.com/fwlink/?linkid=2074664) einen neuen App-Konfigurationsspeicher erstellt und mit einer .NET Core-Konsolen-App verwendet. Weitere Informationen zur Verwendung von App Configuration finden Sie im nächsten Tutorial, in dem es um die Authentifizierung geht.

> [!div class="nextstepaction"]
> [Integration der verwalteten Identität](./howto-integrate-azure-managed-service-identity.md)
