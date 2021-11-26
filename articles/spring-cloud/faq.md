---
title: Azure Spring Cloud についてよく寄せられる質問 | Microsoft Docs
description: この記事では、Azure Spring Cloud についてよく寄せられる質問に回答します。
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: karler
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: ecc563cfc400fcf0e90320cb5c2436fe84cfa0d0
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132704004"
---
# <a name="azure-spring-cloud-faq"></a>Azure Spring Cloud に関する FAQ

この記事では、Azure Spring Cloud についてよく寄せられる質問に回答します。

## <a name="general"></a>全般

### <a name="why-azure-spring-cloud"></a>Azure Spring Cloud が必要なのはなぜですか?

Azure Spring Cloud は、Spring Cloud 開発者のためのサービスとしてのプラットフォーム (PaaS) を提供します。 Azure Spring Cloud がアプリケーション インフラストラクチャを管理するため、開発者はアプリケーション コードやビジネス ロジックに焦点を絞ることができます。 Azure Spring Cloud に組み込まれているコア機能には、Eureka、構成サーバー、サービス レジストリ サーバー、Pivotal Build Service、ブルーグリーン デプロイなどが含まれます。 このサービスにより、開発者はアプリケーションを Azure Cosmos DB、Azure Database for MySQL、Azure Cache for Redis などの他の Azure サービスにバインドすることもできます。

Azure Spring Cloud は、Azure Monitor、Application Insights、および Log Analytics を統合することによって、開発者やオペレーターのアプリケーション診断エクスペリエンスを拡張します。

### <a name="how-secure-is-azure-spring-cloud"></a>Azure Spring Cloud のセキュリティはどの程度でしょうか?

セキュリティとプライバシーは、Azure や Azure Spring Cloud のお客様にとっての最優先事項です。 Azure では、アプリケーション データ、ログ、または構成を確実に暗号化することにより、お客様だけがこれらのすべてのデータにアクセスできるようにしています。

* Azure Spring Cloud のサービス インスタンスは、相互に分離されています。
* Azure Spring Cloud には、TLS/SSL と証明書の包括的な管理機能が用意されています。
* OpenJDK と Spring Cloud のランタイムに重要なセキュリティ パッチがあるときは、可能な限り早期に Azure Spring Cloud に適用されます。

### <a name="how-does-azure-spring-cloud-host-my-applications"></a>Azure Spring Cloud はアプリケーションをホストするにはどうすればよいですか。

Azure Spring Cloud の各サービスインスタンスは、複数のワーカーノードを持つ完全に専用の Kubernetes クラスターによって支えられています。 Azure Spring Cloud は、高可用性やスケーラビリティやKubernetes バージョンのアップグレードなど基になる Kubernetes クラスターを管理します。

Azure Spring Cloud は、基になる Kubernetes worker ノードでアプリケーションをインテリジェントにスケジュールします。 高可用性を実現するために、Azure Spring Cloud は異なるノードに2つ以上のインスタンスを持つアプリケーションを配布します。

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>Azure Spring Cloud はどのリージョンで使用できますか?

