---
title: "静的な HTML Web アプリを Azure に 5 分で作成する | Microsoft Docs"
description: "サンプル アプリをデプロイして、App Service での Web アプリの実行がいかに簡単であるかを説明します。"
services: app-service\web
documentationcenter: 
author: rick-anderson
manager: wpickett
editor: 
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/08/2017
ms.author: riande
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 895906e1ab4bc50093ed3b18f043c3dd515ca054
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---
# <a name="create-a-static-html-web-app-in-azure-in-five-minutes"></a>静的な HTML Web アプリを Azure に 5 分で作成する

このクイック スタートでは、基本的な HTML+CSS サイトを Azure にデプロイする方法について説明します。 [Azure App Service プラン](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview)を使用してアプリを実行し、[Azure CLI](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli) を使用してその内部に Web アプリを作成します。 Git を使用して、アプリを Azure にデプロイします。 前提条件がインストールされた後、このチュートリアルを完了するには約 5 分かかります。

![hello-world-in-browser](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

## <a name="prerequisites"></a>前提条件

このサンプルを作成する前に、次のコンポーネントをダウンロードしてインストールします。

- [Git](https://git-scm.com/)
- [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>サンプルのダウンロード

ターミナル ウィンドウで、サンプル アプリ リポジトリをローカル コンピューターに複製します。

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```

## <a name="view-the-html"></a>HTML を表示する

サンプル HTML が含まれているディレクトリに移動します。 ブラウザーで *index.html* ファイルを開きます。

![hello-world-in-browser](media/app-service-web-get-started-html/hello-world-in-browser.png)

[!INCLUDE [login-to-azure](../../includes/login-to-azure.md)] 
[!INCLUDE [configure-deployment-user](../../includes/configure-deployment-user.md)] 

[!INCLUDE [app-service-web-quickstart1](../../includes/app-service-web-quickstart1.md)] 

`quickStartPlan` App Service プランで [Web アプリ](app-service-web-overview.md)を作成します。 Web アプリによってコード用のホスト領域が提供され、デプロイされたアプリを表示するための URL が提供されます。

[!INCLUDE [app-service-web-quickstart2](../../includes/app-service-web-quickstart2.md)] 

このページは、Azure App Service Web アプリとして実行されています。

![hello-world-in-browser](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

## <a name="update-and-redeploy-the-app"></a>アプリを更新して再デプロイする

*index.html* ファイルを開きます。 マークアップを変更します。 たとえば、`Hello world!` を `Hello Azure!` に変更します

Git で変更をコミットしてから、コード変更を Azure にプッシュします。

```bash
git commit -am "updated HTML"
git push azure master
```

デプロイが完了したら、ブラウザーを更新して変更を表示します。

[!INCLUDE [manage-azure-web-app](../../includes/manage-azure-web-app.md)]


[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>次のステップ

- [Web アプリの CLI スクリプト](app-service-cli-samples.md) サンプルを見ます。
- [カスタム ドメイン名](app-service-web-tutorial-custom-domain.md) (contoso.com など) を [App Service アプリにマップする](app-service-web-tutorial-custom-domain.md)方法を学習します。
