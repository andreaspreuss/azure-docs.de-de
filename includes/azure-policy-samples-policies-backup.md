---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/12/2020
ms.author: dacoulte
ms.openlocfilehash: 4a6c0cad4a93d0d99573a348070823b909c75c7e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79299412"
---
|Name |BESCHREIBUNG |Auswirkungen |Version |GitHub |
|---|---|---|---|---|
|[Azure Backup muss für Virtual Machines aktiviert sein.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Mit dieser Richtlinie können Sie überwachen, ob der Azure Backup-Dienst für alle virtuellen Computer aktiviert ist. Azure Backup ist eine kostengünstige Sicherungslösung per Mausklick, die die Datenwiederherstellung vereinfacht und leichter zu aktivieren ist als andere Cloudsicherungsdienste. |AuditIfNotExists, Disabled |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json)
|[Sicherung von VMs eines Standorts in einem vorhandenen zentralen Tresor am selben Standort konfigurieren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Diese Richtlinie konfiguriert den Azure Backup-Schutz für virtuelle Computer an einem bestimmten Standort für einen vorhandenen zentralen Tresor am gleichen Standort. Sie gilt nur für virtuelle Computer, die noch nicht für die Sicherung konfiguriert sind. Es wird empfohlen, diese Richtlinie nicht mehr als 200 virtuellen Computern gleichzeitig zuzuweisen. Wenn die Richtlinie mehr als 200 virtuellen Computern zugewiesen wird, kann dies dazu führen, dass die Sicherung einige Stunden später als geplant ausgelöst wird. Diese Richtlinie wird verbessert, um weitere VM-Images zu unterstützen. |deployIfNotExists, auditIfNotExists, disabled |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json)
