<properties linkid="manage-services-hdinsight-hbase-provision-on-vnet" urlDisplayName="Provision HBase clusters on Azure Virtual Network" pageTitle="Provision HBase clusters on Azure Virtual Network | Azure" metaKeywords="" description="Learn how to create HDInsight clusters on Azure Virtual Network." metaCanonical="" services="hdinsight" documentationCenter="" title="Provision HBase clusters on Azure Virtual Network" authors="jgao" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="jgao"></tags>

# Azure Virtual Network での HBase クラスターのプロビジョニング

Azure Virtual Network での HDInsight クラスターの作成方法について説明します。

アプリケーションが HBase と直接通信できるように、仮想ネットワーク統合を使用して、HBase クラスターをアプリケーションと同じ仮想ネットワークにデプロイできます。次の利点があります。

-   Web アプリケーションと HBase クラスターのノード間の直接接続により、HBase Java RPC API を使用した通信が可能になります。
-   トラフィックが複数のゲートウェイやロード バランサーを経由しないためパフォーマンスが向上します。
-   機密情報は、パブリック エンドポイントに公開されることなく、より安全な方法で処理されます。

> [WACOM.NOTE] 現在、HBase クラスターのみを Azure Virtual Network にプロビジョニングできます。Hadoop クラスターはサポートされていません。

## この記事の内容

-   [前提条件][]
-   [HBase クラスターを仮想ネットワークへプロビジョニングする][]
-   [HBase Java RPC API を使用して、仮想ネットワーム内でプロビジョニングされた HBase クラスターへ接続する][]
-   [次のステップ][]

## <span id="prerequisites"></span></a>前提条件

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

-   **Azure サブスクリプション**。Azure はサブスクリプション方式のプラットフォームです。サブスクリプションの入手方法の詳細については、[購入オプション][]、[メンバー プラン][]、または[無料評価版][]に関するページを参照してください。

-   **Azure PowerShell がインストールされ構成されたワークステーション**。手順については、[Azure PowerShell のインストールおよび構成に関するページ][]を参照してください。PowerShell スクリプトを実行するには、Azure PowerShell を管理者として実行し、実行ポリシーを *RemoteSigned* に設定する必要があります。「[Run Windows PowerShell scripts (Windows PowerShell スクリプトの実行)][]」を参照してください。

    PowerShell スクリプトを実行する前に、次のコマンドレットを使用して Azure サブスクリプションに接続されていることを確認します。

        Add-AzureAccount

    Azure サブスクリプションが複数ある場合は、次のコマンドレットを使用して、現在のサブスクリプションを設定します。

        Select-AzureSubscription <AzureSubscriptionName>

## <span id="hbaseprovision"></span></a>HBase クラスターを仮想ネットワークへプロビジョニングする

**管理ポータルを使用して仮想ネットワークを作成するには、次の手順を実行します。**

1.  [Azure の管理ポータル][]にサインインします。
2.  ページ下部で **[新規]**、**[ネットワーク サービス]**、**[VIRTUAL NETWORK]** の順にクリックし、さらに **[簡易作成]** をクリックします。
3.  次の値を入力または選択します。

    -   **名前**: 仮想ネットワークの名前
    -   **アドレス空間**: クラスター内のすべてのノードにアドレスを提供するために十分な大きさの、仮想ネットワークのアドレス空間を選択します。そうでないと、プロビジョニングは失敗します。
    -   **最大 VM 数**: 最大 VM 数のいずれかを選択します。
    -   **場所**: 場所は作成する HBase クラスターと同じである必要があります。
    -   **DNS サーバー**: この記事では、Azure が提供する内部 DNS サーバーを使用するため、**[なし]** を選択します。カスタム DNS サーバーを使用した、より高度なネットワーク構成もサポートされています。詳細なガイダンスについては、[][]<http://msdn.microsoft.com/library/azure/jj156088.aspx></a> を参照してください。

4.  **[仮想ネットワークの作成]** をクリックします。新しい仮想ネットワーク名が一覧に表示されます。[ステータス] 列に **[作成済み]** が表示されるまで待機します。
5.  メイン ウィンドウで、作成した仮想ネットワークをクリックします。
6.  ページの上部で、**[ダッシュボード]** をクリックします。
7.  **[概要]** の下の **[仮想ネットワーク ID]** を書き留めます。この ID は、HBase クラスターのプロビジョニング時に必要です。
8.  ページの上部で、**[構成]** をクリックします。
9.  ページの下部に示される既定のサブネット名は、**Subnet-1** です。必要に応じて、HBase クラスターのサブネット名を変更したり、新しいサブネットを追加したりできます。サブネット名を書き留めます。これは、クラスターのプロビジョニング時に必要となります。
10. クラスター用に使用するサブネットの **[CIDR (アドレス数)]** を確認します。アドレス数は、worker ノード数に 7 (ゲートウェイ: 2、ヘッドノード: 2、Zookeeper: 3) を加えた合計よりも多くする必要があります。たとえば、10 ノードの HBase クラスターが必要な場合、サブネットのアドレス数は、17 (10+7) を超えている必要があります。17 以下の場合、デプロイメントは失敗します。

    > [WACOM.NOTE] 1 つのクラスターに単一のサブネットを指定することを強くお勧めします。

