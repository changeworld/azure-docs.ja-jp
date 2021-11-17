---
title: Container Apps と他の Azure コンテナー オプションの比較
description: Azure Container Apps をどのような場合に使用するかについて、および Azure Container Instances、Azure App Service、Azure Functions、Azure Kubernetes Service などのコンテナー オプションとの比較について説明します。
services: container-apps
author: jeffhollan
ms.service: container-apps
ms.topic: quickstart
ms.date: 11/03/2021
ms.author: jehollan
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0ac05794b42e91060e604a9d6921547150eb6706
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132309998"
---
# <a name="comparing-container-apps-with-other-azure-container-options"></a>Container Apps と他の Azure コンテナー オプションの比較

チームには、クラウド ネイティブおよびコンテナー化されたアプリケーションを Azure でビルドしてデプロイするための多くのオプションがあります。 この記事は、Azure Container Apps に最適なシナリオとユース ケース、および次に示す Azure 上の他のコンテナー オプションとの比較を理解するのに役立ちます。  
- [Azure App Service](#azure-app-service)
- [Azure Container Instances](#azure-container-instances)
- [Azure Kubernetes Service](#azure-kubernetes-service)
- [Azure Functions](#azure-functions)
- [Azure Spring Cloud](#azure-spring-cloud)

すべてのユース ケースとすべてのチームに完璧なソリューションはありません。 次の説明では、チームと要件に最適なものを見つけるのに役立つ一般的なガイダンスと推奨事項を示します。

> [!IMPORTANT]
> Azure Container Apps は現在パブリック プレビュー段階ですが、他のオプションは一般提供 (GA) されています。


## <a name="container-option-comparisons"></a>コンテナー オプションの比較

### <a name="azure-container-apps"></a>Azure Container Apps
Azure Container Apps を使用すると、コンテナーに基づいてサーバーレス マイクロサービスを構築できます。 Container Apps の特徴は次のとおりです。

* 汎用コンテナー (特に、コンテナーにデプロイされた多くのマイクロサービスにまたがるアプリケーション向け) を実行するように最適化されています。
* Kubernetes と、[Dapr](https://dapr.io/)、[KEDA](https://keda.sh/)、[envoy](https://www.envoyproxy.io/) などのオープンソースのテクノロジが利用されています。
* [サービス検出](connect-apps.md)や[トラフィック分割](revisions.md)などの機能によって Kubernetes スタイルのアプリやマイクロサービスをサポートします。
* トラフィックに基づくスケーリングをサポートし、[ゼロにスケーリング](scale-app.md)など、[キューのようなイベント ソース](scale-app.md)からプルすることで、イベント駆動型アプリケーション アーキテクチャを有効にします。
* 実行時間の長いプロセスをサポートし、[バックグラウンド タスク](background-processing.md)を実行できます。

Azure Container Apps では、基になる Kubernetes API への直接アクセスは提供されません。 Kubernetes API とコントロール プレーンにアクセスする必要がある場合は、[Azure Kubernetes Service](../aks/intro-kubernetes.md) を使用してください。 ただし、Kubernetes スタイルのアプリケーションを構築したいが、すべてのネイティブ Kubernetes API とクラスター管理への直接アクセスは必要ない場合は、Container Apps により、ベスト プラクティスに基づくフル マネージド エクスペリエンスが提供されます。 このような理由から、多くのチームが、Azure Container Apps を使用してコンテナー マイクロサービスの構築を開始することを選択する場合があります。

### <a name="azure-app-service"></a>Azure App Service
Azure App Service では、Web サイトや Web API を含む、Web アプリケーション用のフル マネージド ホスティングが提供されます。 これらの Web アプリケーションは、コードまたはコンテナーを使用してデプロイできます。 Azure App Service は、Web アプリケーション用に最適化されています。 Azure App Service は、Azure Container Apps や Azure Functions などの他の Azure サービスと統合されます。 Web アプリを構築する場合、Azure App Service は理想的な選択肢です。

### <a name="azure-container-instances"></a>Azure Container Instances
Azure Container Instances (ACI) では、オンデマンドで Hyper-V 分離コンテナーの単一ポッドが提供されます。 これは、Container Apps と比較した場合、下位レベルの "ビルディング ブロック" オプションと考えることができます。 スケーリング、負荷分散、証明書のような概念は、ACI コンテナーでは提供されません。 たとえば、5 つのコンテナー インスタンスにスケーリングするには、5 つの個別のコンテナー インスタンスを作成します。 Azure Container Apps によって、証明書、リビジョン、スケーリング、環境など、多くのアプリケーション固有概念がコンテナーの上に提供されます。 多くの場合、ユーザーは他のサービスを介して Azure Container Instances を操作します。 たとえば、Azure Kubernetes Service では、[仮想ノード](../aks/virtual-nodes.md)を介して、ACI の上にオーケストレーションとスケーリングをレイヤー化できます。 Azure Container Apps で最適化されているシナリオに一致しない、あまり "厳格" でない構成ブロックが必要な場合、Azure Container Instances は理想的なオプションです。

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service
Azure Kubernetes Service では、Azure でフル マネージド Kubernetes オプションが提供されます。 Kubernetes API への直接アクセスがサポートされ、すべての Kubernetes ワークロードが実行されます。 クラスター全体がサブスクリプション内に存在し、クラスターの構成と運用はユーザーの制御と責任の範囲内となります。 Azure で Kubernetes のフル マネージド バージョンを探しているチームにとって、Azure Kubernetes Service は理想的なオプションです。

### <a name="azure-functions"></a>Azure Functions
Azure Functions は、サーバーレスのサービスとしての関数 (FaaS) ソリューションです。 これは、関数プログラミング モデルを使用してイベント駆動型関数を実行するように最適化されています。 スケールやイベントとの統合に関して Azure Container Apps と多くの特性を共有していますが、コードまたはコンテナーとしてデプロイされるエフェメラル関数用に最適化されています。 Azure Functions プログラミング モデルには、イベントでトリガーし、他のデータ ソースにバインドしようとするチームにとって生産性のメリットがあります。 FaaS スタイルの関数を構築する場合は、Azure Functions が理想的なオプションです。 Azure Functions プログラミング モデルは、基本コンテナー イメージとして使用でき、他のコンテナー ベースのコンピューティング プラットフォームに移植可能です。そのため、チームは環境要件の変化に応じてコードを再利用できます。

### <a name="azure-spring-cloud"></a>Azure Spring Cloud
Azure Spring Cloud では、コードを変更せずに、Spring Boot マイクロサービス アプリケーションを Azure に簡単にデプロイできます。 Spring Cloud アプリケーションのインフラストラクチャはこのサービスによって管理されるので、開発者はコードに専念できます。 Azure Spring Cloud は、包括的な監視と診断、構成管理、サービス検出、CI/CD 統合、ブルー/グリーン デプロイなどを使用して、ライフサイクル管理を提供します。 チームまたは組織が主に Spring である場合は、Azure Spring Cloud が理想的なオプションです。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [初めてのコンテナー アプリをデプロイする](get-started.md)
