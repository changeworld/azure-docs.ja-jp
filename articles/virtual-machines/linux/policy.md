---
title: "Azure の Linux VM にセキュリティとポリシーを適用する | Microsoft Docs"
description: "Azure Resource Manager Linux 仮想マシンにポリシーを適用する方法"
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 06778ab4-f8ff-4eed-ae10-26a276fc3faa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: singhkay
ms.openlocfilehash: 72abb01a3ce7f4dea2ee97219e9a406c69cda7c5
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="apply-policies-to-linux-vms-with-azure-resource-manager"></a>Azure Resource Manager を使用して Linux VM にポリシーを適用する
ポリシーを使用すると、さまざまな習慣や規則を企業全体に適用できます。 望ましい行動を強制することによって、組織の成功に貢献しつつ、リスクを軽減することができます。 この記事では、Azure Resource Manager ポリシーを使用して、組織の Virtual Machines の望ましい行動を定義する方法について説明します。

ポリシーの概要については、「[Azure Policy とは](../../azure-policy/azure-policy-introduction.md)」を参照してください。

## <a name="permitted-virtual-machines"></a>許可されている仮想マシン
組織の仮想マシンがアプリケーションと互換性があることを保証するために、許可されるオペレーティング システムを制限することができます。 次のポリシーの例は、Ubuntu 14.04.2-LTS 仮想マシンの作成のみを許可します。

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "in": [
          "Microsoft.Compute/disks",
          "Microsoft.Compute/virtualMachines",
          "Microsoft.Compute/VirtualMachineScaleSets"
        ]
      },
      {
        "not": {
          "allOf": [
            {
              "field": "Microsoft.Compute/imagePublisher",
              "in": [
                "Canonical"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "UbuntuServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "14.04.2-LTS"
              ]
            },
            {
              "field": "Microsoft.Compute/imageVersion",
              "in": [
                "latest"
              ]
            }
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

任意の Ubuntu LTS イメージを許可するには、ワイルドカードを使用して前のポリシーを変更します。 

```json
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

ポリシーのフィールドについては、[ポリシーのエイリアス](../../azure-policy/policy-definition.md#aliases)に関するページをを参照してください。

## <a name="managed-disks"></a>管理ディスク

必ず管理ディスクを使用するように設定するには、次のポリシーを使用します。

```json
{
  "if": {
    "anyOf": [
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/osDisk.uri",
            "exists": true
          }
        ]
      },
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/VirtualMachineScaleSets"
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osDisk.vhdContainers",
                "exists": true
              },
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl",
                "exists": true
              }
            ]
          }
        ]
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="images-for-virtual-machines"></a>仮想マシンのイメージ

セキュリティ上の理由から、承認済みのカスタム イメージのみを環境にデプロイすることを必須にすることができます。 また、承認済みのイメージを含むリソース グループ、または特定の承認済みイメージを指定できます。

次の例では、承認済みリソース グループのイメージを必須にしています。

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in": [
                    "Microsoft.Compute/virtualMachines",
                    "Microsoft.Compute/VirtualMachineScaleSets"
                ]
            },
            {
                "not": {
                    "field": "Microsoft.Compute/imageId",
                    "contains": "resourceGroups/CustomImage"
                }
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
} 
```

次の例では、承認済みイメージ ID を指定しています。

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>仮想マシン拡張機能

特定の種類の拡張機能について使用を禁止することができます。 たとえば、ある拡張機能が特定のカスタム仮想マシンのイメージと互換性がない場合などです。 特定の拡張機能をブロックする例を次に示します。 発行元と種類を使用して、ブロックする拡張機能を決定します。

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "equals": "{extension-type}"

      }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```


## <a name="next-steps"></a>次のステップ
* (上記の例で示すように) ポリシー規則を定義した後、ポリシー定義を作成してスコープに割り当てる必要があります。 スコープには、サブスクリプション、リソース グループ、またはリソースを使用できます。 ポリシーを割り当てるには、[Azure Portal を使用したリソース ポリシーの割り当てと管理](../../azure-policy/assign-policy-definition.md)、[PowerShell を使用したポリシーの割り当て](../../azure-policy/assign-policy-definition-ps.md)、または [Azure CLI を使用したポリシーの割り当て](../../azure-policy/assign-policy-definition-cli.md)に関する各ページをご覧ください。
* リソース ポリシーの概要については、「[Azure Policy とは](../../azure-policy/azure-policy-introduction.md)」を参照してください。
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](../../azure-resource-manager/resource-manager-subscription-governance.md)」を参照してください。
