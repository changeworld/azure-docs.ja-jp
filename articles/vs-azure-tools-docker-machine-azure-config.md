<properties
   pageTitle="Docker マシンを利用し、Azure で Docker ホストを作成する | Microsoft Azure"
   description="Docker マシンを利用し、Azure で Docker ホストを作成する方法について説明します。"
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="mlearned" />

# Docker マシンを利用し、Azure で Docker ホストを作成する

[Docker](https://www.docker.com/) コンテナーを実行するには、Docker デーモンを実行しているホスト VM が必要です。このトピックでは、[docker-machine](https://docs.docker.com/machine/) コマンドを使用して、Docker デーモンで構成され、Azure で実行される新しい Linux VM を作成する方法について説明します。

**注:**
- *この記事は docker-machine バージョン 0.7.0 以上に依存します*
- *Windows コンテナーは今後の docker-machine でサポートされる予定です*

## Docker マシンで VM を作成する

`azure` ドライバーを使用する `docker-machine create` コマンドを使用して、Azure に Docker ホスト VM を作成します。

Azure ドライバーにはサブスクリプション ID が必要になります。[Azure CLI](xplat-cli-install.md) または [Azure Portal](https://portal.azure.com) を使用して、Azure サブスクリプションを取得することができます。

**Azure ポータルの使用**
- 左側のナビゲーション ページからサブスクリプションを選択し、サブスクリプション ID をコピーします。

**Azure CLI の使用**
- 「```azure account list```」と入力し、サブスクリプション ID をコピーします。

「`docker-machine create --driver azure`」と入力し、オプションとその既定値を表示します。[Docker の Azure ドライバー ドキュメント](https://docs.docker.com/machine/drivers/azure/)で詳細を確認することもできます。

次の例では既定値を使用していますが、インターネット アクセス用に必要に応じて VM のポート 80 を開くことができます。

```
docker-machine create -d azure --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> --azure-open-port 80 mydockerhost
```

## docker-machine で Docker ホストを選択する
ホストの docker-machine に入力が行われていると、docker コマンドを実行する際に既定のホストを設定することができます。
##PowerShell の使用

```powershell
docker-machine env MyDockerHost | Invoke-Expression 
```

##Bash の使用

```bash
eval $(docker-machine env MyDockerHost)
```

指定したホストに対して docker コマンドを実行できます

```
docker ps
docker info
```

## コンテナーを実行する

ホストを構成すると、単純な Web サーバーを実行して、ホストが正しく構成されているかどうかをテストすることができます。ここでは標準の nginx イメージを使用し、ポート 80 で待機することと、ホスト VM の再起動時にコンテナーも再起動することを指定します (`--restart=always`)。

```bash
docker run -d -p 80:80 --restart=always nginx
```

出力は次のようになります。

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
83f52fbfa5f8: Pull complete
fa664caa1402: Pull complete
Digest: sha256:12127e07a75bda1022fbd4ea231f5527a1899aad4679e3940482db3b57383b1d
Status: Downloaded newer image for nginx:latest
25942c35d86fe43c688d0c03ad478f14cc9c16913b0e1c2971cb32eb4d0ab721
```

## コンテナーをテストする

`docker ps` を利用し、実行中のコンテナーを調べます。

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

実行中のコンテナーを確認します。「`docker-machine ip <VM name>`」と入力すると、ブラウザーに入力する IP アドレスが見つかります。

```
PS C:\> docker-machine ip MyDockerHost
191.237.46.90
```

![実行中の ngnix コンテナー](./media/vs-azure-tools-docker-machine-azure-config/nginxsuccess.png)

##概要
docker-machine を使用して、個々の Docker ホストの検証のために Azure に Docker ホストを簡単にプロビジョニングできます。コンテナーの運用ホスティングについては、「[Azure Container Service の概要](http://aka.ms/AzureContainerService)」を参照してください。

Visual Studio を使用した .NET Core アプリケーションの開発については、「[Docker Tools for Visual Studio (Visual Studio 用 Docker ツール)](http://aka.ms/DockerToolsForVS)」を参照してください。

<!---HONumber=AcomDC_0921_2016-->