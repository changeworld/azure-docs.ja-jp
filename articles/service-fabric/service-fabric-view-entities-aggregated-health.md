<properties
   pageTitle="Azure Service Fabric のエンティティの正常性の集計を確認する方法"
   description="正常性クエリと一般クエリを通じて、Azure Service Fabric のエンティティの正常性の集計をクエリ、表示、評価する方法について説明します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/03/2015"
   ms.author="oanapl"/>

# Service Fabric の正常性レポートの確認方法
Service Fabric に正常性エンティティで構成される[正常性モデル](service-fabric-health-introduction.md)が導入されます。ここでは、システム コンポーネントやウォッチドッグで監視しているローカルの状態についてのレポートを確認できます。すべての正常性データは[正常性ストア](service-fabric-health-introduction.md#health-store)によって集計され、エンティティが正常であるかどうかが判断されます。

既定では、クラスターには、システム コンポーネントによって送信される正常性レポートが事前設定されます。「[トラブルシューティングのためのシステム正常性レポートの使用](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)」を参照してください。

Service Fabric には、エンティティの正常性の集計を取得する方法が複数用意されています。

- [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) またはその他の視覚化ツール

- 正常性クエリ (Powerhsell/API/REST を使用)

- 正常性をプロパティの 1 つとして取得するエンティティの一覧を返す一般クエリ (Powershell/API/REST を使用)

これらのオプションについて、**5 つのノード**があるローカル クラスターを使用して説明しましょう。**fabric:/System** アプリケーション (最初から使用可) の横にいくつかのアプリケーションがデプロイされていますが、そのうちの 1 つが **fabric:/WordCount** です。このアプリケーションには、7 のレプリカで構成されたステートフル サービスが含まれています。ノードは 5 つのみであるため、システム コンポーネントから警告とともにパーティションがターゲット数より少ないことがフラグされます。

```xml
<Service Name="WordCount.Service">
  <StatefulService ServiceTypeName="WordCount.Service" MinReplicaSetSize="2" TargetReplicaSetSize="7">
    <UniformInt64Partition PartitionCount="1" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## Service Fabric Explorer での正常性
Service Fabric Explorer では、クラスターを視覚的に確認できます。以下の図では、次の情報を確認できます。

- プロパティ Availability について MyWatchdog からエラー イベントが報告されているため、アプリケーション **fabric:/WordCount** が "赤" (エラー) になっています。

- サービスの 1 つ **fabric:/WordCount/WordCount.Service** が "黄色" (警告) になっています。前述のように、サービスは 7 のレプリカで構成されていますが、すべてを配置することはできません (ノードは 5 つのみであるため)。ここでは確認できませんが、システム レポートが原因でサービス パーティションが "黄色" になっています。"黄色" のパーティションは "黄色" のサービスをトリガーします。

- "赤" のアプリケーションが原因で**クラスター**が "赤" になっています。

評価にはクラスター マニフェストやアプリケーション マニフェストの既定のポリシーを使用します。これらのポリシーは厳密なポリシーで、失敗は許容されません。

![ServiceFabricExplorer でのクラスターのビュー。][1]

ServiceFabricExplorer でのクラスターのビュー。

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [AZURE.NOTE][Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) の詳細をご覧ください。

## 正常性クエリ
Service Fabric では、サポート対象の各[エンティティ型](service-fabric-health-introduction.md#health-entities-and-hierarchy)について正常性クエリが公開されています。API (FabricClient.HealthManager のメソッド)、Powershell コマンドレットおよび REST を使用してアクセスできます。これらのクエリは、正常性状態の集計、エンティティに報告された正常性イベント、子の正常性状態 (該当する場合) 、エンティティが正常でない場合の異常性の評価などのエンティティの正常性についての完全な情報を返します。

> [AZURE.NOTE]正常性エンティティは、正常性ストアに完全に設定されたとき、つまりエンティティにシステム レポートがあり、エンティティがアクティブな (削除されていない) 状態で、階層チェーン上の親エンティティにシステム レポートがある場合に、ユーザーに返されます。これらの条件が満たされていないと、正常性クエリでは例外が返され、エンティティが返されない理由が表示されます。

正常性クエリは、エンティティの識別子を渡すことを要求します。エンティティの識別子はエンティティの型によって異なります。また、オプションで正常性ポリシーのパラメーターを指定できます。指定しない場合は、クラスター マニフェストやアプリケーション マニフェストの[正常性ポリシー](service-fabric-health-introduction.md#health-policies)を評価に使用します。正常性クエリにはフィルターも適用できます。適用すると、指定したフィルターに該当する子やイベントのみが返されます。

> [AZURE.NOTE]出力フィルターはサーバー側に適用されるため、応答されるメッセージのサイズが減少します。フィルターはクライアント側に適用するよりも、返されたデータの数を制限するために適用することをお勧めします。

エンティティの正常性には、次の情報が含まれています。

- エンティティの正常性状態の集計。これはエンティティの正常性レポート、子の正常性状態 (該当する場合)、および正常性ポリシーに基づいて、正常性ストアによって計算されます。[エンティティの正常性評価](service-fabric-health-introduction.md#entity-health-evaluation)の詳細をご覧ください。  

- エンティティの正常性イベント。

- 子が存在するエンティティについては、すべての子の正常性状態のコレクション。正常性状態にはエンティティの識別子と正常性状態の集計が含まれます。子の完全な正常性を取得するには、子の識別子を渡すことで子のエンティティ型に対して正常性クエリを呼び出します。

- エンティティが正常でない場合は、エンティティの状態をトリガーしたレポートを示す異常性の評価。

## クラスターの正常性の取得
クラスター エンティティの正常性を返します。アプリケーションとノード (クラスターの子) の正常性状態が含まれます。次の内容を入力します。

- [省略可能] アプリケーション マニフェストのポリシーを上書きするために使用するアプリケーションの正常性ポリシー マップと正常性ポリシー。

- [省略可能] 特定の正常性状態のイベント、ノード、アプリケーションのみを返すフィルター (例: エラーのみを返す、警告かエラーを返すなど)。

### API
クラスターの正常性を取得するには、FabricClient を作成し、HealthManager で GetClusterHealthAsync メソッドを呼び出します。

次のコードは、クラスターの正常性を取得します。

```csharp
ClusterHealth clusterHealth = fabricClient.HealthManager.GetClusterHealthAsync().Result;
```

次のコードは、カスタム クラスターの正常性ポリシーとノードおよびアプリケーションのフィルターを使用してクラスターの正常性を取得します。すべての入力データが含まれる System.Fabric.Description.ClusterHealthQueryDescription が作成されます。

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilter = (long)(HealthStateFilter.Error | HealthStateFilter.Warning)
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilter = (long)HealthStateFilter.Error
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
};
ClusterHealth clusterHealth = fabricClient.HealthManager.GetClusterHealthAsync(queryDescription).Result;
```

### Powershell
クラスターの正常性の取得するコマンドレットは Get-ServiceFabricClusterHealth です。まず、Connect-ServiceFabricCluster コマンドレットを使用してクラスターに接続します。クラスターの状態 (5 つのノード、システム アプリケーション、fabric:/WordCount) は上記のように構成します。

次のコマンドレットは、既定の正常性ポリシーを使用してクラスターの正常性を取得します。fabric:/WordCount アプリケーションが警告になっているため、正常性状態の集計は警告になります。異常性の評価と正常性の集計をトリガーした条件の詳細が表示されます。

```xml
PS C:\> Get-ServiceFabricClusterHealth

AggregatedHealthState   : Warning
UnhealthyEvaluations    :
                          Unhealthy applications: 50% (1/2), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                          Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                          Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Warning'.

                          Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                          Unhealthy partition: PartitionId='889909a3-04d6-4a01-97c1-3e9851d77d6c', AggregatedHealthState='Warning'.

                          Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

NodeHealthStates        :
                          NodeName              : Node.4
                          AggregatedHealthState : Ok

                          NodeName              : Node.2
                          AggregatedHealthState : Ok

                          NodeName              : Node.1
                          AggregatedHealthState : Ok

                          NodeName              : Node.5
                          AggregatedHealthState : Ok

                          NodeName              : Node.3
                          AggregatedHealthState : Ok

ApplicationHealthStates :
                          ApplicationName       : fabric:/CalculatorActor
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
```

次のコマンドレットは、カスタム アプリケーション ポリシーでクラスターの正常性を取得します。結果はフィルター処理され、エラーまたは警告状態のアプリケーションとノードのみを取得します。すべて正常であったため、結果としてノードは返されません。アプリケーションのフィルターが考慮されるのは fabric:/WordCount に対してのみです。カスタム ポリシーでは fabric:/WordCount アプリケーションに対する警告をエラーと認識するため、アプリケーションとノードはエラーとして評価されます。

```powershell
PS c:> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
$warningAndErrorFilter = [System.Fabric.Health.HealthStateFilter]::Warning.value__  + [System.Fabric.Health.HealthStateFilter]::Error.value__
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsHealthStateFilter $warningAndErrorFilter -NodesHealthStateFilter $warningAndErrorFilter

AggregatedHealthState   : Error
UnhealthyEvaluations    :
                          Unhealthy applications: 50% (1/2), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                          Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                          Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                          Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                          Unhealthy partition: PartitionId='889909a3-04d6-4a01-97c1-3e9851d77d6c', AggregatedHealthState='Error'.

                          Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates :
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None

```

## ノードの正常性の取得
ノード エンティティの正常性を返します。ノードで報告された正常性のイベントが含まれます。次の内容を入力します。

- [必須] ノードを識別するノード名。

- [省略可能] 正常性の評価に使用するクラスターの正常性ポリシーの設定。

- [省略可能] 特定の正常性状態のイベントのみを返すフィルター (例: エラーのみを返す、警告かエラーを返すなど)。

### API
API を使用してノードの正常性を取得するには、FabricClient を作成し、HealthManager で GetNodeHealthAsync メソッドを呼び出します。

次のコードは、指定したノード名のノードの正常性を取得します。

```csharp
NodeHealth nodeHealth = fabricClient.HealthManager.GetNodeHealthAsync(nodeName).Result;
```

次のコードは、System.Fabric.Description.NodeHealthQueryDescription を通じてイベント フィルターとカスタム ポリシーを渡し、指定したノード名のノードの正常性を取得します。

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)HealthStateFilter.Warning },
};

