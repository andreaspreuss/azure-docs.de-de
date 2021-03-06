---
title: PowerShell-Beispiel – Aktualisieren des Schemas für die SQL-Datensynchronisierung
description: Azure PowerShell-Beispielskript zum Aktualisieren des Synchronisierungsschemas für die SQL-Datensynchronisierung
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 0106b80259083c6e5e3e527063a18aae2e7c6cee
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74421604"
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>Verwenden von PowerShell zum Aktualisieren des Synchronisierungsschemas in einer bestehenden Synchronisierungsgruppe

In diesem PowerShell-Beispiel wird das Synchronisierungsschema in einer vorhandenen Synchronisierungsgruppe der SQL-Datensynchronisierung aktualisiert. Wenn Sie mehrere Tabellen synchronisieren, hilft Ihnen dieses Skript beim effizienten Aktualisieren des Synchronisierungsschemas. Dieses Beispiel veranschaulicht die Verwendung des Skripts **UpdateSyncSchema**, das auf GitHub als [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1) verfügbar ist.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und nutzen möchten, müssen Sie für dieses Tutorial mindestens Version 1.4.0 von Azure PowerShell verwenden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

Eine Übersicht über die SQL-Datensynchronisierung finden Sie unter [Synchronisieren von Daten über mehrere Cloud- und lokale Datenbanken mit SQL-Datensynchronisierung](../sql-database-sync-data.md).

> [!IMPORTANT]
> Die Azure SQL-Datensynchronisierung unterstützt derzeit keine verwalteten Azure SQL-Datenbank-Instanzen.

## <a name="examples"></a>Beispiele

### <a name="add-all-tables-to-the-sync-schema"></a>Hinzufügen aller Tabellen zum Synchronisierungsschema

Im folgenden Beispiel wird das Datenbankschema aktualisiert, und dem Synchronisierungsschema werden alle gültigen Tabellen in der Hub-Datenbank hinzugefügt.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="add-and-remove-tables-and-columns"></a>Hinzufügen und Entfernen von Tabellen und Spalten

Im folgenden Beispiel werden dem Synchronisierungsschema `[dbo].[Table1]` und `[dbo].[Table2].[Column1]` hinzugefügt und `[dbo].[Table3]` entfernt.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>Skriptparameter

Das Skript **UpdateSyncSchema** weist die folgenden Parameter auf:

| Parameter | Notizen |
|---|---|
| $subscriptionId | Das Abonnement, in dem die Synchronisierungsgruppe erstellt wird |
| $resourceGroupName | Die Ressourcengruppe, in der die Synchronisierungsgruppe erstellt wird|
| $serverName | Der Servername der Hub-Datenbank|
| $databaseName | Der Name der Hub-Datenbank |
| $syncGroupName | Der Name der Synchronisierungsgruppe |
| $memberName | Geben Sie den Namen des Elements an, wenn Sie das Datenbankschema aus dem Synchronisierungselement und nicht aus der Hub-Datenbank laden möchten. Wenn Sie das Datenbankschema vom Hub laden möchten, lassen Sie diesen Parameter leer. |
| $timeoutInSeconds | Das Timeout, nach dem das Skript das Datenbankschema aktualisiert. Der Standardwert ist 900 Sekunden. |
| $refreshDatabaseSchema | Geben Sie an, ob das Skript das Datenbankschema aktualisieren muss. Wenn das Datenbankschema gegenüber der früheren Konfiguration geändert wurde (z.B. wenn Sie eine neue Tabelle oder Spalte hinzugefügt haben), müssen Sie das Schema aktualisieren, bevor Sie es neu konfigurieren. Der Standardwert ist "false". |
| $addAllTables | Wenn dieser Wert TRUE ist, werden dem Synchronisierungsschema alle gültigen Tabellen und Spalten hinzugefügt. Die Werte von $TablesAndColumnsToAdd und $TablesAndColumnsToRemove werden ignoriert. |
| $tablesAndColumnsToAdd | Geben Sie Tabellen oder Spalten an, die dem Synchronisierungsschema hinzugefügt werden sollen. Jeder Name einer Tabelle oder Spalte muss vollständig vom Schemanamen getrennt werden. Beispiel: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Bei Angabe von mehreren Tabellen- oder Spaltennamen können diese durch Kommas (,) voneinander getrennt werden. |
| $tablesAndColumnsToRemove | Geben Sie Tabellen oder Spalten an, die aus dem Synchronisierungsschema entfernt werden sollen. Jeder Name einer Tabelle oder Spalte muss vollständig vom Schemanamen getrennt werden. Beispiel: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Bei Angabe von mehreren Tabellen- oder Spaltennamen können diese durch Kommas (,) voneinander getrennt werden. |

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript **UpdateSyncSchema** verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [Get-AzSqlSyncGroup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncgroup) | Gibt Informationen zu einer Synchronisierungsgruppe zurück |
| [Update-AzSqlSyncGroup](https://docs.microsoft.com/powershell/module/az.sql/update-azsqlsyncgroup) | Aktualisiert eine Synchronisierungsgruppe |
| [Get-AzSqlSyncMember](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncmember) | Gibt Informationen zu einem Synchronisierungselement zurück |
| [Get-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncschema) | Gibt Informationen zu einem Synchronisierungsschema zurück |
| [Update-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/update-azsqlsyncschema) | Aktualisiert ein Synchronisierungsschema |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/overview).

Zusätzliche PowerShell-Skriptbeispiele für SQL-Datenbank finden Sie unter [Azure PowerShell-Beispiele für Azure SQL-Datenbank](../sql-database-powershell-samples.md).

Weitere Informationen zur SQL-Datensynchronisierung finden Sie unter:

- Übersicht: [Synchronisieren von Daten über mehrere Cloud- und lokale Datenbanken mit SQL-Datensynchronisierung](../sql-database-sync-data.md)
- Einrichten der Datensynchronisierung
    - Im Portal: [Tutorial: Einrichten der SQL-Datensynchronisierung zum Synchronisieren von Daten zwischen Azure SQL-Datenbank und lokalem SQL Server](../sql-database-get-started-sql-data-sync.md)
    - Mit PowerShell
        - [Verwenden von PowerShell zum Synchronisieren zwischen mehreren Azure SQL-Datenbanken](sql-database-sync-data-between-sql-databases.md)
        - [Verwenden von PowerShell zum Synchronisieren zwischen einer Azure SQL-Datenbank und einer lokalen SQL Server-Datenbank](sql-database-sync-data-between-azure-onprem.md)
- Datensynchronisierungs-Agent: [Datensynchronisierungs-Agent für die Azure SQL-Datensynchronisierung](../sql-database-data-sync-agent.md)
- Bewährte Methoden: [Bewährte Methoden für die Azure SQL-Datensynchronisierung](../sql-database-best-practices-data-sync.md)
- Überwachung: [Überwachen der SQL-Datensynchronisierung mit Azure Monitor-Protokollen](../sql-database-sync-monitor-oms.md)
- Problembehandlung: [Behandeln von Problemen mit der Azure SQL-Datensynchronisierung](../sql-database-troubleshoot-data-sync.md)
- Aktualisieren des Synchronisierungsschemas
    - Mit Transact-SQL: [Automatisieren der Replikation von Schemaänderungen in der Azure SQL-Datensynchronisierung](../sql-database-update-sync-schema.md)

Weitere Informationen zu SQL-Datenbank finden Sie unter:

- [Übersicht über die SQL-Datenbank](../sql-database-technical-overview.md)
- [Datenbank-Lebenszyklusverwaltung](https://msdn.microsoft.com/library/jj907294.aspx)
