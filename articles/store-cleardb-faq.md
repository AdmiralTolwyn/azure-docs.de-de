<properties
	pageTitle="Häufig gestellte Fragen zur ClearDB MySql-Datenbanken mit Azure App Service | Microsoft Azure"
	description="Antworten auf häufig gestellte Fragen zur Verwendung von ClearDB MySQL-Datenbanken mit Azure App Service."
	documentationCenter="php"
	services=""
	authors="sunbuild"
	manager="yochayk"
	editor=""
	tags="mysql"/>

<tags
	ms.service="multiple"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/08/2016"
	ms.author="sumuth"/>

# Häufig gestellte Fragen zur Verwendung von ClearDB MySql-Datenbanken mit Azure App Service

In diesem Dokument werden häufige gestellte Fragen zu Verwendung und Erwerb von ClearDB MySQL-Datenbanken für Azure-Web-Apps beantwortet.

## Welche Optionen habe ich für MySQL in Azure?

Sie haben mehrere Möglichkeiten:

* [Freigegebene ClearDB MySQL-Datenbank](/marketplace/partners/cleardb/databases/)

* [ClearDB Premium-MySQL-Cluster](/marketplace/partners/cleardb-clusters/cluster/)

* [In einer Azure-VM ausgeführter MySQL-Cluster](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)

* [In einer Azure-VM ausgeführte MySQL-Einzelinstanz](virtual-machines/virtual-machines-windows-classic-mysql-2008r2.md)

ClearDB ist ein MySQL-Hostingdienst für die Verwaltung Ihrer MySQL-Infrastruktur. Wenn Sie Ihren MySQL-Cluster oder Ihre Datenbank auf einem virtuellen Azure-Computer ausführen, müssen Sie den MySQL-Server einrichten und mit Patches auf dem neuesten Stand halten.

## Benötige ich eine Kreditkarte für die Web-App und MySQL-Vorlage in Azure Marketplace?

Dies hängt vom Typ Ihres Abonnements ab. Hier einige gängige Abonnementtypen:

* [Nutzungsbasierte Bezahlung](/offers/ms-azr-0003p/): Erfordert eine Kreditkarte. Wenn Sie eine kostenpflichtige MySQL-Datenbank erwerben, wird die Kreditkarte belastet.

* [Kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial/): Bietet Guthaben für die Nutzung mit Microsoft Azure-Diensten, lässt aber nicht den Erwerb von Ressourcen von Drittanbietern zu. Zum Erwerben von Diensten von Drittanbietern oder einer kostenpflichtigen MySQL-Datenbank benötigen Sie ein kreditkartengestütztes Abonnement. Für Web-Apps können Sie eine ClearDB MySQL-Datenbank im Tarif FREE erstellen.

* [MSDN-Abonnement](/pricing/member-offers/msdn-benefits/) und **MSDN Dev/Test Pay-As-You-Go**: Wie die kostenlose Testversion erfordert ein MSDN-Abonnement, dass Sie über eine Kreditkarte verfügen, um eine kostenpflichtige MySQL-Lösung von ClearDB zu erwerben.

* [Enterprise Agreement (EA)](/pricing/enterprise-agreement/): EA-Kunden erhalten im Rahmen ihres Vertrags vierteljährlich eine Sammelrechnung für alle ihre Käufe (bei Drittanbietern) in Azure Marketplace. Die Rechnungsstellung für alle Marketplace-Einkäufe erfolgt außerhalb der Zahlungsverpflichtung. Beachten Sie, dass der Azure Store nicht für Kunden verfügbar ist, die in Aserbaidschan, Kroatien, Norwegen und Puerto Rico registriert sind.

