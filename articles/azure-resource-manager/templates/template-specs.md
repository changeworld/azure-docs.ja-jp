---
title: テンプレート スペックの概要
description: テンプレート スペックを作成し、組織内の他のユーザーと共有する方法について説明します。
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 47dcf44b35ad5c0b77dd0b88d683071a7f2f4ecb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87094607"
---
# <a name="azure-resource-manager-template-specs-preview"></a>Azure Resource Manager テンプレート スペック (プレビュー)

テンプレート スペックは、後でデプロイするために Azure に Azure Resource Manager テンプレート (ARM テンプレート) を格納するための新しいリソースの種類です。 このリソースの種類を使用すると、ARM テンプレートを組織内の他のユーザーと共有できます。 他の Azure リソースと同じように、ロールベースのアクセス制御 (RBAC) を使用してテンプレート スペックを共有できます。ユーザーがテンプレートをデプロイするのに必要なのは、そのテンプレート スペックへの読み取りアクセス権のみです。そのため、他のユーザーに変更を許可することなくテンプレートを共有できます。

**Microsoft.Resources/templateSpecs** は、テンプレート スペックの新しいリソースの種類です。 これは、メイン テンプレートと、リンクされた任意の数のテンプレートで構成されます。 Azure によって、テンプレート スペックはリソース グループに安全に格納されます。 Template Specs では、[バージョン管理](#versioning)がサポートされています。

テンプレート スペックをデプロイするには、PowerShell、Azure CLI、Azure portal、REST およびその他のサポートされている SDK およびクライアントなど、標準の Azure ツールを使用します。 同じコマンドを使用し、テンプレートに同じパラメーターを渡します。

テンプレート スペックを使用する利点は、組織内のチームが一般的なシナリオでテンプレートを作成し直したりコピーしたりする必要がないことです。 標準的なテンプレートを作成し、共有します。 テンプレート スペックに含まれるテンプレートは、組織の要件とガイダンスに従うように組織の管理者が検証する必要があります。

> [!NOTE]
> Template Specs は現在プレビューの段階です。 使用するには、[待機リストにサインアップする](https://aka.ms/templateSpecOnboarding)必要があります。

## <a name="create-template-spec"></a>テンプレート スペックを作成する

次の例は、Azure でストレージ アカウントを作成するための簡単なテンプレートを示しています。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "location": "[resourceGroup().location]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ]
}
```

テンプレート スペックを作成するときは、PowerShell コマンドまたは CLI コマンドにメイン テンプレート ファイルが渡されます。 メイン テンプレートがリンクされたテンプレートを参照している場合は、コマンドでそれらのテンプレートが検索されてパッケージ化され、テンプレート スペックが作成されます。詳細については、「[リンクされたテンプレートを使用してテンプレート スペックを作成する](#create-a-template-spec-with-linked-templates)」を参照してください。

テンプレート スペックを作成するには、次のコマンドを使用します。

```azurepowershell
New-AzTemplateSpec -Name storageSpec -Version 1.0 -ResourceGroupName templateSpecsRg -TemplateJsonFile ./mainTemplate.json
```

サブスクリプション内のすべてのテンプレート スペックを表示するには、次のコマンドを使用します。

```azurepowershell
Get-AzTemplateSpec
```

テンプレート スペックの詳細 (そのバージョンを含む) を表示するには、次のコマンドを使用します。

```azurepowershell
Get-AzTemplateSpec -ResourceGroupName templateSpecsRG -Name storageSpec
```

## <a name="deploy-template-spec"></a>テンプレート スペックをデプロイする

テンプレート スペックを作成すると、そのテンプレート スペックへの**読み取り**アクセス権を持つユーザーは、それをデプロイできます。 アクセスの許可については、「[チュートリアル: Azure PowerShell を使用して Azure リソースへのアクセス権をグループに付与する](../../role-based-access-control/tutorial-role-assignments-group-powershell.md)」を参照してください。

Template Specs は、ポータル、PowerShell、Azure CLI を通じて、または大規模なテンプレートのデプロイでリンクされたテンプレートとしてデプロイできます。 組織内のユーザーは、Azure 内の任意のスコープ (リソース グループ、サブスクリプション、管理グループ、またはテナント) にテンプレート スペックをデプロイできます。

テンプレートのパスまたは URI を渡す代わりに、リソース ID を指定してテンプレート スペックをデプロイします。 リソース ID の形式は次のとおりです。

**/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Resources/templateSpecs/{template-spec-name}/versions/{template-spec-version}**

リソース ID にテンプレート スペックのバージョン番号が含まれていることに注目してください。

たとえば、次の PowerShell コマンドを使用してテンプレート スペックをデプロイします。

```azurepowershell
$id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0"

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

実際には、通常は `Get-AzTemplateSpec` を実行して、デプロイするテンプレート スペックの ID を取得します。

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0).Version.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

