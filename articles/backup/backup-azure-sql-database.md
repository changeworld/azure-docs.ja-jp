---
title: Azure への SQL Server データベースのバックアップ | Microsoft Docs
description: このチュートリアルでは、SQL Server を Azure に バックアップする方法について説明します。 また、SQL Server の復旧についても説明します。
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2018
ms.author: markgal;anuragm
ms.custom: ''
ms.openlocfilehash: c3321fb64c423b1b3c80f48fb97a70cc7dbc83f9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39433567"
---
# <a name="back-up-sql-server-databases-to-azure"></a>Azure への SQL Server データベースのバックアップ

SQL Server データベースは、低い回復ポイントの目標値 (RPO) と長期のリテンション期間を必要とする重要なワークロードです。 Azure Backup は、インフラストラクチャを必要としない、つまり、管理する複雑なバックアップ サーバー、管理エージェント、バックアップ ストレージなどが存在しない SQL Server バックアップ ソリューションを提供します。 Azure Backup では、SQL Server を実行しているすべてのサーバーのバックアップだけでなく、さまざまなワークロードも含めた一元管理を提供しています。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * Azure に SQL Server インスタンスをバックアップするための前提条件
> * Recovery Services コンテナーを作成および使用する方法
> * SQL Server データベース バックアップを構成する方法
> * 復旧ポイントに対してバックアップ (またはリテンション期間) ポリシーを設定する方法
> * データベースを復元する方法

この記事の手順を開始するには、事前に Azure で SQL Server インスタンスを実行している必要があります。 [SQL Server インスタンスを迅速に作成するには、SQL Marketplace の仮想マシンを使用します](../sql-database/sql-database-get-started-portal.md)。

## <a name="public-preview-limitations"></a>パブリック プレビューの制限事項

次の項目は、パブリック プレビューの既知の制限事項です。

