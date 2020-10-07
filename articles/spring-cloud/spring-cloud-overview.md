---
title: Azure Spring Cloud の概要
description: Azure で Java Spring アプリケーションをデプロイして管理するための、Azure Spring Cloud の機能とメリットついて説明します。
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: b03bedd9abbe59ed3bc1b0ec1439aa27af57e6da
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2020
ms.locfileid: "91758899"
---
# <a name="what-is-azure-spring-cloud"></a>Azure Spring Cloud とは

Azure Spring Cloud では、Spring Boot ベースのマイクロサービス アプリケーションを、コードの変更なしで簡単に Azure にデプロイできます。  Spring Cloud アプリケーションのインフラストラクチャは Azure Spring Cloud によって管理されるので、開発者は自分のコードに専念することができます。  Spring Cloud は、包括的な監視と診断、構成管理、サービス検出、CI/CD 統合、Blue-Green デプロイなどを使用して、ライフサイクル管理を提供します。

Azure Spring Cloud では、Java [Spring Boot](https://spring.io/projects/spring-boot) と ASP.NET Core [Steeltoe](https://steeltoe.io/) の両方のアプリがサポートされています。 Steeltoe に対するサポートは、現在、パブリック プレビューとして提供されています。 パブリック プレビューの提供では、公式リリースの前に新機能をお試しいただけます。  パブリック プレビューの機能とサービスは、運用環境での使用を目的としたものではありません。  詳細については、[FAQ](https://azure.microsoft.com/support/faq/) をご覧ください。または、[サポート リクエスト](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)を提出してください。

Azure エコシステムの一部として、Azure Spring Cloud は、ストレージ、データベース、監視などの他の Azure サービスに簡単にバインドできます。

この概要では、Azure Spring Cloud の次の機能について説明します。

* 構成サーバー
* Blue/Green デプロイ
* アプリケーションのスケーリング
* Azure DevOps との統合
* アプリケーションの監視

## <a name="spring-cloud-config-server"></a>Spring Cloud Config Server

Azure Spring Cloud Config Server は、サーバー側とクライアント側の両方のサポートを備えた分散システムで、外部化された構成を提供します。  Azure Spring Cloud Config Server は、すべての環境にわたってアプリケーションのプロパティを管理するための一元的な場所です。 詳細については、[Spring Cloud Config Server のリファレンス](https://spring.io/projects/spring-cloud-config)を参照してください。 

## <a name="bluegreen-deployments"></a>Blue/Green デプロイ

Azure Spring Cloud では、運用環境に対してコードのリリースと更新を行うための Blue-Green デプロイをサポートしています。  この変更管理パターンを利用すると、開発者は必要になった場合に即時にフォールバックを実行できるセキュリティ状態で、機能とコードの変更を実装できます。  複数の運用環境で、アプリケーションを中断することなくコードを更新したり変更をロールバックしたりできるため、開発者はコードの記述に専念できます。  ステージング環境と Blue-Green デプロイの詳細については、この[ハウツー記事](spring-cloud-howto-staging-environment.md)を参照してください。

## <a name="cicd-pipeline-automation"></a>CI/CD パイプラインの自動化

Azure Spring Cloud では、Azure CLI を使用した Azure DevOps との統合が提供されます。  Azure DevOps を使用すると、コードの統合と Spring アプリケーションへのデプロイを自動化できます。  詳細については、この[記事](spring-cloud-howto-cicd.md)を参照してください。

## <a name="application-scaling"></a>アプリケーションのスケーリング

Azure Spring Cloud では、Azure Spring Cloud ダッシュボードで、マイクロサービスを簡単にスケーリングできます。  要件に合わせて、マイクロサービスで使用可能な vCPU の数とメモリの量の両方をスケールアップまたはスケールダウンできます。  スケーリングは数秒で有効になり、コードの変更や再デプロイは不要です。  詳細を学習するには、この[チュートリアル](spring-cloud-tutorial-scale-manual.md)を完了してください。

## <a name="application-monitoring"></a>アプリケーションの監視

Spring Cloud の分散トレース ツールを使用すると、開発者はアプリケーション内のマイクロサービス間の複雑な相互接続をデバッグおよび監視できます。  [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) と Azure の [Application Insights](../azure-monitor/insights/insights-overview.md) が統合され、Azure portal から強力な分散トレース機能を利用できます。  詳細を学習するには、この[チュートリアル](spring-cloud-tutorial-distributed-tracing.md)を完了してください。

## <a name="next-steps"></a>次のステップ

まず、[Spring Cloud](spring-cloud-quickstart.md) のクイックスタートを完了します。

サンプルは GitHub で入手できます ([Azure Spring Cloud のサンプル](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/))。
