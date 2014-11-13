<properties urlDisplayName="Provision HBase clusters on Azure Virtual Network" pageTitle="Azure Virtual Network での HBase クラスターのプロビジョニング | Azure" metaKeywords="" description="Azure Virtual Network での HDInsight クラスターの作成方法について説明します。" metaCanonical="" services="hdinsight" documentationCenter="" title="Azure Virtual Network での HBase クラスターのプロビジョニング" authors="jgao" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="jgao" />

# Azure Virtual Network での HBase クラスターのプロビジョニング

Azure Virtual Network での HDInsight クラスターの作成方法について説明します。

アプリケーションが HBase と直接通信できるように、仮想ネットワーク統合を使用して、HBase クラスターをアプリケーションと同じ仮想ネットワークにデプロイできます。次の利点があります。

-   Web アプリケーションと HBase クラスターのノード間の直接接続により、HBase Java RPC API を使用した通信が可能になります。
-   トラフィックが複数のゲートウェイやロード バランサーを経由しないためパフォーマンスが向上します。
-   機密情報は、パブリック エンドポイントに公開されることなく、より安全な方法で処理されます。

## この記事の内容

-   [前提条件][前提条件]
-   [HBase クラスターを仮想ネットワークへプロビジョニングする][HBase クラスターを仮想ネットワークへプロビジョニングする]
-   [HBase Java RPC API を使用して、仮想ネットワーム内でプロビジョニングされた HBase クラスターへ接続する][HBase Java RPC API を使用して、仮想ネットワーム内でプロビジョニングされた HBase クラスターへ接続する]
-   [Azure PowerShell を使用して HBase クラスターをプロビジョニングする][Azure PowerShell を使用して HBase クラスターをプロビジョニングする]
-   [次のステップ][次のステップ]

## <span id="prerequisites"></span></a>前提条件

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

-   **Azure サブスクリプション**。Azure はサブスクリプション方式のプラットフォームです。サブスクリプションの入手方法の詳細については、[購入オプション][購入オプション]、[メンバー プラン][メンバー プラン]、または[無料評価版][無料評価版]に関するページを参照してください。

-   **Azure PowerShell がインストールされ構成されたワークステーション**。手順については、[Azure PowerShell のインストールおよび構成に関するページ][Azure PowerShell のインストールおよび構成に関するページ]を参照してください。PowerShell スクリプトを実行するには、Azure PowerShell を管理者として実行し、実行ポリシーを *RemoteSigned* に設定する必要があります。「[Run Windows PowerShell scripts (Windows PowerShell スクリプトの実行)][Run Windows PowerShell scripts (Windows PowerShell スクリプトの実行)]」を参照してください。

    PowerShell スクリプトを実行する前に、次のコマンドレットを使用して Azure サブスクリプションに接続されていることを確認します。

        Add-AzureAccount

    Azure サブスクリプションが複数ある場合は、次のコマンドレットを使用して、現在のサブスクリプションを設定します。

        Select-AzureSubscription <AzureSubscriptionName>

## <span id="hbaseprovision"></span></a>HBase クラスターを仮想ネットワークへプロビジョニングする

**管理ポータルを使用して仮想ネットワークを作成するには、次の手順を実行します。**

1.  [Azure の管理ポータル][Azure の管理ポータル]にサインインします。
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

> [WACOM.NOTE] HDInsight クラスターは、データを保存するために Azure BLOB ストレージを使用します。詳細については、「[Use Azure Blob storage with Hadoop in HDInsight (HDInsight での Hadoop と Azure BLOB ストレージの使用)][Use Azure Blob storage with Hadoop in HDInsight (HDInsight での Hadoop と Azure BLOB ストレージの使用)]」を参照してください。ストレージ アカウントと BLOB ストレージ コンテナーが必要です。ストレージ アカウントの場所は、仮想ネットワークの場所およびクラスターの場所と一致している必要があります。

**Azure Storage アカウントと BLOB ストレージ コンテナーを作成するには、次の手順を実行します。**

1.  [Azure の管理ポータル][Azure の管理ポータル]にサインインします。
2.  左下隅にある **[新規]** をクリックし、**[データ サービス]**、**[ストレージ]**、**[簡易作成]** の順にクリックします。

3.  次の値を入力または選択します。

    -   **URL**: ストレージ アカウントの名前
    -   **場所**: ストレージ アカウントの場所。仮想ネットワークの場所と一致していることを確認します。アフィニティ グループはサポートされていません。
    -   **レプリケーション**: テストの目的には、**[ローカル冗長]** を使用してコストを削減します。