米国東部、米国東部 2、米国中部、米国中南部、米国中北部、米国西部、米国西部 2、西ヨーロッパ、北ヨーロッパ、英国南部、東南アジア、オーストラリア東部、カナダ中部、アラブ首長国連邦北部、インド中部、韓国中部、東アジア、東日本、南アフリカ北部、ブラジル南部、フランス中部、中国東部 2 (Mooncake)。 [詳細情報](https://azure.microsoft.com/global-infrastructure/services/?products=spring-cloud)

### <a name="is-any-customer-data-stored-outside-of-the-specified-region"></a>指定されたリージョン外に格納されている顧客データはありますか?

Azure Spring Cloud はリージョン サービスです。 Azure Spring Cloud の顧客データは全て、指定された 1 つのリージョンに格納されます。 地域とリージョンの詳細については、「[Azure でのデータ所在地](https://azure.microsoft.com/global-infrastructure/data-residency/)」を参照してください。

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Azure Spring Cloud の既知の制限事項はどのようなものですか?

Azure Spring Cloud には、次の既知の制限があります。

* `spring.application.name` が、各アプリケーションを作成するために使用されるアプリケーション名によって上書きされます。
* `server.port` の既定のポートは 1025 です。 他の値が適用されている場合は上書きされます。 また、この設定を尊重するようにして、コードでサーバーのポートを指定しないでください。
* Azure portal、Azure Resource Manager テンプレート、および Terraform は、アプリケーションパッケージのアップロードをサポートしていません。 アプリケーション パッケージをアップロードするには、Azure CLI、Azure DevOps、Azure Spring Cloud 用の Maven プラグイン、Azure Toolkit for IntelliJ、Azure Spring cloud の Visual Studio Code 拡張機能を使用してアプリケーションをデプロイします。

### <a name="what-pricing-tiers-are-available"></a>利用可能な価格レベルを教えてください。

どれを使用すればよいでしょうか? また、各レベルにはどのような制限がありますか?

* Azure Spring Cloud には、2 つの価格レベルがあります。Basic と Standard です。 Basic レベルは、Dev/Test、および Azure Spring Cloud の試用を目的としています。 Standard レベルは、汎用の運用トラフィックを実行するために最適化されています。 制限と機能レベルの比較については、[Azure Spring Cloud の価格の詳細](https://azure.microsoft.com/pricing/details/spring-cloud/)に関する記事を参照してください。

### <a name="whats-the-difference-between-service-binding-and-service-connector"></a>サービスバインドと Service Connector の違いは何ですか?
[Service Connector](../service-connector/overview.md) という新しい Azure を優先するソリューションを使用して、サービスバインドの追加機能を積極的に開発していません。 一方、新しいソリューションでは、App Service のように、Azure 上のアプリホスティングサービス間に一貫性のある統合エクスペリエンスを提供します。 これに対して、MySQLやSQL DBやCosmos DB、Postgres DBやRedisやStorage など使用されている対象の Azure サービスのうち、最も多くのものを10⁺サポートすることから始めて、ニーズにも対応できます。 Service Connector は現在パブリックプレビュー中です。新しいエクスペリエンスをお試し頂き、どうぞよろしくお願いいたします。

### <a name="how-can-i-provide-feedback-and-report-issues"></a>フィードバックの提供や問題の報告はどのようにするのでしょうか?

Azure Spring Cloud で問題が発生した場合は、[Azure サポート要求](../azure-portal/supportability/how-to-create-azure-support-request.md)を作成してください。 機能要求を送信するか、またはフィードバックを提供するには、[Azure フィードバック](https://feedback.azure.com/d365community/forum/79b1327d-d925-ec11-b6e6-000d3a4f06a4)に移動してください。

## <a name="development"></a>開発

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-application-in-azure-spring-cloud"></a>私は Spring Cloud 開発者ですが、Azure は初めてです。 Azure Spring Cloud におけるアプリケーションの開発方法を学習する最短の方法は何ですか?

Azure Spring Cloud を最短で始める方法については、[Azure portal を使用して Azure Spring Cloud のアプリケーションを立ち上げる方法についてのクイックスタート](./quickstart.md)の手順に従ってください。

::: zone pivot="programming-language-java"
### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Azure Spring Cloud はどの Java ランタイムをサポートしていますか?

Azure Spring Cloud は、Java 8 および 11 をサポートしています。 「[Java ランタイムと OS バージョン](#java-runtime-and-os-versions)」を参照してください。

### <a name="is-spring-boot-24x-supported"></a>Spring Boot 2.4.x はサポートされていますか?
Microsoft は、Spring Boot 2.4 の問題を特定しました。現在、Spring コミュニティと協力してその解決に取り組んでいます。 その間は、アプリと Eureka の間で TLS 認証を有効にするために、これらの 2 つの依存関係を含めてください。

```xml
<dependency>
    <groupId>com.sun.jersey</groupId>
    <artifactId>jersey-client</artifactId>
    <version>1.19.4</version>
</dependency>
<dependency>
    <groupId>com.sun.jersey.contribs</groupId>
    <artifactId>jersey-apache-client4</artifactId>
    <version>1.19.4</version>
</dependency>
```

::: zone-end

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Spring Cloud アプリケーションのログとメトリックはどこで表示できますか?

[アプリの概要] タブと [[Azure Monitor]](../azure-monitor/essentials/data-platform-metrics.md#metrics-explorer) タブでメトリックを探してください。

Azure Spring Cloud は、Spring Cloud アプリケーションのログとメトリックの Azure Storage、Event Hub、および [Log Analytics](../azure-monitor/logs/data-platform-logs.md) へのエクスポートをサポートしています。 Log Analytics でのテーブル名は *AppPlatformLogsforSpring* です。 これを有効にする方法については、[診断サービス](diagnostic-services.md)に関するページを参照してください。

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Azure Spring Cloud は分散トレースをサポートしていますか?

はい。 詳細については、[Azure Spring Cloud で分散トレースを使用する](./how-to-distributed-tracing.md)」を参照してください。

::: zone pivot="programming-language-java"
### <a name="what-resource-types-does-service-binding-support"></a>サービス バインディングでサポートされるリソースの種類は何ですか?

現時点でサポートされているサービスは次の 3 種類です:

* Azure Cosmos DB
* Azure Database for MySQL
* Azure Cache for Redis
::: zone-end

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>アプリケーション内から永続ボリュームを表示、追加、または移動できますか?

はい。

### <a name="how-many-outbound-public-ip-addresses-does-an-azure-spring-cloud-instance-have"></a>Azure Spring Cloud インスタンスには、送信パブリック IP アドレスはいくつありますか?

送信パブリック IP アドレスの数は、レベルやその他の要因によって異なる場合があります。

| Azure Spring Cloud インスタンスの種類 | 送信パブリック IP アドレスの既定の数 |
| -------------------------------- | ---------------------------------------------- |
| Basic レベルのインスタンス             | 1                                              |
| Standard レベルのインスタンス          | 2                                              |
| VNet インジェクション インスタンス         | 1                                              |

### <a name="can-i-increase-the-number-of-outbound-public-ip-addresses"></a>送信パブリック IP アドレスの数を増やすことはできますか?

はい。[サポート チケット](https://azure.microsoft.com/support/faq/)を開いて、より多くの送信パブリック IP アドレスを要求することができます。

### <a name="when-i-deletemove-an-azure-spring-cloud-service-instance-will-its-extension-resources-be-deletedmoved-as-well"></a>Azure Spring Cloud サービス インスタンスを削除または移動すると、その拡張機能リソースも削除または移動されますか?

拡張リソースが所有するリソース プロバイダーのロジックによって異なります。 `Microsoft.AppPlatform` インスタンスの拡張リソースは同じ名前空間に属していないため、動作はリソース プロバイダーによって異なります。 たとえば、削除または移動操作は、**診断設定** リソースにカスケードされません。 新しい Azure Spring Cloud インスタンスが、削除されたものと同じリソース ID でプロビジョニングされる場合、または以前の Azure Spring Cloud インスタンスが戻った場合は、以前の **診断設定** リソースが引き続きそれを拡張します。

Azure CLI を使用して、Spring Cloud の診断設定を削除できます。

```azurecli
 az monitor diagnostic-settings delete --name $diagnosticSettingName --resource $azureSpringCloudResourceId
```

::: zone pivot="programming-language-java"
## <a name="java-runtime-and-os-versions"></a>Java ランタイムと OS バージョン

### <a name="which-versions-of-java-runtime-are-supported-in-azure-spring-cloud"></a>Azure Spring Cloud では、どのバージョンの Java ランタイムがサポートされていますか?

Azure Spring Cloud では、2020 年 6 月現在での最新のビルドを含む Java LTS バージョン、Java 8 および Java 11 がサポートされています。 「[Azure 用の JDK および Azure Stack をインストールする](/azure/developer/java/fundamentals/java-jdk-install)」を参照してください。

### <a name="who-built-these-java-runtimes"></a>これらの Java ランタイムはだれがビルドしたのですか?

Azul Systems です。 Azul Zulu for Azure - Enterprise Edition JDK ビルドは、Microsoft および Azul Systems によってサポートされる、無料でマルチプラットフォーム対応かつ実稼働可能な、Azure と Azure Stack 用の OpenJDK のディストリビューションです。 これらには、Java SE アプリケーションを構築および実行するためのすべてのコンポーネントが含まれています。

### <a name="how-often-will-java-runtimes-get-updated"></a>Java ランタイムはどのくらいの頻度で更新されますか?

LTS と MTS の JDK リリースには、四半期ごとのセキュリティ更新プログラムとバグ修正が含まれ、必要に応じて、重要なアウトオブバンドの更新プログラムと修正プログラムも含まれます。 このサポートには、Java 11 などの新しいバージョンの Java で報告された、セキュリティ更新プログラムとバグ修正の Java 7 および 8 へのバックポートが含まれています。

### <a name="how-long-will-java-8-and-java-11-lts-versions-be-supported"></a>Java 8 LTS バージョンと Java 11 LTS バージョンのサポート期間を教えてください。

[Azure および Azure Stack の Java 長期サポート](/azure/developer/java/fundamentals/java-support-on-azure)に関する記事を参照してください。

* Java 8 LTS は 2030 年 12 月までサポートされます。
* Java 11 LTS は 2027 年 9 月までサポートされます。

### <a name="how-can-i-download-a-supported-java-runtime-for-local-development"></a>サポートされている Java ランタイムをローカル開発用にダウンロードするにはどうすればよいですか?

「[Azure 用の JDK および Azure Stack をインストールする](/azure/developer/java/fundamentals/java-jdk-install)」を参照してください。

### <a name="what-is-the-retire-policy-for-older-java-runtimes"></a>古い Java ランタイムの削除ポリシーとは何ですか?

古いランタイム バージョンが廃止される 12 か月前に公開通知が送信されます。 新しいバージョンへの移行に 12 か月の猶予があることになります。

* サブスクリプション管理者は、Java バージョンがインベントリから削除される時点で電子メール通知を受け取ります。
* インベントリからの削除に関する情報は、ドキュメントに記載されます。

### <a name="how-can-i-get-support-for-issues-at-the-java-runtime-level"></a>Java ランタイム レベルの問題についてサポートを受けるにはどうすればよいですか?

Azure サポートでサポート チケットを開くことができます。  [Azure サポート リクエストを作成する方法](../azure-portal/supportability/how-to-create-azure-support-request.md)に関する記事を参照してください。

### <a name="what-is-the-operation-system-to-run-my-apps"></a>アプリを実行するためのオペレーティング システムは何ですか?

最新の Ubuntu LTS バージョンが使用されており、現在は、[Ubuntu 20.04 LTS (Focal Fossa)](https://releases.ubuntu.com/focal/) が既定の OS となっています。

### <a name="how-often-are-os-security-patches-applied"></a>OS のセキュリティ パッチはどのくらいの頻度で適用されますか?

Azure Spring Cloud に適用されるセキュリティ パッチは、月単位で運用環境に展開されます。
Azure Spring Cloud に適用される重要なセキュリティ パッチ (CVE score 9 以上) は、可能な限り迅速に展開されます。
::: zone-end

## <a name="deployment"></a>デプロイ

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Azure Spring Cloud はブルーグリーン デプロイをサポートしていますか?

はい。 詳細については、[ステージング環境の設定](./how-to-staging-environment.md)に関するページを参照してください。

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Kubernetes にアクセスしてアプリケーション コンテナーを操作することはできるのでしょうか?

いいえ。  Azure Spring Cloud は、基盤となるアーキテクチャの管理作業から開発者を解放し、開発者がアプリケーションのコードとビジネス ロジックに専念できるようにするためのものです。

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Azure Spring Cloud でソースからコンテナーを構築することはできますか?

はい。 詳細については、「[ソース コードから Spring Cloud アプリケーションを起動する](./quickstart.md)」を参照してください。

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Azure Spring Cloud はアプリ インスタンス内での自動スケーリングをサポートしていますか?

はい。 詳細については、[マイクロサービスアプリケーションの自動スケールの設定](./how-to-setup-autoscale.md)を参照してください。

### <a name="how-does-azure-spring-cloud-monitor-the-health-status-of-my-application"></a>Azure Spring Cloud はアプリケーションの正常性状態をどのように監視しますか。

Azure Spring Cloud は、お客様のアプリケーションのポート1025を継続的に調査します。 これのプローブは、アプリケーションコンテナーがトラフィックの受け入れを開始する準備ができているかどうか、および Azure Spring Cloud でアプリケーションコンテナーを再起動する必要があるかどうかを判断します。 内部的には、Azure Spring Cloud は Kubernetes の活性および準備プローブを使用して、状態の監視を実現します。

>[!NOTE]
> これらのプローブにより、現在、ポート1025を公開せずに Azure Spring Cloud でアプリケーションを起動することはできません。

### <a name="whether-and-when-will-my-application-be-restarted"></a>アプリケーションを再起動するかどうかを指定します。

はい。 詳細について[Azure のアクティビティログと Azure Service Health を使用したアプリのライフサイクルイベントの監視](./monitor-app-lifecycle-events.md)を参照してください。

::: zone pivot="programming-language-java"
### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>既存の Spring Cloud マイクロサービスを Azure Spring Cloud に移行するためのベスト プラクティスはどのようなものですか?

詳細については、[アプリケーションの移行をSpring Cloudを参照Azure Spring Cloud](/azure/developer/java/migration/migrate-spring-cloud-to-azure-spring-cloud)。
::: zone-end

::: zone pivot="programming-language-csharp"
## <a name="net-core-versions"></a>.NET Core のバージョン

### <a name="which-net-core-versions-are-supported"></a>どのバージョンの .NET Core がサポートされていますか?

.NET Core 3.1 以降のバージョンです。

### <a name="how-long-will-net-core-31-be-supported"></a>.NET Core 3.1 はどのくらいの期間サポートされますか?

2022 年 12 月 3 日までです。 「[.NET Core サポート ポリシー](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)」を参照してください。
::: zone-end

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="what-are-the-impacts-of-service-registry-rarely-unavailable"></a>サービス レジストリがまれに利用できなくなると、どのような影響がありますか?

あまり起こることではありませんが、アプリケーション ログに次のようなエラーが表示されることがあります。

```output
RetryableEurekaHttpClient: Request execution failure with status code 401; retrying on another server if available
```

この問題は、ネットワークの不安定性またはその他のネットワークの問題により、速度が極端に低下した Spring フレームワークによって発生します。

Eureka クライアントには、それに対処するハートビートと再試行ポリシーの両方が備わっているため、ユーザー エクスペリエンスに影響はありません。 一時的なエラーと考えられるので、無視してかまいません。

この点については、今後ユーザーのアプリケーションからこのエラーが返されないよう改善する予定です。

## <a name="next-steps"></a>次のステップ

さらに質問がある場合は、[Azure Spring Cloud のトラブルシューティング ガイド](./troubleshoot.md)に関するページを参照してください。