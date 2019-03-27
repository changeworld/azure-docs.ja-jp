---
title: Azure DevTest Labs で VM のリソース グループを指定する | Microsoft Docs
description: Azure DevTest Labs でラボの VM に対するリソース グループを指定する方法について説明します。
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2019
ms.author: spelluru
ms.openlocfilehash: 94e5f5b29e93409df2373cf6c56e8185dc5373a2
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312976"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Azure DevTest Labs でラボの仮想マシンのリソース グループを指定する
ラボの所有者は、ラボの仮想マシンが特定のリソース グループに作成されるように構成することができます。 この機能は、次のシナリオで役立ちます。 

- サブスクリプション内のリソース グループのうち、ラボによって作成されたものが少ない。
- 自分で構成した一連の固定のリソース グループ内でラボを運用する。
- Azure サブスクリプション内でリソース グループを作成するために必要な制限および承認を回避する。
- ラボのすべてのリソースを単一のリソース グループ内に統合し、リソースの追跡および[ポリシー](../governance/policy/overview.md)の適用を簡単に行えるようにして、リソースをリソース グループ レベルで管理する。

この機能では、スクリプトを使用して、ラボのすべての VM に対し、Azure サブスクリプション内の新規または既存のリソース グループを指定できます。 現時点では、この機能は API によってサポートされています。 

## <a name="api-to-configure-a-resource-group-for-lab-virtual-machines"></a>ラボ仮想マシン用のリソース グループを構成するための API
それでは、ラボ所有者がこの API を使用するときに指定できるオプションを見ていきましょう。 

- すべての仮想マシンに対して、**ラボのリソース グループ**を選択することができます。
- すべての仮想マシンに対して、ラボのリソース グループ以外の**既存のリソース グループ**を選択することができます。
- すべての仮想マシンに対して、**新しいリソース グループ**の名前を入力することができます。
- 引き続き既存の動作を使用する、つまりラボの VM ごとにリソース グループを作成することができます。
 
この設定は、ラボで作成される新しい仮想マシンに適用されます。 独自のリソース グループで作成されたラボの古い VM には影響しません。 ラボで作成された環境は、専用のリソース グループに引き続き残っています。

### <a name="how-to-use-this-api"></a>この API の使用方法:
- この API を使用するときは、API バージョン **2018_10_15_preview** を使用します。 
- 新しいリソース グループを指定する場合は、サブスクリプション内の**リソース グループに対する書き込みアクセス許可**があることを確認してください。 書き込みアクセス許可がない場合、指定したリソース グループに新しい仮想マシンを作成するとエラーになります。 
- API を使用するときは、**完全なリソース グループ ID** を渡します。 (例: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`)。 リソース グループがラボと同じサブスクリプション内にあることを確認します。 

## <a name="use-powershell"></a>PowerShell の使用 
次の例では、PowerShell スクリプトを使用して、すべてのラボ仮想マシンを新しいリソース グループに作成する方法を説明します。

```PowerShell
[CmdletBinding()]
Param(
    $subId,
    $labRg,
    $labName,
    $vmRg
)

az login | out-null

az account set --subscription $subId | out-null

$rgId = "/subscriptions/"+$subId+"/resourceGroups/"+$vmRg

"Updating lab '$labName' with vm rg '$rgId'..."

az resource update -g $labRg -n $labName --resource-type "Microsoft.DevTestLab/labs" --api-version 2018-10-15-preview --set properties.vmCreationResourceGroupId=$rgId

"Done. New virtual machines will now be created in the resource group '$vmRg'."
```

次のコマンドを使用してスクリプトを呼び出します (ResourceGroup.ps1 は、上のスクリプトが含まれるファイルです)。 

```PowerShell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの使用
Azure Resource Manager テンプレートを使用してラボを作成する場合は、次の例で示すように、Resource Manager テンプレートのラボ プロパティ セクションで **vmCreationResourceGroupId** プロパティを使用します。

```json
        {
            "type": "microsoft.devtestlab/labs",
            "name": "[parameters('lab_name')]",
            "apiVersion": "2018_10_15_preview",
            "location": "eastus",
            "tags": {},
            "scale": null,
            "properties": {
                "vmCreationResourceGroupId": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>",
                "labStorageType": "Premium",
                "premiumDataDisks": "Disabled",
                "provisioningState": "Succeeded",
                "uniqueIdentifier": "000000000f-0000-0000-0000-00000000000000"
            },
            "dependsOn": []
        },
```


## <a name="next-steps"></a>次の手順
次の記事を参照してください。 

- [ラボのポリシーを設定する](devtest-lab-get-started-with-lab-policies.md)
- [よく寄せられる質問](devtest-lab-faq.md)
