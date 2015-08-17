
<properties
   pageTitle="モノのインターネット (IoT) に対する高信頼アクター"
   description="Service Fabric 高信頼アクターは、HTTPS、MQTT、AMQP などの複数のトランスポートをサポートするメッセージ システム フロントエンドを結合する、システム内の (中間層としての) 重要な構成要素です。フロントエンドは、個別のデバイスを表すアクターと通信します。"
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
   ms.date="08/05/2015"
   ms.author="claudioc"/>

# 高信頼アクターの設計パターン: モノのインターネット (IoT)
デバイスとクラウド サービス両方での技術の進歩に伴って IoT が新しいトレンドになり、開発者はシステムを開発の主要な構成要素に注目し始めています。次の図は、Service Fabric 高信頼アクターを使用して実現される重要なシナリオを示したものです。

![][1]

Service Fabric 高信頼アクターは、HTTPS、MQTT、AMQP などの複数のトランスポートをサポートするメッセージ システム フロントエンドを結合する、システム内の (中間層としての) 重要な構成要素です。フロントエンドは、個別のデバイスを表すアクターと通信します。アクターは状態を維持できるため、モデリング ストリーム (特にステートフルなストリームの処理) およびデバイスごとの集計は簡単です。データを保存する必要がある場合、オンデマンドまたはタイマーで簡単にフラッシュでき、一方でデータの最新の N ビットを別の変数に簡単に保持してすばやくクエリできます。このサンプルでは、イベント/メッセージ層の詳細を意図的に省略してあることに注意してください。これにより、アクターはデバイスと通信でき、アクター モデルに注目できます。通常組み合わされている基本的に 2 つのシナリオがあります。

* *単一または複数のデバイスからのテレメトリおよび状態のデータの収集と、それらの状態の維持*。データを送信している何万ものネズミ取りを間が手ください (これは、実際の顧客シナリオです)。基本的にデバイスが悪質なペストを捕獲したかどうかと同じです。データは地域ごとに集計され、1 つの地域で十分な数のネズミが捕獲されると、エンジニアはデバイスをクリーンアップするために派遣されます。ネズミ取りがアクターです。 そして、リージョンごとのグループ アクターがアグリゲーターです。 おわかりですね。

* *単一または複数のデバイスへのデバイスの動作と構成のプッシュ*。ベンダーが消費パターンと季節的要因に基づいて異なる構成をプッシュする家庭用太陽光発電装置を考えてみてください。

## テレメトリ データとデバイスのグループ化

最初に、何万ものデバイスがグループ化されてすべてがテレメトリ データを関連付けられているグループに送信している場合を見てみましょう。次の例では、顧客は各リージョンにデバイスを展開しています。デバイスがオンになると、最初に ActivateMe メッセージと、場所、バージョンなどの関連情報を送信します。すると、デバイス ID でデバイスに関連付けられているアクターは、状態のローカルな保存や (永続化されていたかもしれません)、グループ アクターの登録など、デバイスの初期状態を設定します。この場合、ランダムなグループをシミュレーションに割り当てます。

初期化処理の一環として、グループ アクターまたは他のエージェントから構成データを取得することによってデバイスを構成できます。このようにデバイスは最初はダムですが初期化によって "スマート" になります。これが完了すると、デバイスとアクターはテレメトリ データを送信および処理できる状態になります。

すべてのデバイスは、対応するアクターにテレメトリ情報を定期的に送信します。アクターが既にアクティブになっている場合、同じアクターが使用されます。そうでない場合はアクティブ化されます。この時点で、必要な場合は、安定したストアから状態を復元できます。アクターは、テレメトリ情報を受信すると、ローカル変数に格納します。サンプルを簡単にするためにこれを行っています。実際の実装では、おそらく外部ストアに保存し、操作がデバイスの正常性とパフォーマンスを監視および診断できるようにします。最後に、デバイス アクターが論理的に属しているグループ アクターにテレメトリ データをプッシュします。

## IoT のコード サンプル – テレメトリ

