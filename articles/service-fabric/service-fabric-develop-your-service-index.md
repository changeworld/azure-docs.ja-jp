<properties
   pageTitle="Service Fabric サービスを開発する | Microsoft Azure"
   description="Reliable Actor や Reliable Services のプログラミング モデルの使用を理解できる概念的な情報とチュートリアル。"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2015"
   ms.author="ryanwi"/>

# Service Fabric サービスを開発する
このページには、Service Fabric サービスを開発できるようにする概要の記事やチュートリアルのリンクが記載されています。Service Fabric は、サービスを構築するための 2 つの高度なプログラミング モデルを提供します。すなわち、高信頼アクター API と、高信頼サービス API です。いずれも同一の Service Fabric コア上に構築されていますが、同時実行、パーティション分割、および通信の点で、簡潔性と柔軟性の釣り合いの取り方に違いがあります。アプリケーション内の特定のサービスに適切なフレームワークを選択できるように、両方のモデルを理解しておくと便利です。

- [プログラミング モデルを選択する](service-fabric-choose-framework.md)
- [Service Fabric のアクター モデルの概要](service-fabric-reliable-actors-introduction.md)
- [高信頼サービス プログラミング モデルの概要](../Service-Fabric/service-fabric-reliable-services-introduction.md)

## 高信頼アクターのプログラミング モデル
 Reliable Actors は、非同期のシングル スレッド アクター モデルを提供します。アクターとは、高いスケーラビリティを実現するためにクラスター全体に配布される状態と計算の単位を表します。信頼性アクター モデルは、基盤となる Service Fabric プラットフォームにより提供される分散ストアを活用し、可用性が高く一貫性のある状態管理をアプリケーションの開発者に提供します。詳細については、次を参照してください。

- [Reliable Actors の使用](service-fabric-reliable-actors-get-started.md)
- [アクターのライフサイクルとガベージ コレクション](service-fabric-reliable-actors-lifecycle.md)
- [Fabric アクターの Service Fabric プラットフォームの使用方法](service-fabric-reliable-actors-platform.md)
- [Azure Service Fabric のアクター型のシリアル化に関する留意事項](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)
- [Node.js と信頼性の Reliable Actors](service-fabric-node-and-reliable-actors-an-winning-combination.md)

アクターとの通信は以下で説明します。

- [Service Fabric のアクター モデルの概要](service-fabric-reliable-actors-introduction.md#actor-communication)
- [サービスとの通信](service-fabric-connect-and-communicate-with-services.md)

次の記事は、便利なデザイン パターンおよびシナリオについて説明します。

- [アクター モデルのデザイン パターン](service-fabric-reliable-actors-patterns-introduction.md)  
- [パターン: スマート キャッシュ](service-fabric-reliable-actors-pattern-smart-cache.md)
- [パターン: 分散ネットワークとグラフ](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)
- [パターン: リソースのガバナンス](service-fabric-reliable-actors-pattern-resource-governance.md)
- [パターン: ステートフル サービスの構成](service-fabric-reliable-actors-pattern-stateful-service-composition.md)
- [パターン: モノのインターネット](service-fabric-reliable-actors-pattern-internet-of-things.md)
- [パターン: 分散計算](service-fabric-reliable-actors-pattern-distributed-computation.md)
- [いくつかのアンチ パターン](service-fabric-reliable-actors-anti-patterns.md)

Reliable Actor メソッドには、ターンごとの単純な同時実行が提供されます。同時実行、タイマーと通知、および再入については、次の記事で説明します。

- [同時実行](service-fabric-reliable-actors-introduction.md#concurrency)
- [同時実行に関連するイベントとパフォーマンス カウンター](service-fabric-reliable-actors-diagnostics.md)
- [アクターの再入](service-fabric-reliable-actors-reentrancy.md)
- [アクターのタイマー](service-fabric-reliable-actors-timers-reminders.md)

Reliable Actors の構成の詳細については、次を参照してください。

- [KVSActorStateProvider 構成](../Service-Fabric/service-fabric-reliable-actors-KVSActorstateprovider-configuration.md)  
- [Reliable Actors の構成 - ReliableDictionaryActorStateProvider](../service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

Reliable Actors はイベントとパフォーマンス カウンターを出力し、サービスの診断と監視に使用することができます。

- [アクターの診断](service-fabric-reliable-actors-diagnostics.md)
- [アクターのイベント](service-fabric-reliable-actors-events.md)


## Reliable Services プログラミング モデル
Reliable Services は、重要な機能をアプリケーションに組み込むためのシンプルかつ強力な最上位レベルのプログラミング モデルを提供します。詳細については、次を参照してください。

- [Reliable Services 使用](service-fabric-reliable-services-quick-start.md)
- [アーキテクチャ](service-fabric-reliable-services-platform-architecture.md)
- [Reliable Collection](service-fabric-reliable-services-reliable-collections.md)
- [ステートフル Reliable Services の構成](../Service-Fabric/service-fabric-reliable-services-configuration.md)
- [シリアル化](../Service-Fabric/service-fabric-reliable-services-serialization.md)
- [Reliable Services プログラミング モデルの詳細な使用方法](../Service-Fabric/service-fabric-reliable-services-advanced-usage.md)

Reliable Services との通信と、クライアントがサービス エンドポイントでの検出と通信に利用できる抽象化については、次で説明しています。

- [サービスとの通信](service-fabric-connect-and-communicate-with-services.md)
- [サービス通信モデル](service-fabric-reliable-services-communication.md)
- [Reliable Services フレームワークによって提供される既定の通信スタック](service-fabric-reliable-services-communication-remoting.md)
- [Reliable Services の WCF ベースの通信スタック](service-fabric-reliable-services-communication-wcf.md)
- [OWIN 自己ホストによる Microsoft Azure Service Fabric Web API サービスの概要 (VS 2015 RC)](service-fabric-reliable-services-communication-webapi.md)

Reliable Services はイベントとパフォーマンス カウンターを出力し、サービスの診断と監視に使用することができます。

- [ステートフル Reliable Services の診断](service-fabric-reliable-services-diagnostics.md)

<!---HONumber=AcomDC_1203_2015-->