## <a name="create-a-template-spec-with-linked-templates"></a>リンクされたテンプレートを使用してテンプレート スペックを作成する

テンプレート スペックのメイン テンプレートがリンクされたテンプレートを参照している場合は、PowerShell コマンドと CLI コマンドで、リンクされたテンプレートをローカル ドライブから自動的に検索してパッケージ化することができます。 テンプレート スペックをホストするために、ストレージ アカウントまたはリポジトリを手動で構成する必要はありません。テンプレート スペック リソースにすべて含まれています。

次の例は、メイン テンプレートと、リンクされた 2 つのテンプレートで構成されています。 この例は、テンプレートの抜粋にすぎません。 `relativePath` という名前のプロパティを使用して、他のテンプレートにリンクしていることに注目してください。 デプロイ リソースには、`2020-06-01` 以降の `apiVersion` を使用する必要があります。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    ...
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            ...
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "relativePath": "artifacts/webapp.json"
                }
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            ...
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "relativePath": "artifacts/database.json"
                }
            }
        }
    ],
    "outputs": {}
}

```

前の例で、テンプレート スペックを作成する PowerShell コマンドまたは CLI コマンドを実行すると、メイン テンプレート、Web アプリ テンプレート (`webapp.json`)、およびデータベース テンプレート (`database.json`) の 3 つのファイルが検索され、テンプレート スペックにパッケージ化されます。

詳細については、[リンクされたテンプレートを使用してテンプレート スペックを作成する](template-specs-create-linked.md)」を参照してください。

## <a name="deploy-template-spec-as-a-linked-template"></a>テンプレート スペックをリンクされたテンプレートとしてデプロイする

テンプレート スペックを作成すると、ARM テンプレートや別のテンプレート スペックから簡単に再利用できます。テンプレートにリソース ID を追加して、テンプレート スペックにリンクします。 リンクされたテンプレート スペックは、メイン テンプレートのデプロイ時に自動的にデプロイされます。 この動作により、モジュール テンプレート スペックを開発し、必要に応じて再利用することができます。

たとえば、ネットワーク リソースをデプロイするテンプレート スペックと、ストレージ リソースをデプロイする別のテンプレート スペックを作成することができます。 ARM テンプレートでは、ネットワーク リソースまたはストレージ リソースを構成する必要があるときはいつでも、これらの 2 つのテンプレート スペックにリンクします。

次の例は、前の例と似ていますが、ローカル テンプレートにリンクする `relativePath` プロパティではなく、テンプレート スペックにリンクする `id` プロパティを使用します。 デプロイ リソースの API バージョンには `2020-06-01` を使用します。 この例では、テンプレート スペックは **templateSpecsRG** という名前のリソース グループにあります。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    ...
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "networkingDeployment",
            ...
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'networkingSpec', '1.0')]"
                }
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "storageDeployment",
            ...
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
                }
            }
        }
    ],
    "outputs": {}
}
```

テンプレート スペックのリンクの詳細については、「[チュートリアル: テンプレート スペックをリンクされたテンプレートとしてデプロイする](template-specs-deploy-linked-template.md)」を参照してください。

## <a name="versioning"></a>バージョン管理

テンプレート スペックを作成するときは、そのバージョン番号を指定します。 テンプレート コードを反復処理するときは、既存のバージョンを更新する (修正プログラムの場合) か、または新しいバージョンを発行することができます。 バージョンはテキスト文字列です。 セマンティック バージョン管理など、任意のバージョン管理システムに従うことを選択できます。 テンプレート スペックのユーザーは、デプロイ時に使用するバージョン番号を指定できます。

## <a name="next-steps"></a>次のステップ

* テンプレート スペックを作成してデプロイするには、「[クイックスタート: テンプレート スペックの作成とデプロイ](quickstart-create-template-specs.md)」を参照してください。

* テンプレート スペックでテンプレートをリンクする方法の詳細については、「[チュートリアル: リンクされたテンプレートを使用してテンプレート スペックを作成する](template-specs-create-linked.md)」を参照してください。

* テンプレート スペックをリンクされたテンプレートとしてデプロイする方法の詳細については、「[チュートリアル: テンプレート スペックをリンクされたテンプレートとしてデプロイする](template-specs-deploy-linked-template.md)」を参照してください。
