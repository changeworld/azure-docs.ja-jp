<properties
   pageTitle="スタンドアロン Azure Service Fabric クラスターを作成および管理する | Microsoft Azure"
   description="Windows Server が実行されている任意のマシン (物理コンピューターまたは仮想マシン) で Azure Service Fabric クラスターを作成および管理する方法について説明します。クラスターの作成先は、オンプレミスでも、任意のクラウドでもかまいません。"
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
   ms.date="09/15/2016"
   ms.author="dkshir;chackdan"/>


# Windows Server 上で実行するクラスターを作成および管理する

Azure Service Fabric クラスターは、Windows Server を実行するあらゆる仮想マシンまたはコンピューター上に作成できます。つまり相互に接続された一連の Windows Server コンピューターを含む環境さえあれば、オンプレミスであれ、クラウド プロバイダーであれ、Service Fabric アプリケーションをデプロイして実行できるということです。Service Fabric には、Service Fabric クラスターを作成するためのセットアップ パッケージ (スタンドアロン Windows Server パッケージ) が用意されています。

この記事では、Service Fabric のスタンドアロン パッケージを使用して、オンプレミスにクラスターを作成する手順について説明していますが、他の環境 (他のクラウドなど) にも簡単に応用することができます。

>[AZURE.NOTE] このスタンドアロン Windows Server パッケージには、商用では使用できない、プレビュー段階の機能が含まれている可能性があります。プレビュー機能の一覧を表示するには、このドキュメントを最後までスクロールします。EULA のコピーをすぐにダウンロードする必要がある場合は、[こちらをクリック](http://go.microsoft.com/fwlink/?LinkID=733084)してください。

<a id="downloadpackage"></a>
## Service Fabric スタンドアロン パッケージのダウンロード


[Windows Server 2012 R2 以降用の Service Fabric のスタンドアロン パッケージをダウンロード](http://go.microsoft.com/fwlink/?LinkId=730690)してください。これは "*Microsoft.Azure.ServiceFabric.WindowsServer.&lt;バージョン&gt;.zip*" という名前です。

>[AZURE.NOTE] Internet Explorer または Microsoft Edge ブラウザーを使用してこのパッケージをダウンロードする場合は、[http://download.microsoft.com](http://download.microsoft.com) サイトをイントラネット内の信頼済みサイトに追加する必要があります。これにより、zip のダウンロード時に Zones タグがファイルに書き込まれなくなります

 ![TustedZone][TrustedZone]

ダウンロード パッケージには、次のファイルが含まれています。

|**ファイル名**|**簡単な説明**|
|-----------------------|--------------------------|
|MicrosoftAzureServiceFabric.cab|クラスター内の各マシンにデプロイされるバイナリを含んだ CAB ファイル。|
|ClusterConfig.Unsecure.DevCluster.json|クラスター構成サンプル ファイル。3 つのノードと 1 つの VM/マシンを持つ、セキュリティで保護されていない開発クラスターの設定 (クラスター内の各ノードの情報を含む) が含まれています。 |
|ClusterConfig.Unsecure.MultiMachine.json|クラスター構成サンプル ファイル。複数の VM/マシンを持つ、セキュリティで保護されていないクラスターの設定 (クラスター内の各マシンの情報を含む) が含まれています。|
|ClusterConfig.Windows.DevCluster.json|クラスター構成サンプル ファイル。3 つのノードと 1 つの VM/マシンを持つ、セキュリティ保護された開発クラスターのすべての設定 (クラスター内の各ノードの情報を含む) が含まれています。クラスターは [Windows ID](https://msdn.microsoft.com/library/ff649396.aspx) を使用して保護されます。|
|ClusterConfig.Windows.MultiMachine.json|クラスター構成サンプル ファイル。複数の VM/マシンを持つ、Windows セキュリティを使用するセキュリティ保護されたクラスターのすべての設定 (セキュリティ保護されたクラスター内の各マシンの情報を含む) が含まれています。クラスターは [Windows ID](https://msdn.microsoft.com/library/ff649396.aspx) を使用して保護されます。|
|ClusterConfig.x509.DevCluster.json|クラスター構成サンプル ファイル。3 つのノードと 1 つの VM/マシンを持つ、セキュリティ保護された開発クラスターのすべての設定 (クラスターの各ノードの情報を含む) が含まれています。クラスターは x509 証明書を使用して保護されます。|
|ClusterConfig.x509.MultiMachine.json|クラスター構成サンプル ファイル。複数の VM/マシンを持つ、セキュリティ保護されたクラスターのすべての設定 (セキュリティ保護されたクラスター内の各ノードの情報を含む) が含まれています。クラスターは x509 証明書を使用して保護されます。|
|EULA.txt|Microsoft Azure Service Fabric のスタンドアロン Windows Server パッケージの使用に関するライセンス条項。EULA のコピーをすぐにダウンロードする必要がある場合は、[こちらをクリック](http://go.microsoft.com/fwlink/?LinkID=733084)してください。|
|Readme.txt|リリース ノートや基本的なインストール手順へのリンク。このページに記載されている説明も一部含まれています。|
|CreateServiceFabricCluster.ps1|ClusterConfig.json ファイル内の設定を使用してクラスターを作成する PowerShell スクリプト。|
|RemoveServiceFabricCluster.ps1|ClusterConfig.json ファイル内の設定を使用してクラスターを削除する PowerShell スクリプト。|
|AddNode.ps1|デプロイした既存のクラスターにノードを追加するための PowerShell スクリプト。|
|RemoveNode.ps1|デプロイした既存のクラスターからノードを削除するための PowerShell スクリプト。|


## クラスターのデプロイ計画と準備
クラスターを作成する前に、次の手順を実行します。

### 手順 1: クラスターのインフラストラクチャを検討する
ここでの Service Fabric クラスターの作成先は自分が所有するマシンです。そこで、クラスターがどのような障害に対応するかを決定できます。たとえば電気系統やインターネット接続を、それらのマシン専用に確保する必要はあるでしょうか。 また、マシンの物理的なセキュリティも考慮してください。物理的な配置場所はどこか、だれが利用するのかを考えます。 これらの点を判断したら、各種の障害ドメインにマシンを論理的に対応付けることができます (詳細については、このページで後述)。運用環境クラスターのインフラストラクチャ計画は、テスト環境のクラスターよりも複雑です。

<a id="preparemachines"></a>
### 手順 2: マシンの前提条件を満たすための準備をする
クラスターに追加する各マシンの前提条件は次のとおりです。

- 4 GB 以上のメモリを推奨
- ネットワーク接続 – マシンの接続先となるネットワークがセキュリティで保護されていることを確認します。
- Windows Server 2012 R2 または Windows Server 2012 ([KB2858668](https://support.microsoft.com/kb/2858668) がインストールされている必要があります)。
- [.NET Framework 4.5.1 以降](https://www.microsoft.com/download/details.aspx?id=40773) (フル インストール)。
- [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/setup/installing-windows-powershell)。
- クラスターのデプロイと構成を行うクラスター管理者には、個々のマシンに対する[管理者特権](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx)が必要です。
- [RemoteRegistry サービス](https://technet.microsoft.com/library/cc754820)がすべてのマシンで実行されている必要があります。

### 手順 3: クラスターの初期サイズを決める
スタンドアロン Service Fabric クラスターの各ノードには Service Fabric ランタイムがデプロイされており、それぞれがクラスターのメンバーになります。標準的な運用デプロイでは、OS インスタンス (物理または仮想) ごとにノードが 1 つ存在します。クラスターのサイズはビジネス ニーズに左右されるものの、3 ノード (マシン/VM) という最低限のクラスター サイズは満たす必要があります。開発目的では、特定のマシン 1 台に複数のノードを割り当てることができます。運用環境の場合、Service Fabric がサポートするノードは、物理コンピューターまたは仮想マシン 1 台につき 1 つだけです。

### 手順 4: 障害ドメインとアップグレード ドメインの数を決める
**障害ドメイン (FD)** は障害の物理的単位であり、データ センター内の物理インフラストラクチャと直接関連付けられます。障害ドメインは、同じ単一障害点を持ったハードウェア コンポーネント (コンピューター、スイッチ、ネットワークなど) で構成されます。障害ドメインとラックとの間に 1:1 の対応はありませんが、大まかにいえば、それぞれのラックが 1 つの障害ドメインと考えてかまいません。クラスターに含めるノードを検討するときは、3 つ以上の障害ドメインにノードを分散することを強くお勧めします。

各障害ドメインの名前は、*ClusterConfig.json* に障害ドメインを指定するときに選ぶことができます。Service Fabric では階層形式の障害ドメインがサポートされているため、実際のインフラストラクチャのトポロジをそこに反映させることが可能です。有効な障害ドメインの例を次に示します。

- "faultDomain": "fd:/Room1/Rack1/Machine1"
- "faultDomain": "fd:/FD1"
- "faultDomain": "fd:/Room1/Rack1/PDU1/M1"


**アップグレード ドメイン (UD)** は、ノードの論理ユニットです。Service Fabric によってオーケストレーションされるアップグレード (アプリケーション アップグレードまたはクラスター アップグレード) の間、アップグレード ドメイン内のノードはすべて中断されてアップグレードが実行されますが、他のアップグレード ドメイン内のノードはその間も要求に応答し続けることができます。マシンに対するファームウェア アップグレードでは、アップグレード ドメインが考慮されません。アップグレードは 1 台ずつ行う必要があります。

障害ドメインは計画の範囲外の障害の単位、アップグレード ドメインは計画の範囲内のメンテナンスの単位であると考えると理解しやすいでしょう。

アップグレード ドメインの名前は、*ClusterConfig.json* にアップグレード ドメインを指定するときに選ぶことができます。有効なアップグレード ドメインの例を次に示します。

- "upgradeDomain": "UD0"
- "upgradeDomain": "UD1A"
- "upgradeDomain": "DomainRed"
- "upgradeDomain": "Blue"

アップグレード ドメインと障害ドメインの詳細については、「[Service Fabric クラスターの記述](service-fabric-cluster-resource-manager-cluster-description.md)」を参照してください。

### 手順 5: Windows Server 用の Service Fabric スタンドアロン パッケージをダウンロードする
[Windows Server 用の Service Fabric スタンドアロン パッケージをダウンロード](http://go.microsoft.com/fwlink/?LinkId=730690)し、クラスターに属していないデプロイ用のマシンか、これからクラスターに追加するマシンのいずれかにパッケージ ファイルを解凍します。

<a id="createcluster"></a>
## クラスターの作成

前出の計画と準備のセクションで説明した手順を実行したら、クラスターを作成することになります。

### 手順 1: クラスターの構成を変更する
クラスターは、*ClusterConfig.json* ファイルに記述されています。このファイルのセクションの詳細については、「[スタンドアロン Windows クラスターの構成設定](service-fabric-cluster-manifest.md)」を参照してください。ダウンロードしたパッケージの *ClusterConfig.json* ファイルのいずれかを開き、次の設定を変更します。

|**構成設定**|**説明**|
|-----------------------|--------------------------|
|**NodeTypes**|クラスター ノードは、ノードのタイプでグループ分けすることができます。クラスターには少なくとも 1 つの NodeType が必要です。グループ内のすべてのノードは、次の特性を共有します。<br> **Name** - ノード タイプの名前です。<br>**Endpoint Ports** - このノード タイプに関連付けられている、さまざまな名前付きエンドポイント (ポート) です。このマニフェスト内の他のポート番号と競合せず、かつ同じマシン/VM 上で実行している他のアプリケーションでまだ使われていなければ、どのポート番号でも使用できます。<br> **Placement Properties** - このノード タイプの特性を表します。この特性は、システム サービスや独自のサービスを配置するうえでの制約として使用します。これらの特性は、特定のノードを補足するメタデータとして、ユーザー定義のキー/値のペアで記述されます。ノードのプロパティの例としては、ハード ドライブやグラフィック カードの有無、ハード ドライブ内のスピンドル数、コア数などの物理的な特性があります。<br> **Capacities** - ノード容量は、特定のノードで使用できるように確保されている特定のリソースの名前と量を定義します。たとえば、"MemoryInMb" と呼ばれるメトリックに対して既定で 2,048 MB のメモリを使用できるようにノードの容量を定義できます。実行時にはこの容量に基づき、特定のリソース量を必要とするサービスが確実に、その必要なリソースがあるノードに配置されます。<br>**IsPrimary** - 複数の NodeType を定義している場合は、*true* という値を使って 1 つだけプライマリに設定してください。ここでシステム サービスが実行されます。その他のすべてのノード タイプは値 *false* に設定する必要があります。|
|**Nodes**|クラスターに参加しているノードごとの詳細情報です (ノード タイプ、ノード名、IP アドレス、ノードの障害ドメインとアップグレード ドメイン)。ここには、クラスターの作成先となるマシンとその IP アドレスを列挙する必要があります。<br> すべてのノードに同じ IP アドレスを使用した場合、ワンボックス クラスターが作成されます。ワンボックス クラスターはあくまでテスト用です。運用環境のワークロードをデプロイする用途には使用しないでください。|

### 手順 2: クラスター 作成スクリプトを実行する
JSON ファイル内のクラスター構成に変更を加え、すべてのノード情報を追加したら、パッケージ フォルダーにあるクラスター作成 PowerShell スクリプト *CreateServiceFabricCluster.ps1* を実行し、JSON 構成ファイルのパスを渡して、使用許諾契約書に同意します。

このスクリプトは、クラスター構成ファイルにノードとして列挙されているすべてのマシンに管理者アクセスできれば、どのマシンでも実行できます。このスクリプトを実行するマシンが、クラスターに属していなくてもかまいません。

```
#Create an unsecured local development cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA true
```
```
#Create an unsecured multi-machine cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -AcceptEULA true
```

>[AZURE.NOTE] デプロイ ログは、CreateServiceFabricCluster Powershell を実行した VM/マシンのローカルで利用できます。このログは、Powershell コマンドを実行したフォルダーの "DeploymentTraces" という名前のサブフォルダーにあります。また、Service Fabric がマシンに正しくデプロイされたかどうかを確認するために、C:\\ProgramData ディレクトリにインストールされたファイルを探すことができ、タスク マネージャーで FabricHost.exe プロセスと Fabric.exe プロセスが実行されていることがわかります。

### 手順 3: クラスターに接続する

セキュリティで保護されたクラスターに接続する方法については、[こちらのドキュメント](service-fabric-connect-to-secure-cluster.md)を参照してください。

セキュリティで保護されていないクラスターに接続するには、次の PowerShell コマンドを実行します

```powershell

Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>

Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000

```
### 手順 4: Service Fabric Explorer を起動する

これで、Service Fabric Explorer を使用してクラスターに接続できるようになりました。http://localhost:19080/Explorer/index.html を使っていずれかのマシンから直接接続するか、http://<*IPAddressofaMachine*>:19080/Explorer/index.html を使ってリモートで接続することができます。



## クラスターでのノードの追加および削除

ビジネス ニーズの変化に応じて、スタンドアロン Service Fabric クラスターでノードを追加または削除できます。詳細の手順については、「[Service Fabric スタンドアロン クラスターでノードを追加または削除する](service-fabric-cluster-windows-server-add-remove-nodes.md)」をお読みください。


## クラスターの削除

クラスターを削除するには、パッケージ フォルダーにある *RemoveServiceFabricCluster.ps1* Powershell スクリプトを実行し、JSON 構成ファイルのパスを渡して、必要に応じて削除ログの場所を指定します。

このスクリプトは、クラスター構成ファイルにノードとして列挙されているすべてのマシンに管理者アクセスできれば、どのマシンでも実行できます。このスクリプトの実行対象となるマシンがクラスターに属しているかどうかは関係ありません。

```
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json   
```

## このパッケージに含まれる機能の確認

現時点では、パッケージ全体がプレビューの段階です。

## 次のステップ
- [スタンドアロン Windows クラスターの構成設定](service-fabric-cluster-manifest.md)
- [スタンドアロン Service Fabric クラスターでノードを追加または削除する](service-fabric-cluster-windows-server-add-remove-nodes.md)
- [Windows を実行する Azure VM を使用してスタンドアロン Service Fabric クラスターを作成する](service-fabric-cluster-creation-with-windows-azure-vms.md)
- [Windows 上のスタンドアロン クラスターを Windows セキュリティで保護する](service-fabric-windows-cluster-windows-security.md)
- [X.509 証明書を使用した Windows でのスタンドアロン クラスターの保護](service-fabric-windows-cluster-x509-security.md)


<!--Image references-->
[TrustedZone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png

<!---HONumber=AcomDC_0921_2016-->