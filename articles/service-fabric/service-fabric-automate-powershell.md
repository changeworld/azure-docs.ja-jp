<properties
	pageTitle="PowerShell を使用した Service Fabric アプリケーション管理の自動化 | Microsoft Azure"
	description="PowerShell を使用して Service Fabric アプリケーションをデプロイ、アップグレード、テスト、および削除します。"
	services="service-fabric"
	documentationCenter=".net"
	authors="rwike77"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-fabric"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="10/16/2015"
	ms.author="ryanwi"/>

# PowerShell を使用した Service Fabric アプリケーションのデプロイ、アップグレード、テスト、および削除

この記事では、PowerShell を使用して、Service Fabric アプリケーションのデプロイ、アップグレード、削除、およびテストの一般的なタスクを自動化する方法を説明します。

## 前提条件

記事内のタスクに移行する前に必ず、ServiceFabric および ServiceFabricTestability PowerShell モジュールをインストールする[ランタイム、SDK、およびツールをインストール](service-fabric-get-started.md)してください。[PowerShell スクリプトの実行を有効化](service-fabric-get-started.md#enable-powershell-script-execution)し、[ローカル クラスターをインストールして開始](service-fabric-get-started.md#install-and-start-a-local-cluster)することで、記事の例を実行できるようになります。

この記事の例では、[HelloWorldStateful サンプル アプリケーション](https://github.com/Azure/servicefabric-samples/tree/master/samples/Services/VS2015/HelloWorldStateful)を使用します。サンプル アプリケーションのダウンロードとビルド

この記事では、PowerShell コマンドを実行する前に、まず [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/azure/mt125938.aspx) を使用して Service Fabric クラスターに接続することから始めます。

```powershell
Connect-ServiceFabricCluster
```

## タスク: Service Fabric アプリケーションのデプロイ

アプリケーションをビルドし、アプリケーションの種類をパッケージ化したら、ローカル Service Fabric クラスターにアプリケーションをデプロイできます。最初に、ソリューション エクスプ ローラーで、**HelloWorldStatefulApplication** を右クリックし、**[パッケージ]** を選択して、Visual Studio で HelloWorldStateful アプリケーションをパッケージ化します。サービス、アプリケーション マニフェスト、およびパッケージ レイアウトの情報については、「[Service Fabric でのアプリケーションのモデル化](service-fabric-application-model.md)」を参照してください。デプロイメントには、アプリケーションの種類の登録、アプリケーション インスタンスの作成、およびアプリケーション インスタンスの作成が含まれます。クラスターに新しいアプリケーションをデプロイするには、このセクションの手順を使用します。

### 手順 1: アプリケーション パッケージのアップロード
アプリケーション パッケージを ImageStore にアップロードすると、そのパッケージは内部 Service Fabric コンポーネントがアクセスできる場所に置かれます。アプリケーション パッケージには、必要なアプリケーション マニフェスト、サービス マニフェスト、およびアプリケーションとサービス インスタンスを作成するコード、構成、データのパッケージが含まれています。[Copy-ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/azure/mt125905.aspx) コマンドは、パッケージをアップロードします。次に例を示します。

```powershell
Copy-ServiceFabricApplicationPackage C:\ServiceFabricSamples\Services\VS2015\HelloWorldStateful\HelloWorldStatefulApplication\pkg\Debug -ImageStoreConnectionString fabric:ImageStore -ApplicationPackagePathInImageStore HelloWorldStateful
```

### 手順 2: アプリケーションの種類の登録
アプリケーション パッケージを登録すると、アプリケーション マニフェストで宣言されたアプリケーションの種類とバージョンを利用できるようになります。システムは、前の手順でアップロードされたパッケージの読み取り、パッケージの検証 ([Test-ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/azure/mt125950.aspx) をローカルで実行することに相当)、パッケージのコンテンツの処理、および内部システムの場所への処理済みのパッケージのコピーを実行します。[Register-ServiceFabricApplicationType](https://msdn.microsoft.com/library/azure/mt125958.aspx) コマンドレットを実行します。

```powershell
Register-ServiceFabricApplicationType HelloWorldStateful
```
クラスターに登録されているアプリケーションの種類を表示するには、次のコマンドレットを実行します。

```powershell
Get-ServiceFabricApplicationType
```

### 手順 3: アプリケーション インスタンスの作成
[New-ServiceFabricApplication](https://msdn.microsoft.com/library/azure/mt125913.aspx) コマンドを使用して正常に登録したアプリケーションの種類のバージョンを使用して、アプリケーションをインスタンス化できます。各アプリケーションの名前は、**fabric:** スキームで開始され、各アプリケーション インスタンスに対して一意でなければなりません。アプリケーションの種類の名前とアプリケーションの種類のバージョンは、ApplicationManifest.xml ファイルで宣言されます。ターゲット アプリケーションの種類のアプリケーション マニフェストで既定のサービスが定義されている場合、それらのサービスも同時に作成されます。

```powershell
New-ServiceFabricApplication fabric:/HelloWorldStatefulApplication HelloWorldStatefulApplication 1.0.0.0
```

[Get-ServiceFabricApplication](https://msdn.microsoft.com/library/azure/mt163515.aspx) コマンドは、正常に作成されたすべてのアプリケーション インスタンスとその全体的な状態を一覧表示します。[Get-ServiceFabricService](https://msdn.microsoft.com/library/azure/mt125889.aspx) コマンドは、特定のアプリケーション インスタンス内で正常に作成されたすべてのサービス インスタンスを一覧表示します。既定のサービス (指定されている場合) が表示されます。

```powershell
Get-ServiceFabricApplication

Get-ServiceFabricApplication | Get-ServiceFabricService
```

## タスク: Service Fabric アプリケーションのアップグレード

以前にデプロイされた Service Fabric アプリケーションをアップグレードすることができます。このタスクは、タスク: Service Fabric アプリケーションのデプロイでデプロイされた HelloWorldStateful アプリケーションをアップグレードします。詳細については、「[Application Upgrade (アプリケーションのアップグレード)](service-fabric-application-upgrade.md)」を参照してください。

### 手順 1: アプリケーションの更新

HelloWorldStateful サービス内のコードを変更します。

サービスのコードを更新した後、ServiceManifest.xml ファイル (HelloWorldStateful プロジェクトの PackageRoot ディレクトリにあります) で、サービスのバージョン番号をインクリメントする必要があります。マニフェストの **CodePackage** 要素を検索し、サービスのバージョンを 2.0.0.0 に変更します。ServiceManifest.xml ファイル内の対応する行は、次のようになります。

```xml
<ServiceManifest Name="HelloWorldStatefulPkg"
                 Version="2.0.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0.0.0">
```

次に、ApplicationManifest.xml ファイル (HelloWorldStateful ソリューション下の HelloWorldStatefulApplication プロジェクトの下にあります) を更新する必要があります。バージョン 2.0.0.0 の HelloWorldStatefulPkg プロジェクトを使用して、**ServiceManifestRef** 要素を更新します。**ApplicationTypeVersion** も 2.0.0.0 から 1.0.0.0 に更新します。ApplicationManifest.xml の対応する行は次のようになります。

```xml
<ApplicationManifest ApplicationTypeName="HelloWorldStatefulApplication" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<ServiceManifestRef ServiceManifestName="HelloWorldStatefulPkg" ServiceManifestVersion="2.0.0.0" />
```

これらの変更を加えた後、ファイルを保存し、HelloWorldStateful プロジェクトをリビルドします。次に、HelloWorldStatefulApplication プロジェクトを右クリックし、[Service Fabric] メニューを選択して、[パッケージ] を選択し、更新されたアプリケーションをパッケージ化します。デプロイ可能なアプリケーション パッケージが作成されます。これで、更新されたアプリケーションをデプロイする準備は完了です。

### 手順 2: 更新されたアプリケーション パッケージのコピーと登録

アプリケーションはビルドおよびパッケージ化され、アップグレードの準備は完了しています。PowerShell ウィンドウを管理者として開き、「[Get-ServiceFabricApplication](https://msdn.microsoft.com/library/azure/mt163515.aspx)」と入力すると、アプリケーションの種類 1.0.0.0 の **HelloWorldStatefulApplication** がデプロイされていることが表示されます。HelloWorldStateful のサンプルでは、アプリケーション パッケージは、*C:\\ServiceFabricSamples\\Services\\VS2015\\HelloWorldStateful\\HelloWorldStatefulApplication\\pkg\\Debug* にあります。

次に、更新されたアプリケーション パッケージを Service Fabric ImageStore (アプリケーション パッケージが Service Fabric により保存される場所) にコピーします。*ApplicationPackagePathInImageStore* パラメーターから、Service Fabric にアプリケーション パッケージが見つかる場所が通知されます。次のコマンドは、アプリケーション パッケージを、ImageStore の *HelloWorldStatefulV2* にコピーします。

```powershell
Copy-ServiceFabricApplicationPackage  -ApplicationPackagePath C:\ServiceFabricSamples\Services\VS2015\HelloWorldStateful\HelloWorldStatefulApplication\pkg\Debug -ImageStoreConnectionString fabric:ImageStore -ApplicationPackagePathInImageStore HelloWorldStatefulV2
```

次の手順では、Service Fabric を使用して、アプリケーションの新しいバージョンを登録します。これは、[Register-ServiceFabricApplicationType](https://msdn.microsoft.com/library/azure/mt125958.aspx) コマンドレットを使用して実行できます。

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore HelloWorldStatefulV2
```

このコマンドが成功しなかった場合は、手順 1 で説明したように、サービスをリビルドする必要があります。

### 手順 3: アップグレードの開始
アプリケーションのアップグレードには、さまざまなアップグレード パラメーター、タイムアウト、および正常性条件を適用できます。詳細については、「[アプリケーション アップグレードのパラメーター](service-fabric-application-upgrade-parameters.md)」と「[upgrade process (アップグレード プロセス)](service-fabric-application-upgrade.md)」を参照してください。このチュートリアルでは、サービスの正常性評価の条件は、既定値 (と推奨値) のままにします。すべてのサービスとインスタンスは、アップグレード後に_正常_である必要があります。ただし、*HealthCheckStableDuration* は 60 秒に増やします (これにより、次のアップグレード ドメインへアップグレードが進む前に、少なくとも 20 秒間、サービスが正常な状態になります)。また、*UpgradeDomainTimeout* を 1200 秒に、*UpgradeTimeout* を 3000 秒に設定します。最後に、*UpgradeFailureAction* を [ロールバック] に設定します。これにより、アップグレード中に問題が発生した場合、Service Fabric はアプリケーションを以前のバージョンにロールバックするよう要求します。

これで、[Start-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/azure/mt125975.aspx) コマンドレットを使用して、アプリケーションのアップグレードを開始することができます。

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/HelloWorldStatefulApplication -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000  -FailureAction Rollback -Monitored
```

アプリケーション名は、以前にデプロイされた v1.0.0.0 のアプリケーション名 (fabric:/HelloWorldStatefulApplication) と同じであることに注意してください。Service Fabric はこの名前を使用して、アップグレードが実行されるアプリケーションを識別します。タイムアウトを短く設定しすぎている場合、問題を示すタイムアウトのエラー メッセージが表示されることがあります。「[アプリケーションのアップグレードのトラブルシューティング](service-fabric-application-upgrade-troubleshooting.md)」を参照するか、タイムアウトの値を増加してください。

[Service Fabric エクスプ ローラー](service-fabric-visualizing-your-cluster.md) 、または [Get ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/azure/mt125988.aspx) コマンドレットを使用することで、アプリケーションのアップグレードの進行状況を監視できます。

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/HelloWorldStatefulApplication
```

数分で、[Get ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/azure/mt125988.aspx) コマンドレットにより、すべてのアップグレード ドメインがアップグレードされた (完了した) ことが示されます。

## タスク: Service Fabric アプリケーションのテスト

高品質なサービスのコードを記述するには、開発者は信頼性の低いインフラストラクチャの障害を誘発してサービスの安定性をテストできる必要があります。Service Fabric を使用すると、開発者は、混乱とフェールオーバーのテスト シナリオを使用して、障害アクションを誘発し、障害のある状態でサービスをテストできます。追加情報については、「[Testability の概要](service-fabric-testability-overview.md)」を参照してください。

### 手順 1: 混乱テスト シナリオの実行
混乱のシナリオでは、Service Fabric クラスター全体にわたる障害を生成します。このシナリオには、一般的に数か月間または数年間から数時間で発生するエラーが圧縮されています。障害率の高い交互に配置された障害の組み合わせにより、通常は見過ごされるめったに発生しないケースが検出されます。次の例では、混乱テスト シナリオを60 分間実行します。

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```

### 手順 2: フェールオーバー テスト シナリオの実行
フェールオーバー テスト シナリオは、クラスター全体ではなく、特定のサービス パーティションを対象とします。また、影響を受けていないその他のサービスは残します。このシナリオでは、ビジネス ロジックを実行しながら、一連のシミュレートされた障害とサービス検証を反復処理します。サービス検証の失敗は、さらに調査の必要な問題があることを示します。フェールオーバー テストは、一度に 1 つの障害のみを誘発します。これに対し、混乱のテストでは可能性のある複数の障害を発生させます。次の例では、fabric:/HelloWorldStatefulApplication/HelloWorldStateful サービスに対して 60 分間のフェールオーバー テストを実行します。

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/HelloWorldStatefulApplication/HelloWorldStateful"

Connect-ServiceFabricCluster

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindUniformInt64 -PartitionKey 1
```

## タスク: Service Fabric アプリケーションの削除
デプロイされたアプリケーション インスタンスの削除や、クラスターからのプロビジョニングされているアプリケーションの種類の削除、および ImageStore からのアプリケーション パッケージの削除を実行することができます。

### 手順 1: アプリケーション インスタンスの削除
アプリケーション インスタンスが不要になったときは、[Remove-ServiceFabricApplication](https://msdn.microsoft.com/library/azure/mt125914.aspx) コマンドレットを使用して完全に削除できます。これによって、アプリケーションに属するすべてのサービスも自動的に削除され、すべてのサービス状態が完全に削除されます。この操作を元に戻すことはできず、アプリケーションの状態を復元することはできません。

```powershell
Remove-ServiceFabricApplication fabric:/HelloWorldStatefulApplication
```

### 手順 2: アプリケーションの種類の登録解除
アプリケーションの種類の特定のバージョンが不要になったときは、[Unregister-ServiceFabricApplicationType](https://msdn.microsoft.com/library/azure/mt125885.aspx) コマンドレットを使用して登録解除する必要があります。使用していない種類の登録を解除することで、イメージ ストアのアプリケーション パッケージによって使用されるストレージ領域を解放します。あるアプリケーションの種類でインスタンス化されたアプリケーションがない場合、またはそれを参照している保留中のアプリケーションのアップグレードがない場合、そのアプリケーションの種類を登録解除できます。

```powershell
Unregister-ServiceFabricApplicationType HelloWorldStatefulApplication 1.0.0.0
```

### 手順 3: アプリケーション パッケージの削除
アプリケーションの種類を登録解除した後、[Remove-ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/azure/mt163532.aspx) コマンドレットを使用して、ImageStore からアプリケーション パッケージを削除することができます。

```powershell
Remove-ServiceFabricApplicationPackage -ImageStoreConnectionString fabric:ImageStore -ApplicationPackagePathInImageStore HelloWorldStateful
```

## その他のリソース
[Service Fabric アプリケーションの ライフサイクル](service-fabric-application-lifecycle.md)

[Service Fabric サービスの管理](service-fabric-manage-your-service-index.md)

[Azure Service Fabric コマンドレット](https://msdn.microsoft.com/library/azure/mt125965.aspx)

[Azure Service Fabric Testability コマンドレット](https://msdn.microsoft.com/library/azure/mt125844.aspx)

<!---HONumber=Oct15_HO4-->