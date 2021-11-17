---
title: トラステッド起動の VM をデプロイする
description: トラステッド起動を使用する VM をデプロイします。
author: khyewei
ms.author: khwei
ms.reviewer: cynthn
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: how-to
ms.date: 10/25/2021
ms.custom: template-how-to
ms.openlocfilehash: 0212c21a90caa533003d2304cfded72eb2370b49
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286053"
---
# <a name="deploy-a-vm-with-trusted-launch-enabled"></a>トラステッド起動を有効にして VM をデプロイする

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット

[トラステッド起動](trusted-launch.md)は、[第 2 世代](generation-2.md)の VM のセキュリティを向上させる手段です。 トラステッド起動を使用すると、vTPM やセキュア ブートのようなインフラストラクチャ テクノロジを組み合わせることによって、高度で永続的な攻撃手法から保護されます。

## <a name="prerequisites"></a>必須コンポーネント 

- まだ Microsoft Defender for Cloud にサブスクリプションがオンボードされていない場合は、[サブスクリプションを Microsoft Defender for Cloud にオンボード](https://azure.microsoft.com/services/security-center/?&ef_id=CjwKCAjwwsmLBhACEiwANq-tXHeKhV--teH6kIijnBTmP-PgktfvGr5zW9TAx00SR7xsGUc3sTj5sBoCkEoQAvD_BwE:G:s&OCID=AID2200277_SEM_CjwKCAjwwsmLBhACEiwANq-tXHeKhV--teH6kIijnBTmP-PgktfvGr5zW9TAx00SR7xsGUc3sTj5sBoCkEoQAvD_BwE:G:s&gclid=CjwKCAjwwsmLBhACEiwANq-tXHeKhV--teH6kIijnBTmP-PgktfvGr5zW9TAx00SR7xsGUc3sTj5sBoCkEoQAvD_BwE#overview)する必要があります。 Microsoft Defender for Cloud には Free レベルがあります。各種の Azure リソースやハイブリッド リソースについてのきわめて有益な分析情報が得られます。 トラステッド起動は Defender for Cloud を活用して、VM の正常性に関するさまざまな推奨事項を表示します。 

- Azure Policy のイニシアティブをサブスクリプションに割り当てます。 ポリシーのイニシアティブは、サブスクリプションごとに 1 回だけ割り当てる必要があります。 これによって必要な全拡張機能が、サポート対象のすべての VM に自動的にインストールされます。 
    - トラステッド起動が有効な VM でゲスト構成証明を有効にするための前提条件を構成します 

    - 仮想マシンに Azure Monitor と Azure Security エージェントを自動的にインストールするようにマシンを構成します 

 
## <a name="deploy-a-trusted-vm"></a>信頼された VM をデプロイする
トラステッド起動を有効にして仮想マシンを作成します。 以下のオプションを選択してください。

### <a name="portal"></a>[ポータル](#tab/portal)

1. Azure [Portal](https://portal.azure.com) にサインインします。
2. 「**仮想マシン**」を検索します。
3. **[サービス]** で、 **[仮想マシン]** を選択します。
4. **[仮想マシン]** ページで、 **[追加]** を選択してから、 **[仮想マシン]** を選択します。
5. **[プロジェクトの詳細]** で、正しいサブスクリプションが選択されていることを確認します。
6. **[リソース グループ]** で **[新規作成]** を選択し、リソース グループの名前を入力するか、ドロップダウンから既存のリソース グループを選択します。
7. **[インスタンスの詳細]** で、仮想マシンの名前を入力し、[トラステッド起動](trusted-launch.md#limitations)がサポートされているリージョンを選択します。
1. **[セキュリティの種類]** で **[トラステッド起動の仮想マシン]** を選択します。 これにより、さらに **[セキュア ブート]** と **[vTPM]** の 2 つのオプションが表示されるようになります。 自分のデプロイに合った適切なオプションを選択します。
    :::image type="content" source="media/trusted-launch/security.png" alt-text="トラステッド起動のオプションを示すスクリーンショット。":::
3. **[イメージ]** の **[[トラステッド起動] と互換性のある、推奨される Gen 2 イメージ]** からイメージを選択します。 [トラステッド起動をサポートするイメージ](trusted-launch.md#limitations)の一覧を参照してください。 
   > [!TIP]
   > ドロップダウンに目的の Gen 2 バージョンのイメージが表示されない場合は、 **[すべてのイメージを表示]** を選択し、 **[セキュリティの種類]** フィルターを **[トラステッド起動]** に変更します。
13. トラステッド起動がサポートされている VM サイズを選択します。 [サポートされているサイズ](trusted-launch.md#limitations)の一覧を参照してください。
14. **[管理者アカウント]** の情報を入力した後、 **[受信ポートの規則]** を指定します。
15. ページの下部にある **[確認および作成]** を選択します
16. **[仮想マシンの作成]** ページで、デプロイしようとしている VM の詳細を確認できます。 検証結果が合格と表示されたら、 **[作成]** を選択します。

    :::image type="content" source="media/trusted-launch/validation.png" alt-text="トラステッド起動のオプションが含まれる検証ページのスクリーンショット。":::


VM がデプロイされるまでに数分かかります。

### <a name="cli"></a>[CLI](#tab/cli)

最新バージョンの Azure CLI を実行していることを確認してください。 

`az login` を使用して Azure にサインインします。  

```azurecli-interactive
az login 
```

トラステッド起動を使用して仮想マシンを作成します。 

```azurecli-interactive
az group create -n myresourceGroup -l eastus 
az vm create \ 
   --resource-group myResourceGroup \ 
   --name myVM \ 
   --image UbuntuLTS \ 
   --admin-username azureuser \ 
   --generate-ssh-keys \ 
   --SecurityType trustedLaunch \ 
   --EnableSecureBoot true \  
   --EnableVtpm true \
```
 
既存の VM を対象に、セキュア ブートと vTPM の設定を有効または無効にすることができます。 セキュア ブートと vTPM の設定を使用して仮想マシンを更新すると、自動再起動がトリガーされます。

```azurecli-interactive
az vm update \  
   --resource-group myResourceGroup \ 
   --name myVM \ 
   --EnableSecureBoot \  
   --EnableVtpm 
```  

### <a name="powershell"></a>[PowerShell](#tab/powershell)


トラステッド起動を使用して VM をプロビジョニングするためには、`Set-AzVmSecurityType` コマンドレットを使用して、まず VM の `TrustedLaunch` を有効にする必要があります。 その後、Set-AzVmUefi コマンドレットを使用して vTPM と SecureBoot の構成を設定できます。 クイック スタートとして以下のスニペットをご使用ください。ただし、この例の値は実際の値に置き換える必要があります。 

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$location = "West US"
$vmName = "myTrustedVM"
$vmSize = Standard_B2s
$publisher = "MicrosoftWindowsServer"
$offer = "WindowsServer"
$sku = "2019-Datacenter"
$version = latest
$cred = Get-Credential `
   -Message "Enter a username and password for the virtual machine."

$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize 

$vm = Set-AzVMOperatingSystem `
   -VM $vm -Windows `
   -ComputerName $vmName `
   -Credential $cred `
   -ProvisionVMAgent `
   -EnableAutoUpdate 

$vm = Add-AzVMNetworkInterface -VM $vm `
   -Id $NIC.Id 

$vm = Set-AzVMSourceImage -VM $vm `
   -PublisherName $publisher `
   -Offer $offer `
   -Skus $sku `
   -Version $version 

$vm = Set-AzVMOSDisk -VM $vm `
   -StorageAccountType "StandardSSD_LRS" `
   -CreateOption "FromImage" 

$vm = Set-AzVmSecurityType -VM $vm `
   -SecurityType "TrustedLaunch" 

$vm = Set-AzVmUefi -VM $vm `
   -EnableVtpm $true `
   -EnableSecureBoot $true 

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vm 
```
 


### <a name="template"></a>[テンプレート](#tab/template)

クイックスタート テンプレートを使用して、トラステッド起動 VM をデプロイできます。

**Linux**

[![Azure へのデプロイ](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-linux%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-linux%2FcreateUiDefinition.json)

**Windows**

[![Azure へのデプロイ](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-windows%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-windows%2FcreateUiDefinition.json)

---

## <a name="verify-or-update-your-settings"></a>設定を確認または更新する

ポータルで VM の  **[概要]**  ページに移動することにより、既存の VM に対するトラステッド起動の構成を表示できます。 **[プロパティ]** タブには、トラステッド起動機能のステータスが表示されます。

:::image type="content" source="media/trusted-launch/overview-properties.png" alt-text="VM のトラステッド起動のプロパティのスクリーンショット。":::

トラステッド起動の構成を変更するには、左側のメニューで  **[設定]**  セクションの  **[構成]**  を選択します。 セキュア ブートと vTPM は、トラステッド起動の [セキュリティの種類] セクションで有効または無効にすることができます。 終わったら、ページの上部にある [保存] を選択します。 

:::image type="content" source="media/trusted-launch/update.png" alt-text="トラステッド起動の設定を変更するためのチェック ボックスを示すスクリーンショット。":::

VM が実行中である場合、VM が再起動されるというメッセージが表示されます。  **[はい]**  を選択した後、VM が再起動されて変更が有効になるまで待ちます。 


## <a name="next-steps"></a>次のステップ

[トラステッド起動](trusted-launch.md)と[第 2 世代](generation-2.md)の VM の詳細を確認します。
