<properties urlDisplayName="HDInsight Administration" pageTitle="HDInsight での Hadoop クラスターのプロビジョニング | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="管理ポータル、PowerShell、コマンド ラインを使用して Azure HDInsight のクラスターをプロビジョニングする方法について説明します。" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Provision Hadoop clusters in HDInsight (HDInsight での Hadoop クラスターのプロビジョニング)" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/25/2014" ms.author="jgao" />

# カスタム オプションを使用した HDInsight での Hadoop クラスターのプロビジョニング

この記事では、Azure 管理ポータル、PowerShell、コマンド ライン ツール、または HDInsight .NET SDK を使用した、Azure HDInsight 上の Hadoop クラスターのさまざまなカスタム プロビジョニング方法について学習します。この記事では、Hadoop クラスターのプロビジョニングについて説明します。HBase クラスターのプロビジョニング方法については、「[Provision HBase cluster in HDInsight (HDInsight での HBase クラスターのプロビジョニング)][Provision HBase cluster in HDInsight (HDInsight での HBase クラスターのプロビジョニング)]」を参照してください。2 つのうちのどちらかを選ぶ理由については、「[What Is The Difference Between HBase and Hadoop/HDFS? (HBase と Hadoop/HDFS の違い)][What Is The Difference Between HBase and Hadoop/HDFS? (HBase と Hadoop/HDFS の違い)]」を参照してください。

## HDInsight クラスターについて

Hadoop またはビッグデータを説明するときに、クラスターについて必ず触れるのはなぜでしょうか。その理由は、Hadoop がクラスターのさまざまなノードにまたがる大規模データの分散処理を可能にするためです。クラスターは、1 つのマスター (ヘッドノードまたは名前ノードともいいます) と任意の数のスレーブ (データ ノードともいいます) から成るマスター/スレーブ アーキテクチャを構成しています。詳細については、[Apache Hadoop に関する Web ページ][Apache Hadoop に関する Web ページ]を参照してください。

![HDInsight Cluster][HDInsight Cluster]

HDInsight クラスターでは、Hadoop の実装の詳細を抽象化しているため、クラスターの別のノードとの通信方法を心配する必要はありません。HDInsight クラスターをプロビジョニングすると、Hadoop と関連アプリケーションを含む Azure コンピューティング リソースがプロビジョニングされます。詳細については、「[HDInsight での Hadoop 入門][HDInsight での Hadoop 入門]」を参照してください。変更されるデータは、HDInsight のコンテキストでは "*Azure ストレージ - BLOB* (または WASB)" とも呼ばれる Azure BLOB ストレージに格納されます。詳細については、「[HDInsight での Azure BLOB ストレージの使用][HDInsight での Azure BLOB ストレージの使用]」を参照してください。

この記事では、クラスターをプロビジョニングするさまざまな手順を示します。クラスターをすばやくプロビジョニングする方法を調べる場合は、「[Azure HDInsight の概要][Azure HDInsight の概要]」を参照してください。

**前提条件:**

この記事を読み始める前に、次の項目を用意する必要があります。

-   Azure サブスクリプション。Azure はサブスクリプション方式のプラットフォームです。HDInsight PowerShell コマンドレットはサブスクリプションを使ってタスクを実行します。サブスクリプションの入手方法の詳細については、[購入オプション][購入オプション]、[メンバー プラン][メンバー プラン]、または[無料評価版][無料評価版]に関するページを参照してください。

## この記事の内容

-   [構成オプション][構成オプション]
-   [Azure 管理ポータルの使用][Azure 管理ポータルの使用]
-   [Azure PowerShell の使用][Azure PowerShell の使用]
-   [クロスプラットフォーム コマンド ラインの使用][クロスプラットフォーム コマンド ラインの使用]
-   [HDInsight .NET SDK の使用][HDInsight .NET SDK の使用]
-   [次のステップ][次のステップ]

## <span id="configuration"></span></a>構成オプション

### その他のストレージ

構成中に、Azure BLOB ストレージ アカウントと既定のコンテナーを指定する必要があります。このコンテナーは、既定の保存先としてクラスターで使用されます。必要に応じて、クラスターに関連付ける追加の BLOB も指定することができます。

従属的な BLOB ストアの使用の詳細については、「[HDInsight の Hadoop での BLOB ストレージの使用][HDInsight の Hadoop での BLOB ストレージの使用]」を参照してください。

### メタストア

メタストアには、Hive テーブル、パーティション、スキーマ、列などについての情報が格納されます。この情報は、データが HDFS (または HDInsight の WASB) のどこに格納されているかを見つけるために、Hive によって使用されます。既定では、Hive は組み込みデータベースを使用してこの情報を格納します。

HDInsight クラスターをプロビジョニングするとき、Hive のメタストアを格納する SQL Database を指定します。これにより、クラスターを削除するとき、SQL Database に外付けで格納されているメタデータ情報を保持できます。

### 仮想ネットワーク

[Azure 仮想ネットワーク][Azure 仮想ネットワーク]によって、ソリューションに必要なリソースを含む、セキュリティで保護された永続的なネットワークを作成できます。仮想ネットワークでは、次のことが可能になります。

