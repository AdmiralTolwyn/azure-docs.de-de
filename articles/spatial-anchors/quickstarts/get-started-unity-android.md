---
title: 'Schnellstart: Erstellen einer Unity-Android-App'
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie eine Android-App mit Unity erstellen, indem Sie Spatial Anchors verwenden.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 213cf9b64f2c43274192c22efa2fa5a7dfbce5e5
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277061"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>Schnellstart: Erstellen einer Unity Android-App mit Azure Spatial Anchors

In dieser Schnellstartanleitung wird beschrieben, wie Sie mit [Azure Spatial Anchors](../overview.md) eine Unity Android-App erstellen. Azure Spatial Anchors ist ein plattformübergreifender Entwicklerdienst, mit dem Sie Mixed Reality-Umgebungen mit Objekten erstellen können, die ihre Position im Zeitverlauf geräteübergreifend beibehalten. Nach Abschluss des Vorgangs verfügen Sie über eine mit Unity erstellte ARCore-Android-App, mit der ein räumlicher Anker gespeichert und abgerufen werden kann.

Sie lernen Folgendes:

> [!div class="checklist"]
> * Erstellen eines Spatial Anchors-Kontos
> * Vorbereiten von Unity-Buildeinstellungen
> * Konfigurieren des Bezeichners und Kontoschlüssels für das Spatial Anchors-Konto
> * Exportieren des Android Studio-Projekts
> * Bereitstellen und Ausführen auf einem Android-Gerät

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie für diese Schnellstartanleitung sicher, dass Sie über Folgendes verfügen:

- Einen Windows- oder macOS-Computer mit <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.1 oder höher</a>, einschließlich der Module für Android-Buildunterstützung sowie Android SDK- und NDK-Tools.
  - Unter Windows benötigen Sie auch <a href="https://git-scm.com/download/win" target="_blank">Git für Windows</a>.
  - Unter macOS muss Git über Homebrew installiert werden. Geben Sie den folgenden Befehl in einer einzelnen Zeile am Terminal ein: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Führen Sie anschließend `brew install git` aus.
- Ein <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">für Entwickler geeignetes</a> und <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore-fähiges</a> Android-Gerät.
  - Möglicherweise sind zusätzliche Gerätetreiber erforderlich, damit Ihr Computer mit Ihrem Android-Gerät kommunizieren kann. Weitere Informationen und Anweisungen finden Sie [hier](https://developer.android.com/studio/run/device.html).

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Herunterladen und Öffnen des Unity-Beispielprojekts

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Konfigurieren des Kontobezeichners und -schlüssels

Navigieren Sie im Bereich **Projekt** zu `Assets/AzureSpatialAnchors.Examples/Scenes`, und öffnen Sie die Szenendatei `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Speichern Sie die Szene, indem Sie **Datei** -> **Speichern** wählen.

## <a name="export-the-android-studio-project"></a>Exportieren des Android Studio-Projekts

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Wählen Sie unter **Run Device** (Gerät ausführen) Ihr Gerät aus, und klicken Sie auf **Build And Run** (Erstellen und ausführen). Sie werden zum Speichern einer Datei vom Typ `.apk` aufgefordert und können einen beliebigen Namen dafür wählen.

Befolgen Sie in der App die Anleitung zum Anordnen und Abrufen eines Ankers.

## <a name="troubleshooting"></a>Problembehandlung

Falls beim Ausführen der App die Kamera nicht als Hintergrund angezeigt wird (sondern stattdessen beispielsweise ein leerer blauer Bereich oder andere Texturen), müssen Sie die Ressourcen wahrscheinlich erneut in Unity importieren. Beenden Sie die App. Wählen Sie in Unity im Menü am oberen Rand die Option **Assets > Re-import all** (Ressourcen > Alle erneut importieren). Führen Sie die App dann erneut aus.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Freigeben für Sitzungen und Geräte mit Azure Spatial Anchors](../tutorials/tutorial-share-anchors-across-devices.md)
