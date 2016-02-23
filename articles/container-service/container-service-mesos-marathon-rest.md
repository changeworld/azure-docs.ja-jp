<properties
   pageTitle="REST API による ACS コンテナー管理 | Microsoft Azure"
   description="Marathon REST API を使用して Azure コンテナー サービスのクラスター サービスにコンテナーをデプロイします。"
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
   ms.date="02/16/2016"
   ms.author="nepeters"/>
   
# REST API によるコンテナー管理

Mesos はクラスター化されたワークロードをデプロイし、拡張縮小するための環境を提供し、基礎となるハードウェアを抽象化します。Mesos に加え、コンピューティング ワークロードのスケジュールと実行をフレームワークで管理します。さまざまな人気のワークロードのフレームワークを利用できますが、このドキュメントでは、Marathon でコンテナー デプロイを作成し、拡大縮小する方法について説明します。以下の例を進める前に、ACS で Mesos クラスターを構成し、そのクラスターにリモート接続する必要があります。これらの項目の詳細については、次の記事を参照してください。

- [Azure コンテナー サービス クラスターをデプロイする](./container-service-deployment.md) 
- [ACS クラスターに接続する](./container-service-connect.md)


SSH トンネルを設定したら、Mesos 関連 API に `http://localhost:LOCAL_PORT` 経由でアクセスできます。下の例では、ポート 80 でトンネリングするものと想定しています。たとえば、`http://localhost/marathon/v2` は Marathon API のエンドポイントになります。利用できるさまざまな API の詳細については、[Marathon API](https://mesosphere.github.io/marathon/docs/rest-api.html) と [Chronos API](https://mesos.github.io/chronos/docs/api.html) の Mesosphere ドキュメントと [Mesos Scheduler API](http://mesos.apache.org/documentation/latest/scheduler-http-api/) の Apache ドキュメントを参照してください。

## Mesos と Marathon から情報を収集する

Mesos クラスターにコンテナーをデプロイする前に、Mesos エージェントの名前や現在の状態など、Mesos クラスターに関する情報を収集します。これを行うには、Mesos マスターで `master/slaves` エンドポイントにクエリを実行します。問題がなければ、Mesos エージェントの一覧と各エージェントのプロパティが表示されます。

```bash
curl http://localhost/master/slaves
```

次に、Marathon `/apps` エンドポイントを利用し、Mesos クラスターへの現在の Marathon デプロイを確認します。これが新しいクラスターであれば、アプリ用の空の配列が表示されます。

```
curl localhost/marathon/v2/apps

{"apps":[]}
```

## Docker コンテナーをデプロイする

Docker コンテナーは、意図するデプロイを表す JSON ファイルを利用し、Marathon 経由でデプロイされます。次のサンプルでは nginx コンテナーがデプロイされます。Mesos エージェントのポート 80 をコンテナーのポート 80 に関連付けます。

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

Docker コンテナーをデプロイするために、自分の JSON ファイルを作成するか、ここのサンプル ([Azure ACS デモ](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json)) を使用し、アクセス可能な場所に保存します。次に、JSON ファイルの名前を指定して次のコマンドを実行し、コンテナーをデプロイします。

```
curl -X POST http://localhost/marathon/v2/groups -d @marathon.json -H "Content-type: application/json"
```

次のように出力されます。

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

次に、実行中のアプリケーションについて Marathon にクエリを実行する場合、この新しいアプリケーションが出力に表示されます。

```
curl localhost/marathon/v2/apps
```

## Docker コンテナーを拡大縮小する

Marathon API を利用し、アプリケーションのデプロイを拡大縮小することもできます。前の例では、アプリケーションの 1 つのインスタンスがデプロイされました。これを 3 つのインスタンスに拡大してみましょう。これを行うには、次の JSON テキストで JSON ファイルを作成し、それをアクセス可能な場所に保存します。

```json
{ "instances": 3 }
```

次のコマンドを実行し、アプリケーションを拡大します。

> 注 – URI は http://localhost/marathon/v2/apps/ に拡張するアプリケーションの ID を追加したものになります。ここの nginx サンプルの場合、URI は http://localhost/v2/nginx になります。

```json
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

最後に、アプリケーションのインスタンスについて Marathon エンドポイントにクエリを実行します。インスタンスが 3 つになったことがわかります。

```
curl localhost/marathon/v2/apps
```

## Marathon REST API PowerShell

これらの同じ操作を Windows システムで PowerShell を使用して実行できます。この簡易演習で最後の演習と同様のタスクが完了します。今回は PowerShell コマンドを利用します。

エージェント名やエージェント ステータスなど、Mesos クラスターに関する情報を収集するには、次のコマンドを実行します。

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Docker コンテナーは、意図するデプロイを表す JSON ファイルを利用し、Marathon 経由でデプロイされます。次のサンプルでは nginx コンテナーがデプロイされます。Mesos エージェントのポート 80 をコンテナーのポート 80 に関連付けます。

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

自分の JSON ファイルを作成するか、ここのサンプル ([Azure ACS デモ](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json)) を使用し、アクセス可能な場所に保存します。次に、JSON ファイルの名前を指定して次のコマンドを実行し、コンテナーをデプロイします。

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Marathon API を利用し、アプリケーションのデプロイを拡大縮小することもできます。前の例では、アプリケーションの 1 つのインスタンスがデプロイされました。これを 3 つのインスタンスに拡大してみましょう。これを行うには、次の JSON テキストで JSON ファイルを作成し、それをアクセス可能な場所に保存します。

```json
{ "instances": 3 }
```

次のコマンドを実行し、アプリケーションを拡大します。

> 注 – URI は http://loclahost/marathon/v2/apps/ に拡張するアプリケーションの ID を追加したものになります。ここの nginx サンプルの場合、URI は http://localhost/v2/nginx になります。

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

<!---HONumber=AcomDC_0218_2016-->