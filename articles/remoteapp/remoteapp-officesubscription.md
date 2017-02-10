---
title: Verwenden Ihres Office 365-Abonnements mit Azure RemoteApp | Microsoft Docs
description: "Erfahren Sie, wie Sie über Ihr Office 365-Abonnement in Azure RemoteApp Office-Apps freigeben können."
services: remoteapp
documentationcenter: 
author: piotrci
manager: mbaldwin
ms.assetid: f82b6e23-2b71-47be-846d-fd93f2652f3c
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: e4d94d3f9736378d93e93be6645ed04ade763ca3
ms.openlocfilehash: 4ad7970b94949bf105ddf549279d4d1bcbad5f40


---
# <a name="how-to-use-your-office-365-subscription-with-azure-remoteapp"></a>Verwenden Ihres Office 365-Abonnements mit Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp wird eingestellt. Details finden Sie in der [Ankündigung](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Wussten Sie, dass Sie über Ihr vorhandenes Office 365-Abonnement in Azure RemoteApp Office-Apps aus der Cloud freigeben können? Hier erhalten Sie Informationen über die Optionen von Office 365 und Azure RemoteApp, einschließlich Links zu Artikeln zu Office 365, mit denen Sie Ihr Abonnement in vollem Umfang nutzen können.

## <a name="how-do-i-use-office-365-accounts-for-azure-remoteapp"></a>Wie verwende ich Office 365-Konten für Azure RemoteApp?
Sie finden alle Informationen in Peters neuem Artikel: [Verwenden von Azure RemoteApp mit Office 365-Benutzerkonten](remoteapp-o365user.md)

## <a name="can-i-use-my-office-365-subscription-to-run-office-applications-in-azure-remoteapp"></a>Kann ich mein Office 365-Abonnement für die Ausführung von Office-Anwendungen in Azure RemoteApp verwenden?
Ja. Tatsächlich können Sie ausschließlich mit Ihrem Office 365-Abonnement Ihre Office-Anwendungen in Azure RemoteApp bereitstellen.

