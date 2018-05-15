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
ms.openlocfilehash: cf27e852f5ec9b7e12b0c678e9940596bc57b385
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>Azure App Service on Linux での SSH のサポート

[Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) は、ネットワーク サービスを安全に使用するための暗号化ネットワークプロトコルです。 コマンド ラインからシステムにリモートで安全にログインし、管理コマンドをリモートで実行するために最もよく使用されます。

App Service on Linux では、新しい Web アプリのランタイム スタックで使用される各組み込み Docker イメージで、アプリ コンテナーへの SSH をサポートしています。

![ランタイム スタック](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

この記事で説明するように SSH サーバーをイメージの一部として組み込んで構成することで、カスタム Docker イメージで SSH を使うこともできます。

> [!NOTE] 
> SSH、SFTP、または Visual Studio Code (ライブ デバッグ Node.js アプリの場合) を使用して、ローカル開発マシンからアプリ コンテナーに直接接続することもできます。 詳細については、[Linux での App Service のリモート デバッグと SSH](https://aka.ms/linux-debug) に関するページをご覧ください。
>

## <a name="making-a-client-connection"></a>クライアント接続を行う

SSH クライアント接続を行うには、メイン サイトを開始する必要があります。

次の形式を使用して、Web アプリのソース コントロール管理 (SCM) エンドポイントをブラウザーに貼り付けます。

```bash
https://<your sitename>.scm.azurewebsites.net/webssh/host
```

既に認証されていない場合、接続するには Azure サブスクリプションで認証する必要があります。

![SSH 接続](./media/app-service-linux-ssh-support/app-service-linux-ssh-connection.png)

## <a name="ssh-support-with-custom-docker-images"></a>カスタム Docker イメージでの SSH サポート

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

## <a name="next-steps"></a>次の手順

質問や問題は、[Azure フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)に投稿できます。

Web App for Containers について詳しくは、以下をご覧ください。

* [Web App for Containers のカスタム Docker イメージを使用する方法](quickstart-docker-go.md)
* [Azure App Service on Linux で .NET Core を使用する](quickstart-dotnetcore.md)
* [Azure App Service on Linux で Ruby を使用する](quickstart-ruby.md)
* [Azure App Service Web App for Containers の FAQ](app-service-linux-faq.md)