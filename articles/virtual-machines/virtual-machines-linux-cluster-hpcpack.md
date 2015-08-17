<properties
 pageTitle="HPC Pack クラスターで Linux 計算ノードの使用を開始する | Microsoft Azure"
 description="Windows Server を実行するヘッド ノードと Linux 計算ノードを含む HPC Pack クラスターを Azure にデプロイするスクリプトを作成する方法について説明します。"
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="07/27/2015"
 ms.author="danlep"/>

# Azure の HPC Pack クラスターで Linux 計算ノードの使用を開始する

この記事では、Azure PowerShell スクリプトを使用し、Windows Server を実行するヘッド ノードと CentOS Linux ディストリビューションを実行する計算ノードを含む Microsoft HPC Pack クラスターを Azure に設定する方法を紹介します。Linux 計算ノードにデータ ファイルを移動する方法もいくつか紹介します。このクラスターを使用し、Azure で Linux HPC ワークロードを実行できます。

これから作成する HPC Pack クラスターの概要は次の図のようになります。

![Linux ノードを含む HPC クラスター][scenario]

## Linux 計算ノードと共に HPC Pack クラスターをデプロイします。

Microsoft HPC Pack IaaS デプロイメント スクリプト (**New-HpcIaaSCluster.ps1**) を使用して、Azure インフラストラクチャ サービス (IaaS) でクラスター デプロイメントを自動化します。この Azure PowerShell スクリプトは短期間でデプロイするために Azure Marketplace の HPC Pack VM イメージを使用します。包括的な構成パラメーターを備え、簡単かつ柔軟なデプロイメントを可能にします。このスクリプトを使用して、Azure 仮想ネットワーク、ストレージ アカウント、クラウド サービス、ドメイン コントローラー、任意の個別の SQL Server Database サーバー、クラスター ヘッド ノード、計算ノード、ブローカー ノード、Azure PaaS (「バースト」) ノード、Linux 計算ノード ([HPC Pack 2012 R2 Update 2](https://technet.microsoft.com/library/mt269417.aspx) で導入された Linux サポート) をデプロイできます。

HPC Pack クラスター デプロイメント オプションの概要については、「[Getting Started Guide for HPC Pack 2012 R2 and HPC Pack 2012 (HPC Pack 2012 R2 と HPC Pack 2012 の入門ガイド)](https://technet.microsoft.com/library/jj884144.aspx)」をご覧ください。

### 前提条件

* **クライアント コンピューター** - クラスター デプロイメント スクリプトを実行するには、Windows ベースのクライアント コンピューターが必要です。

* **Azure PowerShell** - クライアント コンピューターに [Azure PowerShell をインストールして構成します](../powershell-install-configure.md) (バージョン 0.8.10 以降)。

* **HPC Pack IaaS デプロイメント スクリプト** - [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949) から最新版のスクリプトをダウンロードし、解凍します。`New-HPCIaaSCluster.ps1 –Version` を実行すると、スクリプトのバージョンを確認できます。この記事はバージョン 4.4.0 以降のスクリプトに基づきます。

* **Azure サブスクリプション** - Azure Global または Azure China サービスのサブスクリプションを利用できます。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/)を参照してください。

* **コア クォータ** - 場合によっては、コアのクォータを増やす必要があります。特に、マルチコア VM サイズのクラスター ノードをいくつかデプロイする場合に必要となる可能性があります。この記事の例では、少なくとも 24 コアが必要になります。クォータを増やすには、[オンライン カスタマー サポートに申請](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) (無料) してください。

### 構成ファイルを作成する
HPC Pack IaaS デプロイメント スクリプトは HPC クラスターのインフラストラクチャについて記載された XML 構成ファイルを入力として使用します。1 つのヘッド ノードと 2 つの Linux 計算ノードから構成される小規模のクラスターをデプロイするには、ご利用の環境の値を次のサンプル構成ファイルに代入します。構成ファイルの詳細については、スクリプト フォルダーにある Manual.rtf ファイルまたは[スクリプトに関するドキュメント](https://msdn.microsoft.com/library/azure/dn864734.aspx)をご覧ください。

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>centos7rdmavnetje</VNetName>
    <SubnetName>CentOS7RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS7RDMA-HN</VMName>
    <ServiceName>centos7rdma-je</ServiceName>
<VMSize>A4</VMSize>
<EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS7RDMA-LN%1%</VMNamePattern>
    <ServiceName>centos7rdma-je</ServiceName>
    <VMSize>A7</VMSize>
    <NodeCount>2</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```

次は構成ファイルの構成要素の簡単な説明です。

* **IaaSClusterConfig** - 構成ファイルのルート要素です。

* **サブスクリプション** - HPC Pack クラスターのデプロイに使用される Azure サブスクリプションです。下のコマンドを使用し、Azure サブスクリプションの名前を設定し、それがクライアント コンピューターで一意になるようにします。このサンプルでは、Azure サブスクリプションの「Subscription-1」を使用します。

    ```
    PS > Get-AzureSubscription –SubscriptionName <SubscriptionName>
    ```

    >[AZURE.NOTE]あるいは、サブスクリプション ID を使用し、使用するサブスクリプションを指定できます。スクリプト フォルダーにある Manual.rtf ファイルを参照します。

* **StorageAccount** - HPC Pack クラスターの永続的なデータはすべて、指定のストレージ アカウント (この例では allvhdsje) に保存されます。ストレージ アカウントがない場合、このスクリプトによって、**場所**に指定されているリージョンにストレージ アカウントが作成されます。

* **場所** - HPC Pack クラスターをデプロイする Azure リージョン (この例では東日本) です。

* **VNet** - HPC クラスターが作成される仮想ネットワークとサブネットの設定です。このスクリプトを実行する前に仮想ネットワークとサブネットを自分で作成できます。あるいは、このスクリプトによりアドレス空間が「192.168.0.0/20」の仮想ネットワークと「192.168.0.0/23」のサブネットが作成されます。この例では、このスクリプトにより、仮想ネットワーク centos7rdmavnetje とサブネット CentOS7RDMACluster が作成されます。

* **ドメイン** - HPC Pack クラスターの Active Directory ドメイン設定です。このスクリプトで作成された Windows VM はすべてこのドメインに参加します。現在のところ、このスクリプトでは、ExistingDC、NewDC、 HeadNodeAsDC の 3 つのドメインを選択できます。この例では、ドメイン コントローラーとしてヘッド ノードを構成します。完全修飾ドメイン名は hpc.local です。

* **データベース** - HPC Pack クラスターのデータベース設定です。現在のところ、このスクリプトでは、ExistingDB、NewRemoteDB、LocalDB の 3 つのデータベースを選択できます。この例では、ヘッド ノードでローカル データベースを作成します。

* **HeadNode** - HPC Pack ヘッド ノードの設定です。この例では、クラウド サービス centos7rdma-je で「CentOS7RDMA-HN」という名前の A7 ヘッド ノードを作成します。リモート (ドメイン不参加) クライアントから HPC ジョブを送信するために、このスクリプトにより HPC ジョブ スケジューラ REST API と HPC Web ポータルが有効になります。

* **LinuxComputeNodes** - HPC Pack Linux 計算ノードの設定です。この例では、2 つのサイズの A7 CentOS 7 Linux 計算ノード (CentOS7RDMA-LN1 と CentOS7RDMA-LN2) をクラウド service centos7rdma-je で作成します。

### Linux 計算ノードの追加の考慮事項

* 現在のところ、HPC Pack では計算ノードの Linux ディストリビューションとして Ubuntu Server 14.10、CentOS 6.6、CentOS 7.0、SUSE Linux Enterprise Server 12 に対応しています。

* この記事の例では Azure Marketplace で入手できる特定の CentOS バージョンを使用してクラスターを作成します。他のイメージを使用する場合、**get-azurevmimage** Azure PowerShell コマンドレットを使用して必要なイメージを探します。たとえば、すべての CentOS 7.0 イメージを一覧表示するには、```
    get-azurevmimage | ?{$_.Label -eq "OpenLogic 7.0"}
    ``` コマンドを実行します。

    必要なイメージを見つけ、構成ファイルの **ImageName** 値を置き換えます。

* サイズ A8 と A9 の VM の RDMA 接続をサポートする Linux イメージを利用できます。Linux RDMA ドライバーがインストールされ、有効になっているイメージを指定した場合、HPC Pack IaaS デプロイメント スクリプトによりドライバーがデプロイされます。たとえば、現在の SUSE Linux Enterprise Server 12 に `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708` というイメージ名を指定できます。これは Marketplace のハイパフォーマンス コンピューティング イメージに最適化されています。


* サポートされているイメージから作成した Linux VM で Linux RDMA を有効にして MPI ジョブを実行するには、アプリケーション ニーズに基づいてクラスターをデプロイした後に、Linux ノードに特定の MPI ライブラリをインストールし、構成します。Azure の Linux ノードで RDMA を使用する方法の詳細については、「[MPI アプリケーションを実行するように Linux RDMA クラスターを設定する](virtual-machines-linux-cluster-rdma.md)」をご覧ください。

* RDMA ネットワーク接続がノード間で機能するように、必ず 1 つのサービス内にすべての Linux RDMA ノードをデプロイします。


## HPC Pack IaaS デプロイメント スクリプトを実行する

1. 管理者としてクライアント コンピューターで PowerShell を開きます。

2. ディレクトリをスクリプト フォルダー (この例では、E:\\IaaSClusterScript) に変更します。

    ```
cd E:\IaaSClusterScript
```

3. 下のコマンドを実行し、HPC Pack クラスターをデプロイします。この例では、構成ファイルは E:\\HPCDemoConfig.xml にあるものと想定しています。

    ```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
```

    このスクリプトでは、**-LogFile** パラメーターが指定されていないため、ログ ファイルが自動的に生成されます。ログはリアルタイムで書き込まれませんが、検証とデプロイメントの終わりに回収されます。そのため、スクリプトの実行中に PowerShell プロセスが停止した場合、一部のログが失われます。

    a.上記のコマンドには **AdminPassword** が指定されていないため、ユーザー *MyAdminName* のパスワードを入力するように促されます。

    b.スクリプトにより構成ファイルの検証が開始されます。ネットワーク接続の状態にもよりますが、数秒から数分かかります。

    ![検証][validate]

    c.検証に合格すると、HPC クラスターに作成されるリソースが一覧表示されます。「Y」と入力して続行します。

    ![リソース][resources]

    d.スクリプトにより HPC Pack クラスターのデプロイメントが開始されます。この後はユーザーの介入なしで構成が完了します。これには数分かかる場合があります。

    ![デプロイ][deploy]

4. 構成が完了したら、ヘッド ノードにリモート デスクトップ接続し、HPC Cluster Manager を開き、HPC Pack クラスターの状態を確認します。Windows 計算ノードの場合と同じように Linux 計算ノードを管理し、監視できます。たとえば、ノード管理に Linux ノードが一覧表示されます。

    ![ノード管理][management]

    ヒート マップ ビューにも Linux ノードが表示されます。

    ![ヒート マップ][heatmap]

## Linux ノードのあるクラスターにデータを移動する方法

クラスターの Linux ノードと Windows ヘッド ノードの間でデータを移動するにはいくつかの方法があります。次は 3 つの一般的な方法です。

* **Azure ファイル** - Azure ストレージにデータ ファイルを格納するファイル共有を公開します。デプロイされている仮想ネットワークが異なる場合でも、Windows ノードと Linux ノードの両方でドライブまたはフォルダーとして同時に Azure File 共有をマウントできます。

* **ヘッド ノード SMB 共有** - Linux ノードにヘッド ノードの共有フォルダーをマウントします。

* **ヘッド ノード NFS サーバー** - Windows と Linux の混在環境にファイル共有ソリューションを提供します。

### Azure ファイル

[Azure File](https://azure.microsoft.com/services/storage/files/) サービスは、標準の SMB 2.1 プロトコルを使用してファイル共有を公開します。Azure の VM とクラウド サービスでは、マウントされている共有を介して、アプリケーション コンポーネント間でファイル データを共有できます。また、オンプレミスのアプリケーションでは、File ストレージ API を介して、共有内のファイル データにアクセスできます。詳細については、「[PowerShell と .NET で Azure File ストレージを使用する方法](../storage/storage-dotnet-how-to-use-files.md)」をご覧ください。

Azure File 共有を作成するには、「[Microsoft Azure Files サービスの概要](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)」にある詳しい手順をご覧ください。固定接続を設定するには、「[Microsoft Azure Files への接続の保持](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)」をご覧ください。

この例では、ストレージ アカウント allvhdsje に「rdma」という名前の Azure File 共有を作成します。ヘッド ノードに共有をマウントするには、コマンド ウィンドウを開き、次のコマンドを入力します。

```
> cmdkey /add:allvhdsje.file.core.windows.net /user:allvhdsje /pass:<storageaccountkey>
> net use Z: \\allvhdje.file.core.windows.net\rdma /persistent:yes
```

この例では、allvhdsje はストレージ アカウントの名前、storageaccountkey はストレージ アカウントのキー、rdma は Azure File 共有名になります。Azure File 共有はヘッド ノードの Z: にマウントされます。

Linux ノードに Azure File 共有をマウントするには、ヘッド ノードで **clusrun** コマンドを実行します。**[Clusrun](https://technet.microsoft.com/library/cc947685.aspx)** は、複数のノードで管理タスクを実行するための便利な HPC Pack ツールです (この記事の「[Linux ノードの CLusrun](#CLusrun-for-Linux-nodes)」もご覧ください)。

Windows PowerShell ウィンドウを開き、次のコマンドを入力します。

```
PS > clusrun /nodegroup:LinuxNodes mkdir -p /rdma

PS > clusrun /nodegroup:LinuxNodes mount -t cifs //allvhdsje.file.core.windows.net/rdma /rdma -o vers=2.1`,username=allvhdsje`,password=<storageaccountkey>'`,dir_mode=0777`,file_mode=0777
```

最初のコマンドで「/rdma」という名前のフォルダーが LinuxNodes グループのすべてのノードで作成されます。2 つ目のコマンドにより、ディレクトリとファイル モードのビットが「777」に設定された「/rdma」フォルダーに Azure File 共有 allvhdsjw.file.core.windows.net/rdma がマウントされます。2 つ目のコマンドで、allvhdsje はストレージ アカウント名で、storageaccountkey はストレージ アカウント キーです。

>[AZURE.NOTE]2 つ目のコマンドの「`」記号は PowerShell のエスケープ記号です。「`,」は「,」 (コンマ) がコマンドの一部であることを意味します。

### ヘッド ノード共有

あるいは、Linux ノードにヘッド ノードの共有フォルダーをマウントできます。これはファイルを共有する最も簡単な方法です。ただし、ヘッド ノードとすべての Linux ノードを同じ仮想ネットワークにデプロイする必要があります。手順は次のようになります。

1. ヘッド ノードでフォルダーを作成し、読み書き権限を持つ「全員」と共有します。たとえば、ヘッド ノードの D:\\OpenFOAM を \\CentOS7RDMA-HN\\OpenFOAM として共有します。ここでは CentOS7RDMA-HN がヘッド ノードのホスト名です。

    ![ファイル共有のアクセス許可][fileshareperms]

    ![ファイル共有][filesharing]

2. Windows PowerShell ウィンドウを開き、次のコマンドを実行し、共有フォルダーをマウントします。

```
PS > clusrun /nodegroup:LinuxNodes mkdir -p /openfoam

PS > clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS7RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>,password='<password>’,dir_mode=0777`,file_mode=0777
```

最初のコマンドで「/openfoam」という名前のフォルダーが LinuxNodes グループのすべてのノードで作成されます。2 つ目のコマンドにより、ディレクトリとファイル モードのビットが「777」に設定されたフォルダーに共有フォルダー //CentOS7RDMA-HN/OpenFOAM がマウントされます。コマンドのユーザー名とパスワードはヘッド ノードのユーザーのユーザー名とパスワードにする必要があります。

>[AZURE.NOTE]2 つ目のコマンドの「`」記号は PowerShell のエスケープ記号です。「`,」は「,」 (コンマ) がコマンドの一部であることを意味します。


### NFS サーバー

NFS サービスでは、SMB プロトコルを利用して Windows Server 2012 オペレーティング システムを実行するコンピューターと NFS プロトコルを利用する Linux ベースのコンピューターの間でファイルを共有し、移行できます。NFS サーバーとその他のすべてのノードを同じ仮想ネットワークにデプロイする必要があります。SMB 共有と比較すると、Linux との互換性が良くなります。たとえば、ファイル リンクがサポートされます。

1. NFS サーバーをインストールして設定するには、「[Server for Network File System First Share End-to-End (ネットワーク ファイル システムのエンド ツー エンドの最初の共有のためのサーバー)](http://blogs.technet.com/b/filecab/archive/2012/10/08/server-for-network-file-system-first-share-end-to-end.aspx)」の手順に従います。

    たとえば、次のプロパティを持つ「nfs」という名前の NFS 共有を作成します。

    ![NFS 認証][nfsauth]

    ![NFS 共有のアクセス許可][nfsshare]

    ![NFS NTFS アクセス許可][nfsperm]

    ![NFS 管理のプロパティ][nfsmanage]

2. Windows PowerShell ウィンドウを開き、次のコマンドを実行して NFS 共有をマウントします。

    ```
PS > clusrun /nodegroup:LinuxNodes mkdir -p /nfsshare
PS > clusrun /nodegroup:LinuxNodes mount CentOS7RDMA-HN:/nfs /nfsshared
```

    最初のコマンドで「/nfsshared」という名前のフォルダーが LinuxNodes グループのすべてのノードで作成されます。2 つ目のコマンドにより、NFS 共有 CentOS7RDMA-HN:/nfs がフォルダーにマウントされます。ここで、CentOS7RDMA-HN:/nfs は NFS 共有のリモート パスです。

## ジョブの送信方法
いくつかの方法で HPC Pack クラスターにジョブを送信できます。

* HPC Cluster Manager または HPC Job Manager GUI

* HPC Web ポータル

* REST API

HPC Pack GUI ツールと HPC Web ポータル経由で Azure のクラスターにジョブを送信する方法は Windows 計算ノードの場合と同じです。「[HPC ジョブ マネージャー](https://technet.microsoft.com/library/ff919691.aspx)」および「[オンプレミスのクライアントがジョブを送信できるように構成するための手順](https://msdn.microsoft.com/library/azure/dn689084.aspx)」をご覧ください。

REST API を使用してジョブを送信するには、「[Creating and Submitting Jobs by Using the REST API in Microsoft HPC Pack (Microsoft HPC Pack で REST API を使用したジョブの作成と送信)](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx)」をご覧ください。Linux クライアントからジョブを送信する場合は、[HPC Pack SDK](https://www.microsoft.com/download/details.aspx?id=47756) の Python サンプルを参照してください。

## Linux ノードの clusrun

HPC Pack **clusrun** ツールを使用して、コマンド ウィンドウまたは HPC クラスター マネージャーを介して Linux ノードでコマンドを実行できます。次は一部の例です。

* クラスター内のすべてのノードの現在のユーザー名を表示します。

    ```
> clusrun whoami
```

* linuxnodes グループのすべてのノードに **gdb** デバッガー ツールの ith **yum** をインストールし、10 分後に再起動します。

    ```
> clusrun /nodegroup:linuxnodes yum install gdb –y; shutdown –r 10
```

* クラスターのノードに毎秒 1 ～ 10 を表示するシェル スクリプトを作成し、実行し、各ノードからの出力をすぐに表示します。

    ```
> clusrun /interleaved echo "for i in {1..10}; do echo \\"\$i\\"; sleep 1; done" ^> script.sh; chmod +x script.sh; ./script.sh```

>[AZURE.NOTE]場合によっては、clusrun コマンドに特定のエスケープ文字を使用する必要があります。特殊文字を変換するには、コマンド ウィンドウで「^」を使用し、PowerShell で「`」を使用します。たとえば、PowerShell で、コンマとセミコロンをそれぞれ「`,」と「`,」に変換する必要があります。これらの文字はコマンド ウィンドウでは変換を必要としません。




## 次のステップ

* **clusrun** を使用して、Linux 計算ノードに Linux アプリケーションをインストールし、ジョブを HPC Pack クラスターに送信します。

* クラスターをスケールアップしてノードの数を増やすか、サイズが [A8 または A9](virtual-machines-a8-a9-a10-a11-specs.md) の計算ノードをデプロイして MPI ワークロードを実行します。


<!--Image references-->
[scenario]: ./media/virtual-machines-linux-cluster-hpcpack/scenario.png
[validate]: ./media/virtual-machines-linux-cluster-hpcpack/validate.png
[resources]: ./media/virtual-machines-linux-cluster-hpcpack/resources.png
[deploy]: ./media/virtual-machines-linux-cluster-hpcpack/deploy.png
[management]: ./media/virtual-machines-linux-cluster-hpcpack/management.png
[heatmap]: ./media/virtual-machines-linux-cluster-hpcpack/heatmap.png
[fileshareperms]: ./media/virtual-machines-linux-cluster-hpcpack/fileshare1.png
[filesharing]: ./media/virtual-machines-linux-cluster-hpcpack/fileshare2.png
[nfsauth]: ./media/virtual-machines-linux-cluster-hpcpack/nfsauth.png
[nfsshare]: ./media/virtual-machines-linux-cluster-hpcpack/nfsshare.png
[nfsperm]: ./media/virtual-machines-linux-cluster-hpcpack/nfsperm.png
[nfsmanage]: ./media/virtual-machines-linux-cluster-hpcpack/nfsmanage.png

<!---HONumber=August15_HO6-->