---
title: Regionen übergreifendes Verschieben Ihrer Dienstressourcen
titleSuffix: Azure Cognitive Search
description: In diesem Artikel erfahren Sie, wie Sie Ihre Azure Cognitive Search-Ressourcen in der Azure-Cloud von einer Region in eine andere verschieben.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 03/06/2020
ms.openlocfilehash: 183a937a232dbd28962bb7d6ef42b0d78b8a81fd
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78850685"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>Verschieben Ihres Azure Cognitive Search-Diensts in eine andere Azure-Region

Gelegentlich fragen Kunden an, wie ein vorhandener Suchdienst in eine andere Region verschoben wird. Derzeit gibt es keine integrierten Mechanismen oder Tools, die Sie bei dieser Aufgabe unterstützen. Es bleibt ein manueller Vorgang, der nachstehend in diesem Artikel beschrieben wird.

> [!NOTE]
> Im Azure-Portal verfügen alle Dienste über einen Befehl zum **Exportieren von Vorlagen**. Bei Azure Cognitive Search erstellt dieser Befehl eine grundlegende Definition eines Diensts (Name, Standort, Ebene, Replikat und Partitionsanzahl), erkennt jedoch nicht den Inhalt des Diensts und überträgt auch keine Schlüssel, Rollen oder Protokolle. Der Befehl ist zwar vorhanden, es empfiehlt sich jedoch nicht, ihn zum Verschieben eines Suchdiensts zu verwenden.

## <a name="steps-for-moving-a-service"></a>Schritte zum Verschieben eines Diensts

Wenn Sie einen Suchdienst in eine andere Region verschieben müssen, sollten Sie Schritte ähnlich den folgenden ausführen:

1. Identifizieren Sie zugehörige Dienste, um die gesamten Auswirkungen der Verlagerung eines Diensts zu verstehen. Möglicherweise verwenden Sie Azure Storage für die Protokollierung, als Wissensspeicher oder als externe Datenquelle. Möglicherweise verwenden Sie Cognitive Services zur KI-Anreicherung. Das Zugreifen auf Dienste in anderen Regionen ist verbreitet, aber es fallen zusätzliche Gebühren für die Bandbreite an. Wenn Sie KI-Anreicherung verwenden, müssen sich Cognitive Services und Azure Cognitive Search in derselben Region befinden.

1. Erstellen Sie ein Inventar Ihrer vorhandenen Dienste, um eine vollständige Liste der Objekte im Dienst zu erfassen. Wenn Sie die Protokollierung aktiviert haben, erstellen und archivieren Sie die Berichte, die Sie möglicherweise für einen historischen Datensatz benötigen.

1. Überprüfen Sie die Preise und die Verfügbarkeit in der neuen Region, um die Verfügbarkeit von Azure Cognitive Search und etwaiger zugehöriger Dienste sicherzustellen, die Sie möglicherweise in der gleichen Region erstellen möchten. Überprüfen Sie die Featureparität. Einige Previewfunktionen weisen eine eingeschränkte Verfügbarkeit auf.

1. Erstellen Sie einen Dienst in der neuen Region, und veröffentlichen Sie alle vorhandenen Indizes, Indexer, Datenquellen, Skillsets, Wissensspeicher und Synonymzuordnungen aus dem Quellcode erneut. Dienstnamen müssen eindeutig sein, daher können Sie den bisherigen Namen nicht wiederverwenden.

1. Laden Sie Indizes und Wissensspeicher ggf. neu. Entweder verwenden Sie den Anwendungscode, um JSON-Daten in einen Index zu pushen, oder Sie führen die Indexer erneut aus, um Dokumente aus externen Quellen zu pullen. 

1. Aktivieren Sie die Protokollierung, und erstellen Sie Sicherheitsrollen neu, sofern Sie diese verwenden.

1. Aktualisieren Sie Clientanwendungen und Testsammlungen, sodass sie den neuen Dienstnamen und die API-Schlüssel verwenden, und testen Sie alle Anwendungen.

1. Löschen Sie den alten Dienst, wenn der neue Dienst vollständig getestet und einsatzbereit ist.

## <a name="next-steps"></a>Nächste Schritte

