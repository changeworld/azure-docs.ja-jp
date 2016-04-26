<properties
   pageTitle="Azure コンテナー サービス クラスターに接続する | Microsoft Azure"
   description="SSH トンネルを利用して Azure コンテナー サービス クラスターに接続します。"
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
   ms.date="04/12/2016"
   ms.author="rogardle"/>


# Azure コンテナー サービス クラスターに接続する

Azure コンテナー サービスによってデプロイされている DC/OS クラスターと Swarm クラスターは、REST エンドポイントを公開しています。ただし、これらのエンドポイントは外部には開放されていません。これらのエンドポイントを扱うためには、Secure Shell (SSH) トンネルを作成する必要があります。SSH トンネルが確立されると、クラスター エンドポイントに対してコマンドを実行し、独自のシステムでブラウザー経由でクラスター UI を表示できます。このドキュメントでは、Linux、OS X、Windows から SSH トンネルを作成する手順を紹介します。

>[AZURE.NOTE] SSH セッションは、クラスター管理システムとの間で作成することができます。ただし、これは推奨されません。管理システムで直接作業すると、構成を意図せず変更してしまうおそれがあります。

## Linux または OS X での SSH トンネルの作成

Linux または OS X で SSH トンネルを作成するにはまず、負荷分散されたマスターのパブリック DNS 名を特定する必要があります。そのためには、リソース グループを展開して各リソースを表示します。マスターのパブリック IP アドレスを見つけ、選択します。パブリック IP アドレスに関する情報を含むブレードが開きます。このブレードに DNS 名が表示されています。後で使用するためにこの名前を保存します。 <br />


![Public DNS name](media/pubdns.png)

次に、シェルを開き、次のコマンドを実行します。

**PORT** は、公開するエンドポイントのポートです。Swarm の場合は 2375 です。DC/OS の場合、ポート 80 を使用します。**USERNAME** は、クラスターのデプロイ時に指定したユーザー名です。**DNSPREFIX** は、クラスターのデプロイ時に指定した DNS 接頭辞です。**REGION** は、リソース グループが置かれているリージョンです。

> SSH 接続ポートは、標準の 22 ではなく 2200 です。

```bash
# ssh sample

ssh -L PORT:localhost:PORT -N [USERNAME]@[DNSPREFIX]man.[REGION].cloudapp.azure.com -p 2200
```

### DC/OS トンネル

DC/OS 関連のエンドポイントへのトンネルを開くには、次のようなコマンドを実行します。

```bash
# ssh sample

ssh -L 80:localhost:80 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

これで DC/OS 関連のエンドポイントに次の場所でアクセスできます。

- DC/OS: `http://localhost/`
- Marathon: `http://localhost/marathon`
- Mesos: `http://localhost/mesos`

同様に、各アプリケーションの REST API にはこのトンネルを経由して到達できます。

### Swarm トンネル

Swarm エンドポイントへのトンネルを開くには、次のようなコマンドを実行します。

```bash
# ssh sample

ssh -L 2375:localhost:2375 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

これで DOCKER\_HOST 環境変数を次のように設定し、通常どおり Docker コマンドライン インターフェイス (CLI) の使用を継続できます。

```bash
export DOCKER_HOST=:2375
```

## Windows の SSH トンネルの作成

Windows では、さまざまな方法で SSH トンネルを作成できます。このドキュメントでは、PuTTY を使った方法について説明します。

Windows システムに PuTTY をダウンロードし、実行します。

ホスト名を入力します。ホスト名は、クラスター管理者のユーザー名とクラスターの第 1 マスターのパブリック DNS 名で構成されます。**ホスト名**は `adminuser@PublicDNS` のようになります。**ポート**に 2200 を入力します。

![PuTTY configuration 1](media/putty1.png)

`SSH` と `Authentication` を選択し、認証のためにプライベート キー ファイルを追加します。

![PuTTY configuration 2](media/putty2.png)

`Tunnels` を選択し、次のように転送ポートを構成してください。
- **[Source Port]** (ソース ポート) には、使用する優先設定を指定します (DC/OS の場合は 80、Swarm の場合は 2375)。
- **[Destination]** (ターゲット) には、localhost:80 (DC/OS) または localhost:2375 (Swarm) を指定します。

次の例は DC/OS で構成されていますが、Docker Swarm でも同様です。

>[AZURE.NOTE] このトンネルの作成時にはポート 80 を使用しないでください。

![PuTTY configuration 3](media/putty3.png)

完了したら、接続構成を保存し、PuTTY セッションを接続します。接続すると、PuTTY のイベント ログでポート構成を確認できます。

![PuTTY event log](media/putty4.png)

DC/OS のトンネルを構成したら、関連するエンドポイントに次のアドレスでアクセスできます。

- DC/OS: `http://localhost/`
- Marathon: `http://localhost/marathon`
- Mesos: `http://localhost/mesos`

Docker Swarm のトンネルを構成したら、Docker CLI から Swarm クラスターにアクセスできます。最初に `DOCKER_HOST` という名前の Windows 環境変数を値 ` :2375` で構成する必要があります。

## 次のステップ

コンテナーをデプロイし、DC/OS または Swarm で管理します。

[REST API を使用したコンテナー管理](./container-service-mesos-marathon-rest.md) [Working with the Azure Container Service and Docker Swarm (Azure コンテナー サービスと Docker Swarm の使用)](./container-service-docker-swarm.md)

<!---HONumber=AcomDC_0420_2016-->