NodeHealth nodeHealth = fabricClient.HealthManager.GetNodeHealthAsync(queryDescription).Result;
```

### Powershell
ノードの正常性を取得するコマンドレットは Get-ServiceFabricNodeHealth です。まず、Connect-ServiceFabricCluster コマンドレットを使用してクラスターに接続します。次のコマンドレットは、既定の正常性ポリシーを使用してノードの正常性を取得します。

```powershell
PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1

NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM
```

次のコマンドレットは、クラスター内のすべてのノードの正常性を取得します。

```powershell
PS C:\> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
Node.4                      Ok
Node.2                      Ok
Node.1                      Ok
Node.5                      Ok
Node.3                      Ok
```

## アプリケーションの正常性の取得
アプリケーション エンティティの正常性を返します。デプロイされたアプリケーションとサービスの子の正常性状態が含まれます。次の内容を入力します。

- [必須] アプリケーションを識別するアプリケーション名 (URI)

- [省略可能] アプリケーション マニフェストのポリシーを上書きするために使用するアプリケーションの正常性ポリシー。

- [省略可能] 特定の正常性状態のイベント、サービス、デプロイされたアプリケーションのみを返すフィルター (例: エラーのみを返す、警告かエラーを返すなど)。

### API
アプリケーションの正常性を取得するには、FabricClient を作成し、HealthManager で GetApplicationHealthAsync メソッドを呼び出します。

次のコードは、指定のアプリケーション名 URI のアプリケーションの正常性を取得します。

```csharp
ApplicationHealth applicationHealth = fabricClient.HealthManager.GetApplicationHealthAsync(applicationName).Result;
```

次のコードは、System.Fabric.Description.ApplicationHealthQueryDescription を通じてフィルターとカスタム ポリシーを指定し、指定したアプリケーション名 URI のアプリケーションの正常性を取得します。

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilter = (long)warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilter = (long)warningAndErrors },
};

ApplicationHealth applicationHealth = fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription).Result;
```

