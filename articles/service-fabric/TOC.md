
# 概要
## [Service Fabric とは](service-fabric-overview.md)
## [マイクロサービスの概要](service-fabric-overview-microservices.md)
## [アプリケーションのシナリオ](service-fabric-application-scenarios.md)
## [パターンとシナリオ](service-fabric-patterns-and-scenarios.md)
## [アーキテクチャ](service-fabric-architecture.md)
## [用語集](service-fabric-technical-overview.md)
## [基本](service-fabric-content-roadmap.md)

# 作業開始
## 開発環境を設定する
### [Windows](service-fabric-get-started.md)
### [Linux](service-fabric-get-started-linux.md)
### [Mac OS](service-fabric-get-started-mac.md)
## 最初のアプリケーションを作成する
### [Windows での C# ](service-fabric-create-your-first-application-in-visual-studio.md)
### [Linux での Java](service-fabric-create-your-first-linux-application-with-java.md)
### [Linux での C#](service-fabric-create-your-first-linux-application-with-csharp.md)
## [ローカル クラスターへのアプリのデプロイ](service-fabric-get-started-with-a-local-cluster.md)
## [Azure で初めてのクラスターを作成する](service-fabric-get-started-azure-cluster.md)
## [初めてのスタンドアロン クラスターを作成する](service-fabric-get-started-standalone-cluster.md)

# 方法
## アプリケーションのビルド
  
### 概念
#### [サポートされるプログラミング モデル](service-fabric-choose-framework.md)
#### [アプリケーション モデル](service-fabric-application-model.md)
#### [サービス マニフェスト リソース](service-fabric-service-manifest-resources.md)
#### [サービスの状態](service-fabric-concepts-state.md)
#### [サービスのパーティション分割](service-fabric-concepts-partitioning.md)
#### [サービスの可用性](service-fabric-availability-services.md)
#### [アプリケーションのスケーラビリティ](service-fabric-concepts-scalability.md)
#### [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

### [アプリの容量を計画する](service-fabric-capacity-planning.md)

### ゲスト実行可能サービスの構築
#### [ゲスト実行可能ファイルをデプロイする](service-fabric-deploy-existing-app.md)
#### [複数のゲスト実行可能ファイルのデプロイ](service-fabric-deploy-multiple-apps.md)

### コンテナー サービスの構築
#### [概要](service-fabric-containers-overview.md)
#### [Windows コンテナーのデプロイ](service-fabric-deploy-container.md)
#### [Linux コンテナーのデプロイ](service-fabric-deploy-container-linux.md)

### Reliable Services サービスの構築
#### [概要](service-fabric-reliable-services-introduction.md)
#### 概念
##### [Reliable Services のライフサイクル - C#](service-fabric-reliable-services-lifecycle.md)
##### [Reliable Services のライフサイクル - Java](service-fabric-reliable-services-lifecycle-java.md)
##### [Reliable Collection](service-fabric-reliable-services-reliable-collections.md)

#### 作業開始
##### [Windows での C# ](service-fabric-reliable-services-quick-start.md)
##### [Linux での Java](service-fabric-reliable-services-quick-start-java.md)

#### Reliable Services のライフサイクル
#### [Reliable Collections の使用](service-fabric-work-with-reliable-collections.md)
#### [構成](service-fabric-reliable-services-configuration.md)
#### [通知の送信](service-fabric-reliable-services-notifications.md)
#### [バックアップと復元](service-fabric-reliable-services-backup-restore.md)

#### サービスとの通信
##### [Reliable Services を使用した通信](service-fabric-reliable-services-communication.md)
##### [サービスのリモート処理 - C#](service-fabric-reliable-services-communication-remoting.md)
##### [サービスのリモート処理 - Java](service-fabric-reliable-services-communication-remoting-java.md)
##### [WCF](service-fabric-reliable-services-communication-wcf.md)
##### [通信のセキュリティ保護 - C#](service-fabric-reliable-services-secure-communication.md)
##### [通信のセキュリティ保護 - Java](service-fabric-reliable-services-secure-communication-java.md)

#### [詳細な使用方法](service-fabric-reliable-services-advanced-usage.md)

### Reliable Actors サービスの構築
#### [概要](service-fabric-reliable-actors-introduction.md)
#### 概念
#### [アーキテクチャ](service-fabric-reliable-actors-platform.md)
#### [ライフサイクルとガベージ コレクション](service-fabric-reliable-actors-lifecycle.md)
#### [状態管理](service-fabric-reliable-actors-state-management.md)
#### [ポリモーフィズム](service-fabric-reliable-actors-polymorphism.md)
#### [再入](service-fabric-reliable-actors-reentrancy.md)
#### [型のシリアル化](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

