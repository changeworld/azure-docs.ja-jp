---
title: Azure Resource Manager テンプレートの出力 | Microsoft Docs
description: 宣言型 JSON 構文を使用した Azure Resource Manager テンプレートの出力を定義する方法について説明します。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/18/2018
ms.author: tomfitz
ms.openlocfilehash: 9a46d813f2e50831240303ba47380da39e2cb6af
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53725813"
---
# <a name="outputs-section-in-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートの出力セクション
[出力] セクションではデプロイから返される値を指定します。 たとえば、デプロイされたリソースにアクセスするための URI を返すことができます。

## <a name="define-and-use-output-values"></a>出力値の定義および使用

次の例は、パブリック IP アドレスのリソース ID を返す方法を示しています。

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

デプロイ後、スクリプトを使用して値を取得できます。 PowerShell では、次を使用します。

```powershell
(Get-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

Azure CLI では、次を使用します。

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

[reference](resource-group-template-functions-resource.md#reference) 関数を使用して、リンクされているテンプレートから出力値を取得できます。 リンクされたテンプレートから出力値を取得するには、`"[reference('deploymentName').outputs.propertyName.value]"` のような構文でプロパティ値を取得します。

リンクされたテンプレートから出力プロパティを取得する場合、プロパティ名にダッシュを含めることはできません。

たとえば、リンクされているテンプレートから値を取得することによってロード バランサーの IP アドレスを設定できます。

```json
"publicIPAddress": {
    "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

[入れ子になったテンプレート](resource-group-linked-templates.md#link-or-nest-a-template) の出力セクションでは `reference` 関数を使用できません。 入れ子になったテンプレート内のデプロイされたリソースの値を返すには、入れ子になったテンプレートをリンク済みテンプレートに変換します。

## <a name="available-properties"></a>利用可能なプロパティ

次の例では、出力の定義の構造を示します。

```json
"outputs": {
    "<outputName>" : {
        "type" : "<type-of-output-value>",
        "value": "<output-value-expression>"
    }
}
```

| 要素名 | 必須 | 説明 |
|:--- |:--- |:--- |
| outputName |はい |出力値の名前。 有効な JavaScript 識別子で指定する必要があります。 |
| type |はい |出力値の型。 出力値では、テンプレート入力パラメーターと同じ型がサポートされています。 |
| value |はい |評価され、出力値として返されるテンプレート言語式。 |


## <a name="example-templates"></a>サンプル テンプレート

|テンプレート  |説明  |
|---------|---------|
|[Copy variables](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | 複合変数を作成し、それらの値を出力します。 リソースはデプロイしません。 |
|[パブリック IP アドレス](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | パブリック IP アドレスを作成し、リソース ID を出力します。 |
|[Load Balancer](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | 前述のテンプレートにリンクします。 ロード バランサーの作成時に出力内のリソース ID を使用します。 |


## <a name="next-steps"></a>次の手順
* さまざまな種類のソリューションのテンプレートについては、「 [Azure クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)」をご覧ください。
* テンプレート内から使用できる関数の詳細については、「 [Azure Resource Manager テンプレートの関数](resource-group-template-functions.md)」を参照してください。
* テンプレート作成に関する推奨事項については、「[Azure Resource Manager テンプレートのベスト プラクティス](template-best-practices.md)」を参照してください。
