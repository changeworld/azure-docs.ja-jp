---
title: Azure PowerShell を使用して VHD ファイルからカスタム イメージを作成する
description: PowerShell を使用した VHD ファイルからの Azure DevTest Labs カスタム イメージの作成を自動化します。
ms.topic: how-to
ms.date: 10/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2185309194b04d1b76ca84daea19e92e7c017463
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469053"
---
# <a name="create-a-custom-image-from-a-vhd-file-with-powershell"></a>PowerShell を使用して VHD ファイルからカスタム イメージを作成する

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="powershell-steps"></a>PowerShell ステップ

次の手順で、Azure PowerShell を使用して VHD ファイルからカスタム イメージを作成します。

1. PowerShell コマンド プロンプトで、**Connect-AzAccount** コマンドレットを使用して Azure アカウントにサインインします。

   ```powershell
   Connect-AzAccount
   ```

1. **Select-AzSubscription** コマンドレットを使って、Azure サブスクリプションを選択します。 \<subscription ID> は実際のサブスクリプション ID GUID に置き換えてください。

   ```powershell
   $subscriptionId = '<subscription ID>'
   Select-AzSubscription -SubscriptionId $subscriptionId
   ```

1. **Get-AzResource** コマンドレットを呼び出して、ラボ オブジェクトを取得します。 \<lab resource group name> と \<lab name> のプレースホルダーはそれぞれ実際のリソース グループとラボの名前に置き換えてください。

   ```powershell
   $labRg = '<lab resource group name>'
   $labName = '<lab name>'
   $lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
   ```

1. **$vhdUri** 変数のプレース ホルダーを、アップロードした VHD ファイルの URI に置き換えます。 VHD ファイルの URI は、Azure portal でラボのストレージアカウントの BLOB ページから取得できます。 VHD URI の例を次に示します: `https://acontosolab1234.blob.core.windows.net/uploads/myvhd.vhd`。

   ```powershell
   $vhdUri = '<VHD URI>'
   ```

1. **New-AzResourceGroupDeployment** コマンドレットを使用してカスタム イメージを作成します。 \<custom image name> と \<custom image description> のプレースホルダーは目的の名前と説明に置き換えてください。

   ```powershell
   $customImageName = '<custom image name>'
   $customImageDescription = '<custom image description>'

   $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

   New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
   ```

## <a name="complete-powershell-script"></a>完全な PowerShell スクリプト

これまでの手順を組み合わせると、VHD ファイルからカスタムイメージを作成する次の PowerShell スクリプトが生成されます。 このスクリプトを使用するには、次のプレースホルダーを実際の値に置き換えます。

- \<subscription ID>
- \<lab resource group name>
- \<lab name>
- \<VHD URI>
- \<custom image name>
- \<custom image description>

```powershell
# Log in to your Azure account.
Connect-AzAccount

# Select the desired Azure subscription.
$subscriptionId = '<subscription ID>'
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab object.
$labRg = '<lab resource group name>'
$labName = '<lab name>'
$lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Set the URI of the VHD file.
$vhdUri = '<VHD URI>'

# Set the custom image name and description values.
$customImageName = '<custom image name>'
$customImageDescription = '<custom image description>'

# Set up the parameters object.
$parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

# Create the custom image.
New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
```

## <a name="next-steps"></a>次の手順

- [DevTest ラボのカスタム イメージと数式を比較する](devtest-lab-comparing-vm-base-image-types.md)
- [Copying Custom Images between Azure DevTest Labs (Azure DevTest Labs 間でのカスタム イメージのコピー)](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)
