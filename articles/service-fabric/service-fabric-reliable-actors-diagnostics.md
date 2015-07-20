<properties
   pageTitle="Azure Service Fabric アクターの診断とパフォーマンス監視"
   description="この記事では、出力されるイベントおよびパフォーマンス カウンターを含め、Fabric アクター ランタイムの診断およびパフォーマンス監視機能について説明します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/22/2015"
   ms.author="abhisram"/>

# Fabric アクターの診断とパフォーマンス監視
Fabric アクター ランタイムは、[EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) イベントおよび[パフォーマンス カウンター](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx)を出力します。これらは、ランタイムがどのように動作するかを示し、トラブルシューティングおよびパフォーマンス監視に役立ちます。

## EventSource イベント
Fabric アクター ランタイムの EventSource 名は「Microsoft-ServiceFabric-Actors」です。このイベント ソースのイベントは、アクター アプリケーションが [Visual Studio でデバッグ](service-fabric-debugging-your-application.md)されているときに、[[診断イベント](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio)] ウィンドウに表示されます。

Service Fabric には、これらのイベントを [Application Insights](http://azure.microsoft.com/services/application-insights/) に送信するオプションも用意されています。詳細については、[Service Fabric 用の Application Insights のセットアップ](service-fabric-diagnostics-application-insights-setup.md)に関する記事を参照してください。

EventSource イベントの収集または表示に役立つツールとテクノロジのその他の例には、[PerfView](http://www.microsoft.com/download/details.aspx?id=28567)、[Azure 診断](../cloud-services-dotnet-diagnostics.md)、[セマンティック ログ記録](https://msdn.microsoft.com/library/dn774980.aspx)、[Microsoft TraceEvent ライブラリ](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent)があります。

### キーワード
Fabric アクター EventSource に属しているすべてのイベントは、1 つまたは複数のキーワードに関連付けられます。これにより、収集されたイベントをフィルター処理できます。次のキーワードのビットが定義されています。

|Bit|説明|
|---|---|
|0x1|Fabric アクター ランタイムの操作を要約する重要なイベントのセット。|
|0x2|アクター メソッドの呼び出しを記述するイベントのセット。詳細については、[アクターに関する入門のトピック](service-fabric-reliable-actors-introduction.md#actors)を参照してください。|
|0x4|アクター状態に関連するイベントのセット。詳細については、[ステートフル アクター](service-fabric-reliable-actors-introduction.md#stateful-actors)に関するトピックを参照してください。|
|0x8|アクターでのターンベースの同時実行に関連するイベントのセット。詳細については、[同時実行](service-fabric-reliable-actors-introduction.md#concurrency)に関するトピックを参照してください。|

## パフォーマンス カウンター
Fabric アクター ランタイムは、次のパフォーマンス カウンター カテゴリを定義します。

|カテゴリ|説明|
|---|---|
|Service Fabric アクター|アクター状態を保存するのにかかる時間など、Service Fabric アクターに固有のカウンター。|
|Service Fabric アクター メソッド|アクター メソッドが呼び出される頻度など、Service Fabric アクターによって実装されたメソッドに固有のカウンター。|

上記の各カテゴリには、1 つまたは複数のカウンターがあります。

パフォーマンス カウンター データの収集と表示には、Windows オペレーティング システムで既定で使用できる [Windows パフォーマンス モニター](https://technet.microsoft.com/library/cc749249.aspx) アプリケーションを使用できます。パフォーマンス カウンター データを収集して Azure テーブルにアップロードするためのもう 1 つのオプションとして、[Azure 診断](../cloud-services-dotnet-diagnostics.md)があります。

### パフォーマンス カウンター インスタンス名
多数のアクター サービスまたはアクター サービス パーティションを使用するクラスターには、多数のアクター パフォーマンス カウンター インスタンスが含まれます。パフォーマンス カウンター インスタンス名は、パフォーマンス カウンター インスタンスが関連付けられている特定の[パーティション](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors)とアクター メソッド (該当する場合) を識別するのに役立ちます。

#### `Service Fabric Actor` カテゴリ
カテゴリ `Service Fabric Actor` では、カウンター インスタンス名の形式は次のようになります。

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* - パフォーマンス カウンター インスタンスが関連付けられている Service Fabric パーティション ID の文字列表現。パーティション ID は GUID で、その文字列表現は、書式指定子 "D" を持つ [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) メソッドを使用して生成されます。

*ActorRuntimeInternalID* - 内部使用のために Fabric アクター ランタイムによって生成される 64 ビット整数の文字列表現。これは、その一意性を確保し、他のパフォーマンス カウンター インスタンス名と競合しないようにするために、パフォーマンス カウンター インスタンス名に含まれます。パフォーマンス カウンター インスタンス名のこの部分を解釈しようとしないでください。

「Service Fabric アクター」カテゴリに属するカウンターのカウンター インスタンス名の例を次に示します。

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

上記の例で、`2740af29-78aa-44bc-a20b-7e60fb783264` は Service Fabric パーティション ID の文字列表現、`635650083799324046` はランタイムの内部使用のために生成される 64 ビットの ID です。

#### `Service Fabric Actor Method` カテゴリ
カテゴリ `Service Fabric Actor Method` では、カウンター インスタンス名の形式は次のようになります。

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* - パフォーマンス カウンター インスタンスが関連付けられているアクター メソッドの名前。メソッド名の形式は、名前の読みやすさと、Windows におけるパフォーマンス カウンター インスタンス名の最大長の制約とのバランスを取る、Fabric アクター ランタイム内の何らかのロジックに基づいて決定されます。

*ActorsRuntimeMethodId* - 内部使用のために Fabric アクター ランタイムによって生成される 32 ビット整数の文字列表現。これは、その一意性を確保し、他のパフォーマンス カウンター インスタンス名と競合しないようにするために、パフォーマンス カウンター インスタンス名に含まれます。パフォーマンス カウンター インスタンス名のこの部分を解釈しようとしないでください。

*ServiceFabricPartitionID* - パフォーマンス カウンター インスタンスが関連付けられている Service Fabric パーティション ID の文字列表現。パーティション ID は GUID で、その文字列表現は、書式指定子 "D" を持つ [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) メソッドを使用して生成されます。

*ActorRuntimeInternalID* - 内部使用のために Fabric アクター ランタイムによって生成される 64 ビット整数の文字列表現。これは、その一意性を確保し、他のパフォーマンス カウンター インスタンス名と競合しないようにするために、パフォーマンス カウンター インスタンス名に含まれます。パフォーマンス カウンター インスタンス名のこの部分を解釈しようとしないでください。

「Service Fabric アクター メソッド」カテゴリに属するカウンターのカウンター インスタンス名の例を次に示します。

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

上記の例で、`ivoicemailboxactor.leavemessageasync` はメソッド名、`2` はランタイムの内部使用のために生成される 32 ビットの ID、`89383d32-e57e-4a9b-a6ad-57c6792aa521` は Service Fabric パーティション ID の文字列表現、`635650083804480486` はランタイムの内部使用のために生成される 64 ビットの ID です。

## イベントとパフォーマンス カウンターの一覧

### アクター メソッド イベントとパフォーマンス カウンター
Fabric アクター ランタイムは、[アクター メソッド](service-fabric-reliable-actors-introduction.md#actors)に関連する次のイベントを出力します。

|イベント名|イベント ID|レベル|キーワード|説明|
|---|---|---|---|---|
|ActorMethodStart|7|詳細|0x2|アクター ランタイムがアクター メソッドを呼び出そうとしています。|
|ActorMethodStop|8|詳細|0x2|アクター メソッドが実行を完了しました。つまり、アクター メソッドに対するランタイムの非同期呼び出しが返され、アクター メソッドによって返されたタスクが完了しました。|
|ActorMethodThrewException|9|警告|0x3|アクター メソッドに対するランタイムの非同期呼び出し中、またはアクター メソッドによって返されたタスクの実行中に、アクター メソッドの実行中の例外がスローされました。このイベントは、調査を必要とするアクター コードの何らかのエラーを示します。|

Fabric アクター ランタイムは、アクター メソッドの実行に関連する次のパフォーマンス カウンターを公開します。

|カテゴリ名|カウンター名|説明|
|---|---|---|
|Service Fabric アクター メソッド|呼び出し/秒|アクター サービス メソッドが呼び出される 1 秒あたりの回数|
|Service Fabric アクター メソッド|呼び出しあたりの平均時間 (ミリ秒)|アクター サービス メソッドの実行にかかった時間 (ミリ秒単位)|
|Service Fabric アクター メソッド|スローされた例外の数/秒|アクター サービス メソッドが例外をスローした 1 秒あたりの回数|

### 同時実行イベントとパフォーマンス カウンター
Fabric アクター ランタイムは、[同時実行](service-fabric-reliable-actors-introduction.md#concurrency)に関連する次のイベントを出力します。

|イベント名|イベント ID|レベル|キーワード|説明|
|---|---|---|---|---|
|ActorMethodCallsWaitingForLock|12|詳細|0x8|このイベントは、アクター内の新しい各ターンの開始時に記述されます。ターンベースの同時実行を強制するアクターごとのロックを取得するために待機している、保留中のアクター呼び出しの数が含まれています。|

Fabric アクター ランタイムは、同時実行に関連する次のパフォーマンス カウンターを公開します。

|カテゴリ名|カウンター名|説明|
|---|---|---|
|Service Fabric アクター|アクター ロックを待機しているアクター呼び出しの数|ターンベースの同時実行を強制するアクターごとのロックを取得するために待機している、保留中のアクター呼び出しの数。|

### アクター状態管理イベントとパフォーマンス カウンター
Fabric アクター ランタイムは、[アクター状態管理](service-fabric-reliable-actors-introduction.md#actor-state-management)に関連する次のイベントを出力します。

|イベント名|イベント ID|レベル|キーワード|説明|
|---|---|---|---|---|
|ActorSaveStateStart|10|詳細|0x4|アクター ランタイムが、アクター状態を保存しようとしています。|
|ActorSaveStateStop|11|詳細|0x4|アクター ランタイムが、アクター状態の保存を完了しました。|

Fabric アクター ランタイムは、アクター状態管理に関連する次のパフォーマンス カウンターを公開します。

|カテゴリ名|カウンター名|説明|
|---|---|---|
|Service Fabric アクター|状態保存操作あたりの平均時間 (ミリ秒)|アクター状態の保存にかかった時間 (ミリ秒単位)|

### ステートレス アクター インスタンスに関連するイベント
Fabric アクター ランタイムは、[ステートレス アクター インスタンス](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-stateless-actors)に関連する次のイベントを出力します。

|イベント名|イベント ID|レベル|キーワード|説明|
|---|---|---|---|---|
|ServiceInstanceOpen|3|情報|0x1|ステートレス アクター インスタンスが開きました。これは、このパーティションのアクターが、このインスタンス内で (および、おそらくその他のインスタンス内でも) 作成できることを意味します。|
|ServiceInstanceClose|4|情報|0x1|ステートレス アクター インスタンスが閉じました。これは、このパーティションのアクターが、このインスタンス内で作成されなくなったことを意味します。このインスタンス内で既に作成されているアクターに、新しい要求は配信されません。アクターは、処理中のすべての要求が完了したら破棄されます。|

### ステートフル アクター レプリカに関連するイベント
Fabric アクター ランタイムは、[ステートフル アクター レプリカ](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-stateful-actors)に関連する次のイベントを出力します。

|イベント名|イベント ID|レベル|キーワード|説明|
|---|---|---|---|---|
|ReplicaChangeRoleToPrimary|1|情報|0x1|ステートフル アクター レプリカのロールがプライマリに変わりました。これは、このパーティションのアクターが、このレプリカ内で作成されることを意味します。|
|ReplicaChangeRoleFromPrimary|2|情報|0x1|ステートフル アクター レプリカのロールがプライマリ以外に変わりました。これは、このパーティションのアクターが、このレプリカ内で作成されなくなったことを意味します。このレプリカ内で既に作成されているアクターに、新しい要求は配信されません。アクターは、処理中のすべての要求が完了したら破棄されます。|

### アクターのアクティブ化イベントと非アクティブ化イベント
Fabric アクター ランタイムは、[アクターのアクティブ化と非アクティブ化](service-fabric-reliable-actors-lifecycle.md)に関連する次のイベントを出力します。

|イベント名|イベント ID|レベル|キーワード|説明|
|---|---|---|---|---|
|ActorActivated|5|情報|0x1|アクターがアクティブ化されました。|
|ActorDeactivated|6|情報|0x1|アクターが非アクティブ化されました。|
 

<!---HONumber=July15_HO2-->