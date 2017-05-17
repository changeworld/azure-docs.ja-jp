---
title: "Azure Web App on Linux の概要 | Microsoft Docs"
description: "Azure Web App on Linux について説明します。"
keywords: Azure App Service, Linux, OSS
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 036e5691ecc435da54f381563b5d798f065bfb7f
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---
# <a name="introduction-to-azure-web-app-on-linux"></a>Azure Web App on Linux の概要

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

## <a name="overview"></a>概要
Web App on Linux を使用して、サポートされているアプリケーション スタック向けに Web アプリを Linux 上でネイティブにホストすることができます。 次のセクションは、現在サポートされているアプリケーション スタックの一覧です。 

## <a name="features"></a>機能
現時点では、Web App on Linux は、次のアプリケーション スタックをサポートしています。

* Node.js
    * 4.4.
    * 4.5
    * 6.2
    * 6.6
    * 6.9
* PHP
    * 5.6
    * 7.0
* .NET Core
    * 1.0
    * 1.1
* Ruby
    * 2.3

顧客は次のものを使用してアプリケーションをデプロイすることができます。

* FTP
* ローカル Git
* GitHub
* Bitbucket

アプリケーションのスケーリング方法は次のとおりです。

* App Service プランのレベルを変更することで、Web アプリのスケールアップとスケールダウンを実行できます。
* SKU の範囲内で、アプリケーションをスケールアウトして、複数のアプリ インスタンスを実行することができます。

Kudu の場合、基本的な機能の一部を使用できます。

* 環境
* デプロイメント
* 基本的なコンソール
* SSH

開発運用の場合:

* ステージング環境
* DockerHub CI/CD

## <a name="limitations"></a>制限事項
Azure Portal では、Web App on Linux で現在使用できる機能のみが表示され、残りは非表示となります。 さらに機能を有効にすると、その機能がポータルに表示されます。

仮想ネットワーク統合、Azure Active Directory/サード パーティの認証、Kudu サイト拡張機能などの機能は、まだ利用できません。 これらの機能が利用可能になったら、ドキュメントとブログを更新し、変更についてお知らせします。

このパブリック プレビューは現在、次のリージョンでのみご利用いただけます。

* 米国西部
* 西ヨーロッパ 
* 東南アジア
* オーストラリア東部

Web Apps on Linux は専用 App Service プランでのみサポートされており、Free レベルまたは Shared レベルは存在しません。 また、標準の Web アプリと Linux 向け Web アプリの App Service プランは相互に排他的であり、Linux 向けでない App Service プラン内で Linux 向け Web アプリを作成することはできません。

Web Apps on Linux は、Linux 向けでない Web アプリが同じリージョンにないリソース グループ内で作成される必要があります。

## <a name="next-steps"></a>次のステップ
App Service on Linux の使用を開始するには、次のリンクを参照してください。 質問や問題は、[フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)に投稿できます。

* [Azure Web App on Linux で Web Apps を作成する](app-service-linux-how-to-create-web-app.md)
* [Azure Web App on Linux 向けにカスタム Docker イメージを使用する方法](app-service-linux-using-custom-docker-image.md)
* [Azure Web App on Linuxで Node.js の PM2 構成を使用する](app-service-linux-using-nodejs-pm2.md)
* [Azure App Service Web Apps on Linux で .NET Core を使用する](app-service-linux-using-dotnetcore.md)
* [Azure App Service Web Apps on Linux で Ruby を使用する](app-service-linux-ruby-get-started.md)
* [Azure App Service Web App on Linux の FAQ](app-service-linux-faq.md)
* [Azure Web App on Linux での SSH のサポート](./app-service-linux-ssh-support.md)
* [Azure App Service でステージング環境を設定する](./web-sites-staged-publishing.md)
* [Azure Web App on Linux での Docker Hub の継続的なデプロイ](./app-service-linux-ci-cd.md)


