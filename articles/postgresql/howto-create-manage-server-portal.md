---
title: Verwalten von Azure Database for PostgreSQL – Azure-Portal
description: Hier erfahren Sie, wie Sie Azure Database for PostgreSQL-Server über das Azure-Portal verwalten.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: bcddd64afca29ac9fdd5d284fc8f809ff9e2477d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74534886"
---
# <a name="manage-an-azure-database-for-postgresql-server-using-the-azure-portal"></a>Verwalten eines Azure Database for PostgreSQL-Servers über das Azure-Portal
In diesem Artikel erfahren Sie, wie Sie Ihre Azure Database for PostgreSQL-Server verwalten. Zu den Verwaltungsaufgaben gehören die Compute- und Speicherskalierung, das Zurücksetzen des Administratorkennworts und das Anzeigen von Serverdetails.

## <a name="sign-in"></a>Anmelden
Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

## <a name="create-a-server"></a>Erstellen eines Servers
Wechseln Sie zum [Schnellstart](quickstart-create-server-database-portal.md), um zu erfahren, wie ein Azure Database for PostgreSQL-Server erstellt wird und wie Sie damit beginnen können.

## <a name="scale-compute-and-storage"></a>Skalieren von Compute und Speicher

Nach der Servererstellung können Sie zwischen den Tarifen „Universell“ und „Arbeitsspeicheroptimiert“ skalieren, wenn sich Ihre Anforderungen ändern. Sie können Compute und Speicher auch skalieren, indem Sie virtuelle Kerne vergrößern oder verkleinern. Der Speicher kann zentral hochskaliert werden (ein zentrales Herunterskalieren ist jedoch nicht möglich).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>Skalieren zwischen den Tarifen „Universell“ und „Arbeitsspeicheroptimiert“

Sie können von „Universell“ zu „Arbeitsspeicheroptimiert“ skalieren und umgekehrt. Ein Wechsel in den und aus dem Basic-Tarif nach der Servererstellung wird nicht unterstützt. 

1. Wählen Sie Ihren Server im Azure-Portal aus. Wählen Sie im Abschnitt **Einstellungen** die Option **Tarif** aus.

2. Wählen Sie je nach Skalierung **Universell** oder **Arbeitsspeicheroptimiert** aus. 

    ![change-pricing-tier](./media/howto-create-manage-server-portal/change-pricing-tier.png)

    > [!NOTE]
    > Das Ändern der Tarife löst einen Neustart des Servers aus.

4. Wählen Sie **OK** aus, um die Änderungen zu speichern.


### <a name="scale-vcores-up-or-down"></a>Zentrales Hoch- oder Herunterskalieren von virtuellen Kernen

1. Wählen Sie Ihren Server im Azure-Portal aus. Wählen Sie im Abschnitt **Einstellungen** die Option **Tarif** aus.

2. Ändern Sie die Einstellung **Virtuelle Kerne**, indem Sie den Schieberegler auf den gewünschten Wert ziehen.

    ![Skalieren von Computeressourcen](./media/howto-create-manage-server-portal/scaling-compute.png)

    > [!NOTE]
    > Das Skalieren der virtuellen Kerne löst einen Neustart des Servers aus.

3. Wählen Sie **OK** aus, um die Änderungen zu speichern.


### <a name="scale-storage-up"></a>Zentrales Hochskalieren des Speichers

1. Wählen Sie Ihren Server im Azure-Portal aus. Wählen Sie im Abschnitt **Einstellungen** die Option **Tarif** aus.

2. Ändern Sie die Einstellung **Speicher**, indem Sie den Schieberegler auf den gewünschten Wert ziehen.

    ![Skalieren des Speichers](./media/howto-create-manage-server-portal/scaling-storage.png)

    > [!NOTE]
    > Der Speicher kann nicht herunterskaliert werden.

3. Wählen Sie **OK** aus, um die Änderungen zu speichern.


## <a name="update-admin-password"></a>Aktualisieren des Administratorkennworts
Sie können das Kennwort für die Administratorrolle über das Azure-Portal ändern.

1. Wählen Sie Ihren Server im Azure-Portal aus. Wählen Sie im Fenster **Übersicht** die Option **Kennwort zurücksetzen** aus.

   ![Übersicht](./media/howto-create-manage-server-portal/overview-reset-password.png)

2. Geben Sie ein neues Kennwort ein, und bestätigen Sie es. Im Textfeld werden Sie nach den Anforderungen an die Kennwortkomplexität gefragt.

   ![Kennwort zurücksetzen](./media/howto-create-manage-server-portal/reset-password.png)

3. Wählen Sie **OK** aus, um das neue Kennwort zu speichern.


## <a name="delete-a-server"></a>Löschen eines Servers

Sie können Ihren Server löschen, wenn Sie ihn nicht mehr benötigen. 

1. Wählen Sie Ihren Server im Azure-Portal aus. Wählen Sie im Fenster **Übersicht** die Option **Löschen** aus.

    ![delete](./media/howto-create-manage-server-portal/overview-delete.png)

2. Geben Sie den Namen des Servers in das Eingabefeld ein, um zu bestätigen, dass dies der Server ist, den Sie löschen möchten.

    ![Löschen bestätigen](./media/howto-create-manage-server-portal/confirm-delete.png)

    > [!NOTE]
    > Das Löschen eines Servers kann nicht mehr rückgängig gemacht werden.

3. Klicken Sie auf **Löschen**.


## <a name="next-steps"></a>Nächste Schritte
- Informieren Sie sich über das [Sichern und Wiederherstellen eines Servers](howto-restore-server-portal.md).
- Informieren Sie sich über [Optimierungs- und Überwachungsoptionen in Azure Database for PostgreSQL](concepts-monitoring.md).