### Powershell
アプリケーションの正常性を取得するコマンドレットは Get-ServiceFabricApplicationHealth です。まず、Connect-ServiceFabricCluster コマンドレットを使用してクラスターに接続します。

次のコマンドレットは、fabric:/WordCount アプリケーションの正常性を返します。

```powershell
PS c:> Get-ServiceFabricApplicationHealth fabric:/WordCount

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCount.Service',
                                  MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service',
                                  AggregatedHealthState='Warning'.

                                  Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                  Unhealthy partition: PartitionId='325da69f-16d4-4418-9c30-1feaa40a072c',
                                  AggregatedHealthState='Warning'.

                                  Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning',
                                  ConsiderWarningAsError=false.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCount.WebService
                                  AggregatedHealthState : Ok

                                  ServiceName           : fabric:/WordCount/WordCount.Service
                                  AggregatedHealthState : Warning

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.5
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.1
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.3
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 2456
                                  SentAt                : 4/20/2015 9:57:06 PM
                                  ReceivedAt            : 4/20/2015 9:57:06 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/20/2015 9:57:06 PM
```

次の Powershell は、カスタム ポリシーを渡して子とイベントをフィルター処理します。

```powershell
PS C:\> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error.value__
Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesHealthStateFilter $errorFilter -EventsHealthStateFilter $errorFilter -DeployedApplicationsHealthStateFilter $errorFilter

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                                  Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                  Unhealthy partition: PartitionId='8f82daff-eb68-4fd9-b631-7a37629e08c0', AggregatedHealthState='Error'.

                                  Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCount.Service
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

## サービスの正常性の取得
サービス エンティティの正常性を返します。パーティションの正常性状態が含まれます。次の内容を入力します。

- [必須] サービスを識別するサービスの名前 (URI)
- [省略可能] アプリケーション マニフェストのポリシーを上書きするために使用するアプリケーションの正常性ポリシー。
- [省略可能] 特定の正常性状態のイベントやパーティションのみを返すフィルター (例: エラーのみを返す、警告かエラーを返すなど)。

### API
API を使用してサービスの正常性を取得するには、FabricClient を作成し、HealthManager で GetServiceHealthAsync メソッドを呼び出します。

次の例は、指定のサービス名 (URI) のサービスの正常性を取得します。

```charp
ServiceHealth serviceHealth = fabricClient.HealthManager.GetServiceHealthAsync(serviceName).Result;
```

次のコードは、System.Fabric.Description.ServiceHealthQueryDescription を通じてフィルターとカスタム ポリシーを指定し、指定したサービス名 URI のサービスの正常性を取得します。

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilter = (long)HealthStateFilter.Error },
};

ServiceHealth serviceHealth = fabricClient.HealthManager.GetServiceHealthAsync(queryDescription).Result;
```

