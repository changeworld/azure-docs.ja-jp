<properties
   pageTitle="Azure コンテナー サービス クラスターに接続する | Microsoft Azure"
   description="SSH トンネルを利用して Azure コンテナー サービス クラスターに接続する"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
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
   ms.author="rogardle"/>
   

# Azure コンテナー サービス クラスターに接続する

Azure コンテナー サービスでデプロイされた Mesos クラスターと Swarm クラスターは REST エンドポイントを公開しますが、これらのエンドポイントは外部に公開されません。これらのエンドポイントを管理するために、SSH トンネルを作成する必要があります。SSH トンネルが確立されると、クラスター エンドポイントに対してコマンドを実行し、独自のシステムでブラウザー経由でクラスター UI を表示できます。このドキュメントでは、Linux、OSX、Windows から SSH トンネルを作成する方法を段階的に説明します。

> クラスター管理システムで SSH セッションを作成できますが、推奨されません。管理システムで直接作業すると、構成をうっかり変更してしまうことがあります。

## Linux / OSX の SSH トンネル

最初に、負荷が分散されたマスターのパブリック DNS 名を見つけます。これを行うには、各リソースが表示されるようにリソース グループを展開します。マスターのパブリック IP アドレスを見つけ、選択します。パブリック IP アドレスに関する情報を含むブレードが開きます。これに DNS 名が含まれています。後で使用するためにこの名前を保存します。 <br />

![Putty 接続](media/pubdns.png)

次に、シェルを開き、次のコマンドを実行します。

**PORT** は、公開するエンドポイントのポートです。Swarm の場合はポート 2375 で、Mesos の場合はポート 80 です。**USERNAME** は、クラスターのデプロイ時に指定したユーザー名です。**DNSPREFIX** は、クラスターのデプロイ時に指定した DNS 接頭辞です。**REGION** は、リソース グループが置かれているリージョンです。

```
ssh -L PORT:localhost:PORT -N [USERNAME]@[DNSPREFIX]man.[REGION].cloudapp.azure.com -p 2200
```
### Mesos トンネル

Mesos 関連のエンドポイントへのトンネルを開くには、次のようなコマンドを実行します。

```
ssh -L 80:localhost:80 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

これで Mesos 関連のエンドポイントに次の場所でアクセスできます。

- Mesos - `http://localhost/mesos`
- Marathon - `http://localhost/marathon`
- Chronos -`http://localhost/chronos`. 

同様に、各アプリケーションの REST API にはトンネル (Marathon - `http://localhost/marathon/v2`) を経由して到達できます。利用できるさまざまな API の詳細については、[Marathon API](https://mesosphere.github.io/marathon/docs/rest-api.html) と [Chronos API](https://mesos.github.io/chronos/docs/api.html) の Mesosphere ドキュメントと [Mesos Scheduler API](http://mesos.apache.org/documentation/latest/scheduler-http-api/) の Apache ドキュメントを参照してください。

### Swarm トンネル

Swarm エンドポイントへのトンネルを開くには、次のようなコマンドを実行します。

```
ssh -L 2375:localhost:2375 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

これで DOCKER\_HOST 環境変数を次のように設定し、通常どおり Docker CLI の使用を継続できます。

```
export DOCKER_HOST=:2375
```

## Windows の SSH トンネル

Windows ではさまざまな方法で SSH トンネルを作成できます。このドキュメントでは、Putty の使用方法について説明します。

Windows システムに Putty をダウンロードし、実行します。

ホスト名を入力します。ホスト名は、クラスター管理者のユーザー名とクラスターの第 1 マスターのパブリック DNS 名で構成されます。ホスト名は `adminuser@PublicDNS` のようになります。ポートに 2200 を入力します。

![Putty 接続](media/putty1.png)

`SSH` と `Authentication` を選択し、認証のためにプライベート キー ファイルを追加します。

![Putty 接続](media/putty2.png)

`Tunnels` および `configure` の次の転送ポートを選択します:
- **ソース ポート:** 使用する優先設定 (Mesos の場合は 80、Swarm の場合は 2375)
- **ターゲット:** localhost:80 (Mesos) または localhost:2375 (Swarm)

次の例は Mesos で構成されていますが、Docker Swarm でも同様です。

> このトンネルの作成時にはポート 80 を使用しないでください。

![Putty 接続](media/putty3.png)

完了したら、接続構成を保存し、Putty セッションを接続します。接続すると、ポート構成を Putty イベント ログで確認できます。

![Putty 接続](media/putty4.png)

Mesos のトンネルを構成したら、関連エンドポイントに次の場所でアクセスできます。

- Mesos - `http://localhost/mesos`
- Marathon - `http://localhost/marathon`
- Chronos -`http://localhost/chronos` 

Docker Swarm のトンネルが構成されると、Swarm クラスターに Docker CLI 経由でアクセスできます。最初に「`DOCKER_HOST`」という名前の Windows 環境変数を値 ` :2375` で構成する必要があります。

## 次のステップ
 
コンテナーをデプロイし、Mesos または Swarm で管理します。
 
- [ACS と Mesos を使用する](./container-service-mesos-marathon-rest.md)

<!---HONumber=AcomDC_0309_2016-->