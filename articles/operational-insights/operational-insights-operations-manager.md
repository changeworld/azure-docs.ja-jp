<properties
   pageTitle="Operations Manager とオペレーション インサイトの連携に関する考慮事項"
   description="Microsoft Azure オペレーション インサイトを Operations Manager と連携させる場合、Operations Manager エージェントと管理グループのディストリビューションを使用して分析対象となるデータを収集し、そのデータをオペレーション インサイト サービスに送信することになります。"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="banders" />

# Operations Manager とオペレーション インサイトの連携に関する考慮事項

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Microsoft Azure オペレーション インサイトを Operations Manager と連携させる場合、Operations Manager エージェントと管理グループのディストリビューションを使用して分析対象となるデータを収集し、そのデータをオペレーション インサイト サービスに送信することになります。ただし、Web サービスに直接接続するエージェントを使用する場合、Operations Manager は不要です。オペレーション インサイトを Operations Manager と連携させるときは、以下の点を考慮してください。

また、自身の Operations Manager から Operational Insights への監視されたワークロードの実行アカウントの資格情報を指定する必要もあります。

## オペレーション インサイト ソフトウェアの機能と要件

オペレーション インサイトは、クラウド内の Web サービスと、その Web サービスに直接接続するエージェントで構成されるか、またはクラウド内の Web サービスと、Operations Manager エージェント、さらに環境内のコンピューターを管理する Operations Manager 管理グループで構成されます。

Operations Manager エージェント (データの収集) と管理グループ (エージェントの管理とオペレーション インサイト サービスへのデータの送信) を使用する場合は、次の点を理解しておく必要があります。

- Operations Manager エージェントは、収集と分析の対象となるデータが置かれているすべてのサーバーにインストールします。

- Operations Manager 管理グループは、エージェントからのデータをオペレーション インサイト Web サービスに転送します。データの分析は行いません。

- Operations Manager 管理グループは、Web サービスにデータをアップロードする関係上、インターネットにアクセスできることが必要です。

- ドメイン コントローラーとして使用しているコンピューターに Operations Manager 管理サーバーをインストールすることは、最善の結果が得られるようにするために避けてください。

- Operations Manager エージェントが Operations Manager 管理グループにネットワークで接続され、データを転送できることが必要です。

オペレーション インサイトは、System Center ヘルス サービスを使用してデータを収集し、分析することができます。Operations Manager は、System Center ヘルス サービスに依存します。サーバーにインストールされているプログラムを [プログラムの追加と削除] などで確認すると、System Center Operations Manager エージェント ソフトウェアが表示されます。これらはオペレーション インサイトで使用されているため削除しないでください。Operations Manager エージェント ソフトウェアを削除した場合、オペレーション インサイトが機能しなくなります。

## Operations Manager との連携

Operations Manager と Operational Insights との連携においてサポートされるのは、System Center Operations Manager 2012 R2 または System Center Operations Manager 2012 SP1 の Operations Manager エージェントだけです。それより前のバージョンの System Center Operations Manager との連携はサポートされません。データの収集には Operations Manager エージェントが使用されるため、SharePoint 2012 など、一部の分析対象ワークロードについては、特定の資格情報 (アクション アカウントまたは実行アカウント) を使用してサポートされます。

## Operational Insights と SQL Server 2012

Operations Manager を使用しているときは、System Center ヘルス サービスがローカル システム アカウントで実行されます。SQL Server 2008 R2 より前の SQL Server バージョンでは、ローカル システム アカウントが既定で有効になっており、システム管理者サーバー ロールに含まれていました。SQL Server 2012 では、システム管理者サーバー ロールにローカル システム ログインは含まれていません。そのため、Operational Insights を使用すると、SQL Server 2012 インスタンスを完全には監視できず、一部のルールでアラートが生成される可能性があります。

## インターネットと内部ネットワークの接続

Web サービスと直接接続するエージェントを使用している場合、エージェントは、Web サービスにデータを送信したり、最新の構成情報を Web サービスから受信したりするためにインターネットにアクセスする必要があります。

