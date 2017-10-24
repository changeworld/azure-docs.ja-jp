---
title: "Azure リソースが見つからないエラー | Microsoft Docs"
description: "リソースが見つからない場合に、エラーを解決する方法について説明します。"
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
ms.openlocfilehash: c76c965c43ca8217faa9488c01975ce09a21daaf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Azure リソースが見つからないエラーを解決する

この記事では、デプロイ時にリソースが見つからない場合に発生するエラーについて説明します。 

## <a name="symptom"></a>症状

解決できないリソースの名前がテンプレートに含まれている場合、次のようなエラーが表示されます。

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

解決できないリソースで [reference](resource-group-template-functions-resource.md#reference) または [listKeys](resource-group-template-functions-resource.md#listkeys) 関数を使用しようとした場合、次のエラーが発生します。

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>原因

Resource Manager はリソースのプロパティを取得する必要がありますが、サブスクリプション内のリソースを識別できません。

## <a name="solution"></a>解決策

### <a name="solution-1"></a>解決策 1

不足しているリソースをテンプレートにデプロイする場合は、依存関係を追加する必要があるかどうかを確認してください。 Resource Manager は、可能であれば複数のリソースを並列して作成することで、デプロイを最適化しています。 リソースを順番にデプロイする必要がある場合は、テンプレートで **dependsOn** 要素を使用して、他のリソースへの依存関係を作成してください。 たとえば、Web アプリをデプロイする場合は、App Service プランが存在する必要があります。 Web アプリが App Service プランに依存していることを指定しなかった場合、Resource Manager では同時に両方のリソースが作成されます。 また、App Service プランのリソースが見つからないというエラーが発生します。これは、Web アプリにプロパティを設定しようとしたときに、そのリソースがまだ存在しないためです。 このエラーは、Web アプリに依存関係を設定することで回避します。

```json
{
  "apiVersion": "2015-08-01",
  "type": "Microsoft.Web/sites",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

依存関係に関するエラーのトラブルシューティングのヒントについて詳しくは、「[デプロイの順序の確認](resource-manager-troubleshoot-tips.md#check-deployment-sequence)」をご覧ください。

### <a name="solution-2"></a>解決策 2

デプロイ先とは別のリソース グループにリソースが存在する場合は、[resourceId 関数](resource-group-template-functions-resource.md#resourceid)を使用して、リソースの完全修飾名を取得します。

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

### <a name="solution-3"></a>解決策 3

[reference](resource-group-template-functions-resource.md#reference) 関数を含む式を検索してください。 指定する値は、リソースが同じテンプレート、リソース グループ、およびサブスクリプション内にあるかどうかに応じて異なります。 シナリオで必要なパラメーター値を指定していることを再確認してください。 リソースが別のリソース グループ内にある場合は、完全なリソース ID を指定します。 たとえば、別のリソース グループのストレージ アカウントを参照するには、次のコードを使用します。

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```