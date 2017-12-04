---
title: Erstellen einer Azure Database Migration Service-Instanz mithilfe des Azure-Portals | Microsoft-Dokumentation
description: Verwenden des Azure-Portals zum Erstellen einer Instanz von Azure Database Migration Service
services: database-migration
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/17/2017
ms.openlocfilehash: 9faac0716334d627cdde4c0ef16262670333b5d4
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2017
---
# <a name="create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Erstellen einer Azure Database Migration Service-Instanz über das Azure-Portal
In diesem Schnellstart verwenden Sie das Azure-Portal, um eine Instanz von Azure Database Migration Service zu erstellen.  Nachdem Sie den Dienst erstellt haben, können Sie ihn zum Migrieren von Daten aus einer lokalen SQL Server-Instanz zu einer Azure SQL-Datenbank verwenden.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="log-in-to-the-azure-portal"></a>Anmelden beim Azure-Portal
Öffnen Sie Ihren Webbrowser, und navigieren Sie zum [Microsoft Azure-Portal](https://portal.azure.com/). Geben Sie Ihre Anmeldeinformationen ein, um sich beim Portal anzumelden. Die Standardansicht ist Ihr Dienstdashboard.

## <a name="register-the-resource-provider"></a>Registrieren des Ressourcenanbieters
Sie müssen den Microsoft.DataMigration-Ressourcenanbieter registrieren, bevor Sie Ihre erste Database Migration Service-Instanz erstellen.

1. Klicken Sie im Azure-Portal auf **Alle Dienste** und anschließend auf **Abonnements**.

1. Wählen Sie das Abonnement aus, in dem Sie die Azure Database Migration Service-Instanz erstellen möchten, und klicken Sie auf **Ressourcenanbieter**.

1. Suchen Sie nach „Migration“, und wählen Sie rechts neben „Microsoft.DataMigration“ die Option **Registrieren** aus.

![Registrieren des Ressourcenanbieters](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-azure-database-migration-service"></a>Erstellen einer Instanz von Azure Database Migration Service
1. Klicken Sie auf **+**, um einen neuen Dienst zu erstellen.  Database Migration Service ist noch in der Vorschauversion.  

1. Suchen Sie im Marketplace nach „Migration“, wählen Sie „Database Migration Service (Vorschau)“, und klicken Sie dann auf **Erstellen**.

    ![Database Migration Service erstellen](media/quickstart-create-data-migration-service-portal/dms-create-service.png)

    - Wählen Sie einen **Dienstnamen** aus, der einprägsam und in Ihrer Azure Database Migration Service-Instanz eindeutig ist.
    - Wählen Sie Ihr **Azure-Abonnement** aus, in dem die Database Migration Service-Instanz erstellt werden soll.
    - Erstellen Sie ein neues **Netzwerk** mit einem eindeutigen Namen.
    - Wählen Sie den **Ort** aus, der die geringste Entfernung zum Quell- oder Zielserver aufweist.
    - Wählen Sie als **Tarif** „Basic: 1 vCore“ aus.

1. Klicken Sie auf **Erstellen**.

Nach einigen Augenblicken wird die Azure Database Migration Service-Instanz erstellt, und sie kann verwendet werden.  Die Database Migration Service-Instanz wird wie in der Abbildung angezeigt.

![Erstellte Migration Service-Instanz](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Sie können alle im Schnellstart erstellten Ressourcen bereinigen, indem Sie die [Azure-Ressourcengruppe](../azure-resource-manager/resource-group-overview.md) löschen.  Um die Ressourcengruppe zu löschen, wechseln Sie zu der Database Migration Service-Instanz, die Sie erstellt haben, klicken Sie auf den Namen der **Ressourcengruppe**, und wählen Sie dann **Ressourcengruppe löschen** aus.  Durch diese Aktion werden alle Ressourcen in der Ressourcengruppe sowie die Gruppe selbst gelöscht.

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Migrate SQL Server to Azure SQL Database](tutorial-sql-server-to-azure-sql.md) (Migrieren von SQL Server zu Azure SQL-Datenbank, in englischer Sprache)