Operations Manager を使用している場合は、管理サーバーがインターネットにアクセスして、Web サービスにデータを送信したり、最新の構成情報を Web サービスから受信したりする必要があります。一方、エージェントがインターネットにアクセスする必要はありません。Operations Manager エージェントのあるサーバーがインターネットに接続されていない場合、そのサーバーが、インターネットに接続された管理サーバーと通信できれば、Web サービスを使用することが可能です。

## クラスタリングのサポート

Operations Manager エージェントは、Windows フェールオーバー クラスターの一部として構成されている Windows Server 2012、Windows Server 2008 R2、Windows Server 2008 を実行するコンピューターでサポートされます。クラスターはオペレーション インサイト ポータルで確認できます。[サーバー] ページでは、クラスターが (物理コンピューターを示す TYPE=AGENT ではなく) TYPE=CLUSTER として示されます。

検出および構成ルールはクラスターのアクティブ ノードとパッシブ ノードで動作しますが、パッシブ ノードで生成されたアラートは無視されます。ノードがパッシブからアクティブに切り替わった場合、自動的に、そのノードのアラートが表示されるようになるので、管理者が介入する必要はありません。

アラートを生成するルールによっては、アラートが 2 回生成される場合があります。たとえば、オペレーティング システムを調べて問題のあるドライバーを検出するルールは、物理サーバーとクラスターに対してアラートを生成します。

パッシブ ノードの構成を分析する機能はサポートされていません。

Windows Server を実行する複数のコンピューターが同じフェールオーバー クラスターに属している場合、それらのコンピューターをオペレーション インサイトでグループ化したり関連付けたりすることはできません。

## オペレーション インサイト環境のスケーリング

オペレーション インサイトのデプロイを計画するとき (特に、1 つの管理グループを介してデータを転送する Operations Manager エージェントの数を分析するとき) は、対象サーバーの容量をファイル領域の観点から検討してください。

次の点を考慮する必要があります。

- 管理グループあたりのエージェントの数

- エージェントから管理グループに転送される 1 日あたりの平均データ サイズ。既定では、各エージェントが 1 日に 2 回、管理グループに CAB ファイルをアップロードします。CAB ファイルのサイズは、サーバーの構成 (SQL Server エンジンの数やデータベースの数など)、およびサーバーの正常性 (生成されるアラートの数など) に依存します。1 日あたりの標準的なアップロード サイズは、ほとんどの場合 100 KB 未満です。

- 管理グループにデータが保持されるアーカイブ期間 (既定では 5 日)

たとえば、1 日のアップロード サイズがエージェントごとに 100 KB で既定のアーカイブ期間であるとすると、管理グループには次のストレージが必要になります。

エージェントの数|管理グループに必要な推定領域
---|---
5|最大 2.5 MB (5 エージェント × 100 KB データ/日 × 5 日 ＝ 2,500 KB)
50|最大 25 MB (50 エージェント × 100 KB データ/日 × 5 日 ＝ 25,000 KB)

## オペレーション インサイトで使用される Operations Manager の実行アカウント

Operational Insights は、データの収集 とOperational Insights サービスへのデータの送信に、Operations Manager エージェントと管理グループを使用します。オペレーション インサイトは、ワークロード用の管理パックを基に付加価値サービスを実現しています。それぞれのワークロードがさまざまなセキュリティ コンテキストで管理パックを実行するためには、ワークロード固有の特権が必要となります (ドメイン アカウントなど)。Operations Manager の実行アカウントを構成して資格情報を与えることが必要です。

以降のセクションでは、次のワークロードを対象に、Operations Manager の実行アカウントを設定する方法について説明しています。

- SQL の評価
- Virtual Machine Manager
- Lync Server
- SharePoint

### SQL の評価に使用する実行アカウントの設定

 SQL Server 管理パックを既に使用している場合、SQL Server の実行アカウントを使用する必要があります。

#### オペレーション コンソールで SQL の実行アカウントを構成するには

