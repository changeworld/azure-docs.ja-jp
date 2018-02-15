# 概要
## [Service Bus メッセージングとは](service-bus-messaging-overview.md)
## [Service Bus の基礎](service-bus-fundamentals-hybrid-solutions.md)
## [Service Bus のアーキテクチャ](service-bus-architecture.md)
## [FAQ](service-bus-faq.md)

# クイック スタート
## [名前空間を作成する](service-bus-create-namespace-portal.md)
## キューの使用
### [.NET](service-bus-dotnet-get-started-with-queues.md)
### [Java](service-bus-java-how-to-use-queues.md)
### [Node.JS](service-bus-nodejs-how-to-use-queues.md)
### [PHP](service-bus-php-how-to-use-queues.md)
### [Python](service-bus-python-how-to-use-queues.md)
### [Ruby](service-bus-ruby-how-to-use-queues.md)
### [REST](/rest/api/servicebus/queues)
## トピックとサブスクリプションの使用
### [.NET](service-bus-dotnet-how-to-use-topics-subscriptions.md)
### [Java](service-bus-java-how-to-use-topics-subscriptions.md)
### [Node.JS](service-bus-nodejs-how-to-use-topics-subscriptions.md)
### [PHP](service-bus-php-how-to-use-topics-subscriptions.md)
### [Python](service-bus-python-how-to-use-topics-subscriptions.md)
### [Ruby](service-bus-ruby-how-to-use-topics-subscriptions.md)

# 方法
## 計画と設計
### [管理対象サービス ID (プレビュー)](service-bus-managed-service-identity.md)
### [ロールベースのアクセス制御 (プレビュー)](service-bus-role-based-access-control.md)
### [Premium メッセージング](service-bus-premium-messaging.md)
### [Azure キューと Service Bus キューを比較する](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
### [パフォーマンスを最適化する](service-bus-performance-improvements.md)
### [geo ディザスター リカバリーと geo レプリケーション](service-bus-geo-dr.md)
### [非同期メッセージングと高可用性](service-bus-async-messaging.md)
### [故障と障害の扱い](service-bus-outages-disasters.md)

## 開発
### [多層 Service Bus アプリケーションを構築する](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)
### メッセージの処理
#### [キュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
#### [メッセージ、ペイロード、シリアル化](service-bus-messages-payloads.md)
#### [メッセージの転送、ロック、解決](message-transfers-locks-settlement.md)
#### [メッセージのシーケンス処理とタイムスタンプ](message-sequencing.md)
#### [メッセージの有効期限](message-expiration.md)
### [認証と権限承認](service-bus-authentication-and-authorization.md)
#### [ACS から SAS への移行](service-bus-migrate-acs-sas.md)
#### [Shared Access Signature による認証](service-bus-sas.md)
### [トピック フィルターとアクション](topic-filters.md)
### [パーティション分割されたキューとトピック](service-bus-partitioning.md)
### [メッセージ セッション](message-sessions.md)
### AMQP
#### [AMQP の概要](service-bus-amqp-overview.md)
#### [.NET](service-bus-amqp-dotnet.md)
#### [Java](service-bus-amqp-java.md)
#### [Java Message Service と AMQP](service-bus-java-how-to-use-jms-api-amqp.md)
#### [AMQP プロトコル ガイド](service-bus-amqp-protocol-guide.md)
#### [AMQP 要求応答ベースの操作](service-bus-amqp-request-response.md)
### 高度な機能
#### [配達不能キュー](service-bus-dead-letter-queues.md)
#### [メッセージのプリフェッチ](service-bus-prefetch.md)
#### [重複メッセージ検出](duplicate-detection.md)
#### [メッセージ カウンター](message-counters.md)
#### [メッセージの遅延](message-deferral.md)
#### [メッセージの読み取り](message-browsing.md)
#### [自動転送によるエンティティのチェーン](service-bus-auto-forwarding.md)
#### [トランザクション処理](service-bus-transactions.md)
#### [ペアの名前空間の実装](service-bus-paired-namespaces.md)
### [エンド ツー エンドのトレースと診断](service-bus-end-to-end-tracing.md)
## [管理]
### [Azure 監視による Service Bus の監視](service-bus-metrics-azure-monitor.md)
### [Service Bus 管理ライブラリ](service-bus-management-libraries.md)
### [診断ログ](service-bus-diagnostic-logs.md)
### [メッセージ エンティティの一時停止と再アクティブ化](entity-suspend.md)
### [Azure Resource Manager テンプレートの使用](service-bus-resource-manager-overview.md)
#### [名前空間を作成する](service-bus-resource-manager-namespace.md)
#### [名前空間とキューを作成する](service-bus-resource-manager-namespace-queue.md)
#### [トピックとサブスクリプションを含んだ名前空間を作成する](service-bus-resource-manager-namespace-topic.md)
#### [名前空間とキューの承認規則を作成する](service-bus-resource-manager-namespace-auth-rule.md)
#### [トピック、サブスクリプション、ルールを含んだ名前空間を作成する](service-bus-resource-manager-namespace-topic-with-rule.md)
#### 
### [Azure PowerShell を使用したエンティティのプロビジョニング](service-bus-manage-with-ps.md)

# リファレンス
## .NET
### [Microsoft.ServiceBus.Messaging (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging)
### [Microsoft.Azure.ServiceBus (.NET Standard)](/dotnet/api/microsoft.azure.servicebus)
## [Java](/java/api/overview/azure/servicebus)
## [Azure PowerShell](/powershell/module/azurerm.servicebus)
## [REST](/rest/api/servicebus)
## [Exceptions](service-bus-messaging-exceptions.md)
## [クォータ](service-bus-quotas.md)
## [SQLFilter 構文](service-bus-messaging-sql-filter.md)
## [SQLRuleAction 構文](service-bus-messaging-sql-rule-action.md)

# リソース
## [Azure のロードマップ](https://azure.microsoft.com/roadmap/?category=enterprise-integration)
## [ブログ](https://blogs.msdn.microsoft.com/servicebus/)
## [MSDN フォーラム](https://social.msdn.microsoft.com/forums/home?forum=servbus)
## [料金](https://azure.microsoft.com/pricing/details/service-bus/)
## [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)
## [価格の詳細](service-bus-pricing-billing.md)
## [サンプル](service-bus-samples.md)
## [ServiceBus360](https://www.servicebus360.com/)
## [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer)
## [サービスの更新情報](https://azure.microsoft.com/updates/?product=service-bus)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azureservicebus)
## [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=service-bus)


