---
author: tomarcher
ms.service: jenkins
ms.topic: include
ms.date: 03/03/2020
ms.author: tarcher
ms.openlocfilehash: e9b8ad7a7fcc499f8760b56e6a737be8a6a9e06c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79200246"
---
## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement
* Zugriff auf SSH über die Befehlszeile Ihres Computers (beispielsweise Bash-Shell oder [PuTTY](https://www.putty.org/))

[!INCLUDE [quickstarts-free-trial-note](quickstarts-free-trial-note.md)]

## <a name="create-the-jenkins-vm-from-the-solution-template"></a>Erstellen des virtuellen Jenkins-Computers auf der Grundlage der Lösungsvorlage
Jenkins unterstützt ein Modell, bei dem der Jenkins-Server die Arbeit an einen oder mehrere Agents delegiert, sodass eine einzelne Jenkins-Installation eine große Anzahl von Projekten hosten oder verschiedene Umgebungen bereitstellen kann, die für Builds oder Tests benötigt werden. Die Schritte in diesem Abschnitt führen Sie durch die Installation und Konfiguration eines Jenkins-Servers in Azure.

[!INCLUDE [jenkins-install-from-azure-marketplace-image](jenkins-install-from-azure-marketplace-image.md)]

## <a name="connect-to-jenkins"></a>Herstellen einer Verbindung mit Jenkins

Navigieren Sie in Ihrem Webbrowser zu Ihrem virtuellen Computer (beispielsweise `http://jenkins2517454.eastus.cloudapp.azure.com/`). Auf die Jenkins-Konsole kann nicht über eine unsichere HTTP-Verbindung zugegriffen werden. Daher ist auf der Seite beschrieben, wie Sie von Ihrem Computer aus über einen SSH-Tunnel sicher auf die Jenkins-Konsole zugreifen können.

![Entsperren von Jenkins](./media/jenkins-install-solution-template-steps/jenkins-ssh-instructions.png)

Richten Sie den Tunnel ein, indem Sie den auf der Seite angegebenen Befehl `ssh` über die Befehlszeile ausführen. Ersetzen Sie dabei `username` durch den Namen des VM-Administratorbenutzers, den Sie beim Einrichten des virtuellen Computers auf der Grundlage der Lösungsvorlage gewählt haben.

```bash
ssh -L 127.0.0.1:8080:localhost:8080 jenkinsadmin@jenkins2517454.eastus.cloudapp.azure.com
```

Navigieren Sie nach dem Starten des Tunnels auf Ihrem lokalen Computer zu `http://localhost:8080/`. 

Führen Sie über die Befehlszeile den folgenden Befehl aus, während eine SSH-Verbindung mit dem virtuellen Jenkins-Computer besteht, um das anfängliche Kennwort abzurufen.

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Entsperren Sie das Jenkins-Dashboard zunächst mit diesem anfänglichen Kennwort.

![Entsperren von Jenkins](./media/jenkins-install-solution-template-steps/jenkins-unlock.png)

Wählen Sie auf der nächsten Seite **Install suggested plugins** (Vorgeschlagene Plug-Ins installieren) aus, und erstellen Sie einen Jenkins-Administratorbenutzer für den Zugriff auf das Jenkins-Dashboard.

![Jenkins ist bereit!](./media/jenkins-install-solution-template-steps/jenkins-welcome.png)

Der Jenkins-Server ist nun für die Codeerstellung bereit.

## <a name="create-your-first-job"></a>Erstellen Ihres ersten Auftrags

Wählen Sie in der Jenkins-Konsole die Option **Create new jobs** (Neue Aufträge erstellen) aus, und nennen Sie den Auftrag **mySampleApp**. Wählen Sie dann **Freestyle project** (Freestyle-Projekt) und anschließend **OK** aus.

![Erstellen eines neuen Auftrags](./media/jenkins-install-solution-template-steps/jenkins-new-job.png) 

Aktivieren Sie auf der Registerkarte **Source Code Management** (Quellcodeverwaltung) die Option **Git**, und geben Sie im Feld **Repository URL** (Repository-URL) die folgende URL ein: `https://github.com/spring-guides/gs-spring-boot.git`

![Definieren des Git-Repositorys](./media/jenkins-install-solution-template-steps/jenkins-job-git-configuration.png) 

Wählen Sie auf der Registerkarte **Build** die Option **Add build step** (Buildschritt hinzufügen) und anschließend **Invoke Gradle script** (Gradle-Skript aufrufen) aus. Wählen Sie **Use Gradle Wrapper** (Gradle-Wrapper verwenden) aus, und geben Sie dann im Feld `complete`Wrapper location **(Wrapperspeicherort) die Zeichenfolge** und im Feld `build`Tasks **(Aufgaben) die Zeichenfolge** ein.

![Verwenden des Gradle-Wrappers für die Erstellung](./media/jenkins-install-solution-template-steps/jenkins-job-gradle-config.png) 

Wählen Sie **Erweitert** aus, und geben Sie dann im Feld `complete`Root Build script **(Stammerstellungsskript) die Zeichenfolge** ein. Wählen Sie **Speichern** aus.

![Festlegen erweiterter Einstellungen im Buildschritt des Gradle-Wrappers](./media/jenkins-install-solution-template-steps/jenkins-job-gradle-advances.png) 

## <a name="build-the-code"></a>Erstellen des Codes

Wählen Sie **Build Now** (Jetzt erstellen) aus, um den Code zu kompilieren und das Paket für die Beispiel-App zu erstellen. Wählen Sie nach Abschluss des Buildvorgangs den Link **Workspace** (Arbeitsbereich) für das Projekt aus.

![Navigieren zum Arbeitsbereich, um die JAR-Datei aus dem Build abzurufen](./media/jenkins-install-solution-template-steps/jenkins-access-workspace.png) 

Vergewissern Sie sich unter `complete/build/libs`, dass die Datei `gs-spring-boot-0.1.0.jar` vorhanden ist, um sicherzugehen, dass der Buildvorgang erfolgreich war. Der Jenkins-Server ist nun für die Erstellung Ihrer eigenen Projekte in Azure bereit.

## <a name="troubleshooting-the-jenkins-solution-template"></a>Problembehandlung bei der Jenkins-Lösungsvorlage

Wenn bei der Jenkins-Lösungsvorlage Fehler auftreten, melden Sie das Problem im [Jenkins-GitHub-Repository](https://github.com/azure/jenkins/issues).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Verwenden Sie Azure-VM-Agents für die Continuous Integration mit Jenkins.](/azure/jenkins/jenkins-azure-vm-agents)