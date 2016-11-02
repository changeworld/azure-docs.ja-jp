<properties
   pageTitle="スタンドアロン Azure Service Fabric クラスターを作成および管理する | Microsoft Azure"
   description="Windows Server が実行されている任意のマシン (物理コンピューターまたは仮想マシン) で Azure Service Fabric クラスターを作成および管理します。クラスターの作成先は、オンプレミスでも、任意のクラウドでもかまいません。"
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
   ms.date="09/26/2016"
   ms.author="dkshir;chackdan"/>



# <a name="create-and-manage-a-cluster-running-on-windows-server"></a>Windows Server 上で実行するクラスターを作成および管理する

Azure Service Fabric を使用すると、Windows Server を実行するあらゆる仮想マシンまたはコンピューター上に Service Fabric クラスターを作成できます。 つまり相互に接続された一連の Windows Server コンピューターを含む環境さえあれば、オンプレミスであれ、クラウド プロバイダーであれ、Service Fabric アプリケーションをデプロイして実行できるということです。 Service Fabric には、Service Fabric クラスターを作成するためのセットアップ パッケージ (スタンドアロン Windows Server パッケージ) が用意されています。

この記事では、Service Fabric のスタンドアロン パッケージを使用して、オンプレミスにクラスターを作成する手順について説明していますが、他の環境 (他のクラウドなど) にも簡単に応用することができます。

