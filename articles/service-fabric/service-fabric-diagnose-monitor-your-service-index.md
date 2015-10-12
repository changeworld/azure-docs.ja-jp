<properties
   pageTitle="Service Fabric サービスの診断とトラブルシューティング"
   description="Service Fabric のサービスの診断、監視、およびトラブルシューティングに役立つ概念情報とチュートリアル。"
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

# Service Fabric サービスの診断と監視
監視、検出、診断およびトラブルシューティングでは、ユーザー エクスペリエンスの中断を最小限に抑えてサービスを続行できます。詳細については、次を参照してください。

- [ローカルでのサービスの監視方法と診断方法](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
- [Service Fabric アプリケーションに対する Application Insights の設定](service-fabric-diagnostics-application-insights-setup.md)
- [アプリケーション アップグレードの障害のトラブルシューティング](service-fabric-application-upgrade-troubleshooting.md)
- [高信頼アクターの診断とパフォーマンス監視](service-fabric-reliable-actors-diagnostics.md)
- [Reliable Services の診断とパフォーマンス監視](service-fabric-reliable-services-diagnostics.md)

## クラスターのトラブルシューティング
次の情報は、ローカル開発クラスターのトラブルシューティングに役立ちます。

- [ローカル開発クラスターのセットアップをトラブルシューティングする](service-fabric-troubleshoot-local-cluster-setup.md)

## 正常性モデル
Service Fabric には、Service Fabric のエンティティのための機能豊富、柔軟で拡張可能なレポートと評価の機能が導入されています。Service Fabric コンポーネントは、追加の設定なしですべてのエンティティの正常性をレポートします。ユーザーのサービスは、ロジックに固有の情報、それ自体についてレポートされた情報、またはクラスターのその他のエンティティに関する情報により、正常性のデータを強化できます。詳細については、次を参照してください。

- [Service Fabric の正常性モニタリングの概要](service-fabric-health-introduction.md)
- [Service Fabric の正常性レポートの確認方法](service-fabric-view-entities-aggregated-health.md)
- [トラブルシューティングのためのシステム正常性レポートの使用](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Service Fabric のカスタム正常性レポートの追加](service-fabric-report-health.md)

<!---HONumber=Oct15_HO1-->