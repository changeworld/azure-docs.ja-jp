# 概要
## [Service Fabric とは](service-fabric-overview.md)
## [マイクロサービスの概要](service-fabric-overview-microservices.md)
## [アプリケーションのシナリオ](service-fabric-application-scenarios.md)
## [アーキテクチャ](service-fabric-architecture.md)
## [用語集](service-fabric-technical-overview.md)

# 作業の開始
## 開発環境を設定する
### [Windows](service-fabric-get-started.md)
### [Linux](service-fabric-get-started-linux.md)
### [Mac OS](service-fabric-get-started-mac.md)
## 最初のアプリケーションを作成する
### [Windows での C# ](service-fabric-create-your-first-application-in-visual-studio.md)
### [Linux での Java](service-fabric-create-your-first-linux-application-with-java.md)
### [Linux での C#](service-fabric-create-your-first-linux-application-with-csharp.md)
## [ローカル クラスターへのアプリのデプロイ](service-fabric-get-started-with-a-local-cluster.md)

# 方法
## アプリケーションのビルド
### 基本
#### [プログラミング モデル](service-fabric-choose-framework.md)
#### [アプリケーション モデル](service-fabric-application-model.md)
#### [サービス通信](service-fabric-connect-and-communicate-with-services.md)
#### [ツール](service-fabric-manage-application-in-visual-studio.md)
#### [デバッグ](service-fabric-debugging-your-application.md)
#### 監視と診断
##### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
##### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
#### [アプリケーションのセキュリティ ポリシーの構成](service-fabric-application-runas-security.md)
#### [複数環境向けアプリケーションの構成](service-fabric-manage-multiple-environment-app-configuration.md)

### Reliable Service アプリケーション
#### [概要](service-fabric-reliable-services-introduction.md)
#### 作業開始
##### [Windows での C# ](service-fabric-reliable-services-quick-start.md)
##### [Linux での Java](service-fabric-reliable-services-quick-start-java.md)
#### [アーキテクチャ](service-fabric-reliable-services-platform-architecture.md)
#### [Reliable Collection](service-fabric-reliable-services-reliable-collections.md)
#### [Reliable Collections の使用](service-fabric-work-with-reliable-collections.md)
#### [構成](service-fabric-reliable-services-configuration.md)
#### [Notifications](service-fabric-reliable-services-notifications.md)
#### [バックアップと復元](service-fabric-reliable-services-backup-restore.md)
#### [Reliable Services を使用した通信](service-fabric-reliable-services-communication.md)
##### [ASP.NET](service-fabric-reliable-services-communication-webapi.md)
##### [サービスのリモート処理](service-fabric-reliable-services-communication-remoting.md)
##### [WCF](service-fabric-reliable-services-communication-wcf.md)
##### [リバース プロキシ](service-fabric-reverseproxy.md)
#### [詳細な使用方法](service-fabric-reliable-services-advanced-usage.md)

### Reliable Actors アプリケーション
#### [概要](service-fabric-reliable-actors-introduction.md)
#### 作業の開始
##### [Windows での C# ](service-fabric-reliable-actors-get-started.md)
##### [Linux での Java](service-fabric-reliable-actors-get-started-java.md)
#### [アーキテクチャ](service-fabric-reliable-actors-platform.md)
#### [ライフサイクルとガベージ コレクション](service-fabric-reliable-actors-lifecycle.md)
#### [ポリモーフィズム](service-fabric-reliable-actors-polymorphism.md)
#### [再入](service-fabric-reliable-actors-reentrancy.md)
#### [タイマーとアラーム](service-fabric-reliable-actors-timers-reminders.md)
#### [イベント](service-fabric-reliable-actors-events.md)
#### [状態管理](service-fabric-reliable-actors-state-management.md)
#### [状態プロバイダーの構成](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [型のシリアル化](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

### ゲスト実行可能アプリケーション
#### [ゲスト実行可能ファイルをデプロイする](service-fabric-deploy-existing-app.md)
#### [複数のゲスト実行可能ファイルのデプロイ](service-fabric-deploy-multiple-apps.md)

### コンテナー アプリケーション
#### [概要](service-fabric-containers-overview.md)
#### [Windows コンテナーのデプロイ](service-fabric-deploy-container.md)
#### [Docker コンテナーのデプロイ](service-fabric-deploy-container-linux.md)

## Cloud Services から移行する
### [Cloud Services と Service Fabric の比較](service-fabric-cloud-services-migration-differences.md)
### [Service Fabric への移行](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

## クラスターを作成および管理する

### 基本
#### [概要](service-fabric-deploy-anywhere.md)
#### [クラスターの説明](service-fabric-cluster-resource-manager-cluster-description.md)
#### [容量計画](service-fabric-cluster-capacity.md)
#### [クラスターの視覚化](service-fabric-visualizing-your-cluster.md)
#### [セキュリティ保護されたクラスターへの接続](service-fabric-connect-to-secure-cluster.md)
#### [セキュリティ](service-fabric-cluster-security.md)
#### [障害復旧](service-fabric-disaster-recovery.md)

### Azure 上のクラスター
#### Azure でのクラスターの作成
##### [Azure Portal](service-fabric-cluster-creation-via-portal.md)
##### [Azure リソース マネージャー](service-fabric-cluster-creation-via-arm.md)
#### [ノードの種類と VM Scale Sets](service-fabric-cluster-nodetypes.md)
#### [クラスターを拡張する](service-fabric-cluster-scale-up-down.md)
#### [クラスターのアップグレード](service-fabric-cluster-upgrade.md)
#### [クラスターの削除](service-fabric-cluster-delete.md)
#### [アクセス制御](service-fabric-cluster-security-roles.md)
#### [クラスターの構成](service-fabric-cluster-fabric-settings.md)
#### [Party Cluster を無料で試す](http://aka.ms/tryservicefabric)