>[AZURE.NOTE] このスタンドアロン Windows Server パッケージには、商用では使用できない、プレビュー段階の機能が含まれている可能性があります。 プレビューに含まれている機能の一覧を確認するには、「このパッケージに含まれる機能の確認」を参照してください。 [使用許諾契約書のコピーのダウンロード](http://go.microsoft.com/fwlink/?LinkID=733084) もできるようになりました。


<a id="getsupport"></a>
## <a name="get-support-for-the-service-fabric-standalone-package"></a>Service Fabric スタンドアロン パッケージに関連したサポートの利用

- Windows Server 用 Service Fabric スタンドアロン パッケージに関するコミュニティへの質問を [Azure Service Fabric フォーラム](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?) で投稿します。

- [Service Fabric の専門家によるサポート](http://support.microsoft.com/oas/default.aspx?prid=16146 )については、チケットを申請してください。  Microsoft によるプロフェッショナル サポートの詳細については、[こちら](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0) をご覧ください。

<a id="downloadpackage"></a>
## <a name="download-the-service-fabric-standalone-package"></a>Service Fabric スタンドアロン パッケージのダウンロード


[Service Fabric for Windows Server 2012 R2 以降用のスタンドアロン パッケージをダウンロード](http://go.microsoft.com/fwlink/?LinkId=730690)してください。Microsoft.Azure.ServiceFabric.WindowsServer.&lt;バージョン&gt;.zip という名前です。


ダウンロード パッケージには、次のファイルが含まれています。

|**ファイル名**|**簡単な説明**|
|-----------------------|--------------------------|
|MicrosoftAzureServiceFabric.cab|クラスター内の各マシンにデプロイされるバイナリを含んだ .cab ファイル。|
|ClusterConfig.Unsecure.DevCluster.json|クラスター構成サンプル ファイル。3 つのノードと 1 つのマシン (または仮想マシン) を持つ、セキュリティで保護されていない開発クラスターの設定 (クラスター内の各ノードの情報を含む) が含まれています。 |
|ClusterConfig.Unsecure.MultiMachine.json|クラスター構成サンプル ファイル。複数のマシン (または仮想マシン) を持つ、セキュリティで保護されていないクラスターの設定 (クラスター内の各マシンの情報を含む) が含まれています。|
|ClusterConfig.Windows.DevCluster.json|クラスター構成サンプル ファイル。3 つのノードと 1 つのマシン (または仮想マシン) を持つ、セキュリティ保護された開発クラスターのすべての設定 (クラスターの各ノードの情報を含む) が含まれています。 クラスターは [Windows ID](https://msdn.microsoft.com/library/ff649396.aspx) を使用して保護されます。|
|ClusterConfig.Windows.MultiMachine.json|クラスター構成サンプル ファイル。複数のマシン (または仮想マシン) を持つ、Windows セキュリティを使用するセキュリティ保護されたクラスターのすべての設定 (セキュリティ保護されたクラスター内の各マシンの情報を含む) が含まれています。 クラスターは [Windows ID](https://msdn.microsoft.com/library/ff649396.aspx) を使用して保護されます。|
|ClusterConfig.x509.DevCluster.json|クラスター構成サンプル ファイル。3 つのノードと 1 つのマシン (または仮想マシン) を持つ、セキュリティ保護された開発クラスターのすべての設定 (クラスターの各ノードの情報を含む) が含まれています。 クラスターは x509 証明書を使用して保護されます。|
|ClusterConfig.x509.MultiMachine.json|クラスター構成サンプル ファイル。複数のマシン (仮想マシン) を持つ、セキュリティ保護されたクラスターのすべての設定 (セキュリティ保護されたクラスター内の各ノードの情報を含む) が含まれています。 クラスターは x509 証明書を使用して保護されます。|
|EULA_ENU.txt|Microsoft Azure Service Fabric のスタンドアロン Windows Server パッケージの使用に関するライセンス条項。 [使用許諾契約書のコピーのダウンロード](http://go.microsoft.com/fwlink/?LinkID=733084) ができるようになりました。|
|Readme.txt|リリース ノートや基本的なインストール手順へのリンク。 このドキュメントに記載の説明も一部含まれています。|
|CreateServiceFabricCluster.ps1|ClusterConfig.json ファイル内の設定を使用してクラスターを作成する PowerShell スクリプト。|
|RemoveServiceFabricCluster.ps1|ClusterConfig.json ファイル内の設定を使用してクラスターを削除する PowerShell スクリプト。|
|ThirdPartyNotice.rtf |パッケージに同梱されるサードパーティ製ソフトウェアに関する注意。|
|AddNode.ps1|デプロイした既存のクラスターにノードを追加するための PowerShell スクリプト。|
|RemoveNode.ps1|デプロイした既存のクラスターからノードを削除するための PowerShell スクリプト。|
|CleanFabric.ps1|使用中のコンピューターからスタンドアロンの Service Fabric をアンインストールするための PowerShell スクリプト。 以前の MSI インストールについては、それに対応する独自のアンインストーラーで削除する必要があります。|
|TestConfiguration.ps1|Cluster.json で指定されたインフラストラクチャを分析するための PowerShell スクリプト。|


## <a name="plan-and-prepare-your-cluster-deployment"></a>クラスターのデプロイ計画と準備
クラスターを作成する前に、次の手順を実行します。

### <a name="step-1:-plan-your-cluster-infrastructure"></a>手順 1: クラスターのインフラストラクチャを検討する
ここでの Service Fabric クラスターの作成先は自分が所有するマシンです。そこで、クラスターがどのような障害に対応するかを決定できます。 たとえば電気系統やインターネット接続を、それらのマシン専用に確保する必要はあるでしょうか。 また、マシンの物理的なセキュリティも考慮してください。 マシンの配置場所とマシンへのアクセスが必要なユーザー これらの点を判断したら、各種の障害ドメイン (手順 4 を参照) にマシンを論理的に対応付けることができます。 運用環境クラスターのインフラストラクチャ計画は、テスト環境のクラスターの場合よりも複雑です。

<a id="preparemachines"></a>
### <a name="step-2:-prepare-the-machines-to-meet-the-prerequisites"></a>手順 2: マシンの前提条件を満たすための準備をする
クラスターに追加する各マシンの前提条件は次のとおりです。

- 16 GB 以上の RAM (推奨)。
- 40 GB 以上の使用可能なディスク領域 (推奨)。
- 4 コア以上の CPU (推奨)。
- すべてのマシンのセキュリティで保護された 1 つ以上のネットワークへの接続。
- Windows Server 2012 R2 または Windows Server 2012 ( [KB2858668](https://support.microsoft.com/kb/2858668) がインストールされている必要があります)。
- [.NET Framework 4.5.1 以降](https://www.microsoft.com/download/details.aspx?id=40773)(フル インストール)。
- [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/setup/installing-windows-powershell)。
- [RemoteRegistry サービス](https://technet.microsoft.com/library/cc754820) がすべてのマシンで実行されている必要があります。

クラスターのデプロイと構成を行うクラスター管理者には、個々のマシンに対する [管理者特権](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) が必要です。 ドメイン コントローラーに Service Fabric をインストールすることはできません。

### <a name="step-3:-determine-the-initial-cluster-size"></a>手順 3: クラスターの初期サイズを決める
スタンドアロン Service Fabric クラスターの各ノードには Service Fabric ランタイムがデプロイされており、それぞれがクラスターのメンバーになります。 標準的な運用デプロイでは、OS インスタンス (物理または仮想) ごとにノードが 1 つ存在します。 クラスターのサイズは、ビジネス ニーズによって決まります。 ただし、最低でも 3 つのノード (コンピューターまたは仮想マシン) を備えたクラスター サイズとする必要があります。
開発目的では、特定のマシン 1 台に複数のノードを割り当てることができます。 運用環境の場合、Service Fabric がサポートするノードは、物理コンピューターまたは仮想マシン 1 台につき 1 つだけです。

### <a name="step-4:-determine-the-number-of-fault-domains-and-upgrade-domains"></a>手順 4: 障害ドメインとアップグレード ドメインの数を決める
"*障害ドメイン*" (FD) は障害の物理的単位であり、データ センター内の物理インフラストラクチャと直接関連付けられます。 障害ドメインは、同じ単一障害点を持ったハードウェア コンポーネント (コンピューター、スイッチ、ネットワークなど) で構成されます。 障害ドメインとラックとの間に 1:1 の対応はありませんが、大まかにいえば、それぞれのラックが 1 つの障害ドメインと考えてかまいません。 クラスターに含めるノードを検討するときは、3 つ以上の障害ドメインにノードを分散することを強くお勧めします。

各障害ドメインの名前は、ClusterConfig.json に障害ドメインを指定するときに選ぶことができます。 Service Fabric では階層形式の障害ドメインがサポートされているため、実際のインフラストラクチャのトポロジをそこに反映させることが可能です。  有効な障害ドメインの例を次に示します。

- "faultDomain": "fd:/Room1/Rack1/Machine1"
- "faultDomain": "fd:/FD1"
- "faultDomain": "fd:/Room1/Rack1/PDU1/M1"


"*アップグレード ドメイン*" (UD) は、ノードの論理ユニットです。 Service Fabric によってオーケストレーションされるアップグレード (アプリケーション アップグレードまたはクラスター アップグレード) の間、アップグレード ドメイン内のノードはすべて中断されてアップグレードが実行されますが、他のアップグレード ドメイン内のノードはその間も要求に応答し続けることができます。 マシンに対するファームウェア アップグレードでは、アップグレード ドメインが考慮されません。アップグレードは 1 台ずつ行う必要があります。

障害ドメインは計画の範囲外の障害の単位、アップグレード ドメインは計画の範囲内のメンテナンスの単位であると考えると理解しやすいでしょう。

アップグレード ドメインの名前は、ClusterConfig.jsonにアップグレード ドメインを指定するときに選ぶことができます。 有効な名前の例を次に示します。

- "upgradeDomain": "UD0"
- "upgradeDomain": "UD1A"
- "upgradeDomain": "DomainRed"
- "upgradeDomain": "Blue"

アップグレード ドメインと障害ドメインの詳細については、[「Service Fabric クラスターの記述」](service-fabric-cluster-resource-manager-cluster-description.md)を参照してください。

### <a name="step-5:-download-the-service-fabric-standalone-package-for-windows-server"></a>手順 5: Windows Server 用の Service Fabric スタンドアロン パッケージをダウンロードする
[Windows Server 用の Service Fabric スタンドアロン パッケージをダウンロード](http://go.microsoft.com/fwlink/?LinkId=730690) し、クラスターに属していないデプロイ用のマシンか、これからクラスターに追加するマシンのいずれかにパッケージ ファイルを解凍します。 展開したフォルダー `Microsoft.Azure.ServiceFabric.WindowsServer` の名前は変更してもかまいません。

<a id="createcluster"></a>
## <a name="create-your-cluster"></a>クラスターの作成

計画と準備の手順を実行したら、クラスターを作成するための準備は完了です。

### <a name="step-1:-modify-cluster-configuration"></a>手順 1: クラスターの構成を変更する
クラスターは、ClusterConfig.json ファイルに記述されています。 このファイルのセクションの詳細については、[「スタンドアロン Windows クラスターの構成設定」](service-fabric-cluster-manifest.md) を参照してください。
ダウンロードしたパッケージの ClusterConfig.json ファイルのいずれかを開き、次の設定を変更します。

|**構成設定**|**説明**|
|-----------------------|--------------------------|
|**NodeTypes**|クラスター ノードは、ノードのタイプでグループ分けすることができます。 クラスターには少なくとも 1 つの NodeType が必要です。 グループ内のすべてのノードは、次の特性を共有します。 <br> **Name** - ノード タイプの名前です。 <br>**Endpoint Ports** - このノード タイプに関連付けられている、さまざまな名前付きエンドポイント (ポート) です。 このマニフェスト内の他のポート番号と競合せず、かつ同じマシンまたは仮想マシン上で実行している他のアプリケーションでまだ使われていなければ、どのポート番号でも使用できます。 <br> **Placement Properties** - このノード タイプの特性を表します。この特性は、システム サービスや独自のサービスを配置するうえでの制約として使用します。 これらの特性は、特定のノードを補足するメタデータとして、ユーザー定義のキー/値のペアで記述されます。 ノードのプロパティの例としては、ノード上でのハード ドライブやグラフィック カードの有無、ハード ドライブ内のスピンドル数、コア数などの物理的な特性があります。 <br> **Capacities** - ノード容量は、特定のノードで使用できるように確保されている特定のリソースの名前と量を定義します。 たとえば、"MemoryInMb" と呼ばれるメトリックに対して既定で 2,048 MB のメモリを使用できるようにノードの容量を定義できます。 実行時にはこの容量に基づき、特定のリソース量を必要とするサービスが確実に、そのリソースが確保されているノードに配置されます。<br>**IsPrimary**: 複数の NodeType が定義されている場合は、必ず 1 つのみをプライマリに設定してください (値を *true* にする)。プライマリでシステム サービスが実行されます。 その他のすべてのノード タイプは値 *false* に設定する必要があります。
|**Nodes**|クラスターに参加しているノードごとの詳細情報です (ノード タイプ、ノード名、IP アドレス、ノードの障害ドメインとアップグレード ドメイン)。 ここには、クラスターの作成先となるマシンとその IP アドレスを列挙する必要があります。 <br> すべてのノードに同じ IP アドレスを使用した場合、ワンボックス クラスターが作成されます。ワンボックス クラスターはあくまでテスト用です。 運用環境のワークロードをデプロイする用途には使用しないでください。|

### <a name="step-2:-run-the-testconfiguration-script"></a>手順 2: TestConfiguration スクリプトを実行する

TestConfiguration スクリプトは、cluster.json の定義に従ってインフラストラクチャをテストし、必要なアクセス許可の割り当てやコンピューターの相互接続など、デプロイを正常に実行するうえで必要な属性が定義されていることを確認します。 これは簡易版のベスト プラクティス アナライザーといってもよいでしょう。 このツールについては堅牢性を高めるために、今後さらに検証を行っていく予定です。

このスクリプトは、クラスター構成ファイルにノードとして列挙されているすべてのマシンに管理者アクセスできれば、どのマシンでも実行できます。 このスクリプトが実行されるマシンがクラスターに属している必要はありません。

```powershell

PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 : True
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
Passed                     : True


```

### <a name="step-3:-run-the-create-cluster-script"></a>手順 3: クラスター作成スクリプトを実行する
JSON ファイル内のクラスター構成に変更を加え、すべてのノード情報を追加したら、パッケージ フォルダーにあるクラスター作成 PowerShell スクリプト *CreateServiceFabricCluster.ps1* を実行し、JSON 構成ファイルのパスを渡します。 これが完了したら、使用許諾契約書に同意します。

このスクリプトは、クラスター構成ファイルにノードとして列挙されているすべてのマシンに管理者アクセスできれば、どのマシンでも実行できます。 このスクリプトが実行されるマシンがクラスターに属している必要はありません。

```
#Create an unsecured local development cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```
```
#Create an unsecured multi-machine cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -AcceptEULA
```

>[AZURE.NOTE] デプロイ ログは、CreateServiceFabricCluster PowerShell を実行した VM/マシンのローカルで利用できます。 このログは、PowerShell コマンドを実行したフォルダーの DeploymentTraces という名前のサブフォルダーにあります。 Service Fabric がマシンに正しくデプロイされたかどうかを確認するために、C:\ProgramData ディレクトリにインストールされたファイルを探すことができ、タスク マネージャーで FabricHost.exe プロセスと Fabric.exe プロセスが実行されていることがわかります。

### <a name="step-4:-connect-to-the-cluster"></a>手順 4: クラスターに接続する

セキュリティで保護されたクラスターに接続するには、[セキュリティで保護されたクラスターへの Service Fabric の接続](service-fabric-connect-to-secure-cluster.md)参照してください。

セキュリティで保護されていないクラスターに接続するには、次の PowerShell コマンドを実行します。

```powershell

Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>

Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000

```
### <a name="step-5:-bring-up-service-fabric-explorer"></a>手順 5: Service Fabric Explorer を起動する

これで、Service Fabric Explorer を使用してクラスターに接続できるようになりました。http://localhost:19080/Explorer/index.html を使っていずれかのマシンから直接接続するか、http://<*IPAddressofaMachine*>:19080/Explorer/index.html を使ってリモートで接続することができます。



## <a name="add-and-remove-nodes"></a>ノードの追加と削除

ビジネス ニーズの変化に応じて、スタンドアロン Service Fabric クラスターでノードを追加または削除できます。 詳細の手順については、[「Service Fabric スタンドアロン クラスターでノードを追加または削除する」](service-fabric-cluster-windows-server-add-remove-nodes.md) をお読みください。


## <a name="remove-a-cluster"></a>クラスターの削除

クラスターを削除するには、パッケージ フォルダーにある *RemoveServiceFabricCluster.ps1* PowerShell スクリプトを実行し、JSON 構成ファイルのパスを渡してください。 必要に応じて、削除対象のログの場所を指定することもできます。

このスクリプトは、クラスター構成ファイルにノードとして列挙されているすべてのマシンに管理者アクセスできれば、どのマシンでも実行できます。 このスクリプトが実行されるマシンがクラスターに属している必要はありません。

```
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json   
```


<a id="telemetry"></a>
## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>収集されるテレメトリ データとオプトアウトの方法

製品品質の向上を図るために、Service Fabric の使用状況に関するテレメトリが既定で収集されます。 セットアップの過程で実行されるベスト プラクティス アナライザーは、 [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1)に対する接続の可否をチェックします。 この URL に到達できなかった場合、テレメトリを無効にしない限り、セットアップは失敗します。

  1. テレメトリ パイプラインは、以下のデータを 1 日に 1 回、[https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) にアップロードしようと試みます。 アップロードはベスト エフォートで実行され、クラスターの動作には一切影響しません。 テレメトリの送信元になるのは、Failover Manager プライマリを実行しているノードだけです。 それ以外のノードからはテレメトリが送信されません。

  2. テレメトリは次のデータで構成されています。

-            サービスの数
-            ServiceTypes の数
-            アプリケーションの数
-            ApplicationUpgrades の数
-            FailoverUnits の数
-            InBuildFailoverUnits の数
-            UnhealthyFailoverUnits の数
-            レプリカの数
-            InBuildReplicas の数
-            StandByReplicas の数
-            OfflineReplicas の数
-            CommonQueueLength
-            QueryQueueLength
-            FailoverUnitQueueLength
-            CommitQueueLength
-            ノードの数
-            IsContextComplete: True/False
-            ClusterId: これは各クラスターについてランダムに生成される GUID です。
-            ServiceFabricVersion
-             テレメトリのアップロード元となった仮想マシンまたはコンピューターの IP アドレス


テレメトリを無効にするには、クラスターの構成ファイル内の *properties* に *enableTelemetry: false* を追加します。

<a id="previewfeatures"></a>
## <a name="preview-features-included-in-this-package"></a>このパッケージに含まれる機能の確認

なし。

>[AZURE.NOTE] [Windows Server (バージョン 5.3.204.x) 用のスタンドアロン クラスターの新しい GA バージョン](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/)では、手動でも自動でも、クラスターを今後のリリースにアップグレードすることができます。 この機能はプレビュー バージョンでは提供されていないため、GA バージョンを使用してクラスターを作成し、プレビュー クラスターからデータとアプリケーションを移行する必要があります。 この機能の詳細については、しばらく待ちください。


## <a name="next-steps"></a>次のステップ
- [スタンドアロン Windows クラスターの構成設定](service-fabric-cluster-manifest.md)
- [スタンドアロン Service Fabric クラスターでノードを追加または削除する](service-fabric-cluster-windows-server-add-remove-nodes.md)
- [Windows を実行する Azure VM を使用してスタンドアロン Service Fabric クラスターを作成する](service-fabric-cluster-creation-with-windows-azure-vms.md)
- [Windows 上のスタンドアロン クラスターを Windows セキュリティで保護する](service-fabric-windows-cluster-windows-security.md)
- [X.509 証明書を使用した Windows でのスタンドアロン クラスターの保護](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[信頼できるゾーン]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png



<!--HONumber=Oct16_HO2-->


