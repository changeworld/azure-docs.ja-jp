---
title: Azure Service Fabric クラスター ノードへのリモート接続 | Microsoft Docs
description: スケール セット インスタンス (Service Fabric クラスター ノード) にリモートで接続する方法を説明します。
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: aljo
ms.openlocfilehash: 3c7b3626db0e38d28513d4665a83dd7155663034
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>仮想マシン スケール セット インスタンスまたはクラスター ノードにリモート接続する
Azure で実行される Service Fabric クラスターでは、定義するクラスター ノードの種類ごとに、[仮想マシンの個別のスケールを設定](service-fabric-cluster-nodetypes.md)します。  特定のスケール セット インスタンス (またはクラスター ノード) にリモート接続することができます。  単一インスタンス VM とは異なり、スケール セット インスタンスには独自の仮想 IP アドレスがありません。 そのため、特定のインスタンスにリモート接続するために使用できる IP アドレスとポートの検索が難しい場合があります。

特定のインスタンスにリモート接続するために使用できる IP アドレスとポートを検索するには、以下の手順を実行します。

1. リモート デスクトップ プロトコル (RDP) の受信 NAT 規則を取得して、ノードの種類の仮想 IP アドレスを見つけます。

    まず、`Microsoft.Network/loadBalancers` のリソース定義の一部として定義された受信 NAT 規則の値を取得します。
    
    Azure Portal のロード バランサー ページで、**[設定]** > **[受信 NAT 規則]** の順に選択します。 これで、最初のスケール セット インスタンスにリモート接続するために使用できる IP アドレスとポートを取得できます。 
    
    ![Load Balancer][LBBlade]
    
    次の図では、IP アドレスとポートは **104.42.106.156** と **3389** です。
    
    ![NAT 規則][NATRules]

2. 特定のスケール セット インスタンスまたはノードにリモート接続するために使用できるポートを見つけます。

    スケール セット インスタンスをノードにマップします。 使用するポートを正確に判断するために、スケール セットの情報を使用します。
    
    ポートは、スケール セット インスタンスと一致する昇順で割り当てられます。 次の表では、ノードの種類が FrontEnd である前の例について、5 つのノード インスタンスの各ポートを示しています。 スケール セット インスタンスに同じマッピングを適用します。
    
    | **仮想マシン スケール セットのインスタンス** | **ポート** |
    | --- | --- |
    | FrontEnd_0 |3389 |
    | FrontEnd_1 |3390 |
    | FrontEnd_2 |3391 |
    | FrontEnd_3 |3392 |
    | FrontEnd_4 |3393 |
    | FrontEnd_5 |3394 |

3. 特定のスケール セット インスタンスにリモート接続します。

    次の図は、リモート デスクトップ接続を使用して FrontEnd_1 スケール セット インスタンスに接続する場合を示しています。
    
    ![リモート デスクトップ接続][RDP]


次の手順については、次の記事を参照してください。
* ["任意の場所にデプロイ" 機能の概要と Azure で管理されるクラスターとの比較](service-fabric-deploy-anywhere.md)に関するページを参照します。
* [クラスター セキュリティ](service-fabric-cluster-security.md)について学習します。
* 展開後にクラスター VM で [RDP ポートの範囲値を更新する](./scripts/service-fabric-powershell-change-rdp-port-range.md)
* クラスター VM の[管理者ユーザー名とパスワードを変更する](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md)

<!--Image references-->
[LBBlade]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/NATRules.png
[RDP]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/RDP.png
