---
title: マネージド インスタンスのパブリック エンドポイントをセキュリティで保護する - Azure SQL Database マネージド インスタンス | Microsoft Docs
description: マネージド インスタンスを使用して Azure のパブリック エンドポイントを安全に使用する
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 04/16/2019
ms.openlocfilehash: 9d5a3d18e8a7d3c5a6cb08e16e74dd4fbda9ca31
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013027"
---
# <a name="using-azure-sql-database-managed-instance-securely-with-public-endpoint"></a>パブリック エンドポイントで安全に Azure SQL Database マネージド インスタンスを使用する

Azure SQL Database マネージド インスタンスは、[パブリック エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)でユーザー接続を提供するために有効にされる場合があります。 この記事では、この構成をより安全にする方法についてのガイダンスを提供します。

## <a name="scenarios"></a>シナリオ

マネージド インスタンスでは、その仮想ネットワーク内からの接続を有効にするために、プライベート エンドポイントを提供します。 最大の分離を提供するのが既定のオプションです。 しかし、パブリック エンドポイントの接続が必要なシナリオがいくつかあります。

- PaaS オファリングのみの複数テナントとの統合。
- VPN を使用するよりも高いデータ交換のスループット。
- 会社のポリシーで企業ネットワーク内の PaaS が禁止されている。

## <a name="deploying-managed-instance-for-public-endpoint-access"></a>パブリック エンドポイント アクセスに対してマネージド インスタンスをデプロイする

必須ではありませんが、パブリック エンドポイント アクセスを使ったマネージド インスタンス用の共通デプロイ モデルは、専用の分離された仮想ネットワークでインスタンスを作成するためのものです。 この構成では、仮想ネットワークは仮想クラスターの分離のためだけに使用されます。 マネージド インスタンスの IP アドレス空間が企業ネットワークの IP アドレス空間と重複していても、関係ありません。

## <a name="securing-data-in-motion"></a>移動中のデータをセキュリティで保護する

クライアント ドライバーで暗号化をサポートする場合、マネージド インスタンスのデータ トラフィックは常に暗号化されます。 マネージド インスタンスとその他の Azure Virtual Machines または Azure サービスとの間のデータは、Azure のバックボーンから移動されることはありません。 オンプレミスのネットワーク接続に対するマネージド インスタンスがある場合は、Microsoft ピアリングで Express Route を使用することをお勧めします。 Express Route は、パブリック インターネット経由でデータを移動しないようにするのに役立ちます (マネージド インスタンス プライベート接続の場合、プライベート ピアリングのみを使用できます)。

## <a name="locking-down-inbound-and-outbound-connectivity"></a>インバウンド接続とアウトバウンド接続をロック ダウンする

次の図は、推奨されるセキュリティ構成を示しています。

![managed-instance-vnet.png](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

Managed Instance には、[専用パブリック エンドポイント アドレス](sql-database-managed-instance-find-management-endpoint-ip-address.md)があります。 この IP アドレスは、アウトバウンド接続を制限するために、クライアント側のアウトバウンド ファイアウォールとネットワーク セキュリティ グループの規則で設定する必要があります。

マネージド インスタンスへのトラフィックが信頼できる発行元から確実に取得されるようにするには、既知の IP アドレスを持つソースから接続することをお勧めします。 ネットワーク セキュリティ グループを使用して、ポート 3342 でのマネージド インスタンス パブリック エンドポイントへのアクセスを制限します。

クライアントがオンプレミスのネットワークから接続を開始する必要がある場合、送信元アドレスは既知の IP のセットに変換されることを確認します。 これが実現できない場合 (例: 一般的なシナリオでのモバイル従業員)、[ポイント対サイト VPN 接続とプライベート エンドポイント](sql-database-managed-instance-configure-p2s.md)を使用することをお勧めします。

接続が Azure から開始される場合、トラフィックは既知で割り当てられている [VIP](../virtual-network/virtual-networks-reserved-public-ip.md) (例: Virtual Machines) からのものであることが推奨されます。 VIP アドレスを管理しやすくするために、顧客は[パブリック IP アドレスのプレフィックス](../virtual-network/public-ip-address-prefix.md)を使用することを考える可能性があります。