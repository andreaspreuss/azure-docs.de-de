---
title: Konfigurieren von Serverparametern – Azure-Befehlszeilenschnittstelle – Azure Database for MySQL
description: In diesem Artikel wird beschrieben, wie Sie mit der Azure CLI-Befehlszeile die Dienstparameter in Azure Database for MySQL konfigurieren.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 5f3027909d1c4684e2ef5d1b6e967cb11f570fd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062426"
---
# <a name="customize-server-parameters-by-using-azure-cli"></a>Anpassen der Serverparameter mithilfe der Azure-Befehlszeilenschnittstelle
Sie können Konfigurationsparameter für einen Azure Database for MySQL-Server mithilfe der Azure-Befehlszeilenschnittstelle (Azure CLI) auflisten, anzeigen und aktualisieren. Auf Serverebene ist eine Teilmenge der Engine-Konfigurationen verfügbar und kann geändert werden. 

## <a name="prerequisites"></a>Voraussetzungen
Zum Ausführen der Schritte in dieser Anleitung benötigen Sie Folgendes:
- [Eine Serverinstanz von Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- Das Befehlszeilenprogramm [Azure CLI](/cli/azure/install-azure-cli) oder Azure Cloud Shell im Browser

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Auflisten der Serverkonfigurationsparameter für Azure Database for MySQL
Führen Sie den Befehl [az mysql server configuration list](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-list) aus, um alle änderbaren Parameter eines Servers mit ihren Werten aufzulisten.

Sie können die Serverkonfigurationsparameter für den Server **mydemoserver.mysql.database.azure.com** in der Ressourcengruppe **myresourcegroup** auflisten.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Die Definition der einzelnen aufgeführten Parameter finden Sie in der MySQL-Referenz im Abschnitt [Server System Variables](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Anzeigen von Details zu Serverkonfigurationsparametern
Um Details zu einem bestimmten Konfigurationsparameter für einen Server anzuzeigen, führen Sie den Befehl [az mysql server configuration show](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-show) aus.

Dieses Beispiel zeigt Details des Serverkonfigurationsparameters **slow\_query\_log** für den Server **mydemoserver.mysql.database.azure.com** in der Ressourcengruppe **myresourcegroup**.
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Ändern des Werts von Serverkonfigurationsparametern
Sie können den Wert eines bestimmten Serverkonfigurationsparameters ändern und dadurch den zugrunde liegenden Konfigurationswert für die MySQL-Server-Engine aktualisieren. Um die Konfiguration zu aktualisieren, führen Sie den Befehl [az mysql server configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) aus. 

So aktualisieren Sie den Serverkonfigurationsparameter **slow\_query\_log** für den Server **mydemoserver.mysql.database.azure.com** in der Ressourcengruppe **myresourcegroup**
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Wenn Sie den Wert eines Konfigurationsparameters zurücksetzen möchten, lassen Sie den optionalen Parameter `--value` weg. Der Dienst wendet dann den Standardwert an. Im obigen Beispiel sieht dies so aus:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Dieser Code setzt die Konfiguration **slow\_query\_log** auf den Standardwert **OFF** zurück. 

## <a name="working-with-the-time-zone-parameter"></a>Arbeiten mit dem Zeitzonenparameter

### <a name="populating-the-time-zone-tables"></a>Auffüllen der Zeitzonentabellen

Die Zeitzonentabellen auf Ihrem Server können durch Aufrufen der gespeicherten Prozedur `az_load_timezone` über ein Tool wie die MySQL-Befehlszeile oder MySQL Workbench aufgefüllt werden.

> [!NOTE]
> Wenn Sie den Befehl `az_load_timezone` in MySQL Workbench ausführen, müssen Sie möglicherweise zuerst den sicheren Aktualisierungsmodus mit `SET SQL_SAFE_UPDATES=0;` deaktivieren.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Sie sollten den Server neu starten, um sicherzustellen, dass die Zeitzonentabellen ordnungsgemäß aufgefüllt werden. Um den Server neu zu starten, verwenden Sie das [Azure-Portal](howto-restart-server-portal.md) oder die [Befehlszeilenschnittstelle](howto-restart-server-cli.md).

Um die verfügbaren Zeitzonenwerte anzuzeigen, führen Sie den folgenden Befehl aus:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Festlegen der globalen Zeitzone

Die globale Zeitzone kann mithilfe des Befehls [az mysql server configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) festgelegt werden.

Der folgende Befehl ändert den Serverkonfigurationsparameter **time\_zone** für den Server **mydemoserver.mysql.database.azure.com** in der Ressourcengruppe **myresourcegroup** in **US/Pacific**.

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Festlegen der Sitzungszeitzone

Die Sitzungszeitzone kann durch Ausführen des Befehls `SET time_zone` in einem Tool wie der MySQL-Befehlszeile oder MySQL Workbench festgelegt werden. Im folgenden Beispiel wird die Zeitzone auf **US/Pacific** festgelegt.  

```sql
SET time_zone = 'US/Pacific';
```

Informationen zu [Datums- und Uhrzeitfunktionen](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz) finden Sie in der MySQL-Dokumentation.


## <a name="next-steps"></a>Nächste Schritte

- Konfigurieren von [Serverparametern im Azure-Portal](howto-server-parameters.md)