-   プライベート ネットワーク (クラウドのみ) 内でのクラウド リソース間の接続

    ![diagram of cloud-only configuration][diagram of cloud-only configuration]

-   仮想プライベート ネットワーク (VPN) を使用したローカル データセンター ネットワークへのクラウド リソースの接続 (サイト間またはポイント対サイト)

    サイト間構成では、ハードウェア VPN を使用するか、ルーティングとリモート アクセス サービスを使用して、データセンターから複数のリソースを Azure 仮想ネットワークに接続できます。

    ![diagram of site-to-site configuration][diagram of site-to-site configuration]

    ポイント対サイト構成では、ソフトウェア VPN を使用して、特定のリソースを Azure 仮想ネットワークに接続できます。

    ![diagram of point-to-site configuration][diagram of point-to-site configuration]

仮想ネットワークの機能、利点の詳細については、「[Azure 仮想ネットワークの概要][Azure 仮想ネットワークの概要]」を参照してください。

> [WACOM.NOTE] HDInsight クラスターをプロビジョニングする前に、Azure 仮想ネットワークを作成する必要があります。詳細については、「[仮想ネットワークの構成タスク][仮想ネットワークの構成タスク]」を参照してください。
>
> Azure HDInsight は場所ベースの仮想ネットワークのみをサポートし、アフィニティ グループ ベースの仮想ネットワークは現在取り扱っていません。

> [WACOM.NOTE] 1 つのクラスターに単一のサブネットを指定することを強くお勧めします。

## <span id="portal"></span></a> Azure 管理ポータルの使用

HDInsight クラスターは、既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用します。HDInsight クラスターを作成するには、同じデータ センターにある Azure ストレージ アカウントが必要です。詳細については、「[HDInsight での Azure BLOB ストレージの使用][HDInsight での Azure BLOB ストレージの使用]」を参照してください。Azure ストレージ アカウントの作成の詳細については、「[ストレージ アカウントの作成方法][ストレージ アカウントの作成方法]」を参照してください。

> [WACOM.NOTE] 現在、HDInsight クラスターをホストできるリージョンは、**東アジア**、**東南アジア**、**北ヨーロッパ**、**西ヨーロッパ**、**米国東部**、**米国西部**、**米国中北部**、および**米国中南部**のみです。

**カスタム作成オプションを使用して HDInsight クラスターを作成するには**

1.  [Azure の管理ポータル][Azure の管理ポータル]にサインインします。
2.  ページ下部の **[+ 新規]** をクリックし、**[データ サービス]**、**[HDINSIGHT]**、**[カスタム作成]** の順にクリックします。
3.  **[クラスターの詳細]** ページで、次の値を入力または選択します。

    ![HDI.CustomCreateCluster][HDI.CustomCreateCluster]

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">プロパティ</th>
    <th align="left">値</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">クラスター名</td>
    <td align="left"><p>クラスターの名前を指定します。</p>
    <ul>
    <li>DNS 名の最初と最後の文字は英数字にする必要があります。それ以外ではダッシュ (-) を使用できます。</li>
    <li>3 ～ 63 文字のクラスター名を入力できます。</li>
    </ul></td>
    </tr>
    <tr class="even">
    <td align="left">クラスターの種類</td>
    <td align="left">クラスターの種類には、<strong>[Hadoop]</strong> を選択します。</td>
    </tr>
    <tr class="odd">
    <td align="left">HDInsight のバージョン</td>
    <td align="left">バージョンを選択します。Hadoop の場合、既定では HDInsight Version 3.1 で、これは Hadoop 2.4 を使用します。</td>
    </tr>
    </tbody>
    </table>

    表に示されている値を入力または選択し、右矢印をクリックします。

