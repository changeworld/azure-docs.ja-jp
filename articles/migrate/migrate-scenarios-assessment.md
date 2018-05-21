---
title: DMA と Azure Migrate を使用して、Azure への移行についてオンプレミスのワークロードを評価する | Microsoft Docs
description: Data Migration Assistant (DMA) と Azure Migrate サービスを使用して、オンプレミス マシンの移行のために Azure を準備する方法について説明します。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: fb102cc43c6e1d17afaa78a2833ae447600a96af
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
---
# <a name="scenario-1-assess-on-premises-workloads-for-migration-to-azure"></a>シナリオ 1: Azure への移行についてオンプレミスのワークロードを評価する

Azure への移行を検討する際、Contoso 社は技術的および財務的な評価を実行して、オンプレミスのワークロードがクラウドへの移行に適しているかどうかを確認する必要があります。 特に、移行に関してマシンおよびデータベースの互換性を評価し、Azure でリソースを実行するための容量とコストを見積もる必要があります。

関連するテクノロジに触れて理解を深めるために、小さなオンプレミス旅行アプリを評価して移行します。 これは 2 層のアプリです。1 つの VM で Web アプリが実行され、2 つ目の VM に SQL Server データベースがあります。 アプリケーションは VMware でデプロイされ、環境は vCenter Server で管理されます。 評価は、Data Migration Assistant (DMA) と Azure Migrate サービスを使用して実行します。 この説明用の旅行アプリを使用してこのシナリオを試したい場合は、[github](https://github.com/Microsoft/SmartHotel360) からダウンロードできます。

**テクノロジ** | **説明** | **コスト**
--- | --- | ---
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | DMA は、Azure のデータベース機能に影響を及ぼす可能性がある互換性の問題を評価して検出します。 さらに、SQL Server ソースとターゲットの間の機能パリティを評価し、ターゲット環境のパフォーマンスと信頼性の向上を推奨します。 | これは無料でダウンロードできるツールです。 
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-overview) | このサービスは、Azure への移行についてオンプレミスのマシンを評価するのに役立ちます。 マシンの移行適合性を評価し、Azure で実行する場合のサイズ設定とコストの見積もりを提供します。 現時点では、Azure Migrate サービスを使用して、Azure への移行についてオンプレミスの VMware VM を評価できます。 | 現時点では (2018 年 4 月)、このサービスは無料で使用できます。
[サービス マップ](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | Azure Migrate では、移行したいマシン間の依存関係を示すために Service Map が使用されます。 |  Service Map は Azure Log Analytics に含まれています。 これは現在、180 日間無料で使用できます。 

このシナリオでは、DMA をダウンロードして実行し、旅行アプリ用のオンプレミス SQL Server データベースを評価します。 Azure に移行する前に、Azure Migrate と依存関係マッピングを使用してアプリ VM を評価します。

> [!NOTE]
> このシナリオでは、データベースの評価ターゲットは、"Azure VM 上の SQL Server" です。 ただし、次のシナリオの記事では、Azure SQL マネージド インスタンスへの移行を実行します。 DMA では現在、Azure SQL マネージド インスタンス ターゲットへの評価がサポートされていないため、このアプローチを使用します。

## <a name="architecture"></a>アーキテクチャ


![移行評価のアーキテクチャ](./media/migrate-scenarios-assessment/migration-assessment-architecture.png)

このシナリオでは:
- Contoso は一般的なエンタープライズ組織を表す架空の名前です。 Contoso は、2 階層のオンプレミス旅行アプリを評価し、移行したいと考えています。
- Contoso にオンプレミスのデータセンター (**contoso-datacenter**) があり、そこにオンプレミスのドメイン コントローラー (**contosodc1**) が含まれています。
- 社内の旅行アプリは、2 つの VM (**WEBVM** と **SQLVM**) に階層化されており、VMware ESXi ホスト (**contosohost1.contoso.com**) 上に配置されています。
- VMware 環境は、VM で実行中の vCenter Server (**vcenter.contoso.com**) によって管理されます。




## <a name="prerequisites"></a>前提条件

このシナリオをデプロイするために必要なものを次に示します。

- バージョン 5.5、6.0、または 6.5 を実行しているオンプレミスの vCenter サーバー。
- vCenter サーバーの読み取り専用アカウント、またはそれを作成するためのアクセス許可。 
- .OVA テンプレートを使用して vCenter サーバー上に VM を作成するためのアクセス許可。
- バージョン 5.0 以上を実行している少なくとも 1 つの ESXi ホスト。
- 少なくとも 2 つのオンプレミス VMware VM (1 つは SQL Server データベースを実行)。
- Azure Migrate エージェントを各 VM にインストールするアクセス許可が必要です。
- VM には、インターネットへの直接接続が必要です。
        - [必要な URL](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-pre-requisites) へのインターネット アクセスを制限できます。
        - インターネットに接続されていないマシンの場合、[OMS ゲートウェイ](../log-analytics/log-analytics-oms-gateway.md)をダウンロードしてインストールする必要があります。
- SQL Server インスタンスを実行している VM の FQDN (データベース評価用)。
- SQL Server VM 上で実行されている Windows ファイアウォールでは、DMA が接続できるよう、TCP ポート 1433 (既定値) で外部接続が許可される必要があります。


## <a name="scenario-overview"></a>シナリオの概要

これから実行する内容を次に示します。


> [!div class="checklist"]
> * **手順 1: Azure を準備する**。 必要なのは Azure サブスクリプションのみです。
> * **手順 2: DMA をダウンロードしてインストールする**。オンプレミスの SQL Server データベースを評価するために DMA を準備します。
> * **手順 3: データベースを評価する**。データベース評価を実行して分析します。
> * **手順 4: Azure Migrate による VM 評価の準備をする**。オンプレミスのアカウントを設定し、VMware 設定を微調整します。
> * **手順 5: オンプレミスの VM を検出する**。Azure Migrate コレクター VM を作成します。 次に、コレクターを実行して、評価する VM を検出します。
> * **手順 6: 依存関係の分析の準備をする**。VM 間の依存関係マッピングを確認できるよう、Azure Migrate エージェントを VM にインストールします。
> * **手順 7: VM を評価する**。依存関係を確認して VM をグループ化し、評価を実行します。 評価の準備ができたら、移行に向けてそれを分析します。


## <a name="step-1-prepare-azure"></a>手順 1: Azure を準備する

Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)を作成してください。

