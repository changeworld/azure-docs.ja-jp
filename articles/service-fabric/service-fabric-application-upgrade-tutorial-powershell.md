<properties
   pageTitle="PowerShell を使用した Service Fabric アプリケーションのアップグレード | Microsoft Azure"
   description="この記事では、PowerShell による Service Fabric アプリケーションのデプロイ、コードの変更、アップグレードのロールアウトを段階的に説明します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/13/2016"
   ms.author="subramar"/>




# PowerShell を使用した Service Fabric アプリケーションのアップグレード

最も頻繁に使用され、推奨されているアップグレード方法は、監視付きローリング アップグレードです。アップグレードされているアプリケーションの正常性は、正常性ポリシーのセットに基づいて、Azure Service Fabric により監視されます。更新ドメイン (UD) 内のアプリケーションをアップグレードすると、Service Fabric は、アプリケーションの正常性を評価し、正常性ポリシーに基づいて、次の更新ドメインに進むかアップグレードが失敗かを決定します。

監視付きアプリケーション アップグレードは、マネージ API またはネイティブ API、PowerShell、および REST を使用して実行できます。Visual Studio を使用してアップグレードを実行する方法の詳細については、[Visual Studio を使用したアプリケーションのアップグレード](service-fabric-application-upgrade-tutorial.md)に関するチュートリアルを参照してください。

Service Fabric による監視付きローリング アップグレードを使用することにより、アプリケーション管理者は、アプリケーションの正常性を判断するために Service Fabric が使用する正常性評価ポリシーを構成できます。また、管理者は、正常性評価が失敗した場合 (たとえば、自動ロールバック時) に実行するアクションを構成できます。 このセクションでは、PowerShell を使用した SDK サンプルの 1 つの監視付きアップグレードについて説明します。

## 手順 1: ビジュアル オブジェクト サンプルのビルドとデプロイ


**VisualObjectsApplication** アプリケーション プロジェクトを右クリックし、[Service Fabric] メニュー項目の **[発行]** コマンドを選択することで、アプリケーションをビルドして発行します。詳細については、[Service Fabric アプリケーションのアップグレード チュートリアル](service-fabric-application-upgrade-tutorial.md)を参照してください。代わりに、PowerShell を使用して、アプリケーションをデプロイすることができます。

> [AZURE.NOTE] PowerShell で Service Fabric のコマンドを使用する前に、まず、`Connect-ServiceFabricCluster` コマンドレットを使用してクラスターに接続する必要があります。また、クラスターがローカル コンピューターにセットアップ済みになっている必要があります。「[Service Fabric 開発環境の設定](service-fabric-get-started.md)」の記事を参照してください。

Visual Studio でプロジェクトをビルドした後に、PowerShell コマンド **Copy-ServiceFabricApplicationPackage** を使用してアプリケーション パッケージを ImageStore にコピーできます。この手順の次に、**Register-ServiceFabricApplicationPackage** コマンドレットを使用して Service Fabric ランタイムにアプリケーションを登録します。最後に、**New-ServiceFabricApplication** コマンドレットを使用してアプリケーションのインスタンスを開始します。これら 3 つの手順は、Visual Studio の **[配置]** メニュー項目を使用する場合と似ています。

