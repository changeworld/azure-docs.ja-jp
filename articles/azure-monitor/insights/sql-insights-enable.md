---
title: SQL insights を有効にする
description: Azure Monitor で SQL insights を有効にする
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: e8dd887d151eb553131048f232940555dbef324b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025035"
---
# <a name="enable-sql-insights-preview"></a>SQL insights を有効にする (プレビュー)
この記事では、[SQL insights](sql-insights-overview.md) を有効にして SQL のデプロイを監視する方法について説明します。 監視は、お使いの SQL デプロイに接続し、動的管理ビュー (DMV) を使用して監視データを収集する Azure 仮想マシンから実行されます。 監視プロファイルを使用して、どのようなデータセットを収集するかと、収集の頻度を制御できます。

## <a name="create-log-analytics-workspace"></a>Log Analytics ワークスペースの作成
SQL insights のデータは、1 つ以上の [Log Analytics ワークスペース](../logs/data-platform-logs.md#log-analytics-workspaces)に格納されます。  SQL Insights を有効にする前に、[ワークスペースを作成](../logs/quick-create-workspace.md)するか、既存のワークスペースを選択する必要があります。 1 つのワークスペースを複数の監視プロファイルで使用できますが、ワークスペースとプロファイルは同じ Azure リージョン内に置く必要があります。 SQL insights の機能を有効にしてアクセスするには、ワークスペース内で [Log Analytics 共同作成者ロール](../logs/manage-access.md)を持っている必要があります。 

## <a name="create-monitoring-user"></a>監視ユーザーを作成する 
監視対象にする SQL デプロイ上にユーザーが必要です。 異なる種類の SQL デプロイについて、下記の手順に従ってください。

### <a name="azure-sql-database"></a>Azure SQL データベース
Azure portal で [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) または[クエリ エディター (プレビュー)](../../azure-sql/database/connect-query-portal.md) を使用して、Azure SQL Database を開きます。

次のスクリプトを実行し、必要なアクセス許可を持つユーザーを作成します。 *user* をユーザー名に、*mystrongpassword* をパスワードに置き換えます。

```
CREATE USER [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW DATABASE STATE TO [user]; 
GO 
```

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-script.png" alt-text="telegraf ユーザー スクリプトを作成します。" lightbox="media/sql-insights-enable/telegraf-user-database-script.png":::

ユーザーが作成されたことを確認します。

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-verify.png" alt-text="telegraf ユーザー スクリプトを確認します。" lightbox="media/sql-insights-enable/telegraf-user-database-verify.png":::

### <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance
Azure SQL Managed Instance にログインし、[SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) または同様のツールを使用して次のスクリプトを実行して、必要なアクセス許可を持つ監視ユーザーを作成します。 *user* をユーザー名に、*mystrongpassword* をパスワードに置き換えます。

 
```
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO 
```

### <a name="sql-server"></a>SQL Server
SQL Server を実行している Azure 仮想マシンにログインし、[SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) または同様のツールを使用して次のスクリプトを実行し、必要なアクセス許可を持つ監視ユーザーを作成します。 *user* をユーザー名に、*mystrongpassword* をパスワードに置き換えます。

 
```
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO
```

## <a name="create-azure-virtual-machine"></a>Azure 仮想マシンを作成する 
SQL を監視するためには、データの収集に使用される Azure 仮想マシンを 1 つ以上作成する必要があります。  

> [!NOTE]
>  [監視プロファイル](#create-sql-monitoring-profile)では、監視対象の種類が異なる SQL から、どのようなデータを収集するかを指定します。 それぞれの監視仮想マシンに関連付けることができる監視プロファイルは 1 つだけです。 複数の監視プロファイルが必要な場合は、それぞれのために仮想マシンを作成する必要があります。

### <a name="azure-virtual-machine-requirements"></a>Azure 仮想マシンの要件
Azure 仮想マシンには、次の要件があります。

- オペレーティング システム: Ubuntu 18.04 
- 推奨される Azure 仮想マシンのサイズ: Standard_B2s (2 CPU、4 GiB メモリ) 
- サポートされているリージョン: [Azure Monitor エージェントによってサポートされているリージョン](../agents/azure-monitor-agent-overview.md#supported-regions)すべて

> [!NOTE]
> 仮想マシン サイズが Standard_B2s (2 CPU、4 GiB メモリ) の場合、最大 100 の接続文字列がサポートされます。 1 つの仮想マシンに 100 を超える接続を割り当ててはいけません。

SQL リソースのネットワーク設定によっては、仮想マシンを SQL リソースと同じ仮想ネットワークに配置して、監視データを収集するためのネットワーク接続を確立することが必要になる場合があります。  

## <a name="configure-network-settings"></a>ネットワークの設定を構成する
どの種類の SQL にも、監視仮想マシンが SQL に安全にアクセスするための方法が用意されています。  以下のセクションでは、SQL の種類に基づくオプションについて説明します。

### <a name="azure-sql-databases"></a>Azure SQL Databases  

SQL 分析情報では、そのパブリック エンドポイントおよび仮想ネットワークからの Azure SQL Database へのアクセスをサポートしています。

パブリック エンドポイント経由でアクセスする場合は、 **[ファイアウォールの設定]** ページと [[IP ファイアウォールの設定]](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#ip-firewall-rules) セクションに規則を追加します。  仮想ネットワークからのアクセスを指定する場合は、[仮想ネットワークのファイアウォール規則](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#virtual-network-firewall-rules)を設定し、[Azure Monitor エージェントが必要とするサービス タグ](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview#networking)を設定できます。  [この記事](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#ip-vs-virtual-network-firewall-rules)では、これら 2 種類のファイアウォール規則の違いについて説明します。

:::image type="content" source="media/sql-insights-enable/set-server-firewall.png" alt-text="サーバー ファイアウォールの設定" lightbox="media/sql-insights-enable/set-server-firewall.png":::

:::image type="content" source="media/sql-insights-enable/firewall-settings.png" alt-text="ファイアウォールの設定。" lightbox="media/sql-insights-enable/firewall-settings.png":::


### <a name="azure-sql-managed-instances"></a>Azure SQL Managed Instance 

監視仮想マシンの場所が SQL MI リソースと同じ VNet 内になる場合は、「[同じ vnet 内で接続する](https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance#connect-inside-the-same-vnet)」を参照してください。 監視仮想マシンの場所が SQL MI リソースとは異なる VNet 内になる場合は、「[異なる vnet 内で接続する](https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance#connect-inside-a-different-vnet)」を参照してください。


### <a name="azure-virtual-machine-and-azure-sql-virtual-machine"></a>Azure 仮想マシンと Azure SQL 仮想マシン  
監視仮想マシンが SQL 仮想マシンのリソースと同じ VNet にある場合は、「[仮想ネットワーク内で SQL Server に接続する方法](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/ways-to-connect-to-sql#connect-to-sql-server-within-a-virtual-network)」を参照してください。 監視仮想マシンが SQL 仮想マシンのリソースと同じ VNet にある場合は、「[仮想ネットワーク内で SQL Server に接続する方法](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/ways-to-connect-to-sql#connect-to-sql-server-over-the-internet)」を参照してください。

## <a name="store-monitoring-password-in-key-vault"></a>Key Vault に監視パスワードを格納する
SQL ユーザーの接続パスワードは、監視プロファイルの接続文字列に直接入力するのではなく、Key Vault に格納する必要があります。

SQL 監視のプロファイルを設定するときには、使用する予定の Key Vault リソースに対する以下のいずれかのアクセス許可が必要になります。

- Microsoft.Authorization/roleAssignments/write 
- ユーザー アクセス管理者や所有者などの、Microsoft.Authorization/roleAssignments/delete のアクセス許可 

指定した Key Vault を使用する SQL Monitoring プロファイルの作成の一部として、新しいアクセス ポリシーが自動的に作成されます。 [Key Vault ネットワーク] の設定には、 *[すべてのネットワークからのアクセスを許可する]* を使用します。


## <a name="create-sql-monitoring-profile"></a>SQL 監視プロファイルを作成する
Azure portal で、 **[Azure Monitor]** メニューの **[Insights]** セクションから **[SQL (プレビュー)]** を選択し、SQL 分析情報を開きます。 **[新しいプロファイルの作成]** をクリックします。 

:::image type="content" source="media/sql-insights-enable/create-new-profile.png" alt-text="新しいプロファイルを作成します。" lightbox="media/sql-insights-enable/create-new-profile.png":::

このプロファイルには、SQL システムから収集する情報が格納されます。  これには、以下のための特定の設定があります。 

- Azure SQL データベース 
- Azure SQL Managed Instance 
- 仮想マシンで実行中の SQL Server  

たとえば、*SQL Production* という名前のプロファイルを 1 つ作成し、データ収集の頻度、収集対象のデータ、データ送信先のワークスペースについて異なる設定を持つ、*SQL Staging* という名前の別のプロファイルを作成できます。 

このプロファイルは、選択したサブスクリプションとリソース グループに、[データ収集ルール](../agents/data-collection-rule-overview.md)のリソースとして格納されます。 各プロファイルには以下が必要です。

- 名前。 いったん作成されると編集することはできません。
- 場所。 これは Azure のリージョンです。
- 監視データを格納するための Log Analytics ワークスペース。
- 収集する SQL 監視データの頻度と種類についての収集設定。

> [!NOTE]
> プロファイルの場所は、監視データを送信する予定の Log Analytics ワークスペースと同じ場所の中にある必要があります。


:::image type="content" source="media/sql-insights-enable/profile-details.png" alt-text="プロファイルの詳細。" lightbox="media/sql-insights-enable/profile-details.png":::

監視プロファイルの詳細を入力したら、 **[監視プロファイルの作成]** をクリックします。 プロファイルがデプロイされるのに、最大で 1 分かかることがあります。  **[監視プロファイル]** コンボ ボックスに新しいプロファイルが表示されない場合、デプロイがいったん完了すれば、[更新] ボタンをクリックすると表示されるはずです。  新しいプロファイルを選択したら、 **[プロファイルの管理]** タブを選択して、そのプロファイルに関連付けられる監視コンピューターを追加します。

### <a name="add-monitoring-machine"></a>監視マシンを追加する
**[監視マシンの追加]** を選択してコンテキスト パネルを開き、SQL インスタンスを監視するように設定する仮想マシンを選択し、接続文字列を指定します。

サブスクリプションと、監視仮想マシンの名前を選択します。 監視ユーザーのパスワードを格納するために Key Vault を使おうとしている場合は、これらのシークレットがある Key Vault リソースを選択し、接続文字列内で使用する URL とシークレット名を入力します。 異なる SQL デプロイの接続文字列を識別する方法の詳細については、次のセクションを参照してください。


:::image type="content" source="media/sql-insights-enable/add-monitoring-machine.png" alt-text="監視マシンを追加します。" lightbox="media/sql-insights-enable/add-monitoring-machine.png":::


### <a name="add-connection-strings"></a>接続文字列を追加する 
接続文字列では、SQL にログインして動的管理ビューを実行するときに SQL 分析情報で使用する必要があるユーザー名を指定します。 監視ユーザーのパスワードを保存するために Key Vault を使おうとしている場合は、使用するシークレットの URL と名前を指定します。 

接続文字列は、SQL リソースの種類ごとに異なります。

#### <a name="azure-sql-databases"></a>Azure SQL Databases 
接続文字列を次の形式で入力します。

```
sqlAzureConnections": [ 
   "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=$username;Password=$password;" 
}
```

データベースの **[接続文字列]** メニュー項目から詳細を取得します。

:::image type="content" source="media/sql-insights-enable/connection-string-sql-database.png" alt-text="SQL データベース接続文字列" lightbox="media/sql-insights-enable/connection-string-sql-database.png":::

読み取り可能なセカンダリを監視するには、接続文字列にキー値 `ApplicationIntent=ReadOnly` を含めます。


#### <a name="azure-virtual-machines-running-sql-server"></a>SQL Server を実行している Azure 仮想マシン 
接続文字列を次の形式で入力します。

```
"sqlVmConnections": [ 
   "Server=MyServerIPAddress;Port=1433;User Id=$username;Password=$password;" 
] 
```

監視仮想マシンが同じ VNET 内にある場合は、サーバーのプライベート IP アドレスを使用します。  それ以外の場合は、パブリック IP アドレスを使用します。 Azure SQL 仮想マシンを使おうとしている場合は、リソースの **[セキュリティ]** ページで、ここで使用するポートを確認できます。

:::image type="content" source="media/sql-insights-enable/sql-vm-security.png" alt-text="SQL 仮想マシンのセキュリティ" lightbox="media/sql-insights-enable/sql-vm-security.png":::

読み取り可能なセカンダリを監視するには、接続文字列にキー値 `ApplicationIntent=ReadOnly` を含めます。


### <a name="azure-sql-managed-instances"></a>Azure SQL Managed Instance 
接続文字列を次の形式で入力します。

```
"sqlManagedInstanceConnections": [ 
      "Server= mysqlserver.database.windows.net;Port=1433;User Id=$username;Password=$password;", 
    ] 
```
マネージド インスタンスの **[接続文字列]** メニュー項目から詳細を取得します。


:::image type="content" source="media/sql-insights-enable/connection-string-sql-managed-instance.png" alt-text="SQL Managed Instance の接続文字列" lightbox="media/sql-insights-enable/connection-string-sql-managed-instance.png":::

読み取り可能なセカンダリを監視するには、接続文字列にキー値 `ApplicationIntent=ReadOnly` を含めます。



## <a name="monitoring-profile-created"></a>作成された監視プロファイル 

SQL リソースからデータを収集するように仮想マシンを構成するには、 **[監視仮想マシンの追加]** を選択します。 **[概要]** タブには戻らないでください。数分のうちに [状態] 列が [収集中] に変化するはずです。そして、監視対象として選択した SQL リソースのデータが表示されます。

データが表示されない場合は、「[SQL 分析情報のトラブルシューティング](sql-insights-troubleshoot.md)」を参照して問題を特定してください。 

:::image type="content" source="media/sql-insights-enable/profile-created.png" alt-text="作成されたプロファイル" lightbox="media/sql-insights-enable/profile-created.png":::

> [!NOTE]
> VM の監視で監視プロファイルまたは接続文字列を更新する必要がある場合は、SQL Insights の **[プロファイルの管理]** タブで行うことができます。 更新が保存されると、変更は約 5 分後に適用されます。

## <a name="next-steps"></a>次のステップ

- 有効にした後で SQL 分析情報が正常に機能していない場合は、「[SQL 分析情報のトラブルシューティング](sql-insights-troubleshoot.md)」を参照してください。
