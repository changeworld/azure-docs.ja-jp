---
title: GitHub アクションからのカスタム コンテナー CI/CD
description: GitHub アクションを使用してカスタム Linux コンテナーを CI/CD パイプラインから App Service にデプロイする方法について説明します。
ms.devlang: na
ms.topic: article
ms.date: 10/03/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: github-actions-azure
ms.openlocfilehash: 3a5e319115c124551c05f2ac5aa393ba19596d0d
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893358"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>GitHub Actions を使用した App Service へのカスタム コンテナーのデプロイ

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) を使用すると、自動化されたソフトウェア開発ワークフローを柔軟に構築できます。 [Azure Web Deploy アクション](https://github.com/Azure/webapps-deploy)を使用すれば、カスタム コンテナーを [App Service ](overview.md) にデプロイするワークフローを、GitHub Actions を使用して自動化できます。

ワークフローは、お使いのリポジトリの `/.github/workflows/` パスの YAML (.yml) ファイルに定義されます。 この定義には、ワークフローに含まれるさまざまな手順とパラメーターが含まれています。

Azure App Service のコンテナー ワークフロー ファイルには、次の 3 つのセクションがあります。

|Section  |タスク  |
|---------|---------|
|**認証** | 1.サービス プリンシパルまたは発行プロファイルを取得します。 <br /> 2.GitHub シークレットを作成します。 |
|**ビルド** | 1.環境を作成します。 <br /> 2.コンテナー イメージをビルドします。 |
|**デプロイする** | 1.コンテナー イメージをデプロイする |

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- GitHub アカウント。 お持ちでない場合は、[無料](https://github.com/join)でサインアップしてください。  
- コンテナー用の作業コンテナー レジストリと Azure App Service アプリ。 この例では、Azure Container Registry を使用します。 
    - [コンテナー化された Node.js アプリケーションを Docker を使って作成し、コンテナー イメージをレジストリにプッシュした後、イメージを Azure App Service にデプロイする方法をご確認ください](https://docs.microsoft.com/azure/developer/javascript/tutorial-vscode-docker-node-01)

## <a name="generate-deployment-credentials"></a>デプロイ資格情報を生成する

GitHub Actions 用の Azure App Services での認証で推奨される方法は、発行プロファイルを使用することです。 サービス プリンシパルを使用して認証することもできますが、プロセスに必要な手順が多くなります。 

発行プロファイル資格情報またはサービス プリンシパルを [GitHub シークレット](https://docs.github.com/en/actions/reference/encrypted-secrets)として保存して、Azure で認証します。 このシークレットには、ワークフロー内でアクセスします。 

# <a name="publish-profile"></a>[発行プロファイル](#tab/publish-profile)

発行プロファイルは、アプリレベルの資格情報です。 発行プロファイルを GitHub シークレットとして設定します。 

1. Azure portal で、お使いのアプリ サービスに移動します。 

1. **[概要]** ページで、 **[発行プロファイルの取得]** を選択します。

1. ダウンロードしたファイルを保存します。 このファイルの内容を使用して、GitHub シークレットを作成します。

# <a name="service-principal"></a>[サービス プリンシパル](#tab/service-principal)

[サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)は、[Azure CLI](/cli/azure/) で [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) コマンドを使用して作成できます。 このコマンドは、Azure portal で [Azure Cloud Shell](https://shell.azure.com/) を使用するか、 **[試してみる]** ボタンを選択して実行します。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

この例で、プレースホルダーをお使いのサブスクリプション ID、リソース グループ名、アプリ名に置き換えます。 これにより、この App Service アプリにアクセスするためのロールの割り当て資格情報を含む JSON オブジェクトが出力されます。 この JSON オブジェクトを後のためにコピーします。

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> 常に最小限のアクセス権を付与することをお勧めします。 前の例の範囲は、リソース グループ全体ではなく、特定の App Service アプリに限定されます。

---

## <a name="configure-the-github-secret"></a>GitHub シークレットの構成

[GitHub](https://github.com/) で、自分のリポジトリを参照し、 **[設定] > [シークレット] > [新しいシークレットの追加]** を選択します。

JSON 出力の内容を、シークレット変数の値として貼り付けます。 シークレットに `AZURE_CREDENTIALS` などの名前を付けます。

後でワークフロー ファイルを構成する場合は、Azure ログイン アクションの入力 `creds` にシークレットを使用します。 次に例を示します。

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

## <a name="configure-the-github-secret-for-authentication"></a>認証用の GitHub シークレットを構成する

# <a name="publish-profile"></a>[発行プロファイル](#tab/publish-profile)

[GitHub](https://github.com/) で自分のリポジトリを参照し、 **[設定]、[シークレット]、[Add a new secret]\(新しいシークレットの追加\)** の順に選択します。

[アプリ レベルの資格情報](#generate-deployment-credentials)を使用するには、ダウンロードした発行プロファイルのファイルの内容をシークレットの値フィールドに貼り付けます。 シークレットに `AZURE_WEBAPP_PUBLISH_PROFILE` という名前を付けます。

GitHub ワークフローを構成するときに、Azure Web アプリをデプロイするアクションの中で `AZURE_WEBAPP_PUBLISH_PROFILE` を使用します。 次に例を示します。
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[サービス プリンシパル](#tab/service-principal)

[GitHub](https://github.com/) で自分のリポジトリを参照し、 **[設定]、[シークレット]、[Add a new secret]\(新しいシークレットの追加\)** の順に選択します。

[ユーザー レベルの資格情報](#generate-deployment-credentials)を使用するには、Azure CLI コマンドからの JSON 出力全体をシークレットの値フィールドに貼り付けます。 シークレットに `AZURE_CREDENTIALS` などの名前を付けます。

後でワークフロー ファイルを構成する場合は、Azure ログイン アクションの入力 `creds` にシークレットを使用します。 次に例を示します。

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="configure-github-secrets-for-your-registry"></a>レジストリ用の GitHub シークレットを構成する

Docker ログイン アクションで使用するシークレットを定義します。 

1. Azure portal または Docker でお使いのコンテナーにアクセスし、ユーザー名とパスワードをコピーします。 

2. レジストリ ユーザー名のために `REGISTRY_USERNAME` という名前の新しいシークレットを定義します。 

3. レジストリ パスワードのために `REGISTRY_PASSWORD` という名前の新しいシークレットを定義します。 

## <a name="build-the-container-image"></a>コンテナー イメージのビルド

次の例は、Node.js Docker イメージをビルドするワークフローの一部を示しています。 [Docker Login](https://github.com/azure/docker-login) を使用してプライベート コンテナー レジストリにログインします。 この例では Azure Container Registry を使用しますが、他のレジストリでも同じアクションを実行できます。 


```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

また、[Docker Login](https://github.com/azure/docker-login) を使用して複数のコンテナー レジストリに同時にログインすることもできます。 この例には、docker.io を使用した認証用の 2 つの新しい GitHub シークレットが含まれています。

```yml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - uses: azure/docker-login@v1
      with:
        login-server: index.docker.io
        username: ${{ secrets.DOCKERIO_USERNAME }}
        password: ${{ secrets.DOCKERIO_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

## <a name="deploy-to-an-app-service-container"></a>App Service コンテナーへのデプロイ

App Service のカスタム コンテナーにイメージをデプロイするには、`azure/webapps-deploy@v2` アクションを使用します。 このアクションには、次の 5 つのパラメーターがあります。

| **パラメーター**  | **説明**  |
|---------|---------|
| **app-name** | (必須) App Service アプリの名前 | 
| **publish-profile** | (オプション) Web 配置のシークレットでプロファイル ファイルの内容を発行する |
| **images** | 完全修飾コンテナー イメージ名。 たとえば、' myregistry.azurecr.io/nginx:latest ' や ' python:3.7.2-alpine/' などです。 複数コンテナーのシナリオでは、複数のコンテナー イメージ名を指定できます (複数行で区切ります) |
| **slot-name** | (オプション) 運用スロット以外の既存のスロットを入力します。 |
| **configuration-file** | (オプション) Docker-Compose ファイルのパス |

# <a name="publish-profile"></a>[発行プロファイル](#tab/publish-profile)

```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2

    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}

    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     

    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
```
# <a name="service-principal"></a>[サービス プリンシパル](#tab/service-principal)

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
      
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

---

## <a name="next-steps"></a>次のステップ

GitHub には、一連のアクションが別々のリポジトリにあります。それぞれには、CI/CD に GitHub を使用し、ご自身のアプリを Azure にデプロイするときに役立つドキュメントとサンプルが含まれています。

- [Azure にデプロイするためのアクション ワークフロー](https://github.com/Azure/actions-workflow-samples)

- [Azure ログイン](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Docker でのログイン/ログアウト](https://github.com/Azure/docker-login)

- [ワークフローをトリガーするイベント](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s のデプロイ](https://github.com/Azure/k8s-deploy)

- [スターター ワークフロー](https://github.com/actions/starter-workflows)