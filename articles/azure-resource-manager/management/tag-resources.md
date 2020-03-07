---
title: 論理的な組織のリソースにタグを付ける
description: タグを適用して、課金や管理のために Azure リソースを整理する方法を示します。
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: c7f8d8672e205fa677bff33c8ed173c1105b26c6
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357341"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>タグを使用した Azure リソースの整理

Azure リソースにタグを適用して、分類へと論理的に整理できます。 各タグは、名前と値のペアで構成されます。 たとえば、運用環境のすべてのリソースには名前 "環境" と値 "運用" を適用できます。

タグを適用すると、そのタグの名前と値が付けられた、サブスクリプション内のすべてのリソースを取得できます。 タグを使用すると、複数のリソース グループから関連リソースを取得できます。 この方法は、課金または管理の目的でリソースを整理する必要がある場合に役立ちます。

分類では、ユーザーの負担を軽減し、精度を向上させるために、自動タグ付け方法に加えてセルフサービスのメタデータ タグ付け方法を考慮する必要があります。

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="limitations"></a>制限事項

タグには次の制限事項が適用されます。

* すべてのリソースの種類で、タグがサポートされるわけではありません。 リソースの種類にタグを適用することができるかどうかを確認するには、[Azure リソースに対するタグのサポート](tag-support.md)に関する記事を参照してください。
* 各リソースまたはリソース グループには、最大で 50 個のタグ名/タグ値のペアを付けることができます。 許可される最大数よりも多くのタグを適用する必要がある場合は、タグ値に JSON 文字列を使用します。 JSON 文字列には、1 つのタグ名に適用される値を多数含めることができます。 リソース グループには、それぞれ 50 個のタグ名/タグ値のペアが付けられたリソースを多数含めることができます。
* タグ名は 512 文字まで、タグ値は 256 文字までに制限されます。 ストレージ アカウントについては、タグ名は 128 文字まで、タグ値は 256 文字までに制限されます。
* 一般化された VM では、タグがサポートされていません。
* リソース グループに適用したタグは、そのリソース グループ内のリソースには継承されません。
* Cloud Services など、クラシック リソースにタグを適用することはできません。
* タグ名には、これらの文字を含めることはできません: `<`、`>`、`%`、`&`、`\`、`?`、`/`

   > [!NOTE]
   > また、Azure DNS ゾーンと Traffic Manger サービスでは現在、タグ内でスペースを使用することはできません。 

## <a name="required-access"></a>必要なアクセス

リソースにタグを適用するには、ユーザーがそのリソースの種類への書き込みアクセス権を保持している必要があります。 すべてのリソースの種類にタグを適用するには、[Contributor](../../role-based-access-control/built-in-roles.md#contributor) ロールを使用します。 1 つのリソースの種類だけにタグを適用するには、そのリソースの共同作成者ロールを使用します。 たとえば、仮想マシンにタグを適用するには、[仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)を使用します。

## <a name="policies"></a>ポリシー

[Azure Policy](../../governance/policy/overview.md) を使用して、タグ付けのルールと規則を強制できます。 ポリシーを作成することで、組織の必要なタグに準拠しないサブスクリプションにリソースがデプロイされるというシナリオを回避します。 タグの適用や非準拠リソースの検索を手動で行う代わりに、デプロイ時に必要なタグを自動的に適用するポリシーを作成できます。 また、新しい [Modify](../../governance/policy/concepts/effects.md#modify) 効果と[修復タスク](../../governance/policy/how-to/remediate-resources.md)を使用して、既存のリソースにタグを適用することもできるようになりました。 次のセクションでは、タグのポリシーの例を示します。

[!INCLUDE [Tag policies](../../../includes/azure-policy-samples-policies-tags.md)]

## <a name="powershell"></a>PowerShell

*リソース グループ*の既存のタグを表示するには、次のコマンドを使用します。

```azurepowershell-interactive
(Get-AzResourceGroup -Name examplegroup).Tags
```

このスクリプトは次の形式を返します。

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

*特定の名前とリソース グループに該当するリソース*の既存のタグを表示するには、次のコマンドを使用します。

```azurepowershell-interactive
(Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

または、リソースのリソース ID がある場合は、そのリソース ID を渡してタグを取得できます。

```azurepowershell-interactive
(Get-AzResource -ResourceId /subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>).Tags
```

*特定のタグ名と値を持つリソース グループ*を取得するには、次のコマンドを使用します。

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "Dept"="Finance" }).ResourceGroupName
```

*特定のタグ名と値を持つリソース*を取得するには、次のコマンドを使用します。

```azurepowershell-interactive
(Get-AzResource -Tag @{ "Dept"="Finance"}).Name
```

*特定のタグ名が付いたリソース*を取得するには、次のコマンドを使用します。

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

リソースまたはリソース グループにタグを適用するたびに、そのリソースまたはリソース グループの既存のタグが上書きされます。 したがって、リソースまたはリソース グループに既存のタグがあるかどうかに基づいて、異なるアプローチを使用する必要があります。

*既存のタグのないリソース グループ*にタグを追加するには、次のコマンドを使用します。

```azurepowershell-interactive
Set-AzResourceGroup -Name examplegroup -Tag @{ "Dept"="IT"; "Environment"="Test" }
```

*既存のタグがあるリソース グループ*にタグを追加するには、既存のタグを取得して新しいタグを追加し、タグを適用し直します。

```azurepowershell-interactive
$tags = (Get-AzResourceGroup -Name examplegroup).Tags
$tags.Add("Status", "Approved")
Set-AzResourceGroup -Tag $tags -Name examplegroup
```

*既存のタグのないリソース*にタグを追加するには、次のコマンドを使用します。

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzResource -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $resource.ResourceId -Force
```

