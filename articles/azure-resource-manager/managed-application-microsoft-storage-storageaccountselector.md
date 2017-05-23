---
title: "Azure マネージ アプリケーションの StorageAccountSelector UI 要素 | Microsoft Docs"
description: "Azure マネージ アプリケーションの Microsoft.Storage.StorageAccountSelector UI 要素について説明します。"
services: azure-resource-manager
documentationcenter: na
author: tabrezm
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2017
ms.author: tabrezm;tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 2d64cfcd958f17171e9ef1a38777f202bdfda774
ms.contentlocale: ja-jp
ms.lasthandoff: 05/11/2017


---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Microsoft.Storage.StorageAccountSelector UI 要素
新規または既存のストレージ アカウントを選択するコントロールです。

## <a name="ui-sample"></a>UI サンプル
![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

## <a name="schema"></a>スキーマ
```json
{
  "name": "element1",
  "type": "Microsoft.Storage.StorageAccountSelector",
  "label": "Storage account",
  "toolTip": "",
  "defaultValue": {
    "name": "storageaccount01",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "options": {
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="remarks"></a>解説
- 指定した場合、`defaultValue.name` は一意であることが自動的に検証されます。 ストレージ アカウント名が一意でない場合、ユーザーは別の名前を指定するか、既存のストレージ アカウントを選択する必要があります。
- `defaultValue.type` の既定値は **Premium_LRS** です。
- `constraints.allowedTypes` で指定されていない型はすべて非表示となり、`constraints.excludedTypes` で指定されていない型はすべて表示されます。
`constraints.allowedTypes` と `constraints.excludedTypes` は両方とも使用できますが、同時に使用することはできません。
- `options.hideExisting` が **true** である場合、既存のストレージ アカウントは選択できません。 既定値は **false** です。


## <a name="sample-output"></a>サンプル出力
```json
{
  "name": "storageaccount01",
  "resourceGroup": "rg01",
  "type": "Premium_LRS",
  "newOrExisting": "new"
}
```

## <a name="next-steps"></a>次のステップ
* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](managed-application-createuidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](managed-application-createuidefinition-elements.md)」を参照してください。

