---
title: テンプレート デプロイの what-if (プレビュー)
description: Azure Resource Manager テンプレートをデプロイする前に、リソースがどのような変更されるかを確認します。
author: mumian
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: jgao
ms.openlocfilehash: edb9f5e35008b1270031d8e2d5c8a5efa37cb554
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75476273"
---
# <a name="resource-manager-template-deployment-what-if-operation-preview"></a>Resource Manager テンプレートのデプロイの what-if 操作 (プレビュー)

テンプレートをデプロイする前に、発生する変更をプレビューしたいことがあります。 Azure Resource Manager の what-if 操作を使うと、テンプレートをデプロイした場合にリソースがどのように変更されるかを確認できます。 what-if 操作では、既存のリソースに対していかなる変更も行われません。 代わりに、指定したテンプレートがデプロイされた場合の変更が予測されます。

> [!NOTE]
> what-if 操作は、現在プレビューの段階です。 使用するには、[プレビューにサインアップする](https://aka.ms/armtemplatepreviews)必要があります。 プレビュー リリースなので、実際には何も変更されないときに、結果ではリソースが変更されることが示される場合があります。 このような問題を減らす作業が行われていますが、お客様の支援が必要です。 これらの問題を、[https://aka.ms/whatifissues](https://aka.ms/whatifissues)で報告してください。

what-if 操作は、`New-AzDeploymentWhatIf` PowerShell コマンドまたは [デプロイ - What If](/rest/api/resources/deployments/whatif) REST 操作で使用できます。

PowerShell では、出力は次のようになります。

![Resource Manager テンプレートのデプロイの what-if 操作の fullresourcepayload と変更の種類](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

## <a name="change-types"></a>変更の種類

what-if 操作では、6 種類の異なる変更が一覧表示されます。

- **作成**:現在、リソースは存在しませんが、テンプレートで定義されています。 リソースが作成されます。

- **[削除]** :この変更の種類は、デプロイの[完全モード](deployment-modes.md)を使用するときにのみ適用されます。 リソースは存在しますが、テンプレートでは定義されていません。 完全モードでは、リソースが削除されます。 この変更の種類には、[完全モードの削除をサポートしている](complete-mode-deletion.md)リソースのみが含まれます。

- **無視**: リソースは存在しますが、テンプレートでは定義されていません。 リソースはデプロイまたは変更されません。

- **変更なし**: リソースは存在し、テンプレートで定義されています。 リソースは再デプロイされますが、リソースのプロパティは変更されません。 この変更の種類は、[ResultFormat](#result-format) が `FullResourcePayloads` に設定されている場合に返されます。これは既定値です。

- **変更**: リソースは存在し、テンプレートで定義されています。 リソースは再デプロイされ、リソースのプロパティが変更されます。 この変更の種類は、[ResultFormat](#result-format) が `FullResourcePayloads` に設定されている場合に返されます。これは既定値です。

- **デプロイ**: リソースは存在し、テンプレートで定義されています。 リソースは再デプロイされます。 リソースのプロパティは、変更される場合と変更されない場合があります。 いずれかのプロパティが変更されるかどうか判断するのに十分な情報がない場合、操作ではこの変更の種類が返されます。 この状況は、[ResultFormat](#result-format) が `ResourceIdOnly` に設定されている場合にのみ表示されます。

## <a name="deployment-scope"></a>デプロイのスコープ

デプロイの what-if 操作は、サブスクリプション レベルまたはリソース グループ レベルで使用できます。 デプロイのスコープは、`-ScopeType` パラメーターを使用して設定します。 指定できる値は、`Subscription` と `ResourceGroup` です。 この記事では、リソース グループのデプロイについて説明します。

サブスクリプション レベルでのデプロイについては、「[サブスクリプション レベルでリソース グループとリソースを作成する](deploy-to-subscription.md#)」を参照してください。

## <a name="result-format"></a>結果の形式

予測される変更に関して返される詳細さのレベルを制御できます。 変更されるリソースの一覧と、変更されるプロパティの詳細を取得するには、`ResultFormat` パラメーターを `FullResourcePayloads` に設定します。 変更されるリソースの一覧を取得するには、`ResultFormat` パラメーターを `ResourceIdOnly` に設定します。 既定値は `FullResourcePayloads` です。  

次のスクリーンショットでは、2 つの異なる出力形式が示されています。

- 完全なリソース ペイロード

    ![Resource Manager テンプレートのデプロイの what-if 操作の fullresourcepayload の出力](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-fullresourcepayload.png)

- リソース ID のみ

    ![Resource Manager テンプレートのデプロイの what-if 操作の resourceidonly の出力](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-resourceidonly.png)

## <a name="run-what-if-operation"></a>what-if 操作を実行する

### <a name="set-up-environment"></a>環境を設定する

what-if がどのように動作するか見るため、いくつかのテストを実行してみましょう。 まず、[ストレージ アカウントを作成する Azure クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json)からテンプレートをデプロイします。 既定のストレージ アカウントの種類は `Standard_LRS` です。 このストレージ アカウントを使用して、what-if により変更がどのように報告されるかをテストします。

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

### <a name="test-modification"></a>変更をテストする

デプロイが完了すると、what-if 操作をテストできる状態になります。 what-if コマンドを実行しますが、ストレージ アカウントの種類を `Standard_GRS` に変更します。

```azurepowershell-interactive
New-AzDeploymentWhatIf `
  -ScopeType ResourceGroup `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" `
  -storageAccountType Standard_GRS
```

what-if の出力は次のようになります。

![Resource Manager テンプレートのデプロイの what-if 操作の出力](./media/template-deploy-what-if/resource-manager-deployment-whatif-output.png)

出力の上部で、変更の種類を示す色が定義されていることに注意してください。

出力の下部では、SKU の名前 (ストレージ アカウントの種類) が **Standard_LRS** から **Standard_GRS** に変更されることが示されています。

削除済みとして一覧されたプロパティの一部は、実際には変更されません。 上の図では、そのセクションにある accessTier、encryption.keySource などがこれらのプロパティです。 プロパティは、テンプレートに含まれていない場合、削除済みとして誤って報告されることがありますが、デプロイ時に既定値として自動的に設定されます。 この結果は、what-if 応答では "ノイズ" と見なされます。 最後にデプロイされたリソースには、プロパティ用の値セットがあります。 what-if 操作が成熟すると、これらのプロパティは結果から除外されます。

### <a name="test-deletion"></a>削除をテストする

what-if 操作では、[デプロイ モード](deployment-modes.md)の使用がサポートされています。 完全モードに設定すると、テンプレートに含まれていないリソースは削除されます。 次の例では、[リソースが定義されていないテンプレート](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json)を完全モードでデプロイします。

```azurepowershell-interactive
New-AzDeploymentWhatIf `
  -ScopeType ResourceGroup `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

テンプレートでリソースが何も定義されておらず、デプロイ モードが完全に設定されているため、ストレージ アカウントは削除されます。

![Resource Manager テンプレートのデプロイの what-if 操作の完全モード デプロイに対する出力](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

what-if では実際には何も変更されないことを覚えておくことが重要です。 ストレージ アカウントはリソース グループに存在しています。

## <a name="next-steps"></a>次のステップ

- what-if のプレビュー リリースで正しくない結果が表示された場合は、[https://aka.ms/whatifissues](https://aka.ms/whatifissues) で問題を報告してください。
- Azure PowerShell を使用してテンプレートをデプロイするには、「[Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](deploy-powershell.md)」を参照してください。
- REST を使用してテンプレートをデプロイするには、「[Resource Manager テンプレートと Resource Manager REST API を使用したリソースのデプロイ](deploy-rest.md)」を参照してください。
- エラーが発生したときに正常なデプロイにロールバックするには、「[エラー発生時に正常なデプロイにロールバックする](rollback-on-error.md)」を参照してください。
