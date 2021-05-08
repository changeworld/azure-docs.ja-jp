---
title: 'クイックスタート: Bicep (プレビュー) ファイルを使用した新しいポリシーの割り当て'
description: このクイックスタートでは、Bicep (プレビュー) ファイルを使用して、ポリシー割り当てを作成し、準拠していないリソースを特定します。
ms.date: 04/01/2021
ms.topic: quickstart
ms.custom: subject-bicepqs
ms.openlocfilehash: 17460f9a06d7225d50933608645ea8aea2f5e8f6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223867"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-bicep-file"></a>クイックスタート: Bicep ファイルを使用して、準拠していないリソースを特定するためのポリシー割り当てを作成する

Azure のコンプライアンスを理解する第一歩は、リソースの状態を特定することです。
このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) にコンパイルされた [Bicep (プレビュー)](https://github.com/Azure/bicep) ファイルを使用してポリシー割り当てを作成し、マネージド ディスクを使用していない仮想マシンを特定するプロセスについて順を追って説明します。 このプロセスを終了すると、マネージド ディスクを使用していない仮想マシンを適切に特定できるようになります。 これらはポリシーの割り当てに "_準拠していません_"。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 テンプレートが Azure portal で開きます。

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure Policy を Azure に割り当てるための ARM テンプレートをデプロイするボタン。" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json":::

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。
- Bicep バージョン `0.3` 以降がインストールされていること。 Bicep CLI をまだお持ちでない場合、または更新する必要がある場合は、「[Bicep のインストール (プレビュー)](../../azure-resource-manager/templates/bicep-install.md)」を参照してください。

## <a name="review-the-bicep-file"></a>Bicep ファイルを確認する

このクイックスタートでは、ポリシー割り当てを作成し、"_マネージド ディスクを使用していない VM の監査_" (`06a78e20-9358-41c9-923c-fb736d382a4d`) という組み込みのポリシー定義を割り当てます。 使用できる組み込みポリシーの部分的な一覧については、[Azure Policy サンプル](./samples/index.md)に関する記事をご覧ください。

次の Bicep ファイルを `assignment.bicep` として作成します。

```bicep
param policyAssignmentName string = 'audit-vm-manageddisks'
param policyDefinitionID string = '/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d'

resource assignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
    name: policyAssignmentName
    properties: {
        scope: subscriptionResourceId('Microsoft.Resources/resourceGroups', resourceGroup().name)
        policyDefinitionId: policyDefinitionID
    }
}

output assignmentId string = assignment.id
```

ファイル内に定義されているリソース:

- [Microsoft.Authorization/policyAssignments](/azure/templates/microsoft.authorization/policyassignments)

## <a name="deploy-the-template"></a>テンプレートのデプロイ

> [!NOTE]
> Azure Policy サービスは無料です。 詳細については、[Azure Policy の概要](./overview.md)に関するページを参照してください。

Bicep CLI のインストールとファイルの作成が完了したら、次の方法で Bicep ファイルをデプロイできます。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name PolicyDeployment `
  -ResourceGroupName PolicyGroup `
  -TemplateFile assignment.bicep
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --name PolicyDeployment \
  --resource-group PolicyGroup \
  --template-file assignment.bicep
```

---

いくつかのその他のリソース:

- その他のサンプル テンプレートについては、「[Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular)」を参照してください。
- テンプレート リファレンスを確認するには、[Azure テンプレート リファレンス](/azure/templates/microsoft.authorization/allversions)に関するページを参照してください。
- ARM テンプレートを開発する方法については、[Azure Resource Manager のドキュメント](../../azure-resource-manager/management/overview.md)を参照してください。
- サブスクリプション レベルのデプロイについては、「[サブスクリプション レベルでリソース グループとリソースを作成する](../../azure-resource-manager/templates/deploy-to-subscription.md)」を参照してください。

## <a name="validate-the-deployment"></a>デプロイの検証

ページの左側にある **[コンプライアンス]** を選択します。 次に、作成した "_Managed Disks を使用していない VM の監査_" ポリシー割り当てを見つけます。

:::image type="content" source="./media/assign-policy-template/policy-compliance.png" alt-text="ポリシーのコンプライアンスのページにあるコンプライアンスの詳細のスクリーンショット。" border="false":::

この新しい割り当てに準拠していない既存のリソースがある場合、**[準拠していないリソース]** の下に表示されます。

詳細については、「[コンプライアンスのしくみ](./how-to/get-compliance-data.md#how-compliance-works)」を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成した割り当てを削除するには、次の手順のようにします。

1. Azure Policy ページの左側の **[コンプライアンス]** (または **[割り当て]**) を選択し、作成した "_Audit VMs that do not use managed disks\(マネージド ディスクを使用しない VM の監査\)_" ポリシーの割り当てを見つけます。

1. "_マネージド ディスクを使用しない VM の監査_" ポリシー割り当てを右クリックし、**[割り当ての削除]** を選択します。

   :::image type="content" source="./media/assign-policy-template/delete-assignment.png" alt-text="[コンプライアンス] ページからコンテキスト メニューを使用して割り当てを削除するスクリーンショット。" border="false":::

1. `assignment.bicep` ファイルを削除します。

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、組み込みのポリシー定義をスコープに割り当て、コンプライアンス レポートを評価しました。 ポリシー定義では、スコープ内のすべてのリソースが準拠していることが検証されて、準拠していないリソースが識別されます。

新しいリソースが準拠していることを検証するためのポリシーの割り当てについて詳しく学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [ポリシーの作成と管理](./tutorials/create-and-manage.md)