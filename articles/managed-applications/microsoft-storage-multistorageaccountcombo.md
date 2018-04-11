---
title: Azure の MultiStorageAccountCombo UI 要素 | Microsoft Docs
description: Azure Portal の Microsoft.Storage.MultiStorageAccountCombo UI 要素について説明します。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: c395c076a4910e124c1b93ebc61b5e491b2b53ff
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2018
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Microsoft.Storage.MultiStorageAccountCombo UI 要素
名前が共通のプレフィックスで始まる複数のストレージ アカウントを作成するコントロールのグループです。

## <a name="ui-sample"></a>UI サンプル
![Microsoft.Storage.MultiStorageAccountCombo](./media/managed-application-elements/microsoft.storage.multistorageaccountcombo.png)

## <a name="schema"></a>スキーマ
```json
{
  "name": "element1",
  "type": "Microsoft.Storage.MultiStorageAccountCombo",
  "label": {
    "prefix": "Storage account prefix",
    "type": "Storage account type"
  },
  "toolTip": {
    "prefix": "",
    "type": ""
  },
  "defaultValue": {
    "prefix": "sa",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "count": 2,
  "visible": true
}
```

## <a name="remarks"></a>解説
- `defaultValue.prefix` の値は、ストレージ アカウント名のシーケンスを生成するために、1 つまたは複数の整数と連結されます。 たとえば、`defaultValue.prefix` が **foobar** で `count` が **2** である場合、ストレージ アカウント名として **foobar1** と **foobar2** が生成されます。 生成されたストレージ アカウント名は、一意であることが自動的に検証されます。
- ストレージ アカウント名は、`count` に基づいて辞書式に生成されます。 たとえば、`count` が 10 である場合、ストレージ アカウント名は 2 桁の整数 (01、02、03 など) で終了します。
- `defaultValue.prefix` の既定値は **null** です。`defaultValue.type` の既定値は **Premium_LRS** です。
- `constraints.allowedTypes` で指定されていない型はすべて非表示となり、`constraints.excludedTypes` で指定されていない型はすべて表示されます。
`constraints.allowedTypes` と `constraints.excludedTypes` は両方とも使用できますが、同時に使用することはできません。
- `count` は、ストレージ アカウント名を生成するだけでなく、適切な乗数を要素に設定するためにも使用されます。 **2** などの静的な値のほか、他の要素からの `[steps('step1').storageAccountCount]` などの動的な値もサポートします。 既定値は **1** です。

## <a name="sample-output"></a>サンプル出力
```json
{
  "prefix": "sa",
  "count": 2,
  "resourceGroup": "rg01",
  "type": "Premium_LRS"
}
```

## <a name="next-steps"></a>次の手順
* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
