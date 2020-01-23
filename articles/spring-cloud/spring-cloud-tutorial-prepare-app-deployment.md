---
title: チュートリアル - Azure Spring Cloud で Spring アプリケーションをデプロイ用に準備する
description: このチュートリアルでは、Java Spring アプリケーションをデプロイ用に準備します。
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: brendm
ms.openlocfilehash: 9918c7866b21cd2a9e021a355fb43977c91a89cf
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277442"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Azure Spring Cloud で Java Spring アプリケーションをデプロイ用に準備する

このクイックスタートでは、既存の Java Spring Cloud アプリケーションを Azure Spring Cloud へのデプロイ用に準備する方法について説明します。 適切に構成すると、Azure Spring Cloud によって Java Spring Cloud アプリケーションの監視、スケーリング、更新を行う堅牢なサービスが実現します。

## <a name="java-runtime-version"></a>Java ランタイム バージョン

Azure Spring Cloud で稼働できるのは、Spring または Java アプリケーションのみです。

Azure Spring Cloud は、Java 8 と Java 11 の両方をサポートしています。 ホスティング環境には最新バージョンの Azure 用 Azul Zulu OpenJDK が含まれます。 Azure 用 Azul Zulu OpenJDK の詳細については、[JDK のインストール](https://docs.microsoft.com/azure/java/jdk/java-jdk-install)に関するページを参照してください。

## <a name="spring-boot-and-spring-cloud-versions"></a>Spring Boot と Spring Cloud のバージョン

Azure Spring Cloud では、Spring Boot アプリのみがサポートされています。 Spring Boot バージョン 2.0 とバージョン 2.1 の両方がサポートされています。 サポートされている Spring Boot と Spring Cloud の組み合わせを次の表に示します。

Spring Boot のバージョン | Spring Cloud のバージョン
---|---
2.0 | Finchley.RELEASE
2.1 | Greenwich.RELEASE

お使いの Spring Boot バージョンに基づいて正しい Spring Boot および Spring Cloud の依存関係が pom.xml ファイルにあることを確認します。

### <a name="dependencies-for-spring-boot-version-20"></a>Spring Boot バージョン 2.0 の依存関係

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.9.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Finchley.SR4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-21"></a>Spring Boot バージョン 2.1 の依存関係

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.8.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.SR3</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

## <a name="azure-spring-cloud-client-dependency"></a>Azure Spring Cloud クライアントの依存関係

Spring Cloud のコンポーネントは、Azure Spring Cloud によって自動的にホストおよび管理されます。 たとえば、Spring Cloud Service Registry や Spring Cloud Config Server などのコンポーネントです。 自分の Azure Spring Cloud サービス インスタンスと通信できるよう、Azure Spring Cloud クライアント ライブラリを依存関係に含めます。

Spring Boot と Spring Cloud を使用するアプリの正しい Azure Spring Cloud バージョンを次の表に示します。

Spring Boot のバージョン | Spring Cloud のバージョン | Azure Spring Cloud のバージョン
---|---|---
2.0 | Finchley.RELEASE | 2.0
2.1 | Greenwich.RELEASE | 2.1

次のいずれかの依存関係を pom.xml ファイルに含めます。 Azure Spring Cloud バージョンがお使いのものと一致する依存関係を選択します。

### <a name="dependency-for-azure-spring-cloud-version-20"></a>Azure Spring Cloud バージョン 2.0 の依存関係

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.0.0</version>
</dependency>
```

### <a name="dependency-for-azure-spring-cloud-version-21"></a>Azure Spring Cloud バージョン 2.1 の依存関係

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>その他の必要な依存関係

Azure Spring Cloud の組み込み機能を有効にするには、アプリケーションに次の依存関係が含まれている必要があります。 これにより、アプリケーションが各コンポーネントと共に正しく構成されます。  

### <a name="service-registry-dependency"></a>サービス レジストリの依存関係

マネージド Azure Service Registry サービスを使用するには、次に示すように、pom.xml ファイルに `spring-cloud-starter-netflix-eureka-client` 依存関係を含めます。

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

サービス レジストリ サーバーのエンドポイントは、アプリで環境変数として自動的に挿入されます。 その後、アプリケーションによって、アプリケーション自体がサービス レジストリ サーバーに登録され、他の依存マイクロサービスが検出されます。

### <a name="distributed-configuration-dependency"></a>分散構成の依存関係

分散構成を有効にするには、pom.xml ファイルの依存関係セクションに次の `spring-cloud-config-client` の依存関係を含めます。

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> ブートストラップ構成で `spring.cloud.config.enabled=false` を指定しないでください。 そうしないと、アプリケーションと Config Server の連携が停止します。

### <a name="metrics-dependency"></a>メトリックの依存関係

次に示すように、pom.xml ファイルの依存関係セクションに `spring-boot-starter-actuator` の依存関係を含めます。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 メトリックは JMX エンドポイントから定期的に取得されます。 メトリックを視覚化するには、Azure portal を使用します。

### <a name="distributed-tracing-dependency"></a>分散トレースの依存関係

pom.xml ファイルの依存関係セクションに次の `spring-cloud-starter-sleuth` および `spring-cloud-starter-zipkin` の依存関係を含めます。

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

 さらに、自分の Azure Spring Cloud サービス インスタンスと連携できるよう、Azure Application Insights インスタンスを有効にする必要があります。 Application Insights と Azure Spring Cloud を使用する方法については、[分散トレースに関するチュートリアル](spring-cloud-tutorial-distributed-tracing.md)を参照してください。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Java Spring Cloud アプリケーションを Azure Spring Cloud へのデプロイ用に構成する方法について学習しました。 Config Server インスタンスを設定する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Config Server インスタンスを設定する方法を確認する](spring-cloud-tutorial-config-server.md)

その他のサンプルを GitHub で入手できます ([Azure Spring Cloud のサンプル](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql))。
