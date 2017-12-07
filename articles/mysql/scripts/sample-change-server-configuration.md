---
title: "Azure CLI-Skript – Ändern von Serverkonfigurationen | Microsoft-Dokumentation"
description: "Dieses CLI-Beispielskript listet alle verfügbaren Serverkonfigurationen auf und aktualisiert den Wert von „innodb_lock_wait_timeout“."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 11/03/2017
ms.openlocfilehash: 9a94f257e5cd3534127e8594ddee3c5f837876df
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2017
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mysql-server-using-azure-cli"></a>Auflisten und Aktualisieren eines Servers für Azure-Datenbank für MySQL mithilfe der Azure CLI
Mit diesem CLI-Beispielskript werden alle verfügbaren Konfigurationsparameter sowie ihre zulässigen Werte für den Server für Azure-Datenbank für MySQL aufgeführt. Zudem wird für *innodb_lock_wait_timeout* ein anderer Wert als der Standardwert festgelegt.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für diesen Artikel die Azure CLI-Version 2.0 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu finden. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Installieren von Azure CLI 2.0]( /cli/azure/install-azure-cli) Informationen dazu. 

## <a name="sample-script"></a>Beispielskript
Ändern Sie in diesem Beispielskript die hervorgehobenen Zeilen, um den Benutzernamen und das Kennwort des Administrators anzupassen.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung
Nach Ausführung des Skriptbeispiels können mit dem folgenden Befehl die Ressourcengruppe und alle damit verbundenen Ressourcen entfernt werden.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Erläuterung des Skripts
Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| **Befehl** | **Hinweise** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az mysql server create](/cli/azure/mysql/server#az_msql_server_create) | Erstellt einen MySQL-Server, der die Datenbanken hostet. |
| [az mysql server configuration list](/cli/azure/mysql/server/configuration#az_msql_server_configuration_list) | Listet die Konfigurationen eines Servers mit Azure-Datenbank für MySQL auf. |
| [az mysql server configuration set](/cli/azure/mysql/server/configuration#az_msql_server_configuration_set) | Aktualisiert die Konfiguration eines Servers mit Azure-Datenbank für MySQL. |
| [az mysql server configuration show](/cli/azure/mysql/server/configuration#az_msql_server_configuration_show) | Zeigt die Konfiguration eines Servers mit Azure-Datenbank für MySQL an. |
| [az group delete](/cli/azure/group#az_group_delete) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte
- Lesen Sie weitere Informationen zur Azure CLI: [Azure CLI-Dokumentation](/cli/azure/overview).
- Probieren Sie weitere Skripts aus: [Azure CLI-Beispiele für Azure-Datenbank für MySQL](../sample-scripts-azure-cli.md)
- Weitere Informationen zu Serverparametern finden Sie unter [Konfigurieren Sie Serverparameter in Azure-Datenbank für MySQL mit dem Azure-Portal](../howto-server-parameters.md).
