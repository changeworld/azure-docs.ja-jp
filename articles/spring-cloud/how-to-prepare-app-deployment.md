---
title: Azure Spring Cloud にデプロイするアプリケーションを準備する方法
description: Azure Spring Cloud にデプロイするアプリケーションを準備する方法について説明します。
author: karlerickson
ms.service: spring-cloud
ms.topic: how-to
ms.date: 07/06/2021
ms.author: karler
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 913d750353053398015da9939ec35f0f9b0de1c5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131013709"
---
# <a name="prepare-an-application-for-deployment-in-azure-spring-cloud"></a>Azure Spring Cloud にデプロイするアプリケーションを準備する

::: zone pivot="programming-language-csharp"
Azure Spring Cloud は、Steeltoe アプリをホスト、監視、スケール、更新するための堅牢なサービスを提供しています。 この記事では、Azure Spring Cloud にデプロイするために既存の Steeltoe アプリケーションを準備する方法について説明します。

この記事では、Azure Spring Cloud で .NET Core Steeltoe アプリを実行するために必要な依存関係、構成、およびコードについて説明します。 アプリケーションを Azure Spring Cloud にデプロイする方法については、「[Azure Spring Cloud での初めての Spring Boot アプリケーションをデプロイする](./quickstart.md)」を参照してください。

