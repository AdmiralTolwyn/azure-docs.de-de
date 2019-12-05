---
title: Schnellstart für Azure App Configuration mit .NET Framework | Microsoft-Dokumentation
description: Enthält eine Schnellstartanleitung für die Verwendung von Azure App Configuration mit .NET Framework-Apps.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 10/09/2019
ms.author: lcozzens
ms.openlocfilehash: 36cfe79f5c2735e6d6737d471430deb989905cdc
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185061"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Schnellstart: Erstellen einer .NET Framework-App mit Azure App Configuration

In dieser Schnellanleitung integrieren Sie Azure App Configuration in eine auf .NET Framework basierende Konsolen-App, um die Speicherung und Verwaltung von Anwendungseinstellungen getrennt von Ihrem Code zu zentralisieren.

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.2](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Erstellen eines App Configuration-Speichers

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Wählen Sie **Konfigurations-Explorer** >  **+ Erstellen** aus, um die folgenden Schlüssel-Wert-Paare hinzuzufügen:

    | Schlüssel | Wert |
    |---|---|
    | TestApp:Settings:Message | Daten aus Azure App Configuration |

    Lassen Sie **Bezeichnung** und **Inhaltstyp** vorerst leer.

## <a name="create-a-net-console-app"></a>Erstellen einer .NET-Konsolen-App

1. Starten Sie Visual Studio, und wählen Sie **Datei** > **Neu** > **Projekt** aus.

1. Filtern Sie unter **Neues Projekt erstellen** nach dem Projekttyp **Konsole**, und klicken Sie auf **Console App (.NET Framework)** (Konsolen-App (.NET Framework)). Klicken Sie auf **Weiter**.

1. Geben Sie unter **Neues Projekt konfigurieren** einen Projektnamen ein. Wählen Sie unter **Framework** die Option **.NET Framework 4.7.1** oder höher aus. Klicken Sie auf **Create**.

## <a name="connect-to-an-app-configuration-store"></a>Herstellen einer Verbindung mit einem App Configuration-Speicher

1. Klicken Sie mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **NuGet-Pakete verwalten** aus. Suchen Sie auf der Registerkarte **Durchsuchen** die folgenden NuGet-Pakete, und fügen Sie sie Ihrem Projekt hinzu. Wenn Sie sie nicht finden können, aktivieren Sie das Kontrollkästchen **Vorabversion einbeziehen**.

    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    System.Configuration.ConfigurationManager version 4.6.0 or later
    ```

1. Aktualisieren Sie die Datei *App.config* Ihres Projekts wie folgt:

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" connectionString="${ConnectionString}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="ConnectionString" value ="Set via an environment variable - for example, dev, test, staging, or production connection string." />
    </appSettings>
    ```

   Die Verbindungszeichenfolge Ihres App Configuration-Speichers wird aus der Umgebungsvariablen `ConnectionString` gelesen. Fügen Sie den Konfigurations-Generator `Environment` vor `MyConfigStore` in der Eigenschaft `configBuilders` des Abschnitts `appSettings` ein.

1. Öffnen Sie die Datei *Program.cs*, und aktualisieren Sie die `Main`-Methode für die Verwendung von App Configuration, indem Sie `ConfigurationManager` aufrufen.

    ```csharp
    static void Main(string[] args)
    {
        string message = System.Configuration.ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Lokales Erstellen und Ausführen der App

1. Legen Sie eine Umgebungsvariable mit dem Namen **ConnectionString** auf die Verbindungszeichenfolge Ihres App Configuration-Speichers fest. Führen Sie bei Verwendung der Windows-Eingabeaufforderung den folgenden Befehl aus:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Führen Sie bei Verwendung der Windows PowerShell den folgenden Befehl aus:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. Starten Sie Visual Studio, damit die Änderung wirksam wird. Drücken Sie STRG+F5, um die Konsolen-App zu erstellen und auszuführen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie einen neuen App Configuration-Speicher erstellt und mit einer .NET Framework-Konsolen-App verwendet. Der Wert `AppSettings` von `ConfiguratoinManager` ändert sich nach dem Start der Anwendung nicht mehr. Die Bibliothek des .NET Standard-Konfigurationsanbieters App Configuration kann jedoch auch in einer .NET Framework-App verwendet werden. Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Ihre .NET Framework-App für das dynamische Aktualisieren der Konfigurationseinstellungen konfigurieren.

> [!div class="nextstepaction"]
> [Tutorial: Verwenden der dynamischen Konfiguration in einer .NET Framework-App](./enable-dynamic-configuration-dotnet.md)
