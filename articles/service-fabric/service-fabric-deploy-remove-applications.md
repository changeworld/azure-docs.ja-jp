<properties
   pageTitle="Service Fabric アプリケーションのデプロイ"
   description="Service Fabric のアプリケーションをデプロイおよび削除する方法"
   services="service-fabric"
   documentationCenter=".net"
   authors="alexwun"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/23/2015"
   ms.author="alexwun"/>

# アプリケーションをデプロイする

[アプリケーションの種類をパッケージ化][10]したら、Service Fabric クラスターにデプロイできる状態になっています。デプロイには、次の 3 つの手順が含まれます。

1. アプリケーション パッケージのアップロード
2. アプリケーションの種類の登録
3. アプリケーション インスタンスの作成

>[AZURE.NOTE]Visual Studio を使用して、ローカルの開発クラスターでアプリケーションのデプロイとデバッグを行う場合、アプリケーション プロジェクトの Scripts フォルダーにある PowerShell スクリプトを起動することで、以下に示すすべての手順が自動的に処理されます。この記事では、これらのスクリプトが実行する内容の背景を説明し、Visual Studio の外部で同じ操作を実行できるようにします。

## アプリケーション パッケージをアップロードする

アプリケーション パッケージをアップロードすると、そのパッケージは内部 Service Fabric コンポーネントがアクセスできる場所にアップロードされ、PowerShell 経由で実行できるようになります。この記事では、PowerShell コマンドを実行する前に、まず必ず **Connect-ServiceFabricCluster** を使用して Service Fabric クラスターに接続することから始めます。

必要なアプリケーション マニフェスト、サービス マニフェスト、およびコード/構成/データ パッケージを含む *MyApplicationType* という名前のフォルダーがあるものとします。次いで、**Copy-ServiceFabricApplicationPackage** コマンドでパッケージをアップロードします。次に例を示します。

~~~
PS D:\temp> dir

    Directory: D:\temp

Mode                LastWriteTime     Length Name
----                -------------     ------ ----
d----         3/19/2015   8:11 PM            MyApplicationType

PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat

PS D:\temp> Copy-ServiceFabricApplicationPackage MyApplicationType
Copy application package succeeded

PS D:\temp>
~~~

## アプリケーション パッケージを登録する

アプリケーション パッケージを登録すると、アプリケーション マニフェストで宣言されたアプリケーションの種類とバージョンを利用できるようになります。システムは、前の手順でアップロードされたパッケージの読み取り、パッケージの検証 (**Test-ServiceFabricApplicationPackage** をローカルで実行することに相当)、パッケージのコンテンツの処理、および内部システムの場所への処理済みのパッケージのコピーを実行します。

~~~
PS D:\temp> Register-ServiceFabricApplicationType MyApplicationType
Register application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp>
~~~

**Register-ServiceFabricApplicationType** コマンドは、アプリケーションのパッケージがシステムによって正常にコピーされた場合にのみ戻ります。この所要時間は、アプリケーション パッケージの内容によって異なります。**- TimeoutSec** パラメーターは、必要に応じてより長いタイムアウトを指定する場合に使用できます (既定のタイムアウトは 60 秒)。

**Get ServiceFabricApplicationType** コマンドは、正常に登録されているアプリケーションの種類のすべてのバージョンを一覧表示します。

## アプリケーションを作成する

**New-ServiceFabricApplication** コマンドを使用して正常に登録したアプリケーションの種類のバージョンを使用して、アプリケーションをインスタンス化できます。各アプリケーションの名前は、*fabric:* スキームで開始され、各アプリケーション インスタンスに対して一意でなければなりません。ターゲット アプリケーションの種類のアプリケーション マニフェストで既定のサービスが定義されている場合、それらのサービスも同時に作成されます。

~~~
PS D:\temp> New-ServiceFabricApplication fabric:/MyApp MyApplicationType AppManifestVersion1

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationStatus      : Ready
HealthState            : OK
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/MyService
ServiceKind            : Stateless
ServiceTypeName        : MyServiceType
IsServiceGroup         : False
ServiceManifestVersion : SvcManifestVersion1
ServiceStatus          : Active
HealthState            : Ok

PS D:\temp>
~~~

**Get-ServiceFabricApplication** コマンドは、正常に作成されたすべてのアプリケーション インスタンスとその全体的な状態を一覧表示します。

**Get-ServiceFabricService** コマンドは、特定のアプリケーション インスタンス内で正常に作成されたすべてのサービス インスタンスを一覧表示します。既定のサービス (指定されている場合) は、ここに表示されます。

登録されたアプリケーションの種類の任意のバージョンに対して、複数のアプリケーション インスタンスを作成できます。各アプリケーション インスタンスは分離して実行され、独自の作業ディレクトリとプロセスを使用します。

## アプリケーションの削除

アプリケーション インスタンスが不要になったときは、**Remove-ServiceFabricApplication** コマンドを使用して完全に削除できます。これによって、アプリケーションに属するすべてのサービスも自動的に削除され、すべてのサービス状態が完全に削除されます。この操作を元に戻すことはできず、アプリケーションの状態を復元することはできません。

~~~
PS D:\temp> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS D:\temp> Get-ServiceFabricApplication
PS D:\temp>
~~~

アプリケーションの種類の特定のバージョンが不要になったときは、**Unregister-ServiceFabricApplicationType** コマンドを使用して登録解除する必要があります。使用していない種類の登録を解除することで、イメージ ストアのその型のアプリケーション パッケージのコンテンツによって使用されるストレージ領域を解放します。あるアプリケーションの種類でインスタンス化されたアプリケーションがない場合、またはそれを参照している保留中のアプリケーションのアップグレードがない場合、そのアプリケーションの種類を登録解除できます。

~~~
PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp> Unregister-ServiceFabricApplicationType MyApplicationType AppManifestVersion1
Unregister application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

PS D:\temp>
~~~

<!--
## Next steps

TODO [Upgrade applications][11]
-->

## トラブルシューティング

### Copy-ServiceFabricApplicationPackage がImageStoreConnectionString を求める

Service Fabric SDK 環境には、適切な既定値を事前に設定しておく必要があります。ただし、必要に応じて、すべてのコマンドの ImageStoreConnectionString を Service Fabric のクラスターによって使用されている値と一致させます。この値は、**Get-ServiceFabricClusterManifest** コマンドを使用して取得されるクラスター マニフェストで見つかります。

~~~
PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType

cmdlet Copy-ServiceFabricApplicationPackage at command pipeline position 1
Supply values for the following parameters:
ImageStoreConnectionString:

PS D:\temp> Get-ServiceFabricClusterManifest
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]

PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType -ImageStoreConnectionString file:D:\ServiceFabric\Data\ImageStore
Copy application package succeeded

PS D:\temp>
~~~

## 次のステップ

[Service Fabric アプリケーションのアップグレード](service-fabric-application-upgrade.md)

[Service Fabric の正常性の概要](service-fabric-health-introduction.md)

[Service Fabric サービスの診断とトラブルシューティング](service-fabric-diagnose-monitor-your-service-index.md)

[Service Fabric のアプリケーションのモデル化](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md
 

<!---HONumber=Sept15_HO4-->