+ [Auswählen einer Ebene](search-sku-tier.md)
+ [Erstellen eines Suchdiensts](search-create-service-portal.md)
+ [Laden von Suchdokumenten](search-what-is-data-import.md)
+ [Aktivieren der Protokollierung](search-monitor-logs.md)


<!-- To move your Azure Cognitive Service account from one region to another, you will create an export template to move your subscription(s). After moving your subscription, you will need to move your data and recreate your service.

In this article, you'll learn how to:

> [!div class="checklist"]
> * Export a template.
> * Modify the template: adding the target region, search and storage account names.
> * Deploy the template to create the new search and storage accounts.
> * Verify your service status in the new region
> * Clean up resources in the source region.

## Prerequisites

- Ensure that the services and features that your account uses are supported in the target region.

- For preview features, ensure that your subscription is whitelisted for the target region. For more information about preview features, see [knowledge stores](https://docs.microsoft.com/azure/search/knowledge-store-concept-intro), [incremental enrichment](https://docs.microsoft.com/azure/search/cognitive-search-incremental-indexing-conceptual), and [private endpoint](https://docs.microsoft.com/azure/search/service-create-private-endpoint).

## Assessment and planning

When you move your search service to the new region, you will need to [move your data to the new storage service](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#configure-the-new-storage-account) and then rebuild your indexes, skillsets and knowledge stores. You should record current settings and copy json files to make the rebuilding of your service easier and faster.

## Moving your search service's resources

To start you will export and then modify a Resource Manager template.

### Export a template

1. Sign in to the [Azure portal](https://portal.azure.com).

2. Go to your Resource Group page.

> [!div class="mx-imgBorder"]
> ![Resource Group page example](./media/search-move-resource/export-template-sample.png)

3. Select **All resources**.

3. In the left hand navigation menu select **Export template**.

4. Choose **Download** in the **Export template** page.

5. Locate the .zip file that you downloaded from the portal, and unzip that file to a folder of your choice.

The zip file contains the .json files that comprise the template and scripts to deploy the template.

### Modify the template

You will modify the template by changing the search and storage account names and regions. The names must follow the rules for each service and region naming conventions. 

To obtain region location codes, see [Azure Locations](https://azure.microsoft.com/global-infrastructure/locations/).  The code for a region is the region name with no spaces, **Central US** = **centralus**.

1. In the Azure portal, select **Create a resource**.

2. In **Search the Marketplace**, type **template deployment**, and then press **ENTER**.

3. Select **Template deployment**.

4. Select **Create**.

5. Select **Build your own template in the editor**.

6. Select **Load file**, and then follow the instructions to load the **template.json** file that you downloaded and unzipped in the previous section.

7. In the **template.json** file, name the target search and storage accounts by setting the default value of the search and storage account names. 

8. Edit the **location** property in the **template.json** file to the target region for both your search and storage services. This example sets the target region to `centralus`.

```json
},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Search/searchServices",
            "apiVersion": "2015-08-19",
            "name": "[parameters('searchServices_target_region_search_name')]",
            "location": "centralus",
            "sku": {
                "name": "standard"
            },
            "properties": {
                "replicaCount": 1,
                "partitionCount": 1,
                "hostingMode": "Default"
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[parameters('storageAccounts_tagetstorageregion_name')]",
            "location": "centralus",
            "sku": {
                "name": "Standard_RAGRS",
                "tier": "Standard"
            },
```

### Deploy the template

1. Save the **template.json** file.

2. Enter or select the property values:

- **Subscription**: Select an Azure subscription.

- **Resource group**: Select **Create new** and give the resource group a name.

- **Location**: Select an Azure location.

3. Click the **I agree to the terms and conditions stated above** checkbox, and then click the **Select Purchase** button.

## Verifying your services' status in new region

To verify the move, open the new resource group and your services will be listed with the new region.

To move your data from your source region to the target region, please see this article's guidelines for [moving your data to the new storage account](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#move-data-to-the-new-storage-account).

## Clean up resources in your original region

To commit the changes and complete the move of your service account, delete the source service account.

## Next steps

[Create an index](https://docs.microsoft.com/azure/search/search-get-started-portal)

[Create a skillset](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob)

[Create a knowledge store](https://docs.microsoft.com/azure/search/knowledge-store-create-portal) -->