---
title: "Azure 親リソース エラー | Microsoft Docs"
description: "親リソースの操作時のエラーを解決する方法について説明します。"
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
ms.openlocfilehash: e59147c4ac18f730f27b9d4aa9c008f219881065
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-errors-for-parent-resources"></a>親リソースのエラーを解決する

この記事では、親リソースに依存するリソースのデプロイ時に発生する可能性があるエラーについて説明します。

## <a name="symptom"></a>症状

別のリソースの子となるリソースをデプロイするときに、次のエラーが発生することがあります。

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>原因

あるリソースが別のリソースの子である場合、子リソースを作成する前に親リソースが存在する必要があります。 子リソースの名前には、親の名前が含まれます。 たとえば、SQL Database は次のように定義できます。

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

ただし、サーバーで依存関係を指定していない場合、サーバーがデプロイされる前にデータベースのデプロイが開始されることがあります。

## <a name="solution"></a>解決策

このエラーを解決するには、依存関係を含めます。

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

詳細については、「[Azure Resource Manager テンプレートでのリソース デプロイ順序の定義](resource-group-define-dependencies.md)」を参照してください。