---
title: 接続のアーキテクチャ
description: このドキュメントでは、Azure 内外からのデータベース接続のための Azure SQL の接続アーキテクチャを説明します。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: fasttrack-edit
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: b4e7d827536245a22d168c7d9923c2e5b82830b0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82111795"
---
# <a name="azure-sql-connectivity-architecture"></a>Azure SQL の接続アーキテクチャ
> [!NOTE]
> この記事は Azure SQL サーバーのほか、その Azure SQL サーバーに作成される SQL Database と SQL Data Warehouse の両方に当てはまります。 わかりやすいように、SQL Database という言葉で SQL Database と SQL Data Warehouse の両方を言い表します。

> [!IMPORTANT]
> この記事は、**Azure SQL Database Managed Instance** には "*適用されません*"。 [マネージド インスタンスの接続アーキテクチャ](sql-database-managed-instance-connectivity-architecture.md)に関するページを参照してください。

この記事では、Azure SQL Database または SQL Data Warehouse にネットワーク トラフィックを誘導するさまざまなコンポーネントのアーキテクチャについて説明します。 さまざまな接続ポリシーと、それが Azure 内から接続するクライアントと Azure 外から接続するクライアントに与える影響について説明します。 

## <a name="connectivity-architecture"></a>接続のアーキテクチャ

次の図は、Azure SQL Database の接続アーキテクチャの概要です。

![アーキテクチャの概要](./media/sql-database-connectivity-architecture/connectivity-overview.png)

次の手順では、Azure SQL データベースへの接続を確立する方法について説明します。

- クライアントはゲートウェイに接続します。ゲートウェイにはパブリック IP アドレスが与えられており、ポート 1433 で待ち受けます。
- 有効な接続ポリシーによっては、ゲートウェイがトラフィックを正しいデータベース クラスターにリダイレクトまたはプロキシします。
- データベース クラスター内では、トラフィックは適切な Azure SQL データベースに転送されます。

## <a name="connection-policy"></a>接続ポリシー

Azure SQL Database は、SQL Database サーバーの接続ポリシー設定について次の 3 つのオプションをサポートしています。

- **リダイレクト (推奨):** クライアントは、データベースをホストしているノードへの直接接続を確立します。これにより、待機時間が短縮され、スループットが向上します。 接続でこのモードを使用するには、クライアントで次を行う必要があります。
   - 11000 から 11999 の範囲のポートで、クライアントから、リージョン内のすべての Azure SQL IP アドレスへのアウトバウンド通信を許可します。 SQL のサービス タグを使用すると、この管理が簡単になります。  
   - ポート 1433 で、クライアントから、Azure SQL Database ゲートウェイの IP アドレスへのアウトバウンド通信を許可します。

- **プロキシ:** このモードでは、すべての接続が Azure SQL Database ゲートウェイ経由でプロキシ化されるため、待機時間が長くなり、スループットが低下します。 接続でこのモードを使用するには、クライアントのポート 1433 で、クライアントから、Azure SQL Database ゲートウェイの IP アドレスへのアウトバウンド通信を許可する必要があります。

