<properties
   pageTitle="Azure での Docker VM 拡張機能について | Microsoft Azure"
   description="Docker VM 拡張機能を使用して、Azure で Docker 環境をすばやく安全にデプロイする方法を説明します。"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="04/27/2016"
   ms.author="iainfou"/>

# Docker VM 拡張機能を使用した環境のデプロイ

Docker は一般的なコンテナー管理およびイメージング プラットフォームで、Linux (および Windows) 上のコンテナーを簡単に操作できます。Azure を使用すると、ニーズに合ったいくつかの方法で Docker を柔軟にデプロイできます。

- アプリのプロトタイプを作成するには、または Docker マシンの知識があるか、既に使用している場合は、[Docker マシンと Azure ドライバーを使用](./virtual-machines-linux-docker-machine.md)して、Azure 内で Docker ホストを起動できます。
- ロールベースのアクセス制御、診断、ログに記録され制御されたデプロイメントなど、Azure テンプレートで定義する再現不可能な方法での Docker 環境のデプロイをより詳細に制御するには、Azure Docker VM 拡張機能を Azure Resource Manager と共に使用するのが最良のアプローチです。
- Swarm が提供する追加のスケジュール設定および管理ツールを活用する実稼働レベルのスケーラブルなデプロイ用に [Azure コンテナー サービスに完全な Docker Swarm クラスターをデプロイ](../container-service/container-service-deployment.md)することもできます。

この記事では、Resource Manager テンプレートを使用して、実稼働レベルの定義されたカスタム環境で Docker VM 拡張機能をデプロイする方法を説明します。

## テンプレートのデプロイ用 Azure Docker VM 拡張機能

Azure Docker VM 拡張機能は Docker を Linux VM にインストールし、自動的に構成します。Resource Manager テンプレートを使用することで、環境を一貫した方法で再デプロイできます。Azure Docker VM 拡張機能の使用はより信頼性の高いソリューションの開発者や運用環境に最適です。これは、Docker マシンを使用するか、Docker ホストを自分で作成して簡単に詳細な制御を行えるためです。

Azure Resource Manager を使用すると、環境の構造全体 (Docker ホスト、ストレージ、ロールベースのアクセス制御 (RBAC)、診断など) を定義するテンプレートを作成し、デプロイできます。そのメリットについて詳しくは、[Resource Manager](../resource-group-overview.md) およびテンプレートに関する記事をご覧ください。Docker マシンだけでなく Resource Manager テンプレートも使用するメリットは、追加の Docker ホスト、ストレージ、アクセス制御などを定義でき、また、将来的な必要性に応じてデプロイメントを再現できることです。

## Docker VM 拡張機能を使用したテンプレートのデプロイ:

それでは、既存のクイック スタート テンプレートを使用して、Docker VM 拡張機能がインストールされた Ubuntu VM をデプロイしてみましょう。テンプレートは以下より確認できます: [Simple deployment of an Ubuntu VM with Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu) (Docker を使用した Ubuntu VM の簡単なデプロイ)

新しいリソース グループ (ここでは `myDockerResourceGroup`) の名前とテンプレート URI を指定して、Azure CLI を使ってテンプレートをデプロイします。

```
azure group create --name myDockerResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

ストレージ アカウント、DNS 名、ユーザー名などを指定するためのプロンプトに入力したら、デプロイが完了するまで数分待ちます。次のような出力が表示されます。

```
info:    Executing command group create
+ Getting resource group myDockerResourceGroup
+ Updating resource group myDockerResourceGroup
info:    Updated resource group myDockerResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mydockerstorage
adminUsername: ops
adminPassword: P@ssword!
dnsNameForPublicIP: mydockergroup
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/guid/resourceGroups/myDockerResourceGroup
data:    Name:                myDockerResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK

```

## 最初の nginx コンテナーのデプロイ

デプロイが完了したら、デプロイ時に指定した DNS 名を使用して、SSH で新しい Docker ホストに接続します。Docker ツールが既にインストールされているので、nginx コンテナーを起動してみましょう。

```
docker run -d -p 80:80 nginx
```

次のような出力が表示されます。

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
a48df1751a97: Pull complete
8ddc2d7beb91: Pull complete
Digest: sha256:2ca2638e55319b7bc0c7d028209ea69b1368e95b01383e66dfe7e4f43780926d
Status: Downloaded newer image for nginx:latest
b6ed109fb743a762ff21a4606dd38d3e5d35aff43fa7f12e8d4ed1d920b0cd74
```

`docker ps` を使用してホストで実行されるコンテナーを確認します。

```
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   nostalgic_murdock
```

Web ブラウザーを開き、デプロイ時に指定した DNS 名を入力してコンテナーの動作を確認します。

![実行中の ngnix コンテナー](./media/virtual-machines-linux-dockerextension/nginxrunning.png)

## Docker VM 拡張機能の JSON テンプレート リファレンス

この例では、クイック スタート テンプレートを使用しました。独自の既存の Resource Manager テンプレートを使用して、Docker VM 拡張機能を、テンプレート内で定義された VM にインストールすることもできます。そのためには、以下を JSON 定義ファイルに追加します。

```
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'), '/DockerExtension'))]",
  "apiVersion": "2015-05-01-preview",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "DockerExtension",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Resource Manager テンプレートを使用する詳細な手順については、「[Azure Resource Manager の概要](../resource-group-overview.md)」をご覧ください。

## 次のステップ

さまざまなデプロイのオプションの詳細な手順をご確認ください。
  1. [Docker マシンと Azure ドライバーを使用する](./virtual-machines-linux-docker-machine.md)
  2. [Azure コマンド ライン インターフェイス (Azure CLI) での Docker VM 拡張機能の使用](./virtual-machines-linux-classic-cli-use-docker.md)
  3. [Azure コンテナー サービス クラスターのデプロイ](../container-service/container-service-deployment.md)

<!---HONumber=AcomDC_0504_2016-->