4.  **[クラスターの構成]** ページで、次の値を入力または選択します。

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">名前</th>
    <th align="left">値</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">データ ノード</td>
    <td align="left">デプロイするデータ ノードの数です。テストでは、単一ノード クラスターを作成します。<br />クラスター サイズの制限は、Azure サブスクリプションによって変わります。制限値を上げるには、Azure の課金サポートにお問い合わせください。</td>
    </tr>
    <tr class="even">
    <td align="left">リージョン/仮想ネットワーク</td>
    <td align="left"><p>最後の手順で作成したストレージ アカウントと同じリージョンを選択します。HDInsight は、同じリージョンに配置されたストレージ アカウントを必要とします。これ以後の構成作業では、ここで指定した地域と同じリージョンにあるストレージ アカウントしか選択できません。</p>
    <p>使用可能なリージョンは、<strong>東アジア</strong>、<strong>東南アジア</strong>、<strong>北ヨーロッパ</strong>、<strong>西ヨーロッパ</strong>、<strong>米国東部</strong>、<strong>米国西部</strong>、<strong>米国中北部</strong>、<strong>米国中南部</strong>です。<br />Azure 仮想ネットワークを作成した場合は、HDInsight クラスターで使用するよう構成するネットワークを選択できます。</p>
    <p>Azure 仮想ネットワークの作成の詳細については、「[仮想ネットワークの構成タスク]」(http://msdn.microsoft.com/ja-jp/library/azure/jj156206.aspx) を参照してください。</p></td>
    </tr>
    </tbody>
    </table>

5.  **[クラスター ユーザーの構成]** ページで、次の情報を指定します。

    ![HDI.CustomCreateCluster.ClusterUser][HDI.CustomCreateCluster.ClusterUser]

    | プロパティ                       | 値                                                                                                                                                                                                                                                                                      |
    |----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | ユーザー名                       | HDInsight クラスターのユーザー名を指定します。                                                                                                                                                                                                                                          |
    | パスワード/パスワードの確認      | HDInsight クラスターのユーザー パスワードを指定します。                                                                                                                                                                                                                                 |
    | Hive/Oozie メタストアの入力      | チェックボックスをオンにして、クラスターと同じデータ センターにある Hive/Oozie メタストアとして使用する SQL データベースを指定します。これは、クラスターが削除された後でも Hive/Oozie ジョブに関するメタデータを保持する場合に便利です。                                                |
    | メタストア データベース          | Hive/OOzie のメタストアとして使用する Azure SQL データベースを指定します。この SQL データベースは、HDInsight クラスターと同じデータ センターにある必要があります。ボックスの一覧には、**[クラスターの詳細]** ページで指定したデータ センターにある SQL データベースしか表示されません。 |
    | データベース ユーザー            | データベースへの接続時に使用する SQL データベース ユーザーを指定します。                                                                                                                                                                                                                |
    | データベース ユーザー パスワード | SQL データベース ユーザーのパスワードを指定します。                                                                                                                                                                                                                                     |

    > [WACOM.NOTE] メタストアに使用される Azure SQL データベースでは、Azure HDInsight などの他の Azure サービスに接続できる必要があります。Azure SQL データベース ダッシュボードの右側に表示されているサーバー名をクリックします。これは、SQL データベース インスタンスが実行されているサーバーです。サーバー ビューが表示されたら、**[構成]** をクリックします。**[Windows Azure サービス]** に対して **[はい]** をクリックし、**[保存]** をクリックします。

    右矢印をクリックします。

6.  **[ストレージ アカウント]** ページで、次の情報を指定します。

    ![HDI.CustomCreateCluster.StorageAccount][HDI.CustomCreateCluster.StorageAccount]

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">プロパティ</th>
    <th align="left">値</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">ストレージ アカウント</td>
    <td align="left">HDInsight クラスターの既定のファイル システムとして使用する Azure ストレージ アカウントを指定します。次の 3 つのオプションのいずれかを選択できます。
    <ul>
    <li>既存のストレージを使用する</li>
    <li>新しいストレージを作成する</li>
    <li>別のサブスクリプションのストレージを使用する</li>
    </ul></td>
    </tr>
    <tr class="even">
    <td align="left">アカウント名</td>
    <td align="left"><ul>
    <li>既存のストレージを使用する場合は、<strong>[アカウント名]</strong> で既存のストレージ アカウントを選択します。ボックスの一覧には、クラスターをプロビジョニングする対象として選択したデータ センターと同じデータ センターにあるストレージ アカウントのみが表示されます。</li>
    <li><strong>[新しいストレージを作成する]</strong> または <strong>[別のサブスクリプションのストレージを使用する]</strong> を選択した場合は、ストレージ アカウント名を指定する必要があります。</li>
    </ul></td>
    </tr>
    <tr class="odd">
    <td align="left">アカウント キー</td>
    <td align="left"><strong>[別のサブスクリプションのストレージを使用する]</strong> を選択した場合は、そのストレージ アカウントのアカウント キーを指定します。</td>
    </tr>
    <tr class="even">
    <td align="left">既定のコンテナー</td>
    <td align="left"><p>ストレージ アカウントの既定のコンテナーを指定します。既定のコンテナーは、HDInsight クラスターの既定のファイル システムとして使用されます。<strong>[ストレージ アカウント]</strong> で <strong>[既存のストレージを使用する]</strong> を選択したにもかかわらずそのアカウントに既存のコンテナーがない場合は、既定でクラスターと同じ名前のコンテナーが作成されます。クラスター名と同じ名前のコンテナーが既に存在する場合は、コンテナー名に連番が付加されます。たとえば、mycontainer1、mycontainer2、などとなります。ただし、既存のストレージ アカウントに指定したクラスター名と異なる名前のコンテナーがある場合は、そのコンテナーを使用できます。</p>
    <p>新しいストレージの作成または別の Azure サブスクリプションのストレージの使用を選択した場合は、既定のコンテナー名を指定する必要があります。</p></td>
    </tr>
    <tr class="odd">
    <td align="left">追加のストレージ アカウント</td>
    <td align="left">HDInsight は複数のストレージ アカウントをサポートします。クラスターで使用できる追加ストレージ アカウントの数に制限はありません。ただし、管理ポータルを使用してクラスターを作成する場合は、UI の制約により 7 が上限になります。ここで指定した追加のストレージ アカウントのそれぞれに対する [ストレージ アカウント] ページがウィザードに追加され、そこでアカウント情報を指定できます。たとえば、上のスクリーンショットでは、追加のストレージ アカウントが 1 つ選択されたため、ページ 5 がダイアログに追加されています。</td>
    </tr>
    </tbody>
    </table>

    右矢印をクリックします。

7.  **[ストレージ アカウント]** ページで、追加したストレージ アカウントのアカウント情報を入力します。

    ![HDI.CustomCreateCluster.AddOnStorage][HDI.CustomCreateCluster.AddOnStorage]

    ここでも、既存のストレージを使用する、新しいストレージを作成する、別の Azure サブスクリプションのストレージを使用する、のいずれかを選択できます。値を指定するための手順は、前の手順と同様です。

    チェック マークをクリックして、クラスターのプロビジョニングを開始します。プロビジョニングが完了すると、[状態] 列に **[実行中]** と表示されます。

    > [WACOM.NOTE] HDInsight クラスター用の Azure ストレージ アカウントを選択した後は、そのアカウントを削除することも、別のアカウントに変更することもできません。

## <span id="powershell"></span></a> Azure PowerShell の使用

Azure PowerShell は、Azure のワークロードの展開と管理を制御し自動化するために使用できる強力なスクリプティング環境です。このセクションでは、HDInsight クラスターをプロビジョニングする方法について説明します。コンピューターを構成して HDInsight Powershell コマンドレットを実行する方法については、「[Azure PowerShell のインストールおよび構成][Azure PowerShell のインストールおよび構成]」を参照してください。HDInsight で PowerShell を使用する方法の詳細については、「[PowerShell を使用した HDInsight の管理][PowerShell を使用した HDInsight の管理]」を参照してください。HDInsight PowerShell コマンドレットの一覧については、「[HDInsight コマンドレット リファレンス][HDInsight コマンドレット リファレンス]」を参照してください。

> [WACOM.NOTE] このセクションのスクリプトは、Azure 仮想ネットワークの HDInsight クラスターの構成に使用できますが、Azure 仮想ネットワークは作成されません。Azure 仮想ネットワークの作成の詳細については、「[仮想ネットワークの構成タスク][仮想ネットワークの構成タスク]」を参照してください。

PowerShell を使用して HDInsight クラスターをプロビジョニングするには、以下の手順が必要です。

-   Azure のストレージ アカウントの作成
-   Azure BLOB コンテナーの作成
-   HDInsight クラスターの作成

HDInsight は、既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用します。HDInsight クラスターを作成するには Azure ストレージ アカウントとストレージ コンテナーが必要です。ストレージ アカウントは、HDInsight クラスターと同じデータ センターに置く必要があります。

**Azure ストレージ アカウントを作成するには**

-   次のコマンドを Azure PowerShell コンソール ウィンドウから実行します。

        $storageAccountName = "<StorageAcccountName>"   # Provide a storage account name 
        $location = "<MicrosoftDataCenter>"             # For example, "West US"

        # Create an Azure storage account
        New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

    既にストレージ アカウントを持っていて、アカウント名とアカウント キーがわからない場合は、次の PowerShell コマンドを使ってその情報を取得できます。

        # List storage accounts for the current subscription
        Get-AzureStorageAccount

        # List the keys for a storage account
        Get-AzureStorageKey "<StorageAccountName>"

**Azure BLOB コンテナーを作成するには**

-   次のコマンドを Azure PowerShell コンソール ウィンドウから実行します。

        $storageAccountName = "<StorageAccountName>"    # Provide the storage account name 
        $storageAccountKey = "<StorageAccountKey>"      # Provide either primary or secondary key
        $containerName="<ContainerName>"                # Provide a container name

        # Create a storage context object
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName 
                                               -StorageAccountKey $storageAccountKey  

        # Create a Blob storage container
        New-AzureStorageContainer -Name $containerName -Context $destContext

ストレージ アカウントを用意して、BLOB コンテナーを準備したら、クラスターを作成する準備は整いました。

**HDInsight クラスターをプロビジョニングするには**

> [WACOM.NOTE] PowerShell コマンドレットは、HDInsight クラスターの構成変数を変更する方法として唯一お勧めする方法です。リモート デスクトップ経由でクラスターに接続している間に Hadoop 構成ファイルに加えられた変更は、クラスター パッチの際に上書きされる可能性があります。PowerShell 経由で設定された構成値は、クラスターにパッチが適用された場合でも保持されます。

-   次のコマンドを Azure PowerShell コンソール ウィンドウから実行します。

        $subscriptionName = "<SubscriptionName>"        # Name of the Azure subscription.
        $storageAccountName = "<StorageAccountName>"    # Azure storage account that hosts the default container. 
        $containerName = "<ContainerName>"              # Azure Blob container that is used as the default file system for the HDInsight cluster.

        $clusterName = "<HDInsightClusterName>"         # The name you will name your HDInsight cluster.
        $location = "<MicrosoftDataCenter>"             # The location of the HDInsight cluster. It must in the same data center as the storage account.
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster.

        # Get the storage primary key based on the account name
        Select-AzureSubscription $subscriptionName
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

        # Create a new HDInsight cluster
        New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

    入力を求められたら、クラスターの資格情報を入力します。クラスターのプロビジョニングが完了するまでに数分かかる場合があります。

    ![HDI.CLI.Provision][HDI.CLI.Provision]

**カスタム構成オプションを使用して HDInsight クラスターをプロビジョニングするには**

クラスターをプロビジョニングするときに、複数の Azure BLOB ストレージへの接続、仮想ネットワークの使用、Hive メタストアおよび Oozie メタストア用の Azure SQL データベースの使用など、その他の構成オプションを使用できます。これにより、データとメタデータの寿命を、クラスターの寿命と切り離すことができます。

> [WACOM.NOTE] PowerShell コマンドレットは、HDInsight クラスターの構成変数を変更する方法として唯一お勧めする方法です。リモート デスクトップ経由でクラスターに接続している間に Hadoop 構成ファイルに加えられた変更は、クラスター パッチの際に上書きされる可能性があります。PowerShell 経由で設定された構成値は、クラスターにパッチが適用された場合でも保持されます。

-   次のコマンドを Windows PowerShell ウィンドウから実行します。

        $subscriptionName = "<SubscriptionName>"
        $clusterName = "<ClusterName>"
        $location = "<MicrosoftDataCenter>"
        $clusterNodes = <ClusterSizeInNodes>

        $storageAccountName_Default = "<DefaultFileSystemStorageAccountName>"
        $containerName_Default = "<DefaultFileSystemContainerName>"

        $storageAccountName_Add1 = "<AdditionalStorageAccountName>"

        $hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
        $hiveSQLDatabaseName = "<SQLDatabaseDatabaseNameForHiveMetastore>"
        $oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>"
        $oozieSQLDatabaseName = "<SQLDatabaseDatabaseNameForOozieMetastore>"

        # Get the virtual network ID and subnet name
        $vnetID = "<AzureVirtualNetworkID>"
        $subNetName = "<AzureVirtualNetworkSubNetName>" 

        # Get the storage account keys
        Select-AzureSubscription $subscriptionName
        $storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
        $storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }

        $oozieCreds = Get-Credential -Message "Oozie metastore"
        $hiveCreds = Get-Credential -Message "Hive metastore"

        # Create a Blob storage container
        $dest1Context = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default  
        New-AzureStorageContainer -Name $containerName_Default -Context $dest1Context

        # Create a new HDInsight cluster
        $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
            Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
            Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 |
            Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore |
            Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore |
                New-AzureHDInsightCluster -Name $clusterName -Location $location -VirtualNetworkId $vnetID -SubnetName $subNetName

    > [WACOM.NOTE] メタストアに使用される Azure SQL Database は、Azure HDInsight などの他の Azure サービスに接続できる必要があります。Azure SQL データベース ダッシュボードの右側に表示されているサーバー名をクリックします。これは、SQL データベース インスタンスが実行されているサーバーです。サーバー ビューが表示されたら、**[構成]** をクリックします。**[Windows Azure サービス]** に対して **[はい]** をクリックし、**[保存]** をクリックします。

