
<properties
   pageTitle="モノのインターネットの Reliable Actors | Microsoft Azure"
   description="Service Fabric Reliable Actors は、HTTPS、MQTT、AMQP などの複数のトランスポートをサポートするメッセージ システム フロントエンドを結合する、システム内の重要な構成要素です。"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/14/2015"
   ms.author="vturecek"/>

# Reliable Actors の設計パターン: モノのインターネット (IoT)
デバイスとクラウド サービス両方での技術の進歩に伴ってモノのインターネット (IoT) が新しいトレンドになり、開発者はシステムを開発の主要な構成要素に注目し始めています。次の図は、Azure Service Fabric Reliable Actors を使用して実現される重要なシナリオを示したものです。

![Service Fabric Reliable Actors の主要シナリオ][1]

Service Fabric Reliable Actors は、HTTPS、MQTT、AMQP などの複数のトランスポートをサポートするメッセージ システム フロントエンドを結合する、システム内の (中間層としての) 重要な構成要素です。フロントエンドは、個別のデバイスを表すアクターと通信します。アクターは状態を維持できるため、モデリング ストリーム (特にステートフルなストリームの処理) およびデバイスごとの集計は簡単です。データを永続化する必要がある場合、必要なときに、あるいはタイマーで簡単にフラッシュすることもできます。クイック照合のために別の変数に最新の N ビットのデータを保持しているときに可能です。

このサンプルではアクター モデルに注目する目的で、イベント/メッセージ層の詳細を意図的に省略してあることに注意してください。アクターとデバイスの通信が可能になります。基本的に 2 つのシナリオがあり、通常、組み合わされています。

* **単一または一連のデバイスからのテレメトリと状態のデータの収集とそれらの状態の維持**。何万個ものネズミ捕りがデータを送信しているところを想像してください (これは実際のカスタマー シナリオです)。このデータは、デバイスが中にネズミを捕らえたかどうかを示すくらいの基本的なものになります。このようなデータが地域ごとに集計され、1 つの地域で十分な数のネズミが捕獲されると、エンジニアはデバイスをクリーンアップするために派遣されます。ネズミ取りがアクターですか。 はい。リージョンごとのグループ アクターがアグリゲーターです。 そして、

* **単一または一連のデバイスへのデバイスの動作と構成のプッシュ**。ベンダーが消費パターンと季節的要因に基づいて異なる構成をプッシュする家庭用太陽光発電装置を考えてみてください。

## テレメトリ データとデバイスのグループ化

最初に、何万ものデバイスがグループ化されてすべてがテレメトリ データを関連付けられているグループに送信している場合を見てみましょう。次の例では、顧客は各リージョンにデバイスを展開しています。デバイスがオンになると、最初に **ActivateMe** メッセージと場所やバージョンなどを含む関連情報を送信します。デバイス ID でデバイスに関連付けられているアクターは、状態のローカル保存 (永続化されていることもあります) や、グループ アクターの登録など、デバイスの初期状態を設定します。この場合、ランダムなグループをシミュレーションに割り当てます。

初期化処理の一環として、グループ アクターまたは他のエージェントから構成データを取得することによってデバイスを構成できます。このようにデバイスは最初は「無知」ですが初期化によって「賢く」なります。これが完了すると、デバイスとアクターはテレメトリ データを送信および処理できる状態になります。

すべてのデバイスは、対応するアクターにテレメトリ情報を定期的に送信します。アクターが既にアクティブになっている場合、同じアクターが使用されます。アクティブでない場合はアクティブ化されます。この時点で、必要な場合は、安定したストアから状態を復元できます。アクターは、テレメトリ情報を受信すると、ローカル変数に格納します。

サンプルを簡単にするためにこれを行っています。実際の実装では、おそらく外部ストアに保存することになります。それにより、デバイスの健全性とパフォーマンスを監視し、診断できます。最後に、デバイス アクターが論理的に属しているグループ アクターにテレメトリ データをプッシュします。

## IoT のコード サンプル: テレメトリ

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

public class Thing : StatefulActor<ThingState>, IThing
{

    protected override Task OnActivateAsync()
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
        return Task.FromResult(true);
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

グループ レベルでは、このサンプルの目標は、グループ内のデバイスを監視し、テレメトリ データを集約してエンジニア向けのアラートを生成することです。この場合、顧客の希望は、一定の数のデバイスが故障している特定のリージョンにエンジニアを送ることです。言うまでもなく、顧客は、移動に費やされるエンジニアリング時間を最小限に抑えることによるコスト削減も望んでいます。このため、各グループ アクターは、リージョンごとに問題のあるデバイスの集計された状態を保持します。この数がしきい値に達すると、顧客は、デバイスを交換/修理するためにリージョンにエンジニアを派遣します。

どのように行われているのか見てみましょう。

## IoT のコード サンプル: グループ化と集計

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

public class ThingGroup : StatefulActor<ThingGroupState>, IThingGroup
{

