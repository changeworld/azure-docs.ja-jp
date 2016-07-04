<properties
   pageTitle="Azure Service Fabric クラスターをオンプレミスまたは任意のクラウドに作成する | Microsoft Azure"
   description="Windows Server が実行されている任意のマシン (物理コンピューターまたは仮想マシン) に Azure Service Fabric クラスターを作成する方法について説明します。クラスターの作成先は、オンプレミスでも、任意のクラウドでもかまいません。"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/21/2016"
   ms.author="chackdan"/>


# Azure Service Fabric クラスターをオンプレミスまたはクラウドに作成する

Azure Service Fabric クラスターは、Windows Server を実行するあらゆる仮想マシンまたはコンピューター上に作成できます。つまり相互に接続された一連の Windows Server コンピューターが存在する環境さえあれば、オンプレミスであれ、クラウドであれ、Service Fabric アプリケーションをデプロイして実行できるということです。Service Fabric には、Service Fabric クラスターを作成するためのセットアップ パッケージが用意されています。

この記事では、Service Fabric のスタンドアロン パッケージを使用して、オンプレミスにクラスターを作成する手順について説明していますが、他の環境 (他のクラウドなど) にも簡単に応用することができます。

>[AZURE.NOTE] このスタンドアロン パッケージは、現在プレビュー段階です。EULA のコピーがすぐに必要という場合は、[こちらをクリック](http://go.microsoft.com/fwlink/?LinkID=733084)してダウンロードしてください。

<a id="downloadpackage"></a>
## Service Fabric スタンドアロン パッケージのダウンロード


[Service Fabric for Windows Server 2012 R2 用のスタンドアロン パッケージをダウンロード](http://go.microsoft.com/fwlink/?LinkId=730690)してください。*Microsoft.Azure.ServiceFabric.WindowsServer.&lt;バージョン&gt;.zip* という名前です。

ダウンロード パッケージには、次のファイルが含まれています。

|**ファイル名**|**簡単な説明**|
|-----------------------|--------------------------|
|MicrosoftAzureServiceFabric.cab|クラスター内の各マシンにデプロイされるバイナリを含んだ CAB ファイル。|
|ClusterConfig.Unsecure.DevCluster.JSON|クラスター構成ファイルのサンプル。クラスターに属している各マシンの情報を含め、安全でない、3 つのノードと 1 つの VM/マシンを持つ開発クラスターのすべての設定が含まれています。 |
|ClusterConfig.Unsecure.MultiMachine.JSON|クラスター構成ファイルのサンプル。安全でない複数の VM/マシンを持つクラスターに属している各マシンの情報を含め、クラスターのすべての設定が含まれています。|
|ClusterConfig.Windows.DevCluster.JSON|クラスター構成ファイルのサンプル。クラスターに属している各マシンの情報を含め、安全な、3 つのノードと 1 つの VM/マシンを持つ開発クラスターのすべての設定が含まれています。クラスターは [Windows ID](https://msdn.microsoft.com/library/ff649396.aspx) を使用して保護されます。|
|ClusterConfig.Windows.MultiMachine.JSON|クラスター構成ファイルのサンプル。安全な複数の VM/マシンを持つクラスターに属している各マシンの情報を含め、安全なクラスターのすべての設定が含まれています。クラスターは [Windows ID](https://msdn.microsoft.com/library/ff649396.aspx) を使用して保護されます。|
|ClusterConfig.x509.DevCluster.JSON|クラスター構成ファイルのサンプル。クラスターに属している各マシンの情報を含め、安全な、3 つのノードと 1 つの VM/マシンを持つ開発クラスターのすべての設定が含まれています。クラスターは Windows x509 証明書を使用して保護されます。|
|ClusterConfig.x509.MultiMachine.JSON|クラスター構成ファイルのサンプル。安全な複数の VM/マシンを持つクラスターに属している各マシンの情報を含め、安全なクラスターのすべての設定が含まれています。クラスターは x509 証明書を使用して保護されます。|
|EULA.txt|Microsoft Azure Service Fabric の Service Fabric スタンドアロン パッケージの使用に関するライセンス条項。EULA のコピーをすぐにダウンロードする必要がある場合は、[こちらをクリック](http://go.microsoft.com/fwlink/?LinkID=733084)してください。|
|Readme.txt|リリース ノートや基本的なインストール手順へのリンク。このページに記載されているインストラクションの内容も一部含まれています。|
|CreateServiceFabricCluster.ps1|ClusterConfig.JSON ファイル内の設定を使用してクラスターを作成する PowerShell スクリプト。|
|RemoveServiceFabricCluster.ps1|ClusterConfig.JSON 内の設定を使用してクラスターを削除する PowerShell スクリプト。|
|AddNode.ps1|ノードを既存のクラスターに追加するための PowerShell スクリプト|
|RemoveNode.ps1|ノードをクラスターから削除するための PowerShell スクリプト|


## クラスターのデプロイ計画と準備
以降の手順は、クラスターを作成する前に行う必要があります。

### 手順 1: クラスターのインフラストラクチャを検討する
ここでの Service Fabric クラスターの作成先は自分が所有するマシンです。そこで、クラスターがその機能を持続できる障害の種類を決めます。たとえば電気系統やインターネット接続を、それらのマシン専用に確保する必要はあるでしょうか。 またマシンの物理的なセキュリティにも気を配る必要があります。物理的な配置場所はどこか、だれが利用するのかを考えます。 これらの点を判断したら、各種の障害ドメイン (後述) にマシンを論理的に対応付けます。運用環境クラスターのインフラストラクチャ計画は、テスト環境のクラスターと比べて、はるかに複雑です。

### 手順 2: マシンの前提条件を満たすための準備をする
クラスターに追加する各マシンの前提条件は次のとおりです。

- 2 GB 以上のメモリを推奨
- ネットワーク接続 – マシンの接続先となるネットワークがセキュリティで保護されていることを確認します。
- Windows Server 2012 R2 または Windows Server 2012 (後者については KB2858668 がインストールされていること)。
- .NET Framework 4.5.1 以降 (フル インストール)
- Windows PowerShell 3.0
- クラスターのデプロイと構成を行うクラスター アドミニストレーターには、個々のコンピューターに対する管理者特権が必要です。
- RemoteRegistry サービスは、すべてのマシンで実行する必要があります。

### 手順 3: クラスターの初期サイズを決める
個々のノードは完全な Service Fabric スタックから成っており、それぞれが個別に Service Fabric クラスターに属しています。標準的な Service Fabric デプロイでは、OS インスタンス (物理または仮想) ごとにノードが 1 つ存在します。クラスターのサイズはビジネス ニーズに左右されるものの、3 ノード (マシン/VM) という最低限のクラスター サイズは満たす必要があります。開発目的では、特定のマシン 1 台に複数のノードを割り当てることができます。運用環境の場合、Service Fabric がサポートするノードは、物理コンピューターまたは仮想マシン 1 台につき 1 つだけです。

### 手順 4: 障害ドメインとアップグレード ドメインの数を決める
**障害ドメイン (FD)** は障害の物理的単位であり、データ センター内の物理インフラストラクチャと直接関連付けられます。障害ドメインは、同じ単一障害点を持ったハードウェア コンポーネント (コンピューター、スイッチなど) から成ります。障害ドメインとラックとの間に 1:1 の対応はありませんが、大まかにいえば、それぞれのラックが 1 つの障害ドメインと考えてかまいません。クラスターに含めるノードを検討するときは、3 つ以上の障害ドメインにノードを分散することを強くお勧めします。

障害ドメインの名前は、*ClusterConfig.JSON* に障害ドメインを指定するときに選びます。Service Fabric では階層形式の障害ドメインがサポートされているため、実際のインフラストラクチャのトポロジをそこに反映させることが可能です。その例を次に示します。

- "faultDomain": "fd:/Room1/Rack1/Machine1"
- "faultDomain": "fd:/FD1"
- "faultDomain": "fd:/Room1/Rack1/PDU1/M1"


**アップグレード ドメイン (UD)** は、ノードの論理ユニットです。Service Fabric によってオーケストレーションされるアップグレード (アプリケーション アップグレードまたはクラスター アップグレード) の間、アップグレード ドメイン内のノードはすべて中断されてアップグレードが実行されますが、他のアップグレード ドメイン内のノードはその間も要求に応答し続けることができます。マシンに対するファームウェア アップグレードでは、アップグレード ドメインが考慮されません。アップグレードは 1 台ずつ行う必要があります。

障害ドメインは計画の範囲外の障害の単位、アップグレード ドメインは計画の範囲内のメンテナンスの単位であると考えると理解しやすいでしょう。

アップグレード ドメインの名前は、*ClusterConfig.JSON** にアップグレード ドメインを指定するときに選びます。その例を次に示します。

- "upgradeDomain": "UD0"
- "upgradeDomain": "UD1A"
- "upgradeDomain": "DomainRed"
- "upgradeDomain": "Blue"

### 手順 5: Service Fabric for Windows Server のスタンドアロン パッケージをダウンロードする
[Service Fabric for Windows Server のスタンドアロン パッケージをダウンロード](http://go.microsoft.com/fwlink/?LinkId=730690)し、クラスターに属していないデプロイ用のマシンか、これからクラスターに追加するマシンのいずれかにパッケージ ファイルを解凍します。

<a id="createcluster"></a>
## クラスターの作成

前出の計画と準備のセクションで説明した手順を実行したら、いよいよクラスターを作成することになります。

### 手順 1: クラスターの構成を変更する
ダウンロードしたパッケージから *ClusterConfig.JSON* を開きます。ご利用のエディターを使って以下の設定を変更してください。

|**構成設定**|**説明**|
|-----------------------|--------------------------|
|NodeTypes|クラスター ノードは、ノードのタイプでグループ分けすることができます。クラスターには少なくとも 1 つの NodeType が必要です。グループ内のすべてのノードは、次の特性を共有します。<br> *Name* - ノードのタイプの名前です。<br>*EndPoints* - ノード タイプには、さまざまな名前付きエンド ポイント (ポート) が関連付けられます。このマニフェスト内の他のポート番号と競合せず、なおかつ同じマシン/VM 上の他のプログラムでまだ使われていなければ、どのポート番号でも使用できます。<br> *PlacementProperties* - このノード タイプの特性を表します。この特性が、後でシステム サービスや独自のサービスを配置するうえでの制約としての性質を帯びます。これらの特性は、特定のノードを補足するメタデータとして、ユーザー定義のキー/値のペアで記述されます。ノードのプロパティとしては、たとえばハード ドライブやグラフィック カードの有無やハード ディスク ドライブ内のスピンドル数、コア数といった物理的な特性があります。<br> *Capacities* - ノード容量は、特定のノードで使用できるように確保されている特定のリソースの名前と量を定義します。たとえば、"MemoryInMb" と呼ばれるメトリックに対して既定で 2,048 MB のメモリを使用できるようにノードの容量を定義できます。実行時にはこの容量に基づき、特定のリソース量を必要とするサービスが確実に、そのリソースに空きのあるノードに配置されます。|
|Nodes|クラスターに参加するノードごとの詳しい情報 (ノード タイプとノード名、ノードの IP アドレス、障害ドメイン、アップグレード ドメイン)。ここには、クラスターの作成先となるマシンとその IP アドレスを列挙する必要があります。<br> すべてのノードに同じ IP アドレスを使用した場合、ワンボックス クラスターが作成されます。ワンボックス クラスターはテストに使用できます。運用環境のワークロードをデプロイする用途には使用しないでください。|

### 手順 2: クラスター スクリプトを作成する
JSON ファイル内のクラスター構成に変更を加え、すべてのノード情報を追加したら、パッケージ フォルダーにあるクラスター作成スクリプトを PowerShell で実行します。構成ファイルのパスと、そのパッケージ ルートの場所を引数として指定してください。

このスクリプトは、クラスター構成ファイルにノードとして列挙されているすべてのマシンに管理アクセスできれば、どのマシンでも実行できます。このスクリプトを実行するマシンが、クラスターに属していなくてもかまいません。

```
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.JSON -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
```

>[AZURE.NOTE] デプロイ ログは、CreateServiceFabricCluster PowerShell を実行した VM/マシンのローカルで利用できます。デプロイ ログは、PS コマンドを実行したフォルダー内の "DeploymentTraces" という名前のサブフォルダーにあります。

## すべてのノードを Service Fabric クラスターに追加する 

1. クラスターに追加する VM/マシンを準備します (「クラスターのデプロイ計画と準備」セクションの手順 2. を参照)。 
2. この VM/マシンを追加する障害ドメインとアップグレード ドメインについて計画します。
3. [Service Fabric for Windows Server のスタンドアロン パッケージをダウンロード](http://go.microsoft.com/fwlink/?LinkId=730690)し、クラスターに追加する予定の VM/マシンにパッケージを解凍します。 
4. Powershell 管理プロンプトを開き、解凍したパッケージのある場所に移動します。
5. AddNode.PS1 を実行します。

```
.\AddNode.ps1 -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClusterConnectionEndPoint 182.17.34.52:19000 -UpgradeDomain UD1 -FaultDomain FD1
```

## Service Fabric クラスターからノードを削除する 

1. クラスターから削除する VM/マシンに TS を実行します。
2. Powershell 管理プロンプトを開き、解凍したパッケージのある場所に移動します。
5. RemoveNode.PS1 を実行します。

```
.\RemoveNode.ps1 -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -ExistingClusterConnectionEndPoint 182.17.34.52:19000
```

## Service Fabric クラスターを削除する 
1. クラスターに含まれる VM/マシンのいずれかに TS を実行します。
2. Powershell 管理プロンプトを開き、解凍したパッケージのある場所に移動します。
5. RemoveNode.PS1 を実行します。

```
.\RemoveNode.ps1 -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -ExistingClusterConnectionEndPoint 182.17.34.52:19000
```


## 次のステップ
- [クラスターのセキュリティについて](service-fabric-cluster-security.md)
- [Service Fabric SDK と概要](service-fabric-get-started.md)
- [Visual Studio での Service Fabric アプリケーションの管理](service-fabric-manage-application-in-visual-studio.md)。
- [スタンドアロン クラスター作成機能の概要および Azure によって管理されたクラスターとの比較](service-fabric-deploy-anywhere.md)

<!---HONumber=AcomDC_0622_2016-->