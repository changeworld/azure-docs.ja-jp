---
title: "Azure マネージ アプリケーションの VirtualNetworkCombo UI 要素 | Microsoft Docs"
description: "Azure マネージ アプリケーションの Microsoft.Network.VirtualNetworkCombo UI 要素について説明します。"
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
ms.openlocfilehash: c17ef740dcc709b5b344c4e60ef997a948b2e5de
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Microsoft.Network.VirtualNetworkCombo UI 要素
新規または既存の仮想ネットワークを選択するコントロールのグループです。 この要素は、[Azure マネージ アプリケーションを作成する](publish-service-catalog-app.md)ときに使用します。

## <a name="ui-sample"></a>UI サンプル
![Microsoft.Network.VirtualNetworkCombo](./media/managed-application-elements/microsoft.network.virtualnetworkcombo.png)

- 上部のワイヤーフレームでは、新しい仮想ネットワークが選択されており、ユーザーは各サブネットの名前とアドレスのプレフィックスをカスタマイズすることができます。 この場合、サブネットの構成は省略可能です。
- 下部のワイヤーフレームでは、既存の仮想ネットワークが選択されており、ユーザーはデプロイ テンプレートに必要な各サブネットを既存のサブネットにマップする必要があります。 この場合、サブネットを構成する必要があります。

## <a name="schema"></a>スキーマ
```json
{
  "name": "element1",
  "type": "Microsoft.Network.VirtualNetworkCombo",
  "label": {
    "virtualNetwork": "Virtual network",
    "subnets": "Subnets"
  },
  "toolTip": {
    "virtualNetwork": "",
    "subnets": ""
  },
  "defaultValue": {
    "name": "vnet01",
    "addressPrefixSize": "/16"
  },
  "constraints": {
    "minAddressPrefixSize": "/16"
  },
  "options": {
    "hideExisting": false
  },
  "subnets": {
    "subnet1": {
      "label": "First subnet",
      "defaultValue": {
        "name": "subnet-1",
        "addressPrefixSize": "/24"
      },
      "constraints": {
        "minAddressPrefixSize": "/24",
        "minAddressCount": 12,
        "requireContiguousAddresses": true
      }
    },
    "subnet2": {
      "label": "Second subnet",
      "defaultValue": {
        "name": "subnet-2",
        "addressPrefixSize": "/26"
      },
      "constraints": {
        "minAddressPrefixSize": "/26",
        "minAddressCount": 8,
        "requireContiguousAddresses": true
      }
    }
  },
  "visible": true
}
```

## <a name="remarks"></a>解説
- `defaultValue.addressPrefixSize` を指定した場合、このサイズの最初の重複しないアドレスのプレフィックスが、ユーザーのサブスクリプションにある既存の仮想ネットワークに基づいて自動的に決定されます。
- `defaultValue.name` と `defaultValue.addressPrefixSize` の既定値は **null** です。
- `constraints.minAddressPrefixSize` を指定する必要があります。 アドレス空間が指定した値よりも小さい既存の仮想ネットワークは選択できません。
- `subnets` を指定する必要があるほか、サブネットごとに `constraints.minAddressPrefixSize` を指定する必要があります。
- 新しい仮想ネットワークを作成するときは、その仮想ネットワークのアドレス プレフィックスと各 `addressPrefixSize` に基づいて、各サブネットのアドレス プレフィックスが自動的に計算されます。
- 既存の仮想ネットワークを使用するときは、各 `constraints.minAddressPrefixSize` よりも小さいサブネットは選択できません。 さらに、`minAddressCount` を指定した場合、少なくともその個数の使用可能なアドレスが含まれないサブネットは選択できません。
既定値は **0** です。 使用可能なアドレスを連続させるには、`requireContiguousAddresses` に **true** を設定します。 既定値は **true**です。
- 既存の仮想ネットワークでのサブネットの作成はサポートされていません。
- `options.hideExisting` が **true** である場合、ユーザーは既存の仮想ネットワークを選択できません。 既定値は **false** です。

## <a name="sample-output"></a>サンプル出力
```json
{
  "name": "vnet01",
  "resourceGroup": "rg01",
  "addressPrefixes": ["10.0.0.0/16"],
  "newOrExisting": "new",
  "subnets": {
    "subnet1": {
      "name": "subnet-1",
      "addressPrefix": "10.0.0.0/24",
      "startAddress": "10.0.0.1"
    },
    "subnet2": {
      "name": "subnet-2",
      "addressPrefix": "10.0.1.0/26",
      "startAddress": "10.0.1.1"
    }
  }
}
```

## <a name="next-steps"></a>次のステップ
* マネージ アプリケーションの概要については、「[Azure マネージ アプリケーションの概要](overview.md)」を参照してください。
* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
