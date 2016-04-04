



クライアント コンピューターで HPC Pack IaaS デプロイ PowerShell スクリプトを実行し、Azure インフラストラクチャ サービス (IaaS) で完全な HPC Pack クラスターをデプロイします。このスクリプトは複数のデプロイ オプションを提供します。また、サポートされている Linux ディストリビューションまたは Windows Server オペレーティング システムを実行しているクラスター コンピューティング ノードを追加できます。

環境や選択肢によっては、このスクリプトにより、Azure virtual network、ストレージ アカウント、クラウド サービス、ドメイン コントローラー、リモートまたはローカルの SQL データベース、ヘッド ノード、ブローカー ノード、コンピューティング ノード、Azure クラウド サービス (「バースト」または PaaS) ノードを含む、すべてのクラスター インフラストラクチャを作成できます。あるいは、このスクリプトにより既存の Azure インフラストラクチャを利用し、HPC クラスター ヘッド ノード、ブローカー ノード、コンピューティング ノード、Azure バースト ノードを作成できます。


HPC Pack クラスターの計画に関する背景情報については、HPC Pack TechNet ライブラリの「[製品の評価と計画](https://technet.microsoft.com/library/jj899596.aspx)」と「[はじめに](https://technet.microsoft.com/library/jj899590.aspx)」コンテンツを参照してください。

>[AZURE.NOTE]Azure リソース マネージャーのテンプレートを使用して HPC Pack クラスターをデプロイすることもできます。サンプルが必要な場合、「[HPC クラスターの作成](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/)」、「[カスタム コンピューティング ノード イメージで HPC クラスターを作成する](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/)」、「[Linux コンピューティング ノードで HPC クラスターを作成する](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)」を参照してください。

## 前提条件

* **Azure サブスクリプション** - Azure Global または Azure China サービスのサブスクリプションを利用できます。ご利用のサブスクリプションの制限によりデプロイ可能なクラスター ノードの数と種類が変わります。詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)」をご覧ください。


* **Azure PowerShell 0.8.7 以降がインストールされ、構成されている Windows クライアント コンピューター** - 「[Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)」をご覧ください。このスクリプトは Azure サービス管理で実行されます。


* **HPC Pack IaaS デプロイ スクリプト** - [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949) から最新版のスクリプトをダウンロードし、解凍します。`New-HPCIaaSCluster.ps1 –Version` を実行して、スクリプトのバージョンを確認します。この記事はバージョン 4.4.0 のスクリプトに基づきます。

* **スクリプトの設定ファイル** - HPC クラスターを設定するためにスクリプトにより使用される XML ファイルを作成する必要があります。情報とサンプルについては、この記事の後半のセクションを参照してください。


## 構文

```
New-HPCIaaSCluster.ps1 [-ConfigFile] <String> [-AdminUserName]<String> [[-AdminPassword] <String>] [[-HPCImageName] <String>] [[-LogFile] <String>] [-Force] [-NoCleanOnFailure] [-PSSessionSkipCACheck] [<CommonParameters>]
```
>[AZURE.NOTE]このスクリプトは管理者として実行する必要があります。

### パラメーター