- **既定値:** これは、明示的に接続ポリシーを `Proxy` または `Redirect` に変更しない限り、作成後のすべてのサーバーで有効になる接続ポリシーです。 Azure の内部からの (たとえば、Azure 仮想マシンからの) すべてのクライアント接続の既定のポリシーは `Redirect` であり、外部からのすべてのクライアント接続 (たとえば、ローカル ワークステーションからの接続) の既定のポリシーは `Proxy` です。

 待機時間を最小化してスループットを最大化するために、`Proxy` 接続ポリシーよりも `Redirect` 接続ポリシーを強くお勧めします。ただし、前述のように、ネットワーク トラフィックを許可するための追加要件を満たす必要があります。 クライアントが Azure 仮想マシンの場合は、ネットワーク セキュリティ グループ (NSG) と[サービス タグ](../virtual-network/security-overview.md#service-tags)を使用してこれを実現できます。 クライアントがオンプレミスのワークステーションから接続している場合は、ネットワーク管理者と協力して、企業のファイアウォールを通過するネットワーク トラフィックを許可することが必要になる必要があります。

## <a name="connectivity-from-within-azure"></a>Azure 内からの接続

Azure 内から接続する場合、接続には既定で `Redirect` の接続ポリシーが与えられます。 `Redirect` のポリシーとは、TCP セッションが Azure SQL データベースに対して確立された後に、宛先の仮想 IP を Azure SQL Database ゲートウェイの IP からクラスターの IP に変更して、正しいデータベース クラスターにリダイレクトすることを意味します。 その後、すべての後続パケットは Azure SQL Database ゲートウェイを迂回し、クラスターに直接送信されます。 次の図にこのトラフィックの流れを示します。

![アーキテクチャの概要](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Azure 外からの接続

Azure 外から接続する場合、接続には既定で `Proxy` の接続ポリシーが与えられます。 `Proxy` のポリシーとは、TCP セッションが Azure SQL Database ゲートウェイ経由で確立し、すべての後続パケットがゲートウェイ経由で送信されることを意味します。 次の図にこのトラフィックの流れを示します。

![アーキテクチャの概要](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> [DAC による接続](https://docs.microsoft.com/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators?view=sql-server-2017#connecting-with-dac)を有効にするには、追加で TCP ポート 1434、14000 から 14999 を開きます


## <a name="azure-sql-database-gateway-ip-addresses"></a>Azure SQL Database ゲートウェイ IP アドレス

以下の表は、ゲートウェイの IP アドレスをリージョン別にまとめたものです。 Azure SQL Database に接続するには、そのリージョンの**すべての**ゲートウェイとの間でやり取りされるネットワーク トラフィックを許可する必要があります。

特定のリージョンの新しいゲートウェイにトラフィックを移行する方法の詳細については、次の記事をご覧ください。「[Azure SQL Database traffic migration to newer Gateways](sql-database-gateway-migration.md)」(Azure SQL Database トラフィックの新しいゲートウェイへの移行)


| リージョン名          | ゲートウェイ IP アドレス |
| --- | --- |
| オーストラリア中部    | 20.36.105.0 |
| オーストラリア中部 2   | 20.36.113.0 |
| オーストラリア東部       | 13.75.149.87、40.79.161.1 |
| オーストラリア東南部 | 191.239.192.109、13.73.109.251 |
| ブラジル南部         | 104.41.11.5、191.233.200.14 |
| カナダ中部       | 40.85.224.249      |
| カナダ東部          | 40.86.226.166      |
| 米国中部           | 13.67.215.62、52.182.137.15、23.99.160.139、104.208.16.96、104.208.21.1 | 
| 中国東部           | 139.219.130.35     |
| 中国東部 2         | 40.73.82.1         |
| 中国北部          | 139.219.15.17      |
| 中国北部 2        | 40.73.50.0         |
| 東アジア            | 191.234.2.139、52.175.33.150、13.75.32.4 |
| 米国東部              | 40.121.158.30、40.79.153.12、191.238.6.43、40.78.225.32 |
| 米国東部 2            | 40.79.84.180、52.177.185.181、52.167.104.0、191.239.224.107、104.208.150.3 | 
| フランス中部       | 40.79.137.0、40.79.129.1 |
| ドイツ中部      | 51.4.144.100       |
| ドイツ北東部   | 51.5.144.179       |
| インド中部        | 104.211.96.159     |
| インド南部          | 104.211.224.146    |
| インド西部           | 104.211.160.80     |
| 東日本           | 13.78.61.196、40.79.184.8、13.78.106.224、191.237.240.43、40.79.192.5 | 
| 西日本           | 104.214.148.156、40.74.100.192、191.238.68.11、40.74.97.10 | 
| 韓国中部        | 52.231.32.42       |
| 韓国南部          | 52.231.200.86      |
| 米国中北部     | 23.96.178.199、23.98.55.75、52.162.104.33 |
| 北ヨーロッパ         | 40.113.93.91、191.235.193.75、52.138.224.1 | 
| ノルウェー東部          | 51.120.96.0        |
| ノルウェー西部          | 51.120.216.0       |
| 南アフリカ北部   | 102.133.152.0      |
| 南アフリカ西部    | 102.133.24.0       |
| 米国中南部     | 13.66.62.124、23.98.162.75、104.214.16.32   | 
| 東南アジア      | 104.43.15.0、23.100.117.95、40.78.232.3   | 
| アラブ首長国連邦中部          | 20.37.72.64        |
| アラブ首長国連邦北部            | 65.52.248.0        |
| 英国南部             | 51.140.184.11      |
| 英国西部              | 51.141.8.11        |
| 米国中西部      | 13.78.145.25       |
| 西ヨーロッパ          | 40.68.37.158、191.237.232.75、104.40.168.105  |
| 米国西部              | 104.42.238.205、23.99.34.75、13.86.216.196   |
| 米国西部 2            | 13.66.226.202      |
|                      |                    |



## <a name="next-steps"></a>次のステップ

- Azure SQL Database サーバーの Azure SQL Database 接続ポリシーの変更方法については、「[conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy)」を参照してください。
- ADO.NET 4.5 以降のバージョンを使用するクライアントの Azure SQL Database 接続動作については、「[ADO.NET 4.5 用の 1433 以外のポート](sql-database-develop-direct-route-ports-adonet-v12.md)」を参照してください。
- 一般的なアプリケーション開発概要情報については、「[SQL Database アプリケーションの開発の概要](sql-database-develop-overview.md)」を参照してください。
