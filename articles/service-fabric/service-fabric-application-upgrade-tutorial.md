<properties
   pageTitle="Service Fabric アプリケーションのアップグレード チュートリアル"
   description="この記事のチュートリアルでは、Service Fabric アプリケーションをアップグレードする場合のエクスペリエンスについて説明します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/17/2015"
   ms.author="subramar"/>



# アプリケーション アップグレードのチュートリアル

最も頻繁に使用され、推奨されているアップグレード方法は、監視付きローリング アップグレードです。アップグレードされているアプリケーションの正常性は、正常性ポリシーのセットに基づいて、Service Fabric により監視されます。アップグレード ドメイン (UD) 内のアプリケーションをアップグレードすると、Service Fabric は、アプリケーションの正常性を評価し、正常性ポリシーに基づいて、次のアップグレード ドメインに進むかアップグレードが失敗かを決定します。監視付きアプリケーション アップグレードは、マネージ API またはネイティブ API、PowerShell、および REST を使用して実行できます。

Service Fabric による監視付きローリング アップグレードを使用することにより、アプリケーション管理者は、アプリケーションの正常性を判断するために Service Fabric が使用する正常性評価ポリシーを構成できます。さらに、管理者は、自動ロールバックなどで正常性評価が失敗した場合に実行されるアクションを構成することもできます。このセクションのチュートリアルでは、SDK サンプルの 1 つの監視付きアップグレードについて説明します。

## 手順 1: ビジュアル オブジェクト サンプルのビルドとデプロイ

これらの手順を実行するには、Visual Studio でプロジェクトを開き、[ソリューション] を右クリックして、Service Fabric のメニュー項目で [デプロイ] コマンドを選択してください。詳細については、「[Visual Studio での Service Fabric アプリケーションの管理](service-fabric-manage-application-in-visual-studio.md)」を参照してください。代わりに、PowerShell を使用することもできます。

> [AZURE.NOTE]PowerShell で Service Fabric のコマンドを使用する前に、まず、`Connect-ServiceFabricCluster` コマンドレットを使用してクラスターに接続する必要があります。また、クラスターがローカル コンピューターにセットアップ済みになっている必要があります。「[Service Fabric 開発環境の設定](service-fabric-get-started.md)」の記事を参照してください。

Visual Studio でプロジェクトをビルドした後、PowerShell コマンドの **Copy-ServiceFabricApplicationPackage** を使用してアプリケーション パッケージを ImageStore にコピーし、次に、**Register-ServiceFabricApplicationPackage** コマンドレットを使用して、アプリケーションを Service Fabric ランタイムに登録して、最後に **New-ServiceFabricApplication** コマンドレットを使用して、アプリケーションのインスタンスを開始できます。これら 3 つの手順は、Visual Studio で [デプロイ] メニュー項目を使用することに似ています。