* **ConfigFile** - HPC クラスターについて説明する構成ファイルのファイル パスを指定します。詳細については、このトピックの「[構成ファイル](#Configuration-file)」か、スクリプトが含まれているフォルダーの Manual.rtf ファイルを参照してください。

* **AdminUserName** - ユーザー名を指定します。ドメイン フォレストがこのスクリプトにより作成される場合、これがすべての VM のローカル管理者ユーザー名となり、また、ドメイン管理者名となります。ドメイン フォレストが既に存在する場合、HPC Pack をインストールするために、ドメイン ユーザーがローカル管理者ユーザー名として指定されます。

* **AdminPassword** - 管理者のパスワードを指定します。コマンド ラインで指定されない場合、パスワードを入力するように求められます。

* **HPCImageName** (省略可能) - HPC クラスターのデプロイに使用される HPC Pack VM イメージ名が指定されます。これは Azure Marketplace から Microsoft が提供する HPC Pack イメージにする必要があります。指定されていない場合 (ほとんどの場合に推奨)、最近公開された HPC Pack イメージが選択されます。

    >[AZURE.NOTE] 有効な HPC Pack イメージを指定しない場合、デプロイは失敗します。

* **LogFile** (省略可能) - デプロイ ログ ファイルのパスを指定します。指定しない場合、スクリプトを実行しているコンピューターの一時ディレクトリにログ ファイルが作成されます。

* **Force** (省略可能) - すべての確認プロンプトを非表示にします。

* **NoCleanOnFailure** (省略可能) - デプロイできなかった Azure VM を削除しません。デプロイできなかった VM を最初に手動で削除してから、スクリプトを再実行し、デプロイを続行します。そうしないと、デプロイは失敗します。

* **PSSessionSkipCACheck** (省略可能) - このスクリプトで VM がデプロイされたすべてのクラウド サービスに対して、Azure によって自己署名証明書が自動的に生成されます。クラウド サービスのすべての VM がこの証明書を既定の Windows リモート管理 (WinRM) 証明書として使用します。これらの Azure VM で HPC 機能をデプロイするために、スクリプトによって、これらの証明書がローカル コンピューター/クライアント コンピューターの信頼されたルート証明機関ストアに既定で一時的にインストールされ、スクリプトの実行中、"信頼できない CA" セキュリティ エラーが抑制されます。スクリプトが完了すると、証明書は削除されます。このパラメーターが指定されている場合、証明書はクライアント コンピューターにインストールされず、セキュリティ警告が非表示になります。

    >[AZURE.IMPORTANT] 本稼働デプロイの場合、このパラメーターは推奨されません。

### 例

次の例では、*MyConfigFile.xml* という構成ファイルを使用して新しい HPC Pack クラスターを作成し、クラスターをインストールするための管理者資格情報を指定します。

```
New-HPCIaaSCluster.ps1 –ConfigFile MyConfigFile.xml -AdminUserName <username> –AdminPassword <password>
```

### 追加の考慮事項

* このスクリプトは Azure Marketplace の HPC Pack VM イメージを使用し、クラスター ヘッド ノードを作成します。最新のイメージは、HPC Pack 2012 R2 Update 3 がインストールされた Windows Server 2012 R2 Datacenter に基づいています。

* このスクリプトでは、任意で、HPC Pack Web ポータルまたは HPC Pack REST API を通してジョブ送信を有効にできます。

* このスクリプトでは、追加ソフトウェアをインストールするか、その他の設定を構成する場合、カスタムの構成前スクリプトと構成後スクリプトを任意で実行できます。


## 構成ファイル

デプロイ スクリプトの構成ファイルは XML ファイルです。「HPCIaaSClusterConfig.xsd」という名前のスキーマ ファイルは HPC Pack IaaS デプロイ スクリプト フォルダーにあります。**IaaSClusterConfig** は構成ファイルのルート要素であり、デプロイ スクリプト フォルダーの Manual.rtf ファイルに詳細がある子要素が含まれています。さまざまなシナリオのサンプル ファイルが必要であれば、この記事の「[サンプル構成ファイル](#Example-configuration-files)」を参照してください。

## サンプル構成ファイル

### 例 1

次の構成ファイルでは、既存のドメイン フォレストで HPC Pack クラスターが展開されます。このクラスターにはローカル データベースを持つヘッド ノードが 1 つあり、BGInfo VM 拡張機能が適用されたコンピューティング ノードが 12 あります。Windows 更新プログラムの自動インストールはドメイン フォレストのすべての VM で無効です。すべてのクラウド サービスは東アジアの場所に直接作成されます。コンピューティング ノードは、3 つのクラウド サービスと 3 つのストレージ アカウントで作成されます (_MyHPCCNService01_ と _mycnstorage01_ の _MyHPCCN-0001_ ～ _MyHPCCN-0005_、_MyHPCCNService02_ と _mycnstorage02_ の _MyHPCCN-0006_ ～ _MyHPCCN0010_、_MyHPCCNService03_ と _mycnstorage03_ の _MyHPCCN-0011_ ～ _MyHPCCN-0012_)。コンピューティング ノードはコンピューティング ノードからキャプチャされた既存のプライベート イメージから作成されます。自動拡大縮小サービスは既定の拡大縮小間隔で有効になっています。

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
      </DomainController>
     <NoWindowsAutoUpdate />
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
  </Certificates>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0001%</VMNamePattern>
<ServiceNamePattern>MyHPCCNService%01%</ServiceNamePattern>
<MaxNodeCountPerService>5</MaxNodeCountPerService>
<StorageAccountNamePattern>mycnstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>12</NodeCount>
    <ImageName HPCPackInstalled=”true”>MyHPCComputeNodeImage</ImageName>
    <VMExtensions>
       <VMExtension>
          <ExtensionName>BGInfo</ExtensionName>
          <Publisher>Microsoft.Compute</Publisher>
          <Version>1.*</Version>
       </VMExtension>
    </VMExtensions>
  </ComputeNodes>
  <AutoGrowShrink>
    <CertificateId>1</CertificateId>
  </AutoGrowShrink>
</IaaSClusterConfig>

```

### 例 2

次の構成ファイルでは、既存のドメイン フォレストで HPC Pack クラスターが展開されます。このクラスターには、ヘッド ノードが 1 つ、500GB データ ディスクのデータベース サーバーが 1 つ、Windows Server 2012 R2 オペレーティング システムを実行するブローカー ノードが 2 つ、Windows Server 2012 R2 オペレーティング システムを実行するコンピューティング ノードが 5 つ含まれています。クラウド サービス MyHPCCNService は、アフィニティ グループ *MyIBAffinityGroup* で作成されます。その他のクラウド サービスはすべて、アフィニティ グループ *MyAffinityGroup* で作成されます。HPC ジョブ スケジューラ REST API と HPC Web ポータルはヘッド ノードで有効になっています。

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>    
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>NewRemoteDB</DBOption>
    <DBVersion>SQLServer2014_Enterprise</DBVersion>
    <DBServer>
      <VMName>MyDBServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>ExtraLarge</VMSize>
      <DataDiskSizeInGB>500</DataDiskSizeInGB>
    </DBServer>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>A8</VMSize>
<NodeCount>5</NodeCount>
<AffinityGroup>MyIBAffinityGroup</AffinityGroup>
  </ComputeNodes>
  <BrokerNodes>
    <VMNamePattern>MyHPCBN-%0000%</VMNamePattern>
    <ServiceName>MyHPCBNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>2</NodeCount>
  </BrokerNodes>
</IaaSClusterConfig>
```

### 例 3

次の構成ファイルでは、新しいドメイン フォレストと、が作成され、ローカル データベースを持つヘッド ノードを 1 つと Linux コンピューティング ノードを 20 を含む HPC Pack クラスターがデプロイされます。すべてのクラウド サービスは東アジアの場所に直接作成されます。Linux コンピューティング ノードは、4 つのクラウド サービスと 4 つのストレージ アカウントで作成されます (_MyLnxCNService01_ と _mylnxstorage01_ の _MyLnxCN-0001_ ～ _MyLnxCN-0005_、_MyLnxCNService02_ と _mylnxstorage02_ の _MyLnxCN-0006_ ～ _MyLnxCN-0010_、_MyLnxCNService03_ と _mylnxstorage03_ の _MyLnxCN-0011_ ～ _MyLnxCN-0015_、_MyLnxCNService04_ と _mylnxstorage04_ の _MyLnxCN-0016_ ～ _MyLnxCN-0020_)。コンピューティング ノードは OpenLogic CentOS バージョン 7.0 Linux イメージから作成されます。

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
    </DomainController>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>MyLnxCN-%0001%</VMNamePattern>
    <ServiceNamePattern>MyLnxCNService%01%</ServiceNamePattern>
    <MaxNodeCountPerService>5</MaxNodeCountPerService>
    <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>20</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
    <SSHKeyPairForRoot>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </SSHKeyPairForRoot>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```


### 例 4

次の構成ファイルでは、ローカル データベースを持つヘッド ノードを 1 つと Windows Server 2008 R2 オペレーティング システムを実行しているコンピューティング ノードを 5 つ含む HPC Pack クラスターがデプロイされます。すべてのクラウド サービスは東アジアの場所に直接作成されます。ヘッド ノードはドメイン フォレストのドメイン コントローラーとして機能します。

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionId>08701940-C02E-452F-B0B1-39D50119F267</SubscriptionId>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%1000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>5</NodeCount>
    <OSVersion>WindowsServer2008R2</OSVersion>
  </ComputeNodes>
</IaaSClusterConfig>
```

### 例 5

次の構成ファイルでは、既存のドメイン フォレストで HPC Pack クラスターが展開されます。このクラスターには、ローカル データベースを持つヘッド ノードが 1 つ含まれます。2 つの Azure ノード テンプレートが作成されます。Azure ノード テンプレート _AzureTemplate1_ に対して、中サイズの Azure ノードが 3 つ作成されます。ヘッド ノードが構成されると、スクリプト ファイルがヘッド ノードで実行されます。

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
<VMSize>ExtraLarge</VMSize>
    <PostConfigScript>c:\MyHNPostActions.ps1</PostConfigScript>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
    <Certificate>
      <Id>2</Id>
      <PfxFile>d:\mytestcert2.pfx</PfxFile>
    </Certificate>    
  </Certificates>
  <AzureBurst>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate1</TemplateName>
      <SubscriptionId>bb9252ba-831f-4c9d-ae14-9a38e6da8ee4</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc1</ServiceName>
      <StorageAccount>myteststorage1</StorageAccount>
      <NodeCount>3</NodeCount>
      <RoleSize>Medium</RoleSize>
    </AzureNodeTemplate>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate2</TemplateName>
      <SubscriptionId>ad4b9f9f-05f2-4c74-a83f-f2eb73000e0b</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc2</ServiceName>
      <StorageAccount>myteststorage2</StorageAccount>
      <Proxy>
        <UsesStaticProxyCount>false</UsesStaticProxyCount>     
        <ProxyRatio>100</ProxyRatio>
        <ProxyRatioBase>400</ProxyRatioBase>
      </Proxy>
      <OSVersion>WindowsServer2012</OSVersion>
    </AzureNodeTemplate>
  </AzureBurst>
</IaaSClusterConfig>
```
## 既知の問題


* **「VNet が存在しない」エラー** - 1 つサブスクリプションの下で HPC Pack IaaS デプロイ スクリプトを実行して Azure で複数のクラスターを同時にデプロイするとき、「VNet *VNet\_Name* が存在しない」というエラーで 1 つまたは複数のデプロイが失敗することがあります。このエラーが発生した場合、失敗したデプロイに対してスクリプトを再実行してください。

* **Azure virtual network からインターネットにアクセスできない** - デプロイ スクリプトを利用し、新しいドメイン コントローラーを含む HPC Pack クラスターを作成する場合、あるいは手動で VM をドメイン コントローラーに昇格する場合、Azure virtual network の VM をインターネットに接続できないことがあります。この問題は、フォワーダー DNS サーバーがドメイン コントローラーで自動的に構成されるとき、このフォワーダー DNS サーバーが適切に解決しない場合に発生することがあります。

    この問題を回避するには、ドメイン コントローラーにログオンし、フォワーダー構成設定を削除するか、有効なフォワーダー DNS サーバーを構成します。これを行うには、サーバー マネージャーで、**[ツール]** > **[DNS]** をクリックして DNS マネージャーを開き、**[フォワーダー]** をダブルクリックします。

* **サイズ A8 または A9 の VM から RDMA ネットワークにアクセスできない** - デプロイ スクリプトを使用し、サイズ A8 または A9 の Windows Server コンピューティング ノードまたはブローカー ノード VM を追加する場合、これらの VM を RDMA アプリケーション ネットワークに接続できないことがあります。この問題が発生する原因の 1 つとして、サイズ A8 または A9 の VM がクラスターに追加されたとき、HpcVmDrivers 拡張機能が適切にインストールされていないことがあります。たとえば、拡張機能のインストールが途中で止まります。

    この問題を回避するには、最初に VM の拡張機能の状態を確認します。拡張機能が適切にインストールされていない場合、HPC クラスターからノードを削除し、ノードを再度追加してみます。たとえば、ヘッド ノードで Add-HpcIaaSNode.ps1 スクリプトを実行し、コンピューティング ノード VM を追加できます。

<!---HONumber=AcomDC_0323_2016-->