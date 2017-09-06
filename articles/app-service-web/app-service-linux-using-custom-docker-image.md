---
title: "Azure Web App on Linux 向けにカスタム Docker イメージを使用する方法 | Microsoft Docs"
description: "Azure Web App on Linux 向けにカスタム Docker イメージを使用する方法。"
keywords: "Azure App Service, Web アプリ, Linux, Docker, コンテナー"
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 1458217a31c4781b28877c030a665f5b22819e13
ms.contentlocale: ja-jp
ms.lasthandoff: 08/28/2017

---

# <a name="using-a-custom-docker-image-for-azure-web-app-on-linux"></a>Azure Web App on Linux 向けのカスタム Docker イメージを使用する #

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]


App Service は、事前定義済みのアプリケーション スタックを Linux 上で提供し、PHP 7.0 や Node.js 4.5 などの特定のバージョンをサポートします。 App Service on Linux では、これらの構成済みのアプリケーション スタックをホストするために Docker コンテナーを使用します。 まだ Azure で定義されていないアプリケーション スタックに Web アプリをデプロイする場合にも、カスタム Docker イメージを使用できます。 カスタム Docker イメージは、パブリック Docker リポジトリとプライベート Docker リポジトリのどちらでもホストできます。


## <a name="how-to-set-a-custom-docker-image-for-a-web-app"></a>Web アプリのカスタム Docker イメージを設定する方法
カスタム Docker イメージは、新規の Web アプリと既存の Web アプリのどちらにでも設定できます。 [Azure Portal](https://portal.azure.com/#create/Microsoft.AppSvcLinux) を使用して Linux で Web アプリを作成する場合、**[コンテナーの構成]** をクリックしてカスタム Docker イメージを設定します。

![Linux の新しい Web アプリのカスタム Docker イメージ][1]


## <a name="how-to-use-a-custom-docker-image-from-docker-hub"></a>Docker Hub からカスタム Docker イメージを使用する方法 ##
Docker Hub からカスタム Docker イメージを使用するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) で Linux の Web アプリを特定し、**[設定]** で **[Docker コンテナー]** をクリックします。

2.  **[イメージ ソース]** として **[Docker Hub]** を選択し、**[パブリック]** または **[プライベート]** をクリックし、**イメージとオプションのタグ名**を入力します (例: `node:4.5`)。 **[スタートアップ コマンド]** は、Docker イメージ ファイル内の定義に基づいて自動的に設定されますが、独自のコマンドを設定することもできます。  

    ![Docker Hub パブリック リポジトリ イメージの構成][2]

    イメージがプライベート リポジトリ由来の場合は、さらに Docker Hub の資格情報をプライベート Docker Hub リポジトリの **[ログイン ユーザー名]** と **[パスワード]** として入力する必要があります。

    ![Docker Hub プライベート リポジトリ イメージの構成][3]

3. コンテナーを構成したら、**[保存]** をクリックします。

## <a name="how-to-use-a-docker-image-from-a-private-image-registry"></a>プライベート イメージ レジストリから Docker イメージを使用する方法 ##
プライベート イメージ レジストリからカスタム Docker イメージを使用するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) で Linux の Web アプリを特定し、**[設定]** で **[Docker コンテナー]** をクリックします。

2.  **[イメージのソース]** として **[プライベート レジストリ]** をクリックします。 資格情報 (**[ログイン ユーザー名]** および **[パスワード]**) と共に、**[イメージとオプションのタグ]** とプライベート レジストリの **[サーバー URL]** を入力します。 **[保存]**をクリックします。

    ![プライベート レジストリからの Docker イメージの構成][4]


## <a name="how-to-set-the-port-used-by-your-docker-image"></a>Docker イメージで使用されるポートを設定する方法 ##

Web アプリ向けにカスタム Docker イメージを使用する場合、Dockerfile 内で `WEBSITES_PORT` 環境変数を使用することができます。この環境変数は、生成されたコンテナーに追加されます。 Ruby アプリケーションの場合は、次の Docker ファイルの例を検討してみてください。

    FROM ruby:2.2.0
    RUN mkdir /app
    WORKDIR /app
    ADD . /app
    RUN bundle install
    CMD bundle exec puma config.ru -p WEBSITES_PORT -e production

