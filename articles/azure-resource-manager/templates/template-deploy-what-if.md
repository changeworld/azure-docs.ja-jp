---
title: テンプレート デプロイの what-if (プレビュー)
description: Azure Resource Manager テンプレートをデプロイする前に、リソースがどのような変更されるかを確認します。
author: mumian
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: jgao
ms.openlocfilehash: b9d4150779842614a5dc284a2b3a489593fabfe1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388541"
---
# <a name="resource-manager-template-deployment-what-if-operation-preview"></a>Resource Manager テンプレートのデプロイの what-if 操作 (プレビュー)

テンプレートをデプロイする前に、発生する変更をプレビューしたいことがあります。 Azure Resource Manager の what-if 操作を使うと、テンプレートをデプロイした場合にリソースがどのように変更されるかを確認できます。 what-if 操作では、既存のリソースに対していかなる変更も行われません。 代わりに、指定したテンプレートがデプロイされた場合の変更が予測されます。

> [!NOTE]
> what-if 操作は、現在プレビューの段階です。 使用するには、[プレビューにサインアップする](https://aka.ms/armtemplatepreviews)必要があります。 プレビュー リリースなので、実際には何も変更されないときに、結果ではリソースが変更されることが示される場合があります。 このような問題を減らす作業が行われていますが、お客様の支援が必要です。 これらの問題を、[https://aka.ms/whatifissues](https://aka.ms/whatifissues)で報告してください。

what-if 操作は PowerShell コマンドまたは REST API 操作で使用できます。

PowerShell の出力には、さまざまな種類の変更を確認するのに役立つ、色分けされた結果が表示されます。

![Resource Manager テンプレートのデプロイの what-if 操作の fullresourcepayload と変更の種類](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

テキスト出力は次のとおりです。

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

## <a name="what-if-commands"></a>What-if コマンド

what-if 操作には、Azure PowerShell または Azure REST API を使用できます。

### <a name="azure-powershell"></a>Azure PowerShell

テンプレートをデプロイする前に変更のプレビューを表示するには、デプロイ コマンドに `-Whatif` スイッチ パラメーターを追加します。

* リソース グループ デプロイの場合は `New-AzResourceGroupDeployment -Whatif`
* サブスクリプション レベルのデプロイの場合は `New-AzSubscriptionDeployment -Whatif` および `New-AzDeployment -Whatif`

または、`-Confirm` スイッチ パラメーターを使用して、変更をプレビューし、デプロイを続行するかどうかを確認するプロンプトを表示することもできます。

* リソース グループ デプロイの場合は `New-AzResourceGroupDeployment -Confirm`
* サブスクリプション レベルのデプロイの場合は `New-AzSubscriptionDeployment -Confirm` および `New-AzDeployment -Confirm`

上記のコマンドは、手動で検査できるテキストの概要を返します。 プログラムによって変更を検査できるオブジェクトを取得するには、以下を使用します。

* リソース グループ デプロイの場合は `$results = Get-AzResourceGroupDeploymentWhatIf`
* サブスクリプション レベルのデプロイの場合は `$results = Get-AzSubscriptionDeploymentWhatIf` または `$results = Get-AzDeploymentWhatIf`

> [!NOTE]
> バージョン 2.0.1-alpha5 より前のリリースでは、`New-AzDeploymentWhatIf` コマンドが使用されていました。 このコマンドは、`Get-AzDeploymentWhatIf`、`Get-AzResourceGroupDeploymentWhatIf`、および `Get-AzSubscriptionDeploymentWhatIf` コマンドに置き換えられました。 以前のバージョンを使用している場合は、その構文を更新する必要があります。 `-ScopeType` パラメーターは削除されました。

### <a name="azure-rest-api"></a>Azure REST API

REST API については、以下を使用します。

* リソース グループ デプロイの場合は「[デプロイ - What If](/rest/api/resources/deployments/whatif)」
* サブスクリプション レベルのデプロイの場合は「[デプロイ - サブスクリプション スコープ での What If](/rest/api/resources/deployments/whatifatsubscriptionscope)」

## <a name="change-types"></a>変更の種類

what-if 操作では、6 種類の異なる変更が一覧表示されます。

- **作成**:現在、リソースは存在しませんが、テンプレートで定義されています。 リソースが作成されます。

- **[削除]** :この変更の種類は、デプロイの[完全モード](deployment-modes.md)を使用するときにのみ適用されます。 リソースは存在しますが、テンプレートでは定義されていません。 完全モードでは、リソースが削除されます。 この変更の種類には、[完全モードの削除をサポートしている](complete-mode-deletion.md)リソースのみが含まれます。

- **無視**: リソースは存在しますが、テンプレートでは定義されていません。 リソースはデプロイまたは変更されません。

- **変更なし**: リソースは存在し、テンプレートで定義されています。 リソースは再デプロイされますが、リソースのプロパティは変更されません。 この変更の種類は、[ResultFormat](#result-format) が `FullResourcePayloads` に設定されている場合に返されます。これは既定値です。

- **変更**: リソースは存在し、テンプレートで定義されています。 リソースは再デプロイされ、リソースのプロパティが変更されます。 この変更の種類は、[ResultFormat](#result-format) が `FullResourcePayloads` に設定されている場合に返されます。これは既定値です。

- **デプロイ**: リソースは存在し、テンプレートで定義されています。 リソースは再デプロイされます。 リソースのプロパティは、変更される場合と変更されない場合があります。 いずれかのプロパティが変更されるかどうか判断するのに十分な情報がない場合、操作ではこの変更の種類が返されます。 この状況は、[ResultFormat](#result-format) が `ResourceIdOnly` に設定されている場合にのみ表示されます。

## <a name="result-format"></a>結果の形式

予測される変更に関して返される詳細さのレベルを制御できます。 デプロイ コマンド (`New-Az*Deployment`) では、 **-WhatIfResultFormat** パラメーターを使用します。 プログラム オブジェクト コマンド (`Get-Az*DeploymentWhatIf`) では、**ResultFormat** パラメーターを使用します。

変更されるリソースの一覧と、変更されるプロパティの詳細を取得するには、format パラメーターを **FullResourcePayloads** に設定します。 変更されるリソースの一覧を取得するには、format パラメーターを **ResourceIdOnly** に設定します。 既定値は **FullResourcePayloads** です。  

次の結果では、2 つの異なる出力形式が示されています。

- 完全なリソース ペイロード

  ```powershell
  Resource and property changes are indicated with these symbols:
    - Delete
    + Create
    ~ Modify

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
      - tags.Owner: "Team A"
      ~ properties.addressSpace.addressPrefixes: [
        - 0: "10.0.0.0/16"
        + 0: "10.0.0.0/15"
        ]
      ~ properties.subnets: [
        - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

        ]

  Resource changes: 1 to modify.
  ```

- リソース ID のみ

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>what-if 操作を実行する

### <a name="set-up-environment"></a>環境を設定する

what-if がどのように動作するか見るため、いくつかのテストを実行してみましょう。 まず、[仮想ネットワークを作成するテンプレート](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json)をデプロイします。 この仮想ネットワークを使用して、what-if により変更がどのように報告されるかをテストします。

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

### <a name="test-modification"></a>変更をテストする

デプロイが完了すると、what-if 操作をテストできる状態になります。 ここでは、[仮想ネットワークを変更するテンプレート](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json)をデプロイします。 元のタグのうち 1 つが存在せず、サブネットが削除され、アドレス プレフィックスが変更されています。

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

what-if の出力は次のようになります。

![Resource Manager テンプレートのデプロイの what-if 操作の出力](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

テキスト出力は次のとおりです。

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

        name:                     "subnet001"
        properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

出力の上部で、変更の種類を示す色が定義されていることに注意してください。

出力の下部には、タグ Owner が削除されたことが表示されています。 アドレス プレフィックスが 10.0.0.0/16 から 10.0.0.0/15 に変更されました。 subnet001 という名前のサブネットが削除されました。 この変更は実際にはデプロイされていないことに注意してください。 テンプレートをデプロイした場合に行われる変更のプレビューが表示されています。

削除済みとして一覧されたプロパティの一部は、実際には変更されません。 プロパティは、テンプレートに含まれていない場合、削除済みとして誤って報告されることがありますが、デプロイ時に既定値として自動的に設定されます。 この結果は、what-if 応答では "ノイズ" と見なされます。 最後にデプロイされたリソースには、プロパティ用の値セットがあります。 what-if 操作が成熟すると、これらのプロパティは結果から除外されます。

## <a name="programmatically-evaluate-what-if-results"></a>what-if 結果のプログラムによる評価

次に、コマンドを変数に設定して、what-if 結果をプログラムで評価してみましょう。

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIf `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

それぞれの変更の概要を確認できます。

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

## <a name="confirm-deletion"></a>削除の確定

what-if 操作では、[デプロイ モード](deployment-modes.md)の使用がサポートされています。 完全モードに設定すると、テンプレートに含まれていないリソースは削除されます。 次の例では、[リソースが定義されていないテンプレート](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json)を完全モードでデプロイします。

テンプレートをデプロイする前に変更のプレビューを表示するには、デプロイ コマンドで `-Confirm` スイッチ パラメーターを使用します。 変更が期待通りである場合は、デプロイを完了することを確認します。

```azurepowershell
New-AzResourceGroupDeployment `
  -Confirm `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

テンプレートでリソースが何も定義されておらず、デプロイ モードが Complete に設定されているため、仮想ネットワークは削除されます。

![Resource Manager テンプレートのデプロイの what-if 操作の完全モード デプロイに対する出力](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

テキスト出力は次のとおりです。

```powershell
Resource and property changes are indicated with this symbol:
  - Delete

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  - Microsoft.Network/virtualNetworks/vnet-001

      id:
"/subscriptions/./resourceGroups/ExampleGroup/providers/Microsoft.Network/virtualNet
works/vnet-001"
      location:        "centralus"
      name:            "vnet-001"
      tags.CostCenter: "12345"
      tags.Owner:      "Team A"
      type:            "Microsoft.Network/virtualNetworks"

Resource changes: 1 to delete.

Are you sure you want to execute the deployment?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"):
```

期待通りの変更が表示され、デプロイを実行することを確認できます。

## <a name="next-steps"></a>次のステップ

- what-if のプレビュー リリースで正しくない結果が表示された場合は、[https://aka.ms/whatifissues](https://aka.ms/whatifissues) で問題を報告してください。
- Azure PowerShell を使用してテンプレートをデプロイするには、「[Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](deploy-powershell.md)」を参照してください。
- REST を使用してテンプレートをデプロイするには、「[Resource Manager テンプレートと Resource Manager REST API を使用したリソースのデプロイ](deploy-rest.md)」を参照してください。