**HDInsight クラスターの一覧を表示するには**

-   次のコマンドを Azure PowerShell コンソール ウィンドウから実行して HDInsight クラスターを一覧表示し、クラスターが正常にプロビジョニングされたことを確認します。

        Get-AzureHDInsightCluster -Name <ClusterName>

## <span id="cli"></span></a>クロスプラットフォーム コマンド ラインの使用

> [WACOM.NOTE] 2014 年 8 月 29 日時点で、クロスプラットフォーム コマンド ライン インターフェイスは、クラスターと Azure 仮想ネットワーク間の関連付けに使用できません。

HDInsight クラスターをプロビジョニングするもう 1 つの方法は、クロスプラットフォーム コマンド ライン インターフェイスです。コマンド ライン ツールは Node.js で実装されていて、Windows、Mac、Linux など、Node.js をサポートするどのプラットフォームでも使用できます。コマンド ライン ツールはオープン ソースです。ソース コードは GitHub (<https://github.com/Azure/azure-sdk-tools-xplat>) で管理されています。コマンド ライン インターフェイスの使用方法の一般的ガイドについては、「[Mac および Linux 用 Azure コマンド ライン ツールの使用方法][Mac および Linux 用 Azure コマンド ライン ツールの使用方法]」を参照してください。包括的なリファレンス ドキュメントについては、「[Mac および Linux 用 Azure コマンド ライン ツール][Mac および Linux 用 Azure コマンド ライン ツール]」を参照してください。この記事では、Windows からコマンド ライン インターフェイスを使用する方法だけを取り上げます。

クロスプラットフォーム コマンド ラインを使用して HDInsight クラスターをプロビジョニングするには、以下の手順が必要です。

-   クロスプラットフォーム コマンド ラインのインストール
-   Azure アカウントの発行設定のダウンロードとインポート
-   Azure のストレージ アカウントの作成
-   クラスターのプロビジョニング

コマンド ライン インターフェイスは *Node.js パッケージ マネージャー (NPM)* または Windows インストーラーを使用してインストールできます。Microsoft では、この 2 つのオプションのいずれかを使用してインストールすることをお勧めします。

**NPM を使用してコマンド ライン インターフェイスをインストールするには**

1.  ブラウザーで **www.nodejs.org** を開きます。
2.  **[INSTALL]** をクリックし、指示に従います。設定は既定の設定を使います。
3.  コンピューターから**コマンド プロンプト** (または *Azure コマンド プロンプト*、または *VS2012 の開発者コマンド プロンプト*) を開きます。
4.  コマンド プロンプト ウィンドウで次のコマンドを実行します。

        npm install -g azure-cli

    > [WACOM.NOTE] NPM コマンドが見つからないというエラー メッセージが表示された場合は、次のパスが PATH 環境変数にあるか確認します。*C:\\Program Files (x86)\\nodejs;C:\\Users[username]\\AppData\\Roaming\\npm* または *C:\\Program Files\\nodejs;C:\\Users[username]\\AppData\\Roaming\\npm*

5.  次のコマンドを実行してインストールを確認します。

        azure hdinsight -h

    *-h* スイッチを使うと、さまざまなレベルでヘルプ情報を表示できます。次に例を示します。

        azure -h
        azure hdinsight -h
        azure hdinsight cluster -h
        azure hdinsight cluster create -h

**Windows インストーラーを使用してコマンド ライン インターフェイスをインストールするには**

1.  ブラウザーで **<http://azure.microsoft.com/ja-jp/downloads/>** を開きます。
2.  下へスクロールして、**[コマンド ライン ツール]** セクションの **[クロスプラットフォーム コマンド ライン インターフェイス]** をクリックし、Web プラットフォーム インストーラー ウィザードの指示に従います。

**発行設定をダウンロードしてインポートするには**

コマンド ライン インターフェイスを使用する前に、自分のコンピューターと Azure との接続を構成する必要があります。Azure のサブスクリプション情報は、コマンド ライン インターフェイスがアカウントにアクセスする際に使用されます。この情報は、Azure から発行設定ファイルとして入手できます。発行設定ファイルは永続的なローカル構成設定としてインポートすることができます。インポートすると、コマンド ライン インターフェイスの以降の操作にはこのファイルが使用されます。発行設定のインポートは 1 回だけ行う必要があります。

> [WACOM.NOTE] 発行設定ファイルには、機密情報が含まれます。Microsoft では、このファイルを削除するか、追加の作業を行ってファイルのある user フォルダーを暗号化することをお勧めします。Windows の場合、フォルダー プロパティを変更するか、または BitLocker を使用します。

1.  **コマンド プロンプト**を開きます。
2.  次のコマンドを実行して、発行設定ファイルをダウンロードします。

        azure account download

    ![HDI.CLIAccountDownloadImport][HDI.CLIAccountDownloadImport]

    このコマンドは、発行設定ファイルのダウンロード元の Web ページを起動します。

3.  ファイルを保存するためのプロンプトで、**[保存]** をクリックし、ファイルの保存先となる場所を指定します。
4.  コマンド プロンプト ウィンドウで次のコマンドを実行して、発行設定ファイルをインポートします。

        azure account import <file>

    先のスクリーンショットで、発行設定ファイルはコンピューターの C:\\HDInsight フォルダーに保存されています。

**Azure ストレージ アカウントを作成するには**

HDInsight は、既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用します。HDInsight クラスターを作成するには Azure ストレージ アカウントが必要です。ストレージ アカウントは、クラスターと同じデータ センターに配置する必要があります。

-   コマンド プロンプト ウィンドウで次のコマンドを実行し、Azure ストレージ アカウントを作成します。

        azure storage account create [options] <StorageAccountName>

    場所を指定するよう求められたら、HDINsight クラスターをプロビジョニングできる場所を選択します。このストレージは、HDInsight クラスターと同じ場所にある必要があります。現在、HDInsight クラスターをホストできるリージョンは、**東アジア**、**東南アジア**、**北ヨーロッパ**、**西ヨーロッパ**、**米国東部**、**米国西部**、**米国中北部**、および**米国中南部**のみです。

Azure 管理ポータルを使った Azure ストレージ アカウントの作成については、「[ストレージ アカウントの作成方法][ストレージ アカウントの作成方法]」を参照してください。

既にストレージ アカウントを持っていて、アカウント名とアカウント キーがわからない場合は、次のコマンドを使ってその情報を取得できます。

    -- lists storage accounts
    azure storage account list

    -- Shows information for a storage account
    azure storage account show <StorageAccountName>

    -- Lists the keys for a storage account
    azure storage account keys list <StorageAccountName>

管理ポータルを使用して情報を取得する方法の詳細については、「[ストレージ アカウントの管理方法][ストレージ アカウントの管理方法]」の「*方法: ストレージ アクセス キーの表示、コピーおよび再生成*」を参照してください。

HDInsight クラスターでは、ストレージ アカウント内にコンテナーも必要です。指定するストレージ アカウントにまだコンテナーがない場合は、*azure hdinsight cluster create* により、コンテナー名を指定するよう求められ、コンテナーも作成されます。ただし、コンテナーを事前に作成する場合は、次のコマンドを使用できます。

    azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]
        

