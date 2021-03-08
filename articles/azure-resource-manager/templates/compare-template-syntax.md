---
title: JSON と Bicep の間で Azure Resource Manager テンプレートを変換する
description: JSON と Bicep で開発された Azure Resource Manager テンプレートを比較します。
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 9388ed50f13d6885d0a0668b61a9141dae375244
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101743580"
---
# <a name="comparing-json-and-bicep-for-templates"></a>テンプレートにおける JSON と Bicep の比較

この記事では、Azure Resource Manager テンプレート (ARM テンプレート) に関する Bicep 構文と JSON 構文を比較します。 ほとんどの場合、Bicep 構文は、JSON の同等のものより詳細度が低くなります。

## <a name="syntax-equivalents"></a>対応する構文

JSON を使用した ARM テンプレートの開発に慣れている場合は、次の表を使用して、Bicep の同等の構文について確認してください。

| シナリオ | ARM テンプレート | Bicep |
| -------- | ------------ | ----- |
| 式を作成する | `"[func()]"` | `func()` |
| パラメーター値を取得する | `[parameters('exampleParameter'))]` | `exampleParameter` |
| 変数の値を取得する | `[variables('exampleVar'))]` | `exampleVar` |
| 文字列を連結する | `[concat(parameters('namePrefix'), '-vm')]` | `'${namePrefix}-vm'` |
| リソース プロパティを設定する | `"sku": "2016-Datacenter",` | `sku: '2016-Datacenter'` |
| 論理 AND を返す | `[and(parameter('isMonday'), parameter('isNovember'))]` | `isMonday && isNovember` |
| テンプレート内のリソースのリソース ID を取得する | `[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]` | `nic1.id` |
| テンプレート内のリソースからプロパティを取得する | `[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]` | `diagsAccount.properties.primaryEndpoints.blob` |
| 条件付きで値を設定する | `[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]` | `isMonday ? 'valueIfTrue' : 'valueIfFalse'` |
| ソリューションを複数のファイルに分割する | リンクされたテンプレートを使用します | モジュールを使用します |
| デプロイのターゲット範囲を設定する | `"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"` | `targetScope = 'subscription'` |
| 依存関係を設定する | `"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]` | 依存関係の自動検出を利用するか、`dependsOn: [ stg ]` を使用して依存関係を手動で設定します |

リソースの型とバージョンを宣言するには、Bicep で次を使用します。

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
}
```

JSON の対応する構文の代わりになります。

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2020-06-01",
    ...
  }
]
```

## <a name="recommendations"></a>推奨事項

* 可能であれば、Bicep ファイルで [reference](template-functions-resource.md#reference) と [resourceId](template-functions-resource.md#resourceid) 関数を使用しないようにしてください。 同じ Bicep デプロイでリソースを参照する場合は、代わりにリソース識別子を使用します。 たとえば、リソース識別子として `stg` 使用して Bicep ファイルでリソースをデプロイした場合、プロパティ値を取得するには `stg.id` や `stg.properties.primaryEndpoints.blob` のような構文を使用します。 リソース識別子を使用することで、リソース間に暗黙的な依存関係が作成されます。 dependsOn プロパティを使用して依存関係を明示的に設定する必要はありません。
* 識別子には一貫した大文字小文字の区別を使用してください。 使用する大文字小文字の種類がわからない場合は、キャメル形式を試してください。 たとえば、「 `param myCamelCasedParameter string` 」のように入力します。
* 説明がユーザーにとって重要な情報となる場合にのみ、説明をパラメーターに追加します。 情報のために `//` コメントを使用できます。

## <a name="decompile-json-to-bicep"></a>JSON から Bicep への逆コンパイル

Bicep CLI には、既存の ARM テンプレートを Bicep ファイルに逆コンパイルするコマンドが用意されています。 JSON ファイルをデコンパイルするには、次のように使用します。`bicep decompile "path/to/file.json"`

このコマンドは、Bicep 作成の開始点として使用できますが、このコマンドはすべてのテンプレートで機能するわけではありません。 コマンドが失敗したり、逆コンパイル後に問題を修正することが必要になる場合があります。 現在、このコマンドには次の制限があります。

* コピー ループを使用するテンプレートを逆コンパイルすることはできません。
* 入れ子になったテンプレートは、'inner' 式の評価スコープを使用している場合にのみ逆コンパイルできます。

リソース グループのテンプレートをエクスポートし、それを bicep decompile コマンドに直接渡すことができます。 次の例は、エクスポートされたテンプレートを逆コンパイルする方法を示しています。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group export --name "your_resource_group_name" > main.json
bicep decompile main.json
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Export-AzResourceGroup -ResourceGroupName "your_resource_group_name" -Path ./main.json
bicep decompile main.json
```

# <a name="portal"></a>[ポータル](#tab/azure-portal)

ポータルを使用して[テンプレートをエクスポート](export-template-portal.md)します。 ダウンロードしたファイルに対して `bicep decompile <filename>` を使用します。

---

## <a name="build-json-from-bicep"></a>Bicep から JSON をビルドする

Bicep CLI には、Bicep を JSON に変換するコマンドも用意されています。 JSON ファイルをビルドするには、次のように使用します。`bicep build "path/to/file.json"`

## <a name="side-by-side-view"></a>横に並べて表示

[Bicep Playground](https://aka.ms/bicepdemo) を使用すると、対応する JSON と Bicep のファイルを横並びで表示できます。 サンプル テンプレートを選択すると、両方のバージョンを表示できます。 または、`Decompile` を選択して、独自の JSON テンプレートをアップロードし、同等の Bicep ファイルを表示します。

## <a name="next-steps"></a>次のステップ

Bicep プロジェクトの詳細については、「[Project Bicep](https://github.com/Azure/bicep)」を参照してください。