4.  **[ストレージ アカウントの作成]** をクリックします。新しいストレージ アカウントがストレージ一覧に表示されます。
5.  新しいストレージ アカウントの **[状態]** 列が **[オンライン]** になるまで待ちます。
6.  一覧の新しいストレージ アカウントをクリックして選択します。
7.  ページの下部にある **[アクセス キーの管理]** をクリックします。
8.  **[ストレージ アカウント名]** と **[プライマリ アクセス キー]** (または **[セカンダリ アクセス キー]**) の値をメモします。どちらのキーでもかまいません。この情報は後で必要になります。
9.  ページの上部にある **[コンテナー]** をクリックします。
10. ページの下部にある **[追加]** をクリックします。
11. コンテナーの名前を入力します。このコンテナーは、HBase クラスターの既定のコンテナーとして使用されます。既定では、既定のコンテナー名はクラスター名に一致します。**"アクセス"** フィールドは **[プライベート]** のままにします。
12. チェック アイコンをクリックしてコンテナーを作成します。

**Azure ポータルを使用して HBase クラスターをプロビジョニングするには、次の手順を実行します。**

> [WACOM.NOTE] PowerShell を使用した新しい HBase クラスターのプロビジョニングの詳細については、「[PowerShell を使用して HBase クラスターをプロビジョニングする][Azure PowerShell を使用して HBase クラスターをプロビジョニングする]」を参照してください。

1.  [Azure の管理ポータル][Azure の管理ポータル]にサインインします。
2.  左下隅にある **[新規]** をクリックし、**[データ サービス]**、**[HDINSIGHT]** の順にポイントして、**[カスタム作成]** をクリックします。
3.  **クラスター名**を入力し、クラスターに使用する **HDINSIGHT のバージョン**を選択してします。

    ![cluster name and version fields][cluster name and version fields]

4.  クラスターを作成する**データ ノード**の数を選択し、このクラスターを使用するリージョンまたは Azure Virtual Network (**[リージョン/仮想ネットワーク]**) を選択します。

    ![number of nodes and region fields][number of nodes and region fields]

5.  このクラスターで使用する管理者の**ユーザー名**と**パスワード**を入力します。

    ![administrator name and password fields][administrator name and password fields]

6.  新しいストレージ アカウントを使用するか、既存のストレージ アカウントを使用するかを選択します。新しいストレージ アカウントを使用する場合、**アカウント名**と**既定のコンテナー**を入力して作成します。最後に、チェックマークを選択して、クラスターを作成します。

    ![storage account selection][storage account selection]

新しい HBase クラスターの使用を開始する場合は、「[HDInsight の Hadoop 環境で HBase を使用する][HDInsight の Hadoop 環境で HBase を使用する]」に記載されている手順を使用できます。

## <span id="connect"></span></a>HBase Java RPC API を使用して、仮想ネットワーム内でプロビジョニングされた HBase クラスターへ接続する

1.  IaaS 仮想マシンを同じ Azure 仮想ネットワークと同じサブネットに対してプロビジョニングします。それにより、仮想マシンと HBase クラスターはどちらも同じ内部 DNS サーバーを使用してホスト名を解決します。そのためには、[ギャラリーから] を選択し、データ センターの代わりに仮想ネットワークを選択する必要があります。手順については、「[Windows Server を実行する仮想マシンの作成][Windows Server を実行する仮想マシンの作成]」を参照してください。標準の Windows Server 2012 イメージとサイズの小さい VM で十分です。