これで、[クラスターおよびアプリケーションを表示する Service Fabric エクスプローラー](service-fabric-visualizing-your-cluster.md) を使用できます。このアプリケーションには Web サービスが設けられており、Internet explorer のアドレス バーに「[http://localhost:8081/visualobjects](http://localhost:8081/visualobjects)」と入力して移動できます。画面上を動くフローティング ビジュアル オブジェクトが表示されます。さらに、**Get-ServiceFabricApplication** を使用すると、アプリケーションの状態を確認できます。

## 手順 2: ビジュアル オブジェクト サンプルの更新

手順 1. でデプロイされたバージョンでは、ビジュアル オブジェクトが回転しないことにお気付きになったことでしょう。アプリケーションをアップグレードして、ビジュアル オブジェクトも回転させてみましょう。

VisualObjects ソリューション内の VisualObjects.ActorService プロジェクトを選択し、StatefulVisualObjectActor.cs ファイルを開きます。そのファイル内で `MoveObject` メソッドに移動して、`this.State.Move()` をコメントにし、`this.State.Move(true)` をコメント解除します。この変更によって、サービスのアップグレード後、オブジェクトは回転するようになります。

プロジェクト **VisualObjects.ActorService** の *ServiceManifest.xml* ファイル (PackageRoot の下) を更新する必要もあります。*CodePackage* およびサービスのバージョンを 2.0 に更新し、*ServiceManifest.xml* ファイルの対応する行を更新します。マニフェスト ファイルを変更するには、ソリューションを右クリックしてから、Visual Studio の *[Edit Manifest Files (マニフェスト ファイルの編集)]* オプションを使用します。


変更後、マニフェストは次のようになります (太字部分は変更個所を示します)。

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

次に、*ApplicationManifest.xml* ファイル (**VisualObjects** ソリューションの **VisualObjects** プロジェクトの下に見つかります) を更新して、**VisualObjects.ActorService** プロジェクトのバージョン 2.0 を使用できるようにし、さらに、アプリケーションのバージョンも 1.0.0.0 から 2.0.0.0 に更新する必要があります。これで、*ApplicationManifest.xml* ファイルの対応する行は次のようになります。

```xml
<ApplicationManifestxmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```


ここで、Visual Studio で **ActorService** プロジェクトのみを選択して右クリックし、**[ビルド]** を選択してプロジェクトをビルドします (コードは変更されているので、**[すべてリビルド]** を選択すると、*ServiceManifest.xml* と *ApplicationManifest.xml* に含まれている他のプロジェクトのバージョンの更新も必要になる可能性があります)。次は、***VisualObjectsApplication*** を右クリックし、Service Fabric のメニュー、**[パッケージ]** の順に選択して、更新されたアプリケーションをパッケージ化しましょう。デプロイ可能なアプリケーション パッケージが作成されます。これで、更新されたアプリケーションをデプロイする準備は完了です。


## 手順 3: 正常性ポリシーとアップグレード パラメーターの決定

[アプリケーション アップグレード パラメーター](service-fabric-application-upgrade-parameters.md)と[アップグレード プロセス](service-fabric-application-upgrade.md)に詳しくなって、アップグレードのさまざまなパラメーター、タイムアウト、および適用されている正常性条件について理解を深めてください。このチュートリアルでは、サービスの正常性評価条件を既定 (推奨値) のままにします。つまり、すべてのサービスとインスタンスは、アップグレード後も_正常_である必要があります。

ただし、*HealthCheckStableDuration* を 60 秒間に増やします (これにより、次の更新ドメインにアップグレードが進む前に、少なくとも 20 秒間、サービスは正常な状態になります)。また、*UpgradeDomainTimeout* を 1200 秒に、*UpgradeTimeout* を 3000 秒に設定しましょう。

最後に、*UpgradeFailureAction* をロールバックに設定しましょう。これで、アップグレード中に問題が発生した場合、Service Fabric はアプリケーションを以前のバージョンにロールバックするよう要求されます。これで、アップグレードを開始 (ステップ 4) する時点で指定されているアップグレード パラメーターは、次のようになります。

FailureAction = Rollback

HealthCheckStableDurationSec = 60

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000


## 手順 4: アプリケーションのアップグレードの準備

アプリケーションはビルドされ、アップグレードの準備は完了しています。PowerShell ウィンドウを管理者として開き、「**Get-ServiceFabricApplication**」と入力すると、デプロイされているのが **VisualObjects** のアプリケーションの種類 1.0.0.0 であることが表示されます。

アプリケーション パッケージは、Service Fabric SDK を圧縮解除した次の相対パスの下に格納されています - *Samples\\Services\\Stateful\\VisualObjects\\VisualObjects\\obj\\x64\\Debug*。そのディレクトリ内に "パッケージ" フォルダーが見つかるはずです。このフォルダーにアプリケーション パッケージが格納されます。タイムスタンプを確認して、最新のビルドであることを確認します (場合によってはパスを適切に変更する必要もあります)。

次に、更新されたアプリケーション パッケージを Service Fabric ImageStore (アプリケーション パッケージが Service Fabric により保存される場所) にコピーします。*ApplicationPackagePathInImageStore* パラメーターから、Service Fabric にアプリケーション パッケージが見つかる場所が通知されます。"VisualObjects\_V2" に更新されたアプリケーションが次のコマンド付きで格納されています (場合によってはここでも適切にパスを変更する必要があります)。

```powershell
Copy-ServiceFabricApplicationPackage  -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package
-ImageStoreConnectionString fabric:ImageStore   -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

次の手順では、Service Fabric を使用してこのアプリケーションを登録します。これには、次のコマンドを使用します。

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

上記のコマンドが成功しなかった場合、すべてのサービスのリビルドが必要な可能性があります。手順 2 で説明したとおり、WebService のバージョンの更新が必要な場合もあります。

## 手順 5: アプリケーションのアップグレード開始

アプリケーションのアップグレードを開始する準備がすべて完了しました。開始するには次のコマンドを使用します。

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


アプリケーション名は、*ApplicationManifest.xml* ファイルに記述されている名前と同じにしてください。Service Fabric はこの名前を使用して、アップグレードが実行されるアプリケーションを識別します。タイムアウトを短く設定しすぎている場合、問題を示すエラー メッセージが表示されることがあります。トラブルシューティングのセクションを参照するか、タイムアウトの時間を長くします。

アプリケーションのアップグレード処理の進行状況は、Service Fabric Explorer を使用するか、PowerShell コマンド **Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects** を使用して監視できます。

数分後に、上記の PowerShell コマンドを使用した結果、すべての更新ドメインがアップグレードされた (完了) 状態になります。ブラウザー ウィンドウのビジュアル オブジェクトが回転しているはずです。

練習のために、バージョンをバージョン 2 からバージョン 3 へ、あるいは、バージョン 2 からバージョン 1 に変更および移行してみることもできます (v2 から v1 へのアップグレードも可能です)。タイムアウトと正常性ポリシーをいろいろ試して、これらに精通してください。Azure クラスターにデプロイする場合、ローカル クラスターにデプロイするときに使用するパラメーターと、使用されるパラメーターは異なります。そのため、タイムアウトを控えめに設定することをお勧めします。


## 次のステップ

[Visual Studio を使用したアプリケーションのアップグレード](service-fabric-application-upgrade-tutorial.md)に関する記事では、Visual Studio を使用してアプリケーションをアップグレードする方法について説明します。

[アップグレード パラメーター](service-fabric-application-upgrade-parameters.md)を使用して、アプリケーションのアップグレード方法を制御します。

[データのシリアル化](service-fabric-application-upgrade-data-serialization.md)の使用方法を学ぶことで、アプリケーションのアップグレードに互換性を持たせます。

[高度なトピック](service-fabric-application-upgrade-advanced.md)を参照して、アプリケーションをアップグレードするときの高度な機能の使用方法を学習します。

[アプリケーションのアップグレードのトラブルシューティング](service-fabric-application-upgrade-troubleshooting.md)に関する記事の手順を参照して、アプリケーションのアップグレードでの一般的な問題を修正します。

<!---HONumber=AcomDC_0615_2016-->