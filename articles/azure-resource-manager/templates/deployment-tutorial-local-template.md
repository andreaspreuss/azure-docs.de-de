---
title: 'Tutorial: Bereitstellen einer lokalen Azure Resource Manager-Vorlage'
description: Erfahren Sie, wie Sie eine Azure Resource Manager-Vorlage von Ihrem lokalen Computer bereitstellen.
ms.date: 03/13/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: c8e3eb62fa52caeaa63808b6b9ea199bdff5c4da
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80081819"
---
# <a name="tutorial-deploy-a-local-azure-resource-manager-template"></a>Tutorial: Bereitstellen einer lokalen Azure Resource Manager-Vorlage

Erfahren Sie, wie Sie eine Azure Resource Manager-Vorlage von Ihrem lokalen Computer bereitstellen. Dieser Schritt dauert ungefähr **8 Minuten**.

Dieses Tutorial ist das erste einer Reihe. Während Sie die Reihe durchlaufen, modularisieren Sie die Vorlage, indem Sie eine verknüpfte Vorlage erstellen, die verknüpfte Vorlage in einem Speicherkonto speichern und sie mit dem SAS-Token sichern. Sie erfahren außerdem, wie Sie eine DevOp-Pipeline zum Bereitstellen von Vorlagen erstellen. Diese Reihe konzentriert sich auf die Vorlagenbereitstellung.  Wenn Sie sich mit der Vorlagenentwicklung vertraut machen möchten, verwenden Sie die [Tutorials für Anfänger](./template-tutorial-create-first-template.md).

## <a name="get-tools"></a>Erforderliche Tools

Zunächst sorgen wir dafür, dass Sie über die Tools verfügen, die Sie zum Bereitstellen von Vorlagen benötigen.

### <a name="command-line-deployment"></a>Befehlszeilenbereitstellung

Zum Bereitstellen der Vorlage benötigen Sie entweder Azure PowerShell oder die Azure CLI. Die Installationsanweisungen finden Sie unter:

- [Installieren von Azure PowerShell](/powershell/azure/install-az-ps)
- [Installieren der Azure CLI unter Windows](/cli/azure/install-azure-cli-windows)
- [Installieren der Azure CLI unter Linux](/cli/azure/install-azure-cli-linux)

Melden Sie sich nach der Installation von Azure PowerShell oder der Azure CLI zum ersten Mal an. Hilfe finden Sie unter [Anmelden –PowerShell](/powershell/azure/install-az-ps#sign-in) oder [Anmelden – Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in).

### <a name="editor-optional"></a>Editor (optional)

Vorlagen sind JSON-Dateien. Zum Bewerten/Bearbeiten von Vorlagen benötigen Sie einen guten JSON-Editor. Wir empfehlen Visual Studio Code mit der Erweiterung „Azure Resource Manager-Tools“. Wenn Sie diese Tools installieren müssen, finden Sie die entsprechenden Informationen unter [Verwenden von Visual Studio Code für die Erstellung von Azure Resource Manager-Vorlagen](use-vs-code-to-create-template.md).

## <a name="review-template"></a>Überprüfen der Vorlage

Die in diesem Tutorial verwendete Vorlage ähnelt der Vorlage, die im [Tutorial zu Schnellstartvorlagen](template-tutorial-quickstart-template.md) verwendet wird. Wenn Sie die Vorlage erstellen möchten, können Sie dieses Tutorial durchgehen. Für den Abschluss dieses Tutorials ist es jedoch nicht erforderlich.

Die Vorlage stellt ein Speicherkonto, einen App Service-Plan und eine Web-App bereit.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

> [!IMPORTANT]
> Speicherkontonamen müssen zwischen 3 und 24 Zeichen lang sein und dürfen nur Zahlen und Kleinbuchstaben enthalten. Der Name muss eindeutig sein. In der Vorlage ist der Name des Speicherkontos der Projektname mit „store“ am Ende, und der Projektname muss zwischen 3 und 11 Zeichen haben. Daher muss der Projektname die Anforderungen des Speicherkontonamens erfüllen und weniger als 11 Zeichen enthalten.

Speichern Sie eine Kopie der Vorlage auf dem lokalen Computer mit der Erweiterung „.json“, z. B. „azurebereitstellung.json“. Diese Vorlage können Sie später im Tutorial bereitstellen.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich mit Ihren Azure-Anmeldeinformationen an, um Azure PowerShell/Azure CLI zum Bereitstellen einer Vorlage zu verwenden.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az login
```

---

Wenn Sie über mehrere Azure-Abonnements verfügen, wählen Sie dasjenige aus, das Sie verwenden möchten:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Select-AzSubscription [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>Ressourcengruppe erstellen

Beim Bereitstellen einer Vorlage geben Sie eine Ressourcengruppe an, die die bereitgestellten Ressourcen enthalten soll. Erstellen Sie die Ressourcengruppe vor dem Ausführen des Bereitstellungsbefehls entweder mit der Azure CLI oder Azure PowerShell. Wählen Sie die Registerkarten im folgenden Codeabschnitt aus, um zwischen Azure PowerShell und der Azure CLI zu wählen. Die CLI-Beispiele in diesem Artikel sind für die Bash-Shell geschrieben.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource and resource group names"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup `
  -Name $resourceGroupName `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource and resource group names:"
read projectName
resourceGroupName="${projectName}rg"

az group create \
  --name $resourceGroupName \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Bereitstellen der Vorlage

Verwenden Sie eine oder beide Bereitstellungsoptionen, um die Vorlage bereitzustellen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
  -Name DeployLocalTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -verbose
```

Weitere Informationen zum Bereitstellen von Vorlagen mit Azure PowerShell finden Sie unter [Bereitstellen von Ressourcen mit Resource Manager-Vorlagen und Azure PowerShell](./deploy-powershell.md).

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
echo "Enter the same project name:"
read projectName
echo "Enter the template file path and file name:"
read templateFile
resourceGroupName="${projectName}rg"

az deployment group create \
  --name DeployLocalTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName \
  --verbose
```

Weitere Informationen zum Bereitstellen von Vorlagen mit der Azure CLI finden Sie unter [Bereitstellen von Ressourcen mit Resource Manager-Vorlagen und Azure CLI](./deploy-cli.md).

---

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe, um die bereitgestellten Ressourcen zu bereinigen.

1. Wählen Sie im Azure-Portal im linken Menü die Option **Ressourcengruppe** aus.
2. Geben Sie den Namen der Ressourcengruppe in das Feld **Nach Name filtern** ein.
3. Klicken Sie auf den Namen der Ressourcengruppe.
4. Wählen Sie **Ressourcengruppe löschen** aus dem Menü ganz oben aus.

## <a name="next-steps"></a>Nächste Schritte

Sie haben gelernt, eine lokale Vorlage bereitzustellen. Im nächsten Tutorial trennen Sie die Vorlage in eine Hauptvorlage und eine verknüpfte Vorlage und erfahren zudem, wie Sie die verknüpfte Vorlage speichern und sichern.

> [!div class="nextstepaction"]
> [Bereitstellen einer verknüpften Vorlage](./deployment-tutorial-linked-template.md)