### Powershell
サービスの正常性を取得するコマンドレットは Get-ServiceFabricServiceHealth です。まず、Connect-ServiceFabricCluster コマンドレットを使用してクラスターに接続します。

次のコマンドレットは、既定の正常性ポリシーを使用してサービスの正常性を取得します。

```powershell
PS C:\> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCount.Service


ServiceName           : fabric:/WordCount/WordCount.Service
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                        Unhealthy partition: PartitionId='8f82daff-eb68-4fd9-b631-7a37629e08c0', AggregatedHealthState='Warning'.

                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

PartitionHealthStates :
                        PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
                        AggregatedHealthState : Warning

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 4/20/2015 10:12:29 PM
                        ReceivedAt            : 4/20/2015 10:12:33 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:33 PM
```

## パーティションの正常性の取得
パーティション エンティティの正常性を返します。レプリカの正常性状態が含まれます。次の内容を入力します。

- [必須] パーティションを識別するパーティション ID (GUID)

- [省略可能] アプリケーション マニフェストのポリシーを上書きするために使用するアプリケーションの正常性ポリシー。

- [省略可能] 特定の正常性状態のイベント、レプリカのみを返すフィルター (例: エラーのみを返す、警告かエラーを返すなど)。

### API
API を使用してパーティションの正常性を取得するには、FabricClient を作成し、HealthManager で GetPartitionHealthAsync メソッドを呼び出します。省略可能なパラメーターを指定するには、System.Fabric.Description.PartitionHealthQueryDescription を作成します。

