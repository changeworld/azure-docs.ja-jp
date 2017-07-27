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
ms.date: 06/28/2017
ms.author: singhkay
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: c1ad80a56627695f3594f4d9b60cd623fa9bcce3
ms.contentlocale: ja-jp
ms.lasthandoff: 06/30/2017


---
# <a name="apply-policies-to-linux-vms-with-azure-resource-manager"></a>Azure Resource Manager を使用して Linux VM にポリシーを適用する
ポリシーを使用すると、さまざまな習慣や規則を企業全体に適用できます。 望ましい行動を強制することによって、組織の成功に貢献しつつ、リスクを軽減することができます。 この記事では、Azure Resource Manager ポリシーを使用して、組織の Virtual Machines の望ましい行動を定義する方法について説明します。

ポリシーの概要については、「[ポリシーを使用したリソース管理とアクセス制御](../../azure-resource-manager/resource-manager-policy.md)」を参照してください。

## <a name="define-policy-for-permitted-virtual-machines"></a>許可される Virtual Machines のポリシーを定義する
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

ポリシーのフィールドについては、[ポリシーのエイリアス](../../azure-resource-manager/resource-manager-policy.md#aliases)に関するページをを参照してください。

## <a name="define-policy-for-using-managed-disks"></a>管理ディスクを使用するためのポリシーを定義する

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

## <a name="next-steps"></a>次のステップ
* (上記の例で示すように) ポリシー規則を定義した後、ポリシー定義を作成してスコープに割り当てる必要があります。 スコープには、サブスクリプション、リソース グループ、またはリソースを使用できます。 ポータルでポリシーを割り当てる方法については、「[Use Azure portal to assign and manage resource policies](../../azure-resource-manager/resource-manager-policy-portal.md)」(Azure Portal によるリソース ポリシーの割り当てと管理) を参照してください。 REST API、PowerShell、Azure CLI でポリシーを割り当てる方法については、「[Assign and manage policies through script](../../azure-resource-manager/resource-manager-policy-create-assign.md)」(スクリプトによるポリシーの割り当てと管理) を参照してください。
* リソース ポリシーの概要については、[リソース ポリシーの概要](../../azure-resource-manager/resource-manager-policy.md)に関するページを参照してください。
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](../../azure-resource-manager/resource-manager-subscription-governance.md)」を参照してください。

