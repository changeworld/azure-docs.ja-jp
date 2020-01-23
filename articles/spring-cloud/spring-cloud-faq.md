---
title: Azure Spring Cloud についてよく寄せられる質問 | Microsoft Docs
description: この記事では、Azure Spring Cloud についてよく寄せられる質問に回答します。
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.openlocfilehash: e4fbeef06ae49ffe24f84b1a12dbcdfe0a5f1fec
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278567"
---
# <a name="azure-spring-cloud-faq"></a>Azure Spring Cloud に関する FAQ

この記事では、Azure Spring Cloud についてよく寄せられる質問に回答します。 

## <a name="general"></a>全般

### <a name="why-azure-spring-cloud"></a>Azure Spring Cloud が必要なのはなぜですか?

Azure Spring Cloud は、Spring Cloud 開発者のためのサービスとしてのプラットフォーム (PaaS) を提供します。 Azure Spring Cloud がアプリケーション インフラストラクチャを管理するため、開発者はアプリケーション コードやビジネス ロジックに焦点を絞ることができます。 Azure Spring Cloud に組み込まれているコア機能には、Eureka、構成サーバー、サービス レジストリ サーバー、Pivotal Build Service、ブルーグリーン デプロイなどが含まれます。 このサービスにより、開発者はアプリケーションを Azure Cosmos DB、Azure Database for MySQL、Azure Cache for Redis などの他の Azure サービスにバインドすることもできます。

Azure Spring Cloud は、Azure Monitor、Application Insights、および Log Analytics を統合することによって、開発者やオペレーターのアプリケーション診断エクスペリエンスを拡張します。

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Azure Spring Cloud にはどのようなサービス プランがあるのでしょうか?

プレビュー期間中は、Azure Spring Cloud で提供されるサービス プランは 1 つです。  Spring Cloud のデプロイには、16 個の vCPU コアと 32 GB のメモリが含まれています。  デプロイ内の各マイクロサービス インスタンスの上限は、4 つの vCPU コアと 8 GB のメモリです。

リソース | 金額
------- | -------
Spring アプリケーションあたりのアプリ インスタンスの数 | 20
Azure Spring Cloud サービス インスタンスあたりのアプリ インスタンスの合計数 | 500
サブスクリプション 1 件、1 リージョンあたりの Azure Spring Cloud サービス インスタンスの数 | 10
永続ボリューム | 10 x 50 GB