(Hinweis: Wenn Ihre Azure RemoteApp-Bereitstellung von einem Hostingpartner geliefert wird, kann Ihnen dieser möglicherweise Office-Lizenzen auf der Grundlage eines [Dienstanbieterlizenzvertrags](http://www.microsoft.com/en-us/Licensing/licensing-programs/spla-program.aspx) anbieten.)

Der große Vorteil des Office 365-Abonnements liegt darin, dass Sie die gleiche Benutzerlizenz in vielen verschiedenen Plattformen und Umgebungen nutzen können, darunter auch für die Azure-Cloud. Bei Verwendung von Office-Anwendungen in Azure RemoteApp müssen Sie weder zusätzliche Lizenzen erwerben noch Ihre vorhandenen Lizenzen auf besondere Weise konfigurieren. Sie benötigen lediglich ein Office 365-Abonnement, das [Office 365 ProPlus](https://technet.microsoft.com/library/Gg702619.aspx)umfasst.

Office 365 ProPlus ermöglicht die [Aktivierung gemeinsam genutzter Computer](https://technet.microsoft.com/library/Dn782860.aspx). Diese Funktion ermöglicht die temporäre benutzerbezogene Aktivierung für Office in virtuellen und Cloudumgebungen wie Azure RemoteApp (und Remotedesktopdienste).

Welche Office 365-Pläne umfassen Office 365 ProPlus? Informationen dazu finden Sie in der Tabelle [Dienstverfügbarkeit in einem Office&365;-Plan](https://technet.microsoft.com/library/office-365-plan-options.aspx). Beachten Sie, dass nicht alle Pläne Office 365 ProPlus umfassen (z. B. der Office 365-Business-Plan). Wenn dies der Fall für Ihren Plan ist, empfiehlt sich ein Upgrade auf einen anderen Plan (z. B. Office 365 Education E3).

## <a name="ok-so-how-are-my-office-365-proplus-licenses-used-with-azure-remoteapp"></a>Wie werden meine Office 365 ProPlus-Lizenzen nun mit Azure RemoteApp verwendet?
Mit jeder Benutzerlizenz für Office 365 ProPlus kann ein Benutzer Office-Anwendungen auf bis zu 5 Computern plus Tablets und Smartphones aktivieren. Jede Aktivierung ist für den Benutzer registriert, bis er Office auf dem entsprechenden Gerät deaktiviert. (Benutzer können ihre Geräte im [Office 365-Portal](https://portal.office365.com/)verwalten.)

Mit Azure RemoteApp kann sich ein einzelner Benutzer am selben Tag bei mehreren Computern anmelden, ohne es zu bemerken. Dies liegt daran, dass der Dienst Ressourcen in der Cloud automatisch verwaltet und skaliert, während der Benutzer nur Apps und Programme wahrnimmt, die Sie freigegeben haben. Für dieses Szenario bietet Office 365 ProPlus einen gemeinsam genutzten Computeraktivierungsmodus. Das bedeutet, dass dieser Benutzer keine Lizenzverwaltung benötigt, um auf diese Ressourcen zuzugreifen, und die einzelnen Computer nicht auf das Aktivierungslimit von 5 Computern angerechnet werden.

Solange Sie (als Administrator) Ihren Benutzern Office 365 ProPlus-Lizenzen zuweisen, können diese Office auf ihren persönlichen Geräten sowie über Ihre Azure RemoteApp-Sammlung verwenden.

## <a name="which-office-applications-can-i-use-with-office-365-and-azure-remoteapp"></a>Welche Office-Anwendungen kann ich mit Office 365 und Azure RemoteApp verwenden?
Sie können Ihr Office 365-Abonnement verwenden, um Office 2013 in Azure RemoteApp-Bereitstellungen zu aktivieren und freizugeben. Die Verwendung andere Office-Versionen mit Azure RemoteApp wird zurzeit nicht unterstützt. Dies umfasst Office 2003, Office 2007, Office 2010 und Office 2016.

## <a name="what-about-visio-pro-or-project-pro"></a>Wie sieht es mit Visio Pro oder Project Pro aus?
Das in Ihrem RemoteApp-Abonnement enthaltene Office 365 ProPlus-Image enthält Visio Pro sowie Project Pro. Allerdings können Sie Ihr Office 365 ProPlus-Abonnement nicht zum Aktivieren dieser Programme verwenden, da sie jeweils über eine eigene Lizenz verfügen. Sie können sie im [Office 365-Portal](https://portal.office365.com/)aktivieren. 

Sie müssen diese Programme nicht lizenzieren, wenn Sie sie nicht verwenden möchten. Aktivieren Sie einfach nur die Programme, die Sie verwenden möchten – und ignorieren Sie die anderen. Sie sehen sie weiterhin im Image, verwenden sie aber nicht. 

## <a name="how-do-i-get-started-with-office-365-and-azure-remoteapp"></a>Wie steige ich in Office 365 und Azure RemoteApp ein?
Jetzt kennen Sie die Details der Office 365-Lizenzierung, und wir können zur Verwendung in Azure RemoteApp übergehen – Ihr Einstieg gestaltet sich sehr einfach:

Verwenden Sie zum Erstellen Ihrer Azure RemoteApp-Sammlung das Vorlagenimage **Office 365 ProPlus (Subscription required)** .

![Azure RemoteApp-Image mit Office 365 ProPlus](./media/remoteapp-officesubscription/remoteapp-officeimage.png)

Dieses Vorlagenimage enthält die neueste Version von Windows Server und Office 365 ProPlus. Nachdem Sie Ihre Sammlung konfiguriert haben (einschließlich des Veröffentlichens von Apps), melden sich Ihre Benutzer einfach bei Azure RemoteApp an (über den RemoteApp-Client) und geben ihre Office 365-Anmeldeinformationen an, um Office-Apps abzurufen. Lizenzen werden automatisch übermittelt, ohne dass jegliche Konfiguration oder Verwaltung erforderlich ist.

## <a name="can-i-create-a-custom-image-with-office-365-proplus"></a>Kann ich ein benutzerdefiniertes Image mit Office 365 ProPlus erstellen?
Sie können ein benutzerdefiniertes Image für Ihre Sammlung erstellen, das Office 365 ProPlus enthält. Sie haben zwei Auswahlmöglichkeiten: Sie können das von uns bereitgestellte Azure-Katalogimage verwenden, oder Sie können Ihr eigenes benutzerdefiniertes Image erstellen und darin Office 365 ProPlus installieren.

### <a name="use-the-azure-gallery-image"></a>Verwenden des Azure-Katalogimages
Die einfachste Möglichkeit zum Bereitstellen von Office 365 ProPlus in einer Sammlung besteht darin, [mit einem der Azure-Katalogimages zu beginnen](remoteapp-image-on-azurevm.md) , die in Ihrem Azure RemoteApp-Abonnement enthalten sind. Stellen Sie sicher, dass Sie das Image **Windows Server Remote Desktop Session Host with Office 365 ProPlus pre-installed** auswählen. Installieren Sie dann alle anderen gewünschten Apps in diesem Image, und schon sind Sie startbereit.

### <a name="use-a-custom-image"></a>Verwenden eines benutzerdefinierten Images
Sie können jederzeit ein benutzerdefiniertes Image erstellen, indem Sie einen [virtuellen Azure-Computer](remoteapp-image-on-azurevm.md) erstellen oder das [Image lokal erstellen](remoteapp-create-custom-image.md) und in Azure hochladen. Stellen Sie in beiden Fällen sicher, dass Sie Office 365 ProPlus mit dem Knoten für die Aktivierung gemeinsam genutzter Computer installieren. Verwenden Sie das [Office-Bereitstellungstool](http://blogs.technet.com/b/odsupport/archive/2014/07/11/using-the-office-deployment-tool.aspx), und befolgen Sie die [Anweisungen](https://technet.microsoft.com/library/Dn782858.aspx) für die Installation.  

### <a name="disable-automatic-updates-for-office-365-proplus-in-your-custom-image---important"></a>Deaktivieren von automatischen Updates für Office 365 ProPlus in Ihrem benutzerdefinierten Image – WICHTIG
Ihr benutzerdefiniertes Image dient in Azure RemoteApp als Vorlage zum Hinzufügen zusätzlicher Ressourcen, wenn die Nachfrage Ihrer Benutzer steigt. Um Verzögerungen und Verbindungsprobleme zu verhindern, deaktivieren Sie automatische Updates für Office in Ihrem Image. Andernfalls werden alle mit dieser Vorlage erstellten Ressourcen beim Start jeweils automatisch aktualisiert. Verwenden Sie stattdessen den Azure RemoteApp-Standardprozess zum Aktualisieren Ihres benutzerdefiniertes Images. Auf diese Weise aktualisieren Sie die Office-Anwendungen einmal im Vorlagenimage, und Azure RemoteApp übermittelt die Updates dann an Ihre Benutzer.

Um automatische Updates zu deaktivieren, fügen Sie folgenden Code in die Konfigurationsdatei des Office-Bereitstellungstools ein:

        <Updates Enabled="FALSE" />

Die Konfigurationsdatei sollte nun diese Zeilen enthalten:

        <Display Level="NONE" AcceptEULA="TRUE" />
        <Property Name="SharedComputerLicensing" Value="1" />
        <Updates Enabled="FALSE" />

## <a name="so-how-can-i-update-an-image-with-office-365-proplus"></a>Wie kann ich ein Image mit Office 365 ProPlus aktualisieren?
Es gibt viele Gründe, das Image in Ihrer Sammlung zu aktualisieren. Hier seien nur einige genannt:

* Abrufen der neuesten Windows-Updates 
* Abrufen der neuesten Office 365 ProPlus-Anwendungsupdates
* Aktualisieren Ihrer benutzerdefinierten Anwendung
* Ändern anderer Konfigurationseinstellungen für das Image

Die End-to-End-Schritte zum Aktualisieren Ihrer Sammlung für die Verwendung des aktualisierten Images finden Sie [hier](remoteapp-update.md). Im Folgenden erhalten Sie dagegen Informationen zum Aktualisieren des Images und von Office 365 ProPlus.

Sie haben zwei Möglichkeiten zum Aktualisieren Ihres Images: Ersetzen Sie das Image durch ein vollständig neues Image, oder führen Sie eine manuelle Aktualisierung Ihres vorhandenen Images durch.

### <a name="replace-your-image-with-the-latest-azure-gallery-image--add-customizations"></a>Ersetzen Ihres Images durch das neueste Azure-Katalogimage und Hinzufügen von Anpassungen
Bei dieser Option übernimmt Microsoft die Steuerung der Updates für Windows Server und Office 365 ProPlus für Sie. Anstelle der Aktualisierung Ihres vorhandenen Images erstellen Sie ein vollständig neues Image auf Grundlage des neuesten Katalogimages. Wiederholen Sie dann alle Schritte, die Sie zuvor zum Anpassen des Images ausgeführt haben – Installieren benutzerdefinierter Apps, Ändern der Imagekonfiguration usw.

Die Katalogimages werden regelmäßig aktualisiert, und Sie können sich in der Sicherheit wiegen, dass Ihre Apps für Windows Server und Office 365 ProPlus auf dem neuesten Stand sind. Der Nachteil dabei ist natürlich, dass Sie Ihre Anpassungen mit jedem neuen Image erneut vornehmen müssen. Um dem entgegenzuwirken, können Sie Skripts zum Automatisieren Ihrer Anpassungen erstellen.

### <a name="manually-update-your-existing-image"></a>Manuelle Aktualisierung Ihres vorhandenen Images
Bei dieser Option verwenden Sie Windows-Standardtools, um Updates auf das Image anzuwenden. Für Office 365 ProPlus können Sie mit dem Office-Bereitstellungstool die neuesten Updates oder Versionen von Office 365 ProPlus herunterladen und installieren.

> [!IMPORTANT]
> Und denken Sie daran: Deaktivieren Sie die automatischen Updates für Office 365 ProPlus.
> 
> 

Sie benötigen weitere Informationen zur Verwendung des Office-Bereitstellungstools für Updates?

* [Bereitstellen von Produkten vom Typ "Klick-und-Los für Office 365" mithilfe des Office-Bereitstellungstools](https://technet.microsoft.com/library/JJ219423.aspx)
* [Deploying and Updating Office 365 ProPlus Using the Office Deployment Tool](https://channel9.msdn.com/Events/Ignite/2015/BRK3168) (Video, in englischer Sprache)
* [Konfigurieren von Updateeinstellungen für Office 365 ProPlus](https://technet.microsoft.com/library/dn761708.aspx)




<!--HONumber=Dec16_HO2-->