```csharp
PartitionHealth partitionHealth = fabricClient.HealthManager.GetPartitionHealthAsync(partitionId).Result;
```

### Powershell
パーティションの正常性を取得するコマンドレットは Get-ServiceFabricPartitionHealth です。まず、Connect-ServiceFabricCluster コマンドレットを使用してクラスターに接続します。

次のコマンドレットは、ワード カウント サービスのすべてのパーティションの正常性を取得します。

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCount.Service | Get-ServiceFabricPartitionHealth

PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130740415594605870
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415502123433
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605867
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605869
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605868
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 39
                        SentAt                : 4/20/2015 10:12:59 PM
                        ReceivedAt            : 4/20/2015 10:13:03 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 4/20/2015 10:13:03 PM
```

## レプリカの正常性の取得
レプリカ エンティティの正常性を返します。次の内容を入力します。

- [必須] レプリカを識別するパーティション ID (GUID) とレプリカ ID

- [省略可能] アプリケーション マニフェストのポリシーを上書きするために使用するアプリケーションの正常性ポリシーのパラメーター。

- [省略可能] 特定の正常性状態のイベントのみを返すフィルター (例: エラーのみを返す、警告かエラーを返すなど)。

### API
API を使用してレプリカの正常性を取得するには、FabricClient を作成し、HealthManager で GetReplicaHealthAsync メソッドを呼び出します。詳細なパラメーターは、System.Fabric.Description.ReplicaHealthQueryDescription で指定します。

```csharp
ReplicaHealth replicaHealth = fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId).Result;
```

### Powershell
レプリカの正常性を取得するコマンドレットは Get-ServiceFabricReplicaHealth です。まず、Connect-ServiceFabricCluster コマンドレットを使用してクラスターに接続します。

次のコマンドレットは、サービスのすべてのパーティションのプライマリ レプリカの正常性を取得します。

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCount.Service | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415502123433
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740415502802942
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## デプロイされたアプリケーションの正常性の取得
ノード エンティティにデプロイされたアプリケーションの正常性を返します。デプロイされたサービス パッケージの正常性状態が含まれます。次の内容を入力します。

- [必須] デプロイされたアプリケーションを識別するアプリケーション名 (URI) とノード名 (文字列)

- [省略可能] アプリケーション マニフェストのポリシーを上書きするために使用するアプリケーションの正常性ポリシー。

- [省略可能] 特定の正常性状態のイベント、デプロイされたサービス パッケージのみを返すフィルター (例: エラーのみを返す、警告かエラーを返すなど)。

### API
API を使用してノードにデプロイされたアプリケーションの正常性を取得するには、FabricClient を作成し、HealthManager で GetDeployedApplicationHealthAsync メソッドを呼び出します。省略可能なパラメーターを指定するには、System.Fabric.Description.DeployedApplicationHealthQueryDescription を使用します。

```csharp
DeployedApplicationHealth health = fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName)).Result;
```

### Powershell
デプロイされたアプリケーションの正常性を取得するコマンドレットは Get-ServiceFabricDeployedApplicationHealth です。まず、Connect-ServiceFabricCluster コマンドレットを使用してクラスターに接続します。アプリケーションのデプロイ先を探すには、Get-ServiceFabricApplicationHealth を実行してデプロイされたアプリケーションの子を確認します。

次のコマンドレットは、ノード Node.1 にデプロイされた fabric:/WordCount アプリケーションの正常性を返します。

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName Node.1
ApplicationName                    : fabric:/WordCount
NodeName                           : Node.1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCount.WebService
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCount.Service
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 130740415502842941
                                     SentAt                : 4/20/2015 10:12:30 PM
                                     ReceivedAt            : 4/20/2015 10:12:34 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## デプロイされたサービス パッケージの正常性の取得
デプロイされたサービス パッケージ エンティティの正常性を返します。次の内容を入力します。

- [必須] デプロイされたサービス パッケージを識別するアプリケーション名 (URI)、ノード名 (文字列)、サービス マニフェスト名 (文字列)

- [省略可能] アプリケーション マニフェストのポリシーを上書きするために使用するアプリケーションの正常性ポリシー。

- [省略可能] 特定の正常性状態のイベントのみを返すフィルター (例: エラーのみを返す、警告かエラーを返すなど)。

### API
API を使用してデプロイされたサービス パッケージの正常性を取得するには、FabricClient を作成し、HealthManager で GetDeployedServicePackageHealthAsync メソッドを呼び出します。

```csharp
DeployedServicePackageHealth health = fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName)).Result;
```

### Powershell
デプロイされたサービス パッケージの正常性を取得するコマンドレットは Get-ServiceFabricDeployedServicePackageHealth です。まず、Connect-ServiceFabricCluster コマンドレットを使用してクラスターに接続します。アプリケーションのデプロイ先を探すには、Get-ServiceFabricApplicationHealth を実行してデプロイされたアプリケーションを確認します。アプリケーション内のサービス パッケージを確認するには、Get-ServiceFabricDeployedApplicationHealth の出力でデプロイされたサービス パッケージの子を確認します。

次のコマンドレットは、ノード Node.1 にデプロイされた fabric:/WordCount アプリケーションの WordCount.Service サービス パッケージの正常性を返します。エンティティには、アクティブ化に成功したサービス パッケージとエントリー ポイント、および登録に成功したサービス型に関する System.Hosting のレポートが含まれています。

```powershell
PS C:\> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName Node.1 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCount.Service

ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCount.Service
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 130740415506383060
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 130740415506543054
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCount.Service
                        HealthState           : Ok
                        SequenceNumber        : 130740415520193499
                        SentAt                : 4/20/2015 10:12:32 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## 一般クエリ
