---
title: Verknüpfen eines Azure-Kontos mit einer Partner-ID
description: Verfolgen Sie Bindungen mit Azure-Kunden nach, indem Sie eine Partner-ID mit dem Benutzerkonto verknüpfen, mit dem Sie die Ressourcen des Kunden verwalten.
author: dhirajgandhi
ms.reviewer: dhgandhi
ms.author: banders
ms.date: 02/13/2020
ms.service: cost-management-billing
ms.topic: conceptual
ms.openlocfilehash: c3f5dd4d1b67f5b1079a56d5c3428d286fd9d77f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79204799"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Verknüpfen einer Partner-ID mit Ihren Azure-Konten

Microsoft-Partner stellen Dienste bereit, die Kunden beim Erreichen ihrer Geschäftsziele und der Ziele beim Einsatz von Microsoft-Produkten unterstützen. Wenn die Partner Azure-Dienste im Auftrag des Kunden verwalten, konfigurieren und unterstützen, benötigen sie als Benutzer Zugriff auf die Umgebung des Kunden. Mit dem Partnerzuordnungstyp „Partner Admin Link“ (PAL) können Partner Ihre Partnernetzwerk-ID mit den für die Bereitstellung von Diensten verwendeten Anmeldeinformationen verknüpfen.

Mithilfe von PAL kann Microsoft Partner ermitteln und würdigen, die zum Erfolg von Azure-Kunden beitragen. Die Zuordnung von Einfluss und Umsatz Ihrer Organisation basiert dabei auf den Berechtigungen des Kontos (RBAC-Rolle) sowie auf dem Umfang (Abonnement, Ressourcengruppe, Ressource).

## <a name="get-access-from-your-customer"></a>Erlangen des Zugriffs seitens Ihres Kunden

Bevor Sie Ihre Partner-ID verknüpfen, muss Ihr Kunde Ihnen über eine der folgenden Optionen Zugriff auf ihre Azure-Ressourcen erteilen:

