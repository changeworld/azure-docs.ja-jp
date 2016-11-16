# アーキテクチャ

## クラウドの基礎

### [回復性に優れた Azure 用アプリケーションの設計](guidance-resiliency-overview.md)
#### [回復性のチェックリスト](guidance-resiliency-checklist.md)
#### [障害モードの分析](guidance-resiliency-failure-mode-analysis.md)

#### [Microsoft Azure 上に構築されたアプリケーションの障害復旧](..\resiliency\resiliency-disaster-recovery-azure-applications.md)
#### [Microsoft Azure 上に構築されたアプリケーションの障害復旧と高可用性](..\resiliency\resiliency-disaster-recovery-high-availability-azure-applications.md)
#### [Microsoft Azure 上に構築されたアプリケーションの高可用性](..\resiliency\resiliency-high-availability-azure-applications.md)
#### [高可用性のチェック リスト](..\resiliency\resiliency-high-availability-checklist.md)
#### [Azure サービスに固有の回復性ガイダンス](..\resiliency\resiliency-service-guidance-index.md)
#### [データの破損または偶発的な削除からの復旧](..\resiliency\resiliency-technical-guidance-recovery-data-corruption.md)
#### [Azure でのローカル障害からの復旧](..\resiliency\resiliency-technical-guidance-recovery-local-failures.md)
#### [リージョン全体のサービス中断からの復旧](..\resiliency\resiliency-technical-guidance-recovery-loss-azure-region.md)
#### [オンプレミスから Azure への復旧](..\resiliency\resiliency-technical-guidance-recovery-on-premises-azure.md)
#### [Azure の回復性技術ガイダンス](..\resiliency\resiliency-technical-guidance.md)


## 参照アーキテクチャ

### コンピューティングの参照アーキテクチャ
#### [Azure での Linux VM の実行](guidance-compute-single-vm-linux.md)
#### [Azure での Windows VM の実行](guidance-compute-single-vm.md)
#### [スケーラビリティと可用性のために Azure で複数の VM を実行する](guidance-compute-multi-vm.md)
#### [Azure で N 層アーキテクチャの Linux VM を実行する](guidance-compute-n-tier-vm-linux.md)
#### [Azure で N 層アーキテクチャの Windows VM を実行する](guidance-compute-n-tier-vm.md)
#### [高可用性のために複数のリージョンで Linux VM を実行する](guidance-compute-multiple-datacenters-linux.md)
#### [高可用性のために複数のリージョンで Windows VM を実行する](guidance-compute-multiple-datacenters.md)

### [オンプレミス ネットワークを Azure に接続する](guidance-connecting-your-on-premises-network-to-azure.md)
#### [Azure ExpressRoute を使ってハイブリッド ネットワーク アーキテクチャを実装する](guidance-hybrid-network-expressroute.md)
#### [Azure とオンプレミスの VPN を使ってハイブリッド ネットワーク アーキテクチャを実装する](guidance-hybrid-network-vpn.md)
#### [高可用性ハイブリッド ネットワーク アーキテクチャの実装](guidance-hybrid-network-expressroute-vpn-failover.md)

### Azure でのクラウド境界の保護
#### [Azure における安全なハイブリッド ネットワーク アーキテクチャの実装](guidance-iaas-ra-secure-vnet-hybrid.md)
#### [Azure とインターネットの間に DMZ を実装する](guidance-iaas-ra-secure-vnet-dmz.md)

### [Azure での ID 管理](guidance-ra-identity.md)
#### [Azure Active Directory の実装](guidance-identity-aad.md)
#### [Active Directory ディレクトリ サービス (ADDS) を Azure に拡張する](guidance-identity-adds-extend-domain.md)
#### [Azure で Active Directory ディレクトリ サービス (ADDS) リソース フォレストを作成する](guidance-identity-adds-resource-forest.md)
#### [Azure で Active Directory Federation Services (ADFS) を実装する](guidance-identity-adfs.md)

### PaaS Web アプリケーションの参照アーキテクチャ
#### [Azure の参照アーキテクチャ: 基本の Web アプリケーション](guidance-web-apps-basic.md)
#### [Azure の参照アーキテクチャ: 高可用性 Web アプリケーション](guidance-web-apps-multi-region.md)
#### [Web アプリケーションでのスケーラビリティの向上](guidance-web-apps-scalability.md)


## クラウドの設計パターン

## クラウド アプリケーションのベスト プラクティス

