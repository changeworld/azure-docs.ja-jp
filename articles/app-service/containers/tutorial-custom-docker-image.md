---
title: Web App for Containers のカスタム Docker イメージを使用する - Azure | Microsoft Docs
description: Web App for Containers のカスタム Docker イメージを使用する方法。
keywords: Azure App Service, Web アプリ, Linux, Docker, コンテナー
services: app-service
documentationcenter: ''
author: SyntaxC4
manager: SyntaxC4
editor: ''
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 161207b96deb2f7bd605d845a9207393f9f59c23
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444744"
---
# <a name="use-a-custom-docker-image-for-web-app-for-containers"></a>Web App for Containers のカスタム Docker イメージを使用する

[Web App for Containers](app-service-linux-intro.md) は、PHP 7.0 や Node.js 4.5 などの特定のバージョンをサポートする組み込みの Docker イメージを Linux 上で提供します。 Web App for Containers では、Docker コンテナー テクノロジを使用して、組み込みイメージとカスタム イメージの両方をサービスとしてのプラットフォームとしてホストします。 このチュートリアルでは、カスタム Docker イメージを作成し、Web App for Containers にデプロイする方法について説明します。 このパターンは、組み込みイメージに選択した言語が含まれない場合や、アプリケーションで組み込みイメージで提供されない特定の構成が必要となる場合に便利です。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * カスタム Docker イメージを Azure にデプロイする
> * コンテナーを実行するための環境変数を構成する
> * Docker イメージを更新して再デプロイする
> * SSH を使用してコンテナーに接続する
> * プライベート Docker イメージを Azure にデプロイする

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