### スタンドアロンのクラスター
#### [スタンドアロン クラスターの作成](service-fabric-cluster-creation-for-windows-server.md)
#### [クラスターを拡張する](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [クラスターのアップグレード](service-fabric-cluster-upgrade-windows-server.md)
#### [クラスターの保護](service-fabric-windows-cluster-x509-security.md)
#### [アクセス制御](service-fabric-cluster-security-roles.md)
#### [クラスターの構成](service-fabric-cluster-manifest.md)

## クラスター リソースを管理および調整する
### [Cluster Resource Manager の概要](service-fabric-cluster-resource-manager-introduction.md)
### [Cluster Resource Manager のアーキテクチャ](service-fabric-cluster-resource-manager-architecture.md)
### [クラスターの説明](service-fabric-cluster-resource-manager-cluster-description.md)
### [アプリケーション グループの概要](service-fabric-cluster-resource-manager-application-groups.md)
### [Cluster Resource Manager 設定の構成](service-fabric-cluster-resource-manager-configure-services.md)
### [リソース使用量メトリックス](service-fabric-cluster-resource-manager-metrics.md)
### [サービス アフィニティの使用](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
### [サービス配置ポリシー](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
### [クラスターの管理](service-fabric-cluster-resource-manager-management-integration.md)
### [クラスターの最適化](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
### [クラスターの分散](service-fabric-cluster-resource-manager-balancing.md)
### [調整](service-fabric-cluster-resource-manager-advanced-throttling.md)
### [サービスの移動](service-fabric-cluster-resource-manager-movement-cost.md)

## アプリケーションのライフサイクルを管理する
### [概要](service-fabric-application-lifecycle.md)
### [継続的インテグレーションのセットアップ](service-fabric-set-up-continuous-integration.md)
### アプリケーションをデプロイまたは削除する
#### [PowerShell](service-fabric-deploy-remove-applications.md)
#### [Visual Studio](service-fabric-publish-app-remote-cluster.md)
### [アプリケーション アップグレードの概要](service-fabric-application-upgrade.md)
### [アプリケーション アップグレードの構成](service-fabric-visualstudio-configure-upgrade.md)
### [アプリケーション アップグレードのパラメーター](service-fabric-application-upgrade-parameters.md)
### アプリケーションのアップグレード
#### [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
#### [Visual Studio](service-fabric-application-upgrade-tutorial.md)
### [アプリケーション アップグレードのトラブルシューティング](service-fabric-application-upgrade-troubleshooting.md)
### [アプリケーション アップグレードでのデータのシリアル化](service-fabric-application-upgrade-data-serialization.md)
### [アプリケーション アップグレードの高度なトピック](service-fabric-application-upgrade-advanced.md)
### [REST ベース アプリケーション ライフサイクルのサンプル](service-fabric-rest-based-application-lifecycle-sample.md)

## アプリケーションとクラスターの正常性の確認
### [Service Fabric の正常性の監視](service-fabric-health-introduction.md)
### [サービス正常性のレポートとチェック](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
### [カスタム正常性レポートの追加](service-fabric-report-health.md)
### [システム正常性レポートを使用したトラブルシューティング](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
### [正常性レポートの確認](service-fabric-view-entities-aggregated-health.md)

## 監視と診断
### ローカルでのサービスの監視と診断
#### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
#### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
### Azure 診断ログ
#### [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
#### [Linux](service-fabric-diagnostics-how-to-setup-lad.md)
### [Service Fabric アプリケーションの追跡](service-fabric-diagnostic-how-to-use-elasticsearch.md)
### [Reliable Actors の診断](service-fabric-reliable-actors-diagnostics.md)
### [ステートフル Reliable Services における診断](service-fabric-reliable-services-diagnostics.md)
### [ローカル クラスターのトラブルシューティング](service-fabric-troubleshoot-local-cluster-setup.md)
### [一般的な問題のトラブルシューティング](service-fabric-diagnostics-troubleshoot-common-scenarios.md)

## アプリケーションをスケール調整する
### [Reliable Services のパーティション分割](service-fabric-concepts-partitioning.md)
### [サービスの可用性](service-fabric-availability-services.md)
### [アプリケーションのスケール調整](service-fabric-concepts-scalability.md)
### [アプリケーション容量の計画](service-fabric-capacity-planning.md)

## アプリケーションとサービスをテストする
### [Fault Analysis の概要](service-fabric-testability-overview.md)
### [サービス間通信のテスト](service-fabric-testability-scenarios-service-communication.md)
### エラーをシミュレートする
#### [制御された混乱の使用](service-fabric-controlled-chaos.md)
#### [テスト アクションの使用](service-fabric-testability-actions.md)
#### [ワークロード中](service-fabric-testability-workload-tests.md)
#### [データ損失を呼び出す](service-fabric-use-data-loss-api.md)
#### [テスト シナリオの使用](service-fabric-testability-scenarios.md)
### [アプリケーションのロード テスト](service-fabric-vso-load-test.md)

# リファレンス
## [PowerShell](//powershell/servicefabric/vlatest/servicefabric)
## [Java API](/java/api/microsoft.servicefabric.services)
## [.NET](/dotnet/api/microsoft.servicefabric.services)
## [REST ()](/rest/api/servicefabric)

# リソース
## [コード サンプル](http://aka.ms/servicefabricsamples)
## [ラーニング パス](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
## [料金](https://azure.microsoft.com/pricing/details/service-fabric/)
## [サービスの更新情報](https://azure.microsoft.com/updates/?product=service-fabric)
## [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)
## [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=service-fabric)


<!--HONumber=Dec16_HO1-->