```csharp
public interface IThing : IActor
{
    Task ActivateMe(string region, int version);
    Task SendTelemetryAsync(ThingTelemetry telemetry);
}

[DataContract]
class ThingState
{
	[DataMember]
	public List<ThingTelemetry> _telemetry;
	[DataMember]
	public ThingInfo _deviceInfo;
	[DataMember]
	long _deviceGroupId;
}

public class Thing : Actor<ThingState>, IThing
{

    public override Task OnActivateAsync()
    {
        State._telemetry = new List<ThingTelemetry>();
        State._deviceGroupId = -1; // not activated
        return base.OnActivateAsync();
    }

    public Task SendTelemetryAsync(ThingTelemetry telemetry)
    {
        State._telemetry.Add(telemetry); // saving data at the device level
        if (State._deviceGroupId != -1)
        {
            var deviceGroup = ActorProxy.Create<IThingGroup>(State._deviceGroupId);
            return deviceGroup.SendTelemetryAsync(telemetry); // sending telemetry data for aggregation
        }
        return TaskDone.Done;
    }

    public Task ActivateMe(string region, int version)
    {
        State._deviceInfo = new ThingInfo()
        {
            DeviceId = this.GetPrimaryKeyLong(),
            Region = region,
            Version = version
        };

        // based on the info, assign a group... for demonstration we are assigning a random group
        State._deviceGroupId = new Random().Next(10, 12);

        var deviceGroup = ActorProxy.Create<IThingGroup>(State._deviceGroupId);
        return deviceGroup.RegisterDevice(State._deviceInfo);
    }
}
```

グループ レベルでの目標は、このサンプルのように、グループ内のデバイスを監視し、テレメトリ データを集約してエンジニア向けのアラートを生成することです。この場合、顧客の希望は、一定の数のデバイスが故障している特定のリージョンにエンジニアを送ることです。言うまでもなく、顧客は、移動に費やされるエンジニアリング時間を最小限に抑えることによるコスト削減を望んでいます。このため、各グループ アクターは、リージョンごとに問題のあるデバイスの集計された状態を保持します。この数がしきい値に達すると、顧客は、デバイスを交換/修理するためにリージョンにエンジニアを派遣します。どのように行われているのか見てみましょう。

## IoT のコード サンプル – グループ化と集計

```csharp
public interface IThingGroup : IActor
{
    Task RegisterDevice(ThingInfo deviceInfo);
    Task UnregisterDevice(ThingInfo deviceInfo);
    Task SendTelemetryAsync(ThingTelemetry telemetry);
}

[DataContract]
class ThingGroupState
{
    [DataMember]
    public List<ThingInfo> _devices;
    [DataMember]
    public Dictionary<string, int> _faultsPerRegion;
    [DataMember]
    public List<ThingInfo> _faultyDevices;
}

public class ThingGroup : Actor<ThingGroupState>, IThingGroup
{

    public override Task OnActivateAsync()
    {
        State._devices = new List<ThingInfo>();
        State._faultsPerRegion = new Dictionary<string, int>();
        State._faultyDevices = new List<ThingInfo>();

        return base.OnActivateAsync();
    }

    public Task RegisterDevice(ThingInfo deviceInfo)
    {
        State._devices.Add(deviceInfo);
        return TaskDone.Done;
    }

    public Task UnregisterDevice(ThingInfo deviceInfo)
    {
        State._devices.Remove(deviceInfo);
        return TaskDone.Done;
    }

    public Task SendTelemetryAsync(ThingTelemetry telemetry)
    {
        if (telemetry.DevelopedFault)
        {
            if (false == _faultsPerRegion.ContainsKey(telemetry.Region))
            {
                State._faultsPerRegion[telemetry.Region] = 0;
            }
            State._faultsPerRegion[telemetry.Region]++;
            State._faultyDevices.Add(_devices.Where(d => d.DeviceId == telemetry.DeviceId).FirstOrDefault());

            if (State._faultsPerRegion[telemetry.Region] > _devices.Count(d => d.Region == telemetry.Region) / 3)
            {
                Console.WriteLine("Sending an engineer to repair/replace devices in {0}", telemetry.Region);
                foreach(var device in State._faultyDevices.Where(d => d.Region == telemetry.Region).ToList())
                {
                    Console.WriteLine("\t{0}", device);
                }
            }
        }

        return TaskDone.Done;
    }
}
```

