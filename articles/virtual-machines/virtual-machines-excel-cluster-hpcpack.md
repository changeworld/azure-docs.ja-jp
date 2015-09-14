<properties
 pageTitle="HPC Pack クラスターを開始して Excel と SOA ワークロードを実行する | Microsoft Azure"
	description="."
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>
<tags
ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="08/18/2015"
	ms.author="danlep"/>

# Azure で HPC Pack クラスターを開始して Excel と SOA ワークロードを実行する

この記事では、Azure クイックスタート テンプレートまたは Azure PowerShell デプロイメント スクリプトを使用して Azure インフラストラクチャ サービス (IaaS) に HPC Pack クラスターをデプロイする方法を示します。HPC Pack で Microsoft Excel またはサービス指向アーキテクチャ (SOA) のワークロードを実行するように設計されている Azure Marketplace VM イメージを使用します。クラスターを使用して、オンプレミスのクライアント コンピューターから簡単な Excel HPC サービスおよび SOA サービスを実行できます。Excel の HPC サービスには、Excel ブックのオフロードと Excel ユーザー定義関数、または UDF が含まれます。

これから作成する HPC Pack クラスターの概要は次の図のようになります。

![Excel ワークロードを実行するノードを含む HPC クラスター][scenario]

## 前提条件

* **クライアント コンピューター** - Azure PowerShell のクラスター デプロイメント スクリプト (このデプロイメント方法を選択する場合) を実行したり、Excel および SOA のサンプル ジョブをクラスターに送信したりするには、Windows ベースのクライアント コンピューターが必要です。

* **Azure サブスクリプション** - アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/)を参照してください。

* **コア クォータ** - 場合によっては、コアのクォータを増やす必要があります。特に、マルチコア VM サイズのクラスター ノードをいくつかデプロイする場合に必要となる可能性があります。Azure クイックスタート テンプレートを使用する場合、リソース マネージャーのコア クォータは Azure リージョンごとであることに注意してください。特定のリージョンのクォータを増やす必要がある可能性があります。「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)」をご覧ください。クォータを増やすには、[オンライン カスタマー サポートに申請](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) (無料) できます。


## 手順 1.Azure で HPC Pack クラスターをセットアップする

クラスターをセットアップする 2 つの方法を説明します。1 つめは Azure クイックスタート テンプレートと Azure プレビュー ポータルを使用する方法で、2 つめは Azure PowerShell デプロイメント スクリプトを使用する方法です。


### クイックスタート テンプレートを使用する
Azure クイックスタート テンプレートを使用すると、Azure プレビュー ポータルで HPC Pack クラスターをすばやく簡単にデプロイできます。ポータルでテンプレートを開くと表示される簡単な UI で、クラスターの設定を入力します。手順は次のようになります。

1. [GitHub の HPC クラスター テンプレートの作成に関するページ](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)を参照します。必要な場合は、テンプレートとソース コードに関する情報を確認します。

2. Azure プレビュー ポータルで **[Azure にデプロイ]** をクリックして、テンプレートによるデプロイメントを開始します。

    ![テンプレートを Azure にデプロイする][github]

