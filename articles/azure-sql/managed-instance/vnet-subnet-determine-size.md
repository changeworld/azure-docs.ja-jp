---
title: 必要なサブネットサイズおよび範囲を決める
titleSuffix: Azure SQL Managed Instance
description: このトピックでは、Azure SQL Managed Instance のデプロイ先となるサブネットのサイズを計算する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 02/22/2019
ms.openlocfilehash: 5eebde1fc95cb50f8ff7c13b6cbc411484ddf943
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "84039523"
---
# <a name="determine-required-subnet-size--range-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance に必要なサブネット サイズおよび範囲を決める
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance は、Azure [仮想ネットワーク (VNet)](../../virtual-network/virtual-networks-overview.md) 内にデプロイする必要があります。

VNet のサブネットにデプロイできる SQL Managed Instance の数は、そのサブネットのサイズ (サブネット範囲) によって決まります。

SQL Managed Instance を作成する場合、Azure では、プロビジョニング中に選択された階層に応じて、仮想マシンの数を割り当てます。 これらの仮想マシンはお使いのサブネットに関連付けられているため、IP アドレスが必要です。 通常の操作とサービス メンテナンス時の高可用性を確保するために、Azure は追加の仮想マシンを割り当てることがあります。 その結果、サブネット内の必要な IP アドレスの数が、そのサブネット内の SQL Managed Instance の数より大きくなります。

仕様上、SQL Managed Instance にはサブネット内で 16 個以上の IP アドレスが必要であり、最大 256 個の IP アドレスを使用する場合があります。 その結果、サブネットの IP 範囲を定義するときに、/28 から /24 までの間のサブネット マスクを使用することができます。 ネットワーク マスク ビット /28 (ネットワークあたり 14 ホスト) は、単一の汎用デプロイおよびビジネスクリティカル デプロイに適したサイズです。 マスク ビット /27 (ネットワークあたり 30 ホスト) は、同じ VNet 内での複数の SQL Managed Instance デプロイに最適です。 /26 (62 ホスト) および /24 (254 ホスト) のマスク ビット設定を使用すれば、VNet をさらに拡張して、追加の SQL Managed Instances をサポートすることができます。

> [!IMPORTANT]
> 16 個の IP アドレスから成るサブネット サイズは最小限に抑えられており、仮想コア サイズの変更などのスケーリング操作はサポートされていません。 プレフィックス /27 または最長プレフィックスのサブネットを選択することを強くお勧めします。

## <a name="determine-subnet-size"></a>サブネットのサイズを決める

サブネット内に複数の SQL Managed Instances をデプロイする予定で、サブネット サイズを最適化する必要がある場合、以下のパラメーターを使用して、計算を形成します。

- Azure では、独自のニーズに応じて、サブネット内で 5 個の IP アドレスを使用します。
- 汎用インスタンスにはそれぞれ 2 つのアドレスが必要です。
- 各 Business Critical インスタンスには 4 つのアドレスが必要です

**例**:3 つの General Purpose と 2 つの Business Critical の SQL Managed Instance を予定しています。 これは、5 + 3 * 2 + 2 * 4 = 19 の IP アドレスが必要であることを意味します。 IP 範囲は 2 のべき乗で定義されているため、32 (2^5) の IP アドレスの IP 範囲が必要です。 したがって、/27 サブネット マスクのサブネットを予約する必要があります。

> [!IMPORTANT]
> 今後の改善に伴い、上記の計算は古くなっていきます。

## <a name="next-steps"></a>次のステップ

- 概要については、[SQL Managed Instance とは何か](sql-managed-instance-paas-overview.md)に関するページを参照してください。
- [SQL Managed Instance の接続アーキテクチャ](connectivity-architecture-overview.md)の詳細を確認します。
- [SQL Managed Instance のデプロイ先となる VNet の作成](virtual-network-subnet-create-arm-template.md)方法を確認します。
- DNS の問題については、[カスタム DNS の構成](custom-dns-configure.md)に関するページを参照してください。
