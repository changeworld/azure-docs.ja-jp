---
title: Linux での Python アプリの作成 - Azure App Service | Microsoft Docs
description: Azure App Service on Linux で、初めての Python の Hello World アプリを数分でデプロイします。
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/09/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 3af8f57cab6e420a7c7515bdf32b7ef382441cf9
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53633149"
---
# <a name="create-a-python-app-in-azure-app-service-on-linux-preview"></a>Azure App Service on Linux で Python アプリを作成する (プレビュー)

[App Service on Linux](app-service-linux-intro.md) は、Linux オペレーティング システムを使用する、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供します。 このクイック スタートでは、[Azure CLI](/cli/azure/install-azure-cli)を使用して、 App Service on Linux の組み込み Python イメージ (プレビュー) の上に Python アプリをデプロイする方法を示します。

この記事の手順は、Mac、Windows、または Linux コンピューターを使って実行できます。

![Azure で実行されるサンプル アプリ](media/quickstart-python/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、以下が必要です。

* <a href="https://www.python.org/downloads/" target="_blank">Python 3.7 をインストールする</a>
* <a href="https://git-scm.com/" target="_blank">Git をインストールする</a>

## <a name="download-the-sample"></a>サンプルのダウンロード

ターミナル ウィンドウで、次のコマンドを実行して、ローカル コンピューターにサンプル アプリケーションを複製し、サンプル コードのあるディレクトリに移動します。

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
cd python-docs-hello-world
```

## <a name="run-the-app-locally"></a>アプリをローカルで実行する

アプリケーションをローカルで実行すると、アプリケーションを Azure にデプロイするとどう表示されるかを把握できます。 ターミナル ウィンドウを開き、次のコマンドを使用して必要な依存関係をインストールし、組み込みの開発サーバーを起動します。 

```bash
# In Bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
FLASK_APP=application.py flask run

# In PowerShell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

Web ブラウザーを開き、`http://localhost:5000/` のサンプル アプリに移動します。

サンプル アプリケーションから "**Hello World!**"  というメッセージがページに表示されます。

![ローカルで実行されるサンプル アプリ](media/quickstart-python/hello-world-in-browser.png)

ターミナル ウィンドウで **Ctrl + C** キーを押して、Web サーバーを終了します。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Web アプリを作成する

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

サイトを参照して、お客様が新しく作成した組み込みのイメージによるアプリを確認します。 _&lt;app name>_ は、アプリの名前に置き換えます。

```bash
http://<app_name>.azurewebsites.net
```

新しいアプリは次のようになります。

![空のアプリ ページ](media/quickstart-php/app-service-web-service-created.png)

[!INCLUDE [Push to Azure](../../../includes/app-service-web-git-push-to-azure.md)] 

```bash
Counting objects: 42, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (39/39), done.
Writing objects: 100% (42/42), 9.43 KiB | 0 bytes/s, done.
Total 42 (delta 15), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'c40efbb40e'.
remote: Generating deployment script.
remote: Generating deployment script for python Web Site
.
.
.
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://user2234@cephalin-python.scm.azurewebsites.net/cephalin-python.git
 * [new branch]      master -> master
 ```

## <a name="browse-to-the-app"></a>アプリの参照

Web ブラウザーを使用して、デプロイされたアプリケーションを参照します。

```bash
http://<app_name>.azurewebsites.net
```

App Service on Linux で組み込みのイメージを使用して Python サンプル コードが実行されています。

![Azure で実行されるサンプル アプリ](media/quickstart-python/hello-world-in-browser.png)

**お疲れさまでした。** App Service on Linux に初めての Python アプリをデプロイしました。

## <a name="update-locally-and-redeploy-the-code"></a>コードをローカルで更新して再デプロイする

ローカル リポジトリで `application.py` ファイルを開き、テキストの最後の行を少し変更します。

```python
return "Hello Azure!"
```

Git で変更をコミットしてから、コード変更を Azure にプッシュします。

```bash
git commit -am "updated output"
git push azure master
```

デプロイが完了したら、「**アプリの参照**」の手順で開いたブラウザー ウィンドウに戻り、ページを更新します。

![Azure で実行される更新済みのサンプル アプリ](media/quickstart-python/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>新しい Azure アプリの管理

<a href="https://portal.azure.com" target="_blank">Azure portal</a> に移動し、お客様が作成したアプリを管理します。

左側のメニューで **[App Services]** をクリックしてから、お客様の Azure アプリの名前をクリックします。

![Azure アプリへのポータル ナビゲーション](./media/quickstart-python/app-service-list.png)

お客様のアプリの [概要] ページを確認します。 ここでは、参照、停止、開始、再開、削除のような基本的な管理タスクを行うことができます。

![Azure Portal の [App Service] ページ](media/quickstart-python/app-service-detail.png)

左側のメニューは、アプリを構成するためのさまざまなページを示しています。 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>次の手順

App Service on Linux の組み込み Python イメージは現在プレビュー段階であり、アプリを開始するために使用するコマンドをカスタマイズすることができます。 代わりにカスタム コンテナーを使用して運用環境向け Python アプリを作成することもできます。

> [!div class="nextstepaction"]
> [Python と PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [カスタム スタートアップ コマンドを構成する](how-to-configure-python.md#custom-startup-command)

> [!div class="nextstepaction"]
> [トラブルシューティング](how-to-configure-python.md#troubleshooting)

> [!div class="nextstepaction"]
> [カスタム イメージを使用する](tutorial-custom-docker-image.md)
