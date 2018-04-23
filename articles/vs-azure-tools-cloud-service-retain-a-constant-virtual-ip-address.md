---
title: Beibehalten einer konstanten virtuellen IP-Adresse für einen Azure-Clouddienst | Microsoft Docs
description: Erfahren Sie, wie Sie sicherstellen können, dass die virtuelle IP-Adresse (VIP) Ihres Azure-Clouddiensts beibehalten wird.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 4a58e2c6-7a79-4051-8a2c-99182ff8b881
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: c02e80a97fe5943019f446299e370bf029a01ff4
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2018
---
# <a name="retain-a-constant-virtual-ip-address-for-an-azure-cloud-service"></a>Beibehalten einer konstanten virtuellen IP-Adresse für einen Azure-Clouddienst
Wenn Sie einen in Azure gehosteten Clouddienst aktualisieren, müssen Sie möglicherweise sicherstellen, dass die virtuelle IP-Adresse (VIP) des Diensts unverändert bleibt. Viele Domänenverwaltungsdienste nutzen DNS (Domain Name System) für die Registrierung der Domänennamen. DNS funktioniert nur, wenn die VIP unverändert bleibt. Mithilfe des **Veröffentlichungs-Assistenten** in Azure Tools können Sie sicherstellen, dass die VIP Ihres Clouddiensts beibehalten wird, wenn Sie den Dienst aktualisieren. Weitere Informationen zur Verwendung der DNS-Domänenverwaltung für Clouddienste finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für einen Azure-Clouddienst](cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="publish-a-cloud-service-without-changing-its-vip"></a>Veröffentlichen eines Clouddiensts, ohne die VIP zu ändern
Die VIP eines Clouddiensts wird zugeordnet, wenn der Clouddienst erstmalig in einer bestimmten Umgebung in Azure bereitgestellt wird (z.B. in der Produktionsumgebung). Die VIP ändert sich nur, wenn Sie die Bereitstellung explizit löschen oder wenn diese durch den Vorgang der Bereitstellungsaktualisierung implizit gelöscht wird. Wenn Sie die VIP beibehalten möchten, dürfen Sie die Bereitstellung nicht löschen. Außerdem müssen Sie sicherstellen, dass Visual Studio die Bereitstellung nicht automatisch löscht. 

Sie können im **Veröffentlichungs-Assistenten** die entsprechenden Bereitstellungseinstellungen festlegen. Dieser Assistent unterstützt verschiedene Bereitstellungsoptionen. Sie können eine neue Bereitstellung oder eine Updatebereitstellung angeben, wobei die Aktualisierung inkrementell oder gleichzeitig erfolgen kann. Bei beiden Arten von Updatebereitstellungen wird die VIP beibehalten. Eine Definition dieser Bereitstellungstypen finden Sie unter [Assistent zur Veröffentlichung einer Azure-Anwendung](vs-azure-tools-publish-azure-application-wizard.md). Darüber hinaus können Sie festlegen, ob die vorherige Bereitstellung eines Clouddiensts gelöscht wird, wenn ein Fehler auftritt. Wenn Sie diese Option nicht korrekt festlegen, wird die VIP möglicherweise unerwartet geändert.

## <a name="update-a-cloud-service-without-changing-its-vip"></a>Aktualisieren eines Clouddiensts, ohne die VIP zu ändern
1. Erstellen oder öffnen Sie ein Azure-Clouddienstprojekt in Visual Studio. 

2. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt. Wählen Sie im Kontextmenü **Veröffentlichen** aus.

    ![Veröffentlichungsmenü](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/solution-explorer-publish-menu.png)

3. Wählen Sie im Dialogfeld **Azure-Anwendung veröffentlichen** das Azure-Abonnement für die Bereitstellung aus. Melden Sie sich bei Bedarf an, und wählen Sie **Weiter** aus.

    ![Azure-Anwendung veröffentlichen – Seite „Anmelden“](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-signin.png)

4. Überprüfen Sie auf der Registerkarte **Allgemeine Einstellungen**, ob der Name des Clouddiensts, für den die Bereitstellung durchgeführt wird, sowie die Angaben für **Umgebung**, **Buildkonfiguration** und **Dienstkonfiguration** richtig sind.

    ![Azure-Anwendung veröffentlichen – Registerkarte „Allgemeine Einstellungen“](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-common-settings.png)

5. Vergewissern Sie sich auf der Registerkarte **Erweiterte Einstellungen**, dass die Angaben für **Bereitstellungsbezeichnung** und **Speicherkonto** korrekt sind. Stellen Sie sicher, dass das Kontrollkästchen **Bereitstellung bei Fehler löschen** deaktiviert und das Kontrollkästchen **Bereitstellungsaktualisierung** aktiviert ist. Durch Deaktivierung von **Bereitstellung bei Fehler löschen** stellen Sie sicher, dass die VIP beibehalten wird, wenn während der Bereitstellung ein Fehler auftritt. Durch Aktivierung des Kontrollkästchens **Bereitstellungsaktualisierung** wird sichergestellt, dass die Bereitstellung nicht gelöscht und die VIP beibehalten wird, wenn Sie die Anwendung erneut veröffentlichen. 

    ![Azure-Anwendung veröffentlichen – Registerkarte „Erweiterte Einstellungen“](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-advanced-settings.png)

6. Um festzulegen, wie die Rollen aktualisiert werden sollen, klicken Sie neben **Bereitstellungsaktualisierung** auf **Einstellungen**. Wählen Sie entweder **Inkrementelle Aktualisierung** oder **Gleichzeitige Aktualisierung** und anschließend **OK** aus. Aktivieren Sie **Inkrementelle Aktualisierung**, um die Instanzen der Anwendung nacheinander zu aktualisieren. Die Verfügbarkeit der Anwendung wird somit nicht unterbrochen. Aktivieren Sie **Gleichzeitige Aktualisierung**, um alle Instanzen der Anwendung gleichzeitig zu aktualisieren. Gleichzeitige Updates sind schneller, der Dienst ist während der Aktualisierung jedoch möglicherweise nicht verfügbar. Wenn Sie fertig sind, klicken Sie auf **Weiter**.

    ![Azure-Anwendung veröffentlichen – Seite „Bereitstellungseinstellungen“](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-deployment-update-settings.png)

7. Wählen Sie im Dialogfeld **Azure-Anwendung veröffentlichen** so oft die Option **Weiter** aus, bis die Seite **Zusammenfassung** angezeigt wird. Überprüfen Sie die Einstellungen, und wählen Sie dann **Veröffentlichen** aus.
   
    ![Azure-Anwendung veröffentlichen – Seite „Zusammenfassung“](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-summary.png)

## <a name="next-steps"></a>Nächste Schritte
- [Verwenden des Visual Studio-Assistenten zur Veröffentlichung einer Azure-Anwendung](vs-azure-tools-publish-azure-application-wizard.md)