一般クエリは、指定した型の Service Fabric エンティティの一覧を返します。API (FabricClient.QueryManager のメソッド)、Powershell コマンドレットおよび REST を通じて公開されます。これらのクエリは複数のコンポーネントからのサブクエリを集計します。そのうちの 1 つが[正常性ストア](service-fabric-health-introduction.md#health-store)で、各クエリ結果の正常性状態の集計を設定します。

> [AZURE.NOTE]一般クエリはエンティティの正常性状態の集計を返し、正常性に関する詳細なデータは含まれません。エンティティが正常でない場合は、正常性クエリを実行してイベント、子の正常性状態、異常性の評価などの正常性に関するすべての情報を取得できます。

エンティティについて一般クエリで不明な正常性状態が返された場合、エンティティに関するすべてのデータが正常性ストアに揃っていないか、正常性ストアに対するサブクエリが失敗した可能性があります (例: 通信エラー、正常性ストアが制限されていたなど)。引き続きエンティティに対して正常性クエリを実行します。サブクエリで一時的なエラーが発生していた (ネットワークの問題など) 場合、これは成功することがあります。また、正常性ストアからエンティティが公開されなかった理由の詳細を確認できます。

エンティティの正常性状態が含まれるクエリは次のとおりです。

- ノードの一覧。クラスター内のノードの一覧を返します。
  - API: FabricClient.QueryManager.GetNodeListAsync
  - Powershell: Get-ServiceFabricNode
- アプリケーションの一覧。クラスター内のアプリケーションの一覧を返します。
  - API: FabricClient.QueryManager.GetApplicationListAsync
  - Powershell: Get-ServiceFabricApplication
- サービスの一覧。アプリケーション内のサービスの一覧を返します。
  - API: FabricClient.QueryManager.GetServiceListAsync
  - Powershell: Get-ServiceFabricService
- パーティションの一覧。サービス内のパーティションの一覧を返します。
  - API: FabricClient.QueryManager.GetPartitionListAsync
  - Powershell: Get-ServiceFabricPartition
- レプリカの一覧。パーティション内のレプリカの一覧を返します。
  - API: FabricClient.QueryManager.GetReplicaListAsync
  - Powershell: Get-ServiceFabricReplica
- デプロイされたアプリケーションの一覧。ノード上にデプロイされたアプリケーションの一覧を返します。
  - API: FabricClient.QueryManager.GetDeployedApplicationListAsync
  - Powershell: Get-ServiceFabricDeployedApplication
- デプロイされたサービス パッケージの一覧。デプロイされたアプリケーション内のサービス パッケージの一覧を返します。
  - API: FabricClient.QueryManager.GetDeployedServicePackageListAsync
  - Powershell: Get-ServiceFabricDeployedApplication

### 例

次のコードは、クラスター内の正常でないアプリケーションを取得します。

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

次のコマンドレットは、fabric:/WordCount アプリケーションの詳細を取得します。正常性状態は警告になっています。

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "_WFDebugParams_" = "[{"ServiceManifestName":"WordCount.WebService","CodePackageName":"Code","EntryPointType":"Main"}]" }
```

次のコマンドレットは、正常性状態が警告になっているサービスを取得します。

```powershell
PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Warning"}

