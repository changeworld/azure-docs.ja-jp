---
title: Azure への SQL Server データベースのバックアップ |Microsoft Docs
description: このチュートリアルでは、Azure への SQL Server のバックアップについて説明します。 また、SQL Server の復旧についても説明します。
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
ms.date: 7/6/2018
ms.author: markgal;anuragm
ms.custom: ''
ms.openlocfilehash: 32f45b66c4b1d22da3ffc4310a8a47c17319301f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38302825"
---
# <a name="back-up-sql-server-database-in-azure"></a>Azure での SQL Server データベースのバックアップ

SQL Server データベースは、低い回復ポイントの目標値 (RPO) と長期のリテンション期間を必要とする重要なワークロードです。 Azure Backup は、インフラストラクチャを必要としない、つまり、管理する複雑なバックアップ サーバー、管理エージェント、バックアップ ストレージなどが存在しない SQL Server バックアップ ソリューションを提供します。 Azure Backup では、すべての SQL Server のバックアップだけでなく、さまざまなワークロードも含めた一元管理を提供しています。

 この記事では、次のことについて説明します。

> [!div class="checklist"]
> * Azure に SQL Server をバックアップするための前提条件
> * Recovery Services コンテナーの作成および使用
> * SQL Server データベース バックアップの構成
> * 復旧ポイントに対するバックアップ (またはリテンション期間) ポリシーの設定
> * データベースを復元する方法

この記事の手順を開始するには、事前に Azure で SQL Server を実行している必要があります。 [SQL マーケットプレイスの仮想マシンを使用して、SQL Server を迅速に作成できます](../sql-database/sql-database-get-started-portal.md)。

## <a name="public-preview-limitations"></a>パブリック プレビューの制限事項

次の項目は、パブリック プレビューの既知の制限事項です。

