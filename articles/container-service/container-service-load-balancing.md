<properties
   pageTitle="Azure コンテナー サービス クラスターの負荷分散 | Microsoft Azure"
   description="Azure コンテナー サービス クラスターを負荷分散します。"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="コンテナー, マクロサービス, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/18/2016"
   ms.author="rogardle"/>

# Azure コンテナー サービス クラスターの負荷分散

この記事では、Azure Load Balancer の背後でサービスを提供するためにスケールアップすることができる Web フロントエンドを設定します。

## 前提条件

orchestrator の種類が DC/OS の [Azure コンテナー サービスのインスタンスをデプロイ](container-service-deployment.md)し、[クライアントがクラスターに接続](container-service-connect.md)できるようにします。また、次の手順を実行します。
[AZURE.INCLUDE [DC/OS コマンド ライン インターフェイス (CLI) のインストール](../../includes/container-service-install-dcos-cli-include.md)]


## 負荷分散

コンテナー サービス クラスターには、2 つの負荷分散レイヤーがあります。パブリック エントリ ポイント (エンド ユーザーがヒットするポイント) 用の Azure Load Balancer と、要求を処理するコンテナー インスタンスに受信要求をルーティングする、基になる Marathon Load Balancer (marathon-lb) です。サービスを提供するコンテナーはスケールされるため、marathon-lb が動的に調整されます。

## Marathon Load Balancer

Marathon Load Balancer は、デプロイされたコンテナーに基づいて動的に自身を再構成します。また、コンテナーまたはエージェントの喪失に対する回復力もあります。この問題が起きた場合、Mesos は単に他の場所でコンテナーを再起動し、Marathon Load Balancer を再構成します。

Marathon Load Balancer をインストールするには、クライアント コンピューターから次のコマンドを実行します。

```bash
dcos package install marathon-lb
```

これで marathon-lb パッケージがインストールされたので、次の構成を使用して簡単な Web サーバーをデプロイできます。


```json
{
  "id": "web",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "tutum/hello-world",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  }
}

```

この重要な部分は次のとおりです。
  * HAProxy\_0\_VHOST の値に、エージェントのロード バランサーの FQDN を設定します。これは、`<acsName>agents.<region>.cloudapp.azure.com` という形式です。たとえば、`West US` リージョンに `myacs` という名前のコンテナー サービス クラスターを作成した場合、FQDN は `myacsagents.westus.cloudapp.azure.com` になります。また、[Azure ポータル](https://portal.azure.com)でコンテナー サービス用に作成したリソース グループのリソースを調べる場合は、名前に "agent" が含まれるロード バランサーを検索すると見つかります。
  * servicePort を 10,000 以上のポートに設定します。この設定により、このコンテナーで実行されているサービスを特定できます。つまり、marathon-lb は、これを使用して、分散先となるサービスを特定します。
  * HAPROXY\_GROUP ラベルを "external" に設定します。
  * hostPort を 0 に設定します。この設定は、使用可能なポートを Marathon が任意に割り当てることを意味します。

この JSON を `hello-web.json` という名前のファイルにコピーし、これを使用してコンテナーをデプロイします。

```bash
dcos marathon app add hello-web.json
```

## Azure Load Balancer

既定では、Azure Load Balancer はポート 80、8080、443 を公開します。上の例のように、これらの 3 つのポートのいずれかを使用している場合は、何もする必要がありません。つまり、エージェント ロード バランサーの FQDN をヒットできるようになり、更新するたびにラウンド ロビン方式で 3 つの Web サーバーのいずれかがヒットします。ただし、別のポートを使用する場合は、使用したポート用のラウンド ロビン ルールとプローブをロード バランサーに追加する必要があります。この操作は、[Azure CLI](../xplat-cli-azure-resource-manager.md) で `azure lb rule create` コマンドと `azure lb probe create` コマンドを使用して行うことができます。


## その他のシナリオ

公開するサービスに応じて使用するドメインが異なるシナリオもあります。次に例を示します。

mydomain1.com -> Azure LB:80 -> marathon-lb:10001 -> mycontainer1:33292 mydomain2.com -> Azure LB:80 -> marathon-lb:10002 -> mycontainer2:22321

これを実現するには、[仮想ホスト](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/)に関するページを参照してください。ドメインを特定の marathon-lb パスに関連付ける方法を説明しています。

また、さまざまなポートを公開し、marathon-lb の背後で適切なサービスにそれらのポートを再マップすることもできます。次に例を示します。

Azure lb:80 -> marathon-lb:10001 -> mycontainer:233423 Azure lb:8080 -> marathon-lb:1002 -> mycontainer2:33432


## 次のステップ

Marathon Load Balancer の詳細については、[こちらの負荷分散に関するブログ記事](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/)を参照してください。

<!---HONumber=AcomDC_0622_2016-->