ServiceName            : fabric:/WordCount/WordCount.Service
ServiceKind            : Stateful
ServiceTypeName        : WordCount.Service
IsServiceGroup         : False
ServiceManifestVersion : 1.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Warning
```

## クラスターとアプリケーションのアップグレード
Service Fabric はクラスターとアプリケーションのアップグレードを監視して正常性を確認し、正常な状態の維持に努めます。構成されたポリシーについて異常が見つかった場合、ユーザーが操作できるようにアップグレードが一時停止されるか、自動的にロールバックされます。

**クラスター**のアップグレード中には、クラスターの異常を示す異常性の評価が含まれる、クラスターのアップグレード ステータスを取得できます。正常性の問題によりアップグレードがロールバックされると、管理者が失敗の原因を調査できるように、アップグレード ステータスに最後に問題が発生した理由が保持されます。同様に、**アプリケーション**のアップグレード中には、アプリケーションのアップグレード ステータスに異常性の評価が記録されます。

次のコードは、修正された fabric:/WordCount アプリケーションのアップグレード ステータスを示します。ウォッチドッグによってレプリカの 1 つでエラーが報告されています。正常性チェックは考慮されないため、アップグレードはロールバックされます。

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2015 5:23:26 PM
FailureTimestampUtc           : 4/21/2015 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : Node1
                                UpgradePhase        : Upgrading

                                NodeName            : Node2
                                UpgradePhase        : Upgrading

                                NodeName            : Node3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                                Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                Unhealthy partition: PartitionId='30db5be6-4e20-4698-8185-4bd7ca744020', AggregatedHealthState='Error'.

                                Unhealthy replicas: 16% (1/6), MaxPercentUnhealthyReplicasPerPartition=0%.

                                Unhealthy replica: PartitionId='30db5be6-4e20-4698-8185-4bd7ca744020', ReplicaOrInstanceId='130741105362491906', AggregatedHealthState='Error'.

                                Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

[Service Fabric アプリケーションのアップグレード](service-fabric-application-upgrade.md)の詳細をご覧ください。

## 正常性のトラブルシューティング
クラスターやアプリケーションで問題が発生した場合は必ず、そのクラスターやアプリケーションの正常性を確認して問題を特定します。異常性の評価では、現在の異常な状態をトリガーした原因の詳細を確認できます。必要な場合は、異常が発生している子エンティティにまで掘り下げて問題を特定します。

## 次のステップ
[トラブルシューティングのためのシステム正常性レポートの使用](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Service Fabric のカスタム正常性レポートの追加](service-fabric-report-health.md)

[ローカルでのサービスの監視方法と診断方法](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric アプリケーションのアップグレード](service-fabric-application-upgrade.md)
 

<!---HONumber=Oct15_HO3-->