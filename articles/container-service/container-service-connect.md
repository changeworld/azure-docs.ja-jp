---
title: "Azure Container Service クラスターに接続する | Microsoft Docs"
description: "SSH トンネルを利用して Azure コンテナー サービス クラスターに接続します。"
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, コンテナー, マクロサービス, DC/OS, Azure"
ms.assetid: ff8d9e32-20d2-4658-829f-590dec89603d
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: bcc2d3468c8a560105aa2c2feb0d969ec3cccdcb
ms.openlocfilehash: 5296586b9266f432042f847f4dff9e6ff62ebc8b


---
# <a name="connect-to-an-azure-container-service-cluster"></a>Azure コンテナー サービス クラスターに接続する
Azure Container Service によってデプロイされている DC/OS、Kubernetes、Docker Swarm のクラスターはすべて、REST エンドポイントを公開しています。  Kubernetes では、このエンドポイントはセキュリティ保護されてインターネット上に公開されており、インターネットに接続されているコンピューターから直接アクセスすることができます。 DC/OS と Docker Swarm では、REST エンドポイントに安全に接続するために SSH トンネルを作成する必要があります。 ここでは、これらの接続についてそれぞれ説明します。

> [!NOTE]
> Azure Container Service での Kubernetes のサポートは、現在はプレビューの段階です。
>

## <a name="connecting-to-a-kubernetes-cluster"></a>Kubernetes クラスターへの接続
Kubernetes クラスターに接続するには、`kubectl` コマンド ライン ツールをインストールしておく必要があります。  このツールをインストールする最も簡単な方法は、Azure 2.0 `az` コマンド ライン ツールを使用することです。

```console
az acs kubernetes install cli [--install-location=/some/directory]
```

