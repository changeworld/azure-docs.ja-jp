---
title: Azure App Service on Linux での SSH のサポート | Microsoft Docs
description: Azure App Service on Linux で SSH を使用する方法について説明します。
keywords: Azure App Service, Web アプリ, Linux, OSS
services: app-service
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: wesmc
ms.openlocfilehash: 63fdf2cc82438fe55792b12244dd697721adda15
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39579580"
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>Azure App Service on Linux での SSH のサポート

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) は、一般にコマンド ライン ターミナルから管理コマンドをリモートで実行するために使用されます。 App Service on Linux では、新しい Web アプリのランタイム スタックで使用される各組み込み Docker イメージで、アプリ コンテナーへの SSH をサポートしています。 

![ランタイム スタック](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

カスタム Docker イメージでは、カスタム イメージ内で SSH サーバーを構成します。

SSH と SFTP を使用して、ローカル開発マシンからコンテナーに直接接続することもできます。

## <a name="open-ssh-session-in-browser"></a>ブラウザーで SSH セッションを開く

コンテナーとの SSH クライアント接続を確立するには、アプリが実行されている必要があります。

ブラウザーに次の URL を貼り付け、\<app_name> をアプリの名前に置き換えます。

```
https://<app_name>.scm.azurewebsites.net/webssh/host
```

既に認証されていない場合、接続するには Azure サブスクリプションで認証する必要があります。 認証されると、ブラウザー内シェルが表示され、コンテナー内でコマンドを実行することができます。

![SSH 接続](./media/app-service-linux-ssh-support/app-service-linux-ssh-connection.png)

## <a name="use-ssh-support-with-custom-docker-images"></a>カスタム Docker イメージで SSH サポートを使用する

Azure ポータルでのコンテナーとクライアント間の SSH 通信をカスタム Docker イメージでサポートするために、Docker イメージに対して次の手順を実行します。

これらの手順は、Azure App Service リポジトリに[例](https://github.com/Azure-App-Service/node/blob/master/6.9.3/)として示されています。

1. イメージ用の Dockerfile ファイルの [`RUN` 命令](https://docs.docker.com/engine/reference/builder/#run)に `openssh-server` のインストールを含め、ルート アカウントのパスワードとして `"Docker!"` を設定します。

    > [!NOTE]
    > この構成では、コンテナーへの外部接続は許可されません。 SSH は Kudu/SCM サイト経由でのみアクセスでき、公開されている資格情報を使用して認証されます。

    ```docker
    # ------------------------
    # SSH Server support
    # ------------------------
    RUN apt-get update \
        && apt-get install -y --no-install-recommends openssh-server \
        && echo "root:Docker!" | chpasswd
    ```

1. [sshd_config](http://man.openbsd.org/sshd_config) ファイルを */etc/ssh/* ディレクトリにコピーする [`COPY` 命令](https://docs.docker.com/engine/reference/builder/#copy)を Dockerfile に 追加します。 構成ファイルは、GitHub レポジトリに格納されている Azure App Service の sshd_config ファイルに基づいている必要があります ([こちら](https://github.com/Azure-App-Service/node/blob/master/8.2.1/sshd_config)を参照してください)。

    > [!NOTE]
    > *sshd_config* ファイルには次の項目を指定する必要があります。指定がない場合、接続は失敗します。 
    > * `Ciphers` には次の値を少なくとも 1 つ指定する必要あります。`aes128-cbc,3des-cbc,aes256-cbc`
    > * `MACs` には次の値を少なくとも 1 つ指定する必要あります。`hmac-sha1,hmac-sha1-96`

    ```docker
    COPY sshd_config /etc/ssh/
    ```

1. Dockerfile の [`EXPOSE` 命令](https://docs.docker.com/engine/reference/builder/#expose)に、ポート 2222 を含めます。 ルート パスワードはわかっていますが、ポート 2222 はインターネットからアクセスすることはできません。 それはプライベート仮想ネットワークのブリッジ ネットワーク内でコンテナーのみがアクセスできる内部専用ポートです。

    ```docker
    EXPOSE 2222 80
    ```

1. シェル スクリプトを使って SSH サービスを開始します ([init_container.sh](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh) の例を参照)。

    ```bash
    #!/bin/bash
    service ssh start
    ```

Dockerfile では、[`ENTRYPOINT` 命令](https://docs.docker.com/engine/reference/builder/#entrypoint)を使用してスクリプトを実行します。

    ```docker
    COPY startup /opt/startup
    ...
    RUN chmod 755 /opt/startup/init_container.sh
    ...
    ENTRYPOINT ["/opt/startup/init_container.sh"]
    ```

## <a name="open-ssh-session-from-remote-shell"></a>リモート シェルから SSH セッションを開く

> [!NOTE]
> 現在、この機能はプレビュー段階にあります。
>

TCP トンネリングを使用して、認証済みの WebSocket 接続経由で開発用マシンと Web App for Containers 間にネットワーク接続を作成できます。 これにより、任意のクライアントから App Service で実行されているコンテナーとの SSH セッションを開くことができます。

最初に、[Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) をインストールする必要があります。 Azure CLI をインストールしないとどのように動作するかを確認するには、[Azure Cloud Shell](../../cloud-shell/overview.md) を開きます。 

[az extension add](/cli/azure/extension?view=azure-cli-latest#az-extension-add) を実行して、最新の App Service 拡張機能を追加します。

```azurecli-interactive
az extension add --name webapp
```

`az extension add` を以前に実行した場合は、代わりに [az extension update](/cli/azure/extension?view=azure-cli-latest#az-extension-update) を実行します。

```azurecli-interactive
az extension update --name webapp
```

[az webapp remote-connection create](/cli/azure/ext/webapp/webapp/remote-connection?view=azure-cli-latest#ext-webapp-az-webapp-remote-connection-create) コマンドを使用して、アプリへのリモート接続を開きます。 アプリに _\<subscription\_id>_、_\<group\_name>_、および \_<app\_name>_ を指定し、\<port> をローカル ポート番号に置き換えます。

```azurecli-interactive
az webapp remote-connection create --subscription <subscription_id> --resource-group <group_name> -n <app_name> -p <port> &
```

> [!TIP]
> コマンドの末尾の `&` は、Cloud Shell を使用している場合の便宜のためにあります。 同じシェルで次のコマンドを実行できるように、プロセスをバック グラウンドで実行します。

コマンドの出力では、SSH セッションを開くために必要な情報が示されます。

```
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

ローカル ポートを使用して、任意のクライアントでコンテナーとの SSH セッションを開きます。 次の例では、既定の [ssh](https://ss64.com/bash/ssh.html) コマンドを使用しています。

```azurecli-interactive
ssh root@127.0.0.1 -p <port>
```

プロンプトが表示されたら、`yes` と入力して接続を続行します。 パスワードを入力するように求められます。 前に示された `Docker!` を使用します。

```
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
```

認証されると、セッションのようこそ画面が表示されます。

```
  _____
  /  _  \ __________ _________   ____
 /  /_\  \___   /  |  \_  __ \_/ __ \
/    |    \/    /|  |  /|  | \/\  ___/
\____|__  /_____ \____/ |__|    \___  >
        \/      \/                  \/
A P P   S E R V I C E   O N   L I N U X

0e690efa93e2:~#
```

これでコネクタに接続されました。 

[top](https://ss64.com/bash/top.html) コマンドを実行してみます。 プロセスの一覧にアプリのプロセスが表示されます。 次の出力例では、`PID 263` のものです。

```
Mem: 1578756K used, 127032K free, 8744K shrd, 201592K buff, 341348K cached
CPU:   3% usr   3% sys   0% nic  92% idle   0% io   0% irq   0% sirq
Load average: 0.07 0.04 0.08 4/765 45738
  PID  PPID USER     STAT   VSZ %VSZ CPU %CPU COMMAND
    1     0 root     S     1528   0%   0   0% /sbin/init
  235     1 root     S     632m  38%   0   0% PM2 v2.10.3: God Daemon (/root/.pm2)
  263   235 root     S     630m  38%   0   0% node /home/site/wwwroot/app.js
  482   291 root     S     7368   0%   0   0% sshd: root@pts/0
45513   291 root     S     7356   0%   0   0% sshd: root@pts/1
  291     1 root     S     7324   0%   0   0% /usr/sbin/sshd
  490   482 root     S     1540   0%   0   0% -ash
45539 45513 root     S     1540   0%   0   0% -ash
45678 45539 root     R     1536   0%   0   0% top
45733     1 root     Z        0   0%   0   0% [init]
45734     1 root     Z        0   0%   0   0% [init]
45735     1 root     Z        0   0%   0   0% [init]
45736     1 root     Z        0   0%   0   0% [init]
45737     1 root     Z        0   0%   0   0% [init]
45738     1 root     Z        0   0%   0   0% [init]
```

## <a name="next-steps"></a>次の手順

質問や問題は、[Azure フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)に投稿できます。

Web App for Containers について詳しくは、以下をご覧ください。

* [VS Code からの Azure App Service 上での Node.js アプリのリモート デバッグの概要](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [Web App for Containers のカスタム Docker イメージを使用する方法](quickstart-docker-go.md)
* [Azure App Service on Linux で .NET Core を使用する](quickstart-dotnetcore.md)
* [Azure App Service on Linux で Ruby を使用する](quickstart-ruby.md)
* [Azure App Service Web App for Containers の FAQ](app-service-linux-faq.md)
