---
title: "Azure の無効なテンプレート エラー | Microsoft Docs"
description: "無効なテンプレート エラーを解決する方法について説明します。"
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: e58c10cfb4cdd4ba49945e6c19845cbc957d6326
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
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

## <a name="solution"></a>解決策

### <a name="solution-1---syntax-error"></a>解決策 1 - 構文エラー

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

この種類のエラーが発生したら、式の構文を慎重に確認してください。 [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) や [Visual Studio Code](resource-manager-vs-code.md) など、構文エラーの指摘が可能な JSON エディターの使用を検討してください。

### <a name="solution-2---incorrect-segment-lengths"></a>解決策 2 - セグメントの長さが不適切

テンプレートが無効であるために発生するエラーは他にもあります。たとえばリソース名が正しい形式になっていないとエラーが発生します。

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

ルート レベルのリソースは、名前に含まれるセグメントの数がリソース タイプのセグメントの数よりも 1 つ少なくなっている必要があります。 各セグメントは、スラッシュで区別されます。 次の例は、type のセグメント数が 2 つで、name のセグメント数が 1 つなので、**有効な名前**です。

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "myHostingPlanName",
  ...
}
```

しかし次の例は、name と type のセグメント数が同じであるため、 **有効な名前ではありません** 。

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "appPlan/myHostingPlanName",
  ...
}
```

子のリソースに関しては、type と name のセグメント数が一致します。 このセグメントの数は理に適っています。子の完全な name と type には親の name と type が含まれるためです。 したがって、完全な name のセグメント数は、やはり完全な type よりも 1 つ少なくなります。

```json
"resources": [
    {
        "type": "Microsoft.KeyVault/vaults",
        "name": "contosokeyvault",
        ...
        "resources": [
            {
                "type": "secrets",
                "name": "appPassword",
                ...
            }
        ]
    }
]
```

複数のリソース プロバイダーに対して適用される Resource Manager タイプでは、セグメントを正しく指定するために注意が必要となります。 たとえば、リソース ロックを Web サイトに適用するためには、type が 4 つのセグメントで構成されている必要があります。 したがって name のセグメント数は 3 つになります。

```json
{
    "type": "Microsoft.Web/sites/providers/locks",
    "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
    ...
}
```

### <a name="solution-3---parameter-is-not-valid"></a>解決策 3 - パラメーターが無効

パラメーターに使用できる値がテンプレートで指定されている場合に、それ以外の値を指定すると、次のようなエラー メッセージが表示されます。

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

テンプレートで許可された値を十分に確認し、デプロイの際はその値を用いてください。

### <a name="solution-4---circular-dependency-detected"></a>解決策 4 - 循環依存関係が検出された

このエラーは、リソース同士の依存関係がデプロイの開始を妨げるときに受け取ります。 相互依存関係の組み合わせにより、2 つ以上のリソースが同じく待機しているその他のリソースを待機します。 たとえば、resource1 が resource3 に依存、resource2 が resource1 に依存、resource3 が resource2 している場合などです。 通常、この問題は不要な依存関係を削除することによって解決できます。
