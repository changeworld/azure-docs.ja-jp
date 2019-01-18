---
title: Azure Resource Manager テンプレートの resources | Microsoft Docs
description: 宣言型 JSON 構文を使用した Azure Resource Manager テンプレートの resources セクションについて説明します。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/18/2018
ms.author: tomfitz
ms.openlocfilehash: 5a2b38e5d627341b3684ee55d13ee06881fbae55
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53728365"
---
# <a name="resources-section-of-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートのリソース セクション

resources セクションでは、デプロイまたは更新されるリソースを定義します。 このセクションは、複雑になりやすい部分です。適切な値を指定するためには、デプロイするリソースの種類を理解している必要があるためです。

## <a name="available-properties"></a>利用可能なプロパティ

リソースは、次の構造で定義します。

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "apiVersion": "<api-version-of-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "name": "<name-of-the-resource>",
      "location": "<location-of-resource>",
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "comments": "<your-reference-notes>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| 要素名 | 必須 | 説明 |
|:--- |:--- |:--- |
| condition | いいえ  | このデプロイの間にリソースがプロビジョニングされるかどうかを示すブール値。 `true` の場合、デプロイ時にリソースが作成されます。 `false` の場合、このデプロイでは、リソースはスキップされます。 |
| apiVersion |はい |リソースの作成に使用する REST API バージョン。 |
| type |はい |リソースの種類。 この値は、リソース プロバイダーの名前空間と、リソースの種類の組み合わせです (例: **Microsoft.Storage/storageAccounts**)。 |
| name |はい |リソースの名前。 この名前は、RFC3986 で定義されている URI コンポーネントの制限に準拠する必要があります。 また、リソース名を外部に公開する Azure サービスでは、名前が別の ID になりすますことがないように、その名前を検証します。 |
| location |多様 |指定されたリソースのサポートされている地理的な場所。 利用可能な任意の場所を選択できますが、一般的に、ユーザーに近い場所を選択します。 また、通常、相互に対話するリソースを同じリージョンに配置します。 ほとんどのリソースの種類では場所が必要となりますが、場所を必要としない種類 (ロールの割り当てなど) もあります。 |
| tags |いいえ  |リソースに関連付けられたタグ。 サブスクリプション間でリソースを論理的に編成するためのタグを適用します。 |
| コメント |いいえ  |テンプレート内にドキュメント化するリソースについてのメモ。 |
| copy |いいえ  |複数のインスタンスが必要な場合に作成するリソースの数。 既定のモードはパラレルです。 すべてのリソースを同時にデプロイする必要がない場合は、シリアル モードを指定します。 詳しくは、「[Azure Resource Manager でリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」をご覧ください。 |
| dependsOn |いいえ  |このリソースが配置される前に配置される必要があるリソース。 Resource Manager により、リソース間の依存関係が評価され、リソースが正しい順序でデプロイされます。 相互依存していないリソースは、平行してデプロイされます。 値には、リソース名またはリソースの一意識別子のコンマ区切りリストを指定できます。 このテンプレートに配置されたリソースのみをリストします。 このテンプレートで定義されていないリソースは、既に存在している必要があります。 不要な依存関係は追加しないでください。こうした依存関係によりデプロイの速度が遅くなり、循環依存関係を作成されることがあります。 詳細については、[Azure Resource Manager テンプレートの依存関係の定義](resource-group-define-dependencies.md)に関するページをご覧ください。 |
| properties |いいえ  |リソース固有の構成設定。 properties の値は、リソースを作成するために REST API 操作 (PUT メソッド) の要求本文に指定した値と同じです。 コピー配列を指定して、1 つのプロパティの複数のインスタンスを作成することもできます。 |
| sku | いいえ  | 一部のリソースでは、デプロイする SKU を定義する値が許可されます。 たとえば、ストレージ アカウントの冗長性の種類を指定することができます。 |
| kind | いいえ  | 一部のリソースでは、デプロイするリソースの種類を定義する値が許可されます。 たとえば、作成する Cosmos DB の種類を指定することができます。 |
| プラン | いいえ  | 一部のリソースでは、デプロイするプランを定義する値が許可されます。 たとえば、仮想マシンのマーケットプレース イメージを指定することができます。 | 
| resources |いいえ  |定義されているリソースに依存する子リソース。 親リソースのスキーマで許可されているリソースの種類のみを指定します。 子リソースの完全修飾型には親リソースの種類が含まれます (例: **Microsoft.Web/sites/extensions**)。 親リソースへの依存関係は示されません。 この依存関係は明示的に定義する必要があります。 |

## <a name="condition"></a>条件

デプロイ時にリソースを作成するどうかを決定する必要がある場合は、`condition` 要素を使用します。 この要素の値は、true または false に解決されます。 値が true の場合、リソースが作成されます。 値が false の場合、リソースは作成されません。 この要素の値は、リソース全体にのみ適用できます。

通常、新しいリソースを作成するか、既存のリソースを使用する場合は、この値を使用します。 たとえば、新しいストレージ アカウントをデプロイするか、既存のストレージ アカウントを使用するかを指定するには、次のようにします。

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

`condition` 要素を使用する完全なテンプレート例については、[新規または既存の仮想ネットワーク、ストレージ、およびパブリック IP を使用する VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions) に関するページを参照してください。

## <a name="resource-specific-values"></a>リソース固有の値

**apiVersion**、**type**、および **properties** 要素は、リソースの種類ごとに異なります。 **sku**、**kind**、および **plan** 要素は、すべてではなく一部のリソースの種類だけで使用することができます。 これらのプロパティの値を確認するには、[テンプレート参照](/azure/templates/)をご覧ください。

## <a name="resource-names"></a>リソース名

Resource Manager では、一般に、次の 3 種類のリソース名を使用します。

* 一意である必要があるリソース名
* 一意である必要がないリソース名。ただし、リソースを識別するのに役立つ名前を指定するようにします。
* 一般的なものでよいリソース名

### <a name="unique-resource-names"></a>一意のリソース名

データ アクセス エンドポイントを持つリソースの種類に対しては、一意のリソース名を指定します。 一意の名前にする必要がある一般的なリソースの種類には次のようなものがあります。

* Azure Storage<sup>1</sup> 
* Azure App Service の Web Apps の機能
* SQL Server
* Azure Key Vault
* Azure Cache for Redis
* Azure Batch
* Azure の Traffic Manager
* Azure Search
* Azure HDInsight

<sup>1</sup> ストレージ アカウント名も、小文字で 24 文字以下にする必要があり、ハイフンを含めることはできません。

名前を設定する際には、一意の名前を手動で作成するか、[uniqueString()](resource-group-template-functions-string.md#uniquestring) 関数を使用して名前を生成します。 **uniqueString** の結果に、プレフィックスまたはサフィックスを追加することもできます。 一意の名前を変更すると、リソースの種類を名前から簡単に識別するのに役立ちます。 たとえば、次の変数を使用してストレージ アカウントの一意の名前を生成できます。

```json
"variables": {
    "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

### <a name="resource-names-for-identification"></a>特定しやすいリソース名
リソースの種類には、名前を付けることができるが、一意である必要はないものもあります。 このようなリソースの種類には、リソースのコンテキストとリソースの種類の両方を識別する名前を指定できます。

```json
"parameters": {
    "vmName": { 
        "type": "string",
        "defaultValue": "demoLinuxVM",
        "metadata": {
            "description": "The name of the VM to create."
        }
    }
}
```

### <a name="generic-resource-names"></a>一般的なリソース名
別のリソースからアクセスされることの多いリソースの種類に対しては、テンプレートでハードコーディングされた一般的な名前を使用できます。 たとえば、SQL Server のファイアウォール規則には、標準的で一般的な名前を設定できます。

```json
{
    "type": "firewallrules",
    "name": "AllowAllWindowsAzureIps",
    ...
}
```

## <a name="location"></a>場所
テンプレートをデプロイするときに、各リソースの場所を指定する必要があります。 場所ごとに、異なるリソースの種類がサポートされます。 特定のリソースの種類について、サブスクリプションで利用できる場所の一覧を表示するには、Azure PowerShell または Azure CLI を使用します。 

次の例では、PowerShell を使用して、`Microsoft.Web\sites` リソースの種類の場所を取得しています。

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

次の例では、Azure CLI を使用して、`Microsoft.Web\sites` リソースの種類の場所を取得しています。

```azurecli
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

サポートされるリソースの場所を確認した後は、テンプレートにその場所を設定します。 この値を設定する最も簡単な方法は、リソースの種類をサポートしている場所にリソース グループを作成し、各場所を `[resourceGroup().location]` に設定することです。 テンプレートやパラメーターの値は変更せずに、別の場所のリソース グループにテンプレートを再デプロイすることができます。 

次の例では、リソース グループと同じ場所にデプロイされたストレージ アカウントを示しています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
      "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

テンプレートの場所をハードコーディングする必要がある場合は、サポートされているリージョンのいずれかの名前を指定します。 次の例では、常に米国中北部にデプロイされるストレージ アカウントを示しています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storageloc', uniqueString(resourceGroup().id))]",
      "location": "North Central US",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

## <a name="tags"></a>タグ
[!INCLUDE [resource-manager-governance-tags](../../includes/resource-manager-governance-tags.md)]

### <a name="add-tags-to-your-template"></a>テンプレートにタグを追加する

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="child-resources"></a>子リソース

一部のリソースの種類では、一連の子リソースを定義することができます。 子リソースとは、別のリソースのコンテキスト内でのみ存在するリソースのことです。 たとえば、SQL データベースは SQL サーバーなしでは存在できないので、データベースはサーバーの子であることになります。 データベースはサーバーの定義内に定義することができます。

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  ...
  "resources": [
    {
      "name": "exampledatabase",
      "type": "databases",
      "apiVersion": "2014-04-01",
      ...
    }
  ]
}
```

入れ子にした場合、種類は `databases` に設定されますが、全体的なリソースの種類は `Microsoft.Sql/servers/databases` です。 親リソースの種類から想定されるため、`Microsoft.Sql/servers/` は不要です。 子リソースの名前は `exampledatabase` が設定されていますが、完全な名前には親の名前が含まれています。 親リソースから想定されるため、`exampleserver` は不要です。

子リソースの種類の形式は次の通りです。`{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`

子リソースの名前の形式は次の通りです。`{parent-resource-name}/{child-resource-name}`

ただし、データベースをサーバー内で定義する必要はありません。 最上位レベルで子リソースを定義できます。 親リソースが同じテンプレート内にデプロイされていない場合、または複数の子リソースを作成するために `copy` を使う場合は、このアプローチを使用することがあります。 このアプローチの場合、完全なリソースの種類を指定する必要があり、子リソースの名前に親のリソースの名前を含める必要があります。

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  "resources": [ 
  ],
  ...
},
{
  "name": "exampleserver/exampledatabase",
  "type": "Microsoft.Sql/servers/databases",
  "apiVersion": "2014-04-01",
  ...
}
```

リソースに対する完全修飾参照を作成する場合、種類と名前からセグメントを結合する順序は、単に 2 つの連結ではありません。 名前空間の後に、"*種類/名前*" のペアを具体性の低いものから高いものへの順に使用します。

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

例: 

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt`は正しい`Microsoft.Compute/virtualMachines/extensions/myVM/myExt`は正しくない



## <a name="next-steps"></a>次の手順
* さまざまな種類のソリューションのテンプレートについては、「 [Azure クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)」をご覧ください。
* テンプレート内から使用できる関数の詳細については、「 [Azure Resource Manager テンプレートの関数](resource-group-template-functions.md)」を参照してください。
* テンプレート作成の推奨事項については、「[Azure Resource Manager テンプレートのベスト プラクティス](template-best-practices.md)」を参照してください。
* 別のリソース グループ内に存在するリソースの使用が必要になる場合があります。 このシナリオは、複数のリソース グループ間で共有されているストレージ アカウントまたは仮想ネットワークを使用している場合は一般的です。 詳細については、 [resourceId 関数](resource-group-template-functions-resource.md#resourceid)に関するセクションをご覧ください。
* リソース名の制限事項については、「 [Azure リソースの推奨される名前付け規則](../guidance/guidance-naming-conventions.md)」を参照してください。