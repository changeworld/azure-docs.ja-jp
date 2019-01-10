---
title: Azure SQL Database Managed Instance に使用する VNet/サブネットのサイズを決める | Microsoft Docs
description: このトピックでは、Azure SQL Database Managed Instance のデプロイ先となるサブネットのサイズを計算する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 12/12/2018
ms.openlocfilehash: 97bb7ac79dc96d7f66ae330f5d6a603ca600f648
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54043378"
---
# <a name="determine-vnet-subnet-size-for-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance に使用する VNet のサブネット サイズを決める

Azure SQL Database Managed Instance は、Azure [仮想ネットワーク (VNet)](../virtual-network/virtual-networks-overview.md) 内にデプロイする必要があります。

VNet のサブネットにデプロイできる Managed Instance の数は、そのサブネットのサイズ (サブネット範囲) によって決まります。

マネージド インスタンスを作成する場合、Azure では、プロビジョニング中に選択された階層に応じて、仮想マシンの数を割り当てます。 これらの仮想マシンはお使いのサブネットに関連付けられているため、IP アドレスが必要です。 通常の操作とサービス メンテナンス時の高可用性を確保するために、Azure は追加の仮想マシンを割り当てることがあります。 その結果、サブネット内の必要な IP アドレスの数が、そのサブネット内のマネージド インスタンスの数より大きくなります。

仕様上、マネージド インスタンスにはサブネット内で 16 個以上の IP アドレスが必要であり、最大 256 個の IP アドレスを使用する場合があります。 その結果、サブネットの IP 範囲を定義するときに、/28 から /24 のサブネット マスクを使用することができます。

> [!IMPORTANT]
> 16 個の IP アドレスから成るサブネット サイズは、マネージド インスタンスをさらにスケールアウトするために必要な最小限の潜在能力です。プレフィックス /27 以下のサブネットを選択することを強くお勧めします。

## <a name="determine-subnet-size"></a>サブネットのサイズを決める

サブネット内に複数のマネージド インスタンスをデプロイする予定で、サブネット サイズを最適化する必要がある場合、以下のパラメーターを使用して、計算を形成します。

- Azure では、独自のニーズに応じて、サブネット内で 5 個の IP アドレスを使用します。
- 汎用インスタンスにはそれぞれ 2 つのアドレスが必要です。
- 各 Business Critical インスタンスには 4 つのアドレスが必要です

**例**:3 つの General Purpose と 2 つの Business Critical マネージド インスタンスを予定しています。 これは、5 + 3 * 2 + 2 * 4 = 19 の IP アドレスが必要であることを意味します。 IP 範囲は 2 のべき乗で定義されているため、32 (2^5) の IP アドレスの IP 範囲が必要です。 したがって、/27 サブネット マスクのサブネットを予約する必要があります。

> [!IMPORTANT]
> 今後の改善に伴い、上記の計算は古くなっていきます。

## <a name="next-steps"></a>次の手順

- 概要については、[マネージド インスタンス](sql-database-managed-instance.md)に関するページを参照してください。
- [Managed Instance の接続アーキテクチャ](sql-database-managed-instance-connectivity-architecture.md)の詳細を確認します。
- [Managed Instance のデプロイ先となる VNet の作成](sql-database-managed-instance-create-vnet-subnet.md)方法を確認します。
- DNS の問題については、[カスタム DNS の構成](sql-database-managed-instance-custom-dns.md)に関するページを参照してください。