>[!Note]
> Azure Spring Cloud の Steeltoe のサポートは、現時点ではパブリック プレビューとして提供されています。 パブリック プレビュー オファリングにより、お客様は公式リリースの前に新機能を試すことができます。  パブリック プレビューの機能とサービスは、運用環境での使用を目的としたものではありません。  プレビュー段階のサポートの詳細については、[FAQ](https://azure.microsoft.com/support/faq/) を参照するか、[サポート リクエスト](../azure-portal/supportability/how-to-create-azure-support-request.md)を提出してください。

## <a name="supported-versions"></a>サポートされているバージョン

Azure Spring Cloud は以下をサポートしています。

* .NET Core 3.1
* Steeltoe 2.4 および 3.0

## <a name="dependencies"></a>依存関係

Steeltoe 2.4 の場合は、最新の [Microsoft.Azure.SpringCloud.Client 1.x.x](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) パッケージをプロジェクト ファイルに追加します。

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="1.0.0-preview.1" />
  <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Extensions.Configuration.ConfigServerCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Management.TracingCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Management.ExporterCore" Version="2.4.4" />
</ItemGroup>
```

Steeltoe 3.0 の場合は、最新の [Microsoft.Azure.SpringCloud.Client 2.x.x](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) パッケージをプロジェクト ファイルに追加します。

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="2.0.0-preview.1" />
  <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="3.0.0" />
  <PackageReference Include="Steeltoe.Extensions.Configuration.ConfigServerCore" Version="3.0.0" />
  <PackageReference Include="Steeltoe.Management.TracingCore" Version="3.0.0" />
</ItemGroup>
```

## <a name="update-programcs"></a>Program.cs の更新

`Program.Main` メソッドで、`UseAzureSpringCloudService` メソッドを呼び出します。

Steeltoe 2.4.4 の場合は、`ConfigureWebHostDefaults` および `AddConfigServer` (呼び出される場合) の後に `UseAzureSpringCloudService` を呼び出します。

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .AddConfigServer()
        .UseAzureSpringCloudService();
```

Steeltoe 3.0.0 の場合は、`ConfigureWebHostDefaults` および Steeltoe 構成コードの前に `UseAzureSpringCloudService` を呼び出します。

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .UseAzureSpringCloudService()
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .AddConfigServer();
```

## <a name="enable-eureka-server-service-discovery"></a>Eureka Server サービスの検出を有効にする

アプリが Azure Spring Cloud で実行されるときに使用される構成ソースで、`spring.application.name` をプロジェクトのデプロイ先の Azure Spring Cloud アプリと同じ名前に設定します。

たとえば、`EurekaDataProvider` という名前の .NET プロジェクトを `planet-weather-provider` という名前の Azure Spring Cloud アプリにデプロイする場合、*appSettings.json* ファイルには次の JSON を含める必要があります。

```json
"spring": {
  "application": {
    "name": "planet-weather-provider"
  }
}
```

## <a name="use-service-discovery"></a>サービス検出を使用する

Eureka Server のサービス検出を使用してサービスを呼び出すには、`http://<app_name>` に HTTP 要求を送信します (この `app_name` はターゲット アプリの `spring.application.name` の値です)。 たとえば、次のコードでは `planet-weather-provider` サービスを呼び出しています。

```csharp
using (var client = new HttpClient(discoveryHandler, false))
{
    var responses = await Task.WhenAll(
        client.GetAsync("http://planet-weather-provider/weatherforecast/mercury"),
        client.GetAsync("http://planet-weather-provider/weatherforecast/saturn"));
    var weathers = await Task.WhenAll(from res in responses select res.Content.ReadAsStringAsync());
    return new[]
    {
        new KeyValuePair<string, string>("Mercury", weathers[0]),
        new KeyValuePair<string, string>("Saturn", weathers[1]),
    };
}
```

::: zone-end

::: zone pivot="programming-language-java"
このトピックでは、Azure Spring Cloud にデプロイできるように既存の Java Spring アプリケーションを準備する方法について説明します。 適切に構成すると、Azure Spring Cloud によって Java Spring Cloud アプリケーションの監視、スケーリング、更新を行う堅牢なサービスが実現します。

この例を実行する前に、[基本的なクイックスタート](./quickstart.md)を試してみることができます。

POM ファイルが構成されているときにアプリケーションを Azure Spring Cloud にデプロイする方法については、その他の例で説明しています。

* [最初のアプリを起動する](./quickstart.md)
* [マイクロサービスをビルドして実行する](./quickstart-sample-app-introduction.md)

この記事では、必要な依存関係とそれらを POM ファイルに追加する方法について説明します。

## <a name="java-runtime-version"></a>Java ランタイム バージョン

Azure Spring Cloud は、Java 8 と Java 11 の両方をサポートしています。 一般に、Azure PaaS は Java LTS バージョンのみをサポートし、Azure Spring Cloud は Java 17 LTS をサポートします。 ホスティング環境には最新バージョンの Azure 用 Azul Zulu OpenJDK が含まれます。 Azure 用 Azul Zulu OpenJDK の詳細については、[JDK のインストール](/azure/developer/java/fundamentals/java-jdk-install)に関するページを参照してください。

## <a name="spring-boot-and-spring-cloud-versions"></a>Spring Boot と Spring Cloud のバージョン

既存の Spring Boot アプリケーションを Azure Spring Cloud にデプロイできるよう準備するには、以降のセクションに示したように、アプリケーションの POM ファイルに Spring Boot と Spring Cloud の依存関係を追加します。

Azure Spring Cloud では、リリース後 1 か月以内に最新の Spring Boot または Spring Cloud リリースがサポートされます。 サポートされる Spring Boot のバージョンは [Spring Boot Releases ](https://github.com/spring-projects/spring-boot/wiki/Supported-Versions#releases)から、Spring Cloud のバージョンは [Spring Cloud Releases](https://github.com/spring-projects/spring-boot/wiki/Supported-Versions#releases) から入手できます。 

サポートされている Spring Boot と Spring Cloud の組み合わせを次の表に示します。

Spring Boot のバージョン | Spring Cloud のバージョン
---|---
2.3.x | Hoxton.SR8+
2.4.x, 2.5.x | 2020.0 aka Ilford +

> [!NOTE]
> - CVE レポート「[CVE-2021-22119: Denial-of-Service attack with spring-security-oauth2-client](https://tanzu.vmware.com/security/cve-2021-22119)」に対応するために、Spring Boot を 2.5.2 または 2.4.8 にアップグレードしてください。 Spring Security を使用している場合は、5.5.1、5.4.7、5.3.10、または 5.2.11 にアップグレードしてください。
> - アプリと Spring Cloud Service Registry 間の TLS 認証で Spring Boot 2.4.0 に問題があることが確認されました。2.4.1 以降を使用してください。 2\.4.0 を使用する場合の回避策については、[FAQ](./faq.md?pivots=programming-language-java#development) を参照してください。

### <a name="dependencies-for-spring-boot-version-23"></a>Spring Boot バージョン 2.3 の依存関係

Spring Boot バージョン 2.3 の場合は、アプリケーションの POM ファイルに次の依存関係を追加します。

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.4.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR8</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-2425"></a>Spring Boot バージョン 2.4/2.5 の依存関係

Spring Boot バージョン 2.4/2.5 の場合は、アプリケーションの POM ファイルに次の依存関係を追加します。

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.4.8</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>2020.0.2</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

> [!WARNING]
> 構成に `server.port` を指定しないでください。 Azure Spring Cloud では、この設定が固定ポート番号にオーバーライドされます。 また、この設定を尊重するようにして、コードでサーバーのポートを指定しないでください。

## <a name="other-recommended-dependencies-to-enable-azure-spring-cloud-features"></a>Azure Spring Cloud の機能を有効にするためのその他の推奨される依存関係

サービス レジストリから分散トレースまでの Azure Spring Cloud の組み込み機能を有効にするには、アプリケーションに次の依存関係も含める必要があります。 特定のアプリに対応する機能が不要な場合は、これらの依存関係の一部を削除できます。

### <a name="service-registry"></a>サービス レジストリ

マネージド Azure Service Registry サービスを使用するには、次に示すように、pom.xml ファイルに `spring-cloud-starter-netflix-eureka-client` 依存関係を含めます。

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

サービス レジストリ サーバーのエンドポイントは、アプリで環境変数として自動的に挿入されます。 その後、アプリケーションによって、アプリケーション自体がサービス レジストリ サーバーに登録され、他の依存マイクロサービスが検出されます。

#### <a name="enablediscoveryclient-annotation"></a>EnableDiscoveryClient 注釈

アプリケーションのソース コードに次の注釈を追加します。

```java
@EnableDiscoveryClient
```

たとえば、前出の例の piggymetrics アプリケーションを見てみましょう。

```java
package com.piggymetrics.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@SpringBootApplication
@EnableDiscoveryClient
@EnableZuulProxy

public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}
```

### <a name="distributed-configuration"></a>分散構成

分散構成を有効にするには、pom.xml ファイルの依存関係セクションに次の `spring-cloud-config-client` の依存関係を含めます。

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bootstrap</artifactId>
</dependency>
```

> [!WARNING]
> ブートストラップ構成で `spring.cloud.config.enabled=false` を指定しないでください。 そうしないと、アプリケーションと Config Server の連携が停止します。

### <a name="metrics"></a>メトリック

次に示すように、pom.xml ファイルの依存関係セクションに `spring-boot-starter-actuator` の依存関係を含めます。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 メトリックは JMX エンドポイントから定期的に取得されます。 メトリックを視覚化するには、Azure portal を使用します。

 > [!WARNING]
 > 構成プロパティに `spring.jmx.enabled=true` を指定してください。 そうしないと、Azure portal でメトリックを視覚化することはできません。

## <a name="see-also"></a>関連項目

* [アプリケーションのログとメトリックを分析する](./diagnostic-services.md)
* [構成サーバーを設定する](./how-to-config-server.md)
* [Spring クイックスタート ガイド](https://spring.io/quickstart)
* [Spring Boot のドキュメント](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>次のステップ

このトピックでは、Java Spring Cloud アプリケーションを Azure Spring Cloud へのデプロイ用に構成する方法について学習しました。 Config Server インスタンスを設定する方法については、[Config Server インスタンスの設定](./how-to-config-server.md)に関するページを参照してください。

その他のサンプルを GitHub で入手できます ([Azure Spring Cloud のサンプル](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples))。
::: zone-end