これで、[クラスターおよびアプリケーションを表示する Service Fabric エクスプローラー](service-fabric-visualizing-your-cluster.md) を使用できます。このアプリケーションには、Internet explorer のアドレス バーに「[http://localhost:80](http://localhost:80)」と入力すると移動する Web サービスが設けられています。画面上を動くフローティング ビジュアル オブジェクトが表示されます。さらに、**Get-ServiceFabricApplication** を使用すると、アプリケーションの状態を確認できます。

## 手順 2: ビジュアル オブジェクト サンプルの更新

手順 1. でデプロイされたバージョンでは、ビジュアル オブジェクトが回転しないことにお気付きになったことでしょう。アプリケーションをアップグレードして、ビジュアル オブジェクトも回転させてみましょう。

VisualObjects ソリューション内の VisualObjects.DataService プロジェクトを選択し、プロジェクト内の VisualObjects.cs ファイルを開きます。そのファイル内で、メソッド `CreateMovedObject` に移動し、ビジュアル オブジェクトの回転を設定している行を見つけてください (`CreateMovedObject` メソッドの次の行を検索します)。`nextObject.Rotation = 1` と書かれた行をコメント アウトし、下の行をコメント解除します。コードは、変更が加えられた後、次のようになるはずです。

```c#
            //nextObject.Rotation = 1;

            // Once that's deployed, uncomment this line and upgrade the application:

             nextObject.Rotation = (nextObject.Rotation + 10) % 360;
```

プロジェクト **VisualObjects.DataService** の *ServiceManifest.xml* ファイル (PackageRoot の下) を更新する必要もあります。*CodePackage* およびサービスのバージョンを 2.0 に更新すると、*ServiceManifest.xml* ファイルの対応する行は次のようになります (強調表示されている部分が、変更個所を表しています)。

```xml
<ServiceManifestName="VisualObjects.DataService"Version="2.0"xmlns="http://schemas.microsoft.com/2011/01/fabric"xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code"Version="2.0">
```

次に、*ApplicationManifest.xml* ファイル (**VisualObjects** ソリューションの **VisualObjects** プロジェクトの下に見つかります) を更新して、**VisualObjects.DataService** プロジェクトのバージョン 2.0 が使用できるようにし、さらに、アプリケーションのバージョンも 1.0.0.0 から 2.0.0.0 に更新することが必要です。これで、*ApplicationManifest.xml* ファイルの対応する行は以下のようになっているはずです。

```xml
<ApplicationManifestxmlns:xsd="http://www.w3.org/2001/XMLSchema"xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"ApplicationTypeName="VisualObjects"ApplicationTypeVersion="2.0.0.0"xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.DataService"ServiceManifestVersion="2.0" />
```

次に、Visual Studio で **VisualObjects** プロジェクトを選択し、右クリックしてビルドを選択することによりプロジェクトをビルドします ([すべてリビルド] を選択した場合、コードが変更されることになる可能性があり、その場合は *ServiceManifest.xml* ファイルと *ApplicationManifest.xml* ファイルの **VisualObjects.WebService** プロジェクトのバージョンも更新する必要があります)。*VisualObjects* プロジェクト上で右クリックし、[Service Fabric のメニュー]、[パッケージ] の順に選択して、更新されたアプリケーションをパッケージ化しましょう。デプロイ可能なアプリケーション パッケージが作成されます。これで、更新されたアプリケーションをデプロイする準備は完了です。

## 手順 3: 正常性ポリシーとアップグレード パラメーターの決定

[アプリケーション アップグレード パラメーター](service-fabric-application-upgrade-parameters.md)と[アップグレード プロセス](service-fabric-application-upgrade.md)に精通し、アップグレードのさまざまなパラメーター、タイムアウト、および適用されている正常性条件の理解を深めましょう。このチュートリアルでは、サービスの正常性評価条件を既定 (推奨値) のままにします。これには、すべてのサービスとインスタンスが、アップグレード後_正常_である必要があります。ただし、*HealthCheckStableDuration* を 60 秒間に増やしましょう (そうすると、次のアップグレード ドメインにアップグレードが進む前に、少なくとも 20 秒間、サービスが正常な状態ができます)。また、*UpgradeDomainTimeout* を 1200 秒に、*UpgradeTimeout* を 3000 秒に設定しましょう。最後に、*UpgradeFailureAction* を [ロールバック] に設定しましょう。これで、アップグレード中に問題が発生した場合、Service Fabric はアプリケーションを以前のバージョンにロールバックするよう要求されます。これで、アップグレードを開始 (ステップ 4) する時点で指定されているアップグレード パラメーターは、次のようになります。

FailureAction = Rollback

HealthCheckStableDurationSec = 60

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## 手順 4: アプリケーションのアップグレードの準備

アプリケーションはビルドされ、アップグレードの準備は完了しています。PowerShell ウィンドウを管理者として開き、「**Get-ServiceFabricApplication**」と入力すると、デプロイされているのが **VisualObjects** のアプリケーションの種類 1.0.0.0 であることが表示されます。アプリケーション パッケージは、Service Fabric SDK を圧縮解除した次の相対パスの下に格納されています - *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*。そのディレクトリ内に "パッケージ" フォルダーが見つかるはずです。このフォルダーにアプリケーション パッケージが格納されます。タイムスタンプを確認して、最新のビルドであることを確認します (場合によってはパスを適切に変更する必要もあります)。

次に、更新されたアプリケーション パッケージを Service Fabric ImageStore (アプリケーション パッケージが Service Fabric により保存される場所) にコピーします。*ApplicationPackagePathInImageStore* パラメーターから、Service Fabric にアプリケーション パッケージが見つかる場所が通知されます。"VisualObjects_V2" に更新されたアプリケーションが次のコマンド付きで格納されています (場合によってはここでも適切にパスを変更する必要があります)。

```powershell
Copy-ServiceFabricApplicationPackage  -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package
-ImageStoreConnectionString fabric:ImageStore   -ApplicationPackagePathInImageStore "VisualObjects_V2"
```

次の手順では、Service Fabric を使用してこのアプリケーションを登録します。これには、次のコマンドを使用します。

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects_V2"
```

上記のコマンドが成功しなかった場合、すべてのサービスのリビルドが必要な可能性があります。手順 2 で説明したとおり、WebService のバージョンの更新が必要な場合もあります。

## 手順 5: アプリケーションのアップグレード開始

アプリケーションのアップグレードを開始する準備がすべて完了しました。開始するには次のコマンドを使用します。

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


アプリケーション名は *ApplicationManifest.xml* ファイルに記述されているとおりにするよう注意してください。Service Fabric はこの名前を使用して、アップグレードが実行されるアプリケーションを識別します。タイムアウトを短く設定しすぎている場合、問題を示すエラー メッセージが表示されることがあります。トラブルシューティングのセクションを参照するか、タイムアウトの時間を長くします。

アプリケーションのアップグレード処理の進行状況は、Service Fabric エクスプローラーを使用するか、次の PowerShell コマンドを使用して監視できます: **Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects**。

数分後に、上記の PowerShell コマンドを使用して、すべてのアップグレード ドメインがアップグレードされた (完了) 状態であることが表示されます。ブラウザー ウィンドウのビジュアル オブジェクトが回転しているはずです。

練習のために、バージョンをバージョン 2 からバージョン 3 へ、あるいは、バージョン 2 からバージョン 1 に変更および移行してみることもできます (v2 から v1 へのアップグレードも可能です)。タイムアウトと正常性ポリシーをいろいろ試して、これらに精通してください。Azure クラスターにデプロイする場合、ローカル クラスターにデプロイするときに使用するパラメーターと使用されるパラメーターが異なる場合があります。タイムアウトを控えめに設定することをお勧めします。


## 次のステップ

[アップグレード パラメーター](service-fabric-application-upgrade-parameters.md)

[データのシリアル化](service-fabric-application-upgrade-data-serialization.md)

[高度なトピック](service-fabric-application-upgrade-advanced.md)

[アプリケーション アップグレードのトラブルシューティング](service-fabric-application-upgrade-troubleshooting.md)
 

<!---HONumber=July15_HO4-->