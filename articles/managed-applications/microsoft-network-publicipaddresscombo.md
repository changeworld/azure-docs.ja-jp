---
title: Azure の PublicIpAddressCombo UI 要素 | Microsoft Docs
description: Azure Portal の Microsoft.Network.PublicIpAddressCombo UI 要素について説明します。
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
ms.date: 04/30/2018
ms.author: tomfitz
ms.openlocfilehash: c308b6626f9c37b3928107c4c03e9e0a5da12e6f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Microsoft.Network.PublicIpAddressCombo UI 要素
新規または既存のパブリック IP アドレスを選択するコントロールのグループです。

## <a name="ui-sample"></a>UI サンプル
![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- ユーザーがパブリック IP アドレスとして [なし] を選択した場合、[ドメイン名ラベル] ボックスは表示されません。
- ユーザーが既存のパブリック IP アドレスを選択した場合、[ドメイン名ラベル] ボックスは無効になります。 その値は、選択した IP アドレスのドメイン名ラベルになります。
- ドメイン名のサフィックス (たとえば、westus.cloudapp.azure.com) は、選択した場所に基づいて自動的に更新されます。

## <a name="schema"></a>スキーマ
```json
{
  "name": "element1",
  "type": "Microsoft.Network.PublicIpAddressCombo",
  "label": {
    "publicIpAddress": "Public IP address",
    "domainNameLabel": "Domain name label"
  },
  "toolTip": {
    "publicIpAddress": "",
    "domainNameLabel": ""
  },
  "defaultValue": {
    "publicIpAddressName": "ip01",
    "domainNameLabel": "mydomain"
  },
  "constraints": {
    "required": {
      "domainNameLabel": true
    }
  },
  "options": {
    "hideNone": false,
    "hideDomainNameLabel": false,
    "hideExisting": false,
    "zone": 3
  },
  "visible": true
}
```

## <a name="remarks"></a>解説
- `constraints.required.domainNameLabel` が **true** に設定されている場合、新しいパブリック IP アドレスを作成するときに、ドメイン名ラベルを指定する必要があります。 ラベルがない既存のパブリック IP アドレスは選択できません。
- `options.hideNone` が **true** に設定されている場合、パブリック IP アドレスとして **[なし]** を選択するオプションは表示されません。 既定値は **false** です。
- `options.hideDomainNameLabel` が **true** に設定されている場合、ドメイン名ラベルのテキスト ボックスは表示されません。 既定値は **false** です。
- `options.hideExisting` が true である場合、既存のパブリック IP アドレスを選択することはできません。 既定値は **false** です。
- `zone` の場合、指定されたゾーンのパブリック IP アドレスまたはゾーン回復性のパブリック IP アドレスのみを使用できます。

## <a name="sample-output"></a>サンプル出力
ユーザーがパブリック IP アドレスを選択しなかった場合、出力は次のようになります。
```json
{
  "newOrExistingOrNone": "none"
}
```

ユーザーが新規または既存の IP アドレスを選択した場合、出力は次のようになります。
```json
{
  "name": "ip01",
  "resourceGroup": "rg01",
  "domainNameLabel": "mydomain",
  "publicIPAllocationMethod": "Dynamic",
  "newOrExistingOrNone": "new"
}
```
- `options.hideNone` が **true** として指定されると、`newOrExistingOrNone`は**新規**または**既存** の値のみを持ちます。
- `options.hideDomainNameLabel` が **true** として指定されると、`domainNameLabel` は宣言されません。

## <a name="next-steps"></a>次の手順
* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