    protected override Task OnActivateAsync()
    {
        State._devices = new List<ThingInfo>();
        State._faultsPerRegion = new Dictionary<string, int>();
        State._faultyDevices = new List<ThingInfo>();

        return base.OnActivateAsync();
    }

    public Task RegisterDevice(ThingInfo deviceInfo)
    {
        State._devices.Add(deviceInfo);
        return Task.FromResult(true);
    }

    public Task UnregisterDevice(ThingInfo deviceInfo)
    {
        State._devices.Remove(deviceInfo);
        return Task.FromResult(true);
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

        return Task.FromResult(true);
    }
}
```

ご覧のとおり、これは比較的簡単です。簡単なテストを実行すると、出力は次のようになります。

```
Sending an engineer to repair/replace devices in Richmond
    Device = 33 Region = Richmond Version = 4
    Device = 79 Region = Richmond Version = 5
    Device = 89 Region = Richmond Version = 3
    Device = 63 Region = Richmond Version = 2
    Device = 85 Region = Richmond Version = 4
```

デバイスをリージョンでグループ化する方がいいと思うかもしれません。地理的位置、デバイスの種類、バージョン、テナント、その他の基準など、デバイスのグループ化やパーティション化の方法は、開発者の自由です。

ここで注意すべき点は、デバイスの状態とレポート作成/分析です。アクターにファンアウト クエリを実行してレポート作成アクターを構築することは回避するべきです。不要なインスタンス化とパフォーマンスの問題という少なくとも 2 つの欠点を引き起こします。はっきりとパターンを図示したのはこのためです。レポートに関しては 2 つの方法が推奨されます。

* アグリゲーターなどのグループ レベル アクターを使用して、グループに対するビューを維持します。各アクターは、関連するデータだけをこのアクターに積極的にプッシュします。グループ レベル アクターを使用してグループ内のデバイスの状態を表示できます。

* レポート用に設計された明示的なストアを保持します。アグリゲーターはデータをバッファーに格納し、さらなるクエリと分析のためにレポート ストアにデータを定期的にプッシュできます。

## デバイスの操作
ここまでは順調です。しかしながら、これらのデバイスでの操作についてはどうでしょうか。 デバイスと同様、管理者がデバイスで操作を実行できるようにアクターは操作インターフェイスを公開できます。たとえば、ある管理者は、季節/リージョンの変化に応じて、家庭用太陽光発電デバイスのグループに新しい構成をプッシュする必要があります (もう 1 つの現実的シナリオです)。

ここでの基本的な考えは、「Thing」のアクターを利用して各デバイスを細かく制御し、「ThingGroup」アクターを利用してグループ操作を制御することです。デバイスから入ってくるテレメトリなどのデータを集めたり、構成などのデータを大量のデバイスに送信したりする場合が当てはまります。デバイス アクターの配信とアクター間のメッセージングはプラットフォームの仕事です。これは開発者に対して完全に透過的になります。

マシン間 (M2M) 通信の場合、[分散ネットワークおよびグラフ](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md) セクションで説明したハブとスポークのパターンまたはアクター間の直接的な相互作用はどちらも、問題なく動作します。M2M シナリオの場合は、次の図に示すように、デバイスのグループに対して「Directory/Index」アクターをモデル化し、相互に検出してメッセージを送信できます。

![デバイス グループのディレクトリ/インデックス アクターのモデル][2]

Service Fabric Reliable Actors モデルは、アクターの有効期間も処理します。次のように考えてみてください。常にオンにしているデバイスと時々接続するデバイスがある場合、14 時間ごとに接続するデバイスに対応するアクターをメモリに保持する必要はあるでしょうか。 Service Fabric を使用すると、必要なときに必要な場所でデバイスの状態を保存し、復元できます。

IoT の実装の重要な構成要素として Service Fabric Reliable Actors を考えるようになるお客様が増えるでしょう。

## 次のステップ
[パターン: スマート キャッシュ](service-fabric-reliable-actors-pattern-smart-cache.md)

[パターン: 分散ネットワークとグラフ](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[パターン: リソースのガバナンス](service-fabric-reliable-actors-pattern-resource-governance.md)

[パターン: ステートフル サービスの構成](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[パターン: 分散計算](service-fabric-reliable-actors-pattern-distributed-computation.md)

[いくつかのアンチパターン](service-fabric-reliable-actors-anti-patterns.md)

[Service Fabric アクターの概要](service-fabric-reliable-actors-introduction.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-internet-of-things/internet-of-things-1.png
[2]: ./media/service-fabric-reliable-actors-pattern-internet-of-things/internet-of-things-2.png

<!---HONumber=AcomDC_0121_2016-->