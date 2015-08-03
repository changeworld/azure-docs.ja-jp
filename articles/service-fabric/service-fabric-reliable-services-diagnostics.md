<properties
   pageTitle="ステートフル Reliable Services の診断"
   description="ステートフル Reliable Services の診断機能"
   services="service-fabric"
   documentationCenter=".net"
   authors="AlanWarwick"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/20/2015"
   ms.author="alanwar"/>

# ステートフル Reliable Services の診断機能
ステートフル Reliable Services の StatefulServiceBase クラスは、サービスのデバッグに使用することができる [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) イベントを出力するため、ランタイムの動作状況を理解し、トラブルシューティングに役立ちます。

## EventSource イベント
ステートフル Reliable Services の StatefulServiceBase クラスの EventSource 名は、"Microsoft-ServiceFabric-Services" です。このイベント ソースからのイベントは、サービスを [Visual Studio でデバッグしている](service-fabric-debugging-your-application.md)ときに、[[診断イベント](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio)] ウィンドウに表示されます。

Service Fabric には、これらのイベントを [Application Insights](http://azure.microsoft.com/services/application-insights/) に送信するオプションも用意されています。詳細については、[Service Fabric 用の Application Insights のセットアップ](service-fabric-diagnostics-application-insights-setup.md)に関する記事を参照してください。

EventSource イベントの収集や表示に役立つツールとテクノロジのその他の例には、[PerfView](http://www.microsoft.com/download/details.aspx?id=28567)、[Microsoft Azure 診断](../cloud-services-dotnet-diagnostics.md)、および [Microsoft TraceEvent ライブラリ](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent)があります。

## イベント

|イベント名|イベント ID|レベル|イベントの説明|
|----------|--------|-----|-----------------|
|StatefulRunAsyncInvocation|1|情報|サービスの RunAsync タスクが開始されたるときに出力されます|
|StatefulRunAsyncCancellation|2|情報|サービスの RunAsync タスクが取り消されたときに出力されます|
|StatefulRunAsyncCompletion|3|情報|サービスの RunAsync タスクが完了したときに出力されます|
|StatefulRunAsyncSlowCancellation|4|警告|サービスの RunAsync タスクが取り消しの実行に時間がかかりすぎたときに出力されます|
|StatefulRunAsyncFailure|5|エラー|サービスの RunAsync タスクが例外をスローしたときに出力されます|

## イベントの解釈

StatefulRunAsyncInvocation、StatefulRunAsyncCompletion、および StatefulRunAsyncCancellation イベントは、サービスの作成者が、サービスのライフ サイクルに加えて、サービスが開始されたとき、取り消されたとき、完了したときのタイミングを知るために役立ちます。これはサービスの問題をデバッグする場合やサービスのライフ サイクルを理解する場合に役立つことがあります。

StatefulRunAsyncSlowCancellation および StatefulRunAsyncFailure イベントはサービスの問題を示しているため、サービスの作成者はこれらのイベントに細心の注意を払う必要があります。

StatefulRunAsyncFailure は、サービスの RunAsync() タスクが例外をスローするたびに出力されます。一般に、スローされる例外は、サービスのエラーやバグを示します。さらに、例外により、サービスが失敗するため、別のノードに移動させられますが、これは、コストのかかる操作で、サービスの移動中に受信要求を遅延させる可能性があります。サービスの作成者は例外の原因を特定し、可能であればそれを軽減する必要があります。

StatefulRunAsyncSlowCancellation は、RunAsync タスクの取り消し要求に 4 秒より長くかかるたびに出力されます。サービスが、取り消しの完了に時間がかかる場合、サービスが別のノードで速やかに再開される機能に影響し、サービスの全体の可用性に影響を与える可能性があります。

<!---HONumber=July15_HO4-->