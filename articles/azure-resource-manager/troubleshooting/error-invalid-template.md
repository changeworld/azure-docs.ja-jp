---
title: 無効なテンプレート エラー
description: Bicep ファイルまたは Azure Resource Manager テンプレート (ARM テンプレート) のデプロイ時の無効なテンプレート エラーを解決する方法について説明します。
ms.topic: troubleshooting
ms.date: 11/15/2021
ms.openlocfilehash: d5bd7494300ac8861ebd85478e380132acbac007
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550667"
---
# <a name="resolve-errors-for-invalid-template"></a>無効なテンプレートのエラーを解決する

この記事では、Bicep ファイルおよび Azure Resource Manager テンプレート (ARM テンプレート) に関する無効なテンプレート エラーを解決する方法について説明します。

## <a name="symptom"></a>症状

テンプレートをデプロイすると、次の内容のエラーが表示されます。

```Output
Code=InvalidTemplate
Message=<varies>
```

エラー メッセージはエラーの種類によって変わります。

## <a name="cause"></a>原因

このエラーは、さまざまな種類のエラーが原因となって発生する場合があります。 通常、テンプレートの構文や構造にエラーがあります。

<a id="syntax-error"></a>

## <a name="solution-1---syntax-error"></a>解決策 1 - 構文エラー

テンプレートの検証に失敗したことを示すエラー メッセージが表示された場合、テンプレートに構文エラーが存在する可能性があります。

```Output
Code=InvalidTemplate
Message=Deployment template validation failed
```

テンプレート式には多数の要素があるため、構文エラーが発生することがあります。 たとえば、ストレージ アカウントの名前の割り当てには、単一引用符または二重引用符、中かっこ、角かっこ、かっこのペアが含まれます。 式には、関数や、ドル記号、コンマ、ドットなどの文字も含まれます。


# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
name: 'storage${uniqueString(resourceGroup().id)}'
```

# <a name="json"></a>[JSON](#tab/json)

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

---

この種類のエラーが発生したら、式の構文を確認してください。 テンプレート エラーを識別するには、[Visual Studio Code](https://code.visualstudio.com) と最新の [Bicep 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep)または [Azure Resource Manager ツールの拡張機能](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)を使用できます。

<a id="incorrect-segment-lengths"></a>

## <a name="solution-2---incorrect-segment-lengths"></a>解決策 2 - セグメントの長さが不適切

テンプレートが無効であるために発生するエラーは他にもあります。たとえばリソース名が正しい形式になっていないとエラーが発生します。 このエラーを解決するには、[名前と種類の不一致のエラーの解決](error-invalid-name-segments.md)に関する記事を参照してください。

<a id="parameter-not-valid"></a>

## <a name="solution-3---parameter-isnt-valid"></a>解決策 3 - パラメーターが無効

テンプレートでパラメーターの許容値を指定できます。 デプロイ時に、許可されている値以外の値を指定すると、次のようなエラー メッセージが表示されます。

```Output
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

テンプレートでパラメーターの許容値を確認し、デプロイの際は許容値を使用してください。 詳細については、[Bicep](../bicep/parameters.md#allowed-values) または [ARM テンプレート](../templates/parameters.md#allowed-values)の許容値を参照してください。

<a id="too-many-resource-groups"></a>

## <a name="solution-4---too-many-target-resource-groups"></a>解決策 4 - ターゲット リソース グループが多すぎる

以前のデプロイでは、ターゲット リソース グループが 1 回のデプロイにつき 5 つに制限されていたため、このエラーが表示されることがあります。 この制限は、2020 年 5 月に 800 リソース グループに増やされました。 詳細については、[Bicep](../bicep/deploy-to-resource-group.md#deploy-to-multiple-resource-groups) または [ARM テンプレート](../templates/deploy-to-resource-group.md#deploy-to-multiple-resource-groups)の複数のリソース グループへのデプロイ方法を参照してください。

<a id="circular-dependency"></a>

## <a name="solution-5---circular-dependency-detected"></a>解決策 5 - 循環依存関係が検出された

このエラーは、リソース同士の依存関係がデプロイの開始を妨げるときに受け取ります。 相互依存関係の組み合わせにより、2 つ以上のリソースが同じく待機しているその他のリソースを待機します。 たとえば、resource1 が resource3 に依存、resource2 が resource1 に依存、resource3 が resource2 している場合などです。 通常、この問題は不要な依存関係を削除することによって解決できます。

Bicep は、あるリソースが別のリソースのシンボリック名を使用するときに、暗黙的な依存関係を作成します。 通常、`dependsOn` を使用した明示的な依存関係は必要ありません。 詳しくは、Bicep の[依存関係](../bicep/resource-declaration.md#dependencies)を参照してください。

循環依存関係を解決するには:

1. テンプレートで、循環依存関係が検出されたリソースを検索します。
1. そのリソースについて、`dependsOn` プロパティと `reference` 関数の使用を確認し、どのリソースに依存しているかを調べます。
1. それらのリソースが依存しているリソースを確認します。 元のリソースに依存するリソースを見つけるまで依存関係を辿ります。
1. 循環依存関係に関連するリソースについては、`dependsOn` プロパティのすべての使用を慎重に調べ、不要な依存関係を特定します。 それら不要な依存関係を削除します。 依存関係が必要であるかどうか確証がない場合は、削除してみてください。
1. テンプレートを再デプロイします。

`dependsOn` プロパティから値を削除すると、テンプレートをデプロイするときにエラーが発生することがあります。 エラーが発生した場合は、テンプレートの依存関係を元に戻します。

このアプローチで循環依存関係が解決しない場合は、デプロイ ロジックの一部 (拡張機能や構成設定など) を子リソースに移行することを検討してください。 それらの子リソースを、循環依存関係に関連するリソースの後にデプロイするように構成します。 たとえば、2 つの仮想マシンをデプロイする場合を考えてみます。それぞれ互いに参照するプロパティを設定する必要があるとします。 この仮想マシンは、次の順序でデプロイできます。

1. vm1
1. vm2
1. vm1 の拡張機能は vm1 と vm2 に依存します。 拡張機能は vm2 から取得した値を vm1 に設定します。
1. vm2 の拡張機能は vm1 と vm2 に依存します。 拡張機能は vm1 から取得した値を vm2 に設定します。

このアプローチは App Service アプリでも有効です。 構成値をアプリ リソースの子リソースに移動することを検討してください。 2 つの Web アプリは次の順序でデプロイできます。

1. webapp1
1. webapp2
1. webapp1 の構成は webapp1 と webapp2 に依存します。 アプリの設定には webapp2 からの値が含まれています。
1. webapp2 の構成は webapp1 と webapp2 に依存します。 アプリの設定には webapp1 からの値が含まれています。
