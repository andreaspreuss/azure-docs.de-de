---
title: Migrieren von Servern und VMs zu Azure mit Azure Site Recovery
description: In diesem Artikel wird erläutert, wie Sie lokale Computer und virtuelle Azure-IaaS-Computer mithilfe des Azure Site Recovery-Diensts migrieren.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: raynew
ms.openlocfilehash: 8e256aac16bb8c2d2f1eca494981458f71cc2e4d
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73620621"
---
# <a name="about-migration"></a>Informationen zur Migration

In diesem Artikel finden Sie einen schnellen Überblick darüber, wie der Dienst [Azure Site Recovery](site-recovery-overview.md) Ihnen bei der Migration von Computern hilft. 

Mit Site Recovery können Sie wie folgt migrieren:

- **Migrieren vom lokalen Standort in Azure**: Migrieren Sie lokale Hyper-V-VMs, VMware-VMs und physische Server in Azure. Nach der Migration werden Workloads, die auf den lokalen Computern ausgeführt werden, auf Azure-VMs ausgeführt. 
- **Migrieren in Azure**: Migrieren von Azure-VMs zwischen Azure-Regionen. 
- **Migrieren von AWS**: Migrieren Sie AWS Windows-Instanzen zu Azure IaaS-VMs. 

> [!NOTE]
> Sie können jetzt mit dem Azure Migrate-Dienst von einem lokalen Standort zu Azure migrieren. [Weitere Informationen](../migrate/migrate-overview.md)

## <a name="what-do-we-mean-by-migration"></a>Was bedeutet „Migration“?

Zusätzlich zur Verwendung von Site Recovery für die Notfallwiederherstellung von lokalen und Azure-VMs können Sie den Site Recovery-Dienst nutzen, um diese zu migrieren. Wo liegt der Unterschied?

- Für die Notfallwiederherstellung werden Computer in regelmäßigen Abständen in Azure repliziert. Wenn es zu einem Ausfall kommt, führen Sie für die Computer ein Failover vom primären Standort an einen sekundären Azure-Standort durch und greifen dort darauf zu. Wenn der primäre Standort wieder verfügbar ist, erfolgt das Failback aus Azure.
- Bei der Migration replizieren Sie lokale Computer in Azure oder Azure-VMs in eine sekundäre Region. Dann führen Sie für die VM ein Failover vom primären an den sekundären Standort aus und schließen den Migrationsprozess ab. Hierbei erfolgt kein Failback.  


## <a name="migration-scenarios"></a>Migrationsszenarien

**Szenario** | **Details**
--- | ---
**Migrieren vom lokalen Standort in Azure** | Sie können lokale VMware-VMs, Hyper-V-VMs und physische Server in Azure migrieren. Hierzu führen Sie fast die gleichen Schritte wie bei einer vollständigen Notfallwiederherstellung durch. Es erfolgt jedoch kein Failback der Computer aus Azure an den lokalen Standort.
**Zwischen Azure-Regionen migrieren** | Sie können Azure-VMs aus einer Azure-Region in eine andere migrieren. Nach Abschluss der Migration können Sie die Notfallwiederherstellung für die Azure-VMs jetzt in der sekundären Region konfigurieren, in die Sie migriert wurden.
**Migrieren von AWS zu Azure** | Sie können AWS-Instanzen zu Azure-VMs migrieren. Für Migrationszwecke behandelt Site Recovery AWS-Instanzen als physische Server. 

## <a name="next-steps"></a>Nächste Schritte

- [Migrieren von lokalen Computern zu Azure](migrate-tutorial-on-premises-azure.md)
- [Migrieren von virtuellen Azure-IaaS-Computern zwischen Azure-Regionen mit Azure Site Recovery](azure-to-azure-tutorial-migrate.md)
- [Migrieren von AWS zu Azure](migrate-tutorial-aws-azure.md)
