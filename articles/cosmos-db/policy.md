---
title: Azure Policy を使用して Azure Cosmos DB リソースのガバナンスとコントロールを実装する
description: Azure Policy を使用して Azure Cosmos DB リソースのガバナンスとコントロールを実装する方法について説明します。
author: plzm
ms.author: paelaz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 2249dbdebecc52a8f5d6decccb83d3b1fc0777f7
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747374"
---
# <a name="use-azure-policy-to-implement-governance-and-controls-for-azure-cosmos-db-resources"></a>Azure Policy を使用して Azure Cosmos DB リソースのガバナンスとコントロールを実装する

[Azure Policy](../governance/policy/overview.md) は、組織のガバナンス標準の実施、リソースのコンプライアンスの評価、自動修復の実装を支援します。 一般的なユースケースには、セキュリティ、コスト管理、構成の一貫性などがあります。

Azure Policy には組み込みポリシー定義が用意されています。 組み込みポリシー定義では対応できないシナリオには、カスタムのポリシー定義を作成できます。 詳細については、[Azure Policy のドキュメント](../governance/policy/overview.md)を参照してください。

## <a name="assign-a-built-in-policy-definition"></a>組み込みポリシー定義の割り当て

ポリシー定義には、リソースのコンプライアンス条件と、条件が満たされた場合に実行する効果が記述されます。 ポリシー "_割り当て_" は、ポリシー "_定義_" から作成されます。 Azure Cosmos DB リソースには、組み込みまたはカスタムのポリシー定義を使用できます。 ポリシー割り当ては、スコープが Azure 管理グループ、Azure サブスクリプション、リソース グループのいずれかに設定され、選択されたスコープ内のリソースに適用されます。 必要に応じて、スコープから特定のリソースを除外できます。

ポリシー割り当ては、[Azure portal](../governance/policy/assign-policy-portal.md)、[Azure PowerShell](../governance/policy/assign-policy-powershell.md)、[Azure CLI](../governance/policy/assign-policy-azurecli.md)、または [ARM テンプレート](../governance/policy/assign-policy-template.md)で作成できます。

Azure Cosmos DB に対して組み込みポリシー定義からポリシー割り当てを作成するには、[Azure portal でのポリシー割り当ての作成](../governance/policy/assign-policy-portal.md)に関する記事の手順に従ってください。

ポリシー定義を選択する手順で、[検索] フィールドに「`Cosmos DB`」と入力して、使用可能な組み込みポリシー定義の一覧をフィルター処理します。 使用可能な組み込みポリシー定義の 1 つを選択し、 **[選択]** を選んでポリシー割り当ての作成を進めます。

> [!TIP]
> **[Available Definitions]\(使用可能な定義\)** ペインに表示される組み込みポリシー定義名を、Azure PowerShell、Azure CLI、または ARM テンプレートで使用してポリシー割り当てを作成することもできます。

:::image type="content" source="./media/policy/available-definitions.png" alt-text="Azure Cosmos DB の組み込みポリシー定義の検索":::

## <a name="create-a-custom-policy-definition"></a>カスタム ポリシー定義の作成

組み込みポリシーで対応できない特定のシナリオには、[カスタム ポリシー定義](../governance/policy/tutorials/create-custom-policy-definition.md)を作成できます。 この後に、カスタム ポリシー "_定義_" からポリシー "_割り当て_" を作成します。

### <a name="property-types-and-property-aliases-in-policy-rules"></a>ポリシー ルールのプロパティの種類とプロパティ エイリアス

[カスタム ポリシー定義の手順](../governance/policy/tutorials/create-custom-policy-definition.md)に従って、ポリシー ルールの作成に必要なリソース プロパティとプロパティ エイリアスを特定します。

Azure Cosmos DB 固有のプロパティ エイリアスを特定するには、カスタム ポリシー定義の手順の記事に示されているいずれかの方法で名前空間 `Microsoft.DocumentDB` を使用します。