または、[リリース ページ](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG.md#downloads-for-v146)から直接クライアントをダウンロードできます。

`kubectl` をインストールしたら、クラスターの資格情報をコンピューターにコピーする必要があります。  これを行う最も簡単な方法は、ここでも `az` コマンド ライン ツールを使用することです。

```console
az acs kubernetes get-credentials --dns-prefix=<some-prefix> --location=<some-location>
```

これを実行すると、`kubectl` が求める配置場所 `$HOME/.kube/config` にクラスターの資格情報がダウンロードされます。

または、`scp` を使用して、マスター VM の `$HOME/.kube/config` からローカル コンピューターにファイルを安全にコピーできます。

```console
mkdir $HOME/.kube/config
scp azureuser@<master-dns-name>:.kube/config $HOME/.kube/config
```

Windows を使用している場合、Bash on Ubuntu on Windows または Putty "pscp" ツールを使用する必要があります。

`kubectl` を構成したら、クラスターのノードをリッスンすることでこれをテストできます。

```console
kubectl get nodes
```

最後に Kubernetes ダッシュボードを表示できます。 まず、次のコードを実行します。

```console
kubectl proxy
```

Kubernetes UI は現在、http://localhost:8001/ui で入手できます。

詳細な手順については、[Kubernetes のクイック スタート](http://kubernetes.io/docs/user-guide/quick-start/)に関するページを参照してください。

## <a name="connecting-to-a-dcos-or-swarm-cluster"></a>DC/OS または Swarm クラスターへの接続

Azure コンテナー サービスによってデプロイされている DC/OS クラスターと Docker Swarm クラスターは、REST エンドポイントを公開しています。 ただし、これらのエンドポイントは外部には開放されていません。 これらのエンドポイントを扱うためには、Secure Shell (SSH) トンネルを作成する必要があります。 SSH トンネルが確立されると、クラスター エンドポイントに対してコマンドを実行し、独自のシステムでブラウザー経由でクラスター UI を表示できます。 このドキュメントでは、Linux、OS X、Windows から SSH トンネルを作成する手順を紹介します。

> [!NOTE]
> SSH セッションは、クラスター管理システムとの間で作成することができます。 ただし、これは推奨されません。 管理システムで直接作業すると、構成を意図せず変更してしまうおそれがあります。   
> 
> 

## <a name="create-an-ssh-tunnel-on-linux-or-os-x"></a>Linux または OS X での SSH トンネルの作成
Linux または OS X で SSH トンネルを作成するにはまず、負荷分散されたマスターのパブリック DNS 名を特定する必要があります。 そのためには、リソース グループを展開して各リソースを表示します。 マスターのパブリック IP アドレスを見つけ、選択します。 パブリック IP アドレスに関する情報を含むブレードが開きます。このブレードに DNS 名が表示されています。 後で使用するためにこの名前を保存します。 <br />

![Public DNS name](media/pubdns.png)

次に、シェルを開き、次のコマンドを実行します。

**PORT** は、公開するエンドポイントのポートです。 Swarm の場合は 2375 です。 DC/OS の場合、ポート 80 を使用します。  
**USERNAME** は、クラスターのデプロイ時に指定したユーザー名です。  
**DNSPREFIX** は、クラスターのデプロイ時に指定した DNS 接頭辞です。  
**REGION** は、リソース グループが置かれているリージョンです。  
**PATH_TO_PRIVATE_KEY** [省略可能] は、コンテナー サービス クラスターの作成時に指定した公開キーに対応する秘密キーへのパスです。 このオプションは、-i フラグと共に使用します。

```bash
ssh -L PORT:localhost:PORT -f -N [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com -p 2200
```
> SSH 接続ポートは、標準の 22 ではなく 2200 です。
> 
> 

## <a name="dcos-tunnel"></a>DC/OS トンネル
DC/OS 関連のエンドポイントへのトンネルを開くには、次のようなコマンドを実行します。

```bash
sudo ssh -L 80:localhost:80 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

これで DC/OS 関連のエンドポイントに次の場所でアクセスできます。

* DC/OS: `http://localhost/`
* Marathon: `http://localhost/marathon`
* Mesos: `http://localhost/mesos`

同様に、各アプリケーションの REST API にはこのトンネルを経由して到達できます。

## <a name="swarm-tunnel"></a>Swarm トンネル
Swarm エンドポイントへのトンネルを開くには、次のようなコマンドを実行します。

```bash
ssh -L 2375:localhost:2375 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

これで、DOCKER_HOST 環境変数を次のように設定できます。 通常どおり、Docker コマンド ライン インターフェイス (CLI) を使用し続けることができます。

```bash
export DOCKER_HOST=:2375
```

## <a name="create-an-ssh-tunnel-on-windows"></a>Windows の SSH トンネルの作成
Windows では、さまざまな方法で SSH トンネルを作成できます。 このドキュメントでは、PuTTY を使った方法について説明します。

Windows システムに PuTTY をダウンロードし、実行します。

ホスト名を入力します。ホスト名は、クラスター管理者のユーザー名とクラスターの第 1 マスターのパブリック DNS 名で構成されます。 **ホスト名**は `adminuser@PublicDNS` のようになります。 **[ポート]** に「2200」と入力します。

![PuTTY configuration 1](media/putty1.png)

**[SSH]** と **[認証]** を選択します。 認証のためにプライベート キー ファイルを追加します。

![PuTTY configuration 2](media/putty2.png)

**[Tunnels (トンネル)]** を選択し、次のように転送ポートを構成します。

* **[接続元ポート]** には、DC/OS の場合は 80、Swarm の場合は 2375 を指定します。
* **[ターゲット]** には、DC/OS の場合は localhost:80、Swarm の場合は localhost:2375 を指定します。

次の例は DC/OS で構成されていますが、Docker Swarm でも同様です。

> [!NOTE]
> このトンネルの作成時にはポート 80 を使用しないでください。
> 
> 

![PuTTY configuration 3](media/putty3.png)

完了したら、接続構成を保存し、PuTTY セッションを接続します。 接続すると、PuTTY のイベント ログでポート構成を確認できます。

![PuTTY event log](media/putty4.png)

DC/OS のトンネルを構成したら、関連するエンドポイントに次のアドレスでアクセスできます。

* DC/OS: `http://localhost/`
* Marathon: `http://localhost/marathon`
* Mesos: `http://localhost/mesos`

Docker Swarm のトンネルを構成したら、Docker CLI から Swarm クラスターにアクセスできます。 最初に `DOCKER_HOST` という名前の Windows 環境変数を値 ` :2375` で構成する必要があります。

## <a name="next-steps"></a>次のステップ
コンテナーをデプロイし、DC/OS または Swarm で管理します。

* [Azure コンテナー サービスと DC/OS の使用](container-service-mesos-marathon-rest.md)
* [Azure コンテナー サービスと Docker Swarm の使用](container-service-docker-swarm.md)




<!--HONumber=Dec16_HO3-->


