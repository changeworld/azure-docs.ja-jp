---
title: "App Service on Linux で Web アプリを作成する方法 | Microsoft Docs"
description: "App Service on Linux の Web アプリ作成ワークフローについて説明します。"
keywords: "Azure App Service, Web アプリ, Linux, OSS"
services: app-service
documentationcenter: 
author: naziml
manager: wpickett
editor: 
ms.assetid: 3a71d10a-a0fe-4d28-af95-03b2860057d5
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: naziml
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8d87315ec97631c7ad3f0163aeaa0abde11b7416


---
# <a name="create-a-web-app-with-app-service-on-linux"></a>App Service on Linux を使用した Web アプリの作成
## <a name="use-the-azure-portal-to-create-your-web-app"></a>Azure Portal を使用した Web アプリの作成
Linux での Web アプリの作成は、次の図のとおり [Azure Portal](https://portal.azure.com) から開始することができます。

![Azure Portal を使用した Web アプリの作成の開始][1]

次に、次の図のように **[作成] ブレード**が開きます。

![[作成] ブレード][2]

1. Web アプリに名前を付けます。
2. 既存のリソース グループを選択するか、新しいリソース グループを作成します  (「[limitations (制限)](app-service-linux-intro.md)」セクションで、使用可能なリージョンを確認してください)。
3. 既存の Azure App Service プランを選択するか、新しいプランを作成します  (「[limitations (制限)](app-service-linux-intro.md)」セクションで、App Service プランの注意事項を確認してください)。
4. 使用するアプリケーション スタックを選択します。 Node.js と PHP のいくつかのバージョンから選択できます。

アプリを作成した後は、次の図のように、[アプリケーションの設定] からアプリケーション スタックを変更できます。

![アプリケーションの設定][3]

## <a name="deploy-your-web-app"></a>Web アプリのデプロイ
管理ポータルから **[デプロイ オプション]** を選択することで、ローカル Git または GitHub リポジトリを使用してアプリケーションをデプロイできるようになります。 残りの手順は Linux 以外での Web アプリと同様です。[ローカル Git のデプロイ](app-service-deploy-local-git.md)に関する GitHub の記事か、[継続的なデプロイ](app-service-continuous-deployment.md)に関する GitHub の記事の手順に従ってください。

サイトへのアプリケーションのアップロードには FTP を使用することもできます。 次の図のように、Web アプリ用の FTP エンドポイントは [診断ログ] セクションから取得できます。

![[診断ログ]][4]

## <a name="next-steps"></a>次のステップ
* [App Service on Linux とは](app-service-linux-intro.md)
* [Web Apps on Linux での Node.js 向け PM2 構成の使用](app-service-linux-using-nodejs-pm2.md)

<!--Image references-->
[1]: ./media/app-service-linux-how-to-create-a-web-app/top-level-create.png
[2]: ./media/app-service-linux-how-to-create-a-web-app/create-blade.png
[3]: ./media/app-service-linux-how-to-create-a-web-app/application-settings-change-stack.png
[4]: ./media/app-service-linux-how-to-create-a-web-app/diagnostic-logs-ftp.png



<!--HONumber=Nov16_HO3-->


