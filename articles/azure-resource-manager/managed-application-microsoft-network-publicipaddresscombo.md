---
title: "Azure マネージ アプリケーションの PublicIpAddressCombo UI 要素 | Microsoft Docs"
description: "Azure マネージ アプリケーションの Microsoft.Network.PublicIpAddressCombo UI 要素について説明します。"
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
ms.openlocfilehash: 71d8249046ed74f67514a93ece4efc2996ad9b4c
ms.contentlocale: ja-jp
ms.lasthandoff: 05/11/2017


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
    "domainNameLabel": "foobar"
  },
  "constraints": {
    "required": {
      "domainNameLabel": true
    }
  },
  "options": {
    "hideNone": false,
    "hideDomainNameLabel": false,
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="remarks"></a>解説
- `constraints.required.domainNameLabel` が **true** に設定されている場合、新しいパブリック IP アドレスを作成するときに、ドメイン名ラベルを指定する必要があります。 ラベルがない既存のパブリック IP アドレスは選択できません。
- `options.hideNone` が **true** に設定されている場合、パブリック IP アドレスとして **[なし]** を選択するオプションは表示されません。 既定値は **false** です。
- `options.hideDomainNameLabel` が **true** に設定されている場合、ドメイン名ラベルのテキスト ボックスは表示されません。 既定値は **false** です。
- `options.hideExisting` が true である場合、既存のパブリック IP アドレスを選択することはできません。 既定値は **false** です。

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
  "domainNameLabel": "foobar",
  "newOrExistingOrNone": "new"
}
```
- `options.hideNone` が指定されると、`newOrExistingOrNone` は常に **none** を返します。
- `options.hideDomainNameLabel` が指定されると、`domainNameLabel` は宣言されません。

## <a name="next-steps"></a>次のステップ
* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](managed-application-createuidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](managed-application-createuidefinition-elements.md)」を参照してください。