#### <a name="use-the-azure-cli"></a>Azure CLI を使用する場合:
```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for namespace Microsoft.DocumentDB
az provider show --namespace Microsoft.DocumentDB --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

#### <a name="use-azure-powershell"></a>Azure PowerShell を使用する場合:
```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.DocumentDB namespace
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.DocumentDB').Aliases
```

これらのコマンドによって、Azure Cosmos DB プロパティのプロパティ エイリアス名の一覧が出力されます。 この出力の抜粋を次に示します。

```json
[
  "Microsoft.DocumentDB/databaseAccounts/sku.name",
  "Microsoft.DocumentDB/databaseAccounts/virtualNetworkRules[*]",
  "Microsoft.DocumentDB/databaseAccounts/virtualNetworkRules[*].id",
  "Microsoft.DocumentDB/databaseAccounts/isVirtualNetworkFilterEnabled",
  "Microsoft.DocumentDB/databaseAccounts/consistencyPolicy.defaultConsistencyLevel",
  "Microsoft.DocumentDB/databaseAccounts/enableAutomaticFailover",
  "Microsoft.DocumentDB/databaseAccounts/Locations",
  "Microsoft.DocumentDB/databaseAccounts/Locations[*]",
  "Microsoft.DocumentDB/databaseAccounts/Locations[*].locationName",
  "..."
]
```

[カスタム ポリシー定義のルール](../governance/policy/tutorials/create-custom-policy-definition.md#policy-rule)では、これらのプロパティ エイリアス名をどれでも使用できます。

次に示すポリシー定義の例では、Azure Cosmos DB SQL データベースのプロビジョニング スループットが最大許容値の 400 RU/秒を超えているかどうかをチェックします。 カスタム ポリシー定義には 2 つのルールが含まれています。1 つは特定の種類のプロパティ エイリアスをチェックするルールで、もう 1 つはその種類の特定のプロパティをチェックするルールです。 どちらのルールもエイリアス名を使用します。

```json
"policyRule": {
  "if": {
    "allOf": [
      {
      "field": "type",
      "equals": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings"
      },
      {
      "field": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/default.resource.throughput",
      "greater": 400
      }
    ]
  }
}
```

組み込みポリシー定義を使用する場合と同様に、カスタム ポリシー定義を使用してポリシー割り当てを作成することができます。

## <a name="policy-compliance"></a>ポリシーのコンプライアンス

ポリシー割り当てを作成すると、Azure Policy によって割り当てのスコープ内のリソースが評価されます。 ポリシーに対する各リソースの "_コンプライアンス_" が評価されます。 準拠していないリソースには、ポリシーで指定されている "_効果_" が適用されます。

コンプライアンスの結果と修復の詳細は、[Azure portal](../governance/policy/how-to/get-compliance-data.md#portal)、[Azure CLI](../governance/policy/how-to/get-compliance-data.md#command-line)、または [Azure Monitor ログ](../governance/policy/how-to/get-compliance-data.md#azure-monitor-logs)で確認できます。

次のスクリーンショットは、2 つのポリシー割り当ての例を示しています。 一方の割り当ては組み込みポリシー定義に基づいており、Azure Cosmos DB リソースが許可された Azure リージョンのみにデプロイされているかをチェックします。 もう一方の割り当ては、カスタム ポリシー定義に基づいています。 この割り当ては、Azure Cosmos DB リソースのプロビジョニング スループットが指定された上限を超えていないかをチェックします。

ポリシー割り当てが展開されると、コンプライアンス ダッシュボードに評価結果が表示されます。 これには、ポリシー割り当てを展開してから最大で 30 分かかる場合があることに注意してください。

スクリーンショットに示されているコンプライアンスの評価結果は次のとおりです。

- 指定したスコープにある 1 個の Azure Cosmos DB アカウントのうち 0 個が、許可されたリージョンにリソースがデプロイされたことをチェックするポリシー割り当てに準拠しています。
- 指定したスコープにある 2 個の Azure Cosmos DB データベース リソースまたはコレクション リソースのうち 1 個が、指定の上限を超えているプロビジョニング スループットをチェックするポリシー割り当てに準拠しています。

:::image type="content" source="./media/policy/compliance.png" alt-text="Azure Cosmos DB の組み込みポリシー定義の検索":::

準拠していないリソースの修復方法については、[Azure Policy での修復](../governance/policy/how-to/remediate-resources.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB 用のカスタム ポリシー定義の例を確認する](https://github.com/Azure/azure-policy/tree/master/samples/CosmosDB)
- [Azure portal でポリシー割り当てを作成する](../governance/policy/assign-policy-portal.md)
- [Azure Cosmos DB 用の Azure Policy 組み込みポリシー定義を確認する](./policy-samples.md)
