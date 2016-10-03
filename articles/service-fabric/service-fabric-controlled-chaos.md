<properties
   pageTitle="Service Fabric クラスターでの混乱の誘発 | Microsoft Azure"
   description="フォールト挿入とクラスター分析サービス API を使用して、クラスター内の混乱を管理します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="motanv"
   manager="rsinha"
   editor="toddabel"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/19/2016"
   ms.author="motanv"/>

# Service Fabric クラスターでの制御された混乱の誘発
クラウド インフラストラクチャのような大規模な分散システムは、本質的に信頼性の低いものです。Azure Service Fabric を使用すると、開発者が、信頼性の低いインフラストラクチャ上で信頼できるサービスのコードを記述できます。堅牢なサービスを記述するには、開発者はこのような信頼性の低いインフラストラクチャに対して障害を誘発して、サービスの安定性をテストできる必要があります。

フォールト挿入とクラスター分析サービス (FAS) によって、開発者が、障害アクションを誘発してサービスをテストできます。ただし、ターゲットを特定した障害のシミュレーションには限界があります。さらにテストを実行するには、混乱を使用します。混乱により、長時間にわたり、クラスター全体で、グレースフルと非グレースフルの両方が交互に配置された連続した障害がシミュレートされます。障害の発生率と種類によって構成された混乱は、C# API または PowerShell を使用して開始または停止し、クラスターとサービスに障害を生成できます。混乱の実行中は、その時点での実行状態をキャプチャするさまざまなイベントが生成されます。たとえば、ExecutingFaultsEvent には、その反復で実行されているすべてのエラーが含まれます。また、ValidationFailedEvent には、クラスターの検証中に見つかったエラーの詳細が含まれます。GetChaosReportAsync API を呼び出すと、混乱実行のレポートを取得することができます。

## 混乱で誘発される障害
混乱により、Service Fabric クラスター全体で数か月または数年の間に発生する障害が、数時間に圧縮され生成されます。障害率の高い交互に配置された障害の組み合わせにより、通常は見過ごされるめったに発生しないケースが検出されます。この混乱を実施することで、サービスのコードの品質が大幅に向上します。混乱によって誘発される障害のカテゴリは次のとおりです。

 - ノードの再起動
 - デプロイされたコード パッケージの再起動
 - レプリカの削除
 - レプリカの再起動
 - プライマリ レプリカの移動 (構成可能)
 - セカンダリ レプリカの移動 (構成可能)

混乱は複数回反復され、反復それぞれが、一定の期間にわたる障害とクラスター検証で構成されています。クラスターが安定し、検証が成功するまでの時間は設定できます。クラスター検証で 1 つの障害が見つかると、混乱により、ValidationFailedEvent が UTC タイムスタンプと障害の詳細と共に生成され、保持されます。

たとえば、1 時間実行して、最大 3 つの障害が同時に発生するように設定された混乱のインスタンスを考えてみます。混乱は 3 つの障害を誘発してから、クラスターの正常性を検証し、StopChaosAsync API によって明示的に停止されるまで、または 1 時間が経過するまで、前述の手順で反復処理されます。反復処理中に、クラスターの状態が異常になると (つまり、設定時間内に安定した状態にならないと)、混乱によって ValidationFailedEvent が生成されます。これは、問題が発生し、さらに調査が必要になる場合があることを示します。

現在の形式では、混乱から誘発されるのは安全な障害だけです。これは、外部障害が発生しなければ、クォーラム損失またはデータ損失が起こらないことを意味します。

## 重要な構成オプション
 - **TimeToRun**: 混乱が正常に完了するまでの実行時間の合計。実行中の混乱は、TimeToRun で指定された期間が経過する前に、StopChaos API で停止できます。
 - **MaxClusterStabilizationTimeout**: 再度チェックが実行される前に、クラスターが正常になるのを待機する最大時間。この待機により、復旧中のクラスターにおける負荷が軽減します。次のチェックが実行されます
    - クラスターの正常性に問題がないかどうか
    - サービスの正常性に問題がないかどうか
    - サービス パーティションの対象となるレプリカ セットのサイズに達しているかどうか
    - InBuild レプリカが存在しないかどうか
 - **MaxConcurrentFaults**: 各イテレーションで誘発される同時実行のエラーの最大数。数値が大きいほど、より積極的な混乱となり、結果的に、より複雑なフェールオーバーと遷移の組み合わせになります。混乱により、外部エラーが存在しない場合に、この構成の値がどれだけ高くても、クォーラム損失またはデータ損失が発生しないことが保証されます。
 - **EnableMoveReplicaFaults**: プライマリ レプリカまたはセカンダリ レプリカの移動を発生させる障害を有効または無効にします。このようなエラーは、既定で無効になっています。
 - **WaitTimeBetweenIterations**: イテレーション間、つまり、障害とそれに対応する検証が一巡した後の待機時間。
 - **WaitTimeBetweenFaults**: イテレーションの 2 つの連続する障害間の待機時間。

## 混乱を実行する方法
C# のサンプル

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }

        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;
            var stabilizationTimeout = TimeSpan.FromSeconds(30.0);
            var timeToRun = TimeSpan.FromMinutes(60.0);
            var maxConcurrentFaults = 3;

            var parameters = new ChaosParameters(
                stabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun);

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            while (true)
            {
                var report = client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult();

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```
PowerShell

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRun -MaxConcurrentFaults $concurrentFaults -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    $stopped = $true
                }

                Write-Host $e
            }
        }
    }

    if($stopped -eq $true)
    {
        break
    }

    Start-Sleep -Seconds 1
}

Stop-ServiceFabricChaos
```

<!---HONumber=AcomDC_0921_2016-->