#### 作業開始
##### [Windows での C# ](service-fabric-reliable-actors-get-started.md)
##### [Linux での Java](service-fabric-reliable-actors-get-started-java.md)

#### [通知の送信](service-fabric-reliable-actors-events.md) 
#### [タイマーとアラームの設定](service-fabric-reliable-actors-timers-reminders.md)
#### [KvsActorStateProvider の構成](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [通信の設定の構成](service-fabric-reliable-actors-fabrictransportsettings.md) 
#### [ReliableDictionaryActorStateProvider の構成](service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

### サービスとの通信
#### [サービス通信](service-fabric-connect-and-communicate-with-services.md)
#### [リバース プロキシ](service-fabric-reverseproxy.md)

### [Web フロントエンドの追加](service-fabric-add-a-web-frontend.md)

### IDE での作業
#### [Java 開発用の Eclipse プラグインの使用](service-fabric-get-started-eclipse.md)
#### [Visual Studio でアプリを管理する](service-fabric-manage-application-in-visual-studio.md)
#### [Visual Studio でセキュリティ保護された接続を構成する](service-fabric-visualstudio-configure-secure-connections.md)
#### [複数環境向けアプリケーションの構成](service-fabric-manage-multiple-environment-app-configuration.md)

### セキュリティを構成する
#### [アプリケーション シークレットの管理](service-fabric-application-secret-management.md)  
#### [アプリケーションのセキュリティ ポリシーの構成](service-fabric-application-runas-security.md)

### デバッグ
#### [VS での C# サービスのデバッグ](service-fabric-debugging-your-application.md)
#### [Eclipse での Java サービスのデバッグ](service-fabric-debugging-your-application-java.md)
#### [一般的なエラーと例外](service-fabric-errors-and-exceptions.md)

### ローカルでの監視と診断
#### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
#### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

### Cloud Services から移行する
#### [Cloud Services と Service Fabric の比較](service-fabric-cloud-services-migration-differences.md)
#### [Service Fabric への移行](service-fabric-cloud-services-migration-worker-role-stateless-service.md)
#### [推奨プラクティス](/azure/architecture/service-fabric/migrate-from-cloud-services)

## アプリケーションのライフサイクルを管理する
### [概要](service-fabric-application-lifecycle.md)
### [アプリケーションのパッケージ化](service-fabric-package-apps.md)
### [ImageStoreConnectionString 設定について](service-fabric-image-store-connection-string.md)
### アプリケーションをデプロイまたは削除する
#### [PowerShell](service-fabric-deploy-remove-applications.md)
#### [Visual Studio](service-fabric-publish-app-remote-cluster.md)
#### [FabricClient API](service-fabric-deploy-remove-applications-fabricclient.md)
### アプリケーションのアップグレード
#### [概要](service-fabric-application-upgrade.md)
#### [アプリケーション アップグレードの構成](service-fabric-visualstudio-configure-upgrade.md)
#### [アプリケーション アップグレードのパラメーター](service-fabric-application-upgrade-parameters.md)
#### アップグレード
##### [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
##### [Visual Studio](service-fabric-application-upgrade-tutorial.md)
#### [アプリケーション アップグレードのトラブルシューティング](service-fabric-application-upgrade-troubleshooting.md)
#### [アプリケーション アップグレードでのデータのシリアル化](service-fabric-application-upgrade-data-serialization.md)
#### [アプリケーション アップグレードの高度なトピック](service-fabric-application-upgrade-advanced.md)

### アプリケーションとサービスをテストする
#### [Fault Analysis の概要](service-fabric-testability-overview.md)
#### [サービス間通信のテスト](service-fabric-testability-scenarios-service-communication.md)
#### エラーをシミュレートする
##### [制御された混乱の使用](service-fabric-controlled-chaos.md)
##### [テスト アクションの使用](service-fabric-testability-actions.md)
##### [ワークロード中](service-fabric-testability-workload-tests.md)
##### [テスト シナリオの使用](service-fabric-testability-scenarios.md)
##### [Node Transition API の使用](service-fabric-node-transition-apis.md)
#### [アプリケーションのロード テスト](service-fabric-vso-load-test.md)

### 継続的インテグレーションのセットアップ
#### [VSTS を使用した継続的インテグレーションのセットアップ](service-fabric-set-up-continuous-integration.md)
#### [Jenkins を使用した Linux Java アプリのデプロイ](service-fabric-cicd-your-linux-java-application-with-jenkins.md)

## クラスターを作成および管理する

### [概要](service-fabric-deploy-anywhere.md)
### 概念
#### [クラスターの説明](service-fabric-cluster-resource-manager-cluster-description.md)
#### [クラスターのセキュリティ](service-fabric-cluster-security.md)
#### [Linux と Windows での機能の違い](service-fabric-linux-windows-differences.md)