1. Operations Manager でオペレーション コンソールを開き、**[管理]** をクリックします。

2. **[実行アカウントの構成]** の **[プロファイル]** をクリックし、**[オペレーション インサイト SQL の評価の実行プロファイル]** を開きます。

3. **[実行アカウント]** ページの **[追加]** をクリックします。

4. SQL Server に必要な資格情報を含んだ Windows 実行アカウントを選択するか、**[新規]** をクリックして新たに作成します。
	>[AZURE.NOTE]実行アカウントの種類は Windows であることが必要です。さらに、SQL Server インスタンスをホストするすべての Windows Server 上のローカルの Administrators グループに、その実行アカウントが属している必要があります。

5. **[保存]** をクリックします。

6. 次の T-SQL サンプルに変更を加えて、各 SQL Server インスタンスで実行します。実行アカウントで SQL の評価を行うために必要な最低限の権限が付与されます。ただし、実行アカウントが既に SQL Server インスタンスの sysadmin サーバー ロールに属している場合、この作業は不要です。

```
---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```
#### To configure the SQL Run As account using Windows PowerShell

Open a PowerShell window and run the following script after you’ve updated it with your information:

```

    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "Operational Insights SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```


### Virtual Machine Manager の実行アカウントの設定

実行アカウントには、次の操作の特権が付与されている必要があります。

- VMM Windows PowerShell モジュールを使用する

- VMM データベースに対してクエリを実行する

- 仮想化ホスト上で動作する VMM エージェントをリモートから管理する

オペレーション インサイトを Operations Manager に接続するときは、以下の手順に従ってアカウントを設定してください。

#### VMM の資格情報を設定するには

1. Operations Manager でオペレーション コンソールを開き、**[管理]** をクリックします。

2. **[実行アカウントの構成]** の **[プロファイル]** をクリックし、**[オペレーション インサイト VMM 実行アカウント]** を開きます。

3. **[実行アカウント]** ページの **[追加]** をクリックします。

4. VMM に必要な資格情報を含んだ Windows 実行アカウントを選択するか、**[新規]** をクリックして新たに作成します。
	>[AZURE.NOTE]実行アカウントの種類は Windows であることが必要です。

5. **[保存]** をクリックします。


### Lync Server に使用する実行アカウントの設定

 実行アカウントは、ローカルの Administrators グループと Lync RTCUniversalUserAdmins セキュリティ グループの両方に属している必要があります。

#### Lync アカウントの資格情報を設定するには

1. Operations Manager でオペレーション コンソールを開き、**[管理]** をクリックします。

2. **[実行アカウントの構成]** の **[プロファイル]** をクリックし、**[オペレーション インサイト Lync 実行アカウント]** を開きます。

3. **[実行アカウント]** ページの **[追加]** をクリックします。

4. ローカルの Administrators グループと Lync RTCUniversalUserAdmins セキュリティ グループの両方に属している Windows 実行アカウントを選択します。
	>[AZURE.NOTE]実行アカウントの種類は Windows であることが必要です。

5. **[保存]** をクリックします。


### SharePoint に使用する実行アカウントの設定


#### SharePoint アカウントの資格情報を設定するには

1. Operations Manager でオペレーション コンソールを開き、**[管理]** をクリックします。

2. **[実行アカウントの構成]** の **[プロファイル]** をクリックし、**[オペレーション インサイト SharePoint 実行アカウント]** を開きます。

3. **[実行アカウント]** ページの **[追加]** をクリックします。

4. SharePoint に必要な資格情報を含んだ Windows 実行アカウントを選択するか、**[新規]** をクリックして新たに作成します。
	>[AZURE.NOTE]実行アカウントの種類は Windows であることが必要です。

5. **[保存]** をクリックします。



## 地理的な場所

さまざまな地理的な場所にあるサーバーからのデータを分析する場合は、場所ごとに 1 つの管理グループを設けることを検討してください。エージェントから管理グループへのデータ転送のパフォーマンスを向上させることができます。

<!---HONumber=August15_HO6-->