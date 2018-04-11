---
title: Azure StorageAccountSelector UI 要素 | Microsoft Docs
description: Azure Portal の Microsoft.Storage.StorageAccountSelector UI 要素について説明します。
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
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: ca66b788af68699b4750e1e2826b6a6b104c72c7
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2018
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

## <a name="next-steps"></a>次の手順
* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
