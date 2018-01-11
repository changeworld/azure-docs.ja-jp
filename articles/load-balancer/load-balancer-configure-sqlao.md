---
title: "SQL Server Always On 用にロード バランサーを構成する |Microsoft Docs"
description: "SQL Server Always On で動作するようにロード バランサーを構成し、PowerShell を使って SQL Server の実装用のロード バランサーを作成する方法を説明します"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: d7bc3790-47d3-4e95-887c-c533011e4afd
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 5e890f8314c8f191dbfa6c6818d810b91d0e829d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2017
---
# <a name="configure-a-load-balancer-for-sql-server-always-on"></a>SQL Server Always On 用にロード バランサーを構成する

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

SQL Server Always On 可用性グループは、内部ロード バランサーを使って実行できるようになりました。 可用性グループは、高可用性とディザスター リカバリーを目的とした SQL Server の主要なソリューションです。 可用性グループ リスナーによって、クライアント アプリケーションは、構成されているレプリカの数に関係なく、プライマリ レプリカにシームレスに接続できます。

リスナー (DNS) 名は、負荷分散された IP アドレスにマップされます。 Azure Load Balancer は、レプリカ セット内のプライマリ サーバーに対してのみ、受信トラフィックを送信します。

SQL Server AlwaysOn (リスナー) エンドポイントでは、内部ロード バランサーのサポートを使用できます。 リスナーのアクセシビリティを制御できるようになりました。 仮想ネットワーク内の特定のサブネットから負荷分散された IP アドレスを選ぶことができます。

リスナーで内部ロード バランサーを使うことで、SQL Server エンドポイント (例: Server=tcp:ListenerName,1433;Database=DatabaseName) には次の場所からのみアクセスできます。

* 同じ仮想ネットワーク内のサービスと VM。
* 接続されたオンプレミス ネットワークのサービスと VM。
* 相互接続された仮想ネットワークのサービスと VM。

![SQL Server Always On の内部ロード バランサー](./media/load-balancer-configure-sqlao/sqlao1.png)

## <a name="add-an-internal-load-balancer-to-the-service"></a>サービスに内部ロード バランサーを追加する

1. 次の例では、"Subnet-1" という名前のサブネットを含む仮想ネットワークを構成します。

    ```powershell
    Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc
    ```
2. 各 VM で、内部ロード バランサーの負荷分散エンドポイントを追加します。

    ```powershell
    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -
    DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM
    ```

    前の例には、クラウド サービス "Sqlsvc" で実行する "sqlsvc1" および "sqlsvc2" という 2 つの VM があります。 `DirectServerReturn` スイッチで内部ロード バランサーを作成した後、内部ロード バランサーに負荷分散エンドポイントを追加します。 負荷分散エンドポイントにより、SQL Server は可用性グループのリスナーを構成できます。

SQL Server Always On について詳しくは、「[Azure の AlwaysOn 可用性グループに使用する内部ロード バランサーの構成](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md)」をご覧ください。

## <a name="see-also"></a>関連項目
* [パブリック ロード バランサーの構成の開始](load-balancer-get-started-internet-arm-ps.md)
* [内部ロード バランサーの構成の開始](load-balancer-get-started-ilb-arm-ps.md)
* [ロード バランサー分散モードの構成](load-balancer-distribution-mode.md)
* [ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)
