---
title: Erweiterte Windows Universal-Berichterstellung mit Mobile Engagement
description: Integrieren von Azure Mobile Engagement in Windows Universal-Apps
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: ea5030bf-73ac-49b7-bc3e-c25fc10e945a
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: feac309db1ffce0945012e293bfc1df417aed876
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="advanced-reporting-with-the-windows-universal-apps-engagement-sdk"></a>Erweiterte Berichterstellung Engagement-SDK für Windows Universal-Apps
> [!div class="op_single_selector"]
> * [Universal Windows](mobile-engagement-windows-store-advanced-reporting.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-reporting.md)
> 
> 

Dieses Thema beschreibt zusätzliche Berichterstellungsszenarien in Ihrer Windows Universal-Anwendung. Diese Szenarien umfassen Optionen, die Sie auswählen und auf die App anwenden können, die im [Erste Schritte](mobile-engagement-windows-store-dotnet-get-started.md) -Tutorial erstellt wird.

## <a name="prerequisites"></a>Voraussetzungen
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

Bevor Sie mit diesem Tutorial beginnen, müssen Sie zunächst das Tutorial [Erste Schritte](mobile-engagement-windows-store-dotnet-get-started.md) abschließen, was absichtlich direkt und einfach gehalten ist. Dieses Tutorial behandelt zusätzliche Optionen, die Sie auswählen können.

## <a name="specifying-engagement-configuration-at-runtime"></a>Angeben der Engagement-Konfiguration zur Laufzeit
Die Engagement-Konfiguration befindet sich zentral in der `Resources\EngagementConfiguration.xml` -Datei Ihres Projekts, wo sie im Thema [Erste Schritte](mobile-engagement-windows-store-dotnet-get-started.md) angegeben wurde.

Doch Sie können sie auch zur Laufzeit angeben: Rufen Sie die folgende Methode vor der Initialisierung des Engagement-Agent auf:

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);



## <a name="recommended-method-overload-your-page-classes"></a>Empfohlene Methode: Überladen der `Page`-Klassen
Um den Bericht über alle Protokolle zu aktivieren, die zum Berechnen von Benutzern, Sitzungen, Aktivitäten, Abstürzen und technischen Statistiken durch Engagement erforderlich sind, lassen Sie einfach alle Ihre `Page`-Unterklassen von den `EngagementPage`-Klassen erben.

Hier ist ein Beispiel für eine Seite Ihrer Anwendung. Das Gleiche gilt für alle Seiten der Anwendung.

### <a name="c-source-file"></a>C#-Quelldatei
Ändern Sie die Datei Ihrer Seite `.xaml.cs` :

* Fügen Sie Ihre Anweisungen `using` hinzu:
  
      using Microsoft.Azure.Engagement;
* Ersetzen Sie `Page` durch `EngagementPage`:

**Ohne Engagement:**

        namespace Example
        {
          public sealed partial class ExamplePage : Page
          {
            [...]
          }
        }

**Mit Engagement:**

        using Microsoft.Azure.Engagement;

        namespace Example
        {
          public sealed partial class ExamplePage : EngagementPage
          {
            [...]
          }
        }

> [!IMPORTANT]
> Wenn Ihre Seite die Methode `OnNavigatedTo` überschreibt, rufen Sie unbedingt `base.OnNavigatedTo(e)` auf. Andernfalls wird die Aktivität nicht erfasst (`EngagementPage` ruft `StartActivity` innerhalb der `OnNavigatedTo`-Methode auf).
> 
> 

### <a name="xaml-file"></a>XAML-Datei
Ändern Sie die Datei Ihrer Seite `.xaml` :

* Fügen Sie Ihre Namespace-Deklarationen hinzu:
  
      xmlns:engagement="using:Microsoft.Azure.Engagement"
* Ersetzen Sie `Page` durch `engagement:EngagementPage`:

**Ohne Engagement:**

        <Page>
            <!-- layout -->
            ...
        </Page>

**Mit Engagement:**

        <engagement:EngagementPage
            xmlns:engagement="using:Microsoft.Azure.Engagement">
            <!-- layout -->
            ...
        </engagement:EngagementPage >

### <a name="override-the-default-behaviour"></a>Überschreiben Sie das Standardverhalten
Standardmäßig wird der Klassenname der Seite als der Name der Aktivität gemeldet, ohne Zusatz. Wenn die Klasse das Suffix „Page“ verwendet, löscht Engagement dieses.

Um das Standardverhalten für den Namen zu überschreiben, fügen Sie diesen Code hinzu:

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

Um zusätzliche Informationen mit Ihrer Aktivität zu melden, fügen Sie diesen Code hinzu:

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
          /* your code */
          return extra;
        }

Diese Methoden werden von innerhalb der Methode `OnNavigatedTo` Ihrer Seite aufgerufen.

### <a name="alternate-method-call-startactivity-manually"></a>Alternative Methode: Rufen Sie `StartActivity()` manuell auf
Wenn Sie Ihre `Page`-Klassen nicht überladen möchten oder können, können Sie stattdessen Ihre Aktivitäten durch direktes Aufrufen der `EngagementAgent`-Methoden starten.

Sie sollten `StartActivity` innerhalb Ihrer Methode `OnNavigatedTo` der Seite aufrufen.

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [!IMPORTANT]
> Stellen Sie sicher, dass Ihre Sitzung ordnungsgemäß beendet wird.
> 
> Das Windows Universal-SDK ruft automatisch die Methode `EndActivity` auf, wenn die Anwendung geschlossen wird. Daher wird **dringend** empfohlen, bei jeder Änderung der Benutzeraktivität die Methode `StartActivity` und **niemals** die Methode `EndActivity` aufzurufen. Diese Methode teilt dem Engagement-Server mit, dass der aktuelle Benutzer die Anwendung verlassen hat, was sich auf alle Anwendungsprotokolle auswirkt.
> 
> 

## <a name="advanced-reporting"></a>Erweiterte Berichterstellung
Möglicherweise möchten Sie anwendungsspezifische Ereignisse, Fehler und Aufträge melden; verwenden Sie dazu die anderen Methoden in der Klasse `EngagementAgent` . Mit der Engagement-API können alle erweiterten Funktionen von Engagement verwendet werden.

Weitere Informationen finden Sie unter [Verwenden der erweiterten Mobile Engagement-Tagging-API in Ihrer Windows Universal-App](mobile-engagement-windows-store-use-engagement-api.md).