ストレージ アカウントを用意して、BLOB コンテナーを準備したら、クラスターを作成する準備は整いました。

**HDInsight クラスターをプロビジョニングするには**

-   コマンド プロンプト ウィンドウで次のコマンドを実行します。

        azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <SorageContainerName> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

    ![HDI.CLIClusterCreation][HDI.CLIClusterCreation]

**構成ファイルを使用して HDInsight クラスターをプロビジョニングするには**

通常は、HDInsight クラスターをプロビジョニングして、ジョブを実行した後、クラスターを削除してコストを削減します。コマンド ライン インターフェイスを使うと、構成をファイルに保存して、クラスターをプロビジョニングするたびにその構成を再利用することができます。

-   コマンド プロンプト ウィンドウで次のコマンドを実行します。

        #Create the config file
        azure hdinsight cluster config create <file> 

        #Add commands to create a basic cluster
        azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"

        #If requred, include commands to use additional blob storage with the cluster
        azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
               --storageAccountKey "<StorageAccountKey>"

        #If required, include commands to use a SQL database as a Hive metastore
        azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
               --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

        #If required, include commands to use a SQL database as an Oozie metastore 
        azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
               --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"

        #Run this command to create a cluster using the config file     
        azure hdinsight cluster create --config <file>

    > [WACOM.NOTE] メタストアに使用される Azure SQL データベースでは、Azure HDInsight などの他の Azure サービスに接続できる必要があります。Azure SQL データベース ダッシュボードの右側に表示されているサーバー名をクリックします。これは、SQL データベース インスタンスが実行されているサーバーです。サーバー ビューが表示されたら、**[構成]** をクリックします。**[Windows Azure サービス]** に対して **[はい]** をクリックし、**[保存]** をクリックします。

    ![HDI.CLIClusterCreationConfig][HDI.CLIClusterCreationConfig]