2.  Java アプリケーションを使用して HBase にリモートで接続する場合は、完全修飾ドメイン名 (FQDN) を使用する必要があります。これを確認するには、HBase クラスターの接続固有の DNS サフィックスを取得する必要があります。それには、Curl を使用して Ambari を照会するか、リモート デスクトップを使用してクラスターに接続します。

    -   **Curl** - 次のコマンドを実行します。

            curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

        返された JSON データで、"host\_name" エントリを見つけます。これには、クラスターのノードの完全修飾ドメイン名 (FQDN) が含まれています。次に例を示します。

            ...
            "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
            ...

        クラスター名で始まるドメイン名の部分は、DNS サフィックスです。たとえば、mycluster.b1.cloudapp.net です。

    -   **PowerShell** - 次の PowerShell スクリプトを使用して、DNS サフィックスを返すことができる **Get-ClusterDetail** 関数を登録します。

            function Get-ClusterDetail(
                [String]
                [Parameter( Position=0, Mandatory=$true )]
                $ClusterDnsName,
                [String]
                [Parameter( Position=1, Mandatory=$true )]
                $Username,
                [String]
                [Parameter( Position=2, Mandatory=$true )]
                $Password,
                [String]
                [Parameter( Position=3, Mandatory=$true )]
                $PropertyName
                )
            {
            <#
                .SYNOPSIS 
                 Displays information to facilitate HDInsight cluster to cluster scinario within same virtual network.
                .Description
                 This command shows following 4 properties of an HDInsight cluster.
                 1. ZookeeperQuorum (only support HBase type cluster)
                    Shows the value of hbase property "hbase.zookeeper.quorum".
                 2. ZookeeperClientPort (only support HBase type cluster)
                    Shows the value of hbase property "hbase.zookeeper.property.clientPort".
                 3. HBaseRestServers (only support HBase type cluster)
                    Shows a list of host FQDNs which run HBase rest server.
                 4. FQDNSuffix (support all type cluster)
                    Shows FQDN suffix of hosts in the cluster.
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
                 This command shows the value of hbase property "hbase.zookeeper.quorum".
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
                 This command shows the value of hbase property "hbase.zookeeper.property.clientPort".
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
                 This command shows a list of host FQDNs which run HBase rest server.
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
                 This command shows FQDN suffix of hosts in the cluster.
            #>

                $DnsSuffix = ".azurehdinsight.net"

                $ClusterFQDN = $ClusterDnsName + $DnsSuffix
                $webclient = new-object System.Net.WebClient
                $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

                if($PropertyName -eq "ZookeeperQuorum")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
                }
                if($PropertyName -eq "ZookeeperClientPort")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
                }
                if($PropertyName -eq "HBaseRestServers")
                {
                    $Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
                    $Response1 = $webclient.DownloadString($Url1)
                    $JsonObject1 = $Response1 | ConvertFrom-Json
                    $PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

                    $Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
                    $Response2 = $webclient.DownloadString($Url2)
                    $JsonObject2 = $Response2 | ConvertFrom-Json
                    foreach ($host_component in $JsonObject2.host_components)
                    {
                        $ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
                        Write-host $ConnectionString
                    }
                }
                if($PropertyName -eq "FQDNSuffix")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/components/resourcemanager"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    $FQDN = $JsonObject.host_components[0].HostRoles.host_name
                    $pos = $FQDN.IndexOf(".")
                    $Suffix = $FQDN.Substring($pos + 1)
                    Write-host $Suffix
                }
            }

        PowerShell スクリプトを実行した後、次のコマンドで Get-ClusterDetail 関数を使用して DNS サフィックスを返します。このコマンドを使用する場合は、HDInsight HBase のクラスター名、管理者名、および管理者のパスワードを指定します。

            Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

        これは、DNS サフィックスを返します。たとえば、**yourclustername.b4.internal.cloudapp.net** です。

    > [WACOM.NOTE] リモート デスクトップを使用して HBase クラスターに接続し (ヘッドノードに接続されます)、コマンド プロンプトから **ipconfig** を実行して DNS サフィックスを取得することもできます。RDP を有効にし、RDP を使用してクラスターに接続する手順については、「[Manage Hadoop clusters in HDInsight using the Azure Management Portal (Azure の管理ポータルを使用した HDInsight での Hadoop クラスターの管理)][Manage Hadoop clusters in HDInsight using the Azure Management Portal (Azure の管理ポータルを使用した HDInsight での Hadoop クラスターの管理)]」を参照してください。
    >
    > ![hdinsight.hbase.dns.surffix][hdinsight.hbase.dns.surffix]

<!--  3.  Change the Primary DNS Suffix configuration of the virtual machine. This enables virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to the workernode0 of the HBase cluster.       To make the configuration change:      1. RDP into the virtual machine.      2. Open **Local Group Policy Editor**. The executable is gpedit.msc.     3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.      - Set **Primary DNS Suffix** to the value obtained in the step 2:           ![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]     4. Click **OK**.      5. Reboot the virtual machine. -->

仮想マシンが HBase クラスターと通信できることを確認するには、仮想マシンからコマンド `ping headnode0.<dns suffix>` を使用します。たとえば、「ping headnode0.mycluster.b1.cloudapp.net」のように入力します。

