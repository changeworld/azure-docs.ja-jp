---
title: Azure Spring Cloud アプリのログをリアルタイムでストリーム配信する
description: ログ ストリーミングを使用してアプリケーション ログを即座に表示する方法
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: cc34e87823e11b2c80d669edb0afe50703e38527
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015509"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>Azure Spring Cloud アプリのログをリアルタイムでストリーム配信する

**この記事の適用対象:** ✔️ Java ✔️ C#

Azure Spring Cloud では Azure CLI のログ ストリーミングを使ってトラブルシューティングのためにリアルタイムのアプリケーション コンソール ログを取得できます。 さらに、[診断設定でログとメトリックを分析する](./diagnostic-services.md)こともできます。

## <a name="prerequisites"></a>前提条件

* Spring Cloud (最小バージョン 0.2.0) 用の [Azure CLI 拡張機能](/cli/azure/install-azure-cli)のインストール。
* [Spring Cloud アプリ](./quickstart.md)などのアプリケーションを実行する **Azure Spring Cloud** のインスタンス。

> [!NOTE]
>  ASC CLI 拡張機能はバージョン 0.2.0 から 0.2.1 に更新されました。 この変更によってログ ストリーミングのコマンドの構文が影響を受けます。`az spring-cloud app logs` が `az spring-cloud app log tail` に取って代わります。 コマンド: `az spring-cloud app log tail` は今後のリリースで非推奨になります。 バージョン 0.2.0 を使用している場合、0.2.1 にアップグレードできます。 まず、コマンド `az extension remove -n spring-cloud` を使用して古いバージョンを削除します。  次に、コマンド `az extension add -n spring-cloud` で 0.2.1 をインストールします。

## <a name="use-cli-to-tail-logs"></a>ログ末尾に CLI を使用する

リソース グループとサービス インスタンス名を繰り返し指定するのを避けるには、既定のリソース グループ名とクラスター名を設定します。

```azurecli
az config set defaults.group=<service group name>
az config set defaults.spring-cloud=<service instance name>
```

次の例では、コマンドでリソース グループとサービス名を省略しています。

### <a name="tail-log-for-app-with-single-instance"></a>1 つのインスタンスを持つアプリのログ末尾

auth-service という名前のアプリにインスタンスが 1 つしかない場合は、次のコマンドを使用してアプリ インスタンスのログを表示できます。

```azurecli
az spring-cloud app logs -n auth-service
```

これにより、次のログが返されます。

```output
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>複数のインスタンスがあるアプリのログ末尾

`auth-service` という名前のアプリに対して複数のインスタンスが存在する場合は、`-i/--instance` オプションを使用してインスタンスのログを表示できます。

まず、次のコマンドを使用して、アプリ インスタンス名を取得できます。

```azurecli
az spring-cloud app show -n auth-service --query properties.activeDeployment.properties.instances -o table
```

結果:

```output
Name                                         Status    DiscoveryStatus
-------------------------------------------  --------  -----------------
auth-service-default-12-75cc4577fc-pw7hb  Running   UP
auth-service-default-12-75cc4577fc-8nt4m  Running   UP
auth-service-default-12-75cc4577fc-n25mh  Running   UP
```

次に、オプション `-i/--instance` オプションを使用して、アプリ インスタンスのログをストリーミングできます。

```azurecli
az spring-cloud app logs -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```

アプリ インスタンスの詳細は、Azure portal から取得することもできます。  Azure Spring Cloud サービスの左側のナビゲーション ウィンドウで **[アプリ]** を選択した後、 **[App Instances]\(アプリ インスタンス\)** を選択します。

### <a name="continuously-stream-new-logs"></a>新しいログを継続的にストリーミングする

既定では、`az spring-cloud app logs` は、アプリ コンソールにストリーミングされた既存のログのみを出力し、終了します。 新しいログをストリーミングする場合は、次のように -f (--follow) を追加します。

```azurecli
az spring-cloud app logs -n auth-service -f
```

サポートされているすべてのログ オプションを確認するには:

```azurecli
az spring-cloud app logs -h
```

### <a name="format-json-structured-logs"></a>JSON の構造化されたログの書式設定

> [!NOTE]
> spring-cloud 拡張機能バージョン 2.4.0 以降が必要です。

アプリに対して[構造化されたアプリケーション ログ](./structured-app-log.md)が有効になっている場合、そのログは JSON 形式で出力されます。 これにより読み取りが困難になります。 `--format-json` 引数を使用すると、JSON ログを人が判読できる形式に書式設定できます。

```shell
# Raw JSON log
$ az spring-cloud app logs -n auth-service
{"timestamp":"2021-05-26T03:35:27.533Z","logger":"com.netflix.discovery.DiscoveryClient","level":"INFO","thread":"main","mdc":{},"message":"Disable delta property : false"}
{"timestamp":"2021-05-26T03:35:27.533Z","logger":"com.netflix.discovery.DiscoveryClient","level":"INFO","thread":"main","mdc":{},"message":"Single vip registry refresh property : null"}

# Formatted JSON log
$ az spring-cloud app logs -n auth-service --format-json
2021-05-26T03:35:27.533Z  INFO [           main] com.netflix.discovery.DiscoveryClient   : Disable delta property : false
2021-05-26T03:35:27.533Z  INFO [           main] com.netflix.discovery.DiscoveryClient   : Single vip registry refresh property : null
```

`--format-json` 引数は、キーワード引数である[文字列の書式設定構文](https://docs.python.org/3/library/string.html#format-string-syntax)を使用して、省略可能なカスタマイズされた形式を取ることもできます。

```shell
# Custom format
$ az spring-cloud app logs -n auth-service --format-json="{message}{n}"
Disable delta property : false
Single vip registry refresh property : null
```

> 使用されている既定の形式は次のとおりです。
>
> ```format
> {timestamp} {level:>5} [{thread:>15.15}] {logger{39}:<40.40}: {message}{n}{stackTrace}
> ```

## <a name="next-steps"></a>次のステップ

* [クイック スタート: ログ、メトリック、トレースを使用した Azure Spring Cloud アプリの監視](./quickstart-logs-metrics-tracing.md)
* [診断設定でログとメトリックを分析する](./diagnostic-services.md)
