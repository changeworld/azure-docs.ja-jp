---
title: Azure Policy リソースをエクスポートする
description: ポリシー定義やポリシーの割り当てなど、Azure Policy のリソースを GitHub にエクスポートするやり方を学習します。
ms.date: 10/29/2020
ms.topic: how-to
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: c16ceed755cab3228b8f9e401f486a0629f3a60d
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/29/2020
ms.locfileid: "93025716"
---
# <a name="export-azure-policy-resources"></a>Azure Policy リソースをエクスポートする

この記事では、既存の Azure Policy リソースをエクスポートする方法について説明します。 リソースのエクスポートは、バックアップには便利で推奨されますが、クラウド ガバナンスおよび [コードとしてのポリシー](../concepts/policy-as-code.md)の扱いに関する重要な手順でもあります。 Azure Policy リソースは、 [Azure portal](#export-with-azure-portal)、 [Azure CLI](#export-with-azure-cli)、 [Azure PowerShell](#export-with-azure-powershell)、およびサポートされている各 SDK を使用してエクスポートできます。

## <a name="export-with-azure-portal"></a>Azure portal を使用したエクスポート

Azure portal からポリシー定義をエクスポートするには、次の手順を実行します。

1. Azure portal 上で **[すべてのサービス]** をクリックし、 **[ポリシー]** を検索して選択し、Azure Policy サービスを起動します。

1. Azure Policy ページの左側にある **[Definitions (定義)]** を選択します。

1. **[Export definitions (定義のエクスポート)]** ボタンを使用するか、ポリシー定義の行の省略記号を選択してから、 **[Export definition (定義のエクスポート)]** を選択し ます。

1. **[GitHub でサインイン]** ボタンを選択します。 リソースをエクスポートする Azure Policy を承認するために GitHub で認証されていない場合は、表示される新しいウィンドウで [GitHub Action](https://github.com/features/actions) にアクセスし、 **[Authorize AzureGitHubActions (AzureGitHubActions を承認する)]** を選択して、エクスポート プロセスを続行します。 完了すると、新しいウィンドウが自動的に閉じます。

1. **[Basics (基本)]** タブで、次のオプションを設定し、 **[Policy (ポリシー)]** タブまたは **[Next (次へ): Policies (ポリシー)]** ボタンがページの下部にあるので選択します。

   - **リポジトリ フィルター** :[ _My repositories (マイ リポジトリ)_ ] に設定すると、自分が所有しているリポジトリのみ、または _すべてのリポジトリ_ を表示できます。これにより、GitHub アクションへのアクセスが許可されたすべてのリポジトリが表示されます。
   - **リポジトリ** :Azure Policy リソースをエクスポートするリポジトリを設定します。
   - **ブランチ** :リポジトリにブランチを設定します。 既定以外のブランチを使用すると、ソースコードにマージする前に更新を検証することをお勧めします。
   - **ディレクトリ** :Azure Policy リソースをエクスポートする _ルート レベル フォルダー_ 。 このディレクトリの下のサブフォルダーは、エクスポートされるリソースに基づいて作成されます。

1. **[Policies (ポリシー)]** タブで、省略記号を選択し、管理グループ、サブスクリプション、またはリソース グループの組み合わせを選択して、検索範囲を設定します。
   
1. **[Add policy definition(s) (ポリシー定義の追加)]** ボタンを使用して、エクスポートするオブジェクトのスコープを検索します。 開いたサイド ウィンドウで、エクスポートする各オブジェクトを選択します。 検索ボックスまたは種類によって選択をフィルター処理します。 エクスポートするオブジェクトをすべて選択したら、ページの下部にある **[Add (追加)]** ボタンを使用します。

1. 選択した各オブジェクトについて、ポリシー定義の [ _Only Definition (定義のみ)_ ] または [ _Definition and Assignment(s) (定義と割り当て)_ ] などの目的のエクスポート オプションを選択し ます。 次に、 **[Review + Export (確認 + エクスポート)]** タブまたは **[Next: (次へ)Review + Export (確認 + エクスポート)]** ボタンがページ下部にあるので、これを選択します。

   > [!NOTE]
   > オプション [ _Definition and Assignment(s) (定義と割り当て)]_ が選択されている場合、ポリシー定義の追加時にフィルターによって設定されたスコープ内のポリシー割り当てのみがエクスポートされます。

1. **[Review + Export (確認 + エクスポート)]** タブで、詳細が一致していることを確認し、ページの下部にある **[Export (エクスポート)]** ボタンを使用します。

1. GitHub リポジトリ、ブランチ、 _ルート レベルのフォルダー_ を確認して、選択したリソースがソース管理にエクスポートされていることを確認します。

Azure Policy リソースは、選択した GitHub リポジトリと _ルートレベル フォルダー_ 内の次の構造にエクスポートされ ます。

```text
|
|- <root level folder>/  ________________ # Root level folder set by Directory property
|  |- policies/  ________________________ # Subfolder for policy objects
|     |- <displayName>_<name>____________ # Subfolder based on policy displayName and name properties
|        |- policy.json _________________ # Policy definition
|        |- assign.<displayName>_<name>__ # Each assignment (if selected) based on displayName and name properties
|
```

## <a name="export-with-azure-cli"></a>Azure CLI を使用したエクスポート

Azure Policy の定義、イニシアチブ、および割り当ては、 [Azure CLI](/cli/azure/install-azure-cli)を使用して JSON としてエクスポートできます。 これらの各コマンドは、 **name** パラメーターを使用して、JSON を取得するオブジェクトを指定します。 **name** プロパティは、多くの場合、 _GUID_ であり、オブジェクトの **displayName** ではありません。

- 定義 - [az policy definition show](/cli/azure/policy/definition#az-policy-definition-show)
- イニシアチブ - [az policy set-definition show](/cli/azure/policy/set-definition#az-policy-set-definition-show)
- 割り当て - [az policy assignment show](/cli/azure/policy/assignment#az-policy-assignment-show)

_VirtualMachineStorage_ の **名前** を使用して、ポリシー定義の JSON を取得する例を次に示します。

```azurecli-interactive
az policy definition show --name 'VirtualMachineStorage'
```

## <a name="export-with-azure-powershell"></a>Azure PowerShell を使用したエクスポート

Azure Policy の定義、イニシアチブ、および割り当ては、 [Azure PowerShell](/powershell/azure/)を使用して JSON としてエクスポートできます。 これらの各コマンドレットは、 **Name** パラメーターを使用して、JSON を取得するオブジェクトを指定します。 **Name** プロパティは、多くの場合、 _GUID_ であり、オブジェクトの **displayName** ではありません。

- 定義 - [Get-AzPolicyDefinition](/powershell/module/az.resources/get-azpolicydefinition)
- イニシアチブ - [Get-AzPolicySetDefinition](/powershell/module/az.resources/get-azpolicysetdefinition)
- 割り当て - [Get-AzPolicyAssignment](/powershell/module/az.resources/get-azpolicyassignment)

_VirtualMachineStorage_ の **名前** を使用して、ポリシー定義の JSON を取得する例を次に示します。

```azurepowershell-interactive
Get-AzPolicyDefinition -Name 'VirtualMachineStorage' | ConvertTo-Json -Depth 10
```

## <a name="next-steps"></a>次のステップ

- [Azure Policy のサンプル](../samples/index.md)を確認します。
- 「[Azure Policy の定義の構造](../concepts/definition-structure.md)」を確認します。
- 「[Policy の効果について](../concepts/effects.md)」を確認します。
- [プログラムによってポリシーを作成する](programmatically-create.md)方法を理解します。
- [準拠していないリソースを修復する](remediate-resources.md)方法を学習します。
- 「[Azure 管理グループのリソースを整理する](../../management-groups/overview.md)」で、管理グループとは何かを確認します。
