---
title: Azure における容量予約の割り当て超過 (プレビュー)
description: 容量予約において割り当て超過がどのように機能するかについて説明します。
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: 90f7c99aa82dfc91c9910309b66246439e513c88
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129532654"
---
# <a name="overallocating-capacity-reservation-preview"></a>容量予約の割り当て超過 (プレビュー)

Azure は、バーストやその他のスケール アウト シナリオを促進するために、予約容量の上限を管理するというオーバーヘッドを発生することなく、容量予約で予約した数を超えた追加の VM の関連付けを許可しています。 唯一の違いは、VM の数が予約された数量を超えた場合は、容量可用性 SLA の特典を受けないことです。 Azure で仮想マシンの要件を満たす使用可能な容量がある限り、追加の割り当ては成功します。 

容量予約グループのインスタンス ビューは、各メンバーの容量予約の利用状況を示すスナップショットを提供します。 インスタンス ビューを使って、割り当て超過がどのように機能するかを確認することができます。 

この記事では、容量予約グループ (`myCapacityReservationGroup`)、メンバーの容量予約 (`myCapacityReservation`)、グループに関連付けられている仮想マシン (*myVM1*) が作成されていることを前提とします。 詳細については、[容量予約を作成する](capacity-reservation-create.md)および[容量予約への VM の関連付けを行う](capacity-reservation-associate-vm.md)をご覧ください。

> [!IMPORTANT]
> 容量予約は現在パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することは推奨されません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。 


## <a name="instance-view-for-capacity-reservation-group"></a>容量予約グループのインスタンス ビュー 

容量予約グループのインスタンス ビューは次のようになります。 

```rest
GET 
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/myCapacityReservationGroup?$expand=instanceview&api-version=2021-04-01
```

```json
{ 
    "name": "myCapacityReservationGroup", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/myCapacityReservationGroup", 
    "type": "Microsoft.Compute/capacityReservationGroups", 
    "location": "eastus", 
    "properties": { 
        "capacityReservations": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/MYCAPACITYRESERVATIONGROUP/capacityReservations/MYCAPACITYRESERVATION" 
            } 
        ], 
        "virtualMachinesAssociated": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM1" 
            } 
        ], 
        "instanceView": { 
            "capacityReservations": [ 
                { 
                    "name": "myCapacityReservation", 
"utilizationInfo": { 
                        "virtualMachinesAllocated": [ 
                            { 
                                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM1" 
                            } 
                        ] 
                    }, 
                    "statuses": [ 
                        { 
                            "code": "ProvisioningState/succeeded", 
                            "level": "Info", 
                            "displayStatus": "Provisioning succeeded", 
                            "time": "<time>" 
                        } 
                    ] 
                } 
            ] 
        } 
    } 
} 
```

たとえば、*myVM2* という名前の別の仮想マシンを作成し、上記の容量予約グループに関連付けるとします。 

今度は容量予約グループのインスタンス ビューは次のようになります。 

```json
{ 
    "name": "myCapacityReservationGroup", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/myCapacityReservationGroup", 
    "type": "Microsoft.Compute/capacityReservationGroups", 
    "location": "eastus", 
    "properties": { 
        "capacityReservations": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/MYCAPACITYRESERVATIONGROUP/capacityReservations/MYCAPACITYRESERVATION" 
            } 
        ], 
        "virtualMachinesAssociated": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM1" 
            }, 
 { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM2" 
            } 
        ], 
        "instanceView": { 
            "capacityReservations": [ 
                { 
                    "name": "myCapacityReservation", 
"utilizationInfo": { 
                        "virtualMachinesAllocated": [ 
                            { 
                                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM1" 
                            }, 
{ 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM2" 
            } 
                        ] 
                    }, 
                    "statuses": [ 
                        { 
                            "code": "ProvisioningState/succeeded", 
                            "level": "Info", 
                            "displayStatus": "Provisioning succeeded", 
                            "time": "<time>" 
                        } 
                    ] 
                } 
            ] 
        } 
    } 
} 
``` 

`virtualMachinesAllocated` (2) が `capacity` (1)よりも長い点に注目してください。 この有効な状態は、*割り当て超過* と呼ばれます。 

> [!IMPORTANT]
> 単に容量予約がすべて消費されているだけでは、Azure は割り当てを停止しません。 Azure で使用可能な容量がある限り、自動スケールルール、一時的なスケールアウト、および関連する必要な機能は、予約済み容量の数量を超えて機能します。  


## <a name="states-and-considerations"></a>状態および留意事項  

容量予約には、次の 3 つの有効な状態があります。 

| State  | Status  | 考慮事項  |
|---|---|---|
| 予約済みの容量が使用可能である  | `virtualMachinesAllocated` の長さが `capacity` よりも  <  である  | 予約した容量はすべて必要ですか? コストを削減するために必要に応じて容量を減らしてください。  |
| 予約した容量が消費されている  | `virtualMachinesAllocated` の長さが `capacity` と  ==  である  | 既存の VM の一部が解放されない限り、追加の VM はキャパシティ SLA の適用を受けません。 必要に応じて、追加で予約された VM が SLA の適用を受けるように、容量を増やしてみてください。  |
| 予約した容量が割り当て超過になっている  | `virtualMachinesAllocated` の長さが `capacity` よりも  >  である  | 追加された VM はキャパシティ SLA の適用を受けません。 また、`virtualMachinesAllocated` の長さから `capacity` の長さを差し引いた数の VM は、解放されたとしても SLA の適用を受けません。 必要に応じて、より多くの既存の VM に キャパシティ SLA が適用されるように、容量を増やしてください。  |


## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [容量予約から VM を削除する方法を確認する](capacity-reservation-remove-vm.md)
