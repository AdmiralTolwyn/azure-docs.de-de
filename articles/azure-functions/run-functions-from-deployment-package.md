---
title: Ausführen von Azure Functions aus einem Paket
description: Lassen Sie die Azure Functions-Laufzeit Ihre Funktionen ausführen, indem Sie eine Bereitstellungspaketdatei einbinden, die Ihre Projektdateien für die Funktions-App enthält.
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: 3ae287939f22469b03f0e10f184f067274464905
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087024"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Ausführen Ihrer Azure Functions aus einem Paket

In Azure können Sie Ihre Functions direkt aus der Bereitstellungspaketdatei in Ihrer Funktions-App ausführen. Die andere Option ist die Bereitstellung Ihrer Dateien im Verzeichnis `d:\home\site\wwwroot` Ihrer Funktions-App.

Dieser Artikel beschreibt die Vorteile einer Ausführung Ihrer Functions aus einem Paket. Außerdem wir erläutert, wie Sie diese Funktionalität in Ihrer Funktions-App aktivieren.

> [!IMPORTANT]
> Wenn Sie Ihre Funktionen in einer Linux-Funktions-App in einem [Premium-Tarif](functions-scale.md#premium-plan) bereitstellen, sollte die Ausführung immer über die Paketdatei erfolgen, und Sie sollten [Ihre App unter Verwendung von Azure Functions Core Tools veröffentlichen](functions-run-local.md#project-file-deployment).

## <a name="benefits-of-running-from-a-package-file"></a>Vorteile der Ausführung aus einer Paketdatei
  
Es gibt mehrere Vorteile bei der Ausführung aus einer Paketdatei:

+ Reduziert das Risiko von Sperrungen beim Kopieren von Dateien.
+ Kann in einer Produktions-App (mit Neustart) bereitgestellt werden.
+ Sie können sich sicher sein, dass die Dateien, die in Ihrer App ausgeführt werden, sicher sind.
+ Verbessert die Leistung von [Azure Resource Manager-Bereitstellungen](functions-infrastructure-as-code.md).
+ Kann Kaltstartzeiten verringern, insbesondere für JavaScript-Funktionen mit großen npm-Paketstrukturen.

Weitere Informationen dazu finden Sie in [dieser Ankündigung](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Aktivieren von Funktionen zum Ausführen aus einem Paket

Damit Ihre Funktions-App aus einem Paket ausgeführt werden kann, müssen Sie nur eine Einstellung `WEBSITE_RUN_FROM_PACKAGE` zu Ihren Einstellungen in der Funktions-App hinzufügen. Die Einstellung `WEBSITE_RUN_FROM_PACKAGE` kann einen der folgenden Werte aufweisen:

| Wert  | BESCHREIBUNG  |
|---------|---------|
| **`1`**  | Für unter Windows ausgeführte Funktions-Apps empfohlen. Ausführen aus einer Paketdatei im Ordner `d:\home\data\SitePackages` der Funktions-App. Wenn Sie nicht [mit ZIP Deploy bereitstellen](#integration-with-zip-deployment), muss der Ordner für diese Option außerdem eine Datei namens `packagename.txt` enthalten. Diese Datei enthält nur den Namen der Paketdatei im Ordner ohne Leerzeichen. |
|**`<URL>`**  | Speicherort der spezifischen Paketdatei, die Sie ausführen möchten. Wenn Sie Blob Storage müssen Sie einen privaten Container mit einer [Shared Access Signature (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) verwenden, um die Functions-Laufzeit für den Zugriff auf das Paket zu aktivieren. Sie können den [Azure Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) zum Hochladen von Dateien in Ihr Blob Storage-Konto verwenden. Wenn Sie eine URL angeben, müssen Sie nach dem Veröffentlichen eines aktualisierten Pakets auch [Trigger synchronisieren](functions-deployment-technologies.md#trigger-syncing). |

> [!CAUTION]
> Bei der Ausführung einer Funktions-App unter Windows ergibt die Option mit externer URL ein schlechteres Startverhalten. Beim Bereitstellen Ihrer Funktions-App unter Windows sollten Sie `WEBSITE_RUN_FROM_PACKAGE` auf `1` festlegen und per ZIP-Bereitstellung veröffentlichen.

Das folgende Beispiel zeigt eine Funktions-App, die so konfiguriert ist, dass Sie über eine ZIP-Datei ausgeführt wird, die im Azure Blob Storage gehostet wird:

![WEBSITE_RUN_FROM_ZIP-Appeinstellung](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> Derzeit werden nur ZIP-Paketdateien unterstützt.

## <a name="integration-with-zip-deployment"></a>Integration mit einer ZIP-Bereitstellung

[ZIP-Bereitstellung][Zip deployment for Azure Functions] ist eine Funktion von Azure App Service, mit der Sie Ihr Funktions-App-Projekt im Verzeichnis `wwwroot` bereitstellen können. Das Projekt ist als ZIP-Bereitstellungsdatei verpackt. Mit derselben API können Sie Ihr Paket im Ordner `d:\home\data\SitePackages` bereitstellen. Mit dem `WEBSITE_RUN_FROM_PACKAGE`-App-Einstellungswert von `1` kopieren die ZIP-Bereitstellungs-APIs Ihr Paket in den Ordner `d:\home\data\SitePackages`, anstatt die Dateien nach `d:\home\site\wwwroot` zu extrahieren. Außerdem wird die Datei `packagename.txt` erstellt. Nach einem Neustart wird das Paket in `wwwroot` als schreibgeschütztes Dateisystem bereitgestellt. Weitere Informationen zur ZIP-Bereitstellung finden Sie unter [ZIP-Bereitstellung für Azure Functions](deployment-zip-push.md).

## <a name="adding-the-website_run_from_package-setting"></a>Hinzufügen der Einstellung WEBSITE_RUN_FROM_PACKAGE

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

### <a name="use-key-vault-references"></a>Verwenden von Key Vault-Verweisen

Zur Erhöhung der Sicherheit können Sie Key Vault-Verweise im Zusammenhang mit Ihrer externen URL verwenden. Hierdurch bleibt die ruhende URL verschlüsselt und ermöglicht die Nutzung von Key Vault für die Verwaltung und Rotation von Geheimnissen. Es wird empfohlen, Azure Blob Storage zu verwenden, damit Sie den zugeordneten SAS-Schlüssel problemlos rotieren können. Azure Blob Storage wird ruhend verschlüsselt, wodurch Ihre Anwendungsdaten geschützt bleiben, wenn sie nicht in App Service bereitgestellt werden.

1. Erstellen Sie eine Azure Key Vault-Instanz.

    ```azurecli
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus
    ```

1. Fügen Sie Ihre externe URL in Key Vault als Geheimnis hinzu.

    ```azurecli
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<insert-your-URL>"
    ```

1. Erstellen Sie die App-Einstellung `WEBSITE_RUN_FROM_PACKAGE`, und legen Sie den Wert als Key Vault-Verweis auf die externe URL fest.

    ```azurecli
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"
    ```

Weitere Informationen finden Sie in den folgenden Artikeln.

- [Key Vault-Verweise für App Service](../app-service/app-service-key-vault-references.md)
- [Azure Storage encryption for data at rest (Azure Storage-Verschlüsselung für ruhende Daten)](../storage/common/storage-service-encryption.md)

## <a name="troubleshooting"></a>Problembehandlung

- Durch Ausführen aus Paket wird `wwwroot` schreibgeschützt, sodass Sie einen Fehler erhalten, wenn Sie Dateien in dieses Verzeichnis schreiben möchten.
- Das TAR- und das GZIP-Format werden nicht unterstützt.
- Diese Funktionalität funktioniert nicht mit lokalem Cache.
- Um die Kaltstartleistung zu verbessern, verwenden Sie die lokale ZIP-Option (`WEBSITE_RUN_FROM_PACKAGE`=1).
- Die Option „Aus Paket ausführen“ ist nicht mit der Anpassungsoption für die Bereitstellung (`SCM_DO_BUILD_DURING_DEPLOYMENT=true`) kompatibel. Der Erstellungsschritt wird während der Bereitstellung ignoriert.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Kontinuierliche Bereitstellung für Azure Functions](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
