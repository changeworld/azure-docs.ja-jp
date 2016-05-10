<properties
	pageTitle="Docker マシンを利用し、Azure で Docker ホストを作成する | Microsoft Azure"
	description="Docker マシンを利用し、Azure で Docker ホストを作成する方法について説明します。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="04/20/2016"
	ms.author="rasquill"/>

# Docker マシンと Azure ドライバーを使用する

[Docker](https://www.docker.com/) は、最もよく利用されている仮想化アプローチの 1 つで、アプリケーション データの分離と共有リソースでのコンピューティングの手段として仮想マシンではなく Linux コンテナーを使用します。このトピックでは、[Docker マシン](https://docs.docker.com/machine/) (`docker-machine` コマンド) を利用し、Azure で新しい Linux VM を作成し、Linux コンテナーの Docker ホストとして有効にする場面と方法について説明します。


## Docker マシンで VM を作成する

ドライバー オプション (`-d`) の `azure` ドライバー引数とその他の引数を利用し、`docker-machine create` コマンドで Azure に Docker ホスト VM を作成します。

次の例では既定値が利用されていますが、VM のポート 80 を開いてインターネットに接続し、nginx コンテナーをテストし、`ops` を SSH のログオン ユーザーに設定し、新しい VM `machine` を呼び出します。

`docker-machine create --driver azure` と入力すると、オプションとその既定値が表示されます。[Docker Azure ドライバー文書](https://docs.docker.com/machine/drivers/azure/)も参照してください。(2 要素認証を有効にした場合、2 つ目の要素を使用した認証が求められます)。

```bash
docker-machine create -d azure \
  --azure-ssh-user ops \
  --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> \
  --azure-open-port 80 \
  machine
```

アカウントに 2 要素認証を設定しているかどうかにもよりますが、出力は次のようになります。

```
Creating CA: /Users/user/.docker/machine/certs/ca.pem
Creating client certificate: /Users/user/.docker/machine/certs/cert.pem
Running pre-create checks...
(machine) Microsoft Azure: To sign in, use a web browser to open the page https://aka.ms/devicelogin. Enter the code <code> to authenticate.
(machine) Completed machine pre-create checks.
Creating machine...
(machine) Querying existing resource group.  name="machine"
(machine) Creating resource group.  name="machine" location="eastus"
(machine) Configuring availability set.  name="docker-machine"
(machine) Configuring network security group.  name="machine-firewall" location="eastus"
(machine) Querying if virtual network already exists.  name="docker-machine-vnet" location="eastus"
(machine) Configuring subnet.  name="docker-machine" vnet="docker-machine-vnet" cidr="192.168.0.0/16"
(machine) Creating public IP address.  name="machine-ip" static=false
(machine) Creating network interface.  name="machine-nic"
(machine) Creating storage account.  name="vhdsolksdjalkjlmgyg6" location="eastus"
(machine) Creating virtual machine.  name="machine" location="eastus" size="Standard_A2" username="ops" osImage="canonical:UbuntuServer:15.10:latest"
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with ubuntu(systemd)...
Installing Docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env machine
```

## Docker シェルを構成する

次に、`docker-machine env <VM name>` と入力すると、シェルの構成に必要なものが表示されます。

```bash
docker-machine env machine
```

次のような環境情報が出力されます。IP アドレスが割り当てられています。これは VM のテストで必要になります。

```
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://191.237.46.90:2376"
export DOCKER_CERT_PATH="/Users/rasquill/.docker/machine/machines/machine"
export DOCKER_MACHINE_NAME="machine"
# Run this command to configure your shell:
# eval $(docker-machine env machine)
```

提案された構成コマンドを実行するか、環境変数を自分で設定できます。

## コンテナーを実行する

これで単純な Web サーバーを実行し、すべてが正常に動作しているかどうかをテストできます。ここでは標準の nginx イメージを使用し、ポート 80 で待機することと、VM の再起動時にコンテナーも再起動することを指定します (`--restart=always`)。

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

実行中のコンテナーを確認します。`docker-machine ip <VM name>` と入力すると、IP アドレスが見つかります (`env` コマンドで見つけたアドレスを忘れた場合)。

![実行中の ngnix コンテナー](./media/virtual-machines-linux-docker-machine/nginxsuccess.png)

## 次のステップ

興味があれば、Azure [Docker VM Extension](virtual-machines-linux-dockerextension.md) で同じ操作を試してください。Azure CLI または Azure リソース マネージャーのテンプレートを使用します。

<!---HONumber=AcomDC_0427_2016-->