\*_制限を増やすには、[サポート チケット](https://azure.microsoft.com/support/faq/)を開いてください。_

詳細については、「[Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。

### <a name="how-secure-is-azure-spring-cloud"></a>Azure Spring Cloud のセキュリティはどの程度でしょうか?

セキュリティとプライバシーは、Azure や Azure Spring Cloud のお客様にとっての最優先事項です。 Azure では、アプリケーション データ、ログ、または構成を確実に暗号化することにより、お客様だけがこれらのすべてのデータにアクセスできるようにしています。 Azure Spring Cloud のサービス インスタンスはすべて、相互に分離されています。

Azure Spring Cloud には、SSL と証明書の包括的な管理機能が用意されています。

OpenJDK と Spring Cloud のランタイムに重要なセキュリティ パッチがあるときは、可能な限り早期に Azure Spring Cloud に適用されます。

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>Azure Spring Cloud はどのリージョンで使用できますか?

米国東部、米国西部 2、西ヨーロッパ、東南アジアです。

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Azure Spring Cloud の既知の制限事項はどのようなものですか?

プレビュー リリース中、Azure Spring Cloud には次の既知の制限事項があります。

* `spring.application.name` が、各アプリケーションを作成するために使用されるアプリケーション名によって上書きされます。
* `server.port` が、Git リポジトリの構成ファイルで許可されていません。 これを構成ファイルに追加すると、他のアプリケーションやインターネットからアプリケーションにアクセスできなくなる可能性があります。
* Azure portal と Azure Resource Manager テンプレートがアプリケーション パッケージのアップロードをサポートしていません。 Azure CLI 経由でアプリケーションをデプロイすることによってのみ、アプリケーション パッケージをアップロードできます。
* クォータ制限については、「[Azure Spring Cloud にはどのようなサービス プランがあるのでしょうか?](#what-service-plans-does-azure-spring-cloud-offer)」を参照してください。

### <a name="how-can-i-provide-feedback-and-report-issues"></a>フィードバックの提供や問題の報告はどのようにするのでしょうか?

Azure Spring Cloud で問題が発生した場合は、[Azure サポート要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)を作成してください。 機能要求を送信するか、またはフィードバックを提供するには、[Azure フィードバック](https://feedback.azure.com/forums/34192--general-feedback)に移動してください。

## <a name="development"></a>開発

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>私は Spring Cloud 開発者ですが、Azure は初めてです。 Azure Spring Cloud アプリケーションの開発方法を学習するための最もすばやい方法は何ですか?

Azure Spring Cloud の使用を開始するための最もすばやい方法として、「[クイック スタート: Azure portal を使用して Azure Spring Cloud アプリケーションを起動する](spring-cloud-quickstart-launch-app-portal.md)」の手順に従ってください。

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Azure Spring Cloud はどの Java ランタイムをサポートしていますか?

Azure Spring Cloud は、Java 8 および 11 をサポートしています。

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Spring Cloud アプリケーションのログとメトリックはどこで表示できますか?

[アプリの概要] タブと [[Azure Monitor]](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics) タブでメトリックを探してください。

Azure Spring Cloud は、Spring Cloud アプリケーションのログとメトリックの Azure Storage、EventHub、および [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries) へのエクスポートをサポートしています。 Log Analytics でのテーブル名は *AppPlatformLogsforSpring* です。 これを有効にする方法については、[診断サービス](diagnostic-services.md)に関するページを参照してください。

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Azure Spring Cloud は分散トレースをサポートしていますか?

はい。 詳細については、「[チュートリアル:Azure Spring Cloud で分散トレースを使用する](spring-cloud-tutorial-distributed-tracing.md)」を参照してください。

### <a name="what-resource-types-does-service-binding-support"></a>サービス バインディングでサポートされるリソースの種類は何ですか?

現時点でサポートされているサービスは次の 3 種類です: Azure Cosmos DB、Azure Database for MySQL、および Azure Cache for Redis。

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>アプリケーション内から永続ボリュームを表示、追加、または移動できますか?

はい。

## <a name="deployment"></a>デプロイ

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Azure Spring Cloud はブルーグリーン デプロイをサポートしていますか?
はい。 詳細については、[ステージング環境の設定](spring-cloud-howto-staging-environment.md)に関するページを参照してください。

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Kubernetes にアクセスしてアプリケーション コンテナーを操作することはできるのでしょうか?

いいえ。  Azure Spring Cloud は、基盤となるアーキテクチャの管理作業から開発者を解放し、開発者がアプリケーションのコードとビジネス ロジックに専念できるようにするためのものです。

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Azure Spring Cloud でソースからコンテナーを構築することはできますか?

はい。 詳細については、「[ソース コードから Spring Cloud アプリケーションを起動する](spring-cloud-launch-from-source.md)」を参照してください。

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Azure Spring Cloud はアプリ インスタンス内での自動スケーリングをサポートしていますか?

いいえ。

### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>既存の Spring Cloud マイクロサービスを Azure Spring Cloud に移行するためのベスト プラクティスはどのようなものですか?

既存の Spring Cloud マイクロサービスを Azure Spring Cloud に移行しようとしている場合は、次のベスト プラクティスを確認することをお勧めします。
* アプリケーションの依存関係をすべて解決しておく必要があります。
* 構成エントリ、環境変数、および JVM パラメーターを準備しておき、それらを Azure Spring Cloud でのデプロイと比較できるようにします。
* サービス バインドを使用する場合は、Azure サービスをチェックし、適切なアクセス許可を設定していることを確認してください。
* Azure Spring Cloud によって管理されるサービスと競合する可能性のある組み込みサービス (サービス検出サービスや構成サーバーなど) をすべて削除するか、または無効にすることをお勧めします。
* 公式の、安定した Pivotal Spring ライブラリを使用することをお勧めします。 非公式版、ベータ版、またはフォーク済みバージョンの Pivotal Spring ライブラリにはサービス レベル アグリーメント (SLA) のサポートがありません。

移行したら、CPU/RAM のメトリックやネットワーク トラフィックを監視して、アプリケーション インスタンスが適切にスケーリングされていることを確認します。

## <a name="next-steps"></a>次のステップ

さらに質問がある場合は、[Azure Spring Cloud のトラブルシューティング ガイド](spring-cloud-troubleshoot.md)に関するページを参照してください。
