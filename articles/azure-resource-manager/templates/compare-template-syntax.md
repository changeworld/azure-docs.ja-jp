---
title: Azure Resource Manager テンプレートの構文を JSON と Bicep で比較する
description: JSON と Bicep で開発された Azure Resource Manager テンプレートを比較し、言語間での変換方法を示します。
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 29c2b9948957ebc10a26f22f0fe3daf383dfe5ba
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036216"
---
# <a name="comparing-json-and-bicep-for-templates"></a>テンプレートにおける JSON と Bicep の比較

この記事では、Azure Resource Manager テンプレート (ARM テンプレート) に関する Bicep 構文と JSON 構文を比較します。 ほとんどの場合、Bicep 構文は、JSON の同等のものより詳細度が低くなります。

## <a name="syntax-equivalents"></a>対応する構文

JSON を使用した ARM テンプレートの開発に慣れている場合は、次の表を使用して、Bicep の同等の構文について確認してください。

| シナリオ | Bicep | JSON |
| -------- | ------------ | ----- |
| 式を作成する | `func()` | `"[func()]"` |
| パラメーター値を取得する | `exampleParameter` | `[parameters('exampleParameter'))]` |
| 変数の値を取得する | `exampleVar` | `[variables('exampleVar'))]` |
| 文字列を連結する | `'${namePrefix}-vm'` | `[concat(parameters('namePrefix'), '-vm')]` |
| リソース プロパティを設定する | `sku: '2016-Datacenter'` | `"sku": "2016-Datacenter",` |
| 論理 AND を返す | `isMonday && isNovember` | `[and(parameter('isMonday'), parameter('isNovember'))]` |
| テンプレート内のリソースのリソース ID を取得する | `nic1.id` | `[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]` |
| テンプレート内のリソースからプロパティを取得する | `diagsAccount.properties.primaryEndpoints.blob` | `[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]` |
| 条件付きで値を設定する | `isMonday ? 'valueIfTrue' : 'valueIfFalse'` | `[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]` |
| ソリューションを複数のファイルに分割する | モジュールを使用します | リンクされたテンプレートを使用します |
| デプロイのターゲット範囲を設定する | `targetScope = 'subscription'` | `"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"` |
| 依存関係を設定する | 依存関係の自動検出を利用するか、`dependsOn: [ stg ]` を使用して依存関係を手動で設定します | `"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]` |
| リソースの宣言 | `resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {...}` | `"resources": [ { "type": "Microsoft.Compute/virtualMachines", "apiVersion": "2020-06-01", ... } ]` |

## <a name="recommendations"></a>推奨事項

* 可能であれば、Bicep ファイルで [reference](template-functions-resource.md#reference) と [resourceId](template-functions-resource.md#resourceid) 関数を使用しないようにしてください。 同じ Bicep デプロイでリソースを参照する場合は、代わりにリソース識別子を使用します。 たとえば、リソース識別子として `stg` 使用して Bicep ファイルでリソースをデプロイした場合、プロパティ値を取得するには `stg.id` や `stg.properties.primaryEndpoints.blob` のような構文を使用します。 リソース識別子を使用することで、リソース間に暗黙的な依存関係が作成されます。 dependsOn プロパティを使用して依存関係を明示的に設定する必要はありません。
* 識別子には一貫した大文字小文字の区別を使用してください。 使用する大文字小文字の種類がわからない場合は、キャメル形式を試してください。 たとえば、「 `param myCamelCasedParameter string` 」のように入力します。
* 説明がユーザーにとって重要な情報となる場合にのみ、説明をパラメーターに追加します。 情報のために `//` コメントを使用できます。

## <a name="decompile-json-to-bicep"></a>JSON から Bicep への逆コンパイル

Bicep CLI には、既存の ARM テンプレートを Bicep ファイルに逆コンパイルするコマンドが用意されています。 JSON ファイルをデコンパイルするには、次のように使用します。`bicep decompile "path/to/file.json"`

このコマンドは、Bicep 作成の開始点として使用できますが、このコマンドはすべてのテンプレートで機能するわけではありません。 コマンドが失敗したり、逆コンパイル後に問題を修正することが必要になる場合があります。 現在、入れ子になったテンプレートは、'inner' 式の評価スコープを使用している場合にのみ逆コンパイルできます。

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

Bicep の詳細については、[Bicep のチュートリアル](./bicep-tutorial-create-first-bicep.md)を参照してください。