- 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。
- 既存のサブスクリプションを使用しており、管理者でない場合は、管理者と協力する必要があります。そして、このシナリオで使用するサブスクリプションまたはリソース グループの所有者アクセス許可か共同作成者アクセス許可を割り当ててもらいます。


## <a name="step-2-download-and-install-the-dma"></a>手順 2: DMA をダウンロードしてインストールする

1. [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=53595)から DMA をダウンロードします。
    - Assistant は、SQL インスタンスに接続可能なマシンであればインストールできます。 SQL Server マシンでこれを実行する必要はありません。
    - SQL Server ホスト マシンで実行しないようにしてください。
2. ダウンロードしたセットアップ ファイル (DownloadMigrationAssistant.msi) をダブルクリックして、インストールを開始します。
3. **[完了]** ページで、**[Launch Microsoft Data Migration Assistant]\(Microsoft Data Migration Assistant を起動する\)** が選択されていることを確認して、**[完了]** をクリックします。

## <a name="step-3-run-and-analyze-the-database-assessment"></a>手順 3: データベース評価を実行して分析する

評価を実行し、指定のターゲットに対してソース SQL Server インスタンスを分析します。

1. **[新規]** で **[評価]** を選択し、評価にプロジェクト名を付けます。
2. **[Source server type]\(ソース サーバーの種類\)** で、**[SQL Server]** を選択します。 **[Target server type]\(ターゲット サーバーの種類\)** で、**[Azure Virtual Machines 上の SQL Server]** を選択します。

    ![ソースの選択](./media/migrate-scenarios-assessment/dma-assessment-1.png)

    > [!NOTE]
      現時点の DMA では、SQL マネージド インスタンスへの移行の評価はサポートされていません。 この問題を回避するために、Azure VM 上の SQL Server を想定されるターゲットとして評価に使用します。

1.  **[ターゲット バージョンを選択する]** で、Azure で実行したい SQL Server のターゲット バージョンと、評価で検出したい内容を指定します。
    - **[Compatibility Issues]\(互換性の問題\)** では、移行を中断させる可能性がある変更や、移行前に微調整が必要な変更についてわかります。 さらに、現在使用している非推奨の機能についてもわかります。 問題は、互換性レベルごとに整理されます。 
    - **[新機能のお勧め]** では、移行後にデータベースで使用できる、ターゲット SQL Server プラットフォームの新機能についてわかります。 これらは、パフォーマンス、セキュリティ、ストレージごとに整理されます。 

    ![ターゲット デバイスの選択](./media/migrate-scenarios-assessment/dma-assessment-2.png)