### 計画と準備
#### [容量計画](service-fabric-cluster-capacity.md)
#### [障害復旧](service-fabric-disaster-recovery.md)

### Azure 上のクラスター
#### 概念
##### [ノードの種類と VM Scale Sets](service-fabric-cluster-nodetypes.md)
##### [クラスター ネットワークのパターン](service-fabric-patterns-networking.md)
#### 作成 
##### [Azure ポータル](service-fabric-cluster-creation-via-portal.md)
##### [Azure リソース マネージャー](service-fabric-cluster-creation-via-arm.md)
##### [Visual Studio と Azure Resource Manager](service-fabric-cluster-creation-via-visual-studio.md)
#### スケール 
##### [手動で](service-fabric-cluster-scale-up-down.md)
##### [プログラムで](service-fabric-cluster-programmatic-scaling.md)
#### [アップグレード](service-fabric-cluster-upgrade.md)
#### [アクセス制御の設定](service-fabric-cluster-security-roles.md)
#### [構成](service-fabric-cluster-fabric-settings.md)
#### [クラスター証明書の管理](service-fabric-cluster-security-update-certs-azure.md) 
#### [削除](service-fabric-cluster-delete.md)

### スタンドアロンのクラスター
#### [スタンドアロン パッケージの内容](service-fabric-cluster-standalone-package-contents.md)
#### [デプロイの計画と準備](service-fabric-cluster-standalone-deployment-preparation.md)
#### 作成
##### [オンプレミスでの作成](service-fabric-cluster-creation-for-windows-server.md)
##### [Azure 仮想マシンでの作成](service-fabric-cluster-creation-with-windows-azure-vms.md)
##### [証明書を使用したセキュリティ保護](service-fabric-windows-cluster-x509-security.md)  
##### [Windows セキュリティを使用したセキュリティ保護](service-fabric-windows-cluster-windows-security.md)
#### [スケール](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [アクセス制御の設定](service-fabric-cluster-security-roles.md)
#### [構成](service-fabric-cluster-manifest.md)
#### [アップグレード](service-fabric-cluster-upgrade-windows-server.md) 

### [クラスターの視覚化](service-fabric-visualizing-your-cluster.md)
### [セキュリティ保護されたクラスターへの接続](service-fabric-connect-to-secure-cluster.md)

### [Azure CLI を使用したクラスターの管理](service-fabric-azure-cli.md)

### クラスター リソースを管理および調整する
#### [Cluster Resource Manager の概要](service-fabric-cluster-resource-manager-introduction.md)
#### [Cluster Resource Manager のアーキテクチャ](service-fabric-cluster-resource-manager-architecture.md)
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

### [セキュリティ保護されたクラスターへの接続](service-fabric-connect-to-secure-cluster.md)

## アプリケーションとクラスターの正常性の確認
### [Service Fabric の正常性の監視](service-fabric-health-introduction.md)
### [サービス正常性のレポートとチェック](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
### [カスタム正常性レポートの追加](service-fabric-report-health.md)
### [システム正常性レポートを使用したトラブルシューティング](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
### [正常性レポートの確認](service-fabric-view-entities-aggregated-health.md)

## 監視と診断
### [アプリケーションの監視と診断](service-fabric-diagnostics-overview.md)
### ローカルでのサービスの監視と診断
#### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
#### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
### Azure 診断ログ
#### [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
#### [Linux](service-fabric-diagnostics-how-to-setup-lad.md)
### [サービス プロセスからのログの収集](service-fabric-diagnostic-collect-logs-without-an-agent.md)
### [ステートフル Reliable Services における診断](service-fabric-reliable-services-diagnostics.md)
### [Reliable Actors の診断](service-fabric-reliable-actors-diagnostics.md)
### [ローカル クラスターのトラブルシューティング](service-fabric-troubleshoot-local-cluster-setup.md)

# リファレンス
## [PowerShell](/powershell/azure/overview?view=azureservicefabricps)
## [Java API](/java/api/)
## [.NET](/dotnet/api/)
## [REST ()](/rest/api/servicefabric)

# リソース
## [Service Fabric に関する一般的な質問](service-fabric-common-questions.md)
## [Service Fabric のサポート オプション](service-fabric-support.md)
## [コード サンプル](http://aka.ms/servicefabricsamples)
## [ラーニング パス](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
## [料金](https://azure.microsoft.com/pricing/details/service-fabric/)
## [サービスの更新情報](https://azure.microsoft.com/updates/?product=service-fabric)
## [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)
## [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=service-fabric)