**クラスターの一覧と詳細を表示するには**

-   クラスターの一覧と詳細を表示するには、次のコマンドを使用します。

        azure hdinsight cluster list
        azure hdinsight cluster show <ClusterName>

    ![HDI.CLIListCluster][HDI.CLIListCluster]

**クラスターを削除するには**

-   クラスターを削除するには、次のコマンドを使用します。

        azure hdinsight cluster delete <ClusterName>

## <span id="sdk"></span></a> HDInsight .NET SDK の使用

HDInsight .NET SDK は、.NET アプリケーションから HDInsight を簡単に操作できる .NET クライアント ライブラリを提供します。

SDK を使用して HDInsight クラスターをプロビジョニングするには、以下の手順を実行する必要があります。

-   HDInsight .NET SDK のインストール
-   自己署名証明書の作成
-   コンソール アプリケーションの作成
-   アプリケーションの実行

**HDInsight .NET SDK をインストールするには**

公開されている最新の SDK を [NuGet][NuGet] からインストールできます。次の手順で、具体的な方法を説明します。

**自己署名証明書を取得するには**

自己署名証明書を作成し、それをコンピューターにインストールして、さらに、Azure サブスクリプションにアップロードします。手順については、「[Create a self-signed certificate (自己署名証明書の作成)][Create a self-signed certificate (自己署名証明書の作成)]」を参照してください。