3. ポータルで以下の手順に従って、HPC クラスター テンプレートのパラメーターを入力します。

    a.**[テンプレートの編集]** ページで、**[保存]** をクリックします。

    ![テンプレートを保存する][template]

    b.**[パラメーター]** ページで、テンプレート パラメーターの値を入力します (各設定の隣のアイコンをクリックするとヘルプ情報が表示されます)。 次の画面に示されているのはサンプルの値です。この例では、1 つのヘッド ノードと 2 つの計算ノードで構成される *hpc01* という名前の新しい HPC Pack クラスターが、*hpc.local* ドメインに作成されます。計算ノードは、Microsoft Excel を含む HPC Pack VM イメージから作成されます。

    ![パラメーターを入力する][parameters]

    >[AZURE.NOTE]ヘッド ノードの VM は、Windows Server 2012 R2 上の HPC Pack 2012 R2 の[最新の Marketplace イメージ](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)から自動的に作成されます。現時点では、イメージは HPC Pack 2012 R2 Update 2 に基づいています。
    >
    >計算ノードの VM は、選択した計算ノード ファミリの最新のイメージから作成されます。汎用の最新の HPC Pack 2012 R2 Update 2 計算イメージの **ComputeNode** オプションを選択します。Microsoft Excel Professional Plus 2013 の評価版を含む最新の HPC Pack 計算ノード イメージの **ComputeNodeWithExcel** オプションを選択します。一般的な SOA セッション用または Excel UDF オフロード用にクラスターをデプロイする場合は、**ComputeNode** オプションを選択します (Excel はインストールされません)。
    >
    >運用ワークロードに **ComputeNodeWithExcel** を使用する場合は、有効な Excel ライセンスを提供して計算ノードで Excel をアクティブ化する必要があります。そうしないと、Excel の評価版は 30 日間の有効期限であり、Excel ブックを実行すると COMExeption (0x800AC472) で常に失敗します。このような場合は、ヘッド ノードにログオンして HPC Cluster Manager コンソールから “%ProgramFiles(x86)%\\Microsoft Office\\Office15\\OSPPREARM.exe” をすべての Excel 計算ノードで clusrun 実行し、さらにExcel の 30 日間の評価日数を再実装します。猶予期間の再実装の最大回数は 2 回なので、それを過ぎると有効な Excel ライセンスを実装する必要があります。

    c.サブスクリプションを選択します。

    d.クラスターの新しいリソース グループを作成します (*hpc01RG* など)。

    e.リソース グループの場所を選択します (米国東部など)。

    f.**[法律条項]** ページで、条項を確認します。同意する場合は、**[購入]** をクリックします。

    g.テンプレートの値の設定が完了したら、**[作成]** をクリックします。

    ![クラスターを作成する][create]

3.	デプロイメントが完了したら (通常約 30 分かかります)、クラスターのヘッド ノードからクラスターの証明書ファイルをエクスポートします。後の手順でこのパブリック証明書をクライアント コンピューターにインポートし、セキュリティで保護された HTTP バインディングのサーバー側認証を提供します。

    a.Azure プレビュー ポータルからリモート デスクトップでヘッド ノードに接続します。

     ![ヘッド ノードに接続する][connect]

    b.標準的な手順で証明書マネージャーを使用して、秘密キーを含まないヘッド ノード証明書 (Cert:\\LocalMachine\\My の下にあります) をエクスポートします。この例では、*CN = hpc01.eastus.cloudapp.azure.com* をエクスポートします。

    ![証明書をエクスポートする][cert]

### HPC Pack IaaS デプロイメント スクリプトを使用する

HPC Pack IaaS デプロイメント スクリプトは、HPC Pack クラスターをデプロイするためのもう 1 つの汎用性の高い方法です。スクリプトは Azure サービス管理 (ASM) モードで実行しますが、テンプレートは Azure リソース マネージャー (ARM) モードで実行します。また、スクリプトは Azure Global サービスまたは Azure China サービスのサブスクリプションと互換性があります。

**追加の前提条件**

* **Azure PowerShell** - クライアント コンピューターに [Azure PowerShell をインストールして構成します](../powershell-install-configure.md) (バージョン 0.8.10 以降)。

* **HPC Pack IaaS デプロイメント スクリプト** - [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949) から最新版のスクリプトをダウンロードし、解凍します。`New-HPCIaaSCluster.ps1 –Version` を実行して、スクリプトのバージョンを確認します。この記事はバージョン 4.4.0 以降のスクリプトに基づきます。

