---
title: "Azure DC/OS クラスターでの ACR の使用 | Microsoft Docs"
description: "Azure Container Service の DC/OS クラスターで Azure Container Registry を使用する"
services: container-service
documentationcenter: 
author: julienstroheker
manager: dcaro
editor: 
tags: acs, azure-container-service, acr, azure-container-registry
keywords: "Docker, コンテナー, マイクロサービス, Mesos, Azure, FileShare, cifs"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: juliens
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: a8a3716f8d03b596285026426c7514b7b642cb25
ms.lasthandoff: 03/31/2017


---
# <a name="use-acr-with-a-dcos-cluster-to-deploy-your-application"></a>DC/OS クラスターで ACR を使用してアプリケーションをデプロイする

この記事では、DC/OS クラスターで ACR (Azure Container Registry) などのプライベート コンテナーを使用する方法について説明します。 ACR を使用すると、イメージをプライベートに保存し、バージョンや更新プログラムなどについて管理することができます。

このチュートリアルを進める前に、次を行う必要があります。 
* Azure Container Service 内での DC/OS クラスターの構成。 [Azure Container Service クラスターのデプロイ](container-service-deployment.md)に関するページをご覧ください。
* Azure Container Service のデプロイ。 「[Azure Portal を使用したプライベート Docker コンテナー レジストリの作成](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal)」または「[Azure CLI 2.0 を使用したプライベート Docker コンテナー レジストリの作成](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli)」を参照してください。
* DC/OS クラスター内でのファイル共有の構成。 「[ファイル共有を作成し、DC/OS クラスターにマウントする](container-service-dcos-fileshare.md)」を参照してください。
* [Web UI](container-service-mesos-marathon-ui.md) または [REST API](container-service-mesos-marathon-rest.md) を使用して DC/OS クラスターに Docker イメージをデプロイする方法の確認

## <a name="manage-the-authentication-inside-your-cluster"></a>クラスター内の認証を管理する

従来の方法でプライベート レジストリからイメージをプッシュおよびプルするには、まずレジストリに対して認証を行います。 これを行うには、プライベート レジストリを使用する必要のある Docker クライアント プロセスに対して、`docker login` コマンドを実行する必要があります。
ここでは DC/OS を使用していますが、実稼働環境の場合は任意のノードからイメージをプルできるようにする必要があります。 ご想像のとおり、クラスターのサイズによっては各マシンに対しこのコマンド ラインを実行するのは非常に大がかりな作業となるため、こうした作業を行わずに済むように認証プロセスの自動化が必要になります。 

[DC/OS 内でのファイル共有の設定](container-service-dcos-fileshare.md)を完了しているものとして、次の手順でこのファイル共有を使用します。

### <a name="from-any-client-machine-recommended-method"></a>任意のクライアントマシンから管理する (推奨)

以下のコマンドは、すべての環境 (Windows/Mac/Linux) で実行可能です。

