---
title: Verwalten eines Azure Cosmos DB-Kontos über das Azure-Portal | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihr Azure Cosmos DB-Konto über das Azure-Portal verwalten. Sie erhalten eine Anleitung, wie das Azure-Portal verwendet werden kann, um Konten anzuzeigen, zu kopieren, zu löschen und um auf Konten zuzugreifen.
keywords: Azure-Portal, Azure, Microsoft Azure
services: cosmos-db
documentationcenter: ''
author: kirillg
manager: kfile
editor: cgronlun
ms.assetid: 00fc172f-f86c-44ca-8336-11998dcab45c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: kirillg
ms.openlocfilehash: 9c8126c7f902beec348419c0362722a692cb393e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-manage-an-azure-cosmos-db-account"></a>Verwalten eines Azure Cosmos DB-Kontos
Erfahren Sie, wie Sie globale Konsistenz festlegen, mit Schlüsseln arbeiten und ein Azure Cosmos DB-Konto im Azure-Portal löschen.

## <a id="consistency"></a>Verwalten von Konsistenzeinstellungen in Azure Cosmos DB
Die Auswahl der richtigen Konsistenzebene richtet sich nach der Semantik Ihrer Anwendung. Machen Sie sich unter [Einstellbare Datenkonsistenzebenen in Azure Cosmos DB][consistency] mit den Konsistenzebenen in Azure Cosmos DB vertraut. Azure Cosmos DB bietet auf jeder für Ihr Datenbankkonto verfügbaren Konsistenzebene bestimmte Garantien für Konsistenz, Verfügbarkeit und Leistung. Wenn Sie Ihr Datenbankkonto mit der Konsistenzebene „Strong“ konfigurieren, sind Ihre Daten auf eine einzige Azure-Region beschränkt und dürfen nicht global verfügbar sein. Auf den gelockerten Konsistenzebenen („Begrenzte Veraltung“, „Sitzung“ und „Letztlich“) können Sie Ihrem Datenbankkonto dagegen eine beliebige Anzahl von Azure-Regionen zuordnen. Die folgenden einfachen Schritte zeigen Ihnen, wie Sie die Standardkonsistenzebene für Ihr Datenbankkonto auswählen.

