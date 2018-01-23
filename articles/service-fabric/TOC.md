# [Service Fabric のドキュメント](/azure/service-fabric)
# 概要
## [Service Fabric とは](service-fabric-overview.md)

# クイック スタート
## [.NET アプリケーションの作成](service-fabric-quickstart-dotnet.md)
## [Linux コンテナー アプリケーションのデプロイ](service-fabric-quickstart-containers-linux.md)
## [Windows コンテナー アプリケーションのデプロイ](service-fabric-quickstart-containers.md)
## Java クイック スタート
### [Spring Boot アプリケーションのデプロイ](service-fabric-quickstart-java-spring-boot.md)
### [Reliable Services アプリケーションのデプロイ](service-fabric-quickstart-java-reliable-services.md)

# チュートリアル
## .NET アプリのデプロイ
### [1 - .NET アプリケーションの構築](service-fabric-tutorial-create-dotnet-app.md)
### [2 - アプリケーションのデプロイ](service-fabric-tutorial-deploy-app-to-party-cluster.md)
### [3 - CI/CD の構成](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
### [4 - 監視と診断](service-fabric-tutorial-monitoring-aspnet.md)

## 既存の .NET アプリのコンテナー格納
### [1 - Docker Compose を使用した .NET アプリのデプロイ](service-fabric-host-app-in-a-container.md)
### [2 - コンテナーの監視](service-fabric-tutorial-monitoring-wincontainers.md)

## Linux コンテナー アプリの作成
### [1 - コンテナー イメージの作成](service-fabric-tutorial-create-container-images.md)
### [2 - コンテナーのパッケージ化とデプロイ](service-fabric-tutorial-package-containers.md)
### [3 - フェールオーバーとスケール](service-fabric-tutorial-containers-failover.md)

## クラスターの作成と管理
### 1 - Azure でのクラスターの作成
#### [1a - Windows クラスターの作成](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
#### [1b - Linux クラスターの作成](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
### [2- クラスターのスケーリング](service-fabric-tutorial-scale-cluster.md)
### [3 - クラスター ランタイムのアップグレード](service-fabric-tutorial-upgrade-cluster.md)
### [4 - Service Fabric を使用した API Management のデプロイ](service-fabric-tutorial-deploy-api-management.md)

# サンプル
## [コード サンプル](https://azure.microsoft.com/resources/samples/?service=service-fabric)
## [Azure PowerShell](service-fabric-powershell-samples.md)
## [Service Fabric CLI](samples-cli.md)

# 概念
## [マイクロサービスの概要](service-fabric-overview-microservices.md)
## [全体像](service-fabric-content-roadmap.md)
## [アプリケーションのシナリオ](service-fabric-application-scenarios.md)
## [パターンとシナリオ](service-fabric-patterns-and-scenarios.md)
## [アーキテクチャ](service-fabric-architecture.md)
## [用語集](service-fabric-technical-overview.md)

## [サポートされるプログラミング モデル](service-fabric-choose-framework.md)
### [コンテナー](service-fabric-containers-overview.md)
#### [Docker Compose (プレビュー)](service-fabric-docker-compose.md)
#### [リソース ガバナンス](service-fabric-resource-governance.md)
### [Reliable Service](service-fabric-reliable-services-introduction.md)
#### [Reliable Services のライフサイクル - C#](service-fabric-reliable-services-lifecycle.md)
#### [Reliable Services のライフサイクル - Java](service-fabric-reliable-services-lifecycle-java.md)
#### [Reliable Collection](service-fabric-reliable-services-reliable-collections.md)
#### [Reliable Collection のガイドラインと推奨事項](service-fabric-reliable-services-reliable-collections-guidelines.md)
#### [Reliable Collection での作業](service-fabric-work-with-reliable-collections.md)
#### [トランザクションとロック](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
#### [Reliable Concurrent Queue](service-fabric-reliable-services-reliable-concurrent-queue.md)
#### [Reliable Collection のシリアル化](service-fabric-reliable-services-reliable-collections-serialization.md)
#### [Reliable State Manager と Reliable Collection の内部](service-fabric-reliable-services-reliable-collections-internals.md)
#### [詳細な使用方法](service-fabric-reliable-services-advanced-usage.md)