1. 次の前提条件を満たしていることを確認します。
  * TAR ツール
    * [Windows](http://gnuwin32.sourceforge.net/packages/gtar.htm)
  * Docker 
    * [Windows](https://www.docker.com/docker-windows)
    * [Mac](https://www.docker.com/docker-mac)
    * [Ubuntu](https://www.docker.com/docker-ubuntu)
    * [その他](https://www.docker.com/get-docker)
  * [こちらの方法](container-service-dcos-fileshare.md)に従ってクラスター内にファイル共有をマウントする

2. お使いのターミナルで `sudo docker login --username=<USERNAME> --password=<PASSWORD> <ACR-REGISTRY-NAME>.azurecr.io` コマンドを実行して、ACR サービスへの認証を開始します。 `USERNAME`、`PASSWORD`、`ACR-REGISTRY-NAME` の各変数は Azure Portal で表示される値に置き換えてください。

3. `docker login` 操作を実行すると、これらの値がローカルのホーム フォルダーに格納されます (Mac と Linux では `cd ~/.docker`、Windows では `cd %HOMEPATH%`)。 `tar czf` コマンドを使用してこのフォルダーの中身を圧縮します。

4. 最後に、先ほど作成した tar ファイルを、[前提条件として作成済みの](container-service-dcos-fileshare.md)ファイル共有内にコピーします。 これは、Azure-CLI の `az storage file upload -s <shareName> --account-name <storageAccountName> --account-key <storageAccountKey> -source <pathToTheTarFile>` コマンドを使用して行います。

まとめとして、次の設定を使用したサンプルを以下に示します (Windows 環境を使用)。
* ACR 名: **`demodcos`**
* ユーザー名: **`demodcos`**
* パスワード: **`+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0`**
* ストレージ アカウント名: **`anystorageaccountname`**
* ストレージ アカウント キー: **`aYGl6Nys4De5J3VPldT1rXxz2+VjgO7dgWytnoWClurZ/l8iO5c5N8xXNS6mpJhSc9xh+7zkT7Mr+xIT4OIVMg==`**
* ストレージ アカウント内で作成した共有名: **`share`**
* アップロードする tar アーカイブのパス: **`%HOMEPATH%/.docker/docker.tar.gz`**

```bash
# Changing directory to the home folder of the default user
cd %HOMEPATH%

# Authentication into my ACR
docker login --username=demodcos --password=+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0 demodcos.azurecr.io

# Tar the contains of the .docker folder
tar czf docker.tar.gz .docker

# Upload the tar archive in the fileshare
az storage file upload -s share --account-name anystorageaccountname --account-key aYGl6Nys4De5J3VPldT1rXxz2+VjgO7dgWytnoWClurZ/l8iO5c5N8xXNS6mpJhSc9xh+7zkT7Mr+xIT4OIVMg== --source %HOMEPATH%/docker.tar.gz
```

### <a name="from-the-master-not-recommended-method"></a>マスターから管理する (非推奨)

ミスの影響が環境全体に影響が及ばないようにするために、マスターからは操作を実行しないことをお勧めします。

1. まず、DC/OS に基づくクラスターのマスター (または最初のマスター) に SSH 接続します。 たとえば、`ssh userName@masterFQDN –A –p 22` を使用します。ここで、masterFQDN は、マスター VM の完全修飾ドメイン名です。 [詳細についてはこちらを参照してください](https://docs.microsoft.com/azure/container-service/container-service-connect#connect-to-a-dcos-or-swarm-cluster)。

2. `sudo docker login --username=<USERNAME> --password=<PASSWORD> <ACR-REGISTRY-NAME>.azurecr.io` コマンドを実行して、ACR サービスへの認証を開始します。 `USERNAME`、`PASSWORD`、`ACR-REGISTRY-NAME` の各変数は Azure Portal で表示される値に置き換えてください。

3. `docker login` 操作を実行すると、これらの値がローカルのホーム フォルダー `~/.docker` に格納されます。 `tar czf` コマンドを使用してこのフォルダーの中身を圧縮します。

4. 最後に、先ほど作成した tar ファイルをファイル共有内にコピーします。 この操作により、クラスター内にある仮想マシンすべてでこの資格情報を使用し、Azure Container Registry に対する認証を行うことができるようになります。

まとめとして、次の設定を使用したサンプルを以下に示します。
* ACR 名: **`demodcos`**
* ユーザー名: **`demodcos`**
* パスワード: **`+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0`**
* クラスター内のマウント ポイント: **`/mnt/share`**

```bash
# Changing directory to the home folder of the default user
cd ~

# Authentication into my ACR
sudo docker login --username=demodcos --password=+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0 demodcos.azurecr.io

# Tar the contains of the .docker folder
sudo tar czf docker.tar.gz .docker

# Copy of the tar file in the file share of my cluster
sudo cp docker.tar.gz /mnt/share
```


## <a name="deploy-an-image-from-acr-with-marathon"></a>Marathon を使用して ACR からイメージをデプロイする

コンテナー レジストリ内にデプロイするイメージをプッシュ済みであると想定しています。 「[Docker CLI を使用してプライベート Docker コンテナー レジストリに最初のイメージをプッシュする](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli)」を参照してください。

たとえば、タグが **2.1** の **simple-web** イメージを Azure (ACR) でホストされているプライベート レジストリからデプロイするには、次の構成を使用します。

```json
{
  "id": "myapp",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "demodcos.azurecr.io/simple-web:2.1",
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
  },
  "uris":  [
       "file:///mnt/share/docker.tar.gz"
   ]
}
```

> [!NOTE] 
> ご覧のとおり、**uris** オプションを使用して資格情報の保存場所を指定しています。
>

## <a name="next-steps"></a>次のステップ
* [DC/OS コンテナーの管理](container-service-mesos-marathon-ui.md)で詳細を確認します。
* [Marathon REST API](container-service-mesos-marathon-rest.md) を使用して DC/OS コンテナーを管理します。
