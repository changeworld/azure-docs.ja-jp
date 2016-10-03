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
   ms.date="07/20/2016"
   ms.author="iainfou"/>

# Docker VM 拡張機能を使用した環境のデプロイ

> [AZURE.NOTE] お時間がございましたら、Azure Linux VM ドキュメントの向上のため、こちらの[アンケート](https://aka.ms/linuxdocsurvey)にご回答ください。いただいた回答は、今後のドキュメントの改善に活用させていただきます。

Docker は一般的なコンテナー管理およびイメージング プラットフォームで、Linux (および Windows) 上のコンテナーを簡単に操作できます。Azure を使用すると、ニーズに合ったいくつかの方法で Docker を柔軟にデプロイできます。

- アプリのプロトタイプを迅速に作成する場合、[Docker マシン Azure ドライバーを使用](./virtual-machines-linux-docker-machine.md)して、Azure 内で Docker ホストをデプロイできます。
- テンプレート ベースのデプロイでは、Azure 仮想マシンの Docker VM 拡張機能を使用します。この方法では、Azure Resource Manager テンプレートのデプロイと統合することができ、ロール ベースのアクセス、診断、デプロイ後の構成など、関連するあらゆるメリットが得られます。
- Docker VM 拡張機能では、Docker Compose もサポートしています。Docker Compose は、宣言型 YAML ファイルを使用して、開発者が構築したアプリケーションを環境全体で有効にして、一貫性のあるデプロイを生成します。
- Swarm が提供する追加のスケジュール設定および管理ツールを使用する実稼働レベルのスケーラブルなデプロイを実行するために、[Azure コンテナー サービスに完全な Docker Swarm クラスターをデプロイ](../container-service/container-service-deployment.md)することもできます。

この記事では、Resource Manager テンプレートを使用して、実稼働レベルの定義されたカスタム環境で Docker VM 拡張機能をデプロイする方法を説明します。

## テンプレートのデプロイ用 Azure Docker VM 拡張機能

Azure Docker VM 拡張機能では、Linux 仮想マシンで Docker デーモン、Docker クライアント、Docker Compose をインストールして構成します。この拡張機能は、Docker Compose を使用してコンテナー アプリケーションを定義し、デプロイするためにも使用します。Docker マシンの使用や Docker ホストの自作について制御を強化することができるので、堅牢性に優れた開発者環境や運用環境に適しています。

Azure Resource Manager を使用すると、環境の全体構造を定義するテンプレートを作成してデプロイすることができます。テンプレートを使用すると、Docker ホスト、ストレージ、ロール ベースのアクセス制御 (RBAC)、診断などを定義できます。そのメリットについて詳しくは、[Resource Manager](../resource-group-overview.md) およびテンプレートに関する記事をご覧ください。Resource Manager テンプレートを使用すると、将来必要に応じてデプロイを再現することもできます。

## Docker VM 拡張機能を使用したテンプレートのデプロイ:

それでは、既存のクイック スタート テンプレートを使用して、Docker VM 拡張機能がインストールされた Ubuntu VM をデプロイしてみましょう。テンプレートは以下より確認できます: [Simple deployment of an Ubuntu VM with Docker (Docker を使用した Ubuntu VM の簡単なデプロイ)](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)また、[最新の Azure CLI](../xplat-cli-install.md) をResource Manager モードにする必要があります (`azure config mode arm`)。

新しいリソース グループ (ここでは `myDockerResourceGroup`) の名前とテンプレート URI を指定して、Azure CLI を使ってテンプレートをデプロイします。

```
azure group create --name myDockerResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

メッセージに従ってストレージ アカウント名、ユーザー名とパスワード、および DNS 名を入力します。次の例のような出力が表示されます。

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

わずか数秒でプロンプトに戻りますが、バックグラウンドでは作成したリソース グループへのテンプレートのデプロイが進行中です。デプロイが完了するまで数分待ってから、VM に SSH で接続してください。

`azure vm show` コマンドでデプロイの詳細と VM の DNS 名を確認できます。次の例の `myDockerResourceGroup` を、前の手順で指定した名前に置き換えてください。

```bash
azure vm show -g myDockerResourceGroup -n myDockerVM
info:    Executing command vm show
+ Looking up the VM "myDockerVM"
+ Looking up the NIC "myVMNicD"
+ Looking up the public ip "myPublicIPD"
data:    Id                              :/subscriptions/guid/resourceGroups/mydockerresourcegroup/providers/Microsoft.Compute/virtualMachines/MyDockerVM
data:    ProvisioningState               :Succeeded
data:    Name                            :MyDockerVM
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_F1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :Canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :osdisk1
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :http://mydockerstorage.blob.core.windows.net/vhds/osdiskfordockersimple.vhd
data:
data:    OS Profile:
data:      Computer Name                 :MyDockerVM
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :false
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-D3-95
data:          Provisioning State        :Succeeded
data:          Name                      :myVMNicD
data:          Location                  :westus
data:            Public IP address       :13.91.107.235
data:            FQDN                    :mydockergroup.westus.cloudapp.azure.com
data:
data:    Diagnostics Instance View:
info:    vm show command OK
```

出力の先頭付近に VM の `ProvisioningState` が表示されます。ここで "`Succeeded`" と表示されている場合デプロイは完了しており、VM に SSH で接続できます。

出力の末尾付近にある `FQDN` は、指定したドメイン名と選択した場所に基づく完全修飾ドメイン名を示しています。この FQDN は、後の手順で VM に SSH 接続する際に使用するものです。


## 最初の nginx コンテナーのデプロイ
デプロイが完了したら、デプロイ時に指定した DNS 名を使用して、SSH で新しい Docker ホストに接続します。nginx コンテナーを実行してみましょう。

```
sudo docker run -d -p 80:80 nginx
```

次の例のような出力が表示されます。

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

`sudo docker ps` を使用してホストで実行されるコンテナーを確認します。

```
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

動作中のコンテナーを確認するには、Web ブラウザーを開き、デプロイ時に指定した DNS 名を入力します。

![実行中の ngnix コンテナー](./media/virtual-machines-linux-dockerextension/nginxrunning.png)

Docker デーモン TCP ポートやセキュリティを構成したり、Docker Compose を使ってコンテナーをデプロイすることができます。詳細については、[Docker GitHub プロジェクト用の Azure 仮想マシン拡張機能](https://github.com/Azure/azure-docker-extension/)に関するページを参照してください。

## Docker VM 拡張機能の JSON テンプレート リファレンス

この例では、クイック スタート テンプレートを使用しました。独自の Resource Manager テンプレートを使用して Azure Docker VM 拡張機能をデプロイするには、次の JSON を追加します。

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

Resource Manager テンプレートの使用方法の詳細については、「[Azure Resource Manager の概要](../resource-group-overview.md)」をご覧ください。

## 次のステップ

さまざまなデプロイのオプションの詳細な手順をご確認ください。

1. [Docker マシンと Azure ドライバーを使用する](./virtual-machines-linux-docker-machine.md)
2. [Azure コマンド ライン インターフェイス (Azure CLI) での Docker VM 拡張機能の使用](./virtual-machines-linux-classic-cli-use-docker.md)
3. [Docker と Compose を使用して Azure 仮想マシン上で複数コンテナー アプリケーションを定義して実行する](virtual-machines-linux-docker-compose-quickstart.md)
3. [Azure コンテナー サービス クラスターのデプロイ](../container-service/container-service-deployment.md)

<!---HONumber=AcomDC_0921_2016-->