### [Reliable Actor](service-fabric-reliable-actors-introduction.md)
#### [アーキテクチャ](service-fabric-reliable-actors-platform.md)
#### [ライフサイクルとガベージ コレクション](service-fabric-reliable-actors-lifecycle.md)
#### [状態管理](service-fabric-reliable-actors-state-management.md)
#### [ポリモーフィズム](service-fabric-reliable-actors-polymorphism.md)
#### [再入](service-fabric-reliable-actors-reentrancy.md)
#### [型のシリアル化](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

## アプリケーションとサービス
### [アプリケーション モデル](service-fabric-application-model.md)
### [アプリケーション マニフェストとサービス マニフェスト](service-fabric-application-and-service-manifests.md)
### [ホスティング モデル](service-fabric-hosting-model.md)

### [サービスの状態](service-fabric-concepts-state.md)
### [サービスのパーティション分割](service-fabric-concepts-partitioning.md)
### [アプリケーションのスケーラビリティ](service-fabric-concepts-scalability.md)
### [サービスの可用性](service-fabric-availability-services.md)
### [レプリカとインスタンスのライフサイクル](service-fabric-concepts-replica-lifecycle.md)
### [再構成](service-fabric-concepts-reconfiguration.md)

### [サービス通信](service-fabric-connect-and-communicate-with-services.md)
#### [DNS サービス](service-fabric-dnsservice.md)
#### [リバース プロキシ](service-fabric-reverseproxy.md)
#### [セキュリティで保護された通信用のリバース プロキシの構成](service-fabric-reverseproxy-configure-secure-communication.md)
#### [リバース プロキシの診断](service-fabric-reverse-proxy-diagnostics.md)
#### [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

### [アプリケーションのライフサイクル](service-fabric-application-lifecycle.md)
#### [アプリケーションのアップグレード](service-fabric-application-upgrade.md)
##### [構成](service-fabric-visualstudio-configure-upgrade.md)
##### [アプリケーション アップグレードのパラメーター](service-fabric-application-upgrade-parameters.md)
##### [アプリケーション アップグレードでのデータのシリアル化](service-fabric-application-upgrade-data-serialization.md)
##### [アプリケーション アップグレードの高度なトピック](service-fabric-application-upgrade-advanced.md)
#### [複数の環境向けのアプリケーションの管理](service-fabric-manage-multiple-environment-app-configuration.md)
#### [障害分析によるアプリのテスト](service-fabric-testability-overview.md)
#### [ImageStoreConnectionString 設定](service-fabric-image-store-connection-string.md)

### [サービスのリソース](service-fabric-service-manifest-resources.md)

## [クラスター](service-fabric-deploy-anywhere.md)
### [クラスターのセキュリティ](service-fabric-cluster-security.md)
### [Linux と Windows での機能の違い](service-fabric-linux-windows-differences.md)
### Azure 上のクラスター
#### [ノードの種類と VM Scale Sets](service-fabric-cluster-nodetypes.md)
#### [クラスター ネットワークのパターン](service-fabric-patterns-networking.md)
### [クラスター リソース マネージャー](service-fabric-cluster-resource-manager-introduction.md)
#### [アーキテクチャ](service-fabric-cluster-resource-manager-architecture.md)
#### [クラスターの説明](service-fabric-cluster-resource-manager-cluster-description.md)
#### [アプリケーション グループの概要](service-fabric-cluster-resource-manager-application-groups.md)
#### [Cluster Resource Manager 設定の構成](service-fabric-cluster-resource-manager-configure-services.md)
#### [リソース使用量メトリックス](service-fabric-cluster-resource-manager-metrics.md)
#### [サービス アフィニティの使用](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
#### [サービス配置ポリシー](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
#### [クラスターの管理](service-fabric-cluster-resource-manager-management-integration.md)
#### [クラスターの最適化](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
#### [クラスターの分散](service-fabric-cluster-resource-manager-balancing.md)
#### [調整](service-fabric-cluster-resource-manager-advanced-throttling.md)
#### [サービスの移動](service-fabric-cluster-resource-manager-movement-cost.md)

