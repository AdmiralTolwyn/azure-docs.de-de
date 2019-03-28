---
title: 'Schnellstart: Erstellen einer Android-Unity-App mit Azure Spatial Anchors | Microsoft-Dokumentation'
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie eine Android-App mit Unity erstellen, indem Sie Spatial Anchors verwenden.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 21a10bcedc25ddce63ba468e400dcea1f77148a0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57863116"
---
# <a name="quickstart-create-an-android-unity-app-with-azure-spatial-anchors"></a>Schnellstart: Erstellen einer Android-Unity-App mit Azure Spatial Anchors

In dieser Schnellstartanleitung wird beschrieben, wie Sie mit [Azure Spatial Anchors](../overview.md) eine Android-Unity-App erstellen. Azure Spatial Anchors ist ein plattformübergreifender Entwicklerdienst, mit dem Sie Mixed Reality-Umgebungen mit Objekten erstellen können, die ihre Position im Zeitverlauf geräteübergreifend beibehalten. Nach Abschluss des Vorgangs verfügen Sie über eine mit Unity erstellte ARCore-Android-App, mit der ein räumlicher Anker gespeichert und abgerufen werden kann.

Sie lernen Folgendes:

> [!div class="checklist"]
> * Erstellen eines Spatial Anchors-Kontos
> * Vorbereiten von Unity-Buildeinstellungen
> * Herunterladen und Importieren des ARCore-SDK für Unity
> * Konfigurieren des Bezeichners und Kontoschlüssels für das Spatial Anchors-Konto
> * Exportieren des Android Studio-Projekts
> * Bereitstellen und Ausführen auf einem Android-Gerät

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie für diese Schnellstartanleitung sicher, dass Sie über Folgendes verfügen:

- Ein Windows- oder macOS-Computer mit <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3+</a> und <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3+</a>.
  - Unter Windows benötigen Sie auch <a href="https://git-scm.com/download/win" target="_blank">Git für Windows</a>.
  - Unter macOS muss Git über Homebrew installiert werden. Geben Sie den folgenden Befehl in einer einzelnen Terminalzeile ein: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Führen Sie anschließend `brew install git` aus.
- Ein <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">für Entwickler geeignetes</a> und <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore-fähiges</a> Android-Gerät.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Öffnen des Beispielprojekts in Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Konfigurieren des Kontobezeichners und -schlüssels

Navigieren Sie im Bereich **Projekt** zu `Assets/AzureSpatialAnchorsPlugin/Examples`, und öffnen Sie die Szenendatei `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Speichern Sie die Szene, indem Sie **Datei** -> **Speichern** wählen.

## <a name="export-the-android-studio-project"></a>Exportieren des Android Studio-Projekts

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Stellen Sie sicher, dass das Kontrollkästchen **Projekt exportieren** nicht aktiviert ist. Klicken Sie auf **Erstellen und ausführen**. Sie werden zum Speichern Ihrer `.apk`-Datei aufgefordert und können einen beliebigen Namen dafür wählen.

Befolgen Sie in der App die Anleitung zum Anordnen und Abrufen eines Ankers.

> [!NOTE]
> Falls beim Ausführen der App die Kamera nicht als Hintergrund angezeigt wird (sondern stattdessen beispielsweise ein leerer blauer Bereich oder andere Texturen), müssen Sie die Ressourcen wahrscheinlich erneut in Unity importieren. Beenden Sie die App. Wählen Sie in Unity im Menü am oberen Rand die Option **Assets > Re-import all** (Ressourcen > Alle erneut importieren). Führen Sie die App dann erneut aus.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Freigeben für Sitzungen und Geräte mit Azure Spatial Anchors](../tutorials/tutorial-share-anchors-across-devices.md)