### [API 設計ガイダンス](..\best-practices-api-design.md)
### [API 実装ガイダンス](..\best-practices-api-implementation.md)
### [自動スケール ガイダンス](..\best-practices-auto-scaling.md)
### [可用性のチェックリスト](..\best-practices-availability-checklist.md)
### [バックグラウンド ジョブのガイダンス](..\best-practices-background-jobs.md)
### [ビジネス継続性と障害復旧 (BCDR): Azure のペアになっているリージョン](..\best-practices-availability-paired-regions.md)
### [キャッシュに関するガイダンス](..\best-practices-caching.md)
### [コンテンツ配信ネットワーク (CDN) のガイダンス](..\best-practices-cdn.md)
### [データのパーティション分割のガイダンス](..\best-practices-data-partitioning.md)
### [監視と診断のガイダンス](..\best-practices-monitoring.md)
### [Microsoft クラウド サービスとネットワーク セキュリティ](..\best-practices-network-security.md)
### [Azure Resource Manager テンプレートの設計パターン](..\best-practices-resource-manager-design-templates.md)
### [Azure リソースの推奨される名前付け規則](guidance-naming-conventions.md)
### [Azure Resource Manager のセキュリティに関する考慮事項](..\best-practices-resource-manager-security.md)
### [Azure Resource Manager のテンプレートでの状態の共有](..\best-practices-resource-manager-state.md)
### [再試行の一般的なガイダンス](..\best-practices-retry-general.md)
### [再試行サービス固有のガイダンス](..\best-practices-retry-service-specific.md)
### [スケーラビリティのチェックリスト](..\best-practices-scalability-checklist.md)


## シナリオのガイド

### [Azure での Elasticsearch に関するガイダンス](guidance-elasticsearch.md)
#### [Azure で Elasticsearch を実行する](guidance-elasticsearch-running-on-azure.md)
#### [Azure での Elasticsearch のデータ インジェスト パフォーマンスのチューニング](guidance-elasticsearch-tuning-data-ingestion-performance.md)
#### [Azure の Elasticsearch でのデータ集計およびクエリのパフォーマンスのチューニング](guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md)
#### [Azure での Elasticsearch の復元と回復の構成](guidance-elasticsearch-configuring-resilience-and-recovery.md)
#### [Azure での Elasticsearch 用のパフォーマンス テスト環境の作成](guidance-elasticsearch-creating-performance-testing-environment.md)
#### [Elasticsearch 用の JMeter テスト計画を実装する](guidance-elasticsearch-implementing-jmeter-test-plan.md)
#### [Elasticsearch のパフォーマンスをテストするための JMeter JUnit サンプラーをデプロイする](guidance-elasticsearch-deploying-jmeter-junit-sampler.md)
#### [Elasticsearch の自動回復性テストの実行](guidance-elasticsearch-running-automated-resilience-tests.md)
#### [自動化された Elasticsearch パフォーマンス テストの実行](guidance-elasticsearch-running-automated-performance-tests.md)

### [Microsoft Azure のマルチテナント アプリケーションの ID 管理](guidance-multitenant-identity.md)
#### [Microsoft Azure のマルチテナント アプリケーションの ID 管理の概要](guidance-multitenant-identity-intro.md)
#### [Tailspin Surveys アプリケーションについて](guidance-multitenant-identity-tailspin.md)
#### [Azure AD および OpenID Connect を使用したマルチテナント アプリでの認証](guidance-multitenant-identity-authenticate.md)
#### [マルチテナント アプリケーションでの要求ベース ID の操作](guidance-multitenant-identity-claims.md)
#### [マルチテナント アプリケーションのサインアップとテナントのオンボーディング](guidance-multitenant-identity-signup.md)
#### [マルチテナント アプリケーションのアプリケーション ロール](guidance-multitenant-identity-app-roles.md)
#### [マルチテナント アプリケーションでのロールベースおよびリソースベースの承認](guidance-multitenant-identity-authorize.md)
#### [マルチテナント アプリケーションのバックエンド Web API をセキュリティで保護する](guidance-multitenant-identity-web-api.md)
#### [マルチテナント アプリケーションでアクセス トークンをキャッシュする](guidance-multitenant-identity-token-cache.md)
#### [Azure でのマルチテナント アプリ用に顧客の AD FS とフェデレーションする](guidance-multitenant-identity-adfs.md)
#### [クライアント アサーションを使用した、Azure AD からのアクセス トークンの取得](guidance-multitenant-identity-client-assertion.md)
#### [Azure Key Vault を使用してアプリケーション シークレットを保護する](guidance-multitenant-identity-keyvault.md)

#### [高可用性仮想アプライアンスのデプロイ](guidance-nva-ha.md)


<!--HONumber=Nov16_HO2-->


