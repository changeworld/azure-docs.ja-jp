---
title: "Linux で実行される Azure Web アプリの作成 | Microsoft Docs"
description: "Azure Web App on Linux の Web アプリ作成ワークフローについて説明します。"
keywords: "Azure App Service, Web アプリ, Linux, OSS"
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: 3a71d10a-a0fe-4d28-af95-03b2860057d5
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: 49091d4a85bed23927850f9c0bbc5ea8b6e8c9e1
ms.contentlocale: ja-jp
ms.lasthandoff: 06/08/2017


---
# <a name="create-an-azure-web-app-running-on-linux"></a>Linux で実行される Azure Web アプリの作成

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]


## <a name="use-the-azure-portal-to-create-your-web-app"></a>Azure Portal を使用した Web アプリの作成
Linux での Web アプリの作成は、次の図のとおり [Azure Portal](https://portal.azure.com) から開始することができます。

![Azure Portal を使用した Web アプリの作成の開始][1]

次に、次の図のように **[作成] ブレード**が開きます。

![[作成] ブレード][2]

1. Web アプリに名前を付けます。
2. 既存のリソース グループを選択するか、新しいリソース グループを作成します  (「[limitations (制限)](app-service-linux-intro.md)」セクションで、使用可能なリージョンを確認してください)。
3. 既存の Azure App Service プランを選択するか、新しいプランを作成します  (「[limitations (制限)](app-service-linux-intro.md)」セクションで、App Service プランの注意事項を確認してください)。
4. 使用するアプリケーション スタックを選択します。 Node.js、PHP、.Net Core、Ruby のいくつかのバージョンから選択できます。

アプリを作成した後は、次の図のように、[アプリケーションの設定] からアプリケーション スタックを変更できます。

![アプリケーションの設定][3]

## <a name="deploy-your-web-app"></a>Web アプリのデプロイ
管理ポータルから**デプロイ オプション**を選択することで、ローカル Git または GitHub リポジトリを使用してアプリケーションをデプロイできるようになります。 残りの手順は、Linux 以外の Web アプリの手順と同じです。 [ローカル Git のデプロイ](app-service-deploy-local-git.md)に関する記事または[継続的なデプロイ](app-service-continuous-deployment.md)に関する記事の手順に従って、アプリをデプロイすることができます。

サイトへのアプリケーションのアップロードには FTP を使用することもできます。 次の図のように、Web アプリ用の FTP エンドポイントは [診断ログ] セクションから取得できます。

![[診断ログ]][4]

## <a name="next-steps"></a>次のステップ
* [Azure Web App on Linux とは](app-service-linux-intro.md)
* [Azure Web App on Linuxで Node.js の PM2 構成を使用する](app-service-linux-using-nodejs-pm2.md)
* [Azure App Service Web Apps on Linux で Ruby を使用する](app-service-linux-ruby-get-started.md)
* [Azure App Service Web App on Linux の FAQ](app-service-linux-faq.md)

<!--Image references-->
[1]: ./media/app-service-linux-how-to-create-a-web-app/top-level-create.png
[2]: ./media/app-service-linux-how-to-create-a-web-app/create-blade.png
[3]: ./media/app-service-linux-how-to-create-a-web-app/application-settings-change-stack.png
[4]: ./media/app-service-linux-how-to-create-a-web-app/diagnostic-logs-ftp.png

