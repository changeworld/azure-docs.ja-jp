---
title: クイックスタート - サンプル アプリの概要 - Azure Spring Cloud
description: この一連のクイックスタートでは、サンプル アプリを使用して Azure Spring Cloud にデプロイする方法について説明します。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/12/2021
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 2966f77bb070269e11a01303204362d3b6950207
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130005864"
---
# <a name="introduction-to-the-sample-app"></a>サンプル アプリの概要

::: zone pivot="programming-language-csharp"
この一連のクイックスタートでは、2 つのマイクロサービスで構成されるサンプル アプリを使用して、Azure Spring Cloud サービスに .NET Core Steeltoe アプリをデプロイする方法を示します。 サービスの検出、構成サーバー、ログ、メトリック、分散トレースなど、Azure Spring Cloud の機能を使用します。

## <a name="functional-services"></a>機能サービス

サンプル アプリは、次の 2 つのマイクロサービスで構成されています。

* `planet-weather-provider` サービスは、惑星名を指定する HTTP 要求に応答して、気象のテキストを返します。 たとえば、彗星について "非常に暖かい" と返される場合があります。 気象データは、構成サーバーから取得します。 構成サーバーは、次の例のように、Git リポジトリの YAML ファイルから気象データを取得します。

   ```yaml
   MercuryWeather: very warm
   VenusWeather: quite unpleasant
   MarsWeather: very cool
   SaturnWeather: a little bit sandy
   ```

* `solar-system-weather` サービスは、HTTP 要求に応答して 4 つの惑星のデータを返します。 `planet-weather-provider` に 4 つの HTTP 要求を行って、データを取得します。 `planet-weather-provider` の呼び出しには、Eureka サーバーの検出サービスを使用します。 次のような JSON が返されます。

   ```json
   [{
       "Key": "Mercury",
       "Value": "very warm"
   }, {
       "Key": "Venus",
       "Value": "quite unpleasant"
   }, {
       "Key": "Mars",
       "Value": "very cool"
   }, {
       "Key": "Saturn",
       "Value": "a little bit sandy"
   }]
   ```

次の図に、このサンプル アプリのアーキテクチャを示します。

:::image type="content" source="media/spring-cloud-quickstart-sample-app-introduction/sample-app-diagram.png" alt-text="サンプル アプリケーションの図":::

## <a name="code-repository"></a>コード リポジトリ

このサンプル アプリは、GitHub の Azure-Samples/Azure-Spring-Cloud-Samples リポジトリの [steeltoe-sample](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample) フォルダーにあります。

次のクイックスタートの手順では、必要に応じてソース コードを参照しています。

::: zone-end

::: zone pivot="programming-language-java"
このクイックスタートでは、既知のサンプル アプリである [PetClinic](https://github.com/spring-petclinic/spring-petclinic-microservices) のマイクロサービス バージョンを使用して、Azure Spring Cloud サービスにアプリをデプロイする方法を説明します。 **PetClinic** サンプルは、マイクロサービス アーキテクチャ パターンを示し、サービスの内訳を明確に示しています。 サービス検出、構成サーバー、ログ、メトリック、分散トレース、開発者フレンドリなツールのサポートなど、Azure Spring Cloud の機能を使用して、サービスを Azure にデプロイする方法を確認します。

Azure Spring Cloud のデプロイの例を使用するには、ソース コードの場所だけが必要です。これは、必要に応じて提供されます。

![PetClinic のアーキテクチャ](media/build-and-deploy/microservices-architecture-diagram.jpg)

## <a name="functional-services-to-be-deployed"></a>デプロイされる機能サービス

PetClinic は、4 つのコア マイクロサービスに分解されます。 これらのすべては、個別にデプロイできるアプリケーションであり、ビジネス ドメイン別に整理されています。

* **顧客サービス**: 一般的なユーザー入力ロジックと検証が含まれています。ペットと飼い主の情報 (名前、住所、市区町村、電話番号) が含まれます。
* **訪問サービス**: 各ペットのコメントの訪問情報を保存および表示します。
* **獣医サービス**: 名前や専門分野など、獣医の情報を保存および表示します。
* **API ゲートウェイ**: API ゲートウェイは、システムへの単一のエントリ ポイントです。要求を処理し、適切なサービスにルーティングしたり、複数のサービスを呼び出し、結果を集計したりするために使用されます。  3 つのコア サービスは、外部 API をクライアントに公開します。 実際のシステムでは、システムの複雑さによって関数の数が急速に増加する可能性があります。 1 つの複雑な Web ページのレンダリングには、数百のサービスが関係していることがあります。

## <a name="infrastructure-services-hosted-by-azure-spring-cloud"></a>Azure Spring Cloud でホストされるインフラストラクチャ サービス

コア サービスを支援するために、分散システムにはいくつかの一般的なパターンがあります。 Azure Spring Cloud には、Spring Boot アプリケーションを拡張して次のパターンを実装するツールが用意されています。

* **構成サービス**: Azure Spring Cloud Config は、水平方向にスケーラブルな、分散システムのための一元化された構成サービスです。 現時点でローカル ストレージ、Git、および Subversion をサポートしている、プラグ可能なリポジトリを使用します。
* **サービス検出**: 自動スケーリング、障害、アップグレードのために、アドレスが動的に割り当てられた可能性があるサービス インスタンスのネットワークの場所を自動的に検出できます。

## <a name="database-configuration"></a>データベース構成

**PetClinic** の既定の構成では、起動時にデータが入力されるメモリ内データベース (HSQLDB) が使用されます。 永続的なデータベース構成が必要な場合は、MySql 用に同様のセットアップが提供されます。 MySQL JDBC ドライバーである Connector/J の依存関係は、pom.xml ファイルに既に含まれています。

## <a name="sample-usage-of-petclinic"></a>PetClinic の使用例

完全な実装の詳細については、[PetClinic](https://github.com/Azure-Samples/spring-petclinic-microservices) のフォークを参照してください。 このサンプルでは、必要に応じてソース コードを参照します。

::: zone-end

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Spring Cloud インスタンスをプロビジョニングする](./quickstart-provision-service-instance.md)
