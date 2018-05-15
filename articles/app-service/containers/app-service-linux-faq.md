---
title: Azure App Service on Linux の FAQ | Microsoft Docs
description: Azure App Service on Linux の FAQ
keywords: Azure App Service, Web アプリ, FAQ, Linux, OSS
services: app-service
documentationCenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: aelnably;wesmc
ms.openlocfilehash: 8d25c70a0e5db92bca6f3970049a2e1325fe124b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="azure-app-service-on-linux-faq"></a>Azure App Service on Linux の FAQ

App Service on Linux のリリースでは、機能の追加とプラットフォームの品質向上に取り組んでいます。 この記事では、最近お客様からお問い合わせのあったご質問に回答しています。

ご質問がある場合は、この記事の最後にあるコメント欄をご利用ください。できるだけ早く回答いたします。

## <a name="built-in-images"></a>組み込まれているイメージ

**プラットフォームに用意されている組み込みの Docker コンテナーをフォークしたいと思っています。これらのファイルはどこで入手できますか。**

すべての Docker ファイルは [GitHub](https://github.com/azure-app-service) にあります。 すべての Docker コンテナーは [Docker Hub](https://hub.docker.com/u/appsvc/) にあります。

**ランタイム スタックを構成する場合、[スタートアップ ファイル] セクションではどのような値が有効ですか。**

Node.js の場合は、PM2 構成ファイルまたはスクリプト ファイルを指定します。 .NET Core の場合は、コンパイル済みの DLL 名を指定します。 Ruby の場合は、アプリの初期化に使用する Ruby スクリプトを指定できます。

## <a name="management"></a>管理

**Azure Portal で [再起動] ボタンを押すと何が起こりますか。**

このアクションは Docker の再起動と同じです。

**アプリ コンテナーの仮想マシン (VM) への接続に Secure Shell (SSH) を使用できますか。**

はい、ソース管理 (SCM) サイトからご利用いただけます。

> [!NOTE] 
> SSH、SFTP、または Visual Studio Code (ライブ デバッグ Node.js アプリの場合) を使用して、ローカル開発マシンからアプリ コンテナーに直接接続することもできます。 詳細については、[Linux での App Service のリモート デバッグと SSH](https://aka.ms/linux-debug) に関するページをご覧ください。
>

**SDK または Azure Resource Manager テンプレートから Linux App Service プランを作成するにはどうすればよいですか。**

アプリ サービスの**予約済み**フィールドを *true* に設定する必要があります。

## <a name="continuous-integration-and-deployment"></a>継続的インテグレーションとデプロイ

**自分の Web アプリでは、Docker Hub 上のイメージを更新した後も、古い Docker コンテナー イメージを引き続き使用しています。カスタム コンテナーの継続的な統合およびデプロイをサポートしていますか。**

Azure Container Registry または DockerHub イメージの継続的インテグレーションと継続的デプロイをセットアップするには、「[Azure Web App for Containers での継続的デプロイ](./app-service-linux-ci-cd.md)」をご覧ください。 プライベート レジストリでは、Web アプリを停止してから起動することでコンテナーを更新できます。 または、ダミー アプリケーション設定を変更または追加して、コンテナーを強制的に更新できます。

**ステージング環境はサポートしていますか。**

はい。

**"*Web デプロイ*" を使用して Web アプリをデプロイすることはできますか。**

はい。`WEBSITE_WEBDEPLOY_USE_SCM` というアプリ設定を *false* に設定する必要があります。

**Linux Web アプリを使用すると、アプリケーションの Git デプロイが失敗します。この問題を回避する方法はありますか。**

Linux Web アプリに対して Git デプロイが失敗する場合は、以下の代替オプションを選択してアプリケーション コードをデプロイできます。

- 継続的配信 (プレビュー) 機能を使用する: アプリのソース コードを、Team Services の Git リポジトリか GitHub リポジトリに格納して、Azure の継続的配信を使用できます。 詳細については、[Linux Web アプリに対して継続的配信を構成する方法](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/)に関するページをご覧ください。

- [ZIP デプロイ API](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file) を使用する: この API を使用するには、[Web アプリに SSH で接続](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-ssh-support#making-a-client-connection)し、コードをデプロイするフォルダーに移動します。 次のコマンドレットを実行します。

   ```
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   `curl` コマンドが見つからないというエラーが表示される場合は、`curl` コマンドを実行する前に `apt-get install curl` を使用して curl をインストールしてください。

## <a name="language-support"></a>言語のサポート

**WebSocket を Node.js アプリケーションで使用したいと考えています。特別な設定や構成が必要でしょうか。**

はい、サーバー側の Node.js コードで `perMessageDeflate` を無効にします。 たとえば、socket.io を使用している場合、次の操作を行います。
```
var io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**コンパイルされていない .NET Core アプリはサポートされていますか。**

はい。

**PHP アプリの依存関係マネージャーとして Composer はサポートされていますか。**

はい。 Git のデプロイ中に、Kudu は (composer.lock ファイルの存在により) PHP アプリケーションをデプロイしていることを検出し、その後 Kudu は Composer のインストールを自動的にトリガーします。

## <a name="custom-containers"></a>カスタム コンテナー

**自分が所有するカスタム コンテナーを使用しています。プラットフォームを SMB 共有の `/home/` ディレクトリにマウントさせたいと考えています。**

`WEBSITES_ENABLE_APP_SERVICE_STORAGE` アプリ設定を *true* に設定するか、またはアプリ設定を完全に削除することで、マウントできます。 これにより、プラットフォームのストレージの変更時に、コンテナーの再起動が行われることに注意してください。 

>[!NOTE]
>`WEBSITES_ENABLE_APP_SERVICE_STORAGE` 設定が *false* の場合、`/home/` ディレクトリはスケール インスタンス間で共有されず、このディレクトリに書き込まれたファイルは再起動後には保持されません。

**カスタム コンテナーの起動に時間がかかり、起動が終了する前にプラットフォームがコンテナーを再起動します。**

プラットフォームがコンテナーを再起動する前の待機時間を構成できます。 これを行うには、`WEBSITES_CONTAINER_START_TIME_LIMIT` アプリ設定を目的の値に設定します。 既定値は 230 秒であり、最大値は 600 秒です。

**プライベート レジストリ サーバーの URL の形式は何ですか。**

`http://` または `https://` を含む完全なレジストリ URL を入力します。

**プライベート レジストリ オプションのイメージ名の形式は何ですか。**

プライベート レジストリ の URL を含む完全なイメージ名を追加します (例: myacr.azurecr.io/dotnet:latest)。 カスタム ポートを使用するイメージ名は、[ポータル経由で入力することはできません](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650)。 `docker-custom-image-name` を設定するには、[`az` コマンドライン ツール](https://docs.microsoft.com/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set)を使用します。

**カスタム コンテナー イメージで複数のポートを公開できますか。**

複数のポートの公開は現在サポートされていません。

**自分が所有するストレージを持ち込むことはできますか?**

独自のストレージの持ち込みは現在サポートされていません。

**SCM サイトからカスタム コンテナーのファイル システムや実行中のプロセスを参照できないのはなぜですか。**

SCM サイトは別のコンテナーで実行されています。 アプリ コンテナーのファイル システムや実行中のプロセスをチェックすることはできません。

**カスタム コンテナーがポート 80 以外のポートをリッスンしています。そのポートに要求をルーティングするようにアプリを構成するにはどうすればよいですか。**

ポートの自動検出機能があります。 また、*WEBSITES_PORT* というアプリ設定を指定して、必要なポート番号の値を設定することもできます。 以前、プラットフォームでは *PORT* アプリ設定を使用していました。 このアプリ設定を廃止し、*WEBSITES_PORT* のみを使用する予定です。

**カスタム コンテナーに HTTPS を実装する必要がありますか。**

いいえ、共有フロントエンドでの HTTPS の終了はプラットフォームが処理します。

## <a name="pricing-and-sla"></a>料金と SLA

**一般的にサービスが利用できる現在の料金を教えてください。**

アプリの実行時間に対して、通常の Azure App Service の料金が課金されます。

## <a name="other-questions"></a>どの他の質問

**アプリケーションの設定名でサポートされる文字は何ですか。**

アプリケーション設定では、英字 (A ～ Z、a ～ z)、数字 (0 ～ 9)、およびアンダースコア (_) のみご利用いただけます。

**新機能はどこでリクエストできますか。**

[Web Apps フィードバック フォーラム](https://aka.ms/webapps-uservoice)でご自分のアイデアを送信できます。 アイデアのタイトルに "[Linux]" を追加してください。

## <a name="next-steps"></a>次の手順

* [Azure App Service on Linux とは](app-service-linux-intro.md)
* [Azure App Service でステージング環境を設定する](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Web App for Containers での継続的デプロイ](./app-service-linux-ci-cd.md)