11. サブネット値を更新した場合は、ページ下部の **[保存]** をクリックします。

> [WACOM.NOTE] HDInsight クラスターは、データを保存するために Azure BLOB ストレージを使用します。詳細については、「[Use Azure Blob storage with Hadoop in HDInsight (HDInsight での Hadoop と Azure BLOB ストレージの使用)][]」を参照してください。ストレージ アカウントと BLOB ストレージ コンテナーが必要です。ストレージ アカウントの場所は、仮想ネットワークの場所およびクラスターの場所と一致している必要があります。

**Azure Storage アカウントと BLOB ストレージ コンテナーを作成するには、次の手順を実行します。**

1.  [Azure の管理ポータル][]にサインインします。
2.  左下隅にある **[新規]** をクリックし、**[データ サービス]**、**[ストレージ]**、**[簡易作成]** の順にクリックします。

3.  次の値を入力または選択します。

    -   **URL**: ストレージ アカウントの名前
    -   **場所**: ストレージ アカウントの場所。仮想ネットワークの場所と一致していることを確認します。アフィニティ グループはサポートされていません。
    -   **レプリケーション**: テストの目的には、**[ローカル冗長]** を使用してコストを削減します。

4.  **[ストレージ アカウントの作成]** をクリックします。新しいストレージ アカウントがストレージ一覧に表示されます。
5.  新しいストレージ アカウントの **[状態]** 列が **[オンライン]** になるまで待ちます。
6.  一覧の新しいストレージ アカウントをクリックして選択します。
7.  ページの下部にある **[アクセス キーの管理]** をクリックします。
8.  **[ストレージ アカウント名]** と **[プライマリ アクセス キー]** または **[セカンダリ アクセス キー]** (動作しているいずれかのキー) の値を書き留めます。この情報は後で必要になります。
9.  ページの上部にある **[コンテナー]** をクリックします。
10. ページの下部にある **[追加]** をクリックします。
11. コンテナーの名前を入力します。このコンテナーは、HBase クラスターの既定のコンテナーとして使用されます。既定では、既定のコンテナー名はクラスター名に一致します。**"アクセス"** フィールドは **[プライベート]** のままにします。
12. チェック アイコンをクリックしてコンテナーを作成します。

**Azure PowerShell を使用して HBase クラスターをプロビジョニングするには、次の手順を実行します。**

