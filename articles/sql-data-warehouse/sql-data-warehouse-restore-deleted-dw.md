---
title: Wiederherstellen einer gelöschten Data Warehouse-Instanz
description: Anleitung zum Wiederherstellen einer gelöschten Azure SQL Data Warehouse-Instanz.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: e508eff3b322b49a6dc50d818c8bcccc3e924ff2
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759651"
---
# <a name="restore-a-deleted-azure-sql-data-warehouse"></a>Wiederherstellen einer gelöschten Azure SQL Data Warehouse-Instanz

In diesem Artikel erfahren Sie, wie Sie eine gelöschte SQL Data Warehouse-Instanz über Azure-Portal und PowerShell wiederherstellen:

## <a name="before-you-begin"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Überprüfen Sie Ihre DTU-Kapazität.** Jede SQL Data Warehouse-Instanz wird von einer SQL Server-Instanz gehostet (z.B. „myserver.database.windows.net“), die über ein Standard-DTU-Kontingent verfügt.  Vergewissern Sie sich, dass die SQL Server-Instanz über ein ausreichendes DTU-Kontingent für die Datenbankwiederherstellung verfügt. Informationen zum Berechnen des DTU-Bedarfs bzw. zur Anforderung weiterer DTUs finden Sie unter [Anfordern einer DTU-Kontingentänderung](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Wiederherstellen einer gelöschten Data Warehouse-Instanz mit PowerShell

Verwenden Sie das Cmdlet [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase), um eine gelöschte SQL Data Warehouse-Instanz wiederherzustellen. Wenn der entsprechende logische Server ebenfalls gelöscht wurde, können Sie diese Data Warehouse-Instanz nicht wiederherstellen.

1. Bevor Sie beginnen, müssen Sie [Azure PowerShell installieren](https://docs.microsoft.com/powershell/azure/overview).
2. Öffnen Sie PowerShell.
3. Stellen Sie eine Verbindung mit Ihrem Azure-Konto her, und listen Sie alle Abonnements auf, die Ihrem Konto zugeordnet sind.
4. Wählen Sie das Abonnement aus, in dem die gelöschte Data Warehouse-Instanz enthalten ist.
5. Rufen Sie die spezifische gelöschte Data Warehouse-Instanz ab.
6. Wiederherstellen der gelöschten Data Warehouse-Instanz
    1. Um die gelöschte SQL Data Warehouse-Instanz auf einem anderen logischen Server wiederherzustellen, stellen Sie sicher, dass Sie den Namen des anderen logischen Servers angeben.  Dieser logische Server kann sich auch in einer anderen Ressourcengruppe und Region befinden.
    1. Zum Wiederherstellen in einem anderen Abonnement verwenden Sie die Schaltfläche [Verschieben](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources#use-the-portal), um den logischen Server in ein anderes Abonnement zu verschieben.
1. Überprüfen Sie, ob die wiederhergestellte Data Warehouse-Instanz online ist.
1. Nach Abschluss der Wiederherstellung können Sie Ihre wiederhergestellte Data Warehouse-Instanz konfigurieren. Befolgen Sie hierzu die Anleitung [Konfigurieren der Datenbank nach der Wiederherstellung](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different logical server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>" 
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Use the following command to restore deleted data warehouse to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Wiederherstellen einer gelöschten Datenbank im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Navigieren Sie zu der SQL Server-Instanz, auf der Ihre gelöschte Data Warehouse-Instanz gehostet wurde.
3. Wählen Sie im Inhaltsverzeichnis das Symbol für **gelöschte Datenbanken** aus.

    ![Gelöschte Datenbanken](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. Wählen Sie die gelöschte SQL Data Warehouse-Instanz aus, die Sie wiederherstellen möchten.

    ![Auswahl von gelöschten Datenbanken](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. Geben Sie einen neuen **Datenbanknamen** an, und klicken Sie auf **OK**.

    ![Angeben eines Datenbanknamens](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>Nächste Schritte
- [Wiederherstellen einer vorhandenen Azure SQL Data Warehouse-Instanz](sql-data-warehouse-restore-active-paused-dw.md)
- [Geowiederherstellung einer Azure SQL Data Warehouse-Instanz](sql-data-warehouse-restore-from-geo-backup.md)