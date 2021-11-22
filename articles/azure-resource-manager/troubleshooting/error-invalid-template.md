---
title: 無効なテンプレート エラー
description: Azure Resource Manager テンプレートのデプロイ時の無効なテンプレート エラーを解決する方法について説明します。
ms.topic: troubleshooting
ms.date: 11/11/2021
ms.openlocfilehash: 2565a68b63e23ecd81338c0bfbbdc36878c1a95a
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401917"
---
# <a name="resolve-errors-for-invalid-template"></a>無効なテンプレートのエラーを解決する

この記事では、無効なテンプレート エラーを解決する方法について説明します。

## <a name="symptom"></a>症状

テンプレートをデプロイすると、次のエラーを受け取ります。

```
Code=InvalidTemplate
Message=<varies>
```

エラー メッセージはエラーの種類によって変わります。

## <a name="cause"></a>原因

このエラーは、さまざまな種類のエラーが原因となって発生する場合があります。 通常、テンプレートの構文や構造にエラーがあります。

<a id="syntax-error"></a>

## <a name="solution-1---syntax-error"></a>解決策 1 - 構文エラー

テンプレートの検証に失敗したことを示すエラー メッセージが表示された場合、テンプレートに構文エラーが存在する可能性があります。

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

テンプレートの式は複雑になることもあり、このエラーが発生することは珍しくありません。 たとえば、ストレージ アカウントの次の名前の割り当てには、1 組の角かっこ、3 つの関数、3 組のかっこ、1 組の一重引用符、および 1 つのプロパティが含まれています。

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

一致する構文を指定しないと、テンプレートによって、意図したものとは異なる値が生成されます。

この種類のエラーが発生したら、式の構文を慎重に確認してください。 [Visual Studio](../templates/create-visual-studio-deployment-project.md) や [Visual Studio Code](../templates/quickstart-create-templates-use-visual-studio-code.md) など、構文エラーの指摘が可能な JSON エディターの使用を検討してください。

<a id="incorrect-segment-lengths"></a>

## <a name="solution-2---incorrect-segment-lengths"></a>解決策 2 - セグメントの長さが不適切

テンプレートが無効であるために発生するエラーは他にもあります。たとえばリソース名が正しい形式になっていないとエラーが発生します。 このエラーを解決するには、[名前と種類の不一致のエラーの解決](error-invalid-name-segments.md)に関する記事を参照してください。

<a id="parameter-not-valid"></a>

## <a name="solution-3---parameter-isnt-valid"></a>解決策 3 - パラメーターが無効

許可されているパラメーター値以外の値を指定すると、次のようなエラー メッセージが表示されます。

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

テンプレートで許可された値を十分に確認し、デプロイの際はその値を用いてください。 許可されているパラメーター値の詳細については、「[Azure Resource Manager テンプレートの parameters セクション](../templates/syntax.md#parameters)」をご覧ください。

<a id="too-many-resource-groups"></a>

## <a name="solution-4---too-many-target-resource-groups"></a>解決策 4 - ターゲット リソース グループが多すぎる

以前のデプロイでは、ターゲット リソース グループが 1 回のデプロイにつき 5 つに制限されていたため、このエラーが表示されることがあります。 この制限は、2020 年 5 月に 800 リソース グループに増やされました。 詳細については、「[複数のサブスクリプションまたはリソース グループに Azure リソースをデプロイする](../templates/deploy-to-resource-group.md)」を参照してください。

<a id="circular-dependency"></a>

## <a name="solution-5---circular-dependency-detected"></a>解決策 5 - 循環依存関係が検出された

このエラーは、リソース同士の依存関係がデプロイの開始を妨げるときに受け取ります。 相互依存関係の組み合わせにより、2 つ以上のリソースが同じく待機しているその他のリソースを待機します。 たとえば、resource1 が resource3 に依存、resource2 が resource1 に依存、resource3 が resource2 している場合などです。 通常、この問題は不要な依存関係を削除することによって解決できます。

循環依存関係を解決するには:

1. テンプレートで、循環依存関係が検出されたリソースを検索します。
2. **dependsOn** プロパティと **参照** 関数の使用を確認し、そのリソースが依存しているリソースを調べます。
3. それらのリソースが依存しているリソースを確認します。 元のリソースに依存するリソースを見つけるまで依存関係を辿ります。
5. 循環依存関係に関連するリソースについては、すべての **dependsOn** プロパティを調べ、不要な依存関係を特定します。 それら不要な依存関係を削除します。 依存関係が必要であるかどうか確証がない場合は、削除してみてください。
6. テンプレートを再デプロイします。

**dependsOn** プロパティから値を削除すると、テンプレートをデプロイするときにエラーが発生することがあります。 エラーが発生した場合は、テンプレートの依存関係を元に戻します。

このアプローチで循環依存関係が解決しない場合は、デプロイ ロジックの一部 (拡張機能や構成設定など) を子リソースに移行することを検討してください。 それらの子リソースを、循環依存関係に関連するリソースの後にデプロイするように構成します。 たとえば、2 つの仮想マシンをデプロイする場合を考えてみます。それぞれ互いに参照するプロパティを設定する必要があるとします。 この仮想マシンは、次の順序でデプロイできます。

1. vm1
2. vm2
3. vm1 の拡張機能は vm1 と vm2 に依存します。 拡張機能は vm2 から取得した値を vm1 に設定します。
4. vm2 の拡張機能は vm1 と vm2 に依存します。 拡張機能は vm1 から取得した値を vm2 に設定します。

このアプローチは App Service アプリでも有効です。 構成値をアプリ リソースの子リソースに移動することを検討してください。 2 つの Web アプリは次の順序でデプロイできます。

1. webapp1
2. webapp2
3. webapp1 の config は webapp1 と webapp2 に依存します。 アプリの設定には webapp2 からの値が含まれています。
4. webapp2 の config は webapp1 と webapp2 に依存します。 アプリの設定には webapp1 からの値が含まれています。
