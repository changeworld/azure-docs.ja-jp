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
   ms.date="07/05/2016"
   ms.author="chackdan"/>


# Windows Server 上で稼働するクラスターの作成

Azure Service Fabric クラスターは、Windows Server を実行するあらゆる仮想マシンまたはコンピューター上に作成できます。つまり相互に接続された一連の Windows Server コンピューターが存在する環境さえあれば、オンプレミスであれ、クラウドであれ、Service Fabric アプリケーションをデプロイして実行できるということです。Service Fabric には、Service Fabric クラスターを作成するためのセットアップ パッケージ (スタンドアロン Windows Server パッケージ) が用意されています。

この記事では、Service Fabric のスタンドアロン パッケージを使用して、オンプレミスにクラスターを作成する手順について説明していますが、他の環境 (他のクラウドなど) にも簡単に応用することができます。

>[AZURE.NOTE] このスタンドアロン Windows Server パッケージは現在プレビューの段階であり、運用環境のワークロードではサポートされていません。EULA のコピーがすぐに必要という場合は、[こちらをクリック](http://go.microsoft.com/fwlink/?LinkID=733084)してダウンロードしてください。

<a id="downloadpackage"></a>
## Service Fabric スタンドアロン パッケージのダウンロード


[Windows Server 2012 R2 以降用の Service Fabric のスタンドアロン パッケージをダウンロード](http://go.microsoft.com/fwlink/?LinkId=730690)してください。これは "*Microsoft.Azure.ServiceFabric.WindowsServer.&lt;バージョン&gt;.zip*" という名前です。

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
|Readme.txt|リリース ノートや基本的なインストール手順へのリンク。このページに記載されているインストラクションの内容も一部含まれています。|
|CreateServiceFabricCluster.ps1|ClusterConfig.json ファイル内の設定を使用してクラスターを作成する PowerShell スクリプト。|
|RemoveServiceFabricCluster.ps1|ClusterConfig.json ファイル内の設定を使用してクラスターを削除する PowerShell スクリプト。|
|AddNode.ps1|デプロイした既存のクラスターにノードを追加するための PowerShell スクリプト。|
|RemoveNode.ps1|デプロイした既存のクラスターからノードを削除するための PowerShell スクリプト。|


## クラスターのデプロイ計画と準備
以降の手順は、クラスターを作成する前に行う必要があります。

### 手順 1: クラスターのインフラストラクチャを検討する
ここでの Service Fabric クラスターの作成先は自分が所有するマシンです。そこで、クラスターがどのような障害に対応するかを決定します。たとえば電気系統やインターネット接続を、それらのマシン専用に確保する必要はあるでしょうか。 またマシンの物理的なセキュリティにも気を配る必要があります。物理的な配置場所はどこか、だれが利用するのかを考えます。 これらの点を判断したら、各種の障害ドメイン (後述) にマシンを論理的に対応付けます。運用環境クラスターのインフラストラクチャ計画は、テスト環境のクラスターよりも複雑です。

### 手順 2: マシンの前提条件を満たすための準備をする
クラスターに追加する各マシンの前提条件は次のとおりです。

- 2 GB 以上のメモリを推奨
- ネットワーク接続。マシンがセキュリティで保護されたネットワーク上にあることを確認します。
- Windows Server 2012 R2 または Windows Server 2012 (後者については KB2858668 がインストールされていること)。
- .NET Framework 4.5.1 以降 (フル インストール)
- Windows PowerShell 3.0
- クラスターのデプロイと構成を行うクラスター管理者には、個々のマシンに対する管理者特権が必要です。
- RemoteRegistry サービスがすべてのマシンで実行されている必要があります。

### 手順 3: クラスターの初期サイズを決める
各ノードには Service Fabric ランタイムがデプロイされており、それぞれがクラスターのメンバーになります。標準的な運用デプロイでは、OS インスタンス (物理または仮想) ごとにノードが 1 つ存在します。クラスターのサイズはビジネス ニーズに左右されるものの、3 ノード (マシン/VM) という最低限のクラスター サイズは満たす必要があります。開発目的では、特定のマシン 1 台に複数のノードを割り当てることができます。運用環境の場合、Service Fabric がサポートするノードは、物理コンピューターまたは仮想マシン 1 台につき 1 つだけです。

### 手順 4: 障害ドメインとアップグレード ドメインの数を決める
**障害ドメイン (FD)** は障害の物理的単位であり、データ センター内の物理インフラストラクチャと直接関連付けられます。障害ドメインは、同じ単一障害点を持ったハードウェア コンポーネント (コンピューター、スイッチ、ネットワークなど) から成ります。障害ドメインとラックとの間に 1:1 の対応はありませんが、大まかにいえば、それぞれのラックが 1 つの障害ドメインと考えてかまいません。クラスターに含めるノードを検討するときは、3 つ以上の障害ドメインにノードを分散することを強くお勧めします。

各障害ドメインの名前は、*ClusterConfig.json* に障害ドメインを指定するときに選びます。Service Fabric では階層形式の障害ドメインがサポートされているため、実際のインフラストラクチャのトポロジをそこに反映させることが可能です。有効な障害ドメインの例を次に示します。

- "faultDomain": "fd:/Room1/Rack1/Machine1"
- "faultDomain": "fd:/FD1"
- "faultDomain": "fd:/Room1/Rack1/PDU1/M1"


**アップグレード ドメイン (UD)** は、ノードの論理ユニットです。Service Fabric によってオーケストレーションされるアップグレード (アプリケーション アップグレードまたはクラスター アップグレード) の間、アップグレード ドメイン内のノードはすべて中断されてアップグレードが実行されますが、他のアップグレード ドメイン内のノードはその間も要求に応答し続けることができます。マシンに対するファームウェア アップグレードでは、アップグレード ドメインが考慮されません。アップグレードは 1 台ずつ行う必要があります。

障害ドメインは計画の範囲外の障害の単位、アップグレード ドメインは計画の範囲内のメンテナンスの単位であると考えると理解しやすいでしょう。

アップグレード ドメインの名前は、*ClusterConfig.json* にアップグレード ドメインを指定するときに選びます。有効なアップグレード ドメインの例を次に示します。

- "upgradeDomain": "UD0"
- "upgradeDomain": "UD1A"
- "upgradeDomain": "DomainRed"
- "upgradeDomain": "Blue"

アップグレード ドメインと障害ドメインの詳細については、「[Service Fabric クラスターの記述](service-fabric-cluster-resource-manager-cluster-description.md)」を参照してください。

### 手順 5: Windows Server 用の Service Fabric スタンドアロン パッケージをダウンロードする
[Windows Server 用の Service Fabric スタンドアロン パッケージをダウンロード](http://go.microsoft.com/fwlink/?LinkId=730690)し、クラスターに属していないデプロイ用のマシンか、これからクラスターに追加するマシンのいずれかにパッケージ ファイルを解凍します。

<a id="createcluster"></a>
## クラスターの作成

前出の計画と準備のセクションで説明した手順を実行したら、いよいよクラスターを作成することになります。

### 手順 1: クラスターの構成を変更する
クラスターは、*ClusterConfig.json* ファイルに記述されています。このファイルのセクションの詳細については、「[スタンドアロン Windows クラスターの構成設定](service-fabric-cluster-manifest.md)」を参照してください。ダウンロードしたパッケージの *ClusterConfig.json* ファイルのいずれかを開き、次の設定を変更します。

|**構成設定**|**説明**|
|-----------------------|--------------------------|
|**NodeTypes**|クラスター ノードは、ノードのタイプでグループ分けすることができます。クラスターには少なくとも 1 つの NodeType が必要です。グループ内のすべてのノードは、次の特性を共有します。<br> **Name** - ノード タイプの名前です。<br>**Endpoints Ports** - このノード タイプに関連付けられている、さまざまな名前付きエンドポイント (ポート) です。このマニフェスト内の他のポート番号と競合せず、かつ同じマシン/VM 上で実行している他のアプリケーションでまだ使われていなければ、どのポート番号でも使用できます。<br> **Placement Properties** - このノード タイプの特性を表します。この特性は、後でシステム サービスや独自のサービスを配置するうえでの制約として使用します。これらの特性は、特定のノードを補足するメタデータとして、ユーザー定義のキー/値のペアで記述されます。ノードのプロパティの例としては、ハード ドライブやグラフィック カードの有無、ハード ドライブ内のスピンドル数、コア数などの物理的な特性があります。<br> **Capacities** - ノード容量は、特定のノードで使用できるように確保されている特定のリソースの名前と量を定義します。たとえば、"MemoryInMb" と呼ばれるメトリックに対して既定で 2,048 MB のメモリを使用できるようにノードの容量を定義できます。実行時にはこの容量に基づき、特定のリソース量を必要とするサービスが確実に、そのリソースに空きのあるノードに配置されます。<br>**IsPrimary** - 複数の NodeType を定義している場合は、*true* という値を使って 1 つだけプライマリに設定してください。ここでシステム サービスが実行されます。その他のすべてのノード タイプは値 *false* に設定する必要があります。|
|**Nodes**|クラスターに参加しているノードごとの詳しい情報 (ノード タイプ、ノード名、IP アドレス、ノードの障害ドメインとアップグレード ドメイン)。ここには、クラスターの作成先となるマシンとその IP アドレスを列挙する必要があります。<br> すべてのノードに同じ IP アドレスを使用した場合、ワンボックス クラスターが作成されます。ワンボックス クラスターはあくまでテスト用です。運用環境のワークロードをデプロイする用途には使用しないでください。|

### 手順 2: クラスター 作成スクリプトを実行する
JSON ファイル内のクラスター構成に変更を加え、すべてのノード情報を追加したら、パッケージ フォルダーにあるクラスター作成 PowerShell スクリプト *CreateServiceFabricCluster.ps1* を実行します。JSON 構成ファイルのパスとパッケージ CAB ファイルの場所を引数として指定してください。

このスクリプトは、クラスター構成ファイルにノードとして列挙されているすべてのマシンに管理アクセスできれば、どのマシンでも実行できます。このスクリプトを実行するマシンが、クラスターに属していなくてもかまいません。

```
#Create an unsecure local development cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -AcceptEULA $true
```
```
#Create an unsecure multi-machine cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -AcceptEULA $true
```

>[AZURE.NOTE] デプロイ ログは、CreateServiceFabricCluster Powershell を実行した VM/マシンのローカルで利用できます。デプロイ ログは、Powershell コマンドを実行したフォルダー内の "DeploymentTraces" という名前のサブフォルダーにあります。また、Service Fabric がマシンに正しくデプロイされたかどうかを確認するために、C:\\ProgramData ディレクトリにインストールされたファイルを探すことができ、タスク マネージャーで FabricHost.exe プロセスと Fabric.exe プロセスが実行されていることがわかります。

### 手順 3: クラスターに接続する
これで、Service Fabric Explorer を使用してクラスターに接続できるようになりました。http://localhost:19080/Explorer/index.html を使っていずれかのマシンから直接接続するか、http://<*IPAddressofaMachine*>:19080/Explorer/index.html を使ってリモートで接続することができます。

## クラスターへのノードの追加

1. クラスターに追加する VM/マシンを準備します (「クラスターのデプロイ計画と準備」セクションの手順 2. を参照)。
2. この VM/マシンを追加する障害ドメインとアップグレード ドメインについて計画します。
3. [Windows Server 用の Service Fabric のスタンドアロン パッケージをダウンロード](http://go.microsoft.com/fwlink/?LinkId=730690)またはコピーし、クラスターに追加する予定の VM/マシンにパッケージを解凍します。
4. Powershell 管理プロンプトを開き、解凍したパッケージのある場所に移動します。
5. 追加する新しいノードを記述したパラメーターを指定して、*AddNode.ps1* Powershell を実行します。次の例では、名前が VM5、タイプが NodeType0、IP アドレスが 182.17.34.52 の新しいノードを UD1 と FD1 に追加します。*ExistingClusterConnectionEndPoint* は、既存のクラスターに既にあるノードの接続エンドポイントです。これには、クラスター内の "*任意*" のノードの IP アドレスを選択できます。

```
.\AddNode.ps1 -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClusterConnectionEndPoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain FD1
```

## クラスターからのノードの削除

1. クラスターから削除する VM/マシンにリモート デスクトップ (RDP) 接続します。
2. [Windows Server 用の Service Fabric のスタンドアロン パッケージをダウンロード](http://go.microsoft.com/fwlink/?LinkId=730690)またはコピーし、クラスターから削除する予定の VM/マシンにパッケージを解凍します。
3. Powershell 管理プロンプトを開き、解凍したパッケージのある場所に移動します。
4. *RemoveNode.ps1* Powershell を実行します。次の例では、現在のノードをクラスターから削除します。*ExistingClusterConnectionEndPoint* は、既存のクラスターに既にあるノードの接続エンドポイントです。これには、クラスター内の "*任意*" のノードの IP アドレスを選択できます。

```
.\RemoveNode.ps1 -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -ExistingClusterConnectionEndPoint 182.17.34.50:19000
```

## クラスターの削除
クラスターを削除するには、パッケージ フォルダーにある *RemoveServiceFabricCluster.ps1* Powershell スクリプトを実行し、JSON 構成ファイルのパスとパッケージ CAB ファイルの場所を渡してください。

このスクリプトは、クラスター構成ファイルにノードとして列挙されているすべてのマシンに管理アクセスできれば、どのマシンでも実行できます。このスクリプトの実行対象となるマシンがクラスターに属しているかどうかは関係ありません。

```
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json   -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
```

## 方法: Azure IaaS VM で 3 ノード クラスターを作成する
次の手順では、スタンドアロンの Windows Server インストーラーを使用して Azure IaaS VM 上にクラスターを作成する方法について説明します。Service Fabric リソース プロバイダーによってアップグレードされる Azure ポータルで作成されたクラスターとは異なり、この IaaS クラスター上の Service Fabric ランタイムは完全に自分で管理することに注意してください。

1. Azure ポータルにログインし、リソース グループに新しい Windows Server 2012 R2 Datacenter VM を作成します。
2. 同じリソース グループに Windows Server 2012 R2 Datacenter VM をさらに 2 つ追加します。作成時に各 VM には同じ管理者ユーザー名とパスワードを使用するようにしてください。3 つの VM は、作成されると、すべて同じ仮想ネットワーク内に表示されます。
3. 各 VM に接続し、サーバー マネージャーの [ローカル サーバー] ダッシュボードを使用して Windows ファイアウォールを無効にします。これにより、ネットワーク トラフィックをマシン間でやり取りできるようになります。各マシンで、コマンド プロンプトを開いて「*ipconfig*」と入力し、IP アドレスを取得します。または、Azure ポータルでそのリソース グループの仮想ネットワーク リソースを選択すると、各マシンの IP アドレスを確認することもできます。
4. いずれかのマシンに接続し、他の 2 台のマシンに正常に ping を実行できるかどうかをテストします。
5. いずれかの VM に接続し、スタンドアロン Windows Server パッケージをマシンの新しいフォルダーにダウンロードして、そのパッケージを解凍します。
6. メモ帳で *ClusterConfig.Unsecure.MultiMachine.json* ファイルを開き、3 つのマシンの IP アドレスで各ノードを編集し、上部のクラスター名を変更して、ファイルを保存します。クラスター マニフェストの部分的な例を次に示します。これは、各マシンの IP アドレスを示しています。

    ```
    {
        "name": "TestCluster",
        "clusterManifestVersion": "1.0.0",
        "apiVersion": "2015-01-01-alpha",
        "nodes": [
        {
            "nodeName": "vm0",
        	"metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.5",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        },
        {
            "nodeName": "vm1",
        	"metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.4",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc2/r0",
            "upgradeDomain": "UD1"
        },
        {
            "nodeName": "vm2",
        	"metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.6",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc3/r0",
            "upgradeDomain": "UD2"
        }
    ],
    ```

7. PowerShell ISE ウィンドウを開き、スタンドアロンのインストーラー パッケージをダウンロードして解凍したフォルダーに移動します。このフォルダーには、上記のマニフェスト ファイルが保存されています。次の PowerShell コマンドを実行します。

    ```
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
    ```

8. PowerShell が実行され、各マシンに接続してクラスターが作成されることを確認できます。約 1 分後に、いずれかのマシンの IP アドレス (例: http://10.7.0.5:19080/Explorer/index.html) で Service Fabric Explorer に接続すると、クラスターが動作可能かどうかをテストできます。これは IaaS VM 上のスタンドアロン クラスターであるため、このクラスターをセキュリティで保護する場合は、証明書を VM にデプロイするか、オンプレミスの場合と同様に、いずれかのマシンを Windows 認証用の Windows Server Active Directory (AD) コントローラーとしてセットアップする必要があります。セキュリティで保護されたクラスターの設定については、「次のステップ」を参照してください。

## 次のステップ
- [Windows Server または Linux でのスタンドアロン Service Fabric クラスターの作成](service-fabric-deploy-anywhere.md)
- [スタンドアロン Windows クラスターの構成設定](service-fabric-cluster-manifest.md)

- [Windows 上のスタンドアロン クラスターを Windows セキュリティで保護する](service-fabric-windows-cluster-windows-security.md)
- [X.509 証明書を使用した Windows でのスタンドアロン クラスターの保護](service-fabric-windows-cluster-x509-security.md)

<!---HONumber=AcomDC_0713_2016-->