- SQL 仮想マシンでは、Azure パブリック IP アドレスにアクセスするためにインターネット接続を必要とします。 詳細については、「[ネットワーク接続を確立する](backup-azure-sql-database.md#establish-network-connectivity)」セクションを参照してください。
- 最大 2000 個の SQL データベースを 1 つの Recovery Services コンテナーで保護できます。 追加の SQL データベースは、別個の Recovery Services コンテナーに格納される必要があります。
- [分散型の可用性グループのバックアップには、制限があります](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017)。
- SQL フェールオーバー クラスター インスタンス (FCI) はサポートされていません。
- Azure Portal を使用して Azure Backup を構成し、SQL Server データベースを保護します。 Azure PowerShell、CLI、および REST API のサポートは現在、使用できません。

## <a name="supported-azure-geos"></a>サポートされる Azure GEO

- オーストラリア南東部 (ASE) 
- ブラジル南部 (BRS)
- カナダ中部 (CNC)
- カナダ東部 (CE)
- 米国中部 (CUS)
- 東アジア (EA)
- オーストラリア東部 (AE) 
- 米国東部 (EUS)
- 米国東部 2 (EUS2)
- 東日本 (JPE)
- 西日本 (JPW)
- インド中部 (INC) 
- インド南部 (INS)
- 韓国中部 (KRC)
- 韓国南部 (KRS)
- 米国中北部 (NCUS) 
- 北ヨーロッパ (NE) 
- 米国中南部 (SCUS) 
- 東南アジア (SEA)
- 英国南部 (UKS) 
- 英国西部 (UKW) 
- 西ヨーロッパ (WE) 
- 米国西部 (WUS)
- 米国中西部 (WCUS)
- 米国西部 2 (WUS 2) 

## <a name="supported-operating-systems-and-versions-of-sql-server"></a>サポートされるオペレーティング システムと SQL Server のバージョン

次のオペレーティング システムがサポートされています。 SQL マーケットプレイスの Azure 仮想マシンと、マーケットプレイス以外の (SQL Server が手動でインストールされる) 仮想マシンがサポートされています。

### <a name="supported-operating-systems"></a>サポートされているオペレーティング システム

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

Linux は現在、サポートされていません。

### <a name="supported-versionseditions-of-sql-server"></a>サポートされている SQL Server のバージョン/エディション

- SQL 2012 Enterprise、Standard、Web、Developer、Express
- SQL 2014 Enterprise、Standard、Web、Developer、Express
- SQL 2016 Enterprise、Standard、Web、Developer、Express
- SQL 2017 Enterprise、Standard、Web、Developer、Express


## <a name="prerequisites-for-using-azure-backup-to-protect-sql-server"></a>Azure Backup を使用して SQL Server を保護するための前提条件 

SQL Server データベースをバックアップする前に、次の条件を確認してください。 :

- SQL Server をホストする仮想マシンとして、同じリージョンまたはロケールの [Recovery Services コンテナーを作成](backup-azure-sql-database.md#create-a-recovery-services-vault)または特定する。
- SQL データベースのバックアップに必要な[仮想マシンの権限を確認](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms)する。
- [SQL 仮想マシンがネットワーク接続を備えている](backup-azure-sql-database.md#establish-network-connectivity)。

> [!NOTE]
> SQL Server データベースをバックアップするために一度に保持できるバックアップ ソリューションは、1 つだけです。 この機能を使用する前に他の SQL バックアップをすべて無効にしてください。そうしないと、バックアップは妨害されて失敗します。 IaaS VM 向けの Azure Backup は、競合することなく SQL バックアップと共に有効化できます。 
>

お使いの環境でこれらの条件が満たされている場合は、[コンテナーを構成して SQL データベースを保護する](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database)方法に関するページを参照してください。 いずれかの前提条件が不足している場合は、引き続きこのセクションに目を通してください。


## <a name="establish-network-connectivity"></a>ネットワーク接続を確立する

すべての操作において、SQL 仮想マシンでは、Azure パブリック IP アドレスへの接続を必要とします。 パブリック IP アドレスへの接続がないと、(データベースの検出、バックアップの構成、スケジュールされたバックアップ、復旧ポイントの復元などの) SQL 仮想マシンの操作が失敗します。 次のいずれかの方法を使用して、バックアップ トラフィックへの明確なパスを指定します。

- Azure データ センターの IP 範囲をホワイトリストに登録する - Azure データ センターの IP 範囲をホワイトリストに登録するには、[IP 範囲と手順の詳細に関するダウンロード センターのページ](https://www.microsoft.com/download/details.aspx?id=41653)を活用してください。 
- トラフィックをルーティングするために HTTP プロキシ サーバーをデプロイする - SQL データベースを VM でバックアップしている場合、VM 上のバックアップ拡張機能では HTTPS API を使用して Azure Backup に管理コマンドを送信し、Azure Storage にデータを送信します。 また、バックアップ拡張機能では、認証に Azure Active Directory (AAD) を使用します。 パブリック インターネットにアクセスできるように構成されたコンポーネントは HTTP プロキシのみであるため、HTTP プロキシ経由でこれらの 3 つのサービスのバックアップ拡張機能のトラフィックをルーティングします。

トレードオフの関係にあるのは、管理容易性、詳細な制御、およびコストです。

> [!NOTE]
>Azure Backup 用のサービス タグは必ず、一般提供で入手できます。
>

| オプション | 長所 | 短所 |
| ------ | ---------- | ------------- |
| IP 範囲をホワイトリストに登録する | 追加のコストが発生しない。 <br/> NSG でアクセスを開くには、**Set-AzureNetworkSecurityRule** コマンドレットを使用する。 | 影響を受ける IP の範囲が時間の経過と共に変化するため、管理が複雑である。 <br/>ストレージだけでなく、Azure 全体へのアクセスを提供することになる。|
| HTTP プロキシを使用する   | 許可するストレージ URL をプロキシで詳細に制御可能。 <br/>VM に対するインターネット アクセスを単一の場所で実現。 <br/> Azure の IP アドレスの変更の影響を受けない。 | プロキシ ソフトウェアで VM を実行するための追加のコストが発生する。 |

## <a name="set-permissions-for-non-marketplace-sql-vms"></a>マーケットプレイス以外の SQL VM に権限を設定する

仮想マシンをバックアップするために、Azure Backup には **AzureBackupWindowsWorkload** 拡張機能がインストールされている必要があります。 Azure マーケットプレイスの仮想マシンを使用している場合は、このセクションを省略して「[SQL Server データベースを検出する](backup-azure-sql-database.md#discover-sql-server-databases)」に進んでください。 SQL データベースをホストする仮想マシンが Azure マーケットプレイスから作成されなかった場合は、このセクションの以降の手順を完了して拡張機能をインストールし、適切な権限を設定します。 **AzureBackupWindowsWorkload** 拡張機能に加えて、Azure Backup では、SQL データベースを保護するために SQL sysadmin 特権が必要になります。 仮想マシン上のデータベースの検出中に、Azure Backup は NT Service\AzureWLBackupPluginSvc というアカウントを作成します。 Azure Backup が SQL データベースを検出するには、NT Service\AzureWLBackupPluginSvc アカウントに SQL および SQL sysadmin 権限が必要です。 次の手順では、これらの権限を指定する方法について説明します。

権限を構成するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) で、使用している Recovery Services コンテナーを開き、SQL データベースを保護します。
2. コンテナーのダッシュボード メニューで、**[+ バックアップ]** をクリックして、**[バックアップの目標]** メニューを開きます。

   ![[+ バックアップ] をクリックして [バックアップの目標] メニューを開きます。](./media/backup-azure-sql-database/open-backup-menu.png)

3. **[バックアップの目標]** メニューで、**[ワークロードはどこで実行されていますか?]** ドロップダウン メニューの **[Azure]** を既定のままにします。

4. **[What do you want to backup]\(バックアップする対象\)** メニューで、ドロップダウン メニューを展開し、**[SQL Server in Azure VM]\(Azure VM 内の SQL Server\)** を選択します。

    ![[+ バックアップ] をクリックして [バックアップの目標] メニューを開きます。](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    **[バックアップの目標]** メニューには、**[Discover DBs in VMs]\(VM 内にある DB の検出\)** と **[バックアップの構成]** という 2 つの新しい手順が表示されます。 **[Discover DBs in VMs]\(VM 内にある DB の検出\)** では、Azure 仮想マシンの検索を開始します。

    ![新しい [バックアップの目標] の手順が表示される](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

5. **[検出の開始]** をクリックして、サブスクリプション内にある保護されていない仮想マシンを検索します。 サブスクリプション内にある保護されていない仮想マシンの数によっては、すべての仮想マシンの確認に少し時間がかかる場合があります。

    ![Backup pending](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    保護されていない仮想マシンが検出されると、一覧に表示されます。 保護されていない仮想マシンは、仮想マシン名とリソース グループごとに一覧に表示されます。 複数の仮想マシンが同じ名前になっている可能性もあります。 ただし、同じ名前の仮想マシンは、別のリソース グループに属します。 目的の仮想マシンが一覧に表示されていない場合は、仮想マシンがコンテナーに対して既に保護されているかどうかを確認します。

6. 仮想マシンの一覧から、バックアップしたい SQL データベースが含まれる VM を選択して、**[Discover DBs]\(DB の検出\)** をクリックします。 

    検出プロセスによって、仮想マシン上に **AzureBackupWindowsWorkload** 拡張機能がインストールされます。 拡張機能によって Azure Backup サービスは仮想マシンと通信できるので、SQL データベースをバックアップできます。 拡張機能がインストールされると、Azure Backup は Windows 仮想サービス アカウント **NT Service\AzureWLBackupPluginSvc** を仮想マシン上に作成します。 仮想サービス アカウントには、SQL sysadmin 権限が必要です。 仮想サービス アカウントのインストール プロセス中にエラー **UserErrorSQLNoSysadminMembership** が表示された場合は、「[SQL sysadmin 権限を修正する](backup-azure-sql-database.md#fixing-sql-sysadmin-permissions)」セクションを参照してください。

    [通知] 領域には、データベース検出の進行状況が表示されます。 仮想マシン上にあるデータベースの数に応じて、ジョブが完了するまで少し時間がかかる場合があります。 選択したデータベースが検出されたら、成功のメッセージが表示されます。

    ![成功のデプロイ通知メッセージ](./media/backup-azure-sql-database/notifications-db-discovered.png)

Recovery Services コンテナーとデータベースを関連付けたら、次の手順として[バックアップを構成](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database)します。

### <a name="fixing-sql-sysadmin-permissions"></a>SQL sysadmin 権限を修正する

インストール プロセス中にエラー **UserErrorSQLNoSysadminMembership** が表示された場合は、SQL sysadmin 権限を持つアカウントを使用して SQL Server Management Studio (SSMS) にサインインします。 特殊な権限が必要でない限り、Windows 認証が機能するはずです。

1. SQL Server で、**Security/Logins** フォルダーを開きます。

    ![SQL Server およびセキュリティとログインのフォルダーを開いて、アカウントを表示する](./media/backup-azure-sql-database/security-login-list.png)

2. [ログイン] フォルダーで、**[新しいログイン]** を右クリックして選択し、[ログイン - 新規作成] ダイアログで **[検索]** をクリックします。

    ![[Login - New]\(ログイン - 新規作成\) ダイアログで [検索] を開く](./media/backup-azure-sql-database/new-login-search.png)

3. Windows 仮想サービス アカウント **NT Service\AzureWLBackupPluginSvc** は、仮想マシンの登録と SQL 検出フェーズ中に既に作成済みなので、**[Enter the object name to select]\(選択するオブジェクト名を入力する\)** ダイアログの表示に従ってアカウント名を入力します。 **[名前の確認]** をクリックして名前解決を行います。 

    ![[名前の確認] ボタンをクリックして不明なサービス名を解決する](./media/backup-azure-sql-database/check-name.png)

4. **[OK]** をクリックして、[Select User or Group]\(ユーザーまたはグループの選択\) ダイアログを閉じます。

5. **[サーバー ロール]** ダイアログで、**sysadmin** ロールが選択されていることを確認します。 次に、**[OK]** をクリックして、**[Login - New]\(ログイン - 新規作成\)** を閉じます。

    ![sysadmin サーバー ロールが選択されていることを確認する](./media/backup-azure-sql-database/sysadmin-server-role.png)

    この時点で、必要な権限が揃っているはずです。

6. 権限エラーは修正済しましたが、まだ、Recovery Services コンテナーとデータベースを関連付ける必要があります。 Azure Portal の **[保護されたサーバー]** 一覧で、エラー状態にあるサーバーを右クリックし、**[Rediscover DBs] (DB の再検出)** を選択します。

    ![サーバーに適切な権限があることを確認する](./media/backup-azure-sql-database/check-erroneous-server.png)

    [通知] 領域には、データベース検出の進行状況が表示されます。 仮想マシン上にあるデータベースの数に応じて、ジョブが完了するまで少し時間がかかる場合があります。 選択したデータベースが検出されると、[通知] 領域に成功のメッセージが表示されます。

    ![成功のデプロイ通知メッセージ](./media/backup-azure-sql-database/notifications-db-discovered.png)

Recovery Services コンテナーとデータベースを関連付けたら、次の手順として[バックアップを構成](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database)します。

[!INCLUDE [Section explaining how to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>SQL Server データベースを検出する

Azure Backup では、バックアップの前提条件を基にしてデータベースを保護できるように、SQL Server インスタンス上にあるすべてのデータベースを検出できます。 次の手順を使用して、SQL データベースをホストしている仮想マシンを特定します。 仮想マシンを特定したら、Azure Backup では軽量な拡張機能をインストールして、SQL Server データベースを検出します。

1. [Azure Portal](https://portal.azure.com/) でサブスクリプションにサインインします。
2. 左側のメニューで、**[すべてのサービス]** を選択します。

    ![メイン メニューで、[すべてのサービス] オプションを選択する](./media/backup-azure-sql-database/click-all-services.png) <br/>

3. [すべてのサービス] ダイアログ ボックスに「*Recovery Services*」と入力します。 入力を開始すると、リソースのリストが絞り込まれます。 **[Recovery Services コンテナー]** が表示されたら、それを選択します。

    ![[すべてのサービス] ダイアログに「Recovery Services」と入力する](./media/backup-azure-sql-database/all-services.png) <br/>

    サブスクリプションに Recovery Services コンテナーの一覧が表示されます。 

4. Recovery Services コンテナーの一覧から、SQL データベースの保護に使用するコンテナーを選択します。

5. コンテナーのダッシュボード メニューで、**[+ バックアップ]** をクリックして、**[バックアップの目標]** メニューを開きます。

   ![[+ バックアップ] をクリックして [バックアップの目標] メニューを開きます。](./media/backup-azure-sql-database/open-backup-menu.png)

6. **[バックアップの目標]** メニューで、**[ワークロードはどこで実行されていますか?]** ドロップダウン メニューの **[Azure]** を既定のままにします。

7. **[What do you want to backup]\(バックアップする対象\)** メニューで、ドロップダウン メニューを展開し、**[SQL Server in Azure VM]\(Azure VM 内の SQL Server\)** を選択します。

    ![[+ バックアップ] をクリックして [バックアップの目標] メニューを開きます。](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    選択すると、**[バックアップの目標]** メニューに、[Discover DBs in VMs]\(VM 内にある DB の検出\) と [バックアップの構成] という 2 つの手順が表示されます。 

    ![新しい [バックアップの目標] の手順が表示される](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

8. **[検出の開始]** をクリックして、サブスクリプション内にある保護されていない仮想マシンを検索します。 サブスクリプション内にある保護されていない仮想マシンの数によっては、すべての仮想マシンの確認に少し時間がかかる場合があります。

    ![Backup pending](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    保護されていない仮想マシンが検出されると、一覧に表示されます。 複数の仮想マシンを同じ名前にすることができます。 ただし、同じ名前の複数の仮想マシンは、別のリソース グループに属します。 保護されていない仮想マシンは、仮想マシン名とリソース グループごとに一覧に表示されます。 目的の仮想マシンが一覧に表示されていない場合は、その仮想マシンがコンテナーに対して既に保護されているかどうかを確認します。

9. 仮想マシンの一覧で、保護する SQL データベースを含む仮想マシンのチェックボックスをオンにして、**[Discover DBs]\(DB の検出\)** をクリックします。

    Azure Backup では、仮想マシン上のすべての SQL データベースを検出します。 データベースの検出フェーズで発生した出来事に関する情報については、次のセクションの「[SQL データベースの検出時のバックエンド操作](backup-azure-sql-database.md#backend-operations-when-discovering-sql-databases)」を参照してください。 SQL データベースの検出を終えて、[バックアップ ジョブを構成する](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database)準備ができました。

### <a name="backend-operations-when-discovering-sql-databases"></a>SQL データベースの検出時のバックエンド操作

**[Discover DBs]\(DB の検出\)** ツールを使用すると、Azure Backup はバックグラウンドで次の操作を実行します。

- ワークロード バックアップ用の Recovery Services コンテナーを使って仮想マシンを登録する。 登録されている仮想マシン上のすべてのデータベースを、この Recovery Services コンテナーに対してのみバックアップできます。 

- 仮想マシン上に **AzureBackupWindowsWorkload** 拡張機能をインストールする。 SQL データベースのバックアップは、エージェントレス ソリューションです。つまり、仮想マシン上に拡張機能がインストールされた状態で、エージェントが SQL データベースにインストールされることはありません。

- 仮想マシン上にサービス アカウント **NT Service\AzureWLBackupPluginSvc** を作成する。 すべてのバックアップおよび復元操作に、サービス アカウントを使用します。 **NT Service\AzureWLBackupPluginSvc** には、SQL sysadmin 権限が必要です。 すべての SQL Marketplace 仮想マシンは SqlIaaSExtension がインストールされた状態で出荷されており、AzureBackupWindowsWorkload は SQLIaaSExtension を使用して自動的に必要な権限を取得します。 仮想マシン にSqlIaaSExtension がインストールされていない場合、DB の検出操作は失敗し、エラー メッセージ **UserErrorSQLNoSysAdminMembership** を取得します。 バックアップの sysadmin 権限を追加するには、[マーケットプレース以外の SQL VM に Azure Backup の権限を設定する](backup-azure-sql-database.md#set-permissions-for-non--marketplace-sql-vms)手順に従います。

    ![VM とデータベースを選択する](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup-for-sql-server-database"></a>SQL Server データベースのバックアップの構成

Azure Backup は、SQL Server データベースを保護してバックアップ ジョブを管理するための管理サービスを提供しています。 管理と監視の機能は、お使いの Recovery Services コンテナーに応じて異なります。 

> [!NOTE]
> SQL Server データベースをバックアップするために一度に保持できるバックアップ ソリューションは、1 つだけです。 この機能を使用する前に他の SQL バックアップをすべて無効にしてください。そうしないと、バックアップは妨害されて失敗します。 IaaS VM 向けの Azure Backup は、競合することなく SQL バックアップと共に有効化できます。 
>

お使いの SQL データベースの保護を構成するには、次の手順に従います。

1. SQL 仮想マシンと共に登録された Recovery Services コンテナーを開きます。

2. コンテナーのダッシュボード メニューで、**[+ バックアップ]** をクリックして、**[バックアップの目標]** メニューを開きます。

    ![[+ バックアップ] をクリックして [バックアップの目標] メニューを開きます。](./media/backup-azure-sql-database/open-backup-menu.png)

3. **[バックアップの目標]** メニューで、**[ワークロードはどこで実行されていますか?]** ドロップダウン メニューの **[Azure]** を既定のままにします。

4. **[What do you want to backup]\(バックアップする対象\)** メニューで、ドロップダウン メニューを展開し、**[SQL Server in Azure VM]\(Azure VM 内の SQL Server\)** を選択します。

    ![[+ バックアップ] をクリックして [バックアップの目標] メニューを開きます。](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    選択すると、**[バックアップの目標]** メニューに、[Discover DBs in VMs]\(VM 内にある DB の検出\) と [バックアップの構成] という 2 つの手順が表示されます。 この記事の順序に沿って手順を終えたので、既に保護されていない仮想マシンが検出され、このコンテナーは仮想マシンと共に登録されています。 これで、SQL データベースの保護を構成する準備ができました。

5. [バックアップの目標] メニューで、**[バックアップの構成]** をクリックします。

    ![新しい [バックアップの目標] の手順が表示される](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    Azure Backup サービスでは、SQL AlwaysOn 可用性グループだけでなく、スタンドアロン データベースを備えたすべての SQL インスタンスが表示されます。 SQL インスタンスのスタンドアロン データベースを表示するには、インスタンス名の横にある V 字型のマークをクリックします。 次の図は、スタンドアロン インスタンスと Always On 可用性グループの例を示しています。

    > [!NOTE]
    > SQL Always On 可用性グループの場合は、SQL のバックアップ設定を使用します。 ただし、SQL プラットフォームの制限のために、完全および差分バックアップはプライマリ ノードから実行する必要があります。 ログ バックアップは、バックアップの設定に基づいて行われる場合があります。 この制限のために、可用性グループにプライマリ ノードが常に登録されている必要があります。
    >

    ![SQL インスタンスのデータベースの一覧](./media/backup-azure-sql-database/discovered-databases.png)

    AlwaysOn 可用性グループの横にある V 字型をクリックして、データベースの一覧を表示します。

    ![AlwaysOn 可用性グループのデータベースの一覧](./media/backup-azure-sql-database/discovered-database-availability-group.png)

6. データベースの一覧から、保護したいデータベースをすべて選択して、**[OK]** をクリックします。

    ![複数のデータベースを選択して保護する](./media/backup-azure-sql-database/select-multiple-database-protection.png)

    一度に最大 50 個のデータベースを選択できます。 50 を上回る数のデータベースを保護したい場合は、過程を複数に分けます。 最初の 50 個のデータベースを保護した後に、この手順を繰り返して次のデータベース セットを保護します。
    > [!Note] 
    > バックアップの負荷を最適化するために、Azure Backup では大規模なバックアップ ジョブを複数のバッチに細分化します。 1 つのバックアップ ジョブにおけるデータベースの最大数は 50 です。
    >
    >

7. **[バックアップ]** メニューで、バックアップ ポリシーを作成または選択するには、**[バックアップ ポリシー]** を選択してメニューを開きます。

    ![バックアップ ポリシー オプションを選択する](./media/backup-azure-sql-database/select-backup-policy.png)

8. **[バックアップ ポリシーの選択]** ドロップダウン メニューで、バックアップ ポリシーを選択して、**[OK]** をクリックします。 独自のバックアップ ポリシーの作成については、「[バックアップ ポリシーを定義する](backup-azure-sql-database.md#define-a-backup-policy)」セクションを参照してください。

    ![ドロップダウン メニューからバックアップ ポリシーを選択する](./media/backup-azure-sql-database/select-backup-policy-steptwo.png)

    [バックアップ ポリシー] メニューで、**[バックアップ ポリシーの選択]** ドロップダウン メニューから次のポリシーを選択します。 
    - 既定の HourlyLogBackup ポリシー 
    - SQL 用に以前に作成された既存のバックアップ ポリシー
    - 回復ポイントの目標値 (RPO) とリテンション期間の範囲に基づいて[新しいポリシーを定義する](backup-azure-sql-database.md#define-a-backup-policy) 

    > [!Note]
    > Azure Backup では、三世代バックアップ スキームに基づく長期のリテンション期間をサポートし、コンプライアンス要件を満たしたうえで、バックエンドのストレージ使用量を最適化します。
    >

9. **バックアップ メニュー**でバックアップ ポリシーを選択したら、次に **[バックアップの有効化]** をクリックします。

    ![選択したバックアップ ポリシーを有効にする](./media/backup-azure-sql-database/enable-backup-button.png)

    ポータルの [通知] 領域で構成の進行状況を追跡できます。

    ![[通知] 領域を表示する](./media/backup-azure-sql-database/notifications-area.png)


### <a name="define-a-backup-policy"></a>バックアップ ポリシーを定義する

バックアップ ポリシーは、バックアップをいつ取得し、そのバックアップをいつまで保持するかのマトリクスを定義します。 Azure Backup を使用して、SQL データベースの 3 つの種類のバックアップのスケジュールを設定できます。

* 完全バックアップ - 完全なデータベース バックアップでは、データベース全体をバックアップします。 完全バックアップには、特定のデータベースあるいは一連のファイル グループまたはファイル内のすべてのデータと、そのデータを復旧するための十分なログが含まれます。 最多で、1 日に 1 回の完全バックアップをトリガーできます。 日次または週次で完全バックアップを取得することを選択できます。 
* 差分バックアップ - 差分バックアップは、直近の完全データ バックアップに基づきます。 差分バックアップでは、完全バックアップ以降に変更されたデータのみをキャプチャします。 最多で、1 日に 1 回の差分バックアップをトリガーできます。 完全バックアップと差分バックアップを同じ日に構成することはできません。
* トランザクション ログ バックアップ - ログ バックアップでは、ある特定の時点への復元を、指定した秒に至るまで可能にします。 最多で、15 分ごとにトランザクション ログ バックアップを構成できます。

ポリシーは、Recovery Services コンテナー レベルで作成されます。 複数のコンテナーがある場合、コンテナーでは同じバックアップ ポリシーを使用できますが、各コンテナーにバックアップ ポリシーを適用する必要があります。 バックアップ ポリシーの作成時は、日次での完全バックアップが既定値です。 差分バックアップを追加することは可能ですが、完全バックアップを週次での実行に切り替えた場合だけです。 次の手順では、Azure 仮想マシンで SQL server のバックアップ ポリシーを作成する方法を示します。

バックアップ ポリシーを作成するには

1. [バックアップ ポリシー] メニューで、**[バックアップ ポリシーの選択]** ドロップダウン メニューから、**[新規作成]** を選択します。

   ![新しいバックアップ ポリシーを作成する](./media/backup-azure-sql-database/create-new-backup-policy.png)

    [バックアップ ポリシー] メニューが切り替わり、新しい SQL Server バックアップ ポリシーに必要なフィールドが提示されます。

   ![バックアップ ポリシーの新しいフィールド](./media/backup-azure-sql-database/blank-new-policy.png)

2. **[ポリシー名]** に名前を入力します。 

3. 完全バックアップは必須です。 完全バックアップの既定値を確定するか、**[完全バックアップ]** をクリックしてポリシーを編集できます。

    ![バックアップ ポリシーの新しいフィールド](./media/backup-azure-sql-database/full-backup-policy.png)

    完全バックアップ ポリシー内で、頻度に [毎日] または [毎週] を選択します。 [毎日] を選択する場合は、バックアップ ジョブが開始されるときに、時刻とタイムゾーンを選択します。 日次の完全バックアップを選択する場合は、差分バックアップを作成できません。

   ![日次の設定](./media/backup-azure-sql-database/daily-interval.png)

    [毎週] を選択する場合は、バックアップ ジョブが開始されるときに、曜日、時刻、およびタイムゾーンを選択します。

   ![週次の設定](./media/backup-azure-sql-database/weekly-interval.png)

4. 既定では、すべての [保有期間の範囲] オプション (毎日、毎週、月単位、および年単位) が選択されています。 [保有期間の範囲] 制限のうち使用しない項目のチェックをオフにして、使用する間隔を設定します。 完全バックアップのポリシー メニューで、**[OK]** をクリックして設定を確定します。

   ![[保有期間の範囲] の間隔の設定](./media/backup-azure-sql-database/retention-range-interval.png)

    復旧ポイントは、そのリテンション期間の範囲に基づいて、リテンション期間に対してタグ付けされます。 たとえば、日次での完全バックアップを選択した場合、日ごとにトリガーされる完全バックアップは 1 回だけです。 週次のリテンション期間に応じて、特定の曜日のバックアップがタグ付けされ、週次でのリテンション期間の範囲に基づいて保持されます。 月次および年次のリテンション期間の範囲でも、同様の動作になります。

5. 差分バックアップ ポリシーを追加するには、**[差分バックアップ]** をクリックしてメニューを開きます。 

   ![差分ポリシーを開く](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

    差分バックアップのポリシー メニューで、**[有効]** を選択して頻度とリテンション期間の制御を開きます。 最大で、1 日に 1 回の差分バックアップをトリガーできます。
    > [!Important] 
    > また、差分バックアップは、最大 180 日間保持できます。 より長いリテンション期間が必要な場合、完全バックアップを使用する必要があり、差分バックアップは使用できません。
    >

   ![差分ポリシーを編集する](./media/backup-azure-sql-database/enable-differential-backup-policy.png)

    **[OK]** をクリックしてポリシーを保存し、[バックアップ ポリシー] のメイン メニューに戻ります。

6. トランザクション ログ バックアップ ポリシーを追加するには、**[ログ バックアップ]** をクリックしてメニューを開きます。 [ログ バックアップ] メニューで、**[有効]** を選択して、頻度とリテンション期間の制御を設定します。 ログ バックアップは、毎 15 分の頻度で実行でき、最大 35 日間保持することが可能です。 **[OK]** をクリックしてポリシーを保存し、[バックアップ ポリシー] のメイン メニューに戻ります。

   ![ログ バックアップ ポリシーを編集する](./media/backup-azure-sql-database/log-backup-policy-editor.png)

7. SQL バックアップの圧縮を有効にするかどうかを選択します。 圧縮は既定で、無効になっています。

    バックエンドでは、Azure Backup は SQL ネイティブ バックアップの圧縮を使用します。

8. バックアップ ポリシーに対するすべての編集を終えたら、**[OK]** をクリックします。 

   ![新しいポリシーを受け入れる](./media/backup-azure-sql-database/backup-policy-click-ok.png)

## <a name="restore-a-sql-database"></a>SQL データベースを復元する

Azure Backup は、トランザクション ログ バックアップを使用して、指定した秒に至るまで、特定の日付または時刻へ個々のデータベースを復元する機能を提供しています。 指定した復元時刻を基に、Azure Backup は完全、差分、または一連のログ バックアップから、データの復元に必要になる適切なバックアップを自動的に判断します。

または、特定の完全または差分バックアップを選択して、特定の時刻ではなく、特定の復旧ポイントまで復元できます。
 > [!Note]
 > "マスター" データベースの復元をトリガーする前に、スタートアップ オプション "-m AzureWorkloadBackup" を使用してシングル ユーザー モードで SQL Server を起動してください。 -m への引数はクライアントの名前です。このクライアントのみが、接続を開くことを許可されます。 すべてのシステム データベース (モデル、マスター、msdb) の場合は、復元をトリガーする前に SQL エージェント サービスを停止してください。 これらのいずれかの DB への接続を盗もうとする可能性があるアプリケーションはすべて閉じます。
>

データベースを復元するには、次の手順を実行します。

1. SQL 仮想マシンと共に登録された Recovery Services コンテナーを開きます。

2. コンテナー ダッシュボードで、**[使用量]** バックアップ項目を選択して [バックアップ項目] メニューを開きます。

    ![[+ バックアップ] をクリックして [バックアップの目標] メニューを開きます。](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png)が必要です。

3. **[バックアップ項目]** メニューで、バックアップ管理の種類 **[SQL in Azure VM]\(Azure VM 内の SQL\)** を選択します。 

    ![[+ バックアップ] をクリックして [バックアップの目標] メニューを開きます。](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    [バックアップ項目] 一覧が調整されて、SQL データベースの一覧が表示されるようになります。 

4. SQL データベースの一覧から、復元する目的のデータベースを選択します。

    ![一覧から Azure VM の SQL を選択する](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    データベースを選択すると、そのデータベースのメニューが開きます。 このメニューは、次のようなデータベースのバックアップの詳細を提示します。

    * 最古および最新の復元ポイント
    * 直近 24 時間のログ バックアップ ステータス (トランザクション ログ バックアップ用に構成されている場合、完全および一括ログ復旧モデルのデータベースに対して)

5. 選択したデータベース メニューで、**[DB の復元]** をクリックして、[復元] メニューを開きます。

    ![データベースの復元を選択する](./media/backup-azure-sql-database/restore-db-button.png)

    **[復元]** メニューが開き、**[復元の構成]** メニューが開きます。 **[復元の構成]** メニューは、復元を構成するための最初の手順です。 このメニューで、データを復元する場所を選択します。 オプションは次のとおりです。
    * 別の場所 - 元のソース データベースを引き続き保持したうえで、別の場所にデータベースを復元したい場合は、このオプションを使用します。
    * DB の上書き - 元のソースと同じ SQL Server インスタンスにデータを復元します。 このオプションでは、元のデータベースが上書きされます。

    > [!Important]
    > 選択したデータベースが Always On 可用性グループに属している場合、SQL はデータベースの上書きを許可しません。 この場合は、**[別の場所]** オプションのみが有効です。
    >

    ![[構成] をクリックして [復元の構成] メニューを開く](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>別の場所に復元する

この手順では、別の場所にデータを復元する方法を示します。 復元時にデータベースを上書きしたい場合は、「[データベースを復元して上書きする](backup-azure-sql-database.md#restore-and-overwrite-the-database)」セクションに進んでください。 この手順では、Recovery Services コンテナーを開いており、[復元の構成] メニューが表示されていることを前提にしています。 この状態になっていない場合は、「[SQL データベースを復元する](backup-azure-sql-database.md#restore-a-sql-database)」セクションから開始してください。

> [!NOTE]
> このデータベースを同じ Azure リージョン内の SQL Server に復元することができ、宛先サーバーが Recovery Services コンテナーに登録されている必要があります。 
>

**[サーバー]** ドロップダウン メニューには、Recovery Services コンテナーと共に登録された SQL Server のみが表示されます。 目的のサーバーが **[サーバー]** 一覧にない場合は、「[SQL Server データベースを検出する](backup-azure-sql-database.md#discover-sql-server-databases)」セクションを参照してサーバーを見つけます。 データベースの検出プロセスの間に、Recovery Services コンテナーに新しいサーバーが登録されます。

1. **[復元の構成]** メニューで、次の手順を実行します。

    * **[別の場所]** を選択します。
    * **[サーバー]** で、データベースの復元先となる SQL サーバーを選択します。
    * **[インスタンス]** ドロップダウン メニューで、SQL インスタンスを選択します。
    * **[復元される DB 名]** ダイアログで、ターゲット データベースの名前を指定します。
    * 必要に応じて、**[Overwrite if the DB with the same name already exists on selected SQL]\(同じ名前の DB が選択された SQL に既に存在する場合、上書きする\)** を選択します。
    * **[OK]** をクリックして、保存先の構成を完了し、復元ポイントの選択に進みます。

    ![[復元の構成] メニューで別の場所、インスタンス、および名前を選択する](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. **[復元ポイントの選択]** メニューで、[Logs (Point in Time)]\(ログ (特定の時点)\) または [Full & Differential]\(完全および差分\) 復元ポイントのどちらかを選択します。 指定した特定の時点のログまで復元したい場合は、引き続きこの手順を実行してください。 完全または差分の復元ポイントを復元したい場合は、この手順を省略して手順 3 に進んでください。

    ![[復元ポイントの選択] メニュー](./media/backup-azure-sql-database/recovery-point-menu.png)

    特定の時点への復元は、完全および一括ログの復旧モデルを使ったデータベースのログ バックアップのみで利用可能です。 特定の時点に復元するには、次の手順を実行します。

    1. **[Logs (Point in Time)]\(ログ (特定の時点)\)** を復元オプションとして選択します。

        ![復元ポイントの種類を選択する](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. **[復元の日付/時刻]** で、カレンダー アイコンをクリックして、カレンダーを開きます。 太字の日付に復旧ポイントが含まれ、現在の日付が強調表示されています。 復旧ポイントがあるカレンダーの日付を選択します。 復旧ポイントがない日付は選択できません。

        ![カレンダーを開く](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        日付を選択すると、タイムライン グラフに連続した範囲の使用可能な復旧ポイントが表示されます。

    3. タイムライン グラフまたは [時刻] ダイアログを使用して、復旧ポイントの特定の時刻を指定し、**[OK]** をクリックして、復元ポイントの手順を完了します。
    
       ![カレンダーを開く](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        **[復元ポイントの選択]** メニューが閉じられ、**[詳細な構成]** メニューが開きます。

       ![[詳細な構成] メニュー](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. **[詳細な構成]** メニューから、次の手順を実行します。

        * 復元後のデータベースを非運用状態のままにしておくために、**[Restore with NORECOVERY]\(NORECOVERY による復元\)** メニューで、**[有効]** を選択します。
        * 保存先サーバー上で復元場所を変更したい場合は、**ターゲット** 列に新しいパスを指定します。
        * **[OK]** をクリックして設定を承認し、**[詳細な構成]** を閉じます。

    5. **[復元]** メニューで、**[復元]** をクリックして復元ジョブを開始します。 [通知] 領域で、進行状況を追跡できます。 また、データベースの [復元] ジョブで進行状況を追跡することもできます。

       ![[詳細な構成] メニュー](./media/backup-azure-sql-database/restore-job-notification.png)

3. **[復元ポイントの選択]** メニューで、復旧ポイントを選択します。 [Logs (Point in Time)]\(ログ (特定の時点)\) または [Full & Differential]\(完全および差分\) のどちらかを選択できます。 特定の時点のログを復元したい場合は、手順 2 に戻ります。 この手順では、完全または差分の特定の復元ポイントを復元します。 このオプションでは、直近 30 日間のすべての完全および差分の復旧ポイントを確認できます。 30 日間よりも古い復旧ポイントを確認したい場合は、**[フィルター]** をクリックして **[復元ポイントのフィルター]** メニューを開きます。 [差分] 復旧ポイントを選択した場合、Azure Backup では最初に適切な [完全] 復旧ポイントを復元してから、選択した [差分] 復旧ポイントを適用します。

    ![[復元ポイントの選択] メニュー](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. **[復元ポイントの選択]** メニューで、**[Full & Differential]\(完全および差分\)** を選択します。

       ![[復元ポイントの選択] メニュー](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        使用可能な復旧ポイントの一覧が表示されます。

    2. 復旧ポイントの一覧から、復旧ポイントを選択し、**[OK]** をクリックして復元ポイントの手順を完了します。 

        ![[完全] 復旧ポイントを選択する](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        **[復元ポイント]** メニューが閉じられ、**[詳細な構成]** メニューが開きます。

        ![[詳細な構成] メニュー](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. **[詳細な構成]** メニューから、次の手順を実行します。

        * 復元後のデータベースを非運用状態のままにしておくために、**[Restore with NORECOVERY]\(NORECOVERY による復元\)** メニューで、**[有効]** を選択します。 **[Restore with NORECOVERY]\(NORECOVERY による復元\)** は既定で無効になっています。
        * 保存先サーバー上で復元場所を変更したい場合は、**ターゲット** 列に新しいパスを指定します。
        * **[OK]** をクリックして設定を承認し、**[詳細な構成]** を閉じます。

    4. **[復元]** メニューで、**[復元]** をクリックして復元ジョブを開始します。 [通知] 領域で、進行状況を追跡できます。 また、データベースの [復元] ジョブで進行状況を追跡することもできます。

       ![[詳細な構成] メニュー](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-and-overwrite-the-database"></a>データベースを復元して上書きする

この手順では、データを復元してデータベースを上書きする方法を示します。 別の場所に復元したい場合は、「[別の場所に復元する](backup-azure-sql-database.md#restore-to-an-alternate-location)」セクションに進んでください。 この手順では、Recovery Services コンテナーを開いており、**[復元の構成]** メニューが表示されていることを前提にしています (次の図を参照してください)。 この状態になっていない場合は、「[SQL データベースを復元する](backup-azure-sql-database.md#restore-a-sql-database)」セクションから開始してください。

![[構成] をクリックして [復元の構成] メニューを開く](./media/backup-azure-sql-database/restore-overwrite-db.png)

**[サーバー]** ドロップダウン メニューには、Recovery Services コンテナーと共に登録された SQL Server のみが表示されます。 目的のサーバーが **[サーバー]** 一覧にない場合は、「[SQL Server データベースを検出する](backup-azure-sql-database.md#discover-sql-server-databases)」セクションを参照してサーバーを見つけます。 データベースの検出プロセスの間に、Recovery Services コンテナーに新しいサーバーが登録されます。

1. **[復元の構成]** メニューで、**[DB の上書き]** を選択し、**[OK]** をクリックして保存先の構成を完了します。 

   ![[DB の上書き] をクリックする](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

    **[サーバー]**、**[インスタンス]**、および **[復元される DB 名]** ダイアログは必要ありません。

2. **[復元ポイントの選択]** メニューで、[Logs (Point in Time)]\(ログ (特定の時点)\) または [Full & Differential]\(完全および差分\) 復元ポイントのどちらかを選択します。 特定の時点のログを復元したい場合は、引き続きこの手順を実行してください。 完全および差分の復元ポイントを復元したい場合は、この手順を省略して手順 3 に進んでください。

    ![[復元ポイントの選択] メニュー](./media/backup-azure-sql-database/recovery-point-menu.png)

    特定の時点への復元は、完全および一括ログの復旧モデルを使ったデータベースのログ バックアップのみで利用可能です。 指定した秒への特定の時点の復元を選択するには、次の手順を実行します。

    1. **[Logs (Point in Time)]\(ログ (特定の時点)\)** を復元オプションとして選択します。

        ![復元ポイントの種類を選択する](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. **[復元の日付/時刻]** で、カレンダー アイコンをクリックして、カレンダーを開きます。 太字の日付に復旧ポイントが含まれ、現在の日付が強調表示されています。 復旧ポイントがあるカレンダーの日付を選択します。 復旧ポイントがない日付は選択できません。

        ![カレンダーを開く](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        日付を選択すると、タイムライン グラフに使用可能な復旧ポイントが表示されます。

    3. タイムライン グラフまたは [時刻] ダイアログを使用して、復旧ポイントの特定の時刻を指定し、**[OK]** をクリックして、復元ポイントの手順を完了します。
    
       ![カレンダーを開く](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        **[復元ポイントの選択]** メニューが閉じられ、**[詳細な構成]** メニューが開きます。

       ![[詳細な構成] メニュー](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. **[詳細な構成]** メニューから、次の手順を実行します。

        * 復元後のデータベースを非運用状態のままにしておくために、**[Restore with NORECOVERY]\(NORECOVERY による復元\)** メニューで、**[有効]** を選択します。
        * 保存先サーバー上で復元場所を変更したい場合は、**ターゲット** 列に新しいパスを指定します。
        * **[OK]** をクリックして設定を承認し、**[詳細な構成]** を閉じます。

    5. **[復元]** メニューで、**[復元]** をクリックして復元ジョブを開始します。 [通知] 領域で、進行状況を追跡できます。 また、データベースの [復元] ジョブで進行状況を追跡することもできます。

       ![[詳細な構成] メニュー](./media/backup-azure-sql-database/restore-job-notification.png)

3. **[復元ポイントの選択]** メニューで、復旧ポイントを選択します。 [Logs (Point in Time)]\(ログ (特定の時点)\) または [Full & Differential]\(完全および差分\) のどちらかを選択できます。 特定の時点のログを復元したい場合は、手順 2 に戻ります。 この手順では、完全または差分の特定の復元ポイントを復元します。 このオプションでは、直近 30 日間のすべての完全および差分の復旧ポイントを確認できます。 30 日間よりも古い復旧ポイントを確認したい場合は、**[フィルター]** をクリックして **[復元ポイントのフィルター]** メニューを開きます。 [差分] 復旧ポイントを選択した場合、Azure Backup では最初に適切な [完全] 復旧ポイントを復元してから、選択した [差分] 復旧ポイントを適用します。

    ![[復元ポイントの選択] メニュー](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. **[復元ポイントの選択]** メニューで、**[Full & Differential]\(完全および差分\)** を選択します。

       ![[復元ポイントの選択] メニュー](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        使用可能な復旧ポイントの一覧が表示されます。

    2. 復旧ポイントの一覧から、復旧ポイントを選択し、**[OK]** をクリックして復元ポイントの手順を完了します。 

        ![[完全] 復旧ポイントを選択する](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        **[復元ポイント]** メニューが閉じられ、**[詳細な構成]** メニューが開きます。

        ![[詳細な構成] メニュー](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. **[詳細な構成]** メニューから、次の手順を実行します。

        * 復元後のデータベースを非運用状態のままにしておくために、**[Restore with NORECOVERY]\(NORECOVERY による復元\)** メニューで、**[有効]** を選択します。 **[Restore with NORECOVERY]\(NORECOVERY による復元\)** は既定で無効になっています。
        * 保存先サーバー上で復元場所を変更したい場合は、**ターゲット** 列に新しいパスを指定します。
        * **[OK]** をクリックして設定を承認し、**[詳細な構成]** を閉じます。

    4. **[復元]** メニューで、**[復元]** をクリックして復元ジョブを開始します。 [通知] 領域で、進行状況を追跡できます。 また、データベースの [復元] ジョブで進行状況を追跡することもできます。

       ![[詳細な構成] メニュー](./media/backup-azure-sql-database/restore-job-notification.png)


## <a name="manage-azure-backup-operations-for-sql-on-azure-vms"></a>Azure VM 上で SQL に対する Azure Backup 操作を管理する

このセクションでは、Azure 仮想マシン上で SQL に利用できる、さまざまな Azure Backup 管理操作に関する情報を提供します。 次のような高度な操作があります。

* ジョブの監視
* バックアップ アラート
* SQL Database での保護の停止
* SQL Database での保護の再開
* アドホック バックアップ ジョブのトリガー
* SQL Server の登録解除

### <a name="monitor-jobs"></a>ジョブの監視
エンタープライズ クラスのソリューションである Azure Backup は、高度なバックアップ アラートやすべてのエラーの通知を提供します (下の「バックアップ アラート」セクションを参照)。 引き続き特定のジョブを監視したい場合は、要件に基づいて次のいずれかのオプションを使用できます。

#### <a name="use-azure-portal-for-all-adhoc-operations"></a>[Use Azure portal for all adhoc operations] (すべてのアドホック操作に Azure Portal を使用する)
Azure Backup は、バックアップ ジョブのポータルに、手動でトリガーされたジョブまたはアドホック ジョブをすべて表示します。 ポータルで使用可能なジョブには、バックアップの構成操作、手動でトリガーされたバックアップ操作、復元操作、データベースの登録および検出操作、バックアップの停止操作がすべて含まれます。 
![[詳細な構成] メニュー](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> 完全、差分、およびログ バックアップを含むスケジュールされたバックアップ ジョブはすべてポータルには表示されず、下で説明されているように、SQL Server Management Studio を使用して監視できます。
>

#### <a name="use-sql-server-management-studio-for-backup-jobs"></a>[Use SQL Server Management Studio for backup jobs] (バックアップ ジョブに SQL Server Management Studio を使用する)
Azure Backup では、すべてのバックアップ操作に SQL ネイティブ API を使用します。 ネイティブ API では、msdb データベース内の [SQL アップセット テーブル](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017)からすべてのジョブ情報をフェッチできます。

次の例は、**DB1** という名前のデータベースのすべてのバックアップ ジョブをフェッチするためのクエリです。 より高度な監視を行うには、このクエリをカスタマイズします。
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

### <a name="backup-alerts"></a>バックアップ アラート

15 分ごとに行われるログ バックアップを使って、バックアップ ジョブを適宜監視することは、面倒に感じられる場合があります。 Azure Backup は、任意のバックアップ エラーによってトリガーされる電子メール アラートを提供することで、この面倒に感じられがちな状況に対応しました。 アラートは、エラー コードごとにデータベース レベルで統合されます。 たとえば、データベースで複数のバックアップ エラーが発生した場合、各エラーのアラートを受信するのではなく、最初のエラーに対して電子メールを受信します。 その後、Azure Portal にサインインして、そのデータベースの後続のエラーを監視できます。 

バックアップ アラートを監視するには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com) で、Azure サブスクリプションにサインインします。

2. SQL 仮想マシンと共に登録された Recovery Services コンテナーを開きます。

3. Recovery Services コンテナー メニューで、**[Alerts and Events]\(アラートとイベント\)** を選択します。 

   ![[詳細な構成] メニュー](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. **[Alerts and Events]\(アラートとイベント\)** メニューで、**[バックアップ アラート]** を選択してアラートの一覧を表示します。

   ![[詳細な構成] メニュー](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

### <a name="stop-protection-on-a-sql-server-database"></a>SQL Server データベースでの保護の停止

SQL Server データベースの保護を停止した場合、Azure Backup では、復旧ポイントを維持したいかどうかを尋ねます。 SQL データベースの保護を停止するには、次の 2 つの方法があります。

* 今後のバックアップ ジョブすべてを停止し、すべての復旧ポイントを削除する
* 今後のバックアップ ジョブすべてを停止するが、復旧ポイントはそのままにする 

復旧ポイントをそのままにしておくと、SQL の復旧ポイントでは、SQL で保護されたインスタンス価格の請求に加えて、使用中のストレージ容量も持ち越されるので、コストの持ち越しになります。 SQL に対する Azure Backup の価格設定の詳細情報については、[Azure Backup の価格のページ](https://azure.microsoft.com/pricing/details/backup/)を参照してください。 データベースの保護を停止するには、次の手順を実行します。

1. SQL 仮想マシンと共に登録された Recovery Services コンテナーを開きます。

2. コンテナー ダッシュボードで、**[使用量]** バックアップ項目を選択して [バックアップ項目] メニューを開きます。

    ![[+ バックアップ] をクリックして [バックアップの目標] メニューを開きます。](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png)が必要です。

3. **[バックアップ項目]** メニューで、バックアップ管理の種類 **[SQL in Azure VM]\(Azure VM 内の SQL\)** を選択します。 

    ![[+ バックアップ] をクリックして [バックアップの目標] メニューを開きます。](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    [バックアップ項目] 一覧が調整されて、SQL データベースの一覧が表示されるようになります。 

4. SQL データベースの一覧から、保護を停止したいデータベースを選択します。

    ![一覧から Azure VM の SQL を選択する](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    データベースを選択すると、そのデータベースのメニューが開きます。 

5. 選択されたデータベース メニューで、**[バックアップの停止]** をクリックして、データベースの保護を停止します。

    ![データベースの復元を選択する](./media/backup-azure-sql-database/stop-db-button.png)

    **[バックアップの停止]** メニューが開きます。

6. **[バックアップの停止]** メニューで、[バックアップ データの保持] または [バックアップ データの削除] を選択します。 必要に応じて、保護を停止する理由とコメントを指定できます。

    ![データベースの復元を選択する](./media/backup-azure-sql-database/stop-backup-button.png)

7. **[バックアップの停止]** をクリックして、データベースでの保護を停止します。 

### <a name="resume-protection-for-a-sql-database"></a>SQL Database での保護の再開

SQL データベースの保護を停止するときに **[バックアップデータの保持]** オプションを選択した場合は、保護を再開することができます。 バックアップ データが保持されていなかった場合、保護は再開できません。 

1. SQL データベースの保護を再開するには、バックアップ項目を開き、**[バックアップの再開]** をクリックします。

    ![データベースの保護を再開する](./media/backup-azure-sql-database/resume-backup-button.png)

   [バックアップ ポリシー] メニューが開きます。

2. **[バックアップ ポリシー]** メニューから、ポリシーを選択して、**[保存]** をクリックします。

### <a name="trigger-an-adhoc-backup"></a>アドホック バックアップ ジョブをトリガーする

必要なときに、いつでもアドホック バックアップをトリガーできます。 アドホック バックアップには次の 4 種類があります。 各種類の詳細については、[SQL バックアップの種類](https://docs.microsoft.com/sql/relational-databases/backup-restore/backup-overview-sql-server?view=sql-server-2017#types-of-backups)に関する記事を参照してください。

* 完全バックアップ
* コピーのみの完全バックアップ
* 差分バックアップ
* ログ バックアップ

### <a name="unregister-a-sql-server"></a>SQL Server を登録解除する

保護を削除した後の、まだコンテナーを削除していないときに SQL Server を登録解除するには、次の手順を実行します。

1. SQL 仮想マシンと共に登録された Recovery Services コンテナーを開きます。

2. コンテナー メニューの **[管理]** セクションで、**[バックアップ インフラストラクチャ]** をクリックします。  

   ![データベースの保護を再開する](./media/backup-azure-sql-database/backup-infrastructure-button.png)

3. **[管理サーバー]** セクションで、**[保護されたサーバー]** をクリックします。

   ![データベースの保護を再開する](./media/backup-azure-sql-database/protected-servers.png)

    [保護されたサーバー] メニューが開きます。 

4. **[保護されたサーバー]** メニューで、登録解除するサーバーを選択します。 コンテナーを削除したい場合は、すべてのサーバーの登録を解除する必要があります。

5. [保護されたサーバー] メニューで、保護されているサーバーを右クリックして、**[削除]** を選択します。 

   ![データベースの保護を再開する](./media/backup-azure-sql-database/delete-protected-server.png)

## <a name="sql-database-backup-faq"></a>SQL データベースのバックアップに関する FAQ

次のセクションでは、SQL データベースのバックアップに関する追加情報を提供します。

### <a name="can-i-throttle-the-speed-of-the-sql-backup-policy-so-it-minimizes-impact-on-the-sql-server"></a>SQL サーバーへの影響が最小限に抑えられるように、SQL バックアップ ポリシーの速度を調整できますか

はい。バックアップ ポリシーが実行される速度を調整できます。 設定を変更するには:

1. SQL Server で、`C:\Program Files\Azure Workload Backup\bin` フォルダー内の **TaskThrottlerSettings.json** を開きます。

2. **TaskThrottlerSettings.json** ファイルで、**DefaultBackupTasksThreshold** を小さな値 (たとえば、5) に変更します。

3. 変更を保存してファイルを閉じます。

4. SQL Server で、タスク マネージャーを開き、**Azure Backup ワークロード コーディネーター サービス**を再開します。

### <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>セカンダリ レプリカから完全バックアップを実行できますか

いいえ。この機能はサポートされていません。

### <a name="do-successful-backup-jobs-create-alerts"></a>成功したバックアップ ジョブでアラートが作成されますか

いいえ。 成功したバックアップ ジョブではアラートは生成されません。 アラートは、失敗したバックアップ ジョブに対してのみ送信されます。

### <a name="are-scheduled-backup-job-details-shown-in-the-jobs-menu"></a>スケジュールされたバックアップ ジョブの詳細は [ジョブ] メニューに表示されますか

いいえ。 [ジョブ] メニューにはアドホック ジョブの詳細が表示されますが、スケジュールされたバックアップ ジョブは表示されません。 スケジュールされたバックアップ ジョブのいずれかが失敗した場合は、失敗したジョブのアラートですべての詳細を見つけることができます。 スケジュールされたバックアップ ジョブとアドホック バックアップ ジョブをすべて監視する場合は、[SQL Server Management Studio を使用します](backup-azure-sql-database.md#use-sql-server-management-studio-for-backup-jobs)。

### <a name="if-i-select-a-sql-server-will-future-databases-automatically-be-added"></a>SQL サーバーを選択すると、将来のデータベースは自動的に追加されますか

いいえ。 SQL サーバーの保護を構成する場合、サーバー レベルのチェックボックスを選択すると、すべてのデータベースが追加されます。 ただし、保護を構成した後に SQL サーバーにデータベースを追加した場合、新しいデータベースを保護するにはそれらを手動で追加する必要があります。 これらのデータベースは、構成された保護に自動的には含まれません。

### <a name="if-i-change-the-recovery-model-how-do-i-restart-protection"></a>復旧モデルを変更した場合、保護を再開するにはどうすればよいですか

復旧モデルを変更した場合は、完全バックアップをトリガーすると、ログ バックアップが期待どおりに開始されます。

## <a name="next-steps"></a>次の手順

Azure Backup の詳細については、暗号化された仮想マシンのバックアップに関する PowerShell サンプルをご覧ください。

> [!div class="nextstepaction"]
> [暗号化された VM のバックアップ](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