### <a name="to-specify-the-default-consistency-for-an-azure-cosmos-db-account"></a>So legen Sie die Standardkonsistenz für ein Azure Cosmos DB-Konto fest
1. Greifen Sie im [Azure-Portal](https://portal.azure.com/) auf Ihr Azure Cosmos DB-Konto zu.
2. Klicken Sie auf der Kontoseite auf **Standardkonsistenz**.
3. Wählen Sie auf der Seite **Standardkonsistenz** die neue Konsistenzebene aus, und klicken Sie auf **Speichern**.
    ![Standardkonsistenz - Sitzung][5]

## <a id="keys"></a>Anzeigen, Kopieren und erneutes Generieren von Zugriffsschlüsseln und Kennwörtern
Wenn Sie ein Azure Cosmos DB-Konto erstellen, generiert der Dienst zwei Hauptzugriffsschlüssel (oder zwei Kennwörter für MongoDB-API-Konten), die für die Authentifizierung verwendet werden können, wenn auf das Azure Cosmos DB-Konto zugegriffen wird. Durch Bereitstellen von zwei Zugriffsschlüsseln ermöglicht Azure Cosmos DB Ihnen das erneute Generieren der Schlüssel ohne Unterbrechung des Zugriffs auf das Azure Cosmos DB-Konto. 

Rufen Sie im [Azure-Portal](https://portal.azure.com/) über das Ressourcenmenü auf der Seite **Azure Cosmos DB-Konto** die Seite **Schlüssel** auf, um die für den Zugriff auf Ihr Azure Cosmos DB-Konto verwendeten Schlüssel anzuzeigen, zu kopieren und neu zu generieren. Rufen Sie für MongoDB-API-Konten die Seite **Verbindungszeichenfolge** über das Ressourcenmenü auf, um die Kennwörter für den Zugriff auf Ihr Konto anzuzeigen, zu kopieren und neu zu generieren.

![Screenshot des Azure-Portals (Seite „Schlüssel“)](./media/manage-account/keys.png)

> [!NOTE]
> Die Seite **Schlüssel** enthält auch die primären und sekundären Verbindungszeichenfolgen, mit denen über das [Datenmigrationstool](import-data.md) eine Verbindung mit Ihrem Konto hergestellt werden kann.
> 
> 

Außerdem stehen auf der Seite auch schreibgeschützte Schlüssel zur Verfügung. Lesevorgänge und Abfragen sind schreibgeschützte Vorgänge, während das Erstellen, Löschen und Ersetzen Schreib- und Lesevorgänge sind.

### <a name="copy-an-access-key-or-password-in-the-azure-portal"></a>Kopieren eines Zugriffsschlüssels oder Kennworts im Azure-Portal
Klicken Sie auf der Seite **Schlüssel** (oder der Seite **Verbindungszeichenfolge** für MongoDB-API-Konten) auf die Schaltfläche **Kopieren** rechts neben dem Schlüssel oder Kennwort, den bzw. das Sie kopieren möchten.

![Anzeigen und Kopieren eines Zugriffsschlüssels im Azure-Portal auf der Seite „Schlüssel“](./media/manage-account/copykeys.png)

### <a name="regenerate-access-keys-and-passwords"></a>Erneutes Generieren von Zugriffsschlüsseln und Kennwörtern
Sie sollten regelmäßig die Zugriffsschlüssel (und Kennwörter für MongoDB-API-Konten) für Ihr Azure Cosmos DB-Konto ändern, damit Ihre Verbindungen möglichst sicher sind. Zwei Zugriffsschlüssel/Kennwörter werden zugewiesen, damit Sie Verbindungen mit dem Azure Cosmos DB-Konto mit einem Zugriffsschlüssel aufrechterhalten können, während Sie den anderen Zugriffsschlüssel neu generieren.

> [!WARNING]
> Das erneute Generieren der Zugriffsschlüssel wirkt sich auf alle Anwendungen aus, die vom aktuellen Schlüssel abhängen. Alle Clients, die den Zugriffsschlüssel verwenden, um auf das Azure Cosmos DB-Konto zuzugreifen, müssen aktualisiert werden, um den neuen Schlüssel zu verwenden.
> 
> 

Falls Sie über Webanwendungen oder Cloud-Dienste verfügen, die das Azure Cosmos DB-Konto verwenden, verlieren Sie die Verbindungen beim erneuten Generieren von Schlüsseln – es sei denn, Sie führen einen Rollup für die Schlüssel aus. Die folgenden Schritte stellen den Prozess für den Rollup der Schlüssel/Kennwörter dar.

1. Aktualisieren Sie den Zugriffsschlüssel im Anwendungscode, damit er auf den sekundären Zugriffsschlüssel des Azure Cosmos DB-Kontos verweist.
2. Generieren Sie den primären Zugriffsschlüssel für Ihr Azure Cosmos DB-Konto neu. Greifen Sie im [Azure-Portal](https://portal.azure.com/) auf Ihr Azure Cosmos DB-Konto zu.
3. Klicken Sie auf der Seite **Azure Cosmos DB-Konto** auf **Schlüssel** (bzw. auf **Verbindungszeichenfolge** für MongoDB-Konten\*\*).
4. Klicken Sie auf der Seite **Schlüssel**/**Verbindungszeichenfolge** auf die Schaltfläche zum erneuten Generieren, und bestätigen Sie anschließend durch Klicken auf **OK**, dass Sie einen neuen Schlüssel generieren möchten.
    ![Erneutes Generieren von Zugriffsschlüsseln](./media/manage-account/regenerate-keys.png)
5. Sobald Sie sich vergewissert haben, dass der neue Schlüssel verwendet werden kann (etwa fünf Minuten nach dem erneuten Generieren), aktualisieren Sie den Zugriffsschlüssel im Anwendungscode, damit er auf den neuen primären Zugriffsschlüssel verweist.
6. Generieren Sie den sekundären Zugriffsschlüssel neu.
   
    ![Erneutes Generieren von Zugriffsschlüsseln](./media/manage-account/regenerate-secondary-key.png)

> [!NOTE]
> Die Bereitstellung eines neu generierten Schlüssels kann einige Minuten dauern, bevor Sie damit auf Ihr Azure Cosmos DB-Konto zugreifen können.
> 
> 

## <a name="get-the-connection-string"></a>Abrufen der Verbindungszeichenfolge
Führen Sie zum Abrufen der Verbindungszeichenfolge folgende Schritte aus: 

1. Greifen Sie im [Azure-Portal](https://portal.azure.com) auf Ihr Azure Cosmos DB-Konto zu.
2. Klicken Sie im Ressourcenmenü auf **Schlüssel** (bzw. auf **Verbindungszeichenfolge** für MongoDB-API-Konten).
3. Klicken Sie neben dem Feld für die **primäre Verbindungszeichenfolge** oder **sekundäre Verbindungszeichenfolge** auf die Schaltfläche **Kopieren**. 

Bei Verwendung der Verbindungszeichenfolge im [Azure Cosmos DB-Datenbank-Migrationstool](import-data.md) fügen Sie den Datenbanknamen am Ende der Verbindungszeichenfolge an. `AccountEndpoint=< >;AccountKey=< >;Database=< >`(Fixierte Verbindung) festgelegt ist(Fixierte Verbindung) festgelegt ist.

## <a id="delete"></a>: Löschen eines Azure Cosmos DB-Kontos
Wenn Sie ein nicht mehr verwendetes Azure Cosmos DB-Konto aus dem Azure-Portal entfernen möchten, klicken Sie mit der rechten Maustaste auf den Kontonamen, und klicken Sie anschließend auf **Konto löschen**.

![Löschen eines Azure Cosmos DB-Kontos im Azure-Portal](./media/manage-account/deleteaccount.png)

1. Greifen Sie im [Azure-Portal](https://portal.azure.com/) auf das zu löschende Azure Cosmos DB-Konto zu.
2. Klicken Sie auf der Seite **Azure Cosmos DB-Konto** mit der rechten Maustaste auf das Konto, und klicken Sie dann auf **Konto löschen**. 
3. Geben Sie auf der daraufhin angezeigten Bestätigungsseite den Namen des Azure Cosmos DB-Kontos ein, um zu bestätigen, dass Sie das Konto löschen möchten.
4. Klicken Sie auf die Schaltfläche **Löschen** .

![Löschen eines Azure Cosmos DB-Kontos im Azure-Portal](./media/manage-account/delete-account-confirm.png)

## <a id="next"></a>Nächste Schritte
Erfahren Sie mehr auf der Seite [Erste Schritte mit dem Azure Cosmos DB-Konto](http://go.microsoft.com/fwlink/p/?LinkId=402364).

<!--Image references-->
[5]: ./media/manage-account/documentdb_change_consistency-1.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: consistency-levels.md
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/cosmos-db/