ごらんのようにとても簡単です。簡単なテストを実行すると、出力は次のようになります。

```
Sending an engineer to repair/replace devices in Richmond
    Device = 33 Region = Richmond Version = 4
    Device = 79 Region = Richmond Version = 5
    Device = 89 Region = Richmond Version = 3
    Device = 63 Region = Richmond Version = 2
    Device = 85 Region = Richmond Version = 4
```

ところで、デバイスをリージョンでグループ化するほうがいいと思うかもしれません。もちろん、地理的な場所、デバイスの種類、バージョン、テナントなど、デバイスをグループ化/区分する方法は全く自由です。注意すべきポイントは、デバイスの状態とレポート/分析の関係です。はっきりとパターンを図示したのはこのためです。レポート アクターを構築するためのアクターへのファンアウト クエリは避ける必要があります。不要なインスタンス化とパフォーマンスは代表的な 2 つの欠点です。レポートに関しては 2 つの方法が推奨されます。

* アグリゲーターなどのグループ レベル アクターを使用して、グループに対するビューを維持します。各アクターは、関連するデータだけをこのアクターに積極的にプッシュします。その後、このグループ レベル アクターを使用してグループ内のデバイスの状態を表示できます。

* レポート用に設計された明示的なストアを保持します。アグリゲーターはデータをバッファーに格納し、さらなるクエリと分析のためにレポート ストアにデータを定期的にプッシュできます。

## デバイスの操作
ここまでは問題ありませんが、これらのデバイスでの操作についてはどうでしょうか。 デバイスと同様、管理者がデバイスで操作を実行できるようにアクターは操作インターフェイスを公開できます。たとえば、管理者は、季節/リージョンの変化に応じて、家庭用太陽光発電デバイスのグループに新しい構成をプッシュする必要があります (もう 1 つの現実的シナリオです)。

ここでの重要なアイデアは、テレメトリなどのデバイスからの受信データを集計するのか、多数のデバイスに構成などのデータを送信するのかに関係なく、"Thing" アクターを使用して各デバイスを、また "ThingGroup" アクターを使用してグループ操作を、きめ細かく制御できることです。デバイス アクターの分布およびそれらの間のメッセージングはプラットフォームによって処理され、それは開発者に対して完全に透過的です。

マシン間 (M2M) 通信の場合、分散ネットワークおよびグラフ セクションで説明したハブとスポークのパターンまたはアクター間の直接的な相互作用はどちらも、問題なく動作します。M2M シナリオの場合は、次の図に示すように、デバイスのグループに対して "Directory/Index" アクターをモデル化し、相互に検出してメッセージを送信できます。

![][2]

Azure Service Fabric アクターは、アクターの有効期間も処理します。常に接続されているデバイスと、ときどき接続されるデバイスがあるものとします。14 時間ごとに接続するデバイスを処理するアクターをメモリ内に保持する必要があるでしょうか。 Azure Service Fabric では、デバイスの状態を保存し、必要な時と場所に復元できます。

ますます多くのユーザーが、IoT の実装の重要な構成要素として Azure Service Fabric アクターを考えるようになるはずです。

## 次のステップ
[パターン: スマート キャッシュ](service-fabric-reliable-actors-pattern-smart-cache.md)

[パターン: 分散ネットワークとグラフ](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[パターン: リソースのガバナンス](service-fabric-reliable-actors-pattern-resource-governance.md)

[パターン: ステートフル サービスの構成](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[パターン: 分散計算](service-fabric-reliable-actors-pattern-distributed-computation.md)

[いくつかのアンチ パターン](service-fabric-reliable-actors-anti-patterns.md)

[Service Fabric アクターの概要](service-fabric-reliable-actors-introduction.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-internet-of-things/internet-of-things-1.png
[2]: ./media/service-fabric-reliable-actors-pattern-internet-of-things/internet-of-things-2.png

<!---HONumber=August15_HO6-->