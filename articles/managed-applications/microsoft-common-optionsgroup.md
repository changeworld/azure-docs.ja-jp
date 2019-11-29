---
title: Azure OptionsGroup UI 要素 | Microsoft Docs
description: Azure Portal の Microsoft.Common.OptionsGroup UI 要素について説明します。 管理対象アプリケーションをデプロイするとき、利用できるオプションからユーザーが選択できるようにします。
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 1210c24687c0cd1f38e33674d297dd37fe4d2995
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151884"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Microsoft.Common.OptionsGroup UI 要素

使用可能なオプションの行を備えた選択コントロールです。

## <a name="ui-sample"></a>UI サンプル

![Microsoft.Common.OptionsGroup](./media/managed-application-elements/microsoft.common.optionsgroup.png)

## <a name="schema"></a>スキーマ

```json
{
  "name": "element1",
  "type": "Microsoft.Common.OptionsGroup",
  "label": "Some options group",
  "defaultValue": "Value two",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Value one",
        "value": "one"
      },
      {
        "label": "Value two",
        "value": "two"
      }
    ],
    "required": true
  },
  "visible": true
}
```

## <a name="sample-output"></a>サンプル出力

```json
"two"
```

## <a name="remarks"></a>解説

- `constraints.allowedValues` のラベルは項目の表示テキストであり、その値は要素が選択された際の出力値です。
- 指定する場合、既定値は `constraints.allowedValues` 内に存在するラベルである必要があります。 指定しない場合、`constraints.allowedValues` 内の最初の項目が既定で選択されます。 既定値は **null** です。
- `constraints.allowedValues` には、少なくとも 1 つの項目が必要です。

## <a name="next-steps"></a>次の手順

* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