2. **[サーバーへの接続]** で、SQL Server インスタンスを実行しているマシンの名前、認証の種類、接続の詳細を指定します。 次いで **[接続]** をクリックします。

    ![ターゲット デバイスの選択](./media/migrate-scenarios-assessment/dma-assessment-3.png)
    
3. **[ソースの追加]** で評価したいデータベースを選択し、**[追加]** をクリックします。
4. 指定した名前の評価が作成されます。

    ![評価を作成する](./media/migrate-scenarios-assessment/dma-assessment-4.png)

5.  **次に**、評価を開始します。
6. **[結果のレビュー]** で、有効にした評価テストの結果が表示されます。


### <a name="analyze-the-database-assessment"></a>データベース評価を分析する

結果は、利用可能になるとすぐに Assistant に表示されます。 

1. **[Compatibility Issues]\(互換性の問題\)** レポートでは、各互換性レベルでデータベースに問題があるかどうかを確認します。また、問題がある場合は、それらの問題を修正する方法を確認します。 互換性レベルでは SQL Server バージョンが次のようにマップされます。
    - 100: SQL Server 2008/Azure SQL Database
    - 110: SQL Server 2012/Azure SQL Database
    - 120: SQL Server 2014/Azure SQL Database
    - 130: SQL Server 2016/Azure SQL Database
    - 140: SQL Server 2017/Azure SQL Database

    ![互換性の問題](./media/migrate-scenarios-assessment/dma-assessment-5.png)

2. **[Feature recommendations]\(機能に関する推奨事項\)** レポートでは、移行後に構成するよう評価によって推奨されるパフォーマンス、セキュリティ、ストレージの機能を表示します。 インメモリ OLTP と列ストア、Stretch Database、Always Encrypted、動的データ マスク、Transparent Data Encryption など、さまざまな機能が推奨されます。

    ![機能に関する推奨事項](./media/migrate-scenarios-assessment/dma-assessment-6.png)

3. 問題を修正した場合、**[評価の再開]** をクリックして再実行します。 
4. **[レポートのエクスポート]** をクリックして、JSON または CSV 形式で評価レポートを取得します。

より大規模に評価を実行する場合:

- 複数の評価を同時に実行し、**[All Assessments]\(すべての評価\)** ページを開いて評価の状態を表示できます。
- [評価を SQL Server データベースに統合](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017#import-assessment-results-into-a-sql-server-database)できます。
- [評価を PowerBI レポートに統合](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017)できます。


## <a name="step-4-prepare-for-vm-assessment-with-azure-migrate"></a>手順 4: Azure Migrate による VM 評価の準備をする

評価対象の VM を自動で検出するために Azure Migrate によって使用される VMware アカウントを作成します。次に、VM を作成するためのアクセス許可があることを確認します。さらに、オープンにする必要があるポートを書き留め、統計情報設定レベルを設定します。

### <a name="set-up-a-vmware-account"></a>VMware アカウントを設定する

 vCenter の読み取り専用アカウントが必要です。 持っていない場合は、次のプロパティを備えた VMware アカウントを作成します。

- ユーザーの種類: 読み取り専用以上。
- アクセス許可: データ センター オブジェクト –> 子オブジェクトへの伝達、ロール=読み取り専用。
- 詳細: ユーザーはデータセンター レベルで割り当てられ、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。
- アクセスを制限するには、**子オブジェクトへの伝達**特権を持つ**アクセスなし**ロールを子オブジェクト (vSphere ホスト、データストア、VM、ネットワーク) に割り当てます。

### <a name="verify-permissions-to-create-a-vm"></a>VM を作成するためのアクセス許可を確認する

.OVA 形式でファイルをインポートして、VM を作成するためのアクセス許可があることを確認します。 [詳細情報](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1)。

### <a name="verify-ports"></a>ポートを確認する

このシナリオでは、依存関係マッピングを構成します。 この機能を使用するには、評価する VM にエージェントをインストールする必要があります。 このエージェントは、各 VM の TCP ポート 443 から Azure に接続できる必要があります。 接続要件の詳細については、[こちら](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid)をご覧ください。


### <a name="set-statistics-settings"></a>統計情報設定を行う

vCenter Server の統計情報設定は、デプロイを開始する前に、レベル 3 に設定する必要があります。 以下の点に注意してください。
- レベルを設定した後、少なくとも 1 日経ってから評価を実行する必要があります。 そうしないと、想定どおりに機能しない可能性があります。
- 3 より高いレベルにした場合、評価は機能します。ただし:
    - ディスクとネットワークのパフォーマンス データが収集されません。
    - ストレージに関して、オンプレミス ディスクとサイズが同じ Azure の Standard ディスクが Azure Migrate によって推奨されます。
    - ネットワークに関して、各オンプレミス ネットワーク アダプターごとに Azure のネットワーク アダプターが推奨されます。
    - コンピューティングに関して、Azure Migrate によって VM コアとメモリ サイズが調査され、同じ構成の Azure VM が推奨されます。 条件を満たす Azure VM サイズが複数ある場合、最も低コストのディスクをお勧めします。
   
    
レベル 3 でのサイズ設定の詳細については、[こちら](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing)をご覧ください。

レベルは次のように設定します。

1. vSphere Web Client で、vCenter サーバー インスタンスを開きます。
2. **[Manage]\(管理\)** タブを選択し、**[Settings]\(設定\)** で **[General]\(全般\)** をクリックします。
3. **[Edit]\(編集\)** をクリックし、**[Statistics]\(統計情報\)** で統計情報レベル設定を **[Level 3]\(レベル 3\)** に設定します。

    ![vCenter の統計情報レベル](./media/migrate-scenarios-assessment/vcenter-statistics-level.png)



## <a name="step-5-discover-vms"></a>手順 5: VM を検出する

Azure Migrate プロジェクトを作成し、コレクター VM をダウンロードして設定します。 次に、コレクターを実行して VM を検出します。

### <a name="create-a-project"></a>プロジェクトの作成

1. [Azure Portal](https://portal.azure.com) にログインし、**[リソースの作成]** をクリックします。
2. **Azure Migrate** を検索します。 検索結果で **[Azure Migrate]** を選択し、**[作成]** をクリックします。
3. プロジェクト名と Azure サブスクリプションを指定します。
4. 新しいリソース グループを作成します。
5. プロジェクトの場所を指定して、**[作成]** をクリックします。

    - Azure Migrate プロジェクトを作成できるのは、米国中西部または米国東部リージョンに限られます。
    - 移行は、任意のターゲットの場所で計画できます。
    - プロジェクトの場所は、オンプレミスの VM から収集されたメタデータを格納するためにのみ使用されます。

    ![Azure Migrate](./media/migrate-scenarios-assessment/project-1.png)


    

### <a name="download-the-collector-appliance"></a>コレクター アプライアンスをダウンロードする

Azure Migrate は、コレクター アプライアンスと呼ばれるオンプレミスの VM を作成します。 この VM はオンプレミスの VMware VM を検出し、それについてのメタデータを Azure Migrate サービスに送信します。 コレクター アプライアンスをセットアップするには、.OVA ファイルをダウンロードし、それをオンプレミスの vCenter サーバーにインポートして、VM を作成します。

1. Azure Migrate プロジェクトで、**[作業の開始]** > **[検出と評価]** > **[マシンの検出]** の順にクリックします。
2. **[マシンの検出]** で **[ダウンロード]** をクリックし、.OVA ファイルをダウンロードします。
3. **[プロジェクトの資格情報をコピーします]** で、プロジェクトの ID とキーをコピーします。 これらの情報はコレクターを構成するときに必要になります。

    ![.ova ファイルをダウンロードする](./media/migrate-scenarios-assessment/download-ova.png) 

### <a name="verify-the-collector-appliance"></a>コレクター アプライアンスを確認する

.OVA ファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたマシンで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、OVA のハッシュを生成します。
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用例: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 生成されたハッシュは、次の設定と一致する必要があります (バージョン 1.0.9.7)
    
    **アルゴリズム** | **ハッシュ値**
    --- | ---
    MD5 | d5b6a03701203ff556fa78694d6d7c35
    SHA1 | f039feaa10dccd811c3d22d9a59fb83d0b01151e
    SHA256 | e5e997c003e29036f62bf3fdce96acd4a271799211a84b34b35dfd290e9bea9c
    

### <a name="create-the-collector-appliance"></a>コレクター アプライアンスを作成する

ダウンロードしたファイルを vCenter Server にインポートします。

1. vSphere Client コンソールで、**[File]\(ファイル\)** > **[Deploy OVF Template]\(OVF テンプレートのデプロイ\)** の順にクリックします。

    ![OVF をデプロイする](./media/migrate-scenarios-assessment/vcenter-wizard.png) 

2. [Deploy OVF Template]\(OVF テンプレートのデプロイ\) ウィザードで **[Source]\(ソース\)** を選択し、.OVA ファイルの場所を指定して、**[Next]\(次へ\)** をクリックします。
3. **[OVF Template Details]\(OVF テンプレートの詳細\)** で、**[Next]\(次へ\)** をクリックします。 **[End User License Agreement]\(使用許諾契約書\)** で **[Accept]\(同意する\)** をクリックして契約に同意し、**[Next]\(次へ\)** をクリックします。
4. **[Name]\(名前\) と [Location]\(場所\)** で、コレクター VM のフレンドリ名と、VM がホストされるインベントリの場所を指定して、**[Next]\(次へ\)** をクリックします。 コレクター アプライアンスが実行されるホストまたはクラスターを指定します。
5. **[Storage]\(ストレージ\)** で、アプライアンスのファイルの格納先を指定し、**[Next]\(次へ\)** をクリックします。
6. **[Disk Format]\(ディスク フォーマット\)** で、ストレージをプロビジョニングする方法を指定します。
7. **[Network Mapping]\(ネットワーク マッピング\)** で、コレクター VM の接続先となるネットワークを指定します。 このネットワークには、Azure にメタデータを送信するためのインターネット接続が必要です。 
8. **[Ready to Complete]\(終了の準備完了\)** で設定を確認し、**[Power on after deployment]\(デプロイ後に電源をオンにする\)** を選択して、**[Finish]\(完了\)** をクリックします。

アプライアンスの作成後、正常に完了したことを確認するメッセージが発行されます。

### <a name="run-the-collector-to-discover-vms"></a>コレクターを実行して VM を検出する

開始する前に、現時点でオペレーティング システムの言語およびコレクター インターフェイスの言語としてコレクターでサポートされるのは、"英語 (米国)" のみであることに注意してください。

1. vSphere Client コンソールで、[VM] を右クリックして **[Open Console]\(コンソールを開く\)** を選択します。
2. アプライアンスの設定で、言語、タイム ゾーン、パスワードを指定します。
3. デスクトップにある **[Run collector]\(コレクターの実行\)** ショートカットをクリックします。

    ![コレクターのショートカット](./media/migrate-scenarios-assessment/collector-shortcut.png) 
    
4. Azure Migrate Collector で、**[Set Up Prerequisites]\(前提条件の設定\)** を開きます。
    - ライセンス条項に同意し、サード パーティの情報を確認します。
    - コレクターによって、VM にインターネット アクセスがあること、時間が同期されたこと、(既定で VM にインストールされる) コレクター サービスが実行されていることがチェックされます。 さらに、VMware PowerCLI がインストールされます。 
    
    > [!NOTE]
    > VM に、プロキシを使用しないインターネットへの直接アクセスがあると仮定しています。

    ![前提条件を確認する](./media/migrate-scenarios-assessment/collector-verify-prereqs.png)
    

5. **[Specify vCenter Server details]\(vCenter Server 詳細の指定\)** で、次の操作を行います。
    - vCenter サーバーの名前 (FQDN) または IP アドレスを指定します。
    - **[ユーザー名]** と **[パスワード]** で、コレクターが vCenter サーバー上の VM を検出するために使用する読み取り専用アカウント資格情報を指定します。
    - **[Select scope]\(スコープの選択\)** で、VM 検出のスコープを選択します。 コレクターは、指定されたスコープ内の VM のみを検出できます。 スコープは、指定のフォルダー、データセンター、またはクラスターに設定できます。 VM の数は 1,500 台を超えないようにします。 

    ![vCenter に接続する](./media/migrate-scenarios-assessment/collector-connect-vcenter.png)

6. **[Specify migration project]\(移行プロジェクトの指定\)** で、ポータルからコピーした Azure Migrate プロジェクトの ID とキーを指定します。 ID とキーをコピーしなかった場合は、コレクター VM から Azure Portal を開きます。 プロジェクトの **[概要]** ページで、**[マシンの検出]** をクリックして値をコピーします。  

    ![Azure への接続](./media/migrate-scenarios-assessment/collector-connect-azure.png)

7. **[View collection progress]\(収集の進行状況の表示\)** で検出を監視し、VM から収集されたメタデータがスコープ内にあることを確認します。 コレクターがおおよその検出時間を表示します。

    ![進行中の収集](./media/migrate-scenarios-assessment/collector-collection-process.png)
   


### <a name="verify-vms-in-the-portal"></a>ポータル内での VM の特定

収集の完了後、ポータルに表示された VM を確認します。

1. Azure Migrate プロジェクト内で、**[管理]** > **[マシン]** の順にクリックします。
2. 検出対象の VM がページに表示されていることを確認します。

    ![検出されたマシン](./media/migrate-scenarios-assessment/discovery-complete.png)

3. 現時点では、マシンに Azure Migrate エージェントがインストールされていないことに注意してください。 依存関係を表示できるよう、それらをインストールする必要があります。
    
    ![検出されたマシン](./media/migrate-scenarios-assessment/machines-no-agent.png)



## <a name="step-6-prepare-for-dependency-analysis"></a>手順 6: 依存関係の分析の準備をする

評価対象の VM 間の依存関係を表示するために、エージェントをダウンロードして Web アプリ VM (WEBVM と SQLVM) にインストールします。

### <a name="take-a-snapshot"></a>スナップショットを取得する

修正する前の VM のコピーを用意したい場合、エージェントをインストールする前にスナップショットを取得します。

![マシンのスナップショット](./media/migrate-scenarios-assessment/snapshot-vm.png) 


### <a name="download-and-install-the-vm-agents"></a>VM エージェントをダウンロードしてインストールする

1.  Azure Migrate プロジェクト、**[マシン]** ページの順に移動してマシンを選択し、**[依存関係]** 列の **[インストールする必要があります]** をクリックします。
2.  **[マシンの検出]** ページで、それぞれの VM について、Microsoft Monitoring Agent (MMA) と依存関係エージェントをダウンロードしてインストールします。
3.  ワークスペース ID とキーをコピーします。 これらは、MMA をインストールするときに必要です。

    ![エージェントのダウンロード](./media/migrate-scenarios-assessment/download-agents.png) 



#### <a name="install-the-mma"></a>MMA をインストールする

1. ダウンロードしたエージェントをダブルクリックします。
2. **[ようこそ]** ページで **[次へ]** をクリックします。 **[ライセンス条項]** ページで、**[同意する]** をクリックしてライセンスに同意します。
3. **[インストール先のフォルダー]** で、既定のインストール フォルダーをそのまま使用して、**[次へ]** をクリックします。 
4. **[エージェントのセットアップ オプション]** で、**[Connect the agent to Azure Log Analytics]\(Azure Log Analytics にエージェントを接続する\)** > **[次へ]** の順にクリックします。 

    ![MMA のインストール](./media/migrate-scenarios-assessment/mma-install.png) 
5. **[Azure Log Analytics]** で、ポータルからコピーしたワークスペース ID とキーを貼り付けます。 **[次へ]** をクリックします。
    ![MMA のインストール](./media/migrate-scenarios-assessment/mma-install2.png) 

6. **[インストールの準備完了]** で、MMA をインストールします。



#### <a name="install-the-dependency-agent"></a>依存関係エージェントをインストールする

1.  ダウンロードした依存関係エージェントをダブルクリックします。
2.  **[ライセンス条項]** ページで、**[同意する]** をクリックしてライセンスに同意します。
3.  **[インストール中]** で、インストールの完了を待ちます。 その後、 **[次へ]** をクリックします。

    ![依存関係エージェント](./media/migrate-scenarios-assessment/dependency-agent.png) 


       
## <a name="step-7-run-and-analyze-the-vm-assessment"></a>手順 7: VM 評価を実行して分析する

マシンの依存関係を確認し、グループを作成します。 次に、評価を実行します。

### <a name="verify-dependencies-and-create-a-group"></a>依存関係を確認し、グループを作成する。

1.  **[マシン]** ページで、分析したい VM について **[依存関係の表示]** をクリックします。

    ![マシンの依存関係の表示](./media/migrate-scenarios-assessment/view-machine-dependencies.png) 

2. 依存関係マップでは、SQLVM について次の詳細が表示されます。

    - 指定した期間中 (既定では 1 時間) に SQLVM で実行される、アクティブなネットワーク接続を伴うプロセス グループ/プロセス
    - 依存関係にあるすべてのマシン間の受信 (クライアント) および送信 (サーバー) TCP 接続。
    - Azure Migrate エージェントがインストールされた依存関係にあるマシンは、別々のボックスに表示されます
    - エージェントがインストールされていないマシンには、ポートと IP アドレスの情報が表示されます。
    
 3. エージェントがインストールされたマシン (WEBVM) について、マシンのボックスをクリックし、FQDN、オペレーティング システム、MAC アドレスなどの詳しい情報を表示します。 

    ![グループの依存関係の表示](./media/migrate-scenarios-assessment/sqlvm-dependencies.png)

4. 次に、グループに追加したい VM (SQLVM と WEBVM) を選択します。  Ctrl キーを押しながらクリックして、複数の VM を選択します。
5. **[グループの作成]** をクリックし、名前 (smarthotelapp) を指定します。

> [!NOTE]
    > さらに細かい依存関係を表示するために、時間の範囲を拡張できます。 特定の期間、つまり開始日と終了日を選択できます。 


### <a name="run-an-assessment"></a>評価を実行する


1. **[グループ]**  ページで、グループ (smarthotelapp) を開きます
2. **[評価を作成します]** をクリックします。

    ![評価を作成する](./media/migrate-scenarios-assessment/run-vm-assessment.png)

3. 評価が、**[管理]** > **[評価]** ページに表示されます。


### <a name="modify-assessment-settings"></a>評価の設定を変更する

このチュートリアルでは、既定の評価の設定を使用しましたが、次のように、設定はカスタマイズできます。

1. 移行プロジェクトの **[評価]** ページで、評価を選択し、**[プロパティの編集]** をクリックします。
2. 次の表に従ってプロパティを変更します。

    **設定** | **詳細** | **既定値**
    --- | --- | ---
    **ターゲットの場所** | 移行先にする Azure の場所 | 既定値はありません。
    **ストレージ冗長** | 移行後に Azure VM で使用されるストレージ冗長の種類。 | [ローカル冗長ストレージ (LRS)](../storage/common/storage-redundancy-lrs.md) が既定値です。 Azure Migrate ではマネージド ディスク ベースの評価だけがサポートされ、マネージド ディスクでは LRS だけがサポートされています。そのため、LRS オプションです。 
    **サイズ変更の設定基準** | Azure 用に VM を適切なサイズにするために Azure Migrate によって使用される基準。 "*パフォーマンスに基づく*" サイズ変更、またはパフォーマンスの履歴を考慮しない "*オンプレミスとしての*" VM のサイズ変更を実行できます。 | パフォーマンスに基づくサイズ変更が既定のオプションです。
    **パフォーマンス履歴** | VM のパフォーマンスを評価するために考慮する期間。 このプロパティは、サイズ変更の基準が "*パフォーマンスに基づくサイズ変更*" の場合にのみ適用されます。 | 既定値は 1 日です。
    **百分位数の使用率** | 適切なサイズ設定のために考慮するパフォーマンス サンプル セットのパーセンタイル値。 このプロパティは、サイズ変更の基準が "*パフォーマンスに基づくサイズ変更*" の場合にのみ適用されます。  | 既定は 95 パーセンタイルです。
    **[価格レベル]** | ターゲット Azure VM の[価格レベル (Basic/Standard)](../virtual-machines/windows/sizes-general.md) を指定できます。 たとえば、運用環境の移行を計画している場合は、Standard レベルを検討するかもしれません。この場合、VM の待ち時間は短くなりますが、コストは高くなります。 一方、開発/テスト環境の場合は、Basic レベルを検討するかもしれません。この場合、VM の待ち時間は長くなり、コストは安くなります。 | 既定では [Standard](../virtual-machines/windows/sizes-general.md) レベルが使用されます。
    **快適性係数** | Azure Migrate では、評価時にバッファー (快適性係数) が考慮されます。 VM のマシン使用率データ (CPU、メモリ、ディスク、ネットワーク) に加えて、このバッファーが適用されます。 快適性係数は、季節ごとの使用量、短期間のパフォーマンス履歴、将来に使用量が増える可能性などの問題に相当します。<br/><br/> たとえば、使用率 20% の 10 コア VM の結果は、通常 2 コア VM になります。 一方、快適性係数を 2.0x とした場合は、結果が 4 コア VM になります。 | 既定の設定は 1.3x です。
    **プラン** | 登録されている [Azure プラン](https://azure.microsoft.com/support/legal/offer-details/)。 | [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/)が既定値です。
    **通貨** | 請求通貨です。 | 既定値は、米ドルです。
    **割引率 (%)** | Azure プランに適用される任意のサブスクリプション固有の割引です。 | 既定の設定は 0% です。
    **Azure ハイブリッド特典** | ソフトウェア アシュアランスがあり、[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-use-benefit/)を受ける資格があるかどうかを指定します。 [はい] に設定すると、Windows VM に Windows Azure 以外の価格は考慮されません。 | 既定値は Yes です。

3. **[保存]** をクリックして、評価の設定を更新します。


### <a name="analyze-the-vm-assessment"></a>VM 評価を分析する

Azure Migrate 評価には、オンプレミスの VM と Azure の互換性の有無、推奨される適切な Azure VM サイズ設定、推定月間 Azure コストに関する情報が含まれています。 

![評価レポート](./media/migrate-scenarios-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>信頼度レーティングを確認する

![評価の表示](./media/migrate-scenarios-assessment/assessment-display.png)

評価には、1 つ星から 5 つ星 (1 つ星が最低で 5 つ星が最高) までの信頼度レーティングが付いています。
- 信頼度レーティングは、評価の計算に必要なデータ ポイントの可用性に基づいて、評価に割り当てられます。
- レーティングは、Azure Migrate による推奨サイズの信頼性を見積もるために役立ちます。
- 信頼度レーティングは、"*パフォーマンス ベースのサイズ変更*" を実施する際に役立てることができます。使用率ベースのサイズ変更を実施できるだけのデータ ポイントが Azure Migrate にはない場合があるためです。 "*オンプレミスと同じサイズ*" の場合、Azure Migrate には VM のサイズ変更に必要なすべてのデータ ポイントがあるため、信頼度レーティングは常に 5 つ星になります。
- 次のように、使用可能なデータ ポイントの割合に応じて、評価の信頼度レーティングが決まります。

   **データ ポイントの可用性** | **信頼度レーティング**
   --- | ---
   0% - 20% | 1 つ星
   21% - 40% | 2 つ星
   41% - 60% | 3 つ星
   61% - 80% | 4 つ星
   81% - 100% | 5 つ星

#### <a name="verify-readiness"></a>準備状態を確認する

![アセスメントの準備状態](./media/migrate-scenarios-assessment/azure-readiness.png)  

評価レポートには、情報が表でまとめられて表示されます。 パフォーマンス ベースのサイズ設定を表示するには、Azure Migrate に次の情報が必要であることに注意してください。 この情報を収集できない場合、サイズ設定評価は正確でない可能性があります。

- CPU とメモリの使用率データ。
- VM にアタッチされた各ディスクの読み取り/書き込み IOPS とスループット。
- VM にアタッチされた各ネットワーク アダプターのネットワーク受信/送信情報。


**設定** | **指示内容** | **詳細**
--- | --- | ---
**Azure VM 対応性** | VM が移行できる状態であるかどうかを示します | 次の状態があります。</br><br/>- Azure に対応<br/><br/>- 条件付きで対応 <br/><br/>- Azure に未対応<br/><br/>- 対応不明<br/><br/> VM の準備が整っていない場合、修正手順が表示されます。
**Azure VM サイズ** | 準備が完了している VM について、Azure VM サイズが推奨されます。 | サイズ設定の推奨事項は、評価のプロパティによって異なります。<br/><br/>- パフォーマンス ベースのサイズ設定を使用した場合、サイズ設定では VM のパフォーマンス履歴が考慮されます。<br/><br/>- "オンプレミス" を使用した場合、サイズ設定はオンプレミスの VM サイズに基づき、使用率データは使われません。
**推奨されるツール** | マシンではエージェントが実行されています。そのため、マシン内で実行されているプロセスが Azure Migrate によって確認され、そのマシンがデータベース マシンであるかどうかが特定されます。
**VM 情報** | レポートには、オペレーティング システム、ブートの種類、ディスクとストレージの情報など、オンプレミス VM の設定が表示されます。




#### <a name="review-monthly-cost-estimates"></a>推定月間コストを確認する

このビューには、Azure で VM を実行する際のコンピューティング コストおよびストレージ コストの合計と、各マシンの詳細が表示されます。 

![アセスメントの準備状態](./media/migrate-scenarios-assessment/azure-costs.png) 

- コストの見積もりは、マシンの推奨サイズを使って計算されます。
- コンピューティングとストレージの見積もり月額コストが、グループ内の全 VM について集計されます。 


## <a name="conclusion"></a>まとめ

このシナリオでは、以下を実行しました。

> [!div class="checklist"]
> * DMA ツールを使用してオンプレミス データベースを評価しました。
> * Azure Migrate サービスによる依存関係マッピングを使用して、オンプレミスの VM を評価しました。
> * オンプレミス リソースを Azure に移行する準備を整えるため、評価を確認しました。

## <a name="next-steps"></a>次の手順

次のシナリオに進んで、オンプレミスの VM とデータベースから Azure への[リフトアンドシフト移行](migrate-scenarios-lift-and-shift.md)を実行しましょう。



