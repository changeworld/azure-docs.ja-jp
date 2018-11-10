---
title: Azure Resource Manager のデプロイ モード | Microsoft Docs
description: Azure Resource Manager で完全デプロイ モードと増分デプロイ モードのどちらを使用するか指定する方法について説明します。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/26/2018
ms.author: tomfitz
ms.openlocfilehash: 4d1b27c9b1694f987ea7461c16899f3e5ecb84d2
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/26/2018
ms.locfileid: "50140995"
---
# <a name="azure-resource-manager-deployment-modes"></a>Azure Resource Manager のデプロイ モード
リソースをデプロイするときには、デプロイが増分更新と完全更新のどちらであるかを指定する必要があります。  これら 2 つのモードの主な違いは、テンプレートにないリソース グループの既存のリソースを Resource Manager が処理する方法にあります。 既定のモードは増分です。

## <a name="incremental-and-complete-deployments"></a>増分デプロイと完全デプロイ
リソースをデプロイするときは、以下の手順に従います。

* 完全モードでは、Resource Manager はリソース グループに存在するがテンプレートに指定されていないリソースを**削除します**。 
* 増分モードでは、Resource Manager はリソース グループに存在するが、テンプレートに指定されていないリソースを**変更せず、そのまま残します**。

いずれのモードでも、Resource Manager はテンプレートで指定されたすべてのリソースの作成を試みます。 リソースが既にリソース グループに存在しており、その設定が変更されていない場合、操作の結果に変わりはありません。 リソースのプロパティ値を変更した場合、リソースはそれらの新しい値で更新されます。 既存のリソースの場所または種類を更新しようとすると、デプロイがエラーで失敗します。 その代わり、必要な場所または種類で新しいリソースをデプロイできます。

リソースを増分モードで再デプロイする場合は、更新するものだけでなく、リソースのすべてのプロパティ値を指定してください。 特定のプロパティを指定しなかった場合、Resource Manager は更新によってそれらの値を上書きするものと解釈します。

## <a name="example-result"></a>結果の例

増分モードと完全モードの違いを説明するために、次のシナリオについて考えます。

**リソース グループ**には、次が含まれます。

* リソース A
* リソース B
* リソース C

**テンプレート**には、次が含まれます。

* リソース A
* リソース B
* リソース D

**増分**モードでデプロイすると、リソース グループには次が含まれます。

* リソース A
* リソース B
* リソース C
* リソース D

**完全**モードでデプロイすると、リソース C が削除されます。 リソース グループには次が含まれます。

* リソース A
* リソース B
* リソース D

## <a name="set-deployment-mode"></a>デプロイ モードの設定

PowerShell を使用してデプロイするときにデプロイ モードを設定するには、`Mode` パラメーターを使用します。

```powershell
New-AzureRmResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json 
```

Azure CLI を使用してデプロイするときにデプロイ モードを設定するには、`mode` パラメーターを使用します。

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

[リンクされたテンプレートまたは入れ子になったテンプレート](resource-group-linked-templates.md)を使用するときは、`mode` プロパティを `Incremental` に設定する必要があります。 完全デプロイ モードがサポートされるのはルートレベルのテンプレートのみです。

```json
"resources": [
  {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

## <a name="next-steps"></a>次の手順
* リソース マネージャーのテンプレートの作成の詳細については、 [Azure リソース マネージャーのテンプレートの作成](resource-group-authoring-templates.md)に関するページを参照してください。
* リソースをデプロイする方法を確認するには、「 [Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)」を参照してください。
* リソース プロバイダーの操作を表示するには、「[Azure REST API](/rest/api/)」を参照してください。