*   [DreamSpark](https://www.dreamspark.com/Product/Product.aspx?productid=99): Sie können für Web-Apps nur ClearDB-Datenbanken im Tarif FREE erstellen. Es gibt keine Beschränkung der Anzahl von ClearDB MySQL-Datenbanken im Tarif FREE, die Sie erstellen können. Beachten Sie, dass Datenbanken im Tarif FREE nicht für Produktions-Web-Apps verwendet werden dürfen, da dieser Dienst nur für Tests vorgesehen ist.

## Warum wurden mir 3,50 $ für eine Web-App und MySQL-Vorlage aus Azure Marketplace in Rechnung gestellt?

Die Standardoption Datenbanken ist Titan zum Preis von 3,50 $. Während der Datenbankerstellung zeigen wir die Preise nicht an, und Sie haben ggf. versehentlich eine Datenbank erworben, die Sie nicht wollten. Wir suchen derzeit nach einer Verbesserungsmöglichkeit. Bis dahin müssen Sie alle für die Web-App und Datenbank ausgewählten Tarife prüfen, ehe Sie auf **Erstellen** klicken und mit der Bereitstellung der Ressourcen beginnen.

## Ich führe MySQL auf meiner eigenen Azure-VM aus. Kann ich meine Azure-Web-App mit meiner Datenbank verbinden?

Ja. Solange Ihrer Azure-VM Remotezugriff auf Ihre Web-App gewährt wird, können Sie Ihre Web-App mit Ihrer Datenbank verbinden. Weitere Informationen finden Sie unter [Installieren von MySQL auf einem virtuellen Computer](virtual-machines/virtual-machines-windows-classic-mysql-2008r2.md).

## In welchen Ländern werden ClearDB Premium MySQL-Cluster unterstützt?

[ClearDB Premium MySQL-Cluster](/marketplace/partners/cleardb-clusters/cluster/) sind in allen Azure-Regionen weltweit mit Ausnahme von Indien, Australien, Brasilien, Süden und China verfügbar.

## Kann ich vor dem Erstellen einer Datenbank mit einer ClearDB Premium-Clusterlösung einen neuen Cluster erstellen?

Nein, das Erstellen leerer ClearDB-Cluster wird nicht unterstützt. Im Azure-Portal können Sie Datenbanken in einem Cluster erstellen, wobei ggf. gleichzeitig ein neuer Cluster erstellt wird.

## Werde ich gewarnt, falls ich versuche, eine ClearDB MySQL-Datenbank zu löschen, die von einer meiner Anwendungen verwendet wird?

Nein, Azure warnt Sie nicht, falls Sie einen Marketplace-Einkauf löschen, von dem Ihre Anwendung abhängt.

## In welchen Regionen können ClearDB-Datenbanken erstellt werden?

Azure Marketplace ist nicht für Kunden verfügbar, die in Aserbaidschan, Kroatien, Norwegen und Puerto Rico registriert sind. ClearDB ist in diesen Regionen nicht verfügbar.

## Welche Tarif sollte ich für eine Produktions-Web-App und Datenbank wählen?

Wählen Sie für Web-Apps mindestens den Tarif Basic. Für ClearDB empfehlen wir den Tarif Saturn oder Jupiter. Machen Sie sich mit den Features und Grenzen der einzelnen Tarife sowohl für [Web-Apps](/pricing/details/app-service/) als auch für [ClearDB MySQL-Datenbanken](/marketplace/partners/cleardb/databases/) vertraut, um den für Sie am besten geeigneten Tarif zu ermitteln.

## Wie kann ich meinen ClearDB-Datenbanktarif ändern?

Nutzen Sie hierfür den [ClearDB-Upgrade-Assistenten](https://www.cleardb.com/store/azure/upgrade). Derzeit bieten wir im Azure-Portal keine Upgrademöglichkeit.

## Warum ist meine ClearDB-Datenbank im Azure-Portal nicht sichtbar?

Wenn die ClearDB-Datenbank mithilfe von Azure Resource Manager oder über das [neue Azure-Portal](https://portal.azure.com) erstellt wird, ist sie im [alten Azure-Portal](https://manage.windowsazure.com) nicht sichtbar. Dies können Sie umgehen, indem Sie die Datenbank manuell mit der Web-App verknüpfen. Wenn Sie die ClearDB-Datenbank im [alten Portal](https://manage.windowsazure.com) erstellen, können Sie sie im [neuen Azure-Portal](https://portal.azure.com) auch nicht sehen. Hierfür ist jedoch keine Problemumgehung möglich.

## Wen bitte ich um Unterstützung, wenn meine Datenbank ausfällt?

Wenden Sie sich bei sämtlichen Datenbankproblemen an den [ClearDB-Support](https://www.cleardb.com/developers/help/support). Stellen Sie bei Aufforderung Ihre Azure-Abonnementdaten bereit.

## Kann ich für meine ClearDB MySQL-Datenbank-Clusterlösung zusätzliche Benutzer erstellen?  

Nein. Zusätzliche Benutzer können nicht erstellt werden. Sie können jedoch zusätzliche Datenbanken in Ihrem ClearDB-Datenbankcluster erstellen.

## Können Datenbanken der Basic- oder Pro-Serie direkt im ClearDB-Portal aktualisiert werden, ähnlich wie es mit Planetary-Plänen bereits möglich ist?

Ja, Datenbanken der Basic-Serie können direkt aktualisiert werden (Basic 60 bis Basic 500). Datenbanken der Pro-Serie können mit Ausnahme von Pro 60 direkt aktualisiert werden (Pro 125 bis Pro 1000). Ein Upgrade von Pro 60-Datenbanken wird derzeit nicht unterstützt.

## Wird meine ClearDB MySQL-Datenbank ebenfalls migriert, wenn ich meine Ressourcen von einem Abonnement zu einem anderen migriere?  

Bei der Ressourcenmigration zwischen Abonnements gelten einige [Einschränkungen](./app-service-web/app-service-move-resources.md). Eine ClearDB MySQL-Datenbank ist ein Drittanbieterdienst und wird daher nicht migriert, wenn Sie eine Migration zwischen Azure-Abonnements durchführen. Ihre ClearDB MySQL-Datenbank wird möglicherweise deaktiviert, wenn Sie die Migration der MySQL-Datenbank nicht vor der Migration der Azure-Ressourcen planen und durchführen. Migrieren Sie zunächst manuell Ihre Datenbanken, und führen Sie dann die Azure-Abonnementmigration für Ihre Web-App durch.

## Kann ich Scalable WordPress mit einem EA-Abonnement (Enterprise Agreement) erwerben?

Der Prozess ist bei allen Abonnements gleich. Wechseln Sie im [Azure-Portal](https://portal.azure.com/) zum Azure Marketplace, und wählen Sie [Scalable WordPress](https://portal.azure.com/?feature.customportal=false#create/WordPress.ScalableWordPress) aus, um mit dem Erstellen der App zu beginnen. Scalable WordPress unterstützt nur die ClearDB-Tarife „Saturn“ und „Jupiter“. Ihr EA-Guthaben wird sowohl für Ihre Webanwendung, die im Web-Apps-Tarif „Standard“ ausgeführt wird, als auch für die kostenpflichtige (freigegebene) ClearDB MySQL-Datenbank in Anspruch genommen. [/marketplace/faq/](/marketplace/faq/). Ihre gesamten Einkäufe im Store werden einmal pro Quartal in einer separaten Rechnung zusammengefasst und über Ihr EA abgerechnet.

## Kann ich eine ClearDB-Datenbank aus einem kreditkartengestützten Abonnement in ein EA-Abonnement übertragen?

Für vorhandene ClearDB-Datenbanken wird die Kreditkarte genutzt, die den bestehenden Abonnements zugeordnet ist. Wenn Sie ein EA-Abonnement verwenden möchten, müssen Sie Ihre Daten in eine neue Datenbank migrieren:

* Erwerben Sie in Ihrem EA-Abonnement eine neue ClearDB-Datenbank.
* Migrieren Sie Ihre Daten in die neue Datenbank.
* Ändern Sie Ihre Anwendung so, dass die neue Datenbank verwendet wird.
* Löschen Sie Ihre alte ClearDB-Datenbank.

Beim Erstellen einer neuen Web-App mit MySQL (ClearDB) oder einer neuen MySQL-Datenbank (ClearDB) bestimmt das gewählte Abonnement, wie Sie für den Dienst zahlen. Beachten Sie, dass wir bei einem EA-Abonnement den Erwerb von Diensten von Drittanbietern wie ClearDB im Azure-Portal nicht blockieren. EA-Abonnements werden außerhalb der Zahlungsverpflichtung abgerechnet, wobei die Rechnungsstellung vierteljährlich und nachträglich erfolgt. EA-Kunden müssen eine Zahlungsmethode, z. B. Kreditkarte, für alle im Marketplace erworbenen Dienste von Drittanbietern festlegen.

## Wo kann ich die Kosten für ClearDB-Ressourcen in einem EA-Abonnement finden?

Direkten EA-Kunden werden die Azure Marketplace-Gebühren im Enterprise Portal angezeigt. Beachten Sie, dass alle Marketplace-Käufe und deren Nutzung außerhalb der Zahlungsverpflichtung abgerechnet werden, wobei die Rechnungsstellung vierteljährlich und nachträglich erfolgt. EA-Kunden müssen Dienste von Drittanbietern direkt zahlen, was möglich ist, indem sie für ihr EA-Konto eine Zahlungsmethode wie eine Kreditkarte einrichten.

Indirekte EA-Kunden finden ihre Azure Marketplace-Abonnements im Enterprise Portal auf der Seite **Abonnements verwalten**, wobei die Preise jedoch ausgeblendet sind. Kunden sollten ihren Lizenzierungsdienstanbieter zwecks Informationen zu Marketplace-Gebühren kontaktieren.

Der Zugriff auf Azure Marketplace für Dienste von Drittanbietern kann von Ihren EA-Administratoren für die Azure-Registrierung verwaltet werden. Diese können im Enterprise Portal im Abschnitt „Konten“ unter „Konten und Abonnements verwalten“ den Kauf bei Drittanbietern im Azure Store deaktivieren bzw. reaktivieren.

## An wen wende ich mich bei Fragen zu meiner Rechnung für ClearDB-Dienste in meinem EA-Abonnement?

Wenden Sie sich an den [Support für EA-Kunden](http://aka.ms/AzureEntSupport), wenn Sie Fragen zur Abrechnung über Ihre EA-Registrierung haben. Der Supportteam des EA-Portals beantwortet Ihre Fragen und hilft beim Lösen Ihres Problems.

 



## Weitere Informationen

[Azure Marketplace – häufig gestellte Fragen](/marketplace/faq/)

<!---HONumber=AcomDC_0330_2016-->