* [Git](https://git-scm.com/downloads)
* [Docker](https://docs.docker.com/get-started/#setup)
* [Docker Hub アカウント](https://docs.docker.com/docker-id/)

## <a name="download-the-sample"></a>サンプルのダウンロード

ターミナル ウィンドウで、次のコマンドを実行して、サンプル アプリのリポジトリをローカル コンピューターに複製し、サンプル コードを含むディレクトリに移動します。

```bash
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
cd docker-django-webapp-linux
```

## <a name="build-the-image-from-the-docker-file"></a>Docker ファイルからイメージを作成する

Git リポジトリで、_Dockerfile_ を確認してください。 このファイルには、アプリケーションの実行に必要な Python 環境が記述されています。 さらに、イメージはコンテナーとホスト間のセキュアな通信用に [SSH](https://www.ssh.com/ssh/protocol/) サーバーを設定します。

```docker
FROM python:3.4

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222
#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

Docker イメージを作成するには、`docker build` コマンドを実行し、_mydockerimage_ という名前を付け、タグ _v1.0.0_ を付けます。 _\<docker-id>_ を Docker Hub アカウント ID で置換します。

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

コマンドによって次のような出力が生成されます。

```
# The output from the commands in this article has been shortened for brevity.

Sending build context to Docker daemon  5.558MB
Step 1/13 : FROM python:3.4
 ---> 9ff45ddb54e9
Step 2/13 : RUN mkdir /code
 ---> Using cache
 ---> f3f3ac01db0a
Step 3/13 : WORKDIR /code
 ---> Using cache
 ---> 38b32f15b442
.
.
.
Step 13/13 : ENTRYPOINT init.sh
 ---> Running in 5904e4c70043
 ---> e7cf08275692
Removing intermediate container 5904e4c70043
Successfully built e7cf08275692
Successfully tagged cephalin/mydockerimage:v1.0.0
```

Docker コンテナーを実行して、ビルドの動作をテストします。 [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) コマンドを発行し、イメージの名前とタグを渡します。 必ず `-p` 引数を使用してポートを指定してください。

```bash
docker run -p 2222:8000 <docker-ID>/mydockerimage:v1.0.0
```

`http://localhost:2222` を参照して、Web アプリとコンテナーが正しく機能していることを確認します。

![ローカルでの Web アプリのテスト](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

> [!NOTE] 
> SSH、SFTP、または Visual Studio Code (ライブ デバッグ Node.js アプリ用) を使用して、ローカル開発用コンピューターから直接アプリ コンテナーに接続することもできます。 詳細については、[Linux での App Service のリモート デバッグと SSH](https://aka.ms/linux-debug) に関するページをご覧ください。
>

## <a name="push-the-docker-image-to-docker-hub"></a>Docker イメージを Docker Hub にプッシュする

レジストリは、イメージをホストし、サービス イメージとコンテナー サービスを提供するアプリケーションです。 イメージを共有するには、レジストリにプッシュする必要があります。 

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry such as Azure Container Registry. Select the appropriate tab for your scenario below (your selection will switch multiple tabs on this page). -->

> [!NOTE]
> プライベート Docker レジストリにプッシュする場合は、 「[プライベート レジストリから Docker イメージを使用する](#use-a-docker-image-from-any-private-registry-optional)」の省略可能な手順をご覧ください。

<!--## [Docker Hub](#tab/docker-hub)-->

Docker Hub は Docker イメージのレジストリであり、パブリックまたはプライベートの独自のリポジトリをホストすることができます。 カスタム Docker イメージをパブリック Docker Hub にプッシュするには、[`docker push`](https://docs.docker.com/engine/reference/commandline/push/) コマンドを使用し、完全なイメージ名とタグを指定します。 完全なイメージ名とタグは、次の例のようになります。

```
<docker-id>/image-name:tag
```

イメージをプッシュする前に、[`docker login`](https://docs.docker.com/engine/reference/commandline/login/) コマンドを使用して Docker Hub にサインインする必要があります。 _\<docker-id>_ をアカウント名に置き換え、プロンプトで、コンソールにパスワードを入力します。

```bash
docker login --username <docker-id>
```

「ログインに成功しました」のメッセージで、ログインしていることを確認します。 ログインしたら、[`docker push`](https://docs.docker.com/engine/reference/commandline/push/) コマンドを使用して Docker Hub にイメージをプッシュできます。

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

コマンドの出力を調べて、プッシュが成功したことを確認します。

```
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
c33197c3f6d4: Pushed
ccd2c850ee43: Pushed
02dff2853466: Pushed
6ce78153632a: Pushed
efef3f03cc58: Pushed
3439624d77fb: Pushed
3a07adfb35c5: Pushed
2fcec228e1b7: Mounted from library/python
97d2d3bae505: Mounted from library/python
95aadeabf504: Mounted from library/python
b456afdc9996: Mounted from library/python
d752a0310ee4: Mounted from library/python
db64edce4b5b: Mounted from library/python
d5d60fc34309: Mounted from library/python
c01c63c6823d: Mounted from library/python
v1.0.0: digest: sha256:21f2798b20555f4143f2ca0591a43b4f6c8138406041f2d32ec908974feced66 size: 3676
```

<!--
# [Private Registry](#tab/private-registry)

// Move Private Registry instructions here when Tabbed Conceptual bug is fixed

---
-->

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>アプリを Azure にデプロイする

Azure Web Apps を使用して、クラウドにネイティブの Linux アプリケーションをホストすることができます。 Web App for Containers を作成するには、グループ、続いてサービス プラン、最後に Web アプリ自体を作成する Azure CLI コマンドを実行する必要があります。 

### <a name="create-a-resource-group"></a>リソース グループの作成

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-a-linux-app-service-plan"></a>Linux App Service プランの作成

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)] 

### <a name="create-a-web-app"></a>Web アプリを作成する

Cloud Shell で [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) コマンドを使用して、`myAppServicePlan` App Service プランに [Web アプリ](app-service-linux-intro.md)を作成します。 必ず _<appname>_ を一意のアプリ名に、_\<docker-ID>_ をお使いの Docker ID に置き換えてください。

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --deployment-container-image-name <docker-ID>/mydockerimage:v1.0.0
```

Web アプリが作成されると、Azure CLI によって次の例のような出力が表示されます。

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="configure-environment-variables"></a>環境変数を構成する

ほとんどの Docker イメージには、構成する必要がある環境変数があります。 他のユーザーによって作成された既存の Docker イメージを使用する場合、イメージは 80 以外のポートを使用する場合があります。 イメージが使用するポートを Azure に指示するには、`WEBSITES_PORT` アプリケーション設定を使用します。 [このチュートリアルに含まれる Python サンプル](https://github.com/Azure-Samples/docker-django-webapp-linux)の GitHub ページは、`WEBSITES_PORT` を _8000_ に設定する必要があることを示しています。

アプリ設定を設定するには、Cloud Shell で [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) コマンドを使用します。 アプリケーション設定は、大文字と小文字を区別し、スペースで区切られます。

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITES_PORT=8000
```

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry, such as Azure Container Registry. Select the appropriate tab for your scenario below: -->

> [!NOTE]
> プライベート Docker レジストリからデプロイする場合は、 「[プライベート レジストリから Docker イメージを使用する](#use-a-docker-image-from-any-private-registry-optional)」の省略可能な手順をご覧ください。

<!-- # [Docker Hub](#tab/docker-hub)-->

<!-- # [Private Registry](#tab/private-registry)

// Place Private Registry text back here once Tabbed Conceptual bug is fixed

---
-->

### <a name="test-the-web-app"></a>Web アプリをテストする

Web アプリの動作を確認するには、これを参照します (`http://<app_name>azurewebsites.net`)。 

![Web アプリ ポート構成をテストする](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>Web アプリを変更して再デプロイする

ローカルの Git リポジトリで、app/templates/app/index.html を開きます。 最初の HTML 要素を見つけて、それを次のように変更します。

```python
<nav class="navbar navbar-inverse navbar-fixed-top">
    <div class="container">
      <div class="navbar-header">         
        <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>       
      </div>            
    </div>
  </nav> 
```

Python ファイルを変更し、保存した後で、新しい Docker イメージをリビルドし、プッシュする必要があります。 次に、変更を反映するために Web アプリを再起動します。 このチュートリアルで前に使用したのと同じコマンドを使用します。 「[Docker ファイルからイメージを作成する](#build-the-image-from-the-docker-file)」と「[Docker イメージを Docker Hub にプッシュする](#push-the-docker-image-to-docker-hub)」をご覧ください。 「[Web アプリをテストする](#test-the-web-app)」の手順に従って、Web アプリをテストします。

## <a name="connect-to-web-app-for-containers-using-ssh"></a>SSH を使用して Web App for Containers に接続する

SSH では、コンテナーとクライアント間の通信をセキュリティで保護できます。 カスタム Docker イメージで SSH をサポートするには、Dockerfile に組み込む必要があります。 Docker ファイル自体で SSH を有効にします。 SSH 命令は既にサンプルの dockerfile に追加されているので、独自のカスタム イメージでこれらの手順に従うことができます。

* `apt-get` を呼び出してからルート アカウントのパスワードを `"Docker!"` に設定する [RUN](https://docs.docker.com/engine/reference/builder/#run) 命令。

    ```docker
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > この構成では、コンテナーへの外部接続は許可されません。 SSH は Kudu/SCM サイトを通してのみ利用できます。 Kudu/SCM サイトは、資格情報を発行することで認証されます。

* Docker エンジンに [sshd_config](http://man.openbsd.org/sshd_config) ファイルを "*/etc/ssh/*" ディレクトリにコピーするよう指示する [COPY](https://docs.docker.com/engine/reference/builder/#copy) 命令。 構成ファイルは[この sshd_config ファイル](https://github.com/Azure-App-Service/node/blob/master/6.11.1/sshd_config)に基づく必要があります。

    ```docker
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > *sshd_config* ファイルには次の項目を指定する必要があります。 
    > * `Ciphers` には、`aes128-cbc,3des-cbc,aes256-cbc` の項目を少なくとも 1 つ含める必要があります。
    > * `MACs` には、`hmac-sha1,hmac-sha1-96` の項目を少なくとも 1 つ含める必要があります。

* コンテナーでポート 2222 を公開する [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) 命令。 ルート パスワードはわかっていますが、ポート 2222 はインターネットからアクセスすることはできません。 それはプライベート仮想ネットワークのブリッジ ネットワーク内でコンテナーのみがアクセスできる内部ポートです。 その後、コマンドが SSH 構成の詳細をコピーし、`ssh` サービスを起動します。

    ```docker
    EXPOSE 8000 2222
    ```

* /bin ディレクトリ内のシェル スクリプトを使用して [ssh サービスを開始](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh)してください。
 
    ```bash
    #!/bin/bash
    service ssh start
    ```
     
### <a name="open-ssh-connection-to-container"></a>コンテナーへの SSH 接続 を開く

Web App for Containers では、コンテナーへの外部接続は許可されません。 SSH は Kudu サイトを通してのみ利用できます。Kudo サイトには `https://<app_name>.scm.azurewebsites.net` からアクセスできます。

接続するには、`https://<app_name>.scm.azurewebsites.net/webssh/host` に移動し、Azure アカウントでサインインします。

その後、対話型コンソールを表示するページにリダイレクトされます。 

特定のアプリケーションがコンテナーで実行されていることを確認できます。 コンテナーを検査し、実行中のプロセスを確認するには、プロンプトで `top` コマンドを発行します。

```bash
top
```

`top` コマンドは、コンテナー内のすべての実行中のプロセスを公開します。

```
PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
 1 root      20   0  945616  35372  15348 S  0.0  2.1   0:04.63 node
20 root      20   0   55180   2776   2516 S  0.0  0.2   0:00.00 sshd
42 root      20   0  944596  33340  15352 S  0.0  1.9   0:05.80 node /opt/s+
56 root      20   0   59812   5244   4512 S  0.0  0.3   0:00.93 sshd
58 root      20   0   20228   3128   2664 S  0.0  0.2   0:00.00 bash
62 root      20   0   21916   2272   1944 S  0.0  0.1   0:03.15 top
63 root      20   0   59812   5344   4612 S  0.0  0.3   0:00.03 sshd
65 root      20   0   20228   3140   2672 S  0.0  0.2   0:00.00 bash
71 root      20   0   59812   5380   4648 S  0.0  0.3   0:00.02 sshd
73 root      20   0   20228   3160   2696 S  0.0  0.2   0:00.00 bash
77 root      20   0   21920   2304   1972 R  0.0  0.1   0:00.00 top
```

お疲れさまでした。 Web App for Containers のカスタム Docker イメージの構成が完了しました。

## <a name="use-a-private-image-from-docker-hub-optional"></a>(省略可能) Docker Hub からプライベート イメージを使用する

「[Web アプリを作成する](#create-a-web-app)」では、`az webapp create` コマンドで Docker Hub のイメージを指定しました。 パブリック イメージの場合はこれで十分です。 プライベート イメージを使用するには、Azure Web アプリで Docker アカウント ID とパスワードを構成する必要があります。

Cloud Shell で、`az webapp create` コマンドの後に [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) を続けます。 *\<app_name>* を置き換え、さらに _\<docker-id>_ と _\<password>_ も Docker ID とパスワードに置き換えます。

```azurecli-interactive
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-registry-server-user <docker-id> --docker-registry-server-password <password>
```

コマンドは、次の JSON 文字列のような出力を表示し、構成が正常に変更されたことを示します。

```json
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "false"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "<docker-id>"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  },
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "DOCKER|<image-name-and-tag>"
  }
]
```

## <a name="use-a-docker-image-from-any-private-registry-optional"></a>プライベート レジストリから Docker イメージを使用する (省略可能)

このセクションでは、Web App for Containers でプライベート レジストリから Docker イメージを使用する方法について説明します。例として Azure Container Registry を使用します。 その他のプライベート レジストリを使用するための手順も同様です。 

Azure Container Registry は、プライベート イメージをホスティングするために Azure から管理される Docker サービスです。 デプロイには、[Docker Swarm](https://docs.docker.com/engine/swarm/)、[Kubernetes](https://kubernetes.io/)、Web App for Containers などの任意の種類を使用できます。 

### <a name="create-an-azure-container-registry"></a>Azure Container Registry を作成する

Cloud Shell で、[`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create) コマンドを使用して Azure Container Registry を作成します。 名前、リソース グループ、SKU として `Basic` を渡します。 利用可能な SKU は `Classic`、`Basic`、`Standard`、`Premium` です。

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

コンテナーを作成すると、次の出力が生成されます。

```
 - Finished ..
Create a new service principal and assign access:
  az ad sp create-for-rbac --scopes /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --password <password>

Use an existing service principal and assign access:
  az role assignment create --scope /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --assignee <app-id>
{
  "adminUserEnabled": false,
  "creationDate": "2017-08-09T04:21:09.654153+00:00",
  "id": "/subscriptions/<subscriptionId>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name>",
  "location": "westeurope",
  "loginServer": "<azure-container-registry-name>.azurecr.io",
  "name": "<azure-container-registry-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "myazurecontainerre042025"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="log-in-to-azure-container-registry"></a>Azure Container Registry にログインする

レジストリにイメージをプッシュするには、レジストリがプッシュを受け入れるように資格情報を指定する必要があります。 これらの資格情報は、Cloud Shell で [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az-acr-show) コマンドを使用して取得することができます。 

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

このコマンドでは、ユーザー名に使用できる 2 つのパスワードが表示されます。

```json
<
  "passwords": [
    {
      "name": "password",
      "value": "{password}"
    },
    {
      "name": "password2",
      "value": "{password}"
    }
  ],
  "username": "<registry-username>"
}
```

ローカルのターミナル ウィンドウから、`docker login` コマンドを使用して Azure Container Registry にログインします。 ログインするには、サーバー名が必要です。 `{azure-container-registry-name>.azurecr.io` の形式を使用します。 プロンプトで、コンソールにパスワードを入力します。

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username>
```

ログインが成功したことを確認します。 

### <a name="push-an-image-to-azure-container-registry"></a>Azure Container Registry へのイメージのプッシュ

> [!NOTE]
> 独自のイメージを使用している場合は、次のようにイメージにタグ付けします。
> ```bash
> docker tag <azure-container-registry-name>.azurecr.io/mydockerimage
> ```

コマンド `docker push` を使用してイメージをプッシュします。 レジストリの名前、イメージの名前、およびタグでイメージをタグ付けします。

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

ACR リポジトリを一覧表示して、プッシュによってコンテナーがレジストリに正常に追加されたことを確認します。 

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

レジストリ内のイメージを一覧表示することで、`mydockerimage` がレジストリに存在することを確認します。

```json
[
  "mydockerimage"
]
```

### <a name="configure-web-app-to-use-the-image-from-azure-container-registry-or-any-private-registry"></a>Azure Container Registry (または任意のプライベート レジストリ) からイメージを使用するように Web アプリを構成する

Azure Container Registry に格納されているコンテナーを実行するように、Web App for Containers を構成できます。 Azure Container Registry の使用はプライベート レジストリの使用と同様であるため、独自のプライベート レジストリを使用する必要がある場合、このタスクを完了する手順も同様です。

Cloud Shell で [`az acr credential show`](/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show) を実行して、Azure Container Registry のユーザー名とパスワードを表示します。 次の手順で Web アプリの構成に使用できるように、ユーザー名とパスワードの 1 つをコピーします。

```bash
az acr credential show --name <azure-container-registry-name>
```

```json
{
  "passwords": [
    {
      "name": "password",
      "value": "password"
    },
    {
      "name": "password2",
      "value": "password2"
    }
  ],
  "username": "<registry-username>"
}
```

Cloud Shell で [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) コマンドを実行して、カスタム Docker イメージを Web アプリに割り当てます。 *\<app_name>*、*\<docker-registry-server-url>*、_\<registry-username>_、_\<password>_ を置き換えます。 Azure Container Registry では、*\<docker-registry-server-url>* は `https://<azure-container-registry-name>.azurecr.io` の形式になります。 Docker Hub に加えてレジストリを使用している場合は、イメージ名をレジストリの完全修飾ドメイン名 (FQDN) で始める必要があります。 Azure Container Registry の場合は、`<azure-container-registry>.azurecr.io/mydockerimage` のように記述します。 

```azurecli-interactive
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-custom-image-name <azure-container-registry-name>.azurecr.io/mydockerimage --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> *\<docker-registry-server-url>* には `https://` が必要です。
>

コマンドは、次の JSON 文字列のような出力を表示し、構成が正常に変更されたことを示します。

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "slotSetting": false,
    "value": "mydockerimage"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_URL",
    "slotSetting": false,
    "value": "<azure-container-registry-name>.azurecr.io"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "<registry-username>"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  }
]
```

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure で Docker Python と PostgreSQL Web アプリを作成する](tutorial-docker-python-postgresql-app.md)
