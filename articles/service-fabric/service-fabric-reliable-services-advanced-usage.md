<properties
   pageTitle="Reliable Services プログラミング モデルの詳細な使用方法 | Microsoft Azure"
   description="サービスの柔軟性を高めるための Service Fabric の Reliable Services プログラミング モデルの詳細な使用方法について説明します。"
   services="Service-Fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/28/2016"
   ms.author="jesseb"/>

# Reliable Services プログラミング モデルの詳細な使用方法
Azure Service Fabric により、信頼性の高いステートレス サービスとステートフル サービスの作成と管理が簡素化されます。このガイドでは、サービスの制御と柔軟性を高めるための Reliable Services プログラミング モデルの詳細な使用方法について説明します。このガイドをお読みになる前に、[Reliable Services プログラミング モデル](service-fabric-reliable-services-introduction.md)について理解しておいてください。

## ステートレス サービスの基本クラス
StatelessService 基本クラスでは RunAsync() と CreateServiceInstanceListeners() を提供しており、大半のステートレス サービスにはこれで十分です。StatelessServiceBase クラスは、StatelessService の基盤となり、追加のサービス ライフ サイクル イベントを公開します。制御の強化または柔軟性の向上を必要とする場合は、StatelessServiceBase から派生することができます。詳細については、[StatelessService](https://msdn.microsoft.com/library/microsoft.servicefabric.services.runtime.statelessservice.aspx) および [StatelessServiceBase](https://msdn.microsoft.com/library/microsoft.servicefabric.services.runtime.statelessservicebase.aspx) に関するページの開発者向けリファレンス ドキュメントを参照してください。

- `void OnInitialize(StatelessServiceInitializiationParameters)` OnInitialize は、Service Fabric によって最初に呼び出されるメソッドです。サービス名、パーティション ID、インスタンス ID、コード パッケージ情報など、サービス初期化情報が提供されます。ここでは、複雑な処理を行うべきではありません。時間のかかる初期化は、OnOpenAsync で行う必要があります。

- `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)` OnOpenAsync はステートレス サービス インスタンスが使用されるときに呼び出されます。この時点で、拡張サービス初期化タスクを開始できます。

- `Task OnCloseAsync(CancellationToken)` OnCloseAsync は、ステートレス サービス インスタンスが正常にシャットダウンされるときに呼び出されます。これは、サービスのコードがアップグレードされるか、サービス インスタンスが負荷分散のために移動されるか、または一時的なエラーが検出されたときに行われる可能性があります。OnCloseAsync を使用して、安全にすべてのリソースを閉じたり、バック グラウンド処理を停止したり、外部の状態の保存を完了したり、既存の接続を終了したりすることができます。

- `void OnAbort()` OnAbort は、ステートレス サービス インスタンスが強制的にシャットダウンされているときに呼び出されます。これは一般に、ノードで永続的なエラーが検出されたときや Service Fabric が内部エラーのために、サービス インスタンスのライフ サイクルを確実に管理できないときに呼び出されます。

## ステートフル サービスの基本クラス
StatefulService 基本クラスは、ほとんどのステートフル サービスに十分であるはずです。ステートレス サービスと同様に、StatefulServiceBase クラスは StatefulService の基盤となり、追加のサービス ライフ サイクル イベントを公開します。また、このクラスを使用して、カスタムの信頼性の高い状態プロバイダーを提供し、必要に応じてセカンダリで通信リスナーをサポートできます。制御の強化または柔軟性の向上を必要とする場合は、StatefulServiceBase から派生することができます。詳細については、[StatefulService](https://msdn.microsoft.com/library/microsoft.servicefabric.services.runtime.statefulservice.aspx) および [StatefulServiceBase](https://msdn.microsoft.com/library/microsoft.servicefabric.services.runtime.statefulservicebase.aspx) に関するページの開発者向けリファレンス ドキュメントを参照してください。

- `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)` OnChangeRoleAsync は、ステートフル サービスのロールが、プライマリやセカンダリなどに変更されるときに呼び出されます。プライマリ レプリカには書き込み状態が与えられます (Reliable Collection の作成と Reliable Collection への書き込みが可能)。セカンダリ レプリカには読み取り状態が与えられます (既存の Reliable Collection からの読み取りのみが可能)。ロールの変更に対応して、バックグラウンド タスク (セカンダリでの読み取り専用検証、レポート生成、またはデータ マイニングの実行など) を開始または更新できます。

- `IStateProviderReplica CreateStateProviderReplica()` ステートフル サービスは、信頼性の高い状態プロバイダーを備えることが期待されます。StatefulService は、信頼性の高いコレクション (辞書やキューなど) を提供する ReliableStateManager クラスを使用します。このメソッドを上書きし、ReliableStateManager クラスを構成できます。その場合、ReliableStateManagerConfiguration をそのコンストラクターに渡します。これにより、カスタムの状態シリアライザーの提供、データが失われたときの動作の指定、レプリケーター/状態プロバイダーの構成を行うことができるようになります。

さらに、StatefulServiceBase は、StatelessServiceBase と同じ 4 つのライフ サイクル イベントを同じセマンティクスとユース ケースで提供します。

- `void OnInitialize(StatefulServiceInitializiationParameters)`
- `Task OnOpenAsync(IStatefulServicePartition, CancellationToken)`
- `Task OnCloseAsync(CancellationToken)`
- `void OnAbort()`

## 次のステップ
Service Fabric に関するさらに高度なトピックについては、次の記事をご覧ください。

- [ステートフル Reliable Services の構成](service-fabric-reliable-services-configuration.md)

- [Service Fabric の正常性の概要](service-fabric-health-introduction.md)

- [トラブルシューティングのためのシステム正常性レポートの使用](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

- [配置の制約の概要](service-fabric-placement-constraint.md)

<!---HONumber=AcomDC_0204_2016-->