## 監視と診断
### [アプリケーションの監視と診断](service-fabric-diagnostics-overview.md)
### イベントの生成
#### [プラットフォーム レベルのイベントの生成](service-fabric-diagnostics-event-generation-infra.md)
##### [操作チャネル](service-fabric-diagnostics-event-generation-operational.md)
##### [Reliable Services のイベント](service-fabric-reliable-services-diagnostics.md)
##### [Reliable Actors のイベント](service-fabric-reliable-actors-diagnostics.md)
##### [パフォーマンス メトリック](service-fabric-diagnostics-event-generation-perf.md)
##### [サービスのリモート処理の監視](service-fabric-reliable-serviceremoting-diagnostics.md)
#### [アプリケーション レベルのイベントの生成](service-fabric-diagnostics-event-generation-app.md)
### アプリケーションとクラスターの正常性の確認
#### [Service Fabric の正常性の監視](service-fabric-health-introduction.md)
#### [サービス正常性のレポートとチェック](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
#### [カスタム正常性レポートの追加](service-fabric-report-health.md)
#### [システム正常性レポートを使用したトラブルシューティング](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
#### [正常性レポートの確認](service-fabric-view-entities-aggregated-health.md)
### 集計値
#### [EventFlow を使ったイベントの集計](service-fabric-diagnostics-event-aggregation-eventflow.md)
#### Azure 診断を使ったイベントの集計
##### [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
##### [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
### イベントの分析
#### [Application Insight を使ったイベントの分析](service-fabric-diagnostics-event-analysis-appinsights.md)
#### [OMS を使ったイベントの分析](service-fabric-diagnostics-event-analysis-oms.md)
### [ローカル クラスターのトラブルシューティング](service-fabric-troubleshoot-local-cluster-setup.md)

## [API Management との統合](service-fabric-api-management-overview.md)

# ハウツー ガイド
## 開発環境を設定する
### [Windows](service-fabric-get-started.md)
### [Linux](service-fabric-get-started-linux.md)
### [Mac OS](service-fabric-get-started-mac.md)
### [Service Fabric CLI のセットアップ](service-fabric-cli.md)

## 計画と準備
### [クラスター容量の計画](service-fabric-cluster-capacity.md)
### [スタンドアロン クラスターのデプロイの計画](service-fabric-cluster-standalone-deployment-preparation.md)
### [ディザスター リカバリーの準備](service-fabric-disaster-recovery.md)
### [アプリケーション容量の計画](service-fabric-capacity-planning.md)

## 初めての作成
### [Visual Studio の C# アプリケーション](service-fabric-create-your-first-application-in-visual-studio.md)
### [Windows コンテナー アプリケーション](service-fabric-get-started-containers.md)
### [Linux コンテナー アプリケーション](service-fabric-get-started-containers-linux.md)
### [Windows の C# Reliable Services アプリケーション](service-fabric-reliable-services-quick-start.md)
### [Linux の Java Reliable Services アプリケーション](service-fabric-reliable-services-quick-start-java.md)
### [Linux の C# Reliable Services アプリケーション](service-fabric-create-your-first-linux-application-with-csharp.md)
### [Windows の C# Reliable Actors アプリケーション](service-fabric-reliable-actors-get-started.md)
### [Linux の Java Reliable Actors アプリケーション](service-fabric-create-your-first-linux-application-with-java.md)
### [Windows のゲスト実行可能アプリケーション](quickstart-guest-app.md)
### [スタンドアロン クラスター](service-fabric-get-started-standalone-cluster.md)

## アプリケーションのビルド

### ゲスト実行可能サービスの構築
#### [ゲスト実行可能ファイルをデプロイする](service-fabric-deploy-existing-app.md)
#### [複数のゲスト実行可能ファイルのデプロイ](service-fabric-deploy-multiple-apps.md)
### コンテナー サービスの構築
#### [コンテナー セキュリティ](service-fabric-securing-containers.md)
#### [Docker Compose (プレビュー)](service-fabric-docker-compose.md)
#### [コンテナーとサービスのリソース ガバナンス](service-fabric-resource-governance.md)
#### [ボリュームとログ ドライバー](service-fabric-containers-volume-logging-drivers.md)
#### [コンテナー内のサービス](service-fabric-services-inside-containers.md)
#### [コンテナー ネットワーク モード](service-fabric-networking-modes.md)

### Reliable Services サービスの構築
#### Reliable Collection
##### [Reliable Collection での作業](service-fabric-work-with-reliable-collections.md)
##### [Reliable Concurrent Queue](service-fabric-reliable-services-reliable-concurrent-queue.md)
##### [Reliable Collection のシリアル化](service-fabric-reliable-services-reliable-collections-serialization.md)

#### サービスとの通信
##### [Reliable Services を使用した通信](service-fabric-reliable-services-communication.md)