**Visual Studio コンソール アプリケーションを作成するには**

1.  Visual Studio 2013 を開きます。

2.  [ファイル] メニューの **[新規作成]** をクリックし、**[プロジェクト]** をクリックします。

3.  [新しいプロジェクト] で、次の値を入力または選択します。

    | プロパティ   | 値                              |
    |--------------|---------------------------------|
    | カテゴリ     | テンプレート/Visual C#/Windows |
    | テンプレート | コンソール アプリケーション     |
    | 名前         | CreateHDICluster                |

4.  **[OK]** をクリックしてプロジェクトを作成します。

5.  **[ツール]** メニューで **[NuGet パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。

6.  コンソールで次のコマンドを実行して、パッケージをインストールします。

        Install-Package Microsoft.WindowsAzure.Management.HDInsight

    このコマンドは、.NET ライブラリおよび .NET ライブラリへの参照を現在の Visual Studio プロジェクトに追加します。

7.  ソリューション エクスプローラーで **Program.cs** をダブルクリックして、このファイルを開きます。

8.  次の using ステートメントをファイルの先頭に追加します。

        using System.Security.Cryptography.X509Certificates;
        using Microsoft.WindowsAzure.Management.HDInsight;
        using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;

9.  Main() 関数で、次のコードをコピーしてファイルに貼り付けます。

        string certfriendlyname = "<CertificateFriendlyName>";     // Friendly name for the certificate your created earlier  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);

        // Create the storage account if it doesn't exist.

        // Create the container if it doesn't exist.

        // Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

        // Supply th cluster information
        ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
        {
            Name = clustername,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = containername,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize
        };

        // Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue.");
        Console.ReadKey();

10. main() 関数の先頭にある変数を置き換えます。

**アプリケーションを実行するには**

アプリケーションを Visual Studio で開いている間に、**F5** キーを押してアプリケーションを実行します。コンソール ウィンドウが開き、アプリケーションの状態が表示されます。HDInsight クラスターの作成は数分かかる場合があります。

## <span id="nextsteps"></span></a>次のステップ

この記事では、HDInsight クラスターをプロビジョニングする方法をいくつか説明しました。詳細については、次の記事を参照してください。

-   [Azure HDInsight の概要][Azure HDInsight の概要]
-   [PowerShell を使用した HDInsight の管理][PowerShell を使用した HDInsight の管理]
-   [プログラムによる Hadoop ジョブの送信][プログラムによる Hadoop ジョブの送信]
-   [Azure HDInsight SDK のドキュメント][Azure HDInsight SDK のドキュメント]

  [Provision HBase cluster in HDInsight (HDInsight での HBase クラスターのプロビジョニング)]: http://azure.microsoft.com/ja-jp/documentation/articles/hdinsight-hbase-get-started/
  [What Is The Difference Between HBase and Hadoop/HDFS? (HBase と Hadoop/HDFS の違い)]: http://go.microsoft.com/fwlink/?LinkId=510237
  [Apache Hadoop に関する Web ページ]: http://go.microsoft.com/fwlink/?LinkId=510084
  [HDInsight Cluster]: ./media/hdinsight-provision-clusters/HDI.Cluster.png
  [HDInsight での Hadoop 入門]: ../hdinsight-hadoop-introduction/
  [HDInsight での Azure BLOB ストレージの使用]: ../hdinsight-use-blob-storage/
  [Azure HDInsight の概要]: ../hdinsight-get-started/
  [購入オプション]: http://azure.microsoft.com/ja-jp/pricing/purchase-options/
  [メンバー プラン]: http://azure.microsoft.com/ja-jp/pricing/member-offers/
  [無料評価版]: http://azure.microsoft.com/ja-jp/pricing/free-trial/
  [構成オプション]: #configuration
  [Azure 管理ポータルの使用]: #portal
  [Azure PowerShell の使用]: #powershell
  [クロスプラットフォーム コマンド ラインの使用]: #cli
  [HDInsight .NET SDK の使用]: #sdk
  [次のステップ]: #nextsteps
  [HDInsight の Hadoop での BLOB ストレージの使用]: http://azure.microsoft.com/ja-jp/documentation/articles/hdinsight-use-blob-storage/
  [Azure 仮想ネットワーク]: http://azure.microsoft.com/ja-jp/documentation/services/virtual-network/
  [diagram of cloud-only configuration]: .\media\hdinsight-provision-clusters\cloud-only.png
  [diagram of site-to-site configuration]: .\media\hdinsight-provision-clusters\site-to-site.png
  [diagram of point-to-site configuration]: .\media\hdinsight-provision-clusters\point-to-site.png
  [Azure 仮想ネットワークの概要]: http://msdn.microsoft.com/library/azure/jj156007.aspx
  [仮想ネットワークの構成タスク]: http://msdn.microsoft.com/ja-jp/library/azure/jj156206.aspx
  [ストレージ アカウントの作成方法]: ../storage-create-storage-account/
  [Azure の管理ポータル]: https://manage.windowsazure.com/
  [HDI.CustomCreateCluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
  [HDI.CustomCreateCluster.ClusterUser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
  [HDI.CustomCreateCluster.StorageAccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
  [HDI.CustomCreateCluster.AddOnStorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png
  [Azure PowerShell のインストールおよび構成]: ../install-configure-powershell/
  [PowerShell を使用した HDInsight の管理]: ../hdinsight-administer-use-powershell/
  [HDInsight コマンドレット リファレンス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dn479228.aspx
  [HDI.CLI.Provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png
  [Mac および Linux 用 Azure コマンド ライン ツールの使用方法]: ../xplat-cli/
  [Mac および Linux 用 Azure コマンド ライン ツール]: ../command-line-tools/
  [HDI.CLIAccountDownloadImport]: ./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png
  [ストレージ アカウントの管理方法]: ../storage-manage-storage-account/
  [HDI.CLIClusterCreation]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png
  [HDI.CLIClusterCreationConfig]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png
  [HDI.CLIListCluster]: ./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "クラスタ―の一覧と表示"
  [NuGet]: http://nuget.codeplex.com/wikipage?title=Getting%20Started
  [Create a self-signed certificate (自己署名証明書の作成)]: http://go.microsoft.com/fwlink/?LinkId=511138
  [プログラムによる Hadoop ジョブの送信]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Azure HDInsight SDK のドキュメント]: http://msdn.microsoft.com/ja-jp/library/dn479185.aspx