**構成ファイルを作成する**

 HPC Pack IaaS デプロイメント スクリプトは HPC クラスターのインフラストラクチャについて記載された XML 構成ファイルを入力として使用します。Microsoft Excel を含む計算ノード イメージから作成された 1 個のヘッド ノードと 18 個の計算ノードで構成されるクラスターをデプロイするには、次のサンプル構成ファイルを実際の環境の値に置き換えます。構成ファイルの詳細については、スクリプト フォルダーまたは[スクリプト文書](https://msdn.microsoft.com/library/azure/dn864734.aspx)にある Manual.rtf ファイルを参照してください。

```
<?xml version="1.0" encoding="utf-8"?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>MySubscription</SubscriptionName>
    <StorageAccount>hpc01</StorageAccount>
  </Subscription>
  <Location>West US</Location>
  <VNet>
    <VNetName>hpc-vnet01</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>HPCExcelDC01</VMName>
      <ServiceName>HPCExcelDC01</ServiceName>
      <VMSize>Medium</VMSize>
    </DomainController>
  </Domain>
   <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>HPCExcelHN01</VMName>
    <ServiceName>HPCExcelHN01</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI/>
    <EnableWebPortal/>
<PostConfigScript>C:\tests\PostConfig.ps1</PostConfigScript>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>HPCExcelCN%00%</VMNamePattern>
    <ServiceName>HPCExcelCN01</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>18</NodeCount>
    <ImageName HPCPackInstalled="true">96316178b0644ae08bc4e037635ce104__HPC-Pack-2012R2-Update2-CN-Excel-4.4.4864.0-WS2012R2-ENU</ImageName>
  </ComputeNodes>
</IaaSClusterConfig>
```

**構成ファイルに関する注意事項**

* ヘッド ノードの **VMName** は、**ServiceName** と正確に一致している必要があります。

* ヘッド ノード証明書が生成されてエクスポートされるように、**EnableWebPortal** を必ず指定してください。

* 構成後 PowerShell スクリプト PostConfig.ps1 では、Azure ストレージ接続文字列の設定、ヘッド ノードからの計算ノード ロールの削除、デプロイ後の全ノードのオンライン化など、ヘッド ノードでの特定の設定が構成されます。スクリプトのサンプルを次に示します。

```
    # add the HPC Pack powershell cmdlets
        Add-PSSnapin Microsoft.HPC

    # set the Azure storage connection string for the cluster
        Set-HpcClusterProperty -AzureStorageConnectionString 'DefaultEndpointsProtocol=https;AccountName=<yourstorageaccountname>;AccountKey=<yourstorageaccountkey>'

    # remove the compute node role for head node to make sure the Excel workbook won’t run on head node
        Get-HpcNode -GroupName HeadNodes | Set-HpcNodeState -State offline | Set-HpcNode -Role BrokerNode

    # total number of nodes in the deployment including the head node and compute nodes
        $TotalNumOfNodes = 19

        $ErrorActionPreference = 'SilentlyContinue'

    # bring nodes online when they are deployed until all nodes are online
        while ($true)
        {
          Get-HpcNode -State Offline | Set-HpcNodeState -State Online -Confirm:$false
          $OnlineNodes = @(Get-HpcNode -State Online)
          if ($OnlineNodes.Count -eq $TotalNumOfNodes)
          {
             break
          }
          sleep 60
        }
```

**スクリプトを実行する**

1. 管理者としてクライアント コンピューターで PowerShell を開きます。

2. ディレクトリをスクリプト フォルダー (この例では、E:\\IaaSClusterScript) に変更します。

    ```
    cd E:\IaaSClusterScript
```

4. 下のコマンドを実行し、HPC Pack クラスターをデプロイします。この例では、構成ファイルは E:\\HPCDemoConfig.xml にあるものと想定しています。

    ```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
```

HPC Pack デプロイメント スクリプトの実行には少し時間がかかります。スクリプトでの処理の 1 つとして、クラスター証明書がエクスポートされてダウンロードされ、クライアント コンピューター上の現在のユーザーの Documents フォルダーに保存されます。スクリプトでは、次のようなメッセージが生成されます。次の手順では、適切な証明書ストアに証明書をインポートします。

```
You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
 C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_2015070716
2011.cer
```

## 手順 2.Excel ブックをオフロードし、オンプレミスのクライアントから UDF を実行する

### Excel ブックをオフロードする

以下の手順に従って、Azure の HPC Pack クラスターで実行するように Excel ブックをオフロードします。そのためには、Excel 2010 または 2013 がクライアント コンピューターに既にインストールされている必要があります。

1. 手順 1 のいずれかの方法を使用して、Excel 計算ノード イメージを含む HPC Pack クラスターをデプロイします。クラスター証明書ファイル (.cer) およびクラスターのユーザー名とパスワードを取得します。

2. クライアント コンピューターで、クラスター証明書を Cert:\\CurrentUser\\Root にインポートします。

3. Excel がインストールされていることを確認します。次のような内容の Excel.exe.config ファイルを作成し、クライアント コンピューター上の Excel.exe と同じフォルダーに保存します。これにより、HPC Pack 2012 R2 の Excel COM アドインが正常に読み込まれます。

    ```
<?xml version="1.0"?>
<configuration>
  <startup useLegacyV2RuntimeActivationPolicy="true">
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
  </startup>
</configuration>
```
4.	[HPC Pack 2012 R2 Update 2 インストール](http://www.microsoft.com/download/details.aspx?id=47755)の完全版をダウンロードして HPC Pack クライアントをインストールするか、または [HPC Pack 2012 R2 Update 2 クライアント ユーティリティ](https://www.microsoft.com/download/details.aspx?id=47754)とコンピューターに適した Visual C++ 2010 再頒布可能パッケージをダウンロードしてインストールします ([x64](http://www.microsoft.com/download/details.aspx?id=14632)、[x86](https://www.microsoft.com/download/details.aspx?id=5555))。

5.	この例では、[ここ](https://www.microsoft.com/ja-JP/download/details.aspx?id=2939)からダウンロードできる ConvertiblePricing\_Complete.xlsb という名前のサンプル Excel ブックを使用します。

6.	Excel ブックを D:\\Excel\\Run などの作業フォルダーにコピーします。

7.	Excel ブックを開きます。**[開発]** リボンで **[COM アドイン]** をクリックし、次の画面のように、HPC Pack Excel COM アドインが正常に読み込まれていることを確認します。

    ![HPC Pack 用の Excel アドイン][addin]

8.	VBA マクロ HPCControlMacros を Excel で編集し、次のスクリプトで示されているようにコメント行を変更します。実際の環境に合わせて適切な値に置き換えます。

    ![HPC Pack 用の Excel マクロ][macro]

    ```
    'Private Const HPC_ClusterScheduler = "HEADNODE_NAME"
    Private Const HPC_ClusterScheduler = "hpc01.eastus.cloudapp.azure.com"

    'Private Const HPC_NetworkShare = "\\PATH\TO\SHARE\DIRECTORY"
    Private Const HPC_DependFiles = "D:\Excel\Upload\ConvertiblePricing_Complete.xlsb=ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.Initialize ActiveWorkbook
    HPCExcelClient.Initialize ActiveWorkbook, HPC_DependFiles

    'HPCWorkbookPath = HPC_NetworkShare & Application.PathSeparator & ActiveWorkbook.name
    HPCWorkbookPath = "ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath
    HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath, UserName:="hpc\azureuser", Password:="<YourPassword>"
```

9.	VBA マクロの HPC\_DependsFiles 定数で指定されている D:\\Excel\\Upload などの upload ディレクトリに、Excel ブックをコピーします。

10.	ワークシートの **[Cluster]** ボタンをクリックし、Azure IaaS クラスターでブックを実行します。

### Excel UDF を実行する

Excel の UDF を実行するには、前記の手順 1 ～ 3 に従ってクライアント コンピューターを設定します。Excel UDF の場合、Excel アプリケーションが計算ノードにインストールされている必要はないので、手順 1 では、Excel を含む計算ノード イメージではなく、通常の計算ノード イメージを選択できます。

>[AZURE.NOTE]Excel 2010 および 2013 クラスター コネクタのダイアログ ボックスには 34 文字の制限があります。完全なクラスター名が長い場合は (例: hpcexcelhn01.southeastasia.cloudapp.azure.com)、ダイアログ ボックスに収まりません。回避策は、クライアント マシンの SOA Session API に Update 2 QFE KB3085833 ([ここ](http://www.microsoft.com/ja-JP/download/details.aspx?id=48725)よりダウンロード) を適用し、*CCP\_IAASHN* などのマシン全体の変数に、長いクラスター名の値を設定し、*%CCP\_IAASHN%* をダイアログ ボックスにクラスター ヘッドのノード名として入力します。

クラスターが正常にデプロイされた後、引き続き以下の手順に従って、サンプルの組み込み Excel UDF を実行します。Excel UDF をカスタマイズした場合は、[リソース](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx)を参考にして、XLL を作成し、IaaS クラスターにそれをデプロイしてください。

1.	新しい Excel ブックを開きます。**[開発]** リボンで **[アドイン]** をクリックします。次に、ダイアログ ボックスで **[参照]** をクリックし、%CCP\_HOME%Bin\\XLL32 フォルダーに移動して、サンプルの ClusterUDF32.xll を選択します。

    ![UDF を選択する][udf]

2.	**[ファイル]**、**[オプション]**、**[詳細]** の順にクリックします。**[数式]** の **[計算クラスターでユーザー定義の XLL 関数を実行できるようにする]** をオンにします。**[オプション]** をクリックし、**[クラスター ヘッド ノード名]** に完全なクラスター名を入力します (説明したように、この入力ボックスには 34 文字の制限があり、長いクラスター名が入らない可能性があります。IaaS デプロイメント スクリプトでクラスターをデプロイするときに、短い名前を構成できます)。

    ![UDF を構成する][options]

3.	値が =XllGetComputerNameC() であるセルをクリックし、Enter キーを押して、IaaS クラスターで UDF の計算を実行します。この関数は、UDF が実行している計算ノードの名前を取得するだけです。初めて実行したときは、IaaS クラスターに接続するためのユーザー名とパスワードの入力を求める資格情報ダイアログ ボックスが表示されます。

    ![UDF を実行する][run]

    計算するセルが多い場合は、Alt + Shift + Ctrl + F9 キーを押してすべてのセルに対して計算を実行します。

## 手順 3.オンプレミスのクライアントから SOA ワークロードを実行する

一般的な SOA アプリケーションを HPC Pack IaaS クラスターで実行するには、まず、手順 1 のいずれかの方法に従い、汎用計算ノード イメージを使用して IaaS クラスターをデプロイします (計算ノードでは Excel が必要ないため)。次に、以下の手順に従います。

1. クラスター証明書を取得した後、クライアント コンピューターで Cert:\\CurrentUser\\Root にインポートします。

2. SOA クライアント アプリケーションを作成して実行できるように、[HPC Pack 2012 R2 Update 2 SDK](http://www.microsoft.com/download/details.aspx?id=47756) および [HPC Pack 2012 R2 Update 2 クライアント ユーティリティ](https://www.microsoft.com/download/details.aspx?id=47754)をインストールします。

3. HellowWorldR2 [サンプル コード](https://www.microsoft.com/download/details.aspx?id=41633)をダウンロードします。Visual Studio 2010 または 2012 で HelloWorldR2.sln を開きます。

4. 最初に EchoService プロジェクトをビルドし、オンプレミスのクラスターにデプロイするのと同じ方法で、IaaS クラスターにサービスをデプロイします。詳細な手順については、HelloWordR2 の Readme.doc を参照してください。以下で説明するように HellowWorldR2 および他のプロジェクトを変更してビルドし、オンプレミスのクライアント コンピューターから Azure IaaS クラスター上で実行する SOA クライアント アプリケーションを生成します。

### Azure Storage キューありで Http バインディングを使用する

Azure Storage キューで Http バインディングを使用するには、サンプル コードにいくつかの変更を加えます。

* クラスター名を更新します。

    ```
// Before
const string headnode = "[headnode]";
// After e.g.
const string headnode = "hpc01.eastus.cloudapp.azure.com";
or
const string headnode = "hpc01.cloudapp.net";
```

* 必要に応じて、SessionStartInfo の既定の TransportScheme を使用するか、または Http に明示的に設定します。

```
    info.TransportScheme = TransportScheme.Http;
```

* BrokerClient に既定のバインディングを使用します。

    ```
// Before
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session, binding))
// After
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session))
```

    または、basicHttpBinding の使用を明示的に設定します。

    ```
BasicHttpBinding binding = new BasicHttpBinding(BasicHttpSecurityMode.TransportWithMessageCredential);
binding.Security.Message.ClientCredentialType = BasicHttpMessageCredentialType.UserName;    binding.Security.Transport.ClientCredentialType = HttpClientCredentialType.None;
```

* 必要に応じて、SessionStartInfo で UseAzureQueue フラグを true に設定します。これを設定しないと、クラスター名に Azure ドメイン サフィックスが含まれ、TransportScheme が Http の場合は、既定で true に設定されます。

    ```
    info.UseAzureQueue = true;
```

###Azure Storage キューなしで Http バインディングを使用する

そのためには、SessionStartInfo で UseAzureQueue フラグを明示的に false に設定します。

```
    info.UseAzureQueue = false;
```

### NetTcp バインディングを使用する

NetTcp バインディングを使用するための構成は、オンプレミスのクラスターに接続する場合と似ています。ヘッド ノード VM でいくつかのエンドポイントを開く必要があります。Azure 管理ポータルで次のようにします。


1. VM を停止します。

2. セッション用、ブローカー用、ブローカー ワーカー用、データ サービス用に、それぞれ TCP ポート 9090、9087、9091、9094 を追加します。

    ![エンドポイントを構成する][endpoint]

3. VM を起動します。

SOA クライアント アプリケーションでは、IaaS クラスターの完全な名前にヘッド名を変更する以外の変更は不要です。

## 次のステップ

* HPC Pack での Excel ワークロードの実行に関する詳細については、[これらのリソース](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx)をご覧ください。

* HPC Pack での SOA サービスのデプロイと管理について詳しくは、「[サービス](https://technet.microsoft.com/library/ff919412.aspx)」をご覧ください。

<!--Image references-->
[scenario]: ./media/virtual-machines-excel-cluster-hpcpack/scenario.png
[github]: ./media/virtual-machines-excel-cluster-hpcpack/github.png
[template]: ./media/virtual-machines-excel-cluster-hpcpack/template.png
[parameters]: ./media/virtual-machines-excel-cluster-hpcpack/parameters.png
[create]: ./media/virtual-machines-excel-cluster-hpcpack/create.png
[connect]: ./media/virtual-machines-excel-cluster-hpcpack/connect.png
[cert]: ./media/virtual-machines-excel-cluster-hpcpack/cert.png
[addin]: ./media/virtual-machines-excel-cluster-hpcpack/addin.png
[macro]: ./media/virtual-machines-excel-cluster-hpcpack/macro.png
[options]: ./media/virtual-machines-excel-cluster-hpcpack/options.png
[run]: ./media/virtual-machines-excel-cluster-hpcpack/run.png
[endpoint]: ./media/virtual-machines-excel-cluster-hpcpack/endpoint.png
[udf]: ./media/virtual-machines-excel-cluster-hpcpack/udf.png

<!---HONumber=September15_HO1-->