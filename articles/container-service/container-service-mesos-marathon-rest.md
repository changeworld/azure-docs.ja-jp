<properties
   pageTitle="REST API を使用した Azure コンテナー サービスのコンテナー管理| Microsoft Azure"
   description="Marathon REST API を使用して、Azure コンテナー サービス Mesos クラスターにコンテナーをデプロイします。"
   services="container-service"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker、コンテナー、マイクロ サービス、Mesos、Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="nepeters"/>

# REST API を使用したコンテナー管理

DC/OS はクラスター化されたワークロードをデプロイし、スケールするための環境を提供すると共に、基礎となるハードウェアを抽象化します。DC/OS に加え、コンピューティング ワークロードのスケジュールと実行を管理するフレームワークもあります。

一般的な各種ワークロードに対応したフレームワークがあるものの、このドキュメントでは、Marathon を使ってコンテナー デプロイを作成し、スケールする方法について説明します。これらの例を見ていく前に、Azure コンテナー サービスで構成された DC/OS クラスターが必要です。また、このクラスターへのリモート接続も必要です。これらの項目の詳細については、次の記事を参照してください。

- [Azure コンテナー サービス クラスターのデプロイ](container-service-deployment.md)
- [Azure コンテナー サービス クラスターに接続する](container-service-connect.md)

Azure コンテナー サービス クラスターに接続したら、http://localhost:local-port を通じて DC/OS と関連の REST API にアクセスできます。このドキュメントの例では、ポート 80 にトンネリングしていることを前提としています。たとえば、Marathon エンドポイントには、`http://localhost/marathon/v2/` で到達できます。さまざまな API の詳細については、[Marathon API](https://mesosphere.github.io/marathon/docs/rest-api.html) と [Chronos API](https://mesos.github.io/chronos/docs/api.html) に関する Mesosphere ドキュメントと [Mesos Scheduler API](http://mesos.apache.org/documentation/latest/scheduler-http-api/) に関する Apache ドキュメントを参照してください。

## DC/OS と Marathon から情報を収集する

DC/OS クラスターにコンテナーをデプロイする前に、DC/OS エージェントの名前や現在の状態など、DC/OS クラスターに関する情報を収集します。DC/OS REST API の `master/slaves` エンドポイントに情報を照会してください。問題がなければ、DC/OS エージェントの一覧と各エージェントのプロパティが表示されます。

```bash
curl http://localhost/mesos/master/slaves
```

次に、Marathon `/apps` エンドポイントを利用し、DC/OS クラスターに対する現在のアプリケーション デプロイを確認します。これが新しいクラスターであれば、アプリ用の空の配列が表示されます。

```
curl localhost/marathon/v2/apps

{"apps":[]}
```

## Docker 形式のコンテナーのデプロイ

Docker 形式のコンテナーは、意図するデプロイについて記述した JSON ファイルを利用して、Marathon 経由でデプロイします。次のサンプルでは Nginx コンテナーがデプロイされます。DC/OS エージェントのポート 80 をコンテナーのポート 80 に関連付けます。また、"acceptedResourceRoles" プロパティが "slave\_public" に設定されることにも注意してください。この設定により、パブリックに公開されたエージェント スケール セット内のエージェントにコンテナーがデプロイされます。

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
    "acceptedResourceRoles": [
    "slave_public"
  ],
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Docker 形式のコンテナーをデプロイするために、独自の JSON ファイルを作成するか、[Azure コンテナー サービスのデモ](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json)に用意されているサンプルを使用します。アクセス可能な場所にそれを格納します。次に、コンテナーをデプロイするために、次のコマンドを実行します。JSON ファイルの名前を指定します。

```
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

出力は次のようになります。

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

ここで、アプリケーションについて Marathon にクエリを実行すると、この新しいアプリケーションが出力に表示されます。

```
curl localhost/marathon/v2/apps
```

## コンテナーのスケール

Marathon API を利用して、アプリケーションのデプロイをスケールアウトまたはスケールインすることもできます。前の例では、アプリケーションの 1 つのインスタンスをデプロイしました。それをアプリケーションの 3 つのインスタンスにスケールアウトしてみましょう。これを行うには、次の JSON テキストを使って JSON ファイルを作成し、それをアクセス可能な場所に保存します。

```json
{ "instances": 3 }
```

次のコマンドを実行してアプリケーションをスケールアウトします。

>[AZURE.NOTE] URI は、http://localhost/marathon/v2/apps/ に、スケールするアプリケーションの ID を追加したものになります。ここで示す Nginx サンプルを使用する場合、URI は http://localhost/marathon/v2/apps/nginx になります。

```json
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

最後に、Marathon エンドポイントに対してアプリケーションを照会します。現在、3 つの Nginx コンテナーがあることがわかります。

```
curl localhost/marathon/v2/apps
```

## この演習での PowerShell の使用: PowerShell を使った Marathon REST API の操作

これと同じ操作は、Windows システム上で PowerShell コマンドを使って実行できます。

エージェント名やエージェントの状態など、DC/OS クラスターに関する情報を収集するには、次のコマンドを実行します。

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Docker 形式のコンテナーは、意図するデプロイについて記述した JSON ファイルを利用して、Marathon 経由でデプロイします。次のサンプルでは Nginx コンテナーがデプロイされます。DC/OS エージェントのポート 80 をコンテナーのポート 80 に関連付けます。

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

独自の JSON ファイルを作成するか、[Azure コンテナー サービスのデモ](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json)に用意されているサンプルを使用します。アクセス可能な場所にそれを格納します。次に、コンテナーをデプロイするために、次のコマンドを実行します。JSON ファイルの名前を指定します。

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Marathon API を利用して、アプリケーションのデプロイをスケールアウトまたはスケールインすることもできます。前の例では、アプリケーションの 1 つのインスタンスをデプロイしました。それをアプリケーションの 3 つのインスタンスにスケールアウトしてみましょう。これを行うには、次の JSON テキストを使って JSON ファイルを作成し、それをアクセス可能な場所に保存します。

```json
{ "instances": 3 }
```

次のコマンドを実行してアプリケーションをスケールアウトします。

> [AZURE.NOTE] URI は、http://localhost/marathon/v2/apps/ に、スケールするアプリケーションの ID を追加したものになります。ここで示す Nginx サンプルを使用する場合、URI は http://localhost/marathon/v2/apps/nginx になります。

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## 次のステップ

- [Mesos HTTP エンドポイントの詳細](http://mesos.apache.org/documentation/latest/endpoints/)。
- [Marathon REST API の詳細](https://mesosphere.github.io/marathon/docs/rest-api.html)。

<!---HONumber=AcomDC_0914_2016-->