コマンドの最後の行で、実行時に WEBSITES_PORT 環境変数が渡されることが確認できます。 コマンドでは大文字小文字の区別が重要ですので注意してください。

以前はプラットフォームが `PORT` アプリ設定を使用していましたが、このアプリ設定の使用は止めて、`WEBSITES_PORT` だけを使用するようにする変更を予定しています。

他のユーザーによって作成された既存の Docker イメージを使用する場合は、アプリケーション用のポート 80 以外のポートを指定する必要がある場合があります。 ポートを構成するには、次に示す値を使用して、`WEBSITES_PORT` というアプリケーション設定を追加します。

![カスタム Docker イメージの PORT アプリ設定の構成][6]

## <a name="how-to-set-the-startup-time-for-your-docker-image"></a>Docker イメージの起動時間を設定する方法 ##

既定では、コンテナーが 230 秒より前に起動しない場合、プラットフォームがコンテナーを再起動します。 カスタム Docker イメージの起動時間が 230 秒を超える場合は、`WEBSITES_CONTAINER_START_TIME_LIMIT` アプリ設定を使用できます。これによって、プラットフォームは、コンテナーを再起動する前に、指定された秒数の間、コンテナーを実行し続けることができます。 既定値は 230 秒であり、最大許容値は 600 秒です。

## <a name="how-to-unmount-the-platform-provided-storage"></a>プラットフォームによって提供される記憶域をマウント解除する方法 ##

既定では、プラットフォームは、永続的な記憶域共有を `\home\` ディレクトリにマウントします。 コンテナー イメージで永続的な共有が必要ない場合は、`WEBSITES_ENABLE_APP_SERVICE_STORAGE` アプリ設定を `false` に設定することで、その記憶域のマウントを無効にすることができます。 無効にした場合でも、SCM サイトからその記憶域にアクセスでき、すべての Docker ログ (有効になっている場合) は、プラットフォームによって生成されるログ ファイルに書き込まれます。

## <a name="how-to-switch-back-to-using-a-built-in-image"></a>組み込みイメージを使用するように切り替える方法 ##

カスタム イメージの使用から組み込みイメージの使用に切り替えるには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) で Linux の Web アプリを特定し、**[設定]** で **[App Service]** をクリックします。

2. 組み込みイメージで使用する **[ランタイム スタック]** を選択し、**[保存]** をクリックします。 

![組み込み Docker イメージの構成][5]


## <a name="troubleshooting"></a>トラブルシューティング ##

カスタム Docker イメージを使用してアプリケーションを起動できない場合は、LogFiles ディレクトリで Docker のログを確認してください。 このディレクトリには、SCM サイトまたは FTP 経由でアクセスできます。
コンテナーから `stdout` および `stderr` をログ記録するには、**[診断ログ]** で **[Docker Container ログ]** を有効にする必要があります。

![ログ記録の有効化][8]

![Kudu を使用した Docker のログの表示][7]

SCM サイトには、**[開発ツール]** メニューの **[Advanced Tools]** からアクセスできます。

## <a name="next-steps"></a>次のステップ ##

Web App on Linux の使用を開始するには、次のリンクを参照してください。   

* [App Service on Linux の概要](./app-service-linux-intro.md)
* [Azure Web App on Linuxで Node.js の PM2 構成を使用する](./app-service-linux-using-nodejs-pm2.md)
* [Azure App Service Web App on Linux の FAQ](app-service-linux-faq.md)

質問や問題は、[フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)に投稿してください。


<!--Image references-->
[1]: ./media/app-service-linux-using-custom-docker-image/new-configure-container.png
[2]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-dockerhub-public.png
[3]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-dockerhub-private.png
[4]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-privateregistry.png
[5]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-builtin.png
[6]: ./media/app-service-linux-using-custom-docker-image/setting-port.png
[7]: ./media/app-service-linux-using-custom-docker-image/kudu-docker-logs.png
[8]: ./media/app-service-linux-using-custom-docker-image/logging.png

