<properties
    pageTitle="Erstellen von Azure RemoteApp-Images | Microsoft Azure"
    description="Erfahren Sie mehr über die verfügbaren Optionen zum Erstellen von Images für Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# Erstellen von Azure RemoteApp-Images

> [AZURE.IMPORTANT]
Azure RemoteApp wird eingestellt. Details finden Sie in der [Ankündigung](https://go.microsoft.com/fwlink/?linkid=821148).

Azure RemoteApp verwendet Images, die die Anwendungen enthalten, die Sie für Benutzer freigeben. (Wir erstellen mit Ihrem Image virtuelle Computer – das ist es, worauf Benutzer zugreifen, wenn sie sich bei Azure RemoteApp anmelden.) Zum Erstellen einer Azure RemoteApp-Sammlung mit Ihrer Auswahl an Anwendungen, sei es Cloud oder Hybrid, erstellen Sie zunächst ein Image, auf dem diese Anwendungen installiert sind. Erstellen Sie dann eine Sammlung mit diesem Image, weisen Sie der Sammlung Benutzer zu, und geben Sie für diese Benutzer Anwendungen frei.

Sie haben mehrere Optionen zum Erstellen oder Verwenden von Images. Die grundlegende [Anforderung](remoteapp-imagereqs.md) für ein Image ist, dass es Windows Server 2012 R2 ausführt und die Remote Desktop Session Host (RDSH)-Rolle installiert ist. Und jetzt wird es für Sie interessant.

Bei Images haben Sie die folgenden Möglichkeiten:

- Sie können ein [auf einem virtuellen Azure-Computer basierendes Image](remoteapp-image-on-azurevm.md) importieren und verwenden. Dies eignet sich gut bei Branchenanwendungen, für die benutzerdefinierte Einstellungen erforderlich sind. Sie können das Image anpassen, sodass die Anwendung funktioniert.
- Sie können [ein benutzerdefiniertes Image erstellen und hochladen](remoteapp-create-custom-image.md). Dies ist nützlich, wenn Sie bereits ein Image haben, das Sie für die lokale Remote Desktop Services-Bereitstellung verwenden.
- Sie können eines der [Vorlagenimages](remoteapp-images.md) verwenden, die in Ihrem RemoteApp-Abonnement enthalten sind. Diese Images werden vom RemoteApp-Team erstellt und verwaltet und enthalten einige Standardanwendungen (z. B. die Office-Suite), die Sie Ihren Benutzern zur Verfügung stellen können. Beachten Sie, dass nur das Office 365 Pro Plus-Image in einer Produktionsumgebung verwendet werden kann.

Unabhängig davon, wo Sie Ihr Image erhalten oder wie Sie es erstellen, sollten Sie sicherstellen, dass Sie die [Anwendungsanforderungen](remoteapp-appreqs.md) verstehen, sodass Ihre Anwendung in RemoteApp gut funktioniert. Der nächste Schritt ist die Erstellung einer [Cloudsammlung](remoteapp-create-cloud-deployment.md) oder [Hybridsammlung](remoteapp-create-hybrid-deployment.md).

<!---HONumber=AcomDC_0817_2016-->