##### [サービスのリモート処理 - C#](service-fabric-reliable-services-communication-remoting.md)
##### [サービスのリモート処理 - Java](service-fabric-reliable-services-communication-remoting-java.md)
##### [WCF](service-fabric-reliable-services-communication-wcf.md)
##### [通信のセキュリティ保護 - C#](service-fabric-reliable-services-secure-communication.md)
##### [通信のセキュリティ保護 - Java](service-fabric-reliable-services-secure-communication-java.md)

#### [構成](service-fabric-reliable-services-configuration.md)
#### [通知の送信](service-fabric-reliable-services-notifications.md)
#### [バックアップと復元](service-fabric-reliable-services-backup-restore.md)

### Reliable Actors サービスの構築
#### [通知の送信](service-fabric-reliable-actors-events.md)
#### [タイマーとアラームの設定](service-fabric-reliable-actors-timers-reminders.md)
#### [KvsActorStateProvider の構成](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [通信の設定の構成](service-fabric-reliable-actors-fabrictransportsettings.md)
#### [ReliableDictionaryActorStateProvider の構成](service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

### [Maven への対応を目的とした従来の Java アプリケーションの移行](service-fabric-migrate-old-javaapp-to-use-maven.md)

### [セキュリティで保護された通信用のリバース プロキシの構成](service-fabric-reverseproxy-configure-secure-communication.md)

### [ASP.NET Core サービスの構築](service-fabric-add-a-web-frontend.md)

### セキュリティを構成する
#### [アプリケーション シークレットの管理](service-fabric-application-secret-management.md)  
#### [アプリケーションのセキュリティ ポリシーの構成](service-fabric-application-runas-security.md)

## Windows/VS 開発環境での作業
### [Visual Studio でのアプリケーションの管理](service-fabric-manage-application-in-visual-studio.md)
### [Visual Studio でセキュリティ保護された接続を構成する](service-fabric-visualstudio-configure-secure-connections.md)
### [VS での .NET サービスのデバッグ](service-fabric-debugging-your-application.md)
### [一般的なエラーと例外](service-fabric-errors-and-exceptions.md)
### [ローカルでの監視と診断](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
### [Windows での Linux クラスターの設定](service-fabric-local-linux-cluster-windows.md)

## Linux/Eclipse 開発環境での作業
### [Java 開発用の Eclipse プラグインの使用](service-fabric-get-started-eclipse.md)
### [Eclipse での Java サービスのデバッグ](service-fabric-debugging-your-application-java.md)
### [ローカルでの監視と診断](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

## Cloud Services から移行する
### [Cloud Services と Service Fabric の比較](service-fabric-cloud-services-migration-differences.md)
### [Service Fabric への移行](service-fabric-cloud-services-migration-worker-role-stateless-service.md)
### [推奨プラクティス](/azure/architecture/service-fabric/migrate-from-cloud-services)

## アプリケーションのライフサイクルを管理する
### [アプリケーションのパッケージ化](service-fabric-package-apps.md)
### [構成ファイルでのパラメーターの使用](service-fabric-how-to-parameterize-configuration-files.md)
### [パラメーターを使用したポート番号の指定](service-fabric-how-to-specify-port-number-using-parameters.md)
### [環境変数の指定](service-fabric-how-to-specify-environment-variables.md)

### アプリケーションをデプロイまたは削除する
#### [ローカル クラスターへのアプリケーションのデプロイ](service-fabric-get-started-with-a-local-cluster.md)
#### [Azure Resource Manager](service-fabric-application-arm-resource.md)
#### [Azure PowerShell](service-fabric-deploy-remove-applications.md)
#### [Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
#### [FabricClient API](service-fabric-deploy-remove-applications-fabricclient.md)

### アプリケーションのアップグレード
#### [Azure PowerShell を使用したアップグレード](service-fabric-application-upgrade-tutorial-powershell.md)
#### [Visual Studio を使用したアップグレード](service-fabric-application-upgrade-tutorial.md)
#### [アプリケーション アップグレードのトラブルシューティング](service-fabric-application-upgrade-troubleshooting.md)

### アプリケーションとサービスをテストする
#### [サービス間通信のテスト](service-fabric-testability-scenarios-service-communication.md)
#### エラーをシミュレートする
##### [制御された混乱の使用](service-fabric-controlled-chaos.md)
##### [テスト アクションの使用](service-fabric-testability-actions.md)
##### [ワークロード中](service-fabric-testability-workload-tests.md)
##### [テスト シナリオの使用](service-fabric-testability-scenarios.md)
##### [Node Transition API の使用](service-fabric-node-transition-apis.md)

