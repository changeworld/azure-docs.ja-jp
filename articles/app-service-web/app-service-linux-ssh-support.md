---
title: "Azure App Service Web Apps on Linux での SSH のサポート | Microsoft Docs"
description: "Azure Web App on Linux で SSH を使用する方法について説明します。"
keywords: "Azure App Service, Web アプリ, Linux, OSS"
services: app-service
documentationcenter: 
author: wesmc7777
manager: erikre
editor: 
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: wesmc
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: feee7a5c91d213a6b0bfdaf264a4da4d9e79cbe7
ms.contentlocale: ja-jp
ms.lasthandoff: 08/28/2017

---
# <a name="ssh-support-for-azure-web-app-on-linux"></a>Azure App Service Web Apps on Linux での SSH のサポート

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

## <a name="overview"></a>概要

[Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) は、ネットワーク サービスを安全に使用するための暗号化ネットワークプロトコルです。 コマンド ラインからシステムにリモートで安全にログインし、管理コマンドをリモートで実行するために最もよく使用されます。

Web App on Linux では、新しい Web アプリのランタイム スタックで使用される組み込みの Docker イメージのそれぞれにアプリ コンテナーへの SSH のサポートを提供しています。 

![ランタイム スタック](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

このトピックで説明するように SSH サーバーをイメージの一部として組み込んで構成することで、カスタム Docker イメージで SSH を使用することもできます。



## <a name="making-a-client-connection"></a>クライアント接続を行う

SSH クライアント接続を行うには、メイン サイトを開始する必要があります。 

次の形式を使用して、Web アプリのソース コントロール管理 (SCM) エンドポイントをブラウザーに貼り付けます。

        https://<your sitename>.scm.azurewebsites.net/webssh/host

既に認証されていない場合、接続するには Azure サブスクリプションで認証する必要があります。

![SSH 接続](./media/app-service-linux-ssh-support/app-service-linux-ssh-connection.png)


## <a name="ssh-support-with-custom-docker-images"></a>カスタム Docker イメージでの SSH サポート

Azure ポータルでのコンテナーとクライアント間の SSH 通信をカスタム Docker イメージでサポートするために、Docker イメージに対して次の手順を実行します。 

これらの手順は、Azure App Service レポジトリに例として示されています ([こちら](https://github.com/Azure-App-Service/node/blob/master/6.9.3/)を参照してください)。

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

2. [sshd_config](http://man.openbsd.org/sshd_config) ファイルを */etc/ssh/* ディレクトリにコピーする [`COPY` 命令](https://docs.docker.com/engine/reference/builder/#copy)を Dockerfile に 追加します。 構成ファイルは、GitHub レポジトリに格納されている Azure App Service の sshd_config ファイルに基づいている必要があります ([こちら](https://github.com/Azure-App-Service/node/blob/master/6.11/sshd_config)を参照してください)。

    > [!NOTE] 
    > *sshd_config* ファイルには次の項目を指定する必要があります。指定がない場合、接続は失敗します。 
    > * `Ciphers` には次の値を少なくとも 1 つ指定する必要あります。`aes128-cbc,3des-cbc,aes256-cbc`
    > * `MACs` には次の値を少なくとも 1 つ指定する必要あります。`hmac-sha1,hmac-sha1-96`

    ```docker
    COPY sshd_config /etc/ssh/
    ```


3. Dockerfile の [`EXPOSE` 命令](https://docs.docker.com/engine/reference/builder/#expose)に、ポート 2222 を含めます。 ルート パスワードはわかっていますが、ポート 2222 はインターネットからアクセスすることはできません。 それはプライベート仮想ネットワークのブリッジ ネットワーク内でコンテナーのみがアクセスできる内部専用ポートです。

    ```docker
    EXPOSE 2222 80
    ```

4. ssh サービスが開始されることを確認します。 [こちら](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh)の例では、*/bin* ディレクトリのシェル スクリプトを使用しています。

    ```bash
    #!/bin/bash
    service ssh start
    ```

    Dockerfile では、[`CMD` 命令](https://docs.docker.com/engine/reference/builder/#cmd)を使用してスクリプトを実行します。

    ```docker
    COPY init_container.sh /bin/
      ...
    RUN chmod 755 /bin/init_container.sh 
      ...       
    CMD ["/bin/init_container.sh"]
    ```



## <a name="next-steps"></a>次のステップ
Web App on Linux の詳細については、次のリンクを参照してください。 質問や問題は、[フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)に投稿できます。

* [Azure Web App on Linux 向けにカスタム Docker イメージを使用する方法](app-service-linux-using-custom-docker-image.md)
* [Azure Web App on Linuxで Node.js の PM2 構成を使用する](app-service-linux-using-nodejs-pm2.md)
* [Azure Web App on Linux で .NET Core を使用する](app-service-linux-using-dotnetcore.md)
* [Azure Web App on Linuxで Ruby を使用する](app-service-linux-ruby-get-started.md)
* [Azure App Service Web App on Linux の FAQ](app-service-linux-faq.md)


