---
title: Azure Monitor ブックと Azure Resource Manager テンプレート
description: Azure Resource Manager テンプレートを介してデプロイされた作成済みのブックやパラメーター化されたカスタム Azure Monitor ブックを使用して、複雑なレポート作成を簡素化します
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: d48cefcae16829bc0a58cdb4f4a43f52d78f8460
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751404"
---
# <a name="programmatically-manage-workbooks"></a>プログラムでブックを管理する

リソース所有者は、Resource Manager テンプレートを使用して、プログラムでブックを作成および管理することができます。

これは、次のようなシナリオで役に立ちます。
* リソースのデプロイと共に、組織またはドメインに固有の分析レポートをデプロイする。 たとえば、組織に固有のパフォーマンスおよび障害ブックを新しいアプリまたは仮想マシン用にデプロイできます。
* 既存のリソース用のブックを使用して、標準レポートまたはダッシュボードをデプロイする。

ブックは、Resource Manager テンプレートで指定されたコンテンツと共に、ご希望の (サブ) リソース グループに作成されます。

プログラムで管理できるブック リソースには、次の 2 種類があります。
* [ブック テンプレート](#azure-resource-manager-template-for-deploying-a-workbook-template)
* [ブック インスタンス](#azure-resource-manager-template-for-deploying-a-workbook-instance)

## <a name="azure-resource-manager-template-for-deploying-a-workbook-template"></a>ブック テンプレートをデプロイするための Azure Resource Manager テンプレート

1. プログラムでデプロイするブックを開きます。
2. _[編集]_ ツール バー項目をクリックして、ブックを編集モードに切り替えます。
3. ツール バーの _[</>]_ ボタンを使用して "_詳細エディター_" を開きます。
4. _[ギャラリー テンプレート]_ タブが確実に表示されている状態にします。

    ![[ギャラリー テンプレート] タブ](./media/workbooks-automate/gallery-template.png)
1. ギャラリー テンプレート内の JSON をクリップボードにコピーします。
2. Azure Monitor ブック ギャラリーにブック テンプレートをデプロイするサンプル Azure Resource Manager テンプレートを以下に示します。 `<PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>` の代わりにコピーした JSON を貼り付けます。 ブック テンプレートを作成するリファレンス Azure Resource Manager テンプレートについては、[こちら](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/ARM-template-for-creating-workbook-template)にあります。

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "resourceName": {
                "type": "string",
                "defaultValue": "my-workbook-template",
                "metadata": {
                    "description": "The unique name for this workbook template instance"
                }
            }
        },
        "resources": [
            {
                "name": "[parameters('resourceName')]",
                "type": "microsoft.insights/workbooktemplates",
                "location": "[resourceGroup().location]",
                "apiVersion": "2019-10-17-preview",
                "dependsOn": [],
                "properties": {
                    "galleries": [
                        {
                            "name": "A Workbook Template",
                            "category": "Deployed Templates",
                            "order": 100,
                            "type": "workbook",
                            "resourceType": "Azure Monitor"
                        }
                    ],
                    "templateData": <PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>
                }
            }
        ]
    }
    ```
1. `galleries` オブジェクトで、`name` および `category` キーに、使用する値を入力します。 [パラメーター](#parameters)の詳細については、次のセクションを参照してください。
2. この Azure Resource Manager テンプレートは、[Azure portal](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)、[コマンド ライン インターフェイス](../../azure-resource-manager/templates/deploy-cli.md)、[PowerShell](../../azure-resource-manager/templates/deploy-powershell.md) などを使用してデプロイします。
3. Azure portal を開き、Azure Resource Manager テンプレートで選択したブック ギャラリーに移動します。 テンプレートの例で、Azure Monitor ブック ギャラリーに移動します。
    1. Azure portal を開き、Azure Monitor に移動します。
    2. 目次から `Workbooks` を開く
    3. ギャラリー内のカテゴリ `Deployed Templates` (紫色の項目の中の 1 つ) で使用するテンプレートを検索します。

### <a name="parameters"></a>パラメーター

|パラメーター                |説明                                                                                             |
|:-------------------------|:-------------------------------------------------------------------------------------------------------|
| `name`                   | Azure Resource Manager 内のブック テンプレート リソースの名前。                                  |
|`type`                    | 常に microsoft.insights/workbooktemplates                                                            |
| `location`               | ブックが作成される Azure の場所。                                               |
| `apiVersion`             | 2019-10-17 プレビュー                                                                                     |
| `type`                   | 常に microsoft.insights/workbooktemplates                                                            |
| `galleries`              | このブック テンプレートが表示されるギャラリー セット。                                                |
| `gallery.name`           | ギャラリー内のブック テンプレートのフレンドリ名。                                             |
| `gallery.category`       | テンプレートを配置するギャラリー内のグループ。                                                     |
| `gallery.order`          | ギャラリー内のカテゴリにおいてテンプレートの表示順序を決定する数値。 順位が低いほど優先順位が高くなります。 |
| `gallery.resourceType`   | ギャラリーに対応するリソースの種類。 これは、通常、リソースに対応するリソースの種類の文字列です (例: microsoft.operationalinsights/workspaces)。 |
|`gallery.type`            | ブックの種類と呼ばれます。これは、リソースの種類においてギャラリーを区別する一意のキーです。 たとえば、Application Insights には、さまざまなブック ギャラリーに対応する種類として `workbook` と `tsg` があります。 |

### <a name="galleries"></a>ギャラリー

| [ギャラリー]                                        | リソースの種類                                       | ブックの種類 |
|:-----------------------------------------------|:----------------------------------------------------|:--------------|
| Azure Monitor での Workbooks                     | `Azure Monitor`                                     | `workbook`    |
| Azure Monitor での VM Insights                   | `Azure Monitor`                                     | `vm-insights` |
| Log Analytics ワークスペースでの Workbooks           | `microsoft.operationalinsights/workspaces`          | `workbook`    |
| Application Insights での Workbooks              | `microsoft.insights/components`                     | `workbook`    |
| Application Insights のトラブルシューティング ガイド | `microsoft.insights/components`                     | `tsg`         |
| Application Insights の使用                  | `microsoft.insights/components`                     | `usage`       |
| Kubernetes サービスでの Workbooks                | `Microsoft.ContainerService/managedClusters`        | `workbook`    |
| リソース グループでの Workbooks                   | `microsoft.resources/subscriptions/resourcegroups`  | `workbook`    |
| Azure Active Directory での Workbooks            | `microsoft.aadiam/tenant`                           | `workbook`    |
| Virtual Machines での VM Insights                | `microsoft.compute/virtualmachines`                 | `insights`    |
| 仮想マシン スケール セット内の VM Insights      | `microsoft.compute/virtualmachinescalesets`         | `insights`    |

## <a name="azure-resource-manager-template-for-deploying-a-workbook-instance"></a>ブック インスタンスをデプロイするための Azure Resource Manager テンプレート

1. プログラムでデプロイするブックを開きます。
2. _[編集]_ ツール バー項目をクリックして、ブックを編集モードに切り替えます。
3. ツール バーの _[</>]_ ボタンを使用して "_詳細エディター_" を開きます。
4. エディターで、 _[テンプレートの種類]_ を _[Resource Manager テンプレート]_ に切り替えます。
5. 作成用の Resource Manager テンプレートがエディターに表示されます。 内容をコピーしてそのまま使用するか、ターゲット リソースもデプロイする、より大きなテンプレートとマージします。

    ![ブック UI 内から Resource Manager テンプレートを取得する方法を示す画像](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>サンプルの Azure Resource Manager テンプレート
このテンプレートは、"Hello World!" を表示する単純なブックをデプロイする方法を示しています
```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workbookDisplayName":  {             
            "type":"string",
            "defaultValue": "My Workbook",
            "metadata": {
                "description": "The friendly name for the workbook that is used in the Gallery or Saved List. Needs to be unique in the scope of the resource group and source" 
            }
        },
        "workbookType":  {             
            "type":"string",
            "defaultValue": "tsg",
            "metadata": {
                "description": "The gallery that the workbook will been shown under. Supported values include workbook, `tsg`, Azure Monitor, etc." 
            }
        },
        "workbookSourceId":  {             
            "type":"string",
            "defaultValue": "<insert-your-resource-id-here>",
            "metadata": {
                "description": "The id of resource instance to which the workbook will be associated" 
            }
        },
        "workbookId": {
            "type":"string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "The unique guid for this workbook instance" 
            }
        }
    },    
    "resources": [
        {
            "name": "[parameters('workbookId')]",
            "type": "Microsoft.Insights/workbooks",
            "location": "[resourceGroup().location]",
            "kind": "shared",
            "apiVersion": "2018-06-17-preview",
            "dependsOn": [],
            "properties": {
                "displayName": "[parameters('workbookDisplayName')]",
                "serializedData": "{\"version\":\"Notebook/1.0\",\"items\":[{\"type\":1,\"content\":\"{\\\"json\\\":\\\"Hello World!\\\"}\",\"conditionalVisibility\":null}],\"isLocked\":false}",
                "version": "1.0",
                "sourceId": "[parameters('workbookSourceId')]",
                "category": "[parameters('workbookType')]"
            }
        }
    ],
    "outputs": {
        "workbookId": {
            "type": "string",
            "value": "[resourceId( 'Microsoft.Insights/workbooks', parameters('workbookId'))]"
        }
    }
}
```

### <a name="template-parameters"></a>Template parameters

| パラメーター | 説明 |
| :------------- |:-------------|
| `workbookDisplayName` | ギャラリーまたは保存リストで使用されているブックのフレンドリ名。 リソース グループとソースのスコープ内で一意である必要があります |
| `workbookType` | ブックが表示されるギャラリー。 サポートされる値には、workbook、`tsg`、Azure Monitor などがあります。 |
| `workbookSourceId` | ブックを関連付けるリソース インスタンスの ID。 新しいブックは、このリソース インスタンスに関連して表示されます (たとえば、 _[ブック]_ の下にあるリソースの目次内)。 自分のブックを Azure Monitor のブック ギャラリーに表示したい場合は、リソース ID ではなく文字列 _Azure Monitor_ を使用します。 |
| `workbookId` | このブック インスタンスの一意の GUID。 新しい GUID を自動的に作成するには、 _[newGuid()]_ を使用します。 |
| `kind` | 作成されるブックを共有するかどうかの指定に使用します。 すべての新しいブックで、値 _shared_ が使用されます。 |
| `location` | ブックの作成先となる Azure の場所。 リソース グループと同じ場所に作成するには、 _[resourceGroup().location]_ を使用します |
| `serializedData` | ブックで使用されるコンテンツまたはペイロードが格納されます。 ブック UI で Resource Manager テンプレートを使用して値を取得します |

### <a name="workbook-types"></a>ブックの種類
ブックの種類では、新しいブック インスタンスが表示されるブック ギャラリーの種類を指定します。 次のオプションがあります。

| Type | ギャラリーの場所 |
| :------------- |:-------------|
| `workbook` | Application Insights、Azure Monitor などのブック ギャラリーを含む、ほとんどのレポートで使用される既定値です。  |
| `tsg` | Application Insights の [トラブルシューティング ガイド] ギャラリー |
| `usage` | Application Insights の _[使用法]_ の下にある _[その他]_ ギャラリー |

### <a name="working-with-json-formatted-workbook-data-in-the-serializeddata-template-parameter"></a>serializedData テンプレート パラメーターの JSON 形式ブック データの操作

Azure ブック用に Azure Resource Manager テンプレートをエクスポートするときに、エクスポートされた `serializedData` テンプレート パラメーターの内部に固定のリソース リンクが埋め込まれていることがよくあります。 これらには、機密である可能性があるサブスクリプション ID やリソース グループ名などの値や、その他の種類のリソース ID が含まれます。

次の例では、文字列操作を行わずに、エクスポートしたブック Azure Resource Manager テンプレートをカスタマイズする方法を示しています。 この例で示しているパターンの意図は、Azure portal からエクスポートした未変更のデータを処理することです。 プログラムでブックを管理するときは、埋め込まれた機密の値をマスクで完全に除外することもベスト プラクティスであるため、ここではサブスクリプション ID とリソース グループをマスクしています。 生の受信値 `serializedData` に対するその他の変更は加えられていません。

```json
{
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workbookDisplayName": {
      "type": "string"
    },
    "workbookSourceId": {
      "type": "string",
      "defaultValue": "[resourceGroup().id]"
    },
    "workbookId": {
      "type": "string",
      "defaultValue": "[newGuid()]"
    }
  },
  "variables": {
    // serializedData from original exported Azure Resource Manager template
    "serializedData": "{\"version\":\"Notebook/1.0\",\"items\":[{\"type\":1,\"content\":{\"json\":\"Replace with Title\"},\"name\":\"text - 0\"},{\"type\":3,\"content\":{\"version\":\"KqlItem/1.0\",\"query\":\"{\\\"version\\\":\\\"ARMEndpoint/1.0\\\",\\\"data\\\":null,\\\"headers\\\":[],\\\"method\\\":\\\"GET\\\",\\\"path\\\":\\\"/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups\\\",\\\"urlParams\\\":[{\\\"key\\\":\\\"api-version\\\",\\\"value\\\":\\\"2019-06-01\\\"}],\\\"batchDisabled\\\":false,\\\"transformers\\\":[{\\\"type\\\":\\\"jsonpath\\\",\\\"settings\\\":{\\\"tablePath\\\":\\\"$..*\\\",\\\"columns\\\":[]}}]}\",\"size\":0,\"queryType\":12,\"visualization\":\"map\",\"tileSettings\":{\"showBorder\":false},\"graphSettings\":{\"type\":0},\"mapSettings\":{\"locInfo\":\"AzureLoc\",\"locInfoColumn\":\"location\",\"sizeSettings\":\"location\",\"sizeAggregation\":\"Count\",\"opacity\":0.5,\"legendAggregation\":\"Count\",\"itemColorSettings\":null}},\"name\":\"query - 1\"}],\"isLocked\":false,\"fallbackResourceIds\":[\"/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/XXXXXXX\"]}",

    // parse the original into a JSON object, so that it can be manipulated
    "parsedData": "[json(variables('serializedData'))]",

    // create new JSON objects that represent only the items/properties to be modified
    "updatedTitle": {
      "content":{
        "json": "[concat('Resource Group Regions in subscription \"', subscription().displayName, '\"')]"
      }
    },
    "updatedMap": {
      "content": {
        "path": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups')]"
      }
    },

    // the union function applies the updates to the original data
    "updatedItems": [
      "[union(variables('parsedData')['items'][0], variables('updatedTitle'))]",
      "[union(variables('parsedData')['items'][1], variables('updatedMap'))]"
    ],

    // copy to a new workbook object, with the updated items
    "updatedWorkbookData": {
      "version": "[variables('parsedData')['version']]",
      "items": "[variables('updatedItems')]",
      "isLocked": "[variables('parsedData')['isLocked']]",
      "fallbackResourceIds": ["[parameters('workbookSourceId')]"]
    },

    // convert back to an encoded string
    "reserializedData": "[string(variables('updatedWorkbookData'))]"
  },
  "resources": [
    {
      "name": "[parameters('workbookId')]",
      "type": "microsoft.insights/workbooks",
      "location": "[resourceGroup().location]",
      "apiVersion": "2018-06-17-preview",
      "dependsOn": [],
      "kind": "shared",
      "properties": {
        "displayName": "[parameters('workbookDisplayName')]",
        "serializedData": "[variables('reserializedData')]",
        "version": "1.0",
        "sourceId": "[parameters('workbookSourceId')]",
        "category": "workbook"
      }
    }
  ],
  "outputs": {
    "workbookId": {
      "type": "string",
      "value": "[resourceId( 'microsoft.insights/workbooks', parameters('workbookId'))]"
    }
  },
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#"
}
```

この例では、次の手順によって、エクスポートした Azure Resource Manager テンプレートのカスタマイズを容易にしています。
1. 前のセクションで説明したように、ブックを Azure Resource Manager テンプレートとしてエクスポートします。
2. テンプレートの `variables` セクションで、次を実行します。
    1. `serializedData` 値を JSON オブジェクト変数に解析します。これにより、ブックの内容を表す項目の配列を含む JSON 構造体が作成されます。
    2. 変更する項目またはプロパティのみを表す新しい JSON オブジェクトを作成します。
    3. `union()` 関数を使用して、JSON 内容項目の新しいセット (`updatedItems`) を投影し、元の JSON 項目に変更を適用します。
    4. 新しいブック オブジェクト `updatedWorkbookData` を作成します。これには、元の解析済みデータからの `updatedItems` および `version`/`isLocked` データと、`fallbackResourceIds` の訂正されたセットが含まれます。
    5. 新しい JSON コンテンツをシリアル化して、新しい文字列変数 `reserializedData` に戻します。
3. 元の `serializedData` プロパティの代わりに新しい `reserializedData` 変数を使用します。
4. 更新された Azure Resource Manager テンプレートを使用して新しいブック リソースをデプロイします。

### <a name="limitations"></a>制限事項
技術的な理由により、このメカニズムを使用して Application Insights の _[ブック]_ ギャラリーにブック インスタンスを作成することはできません。 この制限については、解決に取り組んでいる最中です。 その間は、[トラブルシューティング ガイド] ギャラリー (workbookType: `tsg`) を使用して Application Insights に関連するブックをデプロイすることをお勧めします。

## <a name="next-steps"></a>次のステップ

新しい[ストレージ分析情報エクスペリエンス](../../storage/common/storage-insights-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json)を強化するためにブックがどのように使用されているかを確認してください。