### 継続的インテグレーションのセットアップ
#### [VSTS を使用した継続的インテグレーションのセットアップ](service-fabric-set-up-continuous-integration.md)
#### [Jenkins を使用した Linux アプリケーションのデプロイ](service-fabric-cicd-your-linux-applications-with-jenkins.md)

## クラスターを作成および管理する
### Azure 上のクラスター
#### [作成]
##### [Azure Portal](service-fabric-cluster-creation-via-portal.md)
##### [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
#### スケール
##### [手動で](service-fabric-cluster-scale-up-down.md)
##### [プログラムで](service-fabric-cluster-programmatic-scaling.md)
#### [アップグレード](service-fabric-cluster-upgrade.md)
#### [アクセス制御の設定](service-fabric-cluster-security-roles.md)
#### [構成](service-fabric-cluster-fabric-settings.md)
#### [ロード バランサーでポートを開く](create-load-balancer-rule.md)
#### [クラスター証明書の管理](service-fabric-cluster-security-update-certs-azure.md)
#### [削除](service-fabric-cluster-delete.md)

### スタンドアロンのクラスター
#### [作成]
##### [オンプレミスでの作成](service-fabric-cluster-creation-for-windows-server.md)
##### [証明書を使用したセキュリティ保護](service-fabric-windows-cluster-x509-security.md)  
##### [Windows セキュリティを使用したセキュリティ保護](service-fabric-windows-cluster-windows-security.md)
##### [スタンドアロン パッケージの内容](service-fabric-cluster-standalone-package-contents.md)
#### [スケール](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [アクセス制御の設定](service-fabric-cluster-security-roles.md)
#### [構成](service-fabric-cluster-manifest.md)
#### [アップグレード](service-fabric-cluster-upgrade-windows-server.md)

### [クラスターの視覚化](service-fabric-visualizing-your-cluster.md)
### [セキュリティ保護されたクラスターへの接続](service-fabric-connect-to-secure-cluster.md)
### [クラスター ノードへのパッチの適用](service-fabric-patch-orchestration-application.md)

## 監視と診断
### OMS
#### [OMS Log Analytics の設定](service-fabric-diagnostics-oms-setup.md)
#### [OMS エージェントの追加](service-fabric-diagnostics-oms-agent.md)
#### [コンテナーの監視](service-fabric-diagnostics-oms-containers.md)
### パフォーマンスの監視
#### [WAD を使用したパフォーマンスの監視](service-fabric-diagnostics-perf-wad.md)


# リファレンス
## [Azure PowerShell](/powershell/module/azurerm.servicefabric/)
## [PowerShell](/powershell/module/servicefabric/?view=azureservicefabricps)
## [Azure CLI (az sf)](/cli/azure/sf)
## [Service Fabric CLI (sfctl)](service-fabric-sfctl.md)
### [sfctl application](service-fabric-sfctl-application.md)
### [sfctl chaos](service-fabric-sfctl-chaos.md)
### [sfctl cluster](service-fabric-sfctl-cluster.md)
### [sfctl compose](service-fabric-sfctl-compose.md)
### [sfctl is](service-fabric-sfctl-is.md)
### [sfctl node](service-fabric-sfctl-node.md)
### [sfctl partition](service-fabric-sfctl-partition.md)
### [sfctl replica](service-fabric-sfctl-replica.md)
### [sfctl rpm](service-fabric-sfctl-rpm.md)
### [sfctl service](service-fabric-sfctl-service.md)
### [sfctl store](service-fabric-sfctl-store.md)
## [Java API](/java/api/overview/azure/servicefabric)
### [Java REST クライアント API](service-fabric-java-rest-api-usage.md)
## [.NET](/dotnet/api/overview/azure/service-fabric?view=azure-dotnet)
## [REST](/rest/api/servicefabric)
## [サービス モデルの XML スキーマ](service-fabric-service-model-schema.md)
## [環境変数](service-fabric-environment-variables-reference.md)

# リソース
## [Azure のロードマップ](https://azure.microsoft.com/roadmap/)
## [一般的な質問](service-fabric-common-questions.md)
## [ラーニング パス](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
## [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)
## [料金](https://azure.microsoft.com/pricing/details/service-fabric/)
## [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)
## [コード サンプル](http://aka.ms/servicefabricsamples)
## [サポート オプション](service-fabric-support.md)
## [サービスの更新情報](https://azure.microsoft.com/updates/?product=service-fabric)
## [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=service-fabric)
