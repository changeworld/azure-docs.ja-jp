<properties
   pageTitle="イベント トレーによるトラブルシューティング | Microsoft Azure"
   description="Microsoft Azure Service Fabric でサービスをデプロイするときに発生するお問い合わせの多い問題について説明します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="mattrowmsft"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/31/2016"
   ms.author="mattrow"/>


# Azure Service Fabric でサービスをデプロイするときの一般的な問題のトラブルシューティング

開発者のコンピューターでサービスを実行している場合、[Visual Studio のデバッグ ツール](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)は簡単に使用できます。リモート クラスターの場合は、[正常性レポート](service-fabric-view-entities-aggregated-health.md)から開始することをお勧めします。これらのレポートには、PowerShell や [SFX](service-fabric-visualizing-your-cluster.md) を介してアクセスするのが最も簡単な方法です。この記事では、ユーザーがリモート クラスターのデバッグを実行しており、これらのツールのいずれかの使用方法について基本的な知識があることを前提としています。

##アプリケーションのクラッシュ
「パーティションはターゲット レプリカまたはインスタンス カウントより下です」は、サービスがクラッシュしていることを示します。サービスのクラッシュが発生している場所を特定するには、もう少し調査する必要があります。大きな規模で実行している場合、一番の味方はよく練られたトレースのセットです。これらのトレースを収集し、トレースの表示と検索に [Elastic Search](service-fabric-diagnostic-how-to-use-elasticsearch.md) などのソリューションを使用する場合は、[Azure Diagnostics](service-fabric-diagnostics-how-to-setup-wad.md) を使ってみることをお勧めします。

![SFX パーティションの正常性](./media/service-fabric-diagnostics-troubleshoot-common-scenarios/crashNewApp.png)

###サービスまたはアクターの初期化中
サービス タイプの初期化前の例外はすべて、プロセスのクラッシュの原因となります。この種のクラッシュでは、アプリケーション イベント ログにサービスからのエラーが表示されます。これらはサービスが初期化される前に最もよく見られる例外です。

***System.IO.FileNotFoundException***

このエラーは、多くの場合、アセンブリの依存関係がないために発生します。Visual Studio の CopyLocal プロパティまたは ノードのグローバル アセンブリ キャッシュを確認します。

***System.Runtime.InteropServices.COMException*** *at System.Fabric.Interop.NativeRuntime+IFabricRuntime.RegisterStatefulServiceFactory(IntPtr, IFabricStatefulServiceFactory)*
 
 これは、登録されているサービス タイプの名前が、サービス マニフェストと一致していないことを示します。

すべてのノードについてアプリケーション イベント ログを自動的にアップロードするように [Azure Diagnostics](service-fabric-diagnostics-how-to-setup-wad.md) を構成できます。

###RunAsync() または OnActivateAsync()
登録済みのサービス タイプやアクターの初期化中または実行中にクラッシュが発生する場合は、Azure Service Fabric で例外がキャッチされます。これらは「次のステップ」で説明する EventSource プロバイダーで確認できます。

## 次のステップ

Service Fabric によって提供される次の既存の診断について説明します。

* [Reliable Actors の診断](service-fabric-reliable-actors-diagnostics.md)
* [Reliable Services の診断](service-fabric-reliable-services-diagnostics.md)

<!---HONumber=AcomDC_0406_2016-->