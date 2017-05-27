---
title: "初めての Python Web アプリを Azure に 5 分で作成する | Microsoft Docs"
description: "初めての Python Hello World を App Service Web アプリに数分でデプロイします。"
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
ms.assetid: 928ee2e5-6143-4c0c-8546-366f5a3d80ce
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: cfowler
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: df34052acc401fb5bb1e3f808c649c0ea0bcf33c
ms.contentlocale: ja-jp
ms.lasthandoff: 05/25/2017


---
# <a name="create-a-python-application-on-web-app"></a>Web アプリでの Python アプリケーションの作成

このクイック スタート チュートリアルでは、Python アプリを開発し、Azure にデプロイする方法について説明します。 Azure App Service を使用してアプリを実行し、その内部に Azure CLI を使用して新しい Web アプリを作成して構成します。 次に、Git を使用して、Python アプリを Azure にデプロイします。

![hello-world-in-browser](media/app-service-web-get-started-python/hello-world-in-browser.png)

以下の手順は、Mac、Windows、または Linux コンピューターを使用して実行できます。 以下の手順全体を、約 5 分で完了できます。

## <a name="prerequisites"></a>前提条件

このサンプルを実行する前に、前提条件となる以下をローカルにインストールします。

1. [Git をダウンロードし、インストールします](https://git-scm.com/)
1. [Python をダウンロードし、インストールします](https://www.python.org/downloads/)
1. [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) をダウンロードし、インストールします

## <a name="download-the-sample"></a>サンプルのダウンロード

Hello World サンプル アプリ リポジトリをローカル コンピューターにクローンします。

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

> [!TIP]
> または、zip ファイルとして[サンプルをダウンロード](https://github.com/Azure-Samples/Python-docs-hello-world/archive/master.zip)し、展開することもできます。

サンプル コードが含まれているディレクトリに移動します。

```bash
cd Python-docs-hello-world
```

## <a name="run-the-app-locally"></a>アプリをローカルで実行する

ターミナル ウィンドウを開き、サンプルの `Python` コマンド ラインを使用してローカルでアプリケーションを実行し、組み込みの Python Web サーバーを起動します。

```bash
python main.py
```

Web ブラウザーを開き、サンプルに移動します。

```bash
http://localhost:5000
```

ページに表示されているサンプル アプリの **Hello World** メッセージが表示されます。

![localhost-hello-world-in-browser](media/app-service-web-get-started-python/localhost-hello-world-in-browser.png)

ターミナル ウィンドウで **Ctrl + C** キーを押して、Web サーバーを終了します。

## <a name="log-in-to-azure"></a>Azure へのログイン

ターミナル ウィンドウで Azure CLI 2.0 を使用して、Azure で Python アプリをホストするために必要なリソースを作成します。 [az login](/cli/azure/#login) コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

```azurecli
az login
```

## <a name="configure-a-deployment-user"></a>デプロイ ユーザーの構成

FTP とローカル Git の場合、デプロイを認証するには、サーバー上で構成されたデプロイ ユーザーが必要です。 デプロイ ユーザーの作成は、1 回だけの構成です。後の手順でユーザー名とパスワードを使用することになるので、書き留めておいてください。

> [!NOTE]
> FTP と、Web アプリへのローカル Git のデプロイには、デプロイ ユーザーが必要です。
> `username` と `password` はアカウント レベルです。そのため、Azure サブスクリプションの資格情報とは異なります。 **これらの資格情報は、1 回だけ作成される必要があります**。
>

アカウント レベルの資格情報を作成するには、[az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) コマンドを使用します。

```azurecli
az appservice web deployment user set --user-name <username> --password <password>
```

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#create) でリソース グループを作成します。 Azure リソース グループとは、Web アプリ、データベース、ストレージ アカウントなどの Azure リソースのデプロイと管理に使用する論理コンテナーです。

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-an-azure-app-service"></a>Azure App Service の作成

[az appservice plan create](/cli/azure/appservice/plan#create) コマンドで、App Service プランを作成します。

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

次の例では、**Free** 価格レベルを使用して、`quickStartPlan` という名前の App Service プランを作成します。

```azurecli
az appservice plan create --name quickStartPlan --resource-group myResourceGroup --sku FREE
```

App Service プランが作成されると、Azure CLI によって、次の例のような情報が表示されます。

```json
{
"appServicePlanName": "quickStartPlan",
"geoRegion": "North Europe",
"id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
"kind": "app",
"location": "North Europe",
"maximumNumberOfWorkers": 1,
"name": "quickStartPlan",
"provisioningState": "Succeeded",
"resourceGroup": "myResourceGroup",
"sku": {
  "capacity": 0,
  "family": "F",
  "name": "F1",
  "size": "F1",
  "tier": "Free"
},
"status": "Ready",
"type": "Microsoft.Web/serverfarms",
}
```

## <a name="create-a-web-app"></a>Web アプリを作成する

App Service プランを作成したので、`quickStartPlan` App Service プラン内に Web アプリを作成します。 Web アプリにより、コードをデプロイするためのホスト領域が取得され、デプロイされたアプリケーションを表示するための URL が提供されます。 Web アプリを作成するには、[az appservice web create](/cli/azure/appservice/web#create) コマンドを使用します。

次のコマンドで、`<app_name>` プレースホルダーを独自の一意のアプリ名に置き換えてください。 `<app_name>` は、Web アプリの既定の DNS サイトとして使用されます。そのため、名前は Azure のすべてのアプリ間で一意である必要があります。 後で、Web アプリをユーザーに公開する前に、任意のカスタム DNS エントリを Web アプリにマップできます。

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan quickStartPlan
```

Web アプリが作成されると、Azure CLI によって次の例のような情報が表示されます。

```json
{
  "clientAffinityEnabled": true,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "<app_name>.azurewebsites.net",
    "<app_name>.scm.azurewebsites.net"
  ],
  "hostNames": [
    "<app_name>.azurewebsites.net"
  ],
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>",
  "kind": "app",
  "location": "North Europe",
  "outboundIpAddresses": "13.69.190.80,13.69.191.239,13.69.186.193,13.69.187.34",
  "resourceGroup": "myResourceGroup",
  "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
  "state": "Running",
  "type": "Microsoft.Web/sites",
}
```

サイトを参照して、新たに作成された Web アプリを表示します。

```bash
http://<app_name>.azurewebsites.net
```

![app-service-web-service-created](media/app-service-web-get-started-python/app-service-web-service-created.png)

これで、Azure に空の新しい Web アプリが作成されました。 Python を使用するように Web アプリを構成し、それにアプリをデプロイしましょう。

## <a name="configure-to-use-python"></a>Python を使用するための構成

Python バージョン `3.4` を使用するように Web アプリを構成するには、[az appservice web config update](/cli/azure/app-service/web/config#update) コマンドを使用します。

> [!TIP]
> この方法で Python バージョンを設定すると、プラットフォームによって用意される既定のコンテナーが使用されます。独自のコンテナーを使用する場合は、[az appservice web config container update](https://docs.microsoft.com/cli/azure/appservice/web/config/container#update) コマンドの CLI リファレンスを参照してください。

```azurecli
az appservice web config update --python-version 3.4 --name <app-name> --resource-group myResourceGroup
```

## <a name="configure-local-git-deployment"></a>ローカル Git デプロイの構成

Web アプリにデプロイするには、FTP、ローカル Git や GitHub、Visual Studio Team Services、Bitbucket など、さまざまな方法があります。

Web アプリへのローカル Git アクセスを構成するには、[az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) コマンドを使用します。

```azurecli
az appservice web source-control config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

次の手順で使用するため、ターミナルからの出力をコピーしておきます。

```bash
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

## <a name="push-to-azure-from-git"></a>Git から Azure へのプッシュ

ローカル Git リポジトリに Azure リモートを追加します。

```bash
git remote add azure <paste-previous-command-output-here>
```

アプリケーションをデプロイするために、Azure リモートにプッシュします。 デプロイ ユーザーの作成時に指定したパスワードを入力するように求めるメッセージが表示されます。

```azurecli
git push azure master
```

デプロイ中、Azure App Service は進行状況について Git と通信します。

```bash
Counting objects: 18, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (16/16), done.
Writing objects: 100% (18/18), 4.31 KiB | 0 bytes/s, done.
Total 18 (delta 4), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '44e74fe7dd'.
remote: Generating deployment script.
remote: Generating deployment script for python Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling python deployment.
remote: KuduSync.NET from: 'D:\home\site\repository' to: 'D:\home\site\wwwroot'
remote: Deleting file: 'hostingstart.html'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'main.py'
remote: Copying file: 'README.md'
remote: Copying file: 'requirements.txt'
remote: Copying file: 'virtualenv_proxy.py'
remote: Copying file: 'web.2.7.config'
remote: Copying file: 'web.3.4.config'
remote: Detected requirements.txt.  You can skip Python specific steps with a .skipPythonDeployment file.
remote: Detecting Python runtime from site configuration
remote: Detected python-3.4
remote: Creating python-3.4 virtual environment.
remote: .................................
remote: Pip install requirements.
remote: Successfully installed Flask click itsdangerous Jinja2 Werkzeug MarkupSafe
remote: Cleaning up...
remote: .
remote: Overwriting web.config with web.3.4.config
remote:         1 file(s) copied.
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>アプリの参照

Web ブラウザーを使用して、デプロイされたアプリケーションを参照します。

```bash
http://<app_name>.azurewebsites.net
```

今回は、Azure App Service Web アプリとして実行されている Python コードを使用して、Hello World メッセージを表示するページが実行されています。

![hello-world-in-browser](media/app-service-web-get-started-python/hello-world-in-browser.png)

## <a name="updating-and-deploying-the-code"></a>コードの更新とデプロイ

ローカルのテキスト エディターを使用して、Python アプリ内の `main.py` ファイルを開き、`return` ステートメントに続く文字列の中のテキストを少し変更します。

```python
return 'Hello, Azure!'
```

変更を Git にコミットし、コード変更を Azure にプッシュします。

```bash
git commit -am "updated output"
git push azure master
```

デプロイが完了したら、「アプリの参照」の手順で開いたブラウザー ウィンドウに戻り、表示を更新します。

![hello-azure-in-browser](media/app-service-web-get-started-python/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>新しい Azure Web アプリを管理する

Azure Portal に移動し、作成したばかりの Web アプリを表示します。

そのためには、[https://portal.azure.com](https://portal.azure.com) にサインインします。

左側のメニューで **[App Services (App Services)]** をクリックした後、Azure Web アプリの名前をクリックします。

![Azure Web アプリへのポータル ナビゲーション](./media/app-service-web-get-started-python/app-service-list.png)

Web アプリの "_ブレード_" (水平方向に開かれるポータル ページ) が表示されます。

既定では、Web アプリのブレードは **[概要]** ページを表示します。 このページでは、アプリの動作状態を見ることができます。 ここでは、参照、停止、開始、再開、削除のような基本的な管理タスクも行うことができます。 ブレードの左側にあるタブは、開くことができるさまざまな構成ページを示しています。

![Azure Portal の App Service ブレード](media/app-service-web-get-started-python/app-service-detail.png)

ブレードのこれらのタブは、Web アプリに追加することができるさまざまな優れた機能を示しています。 次の一覧では、ほんの一部の例を示しています。

* カスタム DNS 名をマップする
* カスタム SSL 証明書をバインドする
* 継続的なデプロイを構成する
* スケールアップとスケールアウトを行う
* ユーザー認証を追加する

**お疲れさまでした。** App Service に初めての Python アプリをデプロイしました。

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Web アプリの CLI スクリプト サンプルを見る](app-service-cli-samples.md)