Java アプリケーションでこの情報を使用するには、「[HDInsight (Hadoop) 環境の HBase を使用する Java アプリケーションを Maven で構築][HDInsight (Hadoop) 環境の HBase を使用する Java アプリケーションを Maven で構築]」の手順に従って、アプリケーションを作成します。アプリケーションをリモート HBase サーバーに接続するには、ZooKeeper の FQDN を使用するように、この例の **hbase-site.xml** ファイルを変更します。次に例を示します。

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [WACOM.NOTE] 独自の DNS サーバーの使用方法を含め、Azure Virtual Network の名前解決の詳細については、「[名前解決 (DNS)][名前解決 (DNS)]」を参照してください。

## <span id="powershell"></span></a>Azure PowerShell を使用して HBase クラスターをプロビジョニングする:\*\*

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

## <span id="nextsteps"></span></a> 次のステップ

このチュートリアルでは、HBase クラスターのプロビジョニング方法を学習しました。詳細については、次を参照してください。

-   [HDInsight の使用][HDInsight の使用]
-   [Provision Hadoop clusters in HDInsight (HDInsight での Hadoop クラスターのプロビジョニング)][Provision Hadoop clusters in HDInsight (HDInsight での Hadoop クラスターのプロビジョニング)]
-   [Get started using HBase with Hadoop in HDInsight (HDInsight の Hadoop 環境で HBase を使用する)][HDInsight の Hadoop 環境で HBase を使用する]
-   [Analyze real-time Twitter sentiment with HBase in HDInsight (HDInsight 環境の HBase で Twitter のセンチメントをリアルタイム分析)][Analyze real-time Twitter sentiment with HBase in HDInsight (HDInsight 環境の HBase で Twitter のセンチメントをリアルタイム分析)]
-   [仮想ネットワークの概要][仮想ネットワークの概要]

  [前提条件]: #prerequisites
  [HBase クラスターを仮想ネットワークへプロビジョニングする]: #hbaseprovision
  [HBase Java RPC API を使用して、仮想ネットワーム内でプロビジョニングされた HBase クラスターへ接続する]: #connect
  [Azure PowerShell を使用して HBase クラスターをプロビジョニングする]: #powershell
  [次のステップ]: #nextsteps
  [購入オプション]: http://azure.microsoft.com/ja-jp/pricing/purchase-options/
  [メンバー プラン]: http://azure.microsoft.com/ja-jp/pricing/member-offers/
  [無料評価版]: http://azure.microsoft.com/ja-jp/pricing/free-trial/
  [Azure PowerShell のインストールおよび構成に関するページ]: ../install-configure-powershell
  [Run Windows PowerShell scripts (Windows PowerShell スクリプトの実行)]: http://technet.microsoft.com/ja-jp/library/ee176949.aspx
  [Azure の管理ポータル]: https://manage.windowsazure.com
  []: http://msdn.microsoft.com/library/azure/jj156088.aspx
  [Use Azure Blob storage with Hadoop in HDInsight (HDInsight での Hadoop と Azure BLOB ストレージの使用)]: ../hdinsight-use-blob-storage/
  [cluster name and version fields]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png
  [number of nodes and region fields]: ./media/hdinsight-hbase-provision-vnet/hbasewizard2.png
  [administrator name and password fields]: ./media/hdinsight-hbase-provision-vnet/hbasewizard3.png
  [storage account selection]: ./media/hdinsight-hbase-provision-vnet/hbasewizard4.png
  [HDInsight の Hadoop 環境で HBase を使用する]: ../hdinsight-hbase-get-started/
  [Windows Server を実行する仮想マシンの作成]: ../virtual-machines-windows-tutorial/
  [Manage Hadoop clusters in HDInsight using the Azure Management Portal (Azure の管理ポータルを使用した HDInsight での Hadoop クラスターの管理)]: ../hdinsight-administer-use-management-portal/#rdp
  [hdinsight.hbase.dns.surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
  [HDInsight (Hadoop) 環境の HBase を使用する Java アプリケーションを Maven で構築]: azure.microsoft.com/ja-jp/documentation/articles/hdinsight-hbase-build-java-maven/
  [名前解決 (DNS)]: http://msdn.microsoft.com/ja-jp/library/azure/jj156088.aspx
  [HDInsight の使用]: ../hdinsight-get-started/
  [Provision Hadoop clusters in HDInsight (HDInsight での Hadoop クラスターのプロビジョニング)]: ../hdinsight-provision-clusters/
  [Analyze real-time Twitter sentiment with HBase in HDInsight (HDInsight 環境の HBase で Twitter のセンチメントをリアルタイム分析)]: ../hdinsight-hbase-twitter-sentiment/
  [仮想ネットワークの概要]: http://msdn.microsoft.com/library/azure/jj156007.aspx