1.  PowerShell ISE を開きます。
2.  次のコマンドをコピーしてスクリプト ウィンドウに貼り付けます。

        $hbaseClusterName = "<HBaseClusterName>"
        $hadoopUserName = "<HBaseClusterUsername>"
        $hadoopUserPassword = "<HBaseClusterUserPassword>"
        $location = "<HBaseClusterLocation>"  #i.e. "West US"
        $clusterSize = <HBaseClusterSize>  
        $vnetID = "<AzureVirtualNetworkID>"
        $subNetName = "<AzureVirtualNetworkSubNetName>"
        $storageAccountName = "<AzureStorageAccountName>" # Do not use the full name here
        $storageAccountKey = "<AzureStorageAccountKey>"
        $storageContainerName = "<AzureBlobStorageContainer>"

        $password = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
        $creds = New-Object System.Management.Automation.PSCredential ($hadoopUserName, $password) 

        New-AzureHDInsightCluster -Name $hbaseClusterName `
                                  -ClusterType HBase `
                                  -Version 3.1 `
                                  -Location $location `
                                  -ClusterSizeInNodes $clusterSize `
                                  -Credential $creds `
                                  -VirtualNetworkId $vnetID `
                                  -SubnetName $subNetName `
                                  -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
                                  -DefaultStorageAccountKey $storageAccountKey `
                                  -DefaultStorageContainerName $storageContainerName

3.  **[スクリプトの実行]** をクリックするか、**F5** キーを押します。
4.  クラスターを検証するために、管理ポータルからクラスターを確認するか、または下部のウィンドウから次の PowerShell コマンドレットを実行することができます。

    Get-AzureHDInsightCluster

新しい HBase クラスターをテストするために、「[Get started using HBase with Hadoop in HDInsight (HDInsight の Hadoop 環境で HBase を使用する)][]」に記載された手順を使用できます。

## <span id="connect"></span></a>HBase Java RPC API を使用して、仮想ネットワーム内でプロビジョニングされた HBase クラスターへ接続する

1.  IaaS 仮想マシンを同じ Azure 仮想ネットワークと同じサブネットに対してプロビジョニングします。それにより、仮想マシンと HBase クラスターはどちらも同じ内部 DNS サーバーを使用してホスト名を解決します。そのためには、[ギャラリーから] を選択し、データ センターの代わりに仮想ネットワークを選択する必要があります。手順については、「[Windows Server を実行する仮想マシンの作成][]」を参照してください。標準の Windows Server 2012 イメージとサイズの小さい VM で十分です。

2.  HBase クラスターの接続固有の DNS サフィックスを取得します。取得するには、HBase クラスターへの RDP 接続を確立し (ヘッドノードに接続される)、コマンド プロンプトで **ipconfig** を実行します。RDP を有効にし、RDP を使用してクラスターに接続する手順については、「[Manage Hadoop clusters in HDInsight using the Azure Management Portal (Azure の管理ポータルを使用した HDInsight での Hadoop クラスターの管理)][]」を参照してください。

    ![hdinsight.hbase.dns.surffix][]

3.  仮想マシンのプライマリ DNS サフィックス構成を変更します。これによって、仮想マシンは、明示的なサフィックスの指定なしで、HBase クラスターのホスト名を自動的に解決します。たとえば、ホスト名 *workernode0* は、HBase クラスターの workernode0 に正しく解決されます。
    構成を変更するには、次の手順を実行します。

    1.  仮想マシンへの RDP 接続を確立します。
    2.  **[ローカル グループ ポリシー エディター]** を開きます。実行可能ファイルは、gpedit.msc です。
    3.  **[コンピューターの構成]**、**[管理用テンプレート]**、**[ネットワーク]** の順に展開し、**[DNS クライアント]** をクリックします。
    4.  **[プライマリ DNS サフィックス]** を手順 2 で取得した値に設定します。

        ![hdinsight.hbase.primary.dns.suffix][]

    1.  **[OK]** をクリックします。
    2.  仮想マシンを再起動します。

    これで、仮想マシンはいつでも HBase クラスターと通信できます。接続をテストするには、仮想マシンから "ping headnode0" を実行します。

## <span id="nextsteps"></span></a> 次のステップ

このチュートリアルでは、HBase クラスターのプロビジョニング方法を学習しました。詳細については、次を参照してください。

-   [HDInsight の使用][]
-   [Provision Hadoop clusters in HDInsight (HDInsight での Hadoop クラスターのプロビジョニング)][]
-   [Get started using HBase with Hadoop in HDInsight (HDInsight の Hadoop 環境で HBase を使用する)][]
-   [Analyze real-time Twitter sentiment with HBase in HDInsight (HDInsight 環境の HBase で Twitter のセンチメントをリアルタイム分析)][]
-   [仮想ネットワークの概要][]

  [前提条件]: #prerequisites
  [HBase クラスターを仮想ネットワークへプロビジョニングする]: #hbaseprovision
  [HBase Java RPC API を使用して、仮想ネットワーム内でプロビジョニングされた HBase クラスターへ接続する]: #connect
  [次のステップ]: #nextsteps
  [購入オプション]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [メンバー プラン]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [無料評価版]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Azure PowerShell のインストールおよび構成に関するページ]: ../install-configure-powershell
  [Run Windows PowerShell scripts (Windows PowerShell スクリプトの実行)]: http://technet.microsoft.com/en-us/library/ee176949.aspx
  [Azure の管理ポータル]: https://manage.windowsazure.com
  []: http://msdn.microsoft.com/library/azure/jj156088.aspx
  [Use Azure Blob storage with Hadoop in HDInsight (HDInsight での Hadoop と Azure BLOB ストレージの使用)]: ../hdinsight-use-blob-storage/
  [Get started using HBase with Hadoop in HDInsight (HDInsight の Hadoop 環境で HBase を使用する)]: ../hdinsight-hbase-get-started/
  [Windows Server を実行する仮想マシンの作成]: ../virtual-machines-windows-tutorial/
  [Manage Hadoop clusters in HDInsight using the Azure Management Portal (Azure の管理ポータルを使用した HDInsight での Hadoop クラスターの管理)]: ../hdinsight-administer-use-management-portal/#rdp
  [hdinsight.hbase.dns.surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
  [hdinsight.hbase.primary.dns.suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
  [HDInsight の使用]: ../hdinsight-get-started/
  [Provision Hadoop clusters in HDInsight (HDInsight での Hadoop クラスターのプロビジョニング)]: ../hdinsight-provision-clusters/
  [Analyze real-time Twitter sentiment with HBase in HDInsight (HDInsight 環境の HBase で Twitter のセンチメントをリアルタイム分析)]: ../hdinsight-hbase-twitter-sentiment/
  [仮想ネットワークの概要]: http://msdn.microsoft.com/library/azure/jj156007.aspx