- **Gastbenutzer**: Ihr Kunde kann Sie als Gastbenutzer hinzufügen und Ihnen eine beliebige rollenbasierte Zugriffssteuerungsrolle (Role-Based Access Control, RBAC) zuweisen. Weitere Informationen finden Sie unter [Hinzufügen von Gastbenutzern aus einem anderen Verzeichnis](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Verzeichniskonto**: Ihr Kunde kann ein Benutzerkonto für die Sie in seinem eigenen Verzeichnis erstellen und dem Konto eine beliebige RBAC-Rolle zuweisen.

- **Dienstprinzipal**: Ihr Kunde kann eine App oder ein Skript von Ihrer Organisation in seinem Verzeichnis hinzufügen und dieser oder diesem eine beliebige RBAC-Rolle zuweisen. Die Identität der App oder des Skripts wird als Dienstprinzipal bezeichnet.

- **Azure Lighthouse**: Ihr Kunde kann ein Abonnement (oder eine Ressourcengruppe) delegieren, sodass Ihre Benutzer innerhalb Ihres Mandanten daran arbeiten können. Weitere Informationen finden Sie unter [Delegierte Azure-Ressourcenverwaltung](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management).

## <a name="link-to-a-partner-id"></a>Verknüpfen einer Partner-ID

Wenn Sie Zugriff auf die Ressourcen des Kunden haben, können Sie Ihre Microsoft Partner Network-ID (MPN-ID) im Azure-Portal, mit PowerShell oder mithilfe der Azure-Befehlszeilenschnittstelle (Azure CLI) mit Ihrer Benutzer-ID oder Ihrem Dienstprinzipal verknüpfen. Verknüpfen Sie die Partner-ID für jeden Kundenmandanten.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Verknüpfen einer neuen Partner-ID im Azure-Portal

1. Wechseln Sie im Azure-Portal zu [Mit einer Partner-ID verknüpfen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade).

2. Melden Sie sich beim Azure-Portal an.

3. Geben Sie die Microsoft-Partner-ID ein. Die Partner-ID ist die [Microsoft Partner Network](https://partner.microsoft.com/)-ID Ihrer Organisation.

   ![Screenshot von „Mit einer Partner-ID verknüpfen“](./media/link-partner-id/link-partner-id01.png)

4. Wenn Sie die Partner-ID für einen anderen Kunden verknüpfen möchten, wechseln Sie das Verzeichnis. Wählen Sie unter **Verzeichnis wechseln** das gewünschte Verzeichnis aus.

   ![Screenshot der Option „Verzeichnis wechseln“](./media/link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Verknüpfen einer neuen Partner-ID mit PowerShell

1. Installieren Sie das PowerShell-Modul [Az.ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/).

2. Melden Sie sich entweder mit dem Benutzerkonto oder mit dem Dienstprinzipal beim Mandanten des Kunden an. Weitere Informationen finden Sie unter [Anmelden mit PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. Verknüpfen Sie die neue Partner-ID. Die Partner-ID ist die [Microsoft Partner Network](https://partner.microsoft.com/)-ID Ihrer Organisation.

    ```azurepowershell-interactive
    C:\> new-AzManagementPartner -PartnerId 12345
    ```

#### <a name="get-the-linked-partner-id"></a>Abrufen der verknüpften Partner-ID
```azurepowershell-interactive
C:\> get-AzManagementPartner
```

#### <a name="update-the-linked-partner-id"></a>Aktualisieren der verknüpften Partner-ID
```azurepowershell-interactive
C:\> Update-AzManagementPartner -PartnerId 12345
```
#### <a name="delete-the-linked-partner-id"></a>Löschen der verknüpften Partner-ID
```azurepowershell-interactive
C:\> remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Verknüpfen einer neuen Partner-ID mit der Azure-Befehlszeilenschnittstelle
1. Installieren Sie die Erweiterung für die Azure-Befehlszeilenschnittstelle (Azure CLI).

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ```

2. Melden Sie sich entweder mit dem Benutzerkonto oder mit dem Dienstprinzipal beim Mandanten des Kunden an. Weitere Informationen finden Sie unter [Anmelden mit der Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ```

3. Verknüpfen Sie die neue Partner-ID. Die Partner-ID ist die [Microsoft Partner Network](https://partner.microsoft.com/)-ID Ihrer Organisation.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Abrufen der verknüpften Partner-ID
```azurecli-interactive
C:\ az managementpartner show
```

#### <a name="update-the-linked-partner-id"></a>Aktualisieren der verknüpften Partner-ID
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
```

#### <a name="delete-the-linked-partner-id"></a>Löschen der verknüpften Partner-ID
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
```

## <a name="next-steps"></a>Nächste Schritte

Schließen Sie sich der Diskussion in der [Microsoft-Partner-Community](https://aka.ms/PALdiscussion) an, um Aktualisierungen zu erhalten oder Feedback zu senden.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

**Wer kann die Partner-ID verknüpfen?**

Jeder Benutzer der Partnerorganisation, der Azure-Ressourcen des Kunden verwaltet, kann die Partner-ID mit dem Konto verknüpfen.

**Kann eine Partner-ID geändert werden, nachdem sie verknüpft wurde?**

Ja. Eine verknüpfte Partner-ID kann geändert, hinzugefügt oder entfernt werden.

**Was geschieht, wenn ein Benutzer ein Konto bei mehreren Kundenmandanten besitzt?**

Die Verknüpfung zwischen der Partner-ID und dem Konto erfolgt für jeden Kundenmandanten. Verknüpfen Sie die Partner-ID für jeden Kundenmandanten.

**Können andere Partner oder Kunden die Verknüpfung mit der Partner-ID bearbeiten oder entfernen?**

Die Verknüpfung wird auf Benutzerkontoebene zugeordnet. Nur Sie können die Verknüpfung mit der Partner-ID bearbeiten oder entfernen. Weder der Kunde noch andere Partner können die Verknüpfung mit der Partner-ID ändern.


**Welche MPN-ID muss ich verwenden, wenn mein Unternehmen über mehrere MPN-IDs verfügt?**

Standortbezogene Partnerkonten und die zugehörigen MPN-IDs sollten zum Verknüpfen der Partner-ID verwendet werden.  Unter [Partnerkonten](https://docs.microsoft.com/partner-center/account-structure) finden Sie weitere Informationen hierzu.

**Wo finde ich die beeinflusste Umsatzberichterstattung für die verknüpfte Partner-ID?**

Berichte zur Cloudproduktleistung stehen für Partner im Partner Center unter [Dashboard „My Insights“](https://partner.microsoft.com/membership/reports/myinsights) zur Verfügung. Sie müssen „Partner Admin Link“ als Partnerzuordnungstyp auswählen.

**Warum wird mein Kunde in den Berichten nicht angezeigt?**

Der Kunde wird aus folgenden Gründen in den Berichten nicht angezeigt:

1. Das verknüpfte Benutzerkonto verfügt nicht über [rollenbasierten Zugriff](https://docs.microsoft.com/azure/role-based-access-control/overview) auf ein Azure-Kundenabonnement oder eine entsprechende Ressource.

2. Das Azure-Abonnement, für das der Benutzer über [rollenbasierten Zugriff](https://docs.microsoft.com/azure/role-based-access-control/overview) verfügt, wird nicht verwendet.

**Funktioniert das Verknüpfen der Partner-ID mit Azure Stack?**

Ja, Sie können Ihre Partner-ID mit Azure Stack verknüpfen.

**Wie kann ich meine Partner-ID verknüpfen, wenn mein Unternehmen über [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview) auf Kundenressourcen zugreift?**

Wenn Sie Kunden in die delegierte Azure-Ressourcenverwaltung integrieren, indem Sie [ein Angebot für verwaltete Dienste im Azure Marketplace veröffentlichen](https://docs.microsoft.com/azure/lighthouse/how-to/publish-managed-services-offers), wird Ihre MPN-ID automatisch zugeordnet. Wenn Sie [Kunden durch Bereitstellen von Azure Resource Manager-Vorlagen integrieren](https://docs.microsoft.com/azure/lighthouse/how-to/onboard-customer), müssen Sie Ihre MPN-ID (Microsoft Partner Network) mindestens einem Benutzerkonto zuordnen, das Zugriff auf die einzelnen integrierten Abonnements hat. Dieser Schritt muss in Ihrem Dienstanbietermandanten ausgeführt werden. Der Einfachheit halber empfiehlt es sich, ein Dienstprinzipalkonto in Ihrem Mandanten zu erstellen, das Ihrer MPN-ID zugeordnet ist, und diesem Konto Lesezugriff auf jeden integrierten Kunden zu gewähren.
