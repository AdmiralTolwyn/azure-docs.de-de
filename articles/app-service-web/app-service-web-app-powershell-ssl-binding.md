---
title: SSL-Zertifikate-Bindung mithilfe von PowerShell
description: Erfahren Sie mehr darüber, wie SSL-Zertifikate an Ihre Web-App mithilfe von PowerShell gebunden werden können.
services: app-service\web
documentationcenter: ''
author: ahmedelnably
manager: stefsch
editor: ''

ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2016
ms.author: ahmedelnably

---
# Azure App Service SSL-Zertifikatbindung mit PowerShell
Mit der Veröffentlichung von Microsoft Azure PowerShell Version 1.1.0 wurde ein neues Cmdlet hinzugefügt, das dem Benutzer ermöglicht, vorhandene oder neue SSL-Zertifikate an eine vorhandene Web-App zu binden.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

Weitere Informationen zur Verwendung Azure Resource Manager-basierter Azure-PowerShell-Cmdlets zum Verwalten Ihrer Web-Apps finden Sie unter [Azure Resource Manager-basierte PowerShell-Befehle zum Verwalten von Azure-Web-Apps](app-service-web-app-azure-resource-manager-powershell.md).

## Hochladen und Binden eines neuen SSL-Zertifikats
Das Szenario: Der Benutzer möchte ein SSL-Zertifikat an eine seiner Web-Apps binden.

Mit dem Namen der Ressourcengruppe, die die Web-App enthält, dem Namen der Web-App, dem Zertifikat des PFX-Dateipfads auf dem Computer des Benutzers, dem Kennwort für das Zertifikat und dem benutzerdefinierten Hostnamen können wir den folgenden PowerShell-Befehl verwenden, um die SSL-Bindung zu erstellen:

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -CertificateFilePath PathToPfxFile -CertificatePassword PlainTextPwd -Name www.contoso.com

Beachten Sie Folgendes: Bevor Sie eine SSL-Bindung zu einer Web-App hinzufügen können, muss bereits ein Hostname (benutzerdefinierte Domäne) konfiguriert sein. Wenn kein Hostname konfiguriert ist, erhalten Sie beim Ausführen von New-AzureRmWebAppSSLBinding einen Fehler, dass der Hostname nicht existiert. Sie können einen Hostnamen direkt über das Portal oder mithilfe von Azure PowerShell hinzufügen. Mit folgendem PowerShell-Codeausschnitt können Sie den Hostnamen konfigurieren, bevor Sie New-AzureRmWebAppSSLBinding ausführen.

    $webApp = Get-AzureRmWebApp -Name mytestapp -ResourceGroupName myresourcegroup  
    $hostNames = $webApp.HostNames  
    $HostNames.Add("www.contoso.com")  
    Set-AzureRmWebApp -Name mytestapp -ResourceGroupName myresourcegroup -HostNames $HostNames   

Wichtiger Hinweis: Das Cmdlet Set-AzureRmWebApp überschreibt die Hostnamen für die Web-App. Daher fügt der oben stehende PowerShell-Codeausschnitt die Hostnamen an die vorhandene Liste der Hostnamen für die Web-App an.

## Hochladen und Binden eines bereits existierenden SSL-Zertifikats
Das Szenario: Der Benutzer möchte ein zuvor hochgeladenes SSL-Zertifikat an eine seiner Web-Apps binden.

Wir können die Liste der Zertifikate, die schon in eine bestimmte Ressourcengruppe hochgeladen wurden, erhalten, indem wir folgenden Befehl abrufen:

    Get-AzureRmWebAppCertificate -ResourceGroupName myresourcegroup

Beachten Sie, dass die Zertifikate lokal für einen bestimmten Speicherort und eine Ressourcengruppe gelten. Der Benutzer muss das Zertifikat erneut hochladen, wenn sich die konfigurierte Web-App an einem anderen Speicherort und in einer anderen Ressourcengruppe befindet, als das erforderliche Zertifikat

Mit dem Namen der Ressourcengruppe, die die Web-App enthält, dem Namen der Web-App, dem Zertifikatfingerabdruck und dem benutzerdefinierten Hostnamen können wir den folgenden PowerShell-Befehl verwenden, um die SSL-Bindung zu erstellen:

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Thumbprint <certificate thumbprint> -Name www.contoso.com

## Löschen einer vorhandenen SSL-Bindung
Das Szenario: Der Benutzer möchte eine vorhandene SSL-Bindung löschen.

Mit dem Namen der Ressourcengruppe, die die Web-App enthält, dem Namen der Web-App und dem benutzerdefinierten Hostnamen können wir den folgenden PowerShell-Befehl verwenden, um die SSL-Bindung zu entfernen:

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com

Beachten Sie, dass das Zertifikat standardmäßig gelöscht wird, wenn die entfernte SSL-Bindung die letzte Bindung war, die dieses Zertifikat an diesem Speicherort verwendet hat. Falls der Benutzer das Zertifikat behalten möchte, kann er die DeleteCertificate-Option anpassen, um das Zertifikat zu behalten.

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com -DeleteCertificate $false

### Referenzen
* [Azure Resource Manager-basierte PowerShell-Befehle zum Verwalten von Azure-Web-Apps](app-service-web-app-azure-resource-manager-powershell.md)
* [Einführung in die App Service-Umgebung](app-service-app-service-environment-intro.md)
* [Verwenden von Windows PowerShell mit dem Azure-Ressourcen-Manager](../powershell-azure-resource-manager.md)

<!---HONumber=AcomDC_0601_2016-->