---
title: Azure SQL Database と SQL Data Warehouse への Azure トラフィックの転送 | Microsoft Docs
description: この文書では、Azure 内外からの Azure SQL Database と SQL Data Warehouse の接続アーキテクチャについて説明します。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab
manager: craigg
ms.date: 12/20/2018
ms.openlocfilehash: 62e4171a6895f2f425d67b9d1143fe9d3999a9b9
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53715904"
---
# <a name="azure-sql-connectivity-architecture"></a>Azure SQL の接続アーキテクチャ

この記事では Azure SQL Database および SQL Data Warehouse の接続アーキテクチャのほか、さまざまなコンポーネントがどのように機能し、トラフィックが Azure SQL インスタンスに送信されるか説明します。 これらの接続コンポーネントが機能し、Azure 内外からクライアントが接続する Azure SQL Database または SQL Data Warehouse にネットワーク トラフィックが送信されます。 この記事では、接続方法を変更するためのスクリプト サンプルと、既定の接続設定の変更に関連する考慮事項も提供します。

> [!IMPORTANT]
> **[今後の変更] Azure SQL サーバーへのサービス エンドポイントの接続の場合、`Default` の接続動作が `Redirect` に変わります。**
>
> 変更は既に、2018 年 11 月 10 日からブラジル南部と西ヨーロッパのリージョンで有効になっています。 その他のすべてのリージョンでの変更は、2019 年 1 月 2 日から有効になります。
>
> サービス エンドポイントを通じた接続が、この変更の結果として既存の環境で切断しないようにするために、以下の操作にテレメトリを使用します。
> - 変更前にサービス エンドポイントからアクセスされたサーバーを検出した場合、接続タイプを `Proxy` に切り替えます。
> - 他のすべてのサーバーについては、接続タイプを `Redirect` に切り替えます。
>
> サービス エンドポイントのユーザーは、次のシナリオでも影響を受ける可能性があります。 
> - アプリケーションがめったに既存のサーバーに接続しないため、テレメトリがこれらのアプリケーションに関する情報を取得しなかった場合 
> - サービス エンドポイント接続の既定の動作が `Proxy` であるとしたときに、自動デプロイ ロジックが論理サーバーを作成する場合 
>
> Azure SQL server へのサービス エンドポイント接続を確立できず、この変更による影響を受けていると思われる場合は、接続の種類が明示的に `Redirect` に設定されていることを確認します。 この場合は、ポート 11000 から 12000 の SQL [サービス タグ](../virtual-network/security-overview.md#service-tags)に所属する、リージョン内の Azure IP アドレスに対して VM ファイアウォール ルールおよびネットワーク セキュリティ グループ (NSG) を開く必要があります。 これがオプションでない場合、明示的にサーバーを `Proxy` に切り替えます。

> [!NOTE]
> このトピックは Azure SQL サーバーのほか、その Azure SQL サーバーに作成される SQL Database と SQL Data Warehouse の両方に当てはまります。 わかりやすいように、SQL Database という言葉で SQL Database と SQL Data Warehouse の両方を言い表します。

## <a name="connectivity-architecture"></a>接続のアーキテクチャ

次の図は、Azure SQL Database の接続アーキテクチャの概要です。

![アーキテクチャの概要](./media/sql-database-connectivity-architecture/connectivity-overview.png)

次の手順では、Azure SQL Database への接続を確立する方法について説明します。

- クライアントはゲートウェイに接続します。ゲートウェイにはパブリック IP アドレスが与えられており、ポート 1433 で待ち受けます。
- 有効な接続ポリシーによっては、ゲートウェイがトラフィックを正しいデータベース クラスターにリダイレクトまたはプロキシします。
- データベース クラスター内では、トラフィックは適切な Azure SQL Database に転送されます。

## <a name="connection-policy"></a>接続ポリシー

Azure SQL Database は、SQL Database サーバーの接続ポリシー設定について次の 3 つのオプションをサポートしています。

- **リダイレクト (推奨):** クライアントは、データベースをホストしているノードへの直接接続を確立します。 接続を有効にするには、ポート 1433 上の Azure SQL Database ゲートウェイの IP アドレスだけでなく、ネットワーク セキュリティ グループ (NSG) と[サービス タグ](../virtual-network/security-overview.md#service-tags)を使用して、11000 から 12000 のポートのリージョン内のすべての Azure IP アドレスに対する送信ファイアウォール ルールを、クライアントで許可する必要があります。 パケットはデータベースに直接送信されるため、待機時間とスループットのパフォーマンスが改善されます。
- **プロキシ:** このモードでは、すべての接続が Azure SQL Database ゲートウェイ経由でプロキシされます。 接続を有効にするには、Azure SQL Database ゲートウェイの IP アドレスのみ (通常はリージョンあたり 2 つの IP アドレス) を許可する送信ファイアウォール規則がクライアントに必要です。 このモードを選択すると、ワークロードの性質によっては待機時間が長くなり、スループットが低下する可能性があります。 最短の待機時間と最高のスループットを実現するために、`Proxy` 接続ポリシーではなく `Redirect` 接続ポリシーを強くお勧めします。
- **既定:** これは、明示的に接続ポリシーを `Proxy` または `Redirect` に変更しない限り、作成後のすべてのサーバーで有効になる接続ポリシーです。 有効なポリシーは、接続が Azure (`Redirect`) 内か Azure (`Proxy`) の外部かによって変わります。

## <a name="connectivity-from-within-azure"></a>Azure 内からの接続

Azure 内から接続する場合、接続には既定で `Redirect` の接続ポリシーが与えられます。 `Redirect` のポリシーとは、TCP セッションが Azure SQL Database に対して確立された後に、宛先の仮想 IP を Azure SQL Database ゲートウェイの IP からクラスターの IP に変更して、正しいデータベース クラスターにリダイレクトすることを意味します。 その後、すべての後続パケットは Azure SQL Database ゲートウェイを迂回し、クラスターに直接送信されます。 次の図にこのトラフィックの流れを示します。

![アーキテクチャの概要](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Azure 外からの接続

Azure 外から接続する場合、接続には既定で `Proxy` の接続ポリシーが与えられます。 `Proxy` のポリシーとは、TCP セッションが Azure SQL Database ゲートウェイ経由で確立し、すべての後続パケットがゲートウェイ経由で送信されることを意味します。 次の図にこのトラフィックの流れを示します。

![アーキテクチャの概要](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>Azure SQL Database ゲートウェイ IP アドレス

オンプレミス リソースから Azure SQL データベースに接続するには、自分の Azure リージョンに関して、Azure SQL Database ゲートウェイへの送信ネットワーク トラフィックを許可する必要があります。 `Proxy` モードで接続するとき、ゲートウェイ経由でのみ接続されます。プロキシ モードは、オンプレミス リソースから接続するときの既定です。

次の表は、すべてのデータ リージョンの Azure SQL Database ゲートウェイのプライマリ IP とセカンダリ IP を一覧にまとめたものです。 IP アドレスが 2 つのリージョンもあります。 そのようなリージョンでは、プライマリ IP アドレスはゲートウェイの現行 IP アドレスであり、セカンダリ IP アドレスはフェールオーバー IP アドレスになります。 フェールオーバー アドレスは、サービスの高い可用性を維持するためにサーバーを移動するとき、その移動先となるアドレスです。 そのようなリージョンについては、両方の IP アドレスへの送信を許可することが推奨されます。 セカンダリ IP アドレスは Microsoft の所有であり、接続を受け入れるために Azure SQL Database によりアクティベートされるまで、いかなるサービスでもリッスンしません。

| リージョン名 | プライマリ IP アドレス | セカンダリ IP アドレス |
| --- | --- |--- |
| オーストラリア東部 | 13.75.149.87 | 40.79.161.1 |
| オーストラリア東南部 | 191.239.192.109 | 13.73.109.251 |
| ブラジル南部 | 104.41.11.5 | |
| カナダ中部 | 40.85.224.249 | |
| カナダ東部 | 40.86.226.166 | |
| 米国中央部 | 23.99.160.139 | 13.67.215.62 |
| 中国東部 1 | 139.219.130.35 | |
| 中国東部 2 | 40.73.82.1 | |
| 中国北部 1 | 139.219.15.17 | |
| 中国北部 2 | 40.73.50.0 | |
| 東アジア | 191.234.2.139 | 52.175.33.150 |
| 米国東部 1 | 191.238.6.43 | 40.121.158.30 |
| 米国東部 2 | 191.239.224.107 | 40.79.84.180 * |
| フランス中部 | 40.79.137.0 | 40.79.129.1 |
| ドイツ中部 | 51.4.144.100 | |
| ドイツ北東部 | 51.5.144.179 | |
| インド中部 | 104.211.96.159 | |
| インド南部 | 104.211.224.146 | |
| インド西部 | 104.211.160.80 | |
| 東日本 | 191.237.240.43 | 13.78.61.196 |
| 西日本 | 191.238.68.11 | 104.214.148.156 |
| 韓国中部 | 52.231.32.42 | |
| 韓国南部 | 52.231.200.86 |  |
| 米国中北部 | 23.98.55.75 | 23.96.178.199 |
| 北ヨーロッパ | 191.235.193.75 | 40.113.93.91 |
| 米国中南部 | 23.98.162.75 | 13.66.62.124 |
| 東南アジア | 23.100.117.95 | 104.43.15.0 |
| 英国北部 | 13.87.97.210 | |
| 英国南部 1 | 51.140.184.11 | |
| 英国南部 2 | 13.87.34.7 | |
| 英国西部 | 51.141.8.11 | |
| 米国中西部 | 13.78.145.25 | |
| 西ヨーロッパ | 191.237.232.75 | 40.68.37.158 |
| 米国西部 1 | 23.99.34.75 | 104.42.238.205 |
| 米国西部 2 | 13.66.226.202 | |
||||

\* **注:***米国東部 2* には、`52.167.104.0` の第 3 IP アドレスもあります。

## <a name="change-azure-sql-database-connection-policy"></a>Azure SQL Database 接続ポリシーを変更する

Azure SQL Database サーバーの Azure SQL Database 接続ポリシーを変更するには、[conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy) コマンドを使用します。

- 接続ポリシーを `Proxy` に設定すると、すべてのネットワーク パケットが Azure SQL Database ゲートウェイ経由で送信されます。 この設定の場合、Azure SQL Database ゲートウェイ IP のみに送信を許可する必要があります。 `Proxy` の設定を利用すると、`Redirect` の設定より待ち時間が長くなります。
- 接続ポリシーで `Redirect` を設定すると、すべてのネットワーク パケットがデータベース クラスターに直接送信されます。 この設定の場合、複数の IP への送信を許可する必要があります。

## <a name="script-to-change-connection-settings-via-powershell"></a>接続の設定を変更する PowerShell のスクリプト

> [!IMPORTANT]
> このスクリプトは [Azure PowerShell モジュール](/powershell/azure/install-azurerm-ps)を必要とします。
>

次の PowerShell スクリプトは、接続ポリシーの変更方法を示しています。

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzureRmSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzureRmResource -ResourceId $id).Properties.connectionType

# Update connection policy
Set-AzureRmResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="script-to-change-connection-settings-via-azure-cli"></a>接続の設定を変更する Azure CLI のスクリプト

> [!IMPORTANT]
> このスクリプトは [Azure CLI ](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) を必要とします。

次の CLI スクリプトは、接続ポリシーの変更方法を示しています。

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $id

# Update connection policy
az resource update --ids $id --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>次の手順

- Azure SQL Database サーバーの Azure SQL Database 接続ポリシーの変更方法については、「[conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy)」を参照してください。
- ADO.NET 4.5 以降のバージョンを使用するクライアントの Azure SQL Database 接続動作については、「[ADO.NET 4.5 用の 1433 以外のポート](sql-database-develop-direct-route-ports-adonet-v12.md)」を参照してください。
- 一般的なアプリケーション開発概要情報については、「[SQL Database アプリケーションの開発の概要](sql-database-develop-overview.md)」を参照してください。
