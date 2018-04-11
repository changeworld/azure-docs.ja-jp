---
title: Azure の SizeSelector UI 要素 | Microsoft Docs
description: Azure Portal の Microsoft.Compute.SizeSelector UI 要素について説明します。
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
ms.openlocfilehash: 3966de95233f32a09d4799630632c2bb6a490d78
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2018
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Microsoft.Compute.SizeSelector UI 要素
1 つまたは複数の仮想マシン インスタンスのサイズを選択するコントロールです。

## <a name="ui-sample"></a>UI サンプル
![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

## <a name="schema"></a>スキーマ
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.SizeSelector",
  "label": "Size",
  "toolTip": "",
  "recommendedSizes": [
    "Standard_D1",
    "Standard_D2",
    "Standard_D3"
  ],
  "constraints": {
    "allowedSizes": [],
    "excludedSizes": []
  },
  "osPlatform": "Windows",
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2012-R2-Datacenter"
  },
  "count": 2,
  "visible": true
}
```

## <a name="remarks"></a>解説
- `recommendedSizes` には少なくとも 1 つのサイズを含める必要があります。 最初の推奨サイズが既定値として使用されます。
- 推奨されたサイズが選択した場所で使用できない場合、そのサイズは自動的にスキップされます。 代わりに、次の推奨サイズが使用されます。
- `constraints.allowedSizes` で指定されていないサイズはすべて非表示となり、`constraints.excludedSizes` で指定されていないサイズはすべて表示されます。
`constraints.allowedSizes` と `constraints.excludedSizes` は両方とも使用できますが、同時に使用することはできません。 使用可能なサイズの一覧は、[サブスクリプションの使用可能な仮想マシン サイズを一覧表示する操作](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region)を呼び出すことで決定できます。
- `osPlatform` は指定する必要があり、**Windows** と **Linux** のいずれかを使用できます。 これは、仮想マシンのハードウェア コストの決定に使用されます。
- `imageReference` はファーストパーティのイメージでは省略されますが、サードパーティのイメージでは指定されます。 これは、仮想マシンのソフトウェア コストの決定に使用されます。
- `count` は適切な乗数を要素に設定するために使用されます。 **2** などの静的な値のほか、他の要素からの `[steps('step1').vmCount]` などの動的な値もサポートします。 既定値は **1** です。

## <a name="sample-output"></a>サンプル出力
```json
"Standard_D1"
```

## <a name="next-steps"></a>次の手順
* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
