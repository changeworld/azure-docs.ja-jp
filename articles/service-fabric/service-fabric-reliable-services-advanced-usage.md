<properties
   pageTitle="Service Fabric の Reliable Service プログラミング モデルの詳細な使用方法"
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
   ms.date="06/09/2015"
   ms.author="jesseb"/>

# Reliable Services プログラミング モデルの詳細な使用方法
Service Fabric を使用すると、信頼性の高いステートレス サービスとステートフル サービスを簡単に作成および管理できます。このガイドでは、サービスの制御と柔軟性を高めるための Reliable Services プログラミング モデルの詳細な使用方法について説明します。このガイドをお読みになる前に、[Reliable Services プログラミング モデル](service-fabric-reliable-services-introduction.md)について理解しておいてください。

## ステートレス サービスの基本クラス
StatelessService 基本クラスでは CreateCommunicationListener() と RunAsync() を提供しており、大半のステートレス サービスにはこれで十分です。StatelessServiceBase クラスは、StatelessService の基盤となり、追加のサービス ライフ サイクル イベントを公開します。詳細については、[StatelessService](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.services.statelessservice.aspx) および [StatelessServiceBase](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.services.statelessservicebase.aspx) に関するページの開発者向けリファレンス ドキュメントを参照してください。

- `void OnInitialize(StatelessServiceInitializiationParameters)` OnInitialize は、Service Fabric によって最初に呼び出されるメソッドです。サービス名、パーティション ID、インスタンス ID、およびコード パッケージ情報などのサービス初期化情報が得られます。ここでは、複雑な処理を行うべきではありません。時間のかかる初期化は、OnOpenAsync で行う必要があります。

- `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)` OnOpenAsync はステートレス サービス インスタンスが使用されるときに呼び出されます。この時点で、拡張サービス初期化タスクを開始できます。

- `Task OnCloseAsync(CancellationToken)` OnCloseAsync はステートレス サービス インスタンスが正常にシャット ダウンされるときに呼び出されます。これは、サービスのコードがアップグレードされるか、サービス インスタンスが負荷分散のために移動されるか、または一時的なエラーが検出されたときに行われる可能性があります。OnCloseAsync を使用して、安全にすべてのリソースを閉じたり、バック グラウンド処理を停止したり、外部の状態の保存を完了したり、既存の接続を終了したりすることができます。

- `void OnAbort()` OnAbort はステートレス サービス インスタンスが強制的にシャット ダウンされているときに呼び出されます。これは一般に、ノードで永続的なエラーが検出されたときや Service Fabric が内部エラーのために、サービス インスタンスのライフ サイクルを確実に管理できないときに呼び出されます。

## ステートフル サービスの基本クラス
StatefulService 基本クラスは、ほとんどのステートフル サービスに十分であるはずです。ステートレス サービスと同様に、StatefulServiceBase クラスは StatefulService の基盤となり、追加のサービス ライフ サイクル イベントを公開します。さらに、カスタムの信頼性の高い状態プロバイダーを提供し、必要に応じてセカンダリで通信リスナーをサポートできます。詳細については、[StatefulService](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.services.statefulservice.aspx) および [StatefulServiceBase](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.services.statefulservicebase.aspx) に関するページの開発者向けリファレンス ドキュメントを参照してください。

- `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)` OnChangeRoleAsync は、ステートフル サービスのロールが、プライマリやセカンダリなどに変更されるときに呼び出されます。プライマリ レプリカには書き込み状態 (信頼性の高いコレクションの作成と書き込みが許可される) が指定され、セカンダリ レプリカには読み取り状態 (既存の信頼性の高いコレクションからのみ読み取ることができる) が指定されます。ロールの変更に対応して、セカンダリでの読み取り専用検証、レポート生成、またはデータ マイニングの実行などのバック グラウンド タスクを開始または更新できます。

- `IStateProviderReplica CreateStateProviderReplica()` ステートフル サービスは、信頼性の高い状態プロバイダーを備えることが期待されます。StatefulService は、信頼性の高いコレクション (辞書やキューなど) を提供する ReliableStateManager クラスを使用します。状態を自分で管理する場合に独自のプロバイダーを提供したり、組み込みのいずれかの状態プロバイダーの機能を拡張したりすることができます。

- `bool EnableCommunicationListenerOnSecondary { get; }` 既定で、通信リスナーはプライマリにのみ作成されます。StatefulService と StatefulServiceBase の両方を使用して、セカンダリに通信リスナーを作成するように、このプロパティをオーバーライドできます。読み取りが多いワークロードでのスループットを向上するため、セカンダリで読み取り専用要求を処理させたいと考える場合があります。

    > [AZURE.NOTE]ただし、セカンダリでは信頼性の高いコレクションの作成や書き込みが試行されないようにする必要があります。セカンダリでの書き込みの試行により、例外が発生しますが、これを処理しないと、レプリカが閉じられ、再度開かれます。

さらに、StatefulServiceBase は、StatelessServiceBase と同じ 4 つのライフ サイクル イベントを同じセマンティクスとユース ケースで提供します。

- `void OnInitialize(StatelessServiceInitializiationParameters)`
- `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)`
- `Task OnCloseAsync(CancellationToken)`
- `void OnAbort()`

## 次のステップ
Service Fabric に関する詳細なトピックについては、次の記事を参照してください。

- [ステートフル Reliable Services の構成](service-fabric-reliable-services-configuration.md)

- [Service Fabric の正常性の概要](../service-fabric/service-fabric-health-introduction.md)

- [トラブルシューティングのためのシステム正常性レポートの使用](../service-fabric/service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

- [配置の制約の概要](../service-fabric/service-fabric-placement-constraint.md)

- [Azure Service Fabric のステートフル サービスのレプリケーション トラフィックのセキュリティ保護](../service-fabric/service-fabric-replication-security.md)
 

<!---HONumber=July15_HO4-->