1 つのリソース グループに同じ名前のリソースが複数存在する場合があります。 その場合は、次のコマンドを使用して各リソースを設定できます。

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Set-AzResource -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Force }
```

*既存のタグがあるリソース*にタグを追加するには、次のコマンドを使用します。

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
$resource.Tags.Add("Status", "Approved")
Set-AzResource -Tag $resource.Tags -ResourceId $resource.ResourceId -Force
```

"*リソース上にある既存のタグを保持せずに*"、リソース グループのすべてのタグをそのリソースに適用するには、次のスクリプトを使用します。

```azurepowershell-interactive
$group = Get-AzResourceGroup -Name examplegroup
Get-AzResource -ResourceGroupName $group.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.ResourceId -Tag $group.Tags -Force }
```

"*リソース上にある重複しない既存のタグを保持したうえで*"、リソース グループのすべてのタグをそのリソースに適用するには、次のスクリプトを使用します。

```azurepowershell-interactive
$group = Get-AzResourceGroup -Name examplegroup
if ($null -ne $group.Tags) {
    $resources = Get-AzResource -ResourceGroupName $group.ResourceGroupName
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzResource -ResourceId $r.ResourceId).Tags
        if ($resourcetags)
        {
            foreach ($key in $group.Tags.Keys)
            {
                if (-not($resourcetags.ContainsKey($key)))
                {
                    $resourcetags.Add($key, $group.Tags[$key])
                }
            }
            Set-AzResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
        else
        {
            Set-AzResource -Tag $group.Tags -ResourceId $r.ResourceId -Force
        }
    }
}
```

すべてのタグを削除するには、空のハッシュ テーブルを渡します。

```azurepowershell-interactive
Set-AzResourceGroup -Tag @{} -Name examplegroup
```

## <a name="azure-cli"></a>Azure CLI

*リソース グループ*の既存のタグを表示するには、次のコマンドを使用します。

```azurecli-interactive
az group show -n examplegroup --query tags
```

このスクリプトは次の形式を返します。

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

*特定の名前、型、リソース グループに該当するリソース*の既存のタグを表示するには、次のコマンドを使用します。

```azurecli-interactive
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

リソースのコレクション内をループ処理するときに、リソース ID ごとにリソースを表示したいことがあります。 完成した例については、この記事で後ほど紹介します。 *特定のリソース ID に該当するリソース*の既存のタグを表示するには、次のコマンドを使用します。

```azurecli-interactive
az resource show --id <resource-id> --query tags
```

特定のタグが付いたリソース グループを取得するには、`az group list` を使用します。

```azurecli-interactive
az group list --tag Dept=IT
```

特定のタグと値を持つすべてのリソースを取得するには、`az resource list` を使用します。

```azurecli-interactive
az resource list --tag Dept=Finance
```

リソース グループまたはリソースにタグを追加する場合は、既存のタグを上書きするか、既存のタグに新しいタグを追加することができます。

リソース グループの既存のタグを上書きするには、次のコマンドを使用します。

```azurecli-interactive
az group update -n examplegroup --tags 'Environment=Test' 'Dept=IT'
```

リソース グループの既存のタグにタグを追加するには、次のコマンドを使用します。

```azurecli-interactive
az group update -n examplegroup --set tags.'Status'='Approved'
```

リソースのタグを上書きするには、次のコマンドを使用します。

```azurecli-interactive
az resource tag --tags 'Dept=IT' 'Environment=Test' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

リソースの既存のタグにタグを追加するには、次のコマンドを使用します。

```azurecli-interactive
az resource update --set tags.'Status'='Approved' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

"*リソース上にある既存のタグを保持せずに*"、リソース グループのすべてのタグをそのリソースに適用するには、次のスクリプトを使用します。

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '"{},' | sed 's/: /=/g')
resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  az resource tag --tags $tags --id $id
done
```

"*リソースにある既存のタグを保持したうえで*"、リソース グループのすべてのタグをそのリソースに適用するには、次のスクリプトを使用します。

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '"{},' | sed 's/: /=/g')

resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  resourcejsontags=$(az resource show --id $id --query tags -o json)
  resourcetags=$(echo $resourcejsontags | tr -d '"{},' | sed 's/: /=/g')
  az resource tag --tags $tags$resourcetags --id $id
done
```

タグの名前または値にスペースが含まれている場合は、いくつかの追加手順を実行する必要があります。 次の例では、タグにスペースが含まれている場合に、リソース グループのすべてのタグをそのリソースに適用します。

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '{}"' | sed 's/: /=/g' | sed "s/\"/'/g" | sed 's/, /,/g' | sed 's/ *$//g' | sed 's/^ *//g')
origIFS=$IFS
IFS=','
read -a tagarr <<< "$tags"
resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  az resource tag --tags "${tagarr[@]}" --id $id
done
IFS=$origIFS
```

## <a name="templates"></a>テンプレート

デプロイ中にリソースにタグを付けるには、デプロイするリソースに `tags` 要素を追加します。 タグの名前と値を指定します。

### <a name="apply-a-literal-value-to-the-tag-name"></a>タグ名へのリテラル値の適用

次の例は、2 つのタグ (`Dept` と `Environment`) をリテラル値に設定するストレージ アカウントを示しています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "Finance",
                "Environment": "Production"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

タグに datetime 値を設定するには、[utcNow 関数](../templates/template-functions-string.md#utcnow)を使用します。

### <a name="apply-an-object-to-the-tag-element"></a>タグ要素へのオブジェクトの適用

複数のタグを格納するオブジェクト パラメーターを定義し、そのオブジェクトをタグ要素に適用できます。 オブジェクト内の各プロパティがリソースの個々のタグになります。 次の例は、タグ要素に適用される `tagValues` という名前のパラメーターを示しています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "tagValues": {
            "type": "object",
            "defaultValue": {
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": "[parameters('tagValues')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-a-json-string-to-the-tag-name"></a>タグ名への JSON 文字列の適用

1 つのタグに複数の値を格納するには、値を表す JSON 文字列を適用します。 JSON 文字列全体は、1 つのタグとして格納されます。256 文字以下にする必要があります。 次の例は、JSON 文字列で複数の値を格納する `CostCenter` という名前の 1 つのタグを示しています。  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-from-resource-group"></a>リソース グループからタグを適用する

リソース グループからリソースにタグを適用するには、[resourceGroup](../templates/template-functions-resource.md#resourcegroup) 関数を使います。 タグの値を取得するときは、`tags.tag-name` 構文ではなく `tags[tag-name]` 構文を使います。これは、ドット表記では一部の文字が正しく解析されないためです。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "[resourceGroup().tags['Dept']]",
                "Environment": "[resourceGroup().tags['Environment']]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

## <a name="portal"></a>ポータル

[!INCLUDE [resource-manager-tag-resource](../../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST API

Azure Portal と PowerShell のどちらも、バックグラウンドで [リソース マネージャーの REST API](/rest/api/resources/) を使用します。 別の環境にタグ付けを統合する必要がある場合、リソース ID に対する **GET** 操作でタグを取得し、**PATCH** 呼び出しでタグのセットを更新できます。

## <a name="tags-and-billing"></a>タグと課金

タグを使用して課金データをグループ化できます。 たとえば、異なる組織向けに複数の VM を実行している場合は、タグを使用して、コスト センターごとに使用状況をグループ化します。 また、タグを使用すると、運用環境で実行されている VM の課金データなどの、ランタイム環境ごとにコストを分類することもできます。

タグに関する情報は、 [Azure Resource Usage API と RateCard API](../../billing/billing-usage-rate-card-overview.md) から、あるいはコンマ区切り値 (CSV) ファイルから取得できます。 使用状況ファイルは [Azure アカウント センター](https://account.azure.com/Subscriptions)または Azure portal からダウンロードします。 詳細については、「[Azure の請求書と毎日の使用状況データをダウンロードまたは表示する](../../billing/billing-download-azure-invoice-daily-usage-date.md)」を参照してください。 Azure アカウント センターから使用状況ファイルをダウンロードする場合は、 **[バージョン 2]** を選択します。 課金のタグがサポートされているサービスの場合、タグは **[Tags]** 列に表示されます。

REST API の操作については、「 [Azure Billing REST API Reference (Azure Billing REST API リファレンス)](/rest/api/billing/)」を参照してください。

## <a name="next-steps"></a>次のステップ

* すべてのリソースの種類で、タグがサポートされるわけではありません。 リソースの種類にタグを適用することができるかどうかを確認するには、[Azure リソースに対するタグのサポート](tag-support.md)に関する記事を参照してください。
* ポータルの使用方法の概要については、「[Azure ポータルを使用した Azure リソースの管理](manage-resource-groups-portal.md)」をご覧ください。  