- SQL 仮想マシン (VM) では、Azure パブリック IP アドレスにアクセスするためにインターネット接続を必要とします。 詳細については、「[ネットワーク接続を確立する](backup-azure-sql-database.md#establish-network-connectivity)」を参照してください。
- 最大 2,000 個の SQL データベースを 1 つの Recovery Services コンテナーで保護します。 追加の SQL データベースは、別個の Recovery Services コンテナーに格納される必要があります。
- [分散型の可用性グループのバックアップ](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017)には、制限があります。
- SQL Server Always On フェールオーバー クラスター インスタンス (FCI) はサポートされていません。
- Azure Portal を使用して Azure Backup を構成し、SQL Server データベースを保護します。 Azure PowerShell、Azure CLI、および REST API は現在サポートされていません。

サポート/サポート対象外のシナリオの詳細については、[「FAQ」セクション](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#faq)を参照してください。

## <a name="support-for-azure-geos"></a>Azure geo のサポート

Azure Backup は次の geo でサポートされています。

- オーストラリア南東部 (ASE) 
- ブラジル南部 (BRS)
- カナダ中部 (CNC)
- カナダ東部 (CE)
- 米国中部 (CUS)
- 東アジア (EA)
- オーストラリア東部 (AE) 
- 米国東部 (EUS)
- 米国東部 2 (EUS2)
- インド中部 (INC) 
- インド南部 (INS)
- 東日本 (JPE)
- 西日本 (JPW)
- 韓国中部 (KRC)
- 韓国南部 (KRS)
- 米国中北部 (NCUS) 
- 北ヨーロッパ (NE) 
- 米国中南部 (SCUS) 
- 東南アジア (SEA)
- 英国南部 (UKS) 
- 英国西部 (UKW) 
- 米国中西部 (WCUS)
- 西ヨーロッパ (WE) 
- 米国西部 (WUS)
- 米国西部 2 (WUS 2) 

## <a name="support-for-operating-systems-and-sql-server-versions"></a>オペレーティング システムと SQL Server のバージョンのサポート

ここでは、Azure Backup でサポートされるオペレーティング システムおよび SQL Server のバージョンについて説明します。 SQL Marketplace の Azure 仮想マシンと、Marketplace 以外の (SQL Server が手動でインストールされる) 仮想マシンがサポートされています。

### <a name="supported-operating-systems"></a>サポートされているオペレーティング システム

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

Linux は現在サポートされていません。

### <a name="supported-sql-server-versions-and-editions"></a>サポートされている SQL Server のバージョン/エディション

- SQL Server 2012 Enterprise、Standard、Web、Developer、Express
- SQL Server 2014 Enterprise、Standard、Web、Developer、Express
- SQL Server 2016 Enterprise、Standard、Web、Developer、Express
- SQL Server 2017 Enterprise、Standard、Web、Developer、Express

## <a name="prerequisites"></a>前提条件

SQL Server データベースをバックアップする前に、次の条件を確認してください。

- SQL Server インスタンスをホストする仮想マシンとして、同じリージョンまたはロケールの [Recovery Services コンテナーを作成](backup-azure-sql-database.md#create-a-recovery-services-vault)または特定する。
- SQL データベースのバックアップに必要な[仮想マシンの権限を確認](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms)する。
- [SQL 仮想マシンがネットワーク接続を備えている](backup-azure-sql-database.md#establish-network-connectivity)ことを確認する。

> [!NOTE]
> SQL Server データベースをバックアップするために一度に保持できるバックアップ ソリューションは、1 つだけです。 この機能を使用する前に他の SQL バックアップをすべて無効にしてください。そうしないと、バックアップは妨害されて失敗します。 IaaS VM 向けの Azure Backup は、競合することなく SQL バックアップと共に有効化できます。
>

お使いの環境でこれらの条件が満たされている場合は、「[SQL Server データベースのバックアップを構成する](backup-azure-sql-database.md#configure-backup-for-sql-server-databases)」に進んでください。 いずれかの前提条件が不足している場合は、引き続きお読みください。


## <a name="establish-network-connectivity"></a>ネットワーク接続を確立する

すべての操作において、SQL 仮想マシンでは、Azure パブリック IP アドレスへの接続を必要とします。 パブリック IP アドレスへの接続がないと、(データベースの検出、バックアップの構成、バックアップのスケジュール、復旧ポイントの復元などの) SQL 仮想マシンの操作が失敗します。 次のいずれかの方法を使用して、バックアップ トラフィックへの明確なパスを指定します。

- Azure データ センターの IP 範囲をホワイトリストに登録する: Azure データ センターの IP 範囲をホワイトリストに登録するには、[IP 範囲と手順の詳細に関するダウンロード センターのページ](https://www.microsoft.com/download/details.aspx?id=41653)を活用してください。 
- トラフィックをルーティングするために HTTP プロキシ サーバーをデプロイする: SQL データベースを VM でバックアップする場合、VM 上のバックアップ拡張機能では HTTPS API を使用して Azure Backup に管理コマンドを送信し、Azure Storage にデータを送信します。 また、バックアップ拡張機能では、認証に Azure Active Directory (Azure AD) を使用します。 HTTP プロキシ経由でこれらの 3 つのサービスのバックアップ拡張機能のトラフィックをルーティングします。 パブリック インターネットにアクセスできるように構成されたコンポーネントはバックアップ拡張機能のみです。

トレードオフの関係にあるのは、管理容易性、詳細な制御、およびコストです。

> [!NOTE]
> Azure Backup 用のサービス タグは必ず、一般提供で入手できます。
>

| オプション | 長所 | 短所 |
| ------ | ---------- | ------------- |
| IP 範囲をホワイトリストに登録する | 追加のコストが発生しない。 <br/> NSG でアクセスを開くには、**Set-AzureNetworkSecurityRule** コマンドレットを使用する。 | 影響を受ける IP の範囲が時間の経過と共に変化するため、管理が複雑である。 <br/>Azure Storage だけでなく、Azure 全体へのアクセスを提供することになる。|
| HTTP プロキシを使用する   | 許可するストレージ URL をプロキシで詳細に制御可能。 <br/>VM に対するインターネット アクセスを単一の場所で実現。 <br/> Azure の IP アドレスの変更の影響を受けない。 | プロキシ ソフトウェアで VM を実行するための追加のコストが発生する。 |

## <a name="set-permissions-for-non-marketplace-sql-vms"></a>Marketplace 以外の SQL VM に権限を設定する

仮想マシンをバックアップするために、Azure Backup には **AzureBackupWindowsWorkload** 拡張機能がインストールされている必要があります。 Microsoft Azure Marketplace の仮想マシンを使用する場合は、「[SQL Server データベースを検出する](backup-azure-sql-database.md#discover-sql-server-databases)」に進んでください。 SQL データベースをホストする仮想マシンが Microsoft Azure Marketplace から作成されない場合は、以下の手順を完了して拡張機能をインストールし、適切な権限を設定します。 **AzureBackupWindowsWorkload** 拡張機能に加えて、Azure Backup では、SQL データベースを保護するために SQL sysadmin 特権が必要になります。 仮想マシン上のデータベースを検出するために、Azure Backup は **NT Service\AzureWLBackupPluginSvc** というアカウントを作成します。 Azure Backup が SQL データベースを検出するには、**NT Service\AzureWLBackupPluginSvc** アカウントに SQL および SQL sysadmin 権限が必要です。 次の手順では、これらの権限を指定する方法について説明します。

権限を構成するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) で、SQL データベースの保護に使用する Recovery Services コンテナーを開きます。

2. **[Recovery Services コンテナー]** ダッシュボードで **[バックアップ]** を選択します。 **[バックアップの目標]** メニューが開きます。

   ![[バックアップ] を選択して [バックアップの目標] メニューを開く](./media/backup-azure-sql-database/open-backup-menu.png)

3. **[バックアップの目標]** メニューで、**[ワークロードはどこで実行されていますか?]** を既定値の **[Azure]** に設定します。

4. **[何をバックアップしますか?]** ドロップダウン リスト ボックスを展開し、**[Azure VM 内の SQL Server]** を選択します。

    ![バックアップ対象として [Azure VM 内の SQL Server] を選択する](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    **[バックアップの目標]** メニューには、**[VM 内のデータベースを検出]** と **[バックアップの構成]** という 2 つの手順が表示されます。 **[VM 内のデータベースを検出]** の手順では、Azure 仮想マシンの検索を開始します。

    ![[バックアップの目標] の 2 つの手順を確認する](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

5. **[VM 内のデータベースを検出]** で、**[検出の開始]** を選択して、サブスクリプション内にある保護されていない仮想マシンを検索します。 すべての仮想マシンの検索には少し時間がかかる場合があります。 検索時間は、サブスクリプション内にある保護されていない仮想マシンの数によって異なります。

    ![VM 内にある DB の検索中はバックアップが保留される](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    保護されていない仮想マシンが検出されると、一覧に表示されます。 保護されていない仮想マシンは、仮想マシン名とリソース グループごとに一覧に表示されます。 複数の仮想マシンが同じ名前になっている可能性もあります。 ただし、同じ名前の仮想マシンは、別のリソース グループに属します。 目的の仮想マシンが一覧に表示されていない場合は、その仮想マシンがコンテナーに対して既に保護されているかどうかを確認します。

6. 仮想マシンの一覧で、バックアップする SQL データベースが含まれる VM を選択して、**[Discover DBs]\(DB の検出\)** を選択します。 

    検出プロセスによって、仮想マシン上に **AzureBackupWindowsWorkload** 拡張機能がインストールされます。 拡張機能によって Azure Backup サービスは仮想マシンと通信できるので、SQL データベースをバックアップできます。 拡張機能がインストールされると、Azure Backup は Windows 仮想サービス アカウント **NT Service\AzureWLBackupPluginSvc** を仮想マシン上に作成します。 仮想サービス アカウントには、SQL sysadmin 権限が必要です。 仮想サービス アカウントのインストール プロセス中にエラー `UserErrorSQLNoSysadminMembership` が表示された場合は、「[SQL sysadmin 権限を修正する](backup-azure-sql-database.md#fix-sql-sysadmin-permissions)」を参照してください。

    **[通知]** 領域には、データベース検出の進行状況が表示されます。 ジョブが完了するまで少し時間がかかる場合があります。 ジョブの時間は、仮想マシン上にあるデータベースの数によって異なります。 選択したデータベースが検出されたら、成功のメッセージが表示されます。

    ![デプロイの成功メッセージ](./media/backup-azure-sql-database/notifications-db-discovered.png)

Recovery Services コンテナーとデータベースを関連付けたら、次の手順として[バックアップ ジョブを構成](backup-azure-sql-database.md#configure-backup-for-sql-server-databases)します。

### <a name="fix-sql-sysadmin-permissions"></a>SQL sysadmin 権限を修正する

インストール プロセス中にエラー `UserErrorSQLNoSysadminMembership` が表示された場合は、SQL Server sysadmin 権限を持つアカウントを使用して SQL Server Management Studio (SSMS) にサインインします。 特殊な権限が必要でない限り、Windows 認証が機能するはずです。

1. SQL Server で、セキュリティ/ログイン フォルダーを開きます。

    ![セキュリティ/ログイン フォルダーを開いてアカウントを表示する](./media/backup-azure-sql-database/security-login-list.png)

2. [ログイン] フォルダーを右クリックし、**[新しいログイン]** を選択します。 **[ログイン - 新規作成]** ダイアログ ボックスで **[検索]** を選択します。

    ![[ログイン - 新規作成] ダイアログ ボックスで [検索] を選択する](./media/backup-azure-sql-database/new-login-search.png)

3. Windows 仮想サービス アカウント **NT Service\AzureWLBackupPluginSvc** は、仮想マシンの登録と SQL 検出フェーズ中に作成済みです。 **[選択するオブジェクト名を入力してください]** ボックスにアカウント名を入力します。 **[名前の確認]** を選択して名前解決を行います。 

    ![[名前の確認] を選択して不明なサービス名を解決する](./media/backup-azure-sql-database/check-name.png)

4. **[OK]** を選択してダイアログ ボックスを閉じます。

5. **[サーバー ロール]** ボックスで、**sysadmin** ロールが選択されていることを確認します。 **[OK]** を選択してダイアログ ボックスを閉じます。

    ![sysadmin サーバー ロールが選択されていることを確認する](./media/backup-azure-sql-database/sysadmin-server-role.png)

    この時点で、必要な権限が揃っているはずです。

6. 権限エラーは修正しましたが、Recovery Services コンテナーとデータベースを関連付ける必要があります。 Azure Portal の **[保護されたサーバー]** 一覧で、エラー状態にあるサーバーを右クリックし、**[DB の再検出]** を選択します。

    ![サーバーに適切な権限があることを確認する](./media/backup-azure-sql-database/check-erroneous-server.png)

    **[通知]** 領域には、データベース検出の進行状況が表示されます。 ジョブが完了するまで少し時間がかかる場合があります。 ジョブの時間は、仮想マシン上にあるデータベースの数によって異なります。 選択したデータベースが検出されたら、成功のメッセージが表示されます。

    ![デプロイの成功メッセージ](./media/backup-azure-sql-database/notifications-db-discovered.png)

Recovery Services コンテナーとデータベースを関連付けたら、次の手順として[バックアップ ジョブを構成](backup-azure-sql-database.md#configure-backup-for-sql-server-databases)します。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>SQL Server データベースを検出する

Azure Backup では、SQL Server インスタンス上にあるすべてのデータベースを検出します。 バックアップの前提条件に従ってデータベースを保護できます。 次の手順を使用して、SQL データベースをホストする仮想マシンを特定します。 仮想マシンを特定したら、Azure Backup では軽量な拡張機能をインストールして、SQL Server データベースを検出します。

1. [Azure Portal](https://portal.azure.com/) でサブスクリプションにサインインします。

2. 左側のメニューから、**[すべてのサービス]** を選択します。

    ![[すべてのサービス] を選択する](./media/backup-azure-sql-database/click-all-services.png) <br/>

3. **[すべてのサービス]** ダイアログ ボックスに、「**Recovery Services**」と入力します。 入力を完了すると、リソースのリストが絞り込まれます。 一覧で **[Recovery Services コンテナー]** を選択します。

    ![[Recovery Services コンテナー] と入力して選択する](./media/backup-azure-sql-database/all-services.png) <br/>

    サブスクリプションに Recovery Services コンテナーの一覧が表示されます。 

4. Recovery Services コンテナーの一覧で、SQL データベースの保護に使用するコンテナーを選択します。

5. **[Recovery Services コンテナー]** ダッシュボードで **[バックアップ]** を選択します。 **[バックアップの目標]** メニューが開きます。

   ![[バックアップ] を選択して [バックアップの目標] メニューを開く](./media/backup-azure-sql-database/open-backup-menu.png)

6. **[バックアップの目標]** メニューで、**[ワークロードはどこで実行されていますか?]** を既定値の **[Azure]** に設定します。

7. **[何をバックアップしますか?]** ドロップダウン リスト ボックスを展開し、**[Azure VM 内の SQL Server]** を選択します。

    ![バックアップ対象として [Azure VM 内の SQL Server] を選択する](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    **[バックアップの目標]** メニューには、**[VM 内のデータベースを検出]** と **[バックアップの構成]** という 2 つの手順が表示されます。
    
    ![[バックアップの目標] の 2 つの手順を確認する](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

8. **[VM 内のデータベースを検出]** で、**[検出の開始]** を選択して、サブスクリプション内にある保護されていない仮想マシンを検索します。 すべての仮想マシンの検索には少し時間がかかる場合があります。 検索時間は、サブスクリプション内にある保護されていない仮想マシンの数によって異なります。

    ![VM 内にある DB の検索中はバックアップが保留される](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    保護されていない仮想マシンが検出されると、一覧に表示されます。 複数の仮想マシンを同じ名前にすることができます。 ただし、同じ名前の仮想マシンは、別のリソース グループに属します。 保護されていない仮想マシンは、仮想マシン名とリソース グループごとに一覧に表示されます。 目的の仮想マシンが一覧に表示されていない場合は、その仮想マシンがコンテナーに対して既に保護されているかどうかを確認します。

9. 仮想マシンの一覧で、バックアップする SQL データベースが含まれる VM を選択して、**[Discover DBs]\(DB の検出\)** を選択します。

    Azure Backup では、仮想マシン上のすべての SQL データベースを検出します。 データベースの検出フェーズで発生した出来事に関する情報については、「[バックグラウンド操作](backup-azure-sql-database.md#background-operations)」を参照してください。 SQL データベースの検出を終えて、[バックアップ ジョブを構成する](backup-azure-sql-database.md#configure-backup-for-sql-server-databases)準備ができました。

### <a name="background-operations"></a>バックグラウンド操作

**[Discover DBs]\(DB の検出\)** ツールを使用すると、Azure Backup はバックグラウンドで次の操作を実行します。

- ワークロード バックアップ用の Recovery Services コンテナーを使って仮想マシンを登録する。 登録されている仮想マシン上のすべてのデータベースを、この Recovery Services コンテナーに対してのみバックアップできます。 

- 仮想マシン上に **AzureBackupWindowsWorkload** 拡張機能をインストールする。 SQL データベースのバックアップは、エージェントレス ソリューションです。 仮想マシン上に拡張機能がインストールされた状態で、エージェントが SQL データベースにインストールされることはありません。

- 仮想マシン上にサービス アカウント **NT Service\AzureWLBackupPluginSvc** を作成する。 すべてのバックアップおよび復元操作に、サービス アカウントを使用します。 **NT Service\AzureWLBackupPluginSvc** には、SQL sysadmin 権限が必要です。 すべての SQL Marketplace 仮想マシンは **SqlIaaSExtension** がインストールされた状態で出荷されます。 **AzureBackupWindowsWorkload** 拡張機能は **SQLIaaSExtension** を使用して自動的に必要な権限を取得します。 仮想マシンに **SqlIaaSExtension** がインストールされていない場合、DB の検出操作は失敗し、エラー メッセージ `UserErrorSQLNoSysAdminMembership` が表示されます。 バックアップの sysadmin 権限を追加するには、[Marketplace 以外の SQL VM に Azure Backup の権限を設定する](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms)手順に従います。

    ![VM とデータベースを選択する](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup-for-sql-server-databases"></a>SQL Server データベースのバックアップを構成する

Azure Backup は、SQL Server データベースを保護してバックアップ ジョブを管理するための管理サービスを提供しています。 管理と監視の機能は、お使いの Recovery Services コンテナーに応じて異なります。 

> [!NOTE]
> SQL Server データベースをバックアップするために一度に保持できるバックアップ ソリューションは、1 つだけです。 この機能を使用する前に他の SQL バックアップをすべて無効にしてください。そうしないと、バックアップは妨害されて失敗します。 IaaS VM 向けの Azure Backup は、競合することなく SQL バックアップと共に有効化できます。
>

SQL データベースの保護を構成するには、次の手順に従います。

1. SQL 仮想マシンと共に登録された Recovery Services コンテナーを開きます。

2. **[Recovery Services コンテナー]** ダッシュボードで **[バックアップ]** を選択します。 **[バックアップの目標]** メニューが開きます。

   ![[バックアップ] を選択して [バックアップの目標] メニューを開く](./media/backup-azure-sql-database/open-backup-menu.png)

3. **[バックアップの目標]** メニューで、**[ワークロードはどこで実行されていますか?]** を既定値の **[Azure]** に設定します。

4. **[何をバックアップしますか?]** ドロップダウン リスト ボックスを展開し、**[Azure VM 内の SQL Server]** を選択します。

    ![バックアップ対象として [Azure VM 内の SQL Server] を選択する](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    **[バックアップの目標]** メニューには、**[VM 内のデータベースを検出]** と **[バックアップの構成]** という 2 つの手順が表示されます。
    
    この記事の順序に沿って手順を終えたので、保護されていない仮想マシンが検出され、このコンテナーは仮想マシンと共に登録されています。 これで、SQL データベースの保護を構成する準備ができました。
    
5. **[バックアップの目標]** メニューで、**[バックアップの構成]** を選択します。

    ![[バックアップの構成] を選択する](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    Azure Backup サービスでは、スタンドアロン データベースを備えたすべての SQL Server インスタンスと SQL Server Always On 可用性グループが表示されます。 SQL Server インスタンスのスタンドアロン データベースを表示するには、インスタンス名の左側にあるシェブロンを選択します。 次の図は、スタンドアロン インスタンスと Always On 可用性グループの例を示しています。

    > [!NOTE]
    > SQL Server Always On 可用性グループの場合は、SQL のバックアップ設定が使用されます。 ただし、SQL プラットフォームの制限のために、完全および差分バックアップはプライマリ ノードから実行する必要があります。 ログ バックアップは、バックアップの設定に従って行われます。 この制限のために、可用性グループにプライマリ ノードが常に登録されている必要があります。
    >

    ![SQL インスタンスのデータベースの一覧](./media/backup-azure-sql-database/discovered-databases.png)

    データベースの一覧を表示する Always On 可用性グループの左側にあるシェブロンを選択します。

    ![Always On 可用性グループのデータベースの一覧](./media/backup-azure-sql-database/discovered-database-availability-group.png)

6. データベースの一覧で、保護するデータベースをすべて選択して、**[OK]** を選択します。

    ![保護する複数のデータベースを選択する](./media/backup-azure-sql-database/select-multiple-database-protection.png)

    一度に最大 50 個のデータベースを選択できます。 50 を上回る数のデータベースを保護するには、過程を複数に分けます。 最初の 50 個のデータベースを保護した後に、この手順を繰り返して次のデータベース セットを保護します。

    > [!Note] 
    > バックアップの負荷を最適化するために、Azure Backup では大規模なバックアップ ジョブを複数のバッチに細分化します。 1 つのバックアップ ジョブにおけるデータベースの最大数は 50 です。
    >
    >

7. **[バックアップ]** メニューで、バックアップ ポリシーを作成または選択するには、**[バックアップ ポリシー]** を選択します。 **[バックアップ ポリシー]** メニューが開きます。

    ![[バックアップ ポリシー] を選択する](./media/backup-azure-sql-database/select-backup-policy.png)

8. **[バックアップ ポリシーの選択]** ドロップダウン リスト ボックスで、バックアップ ポリシーを選択して、**[OK]** を選択します。 バックアップ ポリシーを作成する方法については、「[バックアップ ポリシーを定義する](backup-azure-sql-database.md#define-a-backup-policy)」を参照してください。

   > [!NOTE]
   > プレビュー期間中は、バックアップ ポリシーを編集できません。 一覧で使用できるもの以外のポリシーが必要な場合は、そのポリシーを作成する必要があります。 新しいバックアップ ポリシーの作成については、「[バックアップ ポリシーを定義する](backup-azure-sql-database.md#define-a-backup-policy)」セクションを参照してください。

    ![一覧からバックアップ ポリシーを選択する](./media/backup-azure-sql-database/select-backup-policy-steptwo.png)

    **[バックアップ ポリシー]** メニューで、**[バックアップ ポリシーの選択]** ドロップダウン リスト ボックスから次の操作を実行できます。 
    - 既定のポリシー (**HourlyLogBackup**) を選択する。
    - SQL 用に以前に作成された既存のバックアップ ポリシーを選択する。
    - RPO とリテンション期間の範囲に基づいて[新しいポリシーを定義する](backup-azure-sql-database.md#define-a-backup-policy)。 

    > [!Note]
    > Azure Backup では、三世代バックアップ スキームに基づく長期のリテンション期間をサポートします。 このスキームでは、コンプライアンス要件を満たしたうえで、バックエンドのストレージ使用量を最適化します。
    >

9. **[バックアップ]** メニューでバックアップ ポリシーを選択したら、**[バックアップの有効化]** を選択します。

    ![選択したバックアップ ポリシーを有効にする](./media/backup-azure-sql-database/enable-backup-button.png)

    ポータルの **[通知]** 領域で構成の進行状況を追跡します。

    ![[通知] 領域](./media/backup-azure-sql-database/notifications-area.png)


### <a name="define-a-backup-policy"></a>バックアップ ポリシーを定義する

バックアップ ポリシーは、バックアップをいつ取得し、そのバックアップをいつまで保持するかのマトリックスを定義します。 Azure Backup を使用して、SQL データベースの 3 種類のバックアップのスケジュールを設定します。

* 完全バックアップ: 完全なデータベース バックアップでは、データベース全体をバックアップします。 完全バックアップには、特定のデータベースあるいは一連のファイル グループまたはファイル内のすべてのデータと、そのデータを復旧するための十分なログが含まれます。 最多で、1 日に 1 回の完全バックアップをトリガーできます。 日次または週次で完全バックアップを取得することを選択できます。 
* 差分バックアップ: 差分バックアップは、直近の完全データ バックアップに基づきます。 差分バックアップでは、完全バックアップ以降に変更されたデータのみをキャプチャします。 最多で、1 日に 1 回の差分バックアップをトリガーできます。 完全バックアップと差分バックアップを同じ日に構成することはできません。
* トランザクション ログ バックアップ: ログ バックアップでは、ある特定の時点への復元を、指定した秒に至るまで可能にします。 最多で、15 分ごとにトランザクション ログ バックアップを構成できます。

ポリシーは、Recovery Services コンテナー レベルで作成されます。 複数のコンテナーでは同じバックアップ ポリシーを使用できますが、各コンテナーにバックアップ ポリシーを適用する必要があります。 バックアップ ポリシーの作成時は、日次での完全バックアップが既定値です。 差分バックアップを追加することは可能ですが、週次で実行するように完全バックアップを構成する場合だけです。 次の手順では、Azure 仮想マシンで SQL Server インスタンスのバックアップ ポリシーを作成する方法を示します。 

> [!NOTE]
> プレビューでは、バックアップ ポリシーを編集することはできません。 代わりに、必要な詳細で新しいポリシーを作成する必要があります。  
 
バックアップ ポリシーを作成するには:

1. SQL データベースを保護する Recovery Services コンテナーで、**[バックアップ ポリシー]** をクリックし、**[追加]** をクリックします。 

   ![バックアップ ポリシー新規作成ダイアログを開く](./media/backup-azure-sql-database/new-policy-workflow.png)

   **[追加]** メニューが表示されます。

2. **[追加]** メニューの **[Azure VM 内の SQL Server]** をクリックします。

   ![新しいバックアップ ポリシーのポリシーの種類を選択する](./media/backup-azure-sql-database/policy-type-details.png)

   [Azure VM 内の SQL Server] を選択すると、ポリシーの種類が定義されて、[バックアップ ポリシー] メニューが開きます。 新しい SQL Server バックアップ ポリシーに必要なフィールドが **[バックアップ ポリシー]** メニューに表示されます。

3. **[ポリシー名]** に新しいポリシーの名前を入力します。

4. 完全バックアップは必須です。**[完全バックアップ]** オプションをオフにすることはできません。 **[完全バックアップ]** をクリックし、ポリシーを表示して編集します。 バックアップ ポリシーを変更しない場合でも、ポリシーの詳細を表示する必要があります。

    ![バックアップ ポリシーの新しいフィールド](./media/backup-azure-sql-database/full-backup-policy.png)

    **完全バックアップのポリシー** メニューで、**[バックアップ頻度]** として **[毎日]** または **[毎週]** を選択します。 **[毎日]** を選択する場合は、バックアップ ジョブが開始されるときに、時刻とタイム ゾーンを選択します。 日次の完全バックアップを選択する場合は、差分バックアップを作成できません。

   ![日次の設定](./media/backup-azure-sql-database/daily-interval.png)

    **[毎週]** を選択する場合は、バックアップ ジョブが開始されるときに、曜日、時刻、およびタイム ゾーンを選択します。

   ![週次の設定](./media/backup-azure-sql-database/weekly-interval.png)

5. 既定では、**[リテンション期間]** のすべてのオプション (毎日、毎週、月単位、および年単位) が選択されています。 不要なリテンション期間の制限の選択を解除します。 使用する間隔を設定します。 **完全バックアップのポリシー** メニューで、**[OK]** を選択して設定を確定します。

   ![[リテンション期間] の間隔の設定](./media/backup-azure-sql-database/retention-range-interval.png)

    復旧ポイントは、そのリテンション期間の範囲に基づいて、リテンション期間に対してタグ付けされます。 たとえば、日次での完全バックアップを選択した場合、日ごとにトリガーされる完全バックアップは 1 回だけです。 特定の曜日のバックアップがタグ付けされ、週次でのリテンション期間の範囲と週次でのリテンション期間の設定に基づいて保持されます。 月次および年次のリテンション期間の範囲でも、同様の動作になります。

6. 差分バックアップ ポリシーを追加するには、**[差分バックアップ]** を選択します。 **差分バックアップのポリシー** メニューが開きます。 

   ![差分バックアップのポリシー メニューを開く](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

    **差分バックアップのポリシー** メニューで、**[有効]** を選択して頻度とリテンション期間の制御を開きます。 最多で、1 日に 1 回の差分バックアップをトリガーできます。
    
    > [!Important] 
    > 差分バックアップは、最大 180 日間保持できます。 より長いリテンション期間が必要な場合は、完全バックアップを使用する必要があります。
    >

   ![差分バックアップのポリシーを編集する](./media/backup-azure-sql-database/enable-differential-backup-policy.png)

    **[OK]** を選択してポリシーを保存し、**[バックアップ ポリシー]** のメイン メニューに戻ります。

7. トランザクション ログ バックアップ ポリシーを追加するには、**[ログ バックアップ]** を選択します。 **[ログ バックアップ]** メニューが開きます。

    **[ログ バックアップ]** メニューで、**[有効]** を選択して、頻度とリテンション期間の制御を設定します。 ログ バックアップは、毎 15 分の頻度で実行でき、最大 35 日間保持することが可能です。 **[OK]** を選択してポリシーを保存し、**[バックアップ ポリシー]** のメイン メニューに戻ります。

   ![ログ バックアップ ポリシーを編集する](./media/backup-azure-sql-database/log-backup-policy-editor.png)

8. **[バックアップ ポリシー]** メニューで、**SQL バックアップの圧縮**を有効にするかどうかを選択します。 圧縮は既定で、無効になっています。

    バックエンドでは、Azure Backup は SQL ネイティブ バックアップの圧縮を使用します。

9. バックアップ ポリシーに対する編集が完了したら、**[OK]** を選択します。 

   ![新しいバックアップ ポリシーを確定する](./media/backup-azure-sql-database/backup-policy-click-ok.png)

## <a name="restore-a-sql-database"></a>SQL データベースを復元する
Azure Backup は、トランザクション ログ バックアップを使用して、特定の日付または時刻 (秒) へ個々のデータベースを復元する機能を提供しています。 Azure Backup は、復元時刻を基に、完全、差分、または一連のログ バックアップから、データの復元に必要になる適切なバックアップを自動的に判断します。

特定の完全または差分バックアップを選択して、特定の時刻ではなく、特定の復旧ポイントまで復元することもできます。

### <a name="pre-requisite-before-triggering-a-restore"></a>復元をトリガーする前の前提条件

1. このデータベースを同じ Azure リージョン内の SQL Server のインスタンスに復元することができます。 宛先サーバーがソースと同じ Recovery Services コンテナーに登録されている必要があります。  
2. TDE で暗号化されたデータベースを別の SQL Server に復元するには、[こちら](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017)で説明されている手順に従って、最初に証明書を宛先サーバーに復元してください。
3. "マスター" データベースの復元をトリガーする前に、スタートアップ オプション `-m AzureWorkloadBackup` を使用してシングル ユーザー モードで SQL Server インスタンスを起動してください。 `-m` オプションへの引数はクライアントの名前です。 このクライアントのみが、接続を開くことを許可されます。 すべてのシステム データベース (モデル、マスター、msdb) の場合は、復元をトリガーする前に SQL エージェント サービスを停止してください。 これらのいずれかのデータベースへの接続を盗もうとする可能性があるアプリケーションはすべて閉じます。

### <a name="steps-to-restore-a-database"></a>データベースを復元する手順:

1. SQL 仮想マシンと共に登録された Recovery Services コンテナーを開きます。

2. **[Recovery Services コンテナー]** ダッシュボードの **[使用量]** で、**[バックアップ項目]** を選択して **[バックアップ項目]** メニューを開きます。

    ![[バックアップ項目] メニューを開く](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. **[バックアップ項目]** メニューの **[バックアップの管理の種類]** で、**[SQL in Azure VM]\(Azure VM 内の SQL\)** を選択します。 

    ![[SQL in Azure VM]\(Azure VM 内の SQL\) を選択する](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    **[バックアップ項目]** メニューに、SQL データベースの一覧が表示されます。 

4. SQL データベースの一覧で、復元するデータベースを選択します。

    ![復元するデータベースの選択](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    データベースを選択すると、そのデータベースのメニューが開きます。 このメニューは、次のようなデータベースのバックアップの詳細を提示します。

    * 最古および最新の復元ポイント
    * 直近 24 時間のログ バックアップ ステータス (トランザクション ログ バックアップ用に構成されている場合、完全および一括ログ復旧モデルのデータベースに対して)

5. 選択したデータベースのメニューで、**[DB の復元]** を選択します。 **[復元]** メニューが開きます。

    ![[DB の復元] を選択する](./media/backup-azure-sql-database/restore-db-button.png)

    **[復元]** メニューが開くと、**[復元の構成]** メニューも開きます。 **[復元の構成]** メニューは、復元を構成するための最初の手順です。 このメニューを使用して、データを復元する場所を選択します。 オプションは次のとおりです。
    - **別の場所**: 別の場所にデータベースを復元し、元のソース データベースを保持します。
    - **DB の上書き**: 元のソースと同じ SQL Server インスタンスにデータを復元します。 このオプションでは、元のデータベースが上書きされます。

    > [!Important]
    > 選択したデータベースが Always On 可用性グループに属している場合、SQL Server はデータベースの上書きを許可しません。 この場合は、**[別の場所]** オプションのみが有効です。
    >

    ![[復元の構成] メニュー](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>別の場所に復元する

この手順では、別の場所にデータを復元する方法を示します。 復元時にデータベースを上書きするには、「[データベースを復元して上書きする](backup-azure-sql-database.md#restore-and-overwrite-the-database)」に進んでください。 この段階では、Recovery Services コンテナーが開かれていて、**[復元の構成]** メニューが表示されています。 この段階になっていない場合は、「[SQL データベースを復元する](backup-azure-sql-database.md#restore-a-sql-database)」から開始してください。

> [!NOTE]
> このデータベースを同じ Azure リージョン内の SQL Server のインスタンスに復元することができます。 宛先サーバーが Recovery Services コンテナーに登録されている必要があります。 
>

**[復元の構成]** メニューの **[サーバー]** ドロップダウン リスト ボックスには、Recovery Services コンテナーと共に登録された SQL Server インスタンスのみが表示されます。 目的のサーバーが一覧にない場合は、「[SQL Server データベースを検出する](backup-azure-sql-database.md#discover-sql-server-databases)」を参照してサーバーを見つけます。 検出プロセスの間に、Recovery Services コンテナーに新しいサーバーが登録されます。

1. **[復元の構成]** メニューで、次の手順を実行します。

    * **[復元先]** で、**[別の場所]** を選択します。
    * **[サーバー]** ドロップダウン リスト ボックスを開き、データベースを復元する SQL Server インスタンスを選択します。
    * **[インスタンス]** ドロップダウン リスト ボックスを開き、SQL Server インスタンスを選択します。
    * **[復元される DB 名]** ボックスに、ターゲット データベースの名前を入力します。
    * 必要に応じて、**[選択した SQL インスタンスに既に同じ名前の DB が存在する場合に上書きする]** を選択します。
    * **[OK]** を選択して復元先の構成を完了し、復元ポイントの選択に進みます。

    ![[復元の構成] メニューの値を指定する](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. **[復元ポイントの選択]** メニューで、**[ログ (特定の時点)]** または **[完全および差分]** を復元ポイントとして選択します。 指定した特定の時点のログまで復元するには、引き続きこの手順を実行してください。 完全および差分の復元ポイントを復元するには、手順 3 に進んでください。

    ![[復元ポイントの選択] メニュー](./media/backup-azure-sql-database/recovery-point-menu.png)

    特定の時点への復元は、完全および一括ログ復旧モデルを使ったデータベースのログ バックアップのみで利用可能です。 特定の時点に復元するには、次の手順を実行します。

    1. **[ログ (特定の時点)]** を復元の種類として選択します。

        ![復元ポイントの種類を選択する](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. **[復元の日付/時刻]** で、小さいカレンダー アイコンを選択して、**カレンダー**を開きます。 **カレンダー**では、太字の日付に復旧ポイントが含まれ、現在の日付が強調表示されています。 復旧ポイントがある日付を選択します。 復旧ポイントがない日付は選択できません。

        ![カレンダーを開く](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        日付を選択すると、タイムライン グラフに連続した範囲の使用可能な復旧ポイントが表示されます。

    3. タイムライン グラフまたは **[時刻]** ダイアログ ボックスを使用して、復旧ポイントの特定の時刻を指定します。 **[OK]** を選択して、復元ポイントの手順を完了します。
    
       ![カレンダーを開く](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        **[復元ポイントの選択]** メニューが閉じられ、**[詳細な構成]** メニューが開きます。

       ![[詳細な構成] メニュー](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. **[詳細な構成]** メニューで、次の手順を実行します。

        * 復元後のデータベースを非運用状態のままにしておくために、**[NORECOVERY を使用して復元]** を **[有効]** に設定します。
        * 宛先サーバー上で復元場所を変更するには、**[ターゲット]** 列に新しいパスを入力します。
        * **[OK]** を選択して設定を承認します。 **[詳細な構成]** メニューを閉じます。

    5. **[復元]** メニューで、**[復元]** を選択して復元ジョブを開始します。 **[通知]** 領域で復元の進行状況を追跡するか、またはデータベース メニューの **[Restore jobs]\(復元ジョブ\)** を選択します。

       ![復元ジョブの進行状況](./media/backup-azure-sql-database/restore-job-notification.png)

3. **[復元ポイントの選択]** メニューで、**[ログ (特定の時点)]** または **[完全および差分]** を復元ポイントとして選択します。 特定の時点のログを復元するには、手順 2 に戻ります。 この手順では、完全または差分の特定の復元ポイントを復元します。 直近 30 日間のすべての完全および差分の復旧ポイントを確認できます。 30 日間よりも古い復旧ポイントを確認するには、**[フィルター]** を選択して **[復元ポイントのフィルター]** メニューを開きます。 差分復旧ポイントの場合、Azure Backup では最初に適切な完全復旧ポイントを復元してから、選択した差分復旧ポイントを適用します。

    ![[復元ポイントの選択] メニュー](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. **[復元ポイントの選択]** メニューで、**[Full & Differential]\(完全および差分\)** を選択します。

       ![[完全および差分] を選択する](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        使用可能な復旧ポイントの一覧がメニューに表示されます。

    2. 一覧から復旧ポイントを選択し、**[OK]** を選択して復元ポイントの手順を完了します。 

        ![完全復旧ポイントを選択する](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        **[復元ポイント]** メニューが閉じられ、**[詳細な構成]** メニューが開きます。

        ![[詳細な構成] メニュー](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. **[詳細な構成]** メニューで、次の手順を実行します。

        * 復元後のデータベースを非運用状態のままにしておくために、**[NORECOVERY を使用して復元]** を **[有効]** に設定します。 **[Restore with NORECOVERY]\(NORECOVERY による復元\)** は既定で無効になっています。
        * 宛先サーバー上で復元場所を変更するには、**[ターゲット]** 列に新しいパスを入力します。
        * **[OK]** を選択して設定を承認します。 **[詳細な構成]** メニューを閉じます。

    4. **[復元]** メニューで、**[復元]** を選択して復元ジョブを開始します。 **[通知]** 領域で復元の進行状況を追跡するか、またはデータベース メニューの **[Restore jobs]\(復元ジョブ\)** を選択します。

       ![復元ジョブの進行状況](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-and-overwrite-the-database"></a>データベースを復元して上書きする

この手順では、データを復元してデータベースを上書きする方法を示します。 別の場所に復元するには、「[別の場所に復元する](backup-azure-sql-database.md#restore-to-an-alternate-location)」に進んでください。 この段階では、Recovery Services コンテナーが開かれていて、**[復元の構成]** メニューが表示されています (次の図を参照してください)。 この段階になっていない場合は、「[SQL データベースを復元する](backup-azure-sql-database.md#restore-a-sql-database)」から開始してください。

![[復元の構成] メニュー](./media/backup-azure-sql-database/restore-overwrite-db.png)

**[復元の構成]** メニューの **[サーバー]** ドロップダウン リスト ボックスには、Recovery Services コンテナーと共に登録された SQL Server インスタンスのみが表示されます。 目的のサーバーが一覧にない場合は、「[SQL Server データベースを検出する](backup-azure-sql-database.md#discover-sql-server-databases)」を参照してサーバーを見つけます。 検出プロセスの間に、Recovery Services コンテナーに新しいサーバーが登録されます。

1. **[復元の構成]** メニューで、**[DB の上書き]** を選択し、**[OK]** を選択して復元先の構成を完了します。 

   ![[DB の上書き] を選択する](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

    **[サーバー]**、**[インスタンス]**、および **[復元される DB 名]** の設定は必要ありません。

2. **[復元ポイントの選択]** メニューで、**[ログ (特定の時点)]** または **[完全および差分]** を復元ポイントとして選択します。 指定した特定の時点のログまで復元するには、引き続きこの手順を実行してください。 完全および差分の復元ポイントを復元するには、手順 3 に進んでください。

    ![[復元ポイントの選択] メニュー](./media/backup-azure-sql-database/recovery-point-menu.png)

    特定の時点への復元は、完全および一括ログ復旧モデルを使ったデータベースのログ バックアップのみで利用可能です。 指定した秒に復元するには、次の手順を実行します。

    1. **[ログ (特定の時点)]** を復元ポイントとして選択します。

        ![復元ポイントの種類を選択する](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. **[復元の日付/時刻]** で、小さいカレンダー アイコンを選択して、**カレンダー**を開きます。 **カレンダー**では、太字の日付に復旧ポイントが含まれ、現在の日付が強調表示されています。 復旧ポイントがある日付を選択します。 復旧ポイントがない日付は選択できません。

        ![カレンダーを開く](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        日付を選択すると、タイムライン グラフに使用可能な復旧ポイントが表示されます。

    3. タイムライン グラフまたは **[時刻]** ダイアログ ボックスを使用して、復旧ポイントの特定の時刻を指定します。 **[OK]** を選択して、復元ポイントの手順を完了します。
    
       ![カレンダーを開く](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        **[復元ポイントの選択]** メニューが閉じられ、**[詳細な構成]** メニューが開きます。

       ![[詳細な構成] メニュー](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. **[詳細な構成]** メニューで、次の手順を実行します。

        * 復元後のデータベースを非運用状態のままにしておくために、**[NORECOVERY を使用して復元]** を **[有効]** に設定します。
        * 宛先サーバー上で復元場所を変更するには、**[ターゲット]** 列に新しいパスを入力します。
        * **[OK]** を選択して設定を承認します。 **[詳細な構成]** メニューを閉じます。

    5. **[復元]** メニューで、**[復元]** を選択して復元ジョブを開始します。 **[通知]** 領域で復元の進行状況を追跡するか、またはデータベース メニューの **[Restore jobs]\(復元ジョブ\)** を選択します。

       ![復元ジョブの進行状況](./media/backup-azure-sql-database/restore-job-notification.png)

3. **[復元ポイントの選択]** メニューで、**[ログ (特定の時点)]** または **[完全および差分]** を復元ポイントとして選択します。 特定の時点のログを復元するには、手順 2 に戻ります。 この手順では、完全または差分の特定の復元ポイントを復元します。 直近 30 日間のすべての完全および差分の復旧ポイントを確認できます。 30 日間よりも古い復旧ポイントを確認するには、**[フィルター]** を選択して **[復元ポイントのフィルター]** メニューを開きます。 差分復旧ポイントの場合、Azure Backup では最初に適切な完全復旧ポイントを復元してから、選択した差分復旧ポイントを適用します。

    ![[復元ポイントの選択] メニュー](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. **[復元ポイントの選択]** メニューで、**[Full & Differential]\(完全および差分\)** を選択します。

       ![[完全および差分] を選択する](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        使用可能な復旧ポイントの一覧がメニューに表示されます。

    2. 一覧から復旧ポイントを選択し、**[OK]** を選択して復元ポイントの手順を完了します。 

        ![完全復旧ポイントを選択する](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        **[復元ポイント]** メニューが閉じられ、**[詳細な構成]** メニューが開きます。

        ![[詳細な構成] メニュー](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. **[詳細な構成]** メニューで、次の手順を実行します。

        * 復元後のデータベースを非運用状態のままにしておくために、**[NORECOVERY を使用して復元]** を **[有効]** に設定します。 **[Restore with NORECOVERY]\(NORECOVERY による復元\)** は既定で無効になっています。
        * 宛先サーバー上で復元場所を変更するには、**[ターゲット]** 列に新しいパスを入力します。
        * **[OK]** を選択して設定を承認します。 **[詳細な構成]** メニューを閉じます。

    4. **[復元]** メニューで、**[復元]** を選択して復元ジョブを開始します。 **[通知]** 領域で復元の進行状況を追跡します。またはデータベース メニューの **[Restore jobs]\(復元ジョブ\)** を選択して復元の進行状況を追跡します。

       ![復元ジョブの進行状況](./media/backup-azure-sql-database/restore-job-notification.png)

## <a name="manage-azure-backup-operations-for-sql-on-azure-vms"></a>Azure VM 上で SQL に対する Azure Backup 操作を管理する

ここでは、Azure 仮想マシン上で SQL に利用できる、さまざまな Azure Backup 管理操作に関する情報を提供します。 次のような高度な操作があります。

* ジョブの監視
* [バックアップ アラート]
* SQL Database での保護の停止
* SQL Database での保護の再開
* アドホック バックアップ ジョブのトリガー
* SQL Server を実行しているサーバーの登録解除

### <a name="monitor-backup-jobs"></a>バックアップ ジョブを監視する
エンタープライズ クラスのソリューションである Azure Backup は、高度なバックアップ アラートやすべてのエラーの通知を提供します  (「[バックアップ アラートを表示する](backup-azure-sql-database.md#view-backup-alerts)」を参照)。特定のジョブを監視するには、要件に従って次のいずれかのオプションを使用します。

#### <a name="use-the-azure-portal-for-adhoc-operations"></a>アドホック操作に Azure Portal を使用する
Azure Backup は、**[バックアップ ジョブ]** ポータルに、手動でトリガーされたジョブまたはアドホック ジョブをすべて表示します。 **[バックアップ ジョブ]** ポータルで使用可能なジョブを次に示します。
- すべてのバックアップの構成操作
- 手動でトリガーされたバックアップ操作
- 復元操作
- データベースの登録および検出操作
- バックアップの停止操作 

![[バックアップ ジョブ] ポータル](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> 完全、差分、およびログ バックアップを含むスケジュールされたバックアップ ジョブはすべて **[バックアップ ジョブ]** ポータルには表示されません。 次のセクションで説明されているように、スケジュールされたバックアップ ジョブを監視するには、SQL Server Management Studio を使用します。
>

#### <a name="use-sql-server-management-studio-for-backup-jobs"></a>[Use SQL Server Management Studio for backup jobs] (バックアップ ジョブに SQL Server Management Studio を使用する)
Azure Backup では、すべてのバックアップ操作に SQL ネイティブ API を使用します。 ネイティブ API を使用して、msdb データベース内の [SQL バックアップセット テーブル](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017)からすべてのジョブ情報をフェッチします。

次の例は、**DB1** という名前のデータベースのすべてのバックアップ ジョブをフェッチするためのクエリです。 高度な監視を行うには、このクエリをカスタマイズします。

```
select CAST (
Case type
                when 'D' 
                                 then 'Full'
                when  'I'
                               then 'Differential' 
                ELSE 'Log'
                END         
                AS varchar ) AS 'BackupType',
database_name, 
server_name,
machine_name,
backup_start_date,
backup_finish_date,
DATEDIFF(SECOND, backup_start_date, backup_finish_date) AS TimeTakenByBackupInSeconds,
backup_size AS BackupSizeInBytes
  from msdb.dbo.backupset where user_name = 'NT SERVICE\AzureWLBackupPluginSvc' AND database_name =  <DB1>  
 
```

### <a name="view-backup-alerts"></a>バックアップ アラートを表示する

ログ バックアップは 15 分ごとに行われるため、バックアップ ジョブを適宜監視することは面倒に感じられる場合があります。 Azure Backup では、このような状況に対応します。 すべてのバックアップ エラーに対して電子メール アラートがトリガーされます。 アラートは、エラー コードごとにデータベース レベルで統合されます。 電子メール アラートは、データベースの最初のバックアップ エラーに対してのみ送信されます。 データベースのすべてのエラーを監視するには、Azure Portal にサインインします。 

バックアップ アラートを監視するには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com) で、Azure サブスクリプションにサインインします。

2. SQL 仮想マシンと共に登録された Recovery Services コンテナーを開きます。

3. **[Recovery Services コンテナー]** ダッシュボードで、**[アラートとイベント]** を選択します。 

   ![[アラートとイベント] を選択する](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. **[アラートとイベント]** メニューで、**[バックアップ アラート]** を選択してアラートの一覧を表示します。

   ![[バックアップ アラート] を選択する](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

### <a name="stop-protection-for-a-sql-server-database"></a>SQL Server データベースの保護を停止する

SQL Server データベースの保護を停止する場合、Azure Backup は復旧ポイントを維持するかどうかを確認します。 SQL データベースの保護を停止するには、次の 2 つの方法があります。

* 今後のバックアップ ジョブすべてを停止し、すべての復旧ポイントを削除する。
* 今後のバックアップ ジョブすべてを停止するが、復旧ポイントはそのままにする。

復旧ポイントをそのままにしておくと、コストが発生します。 SQL の復旧ポイントにより、SQL で保護されたインスタンス価格が請求されるだけでなく、ストレージ容量が消費されます。 SQL に対する Azure Backup の価格設定の詳細情報については、[Azure Backup の価格のページ](https://azure.microsoft.com/pricing/details/backup/)を参照してください。 

データベースの保護を停止するには、次の手順を実行します。

1. SQL 仮想マシンと共に登録された Recovery Services コンテナーを開きます。

2. **[Recovery Services コンテナー]** ダッシュボードの **[使用量]** で、**[バックアップ項目]** を選択して **[バックアップ項目]** メニューを開きます。

    ![[バックアップ項目] メニューを開く](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. **[バックアップ項目]** メニューの **[バックアップの管理の種類]** で、**[SQL in Azure VM]\(Azure VM 内の SQL\)** を選択します。 

    ![[SQL in Azure VM]\(Azure VM 内の SQL\) を選択する](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    **[バックアップ項目]** メニューに、SQL データベースの一覧が表示されます。 

4. SQL データベースの一覧で、保護を停止するデータベースを選択します。

    ![保護を停止するデータベースを選択する](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    データベースを選択すると、そのデータベースのメニューが開きます。

5. 選択したデータベースのメニューで、**[バックアップの停止]** を選択します。 

    ![[バックアップの停止] を選択する](./media/backup-azure-sql-database/stop-db-button.png)

    **[バックアップの停止]** メニューが開きます。

6. **[バックアップの停止]** メニューで、**[バックアップ データの保持]** または **[バックアップ データの削除]** を選択します。 必要に応じて、保護を停止する理由とコメントを指定します。

    ![[バックアップの停止] メニュー](./media/backup-azure-sql-database/stop-backup-button.png)

7. **[バックアップの停止]** を選択して、データベースでの保護を停止します。 

### <a name="resume-protection-for-a-sql-database"></a>SQL Database での保護の再開

SQL データベースの保護を停止するときに **[バックアップ データの保持]** オプションを選択した場合は、保護を再開することができます。 バックアップ データが保持されていなかった場合、保護は再開できません。 

1. SQL データベースの保護を再開するには、バックアップ項目を開き、**[バックアップの再開]** を選択します。

    ![[バックアップの再開] を選択してデータベースの保護を再開する](./media/backup-azure-sql-database/resume-backup-button.png)

   **[バックアップ ポリシー]** メニューが開きます。

2. **[バックアップ ポリシー]** メニューで、ポリシーを選択して、**[保存]** を選択します。

### <a name="trigger-an-adhoc-backup"></a>アドホック バックアップ ジョブをトリガーする

必要に応じて、アドホック バックアップをトリガーします。 アドホック バックアップには次の 4 種類があります。

* 完全バックアップ
* コピーのみの完全バックアップ
* 差分バックアップ
* ログ バックアップ

各種類の詳細については、[SQL バックアップの種類](https://docs.microsoft.com/sql/relational-databases/backup-restore/backup-overview-sql-server?view=sql-server-2017#types-of-backups)に関する記事を参照してください。

### <a name="unregister-a-sql-server-instance"></a>SQL Server インスタンスを登録解除する

保護を削除した後の、まだコンテナーを削除していないときに SQL Server インスタンスを登録解除するには、次の手順を実行します。

1. SQL 仮想マシンと共に登録された Recovery Services コンテナーを開きます。

2. **[Recovery Services コンテナー]** ダッシュボードの **[管理]** で、**[バックアップ インフラストラクチャ]** を選択します。  

   ![[バックアップ インフラストラクチャ] を選択する](./media/backup-azure-sql-database/backup-infrastructure-button.png)

3. **[管理サーバー]** で、**[保護されたサーバー]** を選択します。

   ![[保護されたサーバー] を選択する](./media/backup-azure-sql-database/protected-servers.png)

    **[保護されたサーバー]** メニューが開きます。 

4. **[保護されたサーバー]** メニューで、登録解除するサーバーを選択します。 コンテナーを削除するには、すべてのサーバーの登録を解除する必要があります。

5. **[保護されたサーバー]** メニューで、保護されているサーバーを右クリックして、**[削除]** を選択します。 

   ![[削除] を選択する](./media/backup-azure-sql-database/delete-protected-server.png)

## <a name="faq"></a>FAQ

次のセクションでは、SQL データベースのバックアップに関する追加情報を提供します。

### <a name="can-i-throttle-the-speed-of-the-sql-server-backup-policy"></a>SQL Server バックアップ ポリシーの速度を調整できますか?

はい。 バックアップ ポリシーが実行される速度を調整して、SQL Server インスタンスへの影響を最小限に抑えることができます。

設定を変更するには:

1. SQL Server インスタンスで、C:\Program Files\Azure Workload Backup\bin フォルダー内の **TaskThrottlerSettings.json** ファイルを開きます。

2. TaskThrottlerSettings.json ファイルで、**DefaultBackupTasksThreshold** の設定を小さな値 (たとえば、5) に変更します。

3. 変更を保存します。 ファイルを閉じます。

4. SQL Server インスタンスで、**タスク マネージャー**を開きます。 **Azure Backup ワークロード コーディネーター サービス**を再開します。

### <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>セカンダリ レプリカから完全バックアップを実行できますか?

いいえ。 この機能はサポートされていません。

### <a name="do-successful-backup-jobs-create-alerts"></a>成功したバックアップ ジョブでアラートが作成されますか?

いいえ。 成功したバックアップ ジョブではアラートは生成されません。 アラートは、失敗したバックアップ ジョブに対してのみ送信されます。

### <a name="can-i-see-details-for-scheduled-backup-jobs-in-the-jobs-menu"></a>スケジュールされたバックアップ ジョブの詳細を [ジョブ] メニューで確認できますか?

いいえ。 **[バックアップ ジョブ]** メニューにはアドホック ジョブの詳細が表示されますが、スケジュールされたバックアップ ジョブは表示されません。 スケジュールされたバックアップ ジョブのいずれかが失敗した場合は、失敗したジョブのアラートで詳細を確認できます。 スケジュールされたバックアップ ジョブとアドホック バックアップ ジョブをすべて監視するには、[SQL Server Management Studio](backup-azure-sql-database.md#use-sql-server-management-studio-for-backup-jobs) を使用します。

### <a name="when-i-select-a-sql-server-instance-are-future-databases-automatically-added"></a>SQL Server インスタンスを選択すると、将来のデータベースは自動的に追加されますか?

いいえ。 SQL Server インスタンスの保護を構成する場合、サーバー レベルのオプションを選択すると、すべてのデータベースが追加されます。 保護を構成した後に SQL Server インスタンスにデータベースを追加する場合、新しいデータベースを保護するにはそれらを手動で追加する必要があります。 これらのデータベースは、構成された保護に自動的には含まれません。

### <a name="if-i-change-the-recovery-model-how-do-i-restart-protection"></a>復旧モデルを変更した場合、保護を再開するにはどうすればよいですか?

完全バックアップをトリガーします。 ログ バックアップは想定どおりに開始されます。

### <a name="can-i-protect-sql-always-on-availability-groups-where-the-primary-replica-is-on-premises"></a>プライマリ レプリカがオンプレミスにある SQL Always On 可用性グループを保護できますか?

いいえ。 Azure Backup では、Azure で実行している SQL Server を保護します。 Azure とオンプレミスのマシンの間にまたがっている可用性グループ (AG) は、プライマリ レプリカが Azure で実行されている場合にのみ保護できます。 また、Azure Backup では、Recovery Services コンテナーと同じ Azure リージョンで実行されているノードのみ保護します。

## <a name="next-steps"></a>次の手順

Azure Backup の詳細については、暗号化された仮想マシンをバックアップするための Azure PowerShell サンプルを参照してください。

> [!div class="nextstepaction"]
> [暗号化された VM のバックアップ](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
