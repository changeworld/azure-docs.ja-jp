---
title: GitHub アクションを使用して CI/CD を構成する
description: GitHub Actions を使用して CI/CD パイプラインからご自分のコードを Azure App Service にデプロイする方法について説明します。 ビルド タスクをカスタマイズし、複雑なデプロイを実行します。
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: be6b5f0af17aa8343dcb74fd5f0710d44332ce0e
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2020
ms.locfileid: "84193305"
---
# <a name="deploy-to-app-service-using-github-actions"></a>GitHub Actions を使用した App Service へのデプロイ

[GitHub アクション](https://help.github.com/en/articles/about-github-actions)を使用すると、自動化されたソフトウェア開発ライフサイクル ワークフローを柔軟に構築できます。 GitHub 向けの Azure App Service Actions と使用すると、GitHub Actions を使用してご自分のワークフローを自動化して [Azure App Service](overview.md) にデプロイできます。

> [!IMPORTANT]
> GitHub Actions は現在ベータ版です。 まず、ご自分の GitHub アカウントを使用し、[新規登録し、プレビューに参加](https://github.com/features/actions)する必要があります。
> 

ワークフローは、お使いのリポジトリの `/.github/workflows/` パスの YAML (.yml) ファイルに定義されます。 この定義には、ワークフローを構成するさまざまな手順とパラメーターが含まれます。

Azure App Service のワークフロー ファイルには、次の 3 つのセクションがあります。

|Section  |タスク  |
|---------|---------|
|**認証** | 1.サービス プリンシパルを定義します <br /> 2.GitHub シークレットを作成します |
|**ビルド** | 1.環境をセットアップする <br /> 2.Web アプリを作成します |
|**デプロイする** | 1.Web アプリのデプロイ |

## <a name="create-a-service-principal"></a>サービス プリンシパルの作成

[Azure CLI](https://docs.microsoft.com/cli/azure/) の [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) コマンドを使用すると、[サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)を作成できます。 このコマンドは、Azure portal の [Azure Cloud Shell](https://shell.azure.com/) を使用するか、 **[試してみる]** ボタンを選択して実行できます。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> --sdk-auth
```

この例のリソースのプレースホルダーは、ご自分のサブスクリプション ID、リソース グループ名、およびアプリ名に置き換えます。 これにより、ご自分の App Service アプリにアクセスするためのロールの割り当て資格情報が出力されます。 この JSON オブジェクトをコピーします。このオブジェクトは、GitHub に対する認証に使用します。

> [!NOTE]
> 認証に発行プロファイルを使用する場合、サービス プリンシパルを作成する必要はありません。

> [!IMPORTANT]
> 常に最小限のアクセス権を付与することをお勧めします。 これが、前の例の範囲がリソース グループ全体ではなく、特定の App Service アプリに限定される理由です。

## <a name="configure-the-github-secret"></a>GitHub シークレットの構成

デプロイには、アプリ レベルの資格情報 (つまり発行プロファイルなど) を使用することもできます。 次のステップに従って、シークレットを構成します。

1. ポータルで **[発行プロファイルの取得]** オプションを使用して、App Service アプリの発行プロファイルをダウンロードします。

2. [GitHub](https://github.com/) でご自分のリポジトリを参照し、 **[設定]、[シークレット]、[Add a new secret]** \(新しいシークレットの追加\) を選択します。

    ![secrets](media/app-service-github-actions/secrets.png)

3. ダウンロードした発行プロファイルのファイルの内容をシークレットの [値] フィールドに貼り付けます。

4. ここで、ご自分のブランチの `.github/workflows/workflow.yml` のワークフロー ファイルで、デプロイ Azure Web アプリ アクションの入力 `publish-profile` のシークレットを置き換えます。
    
    ```yaml
        - uses: azure/webapps-deploy@v2
          with:
            publish-profile: ${{ secrets.azureWebAppPublishProfile }}
    ```

5. 定義されたシークレットは以下のように表示されます。

    ![secrets](media/app-service-github-actions/app-service-secrets.png)

## <a name="set-up-the-environment"></a>環境をセットアップする

次の設定操作のいずれかを使用し、環境を設定できます。

|**Language**  |**設定操作**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

次の例は、サポートされているさまざまな言語の環境を設定するワークフローの一部です。

**JavaScript**

```yaml
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```
**Python**

```yaml
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

**.NET**

```yaml
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

**Java**

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

## <a name="build-the-web-app"></a>Web アプリを作成します

これは、言語および Azure App Service でサポートされる言語によって異なります。このセクションは、各言語の標準的なビルド手順です。

次の例は、サポートされているさまざまな言語で Web アプリをビルドするワークフローの一部です。

**JavaScript**

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

**Python**

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

**.NET**

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

**Java**

```yaml
    - uses: actions/checkout@v1
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
```
## <a name="deploy-to-app-service"></a>App Service にデプロイする

ご自分のコードを App Service アプリにデプロイするには、`azure/webapps-deploy@v2` アクションを使用します。 このアクションには、次の 4 つのパラメーターがあります。

| **パラメーター**  | **説明**  |
|---------|---------|
| **app-name** | (必須) App Service アプリの名前 | 
| **publish-profile** | (オプション) Web 配置のシークレットでプロファイル ファイルの内容を発行する |
| **package** | (オプション) パッケージまたはフォルダーへのパス。 デプロイする *.zip、*.war、*.jar またはフォルダー |
| **slot-name** | (オプション) 運用スロット以外の既存のスロットを入力します。 |

### <a name="deploy-using-publish-profile"></a>発行プロファイルを使用したデプロイ

発行プロファイルを使用して Node.js アプリをビルドし、Azure にデプロイするサンプル ワークフローを次に示します。

```yaml
# File: .github/workflows/workflow.yml

on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
          uses: azure/webapps-deploy@v2
          with: 
            app-name: node-rn
            publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

### <a name="deploy-using-azure-service-principal"></a>Azure サービス プリンシパルを使用したデプロイ

Azure サービス プリンシパルを使用して Node.js アプリを構築し、Azure にデプロイするサンプル ワークフローを次に示します。

```yaml
on: [push]

name: Node.js

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
   
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
        
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
               
    # deploy web app using Azure credentials
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'node-rn'

    # Azure logout 
    - name: logout
      run: |
        az logout
```

## <a name="next-steps"></a>次のステップ

GitHub には、一連のアクションが別々のリポジトリにあります。それぞれには、CI/CD に GitHub を使用し、ご自身のアプリを Azure にデプロイするときに役立つドキュメントとサンプルが含まれています。

- [Azure にデプロイするための Actions ワークフロー](https://github.com/Azure/actions-workflow-samples)

- [Azure ログイン](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [コンテナー用の Azure Web App](https://github.com/Azure/webapps-container-deploy)

- [Docker でのログイン/ログアウト](https://github.com/Azure/docker-login)

- [ワークフローをトリガーするイベント](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s のデプロイ](https://github.com/Azure/k8s-deploy)

- [スターター ワークフロー](https://github.com/actions/starter-workflows)
