---
title: GitHub Actions を使用してコンテナー イメージをスキャンする
description: コンテナー スキャン アクションを使用してコンテナー イメージをスキャンする方法について学習します
author: v-abiss
ms.author: v-abiss
ms.topic: quickstart
ms.reviewer: jukullam
ms.service: azure
ms.date: 05/20/2021
ms.custom: github-actions-azure
ms.openlocfilehash: a16c858008ce2233d0382e8a8f2a8e6224bbb6b8
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315807"
---
# <a name="scan-container-images-using-github-actions"></a>GitHub Actions を使用してコンテナー イメージをスキャンする

コンテナー イメージをビルドおよびスキャンするためのワークフローを作成することによって、[GitHub Actions](https://docs.github.com/en/actions/learn-github-actions) の使用を開始します。

GitHub Actions を使用すると、ワークフローからイメージをビルドおよびスキャンし、パブリックまたはプライベート [Container Registry](https://azure.microsoft.com/services/container-registry/) にプッシュすることで、CI/CD プロセスを高速化できます。

この記事では、[GitHub Marketplace](https://github.com/marketplace) の[コンテナー イメージ スキャン](https://github.com/marketplace/actions/container-image-scan)を利用します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- ビルドおよびプッシュされたすべてのコンテナー イメージを格納するための Azure Container Registry。 [Azure Container Registry は Azure portal から作成](../container-registry/container-registry-get-started-portal.md)できます。
- アクティブなリポジトリを持つ GitHub アカウント。 ない場合は、[無料](https://github.com/join)でサインアップしてください。 
    - この例では、 [Java Spring PetClinic サンプル アプリケーション](https://github.com/spring-projects/spring-petclinic)を使用します。

## <a name="workflow-file-overview"></a>ワークフロー ファイルの概要

ワークフローは、お使いのリポジトリの `/.github/workflows/` パスの YAML (.yml) ファイルに定義されます。 この定義には、ワークフローを構成するさまざまな手順とパラメーターが含まれます。

このファイルには 3 つのセクションがあります。

|Section  |タスク  |
|---------|---------|
|**認証** | 1. Azure で Container Registry を作成します。 <br /> 2.GitHub シークレットを作成します。 <br /> 3. GH アクションを使用してレジストリにログインします。 |
|**ビルド** | 1.環境を設定します。 <br /> 2. アプリをビルドします。 |
|**イメージをビルドおよびスキャンし、コンテナー レジストリにプッシュする** | 1. イメージをビルドします。 <br /> 2. イメージをスキャンします。 <br /> 3. イメージをプッシュします。|

## <a name="create-github-secrets"></a>GitHub シークレットを作成する

[Azure Container Registry Login アクション](https://github.com/marketplace/actions/azure-container-registry-login)を使用するには、まず、Container Registry の詳細をシークレットとして GitHub リポジトリに追加する必要があります。

この例では、Azure での認証に使用できる 3 つのシークレットを作成します。  

1. GitHub リポジトリを開き、 **[Settings]\(設定\)** に移動します。

    :::image type="content" source="media/github-action-scan/github-repo-settings.png" alt-text="ナビゲーションで [設定] を選択します。":::

1. **[Settings]\(設定\)** 、 **[New Secret]\([新しいシークレット\)]** の順に選択します。

    :::image type="content" source="media/github-action-scan/azure-secret-add.png" alt-text="シークレットの追加を選択します。":::

1. Azure portal から Container Registry の **[アクセス キー]** に移動して、次の値で作成された各シークレットに対して以下の値を貼り付けます。 

    | GitHub シークレット名 | Azure Container Registry の値 |
    |---------|---------|
    |`ACR_LOGIN_SERVER` | **ログイン サーバー** |
    |`REGISTRY_USERNAME` | **ユーザー名** |
    |`REGISTRY_PASSWORD` | **password** |
    
1. **[Add secret]\(シークレットの追加\)** を選択して保存します。

## <a name="add-a-dockerfile"></a>Dockerfile を追加する

`Dockerfile` を作成してリポジトリにコミットするには、次のスニペットを使用します。

```
FROM openjdk:11-jre-stretch
ADD target/spring-petclinic-2.4.2.jar spring-petclinic-2.4.2.jar
EXPOSE 8080
ENTRYPOINT [ "java", "-jar", "spring-petclinic-2.4.2.jar" ]
```


## <a name="use-the-docker-login-action"></a>Docker Login アクションを使用する

Azure で認証するには、シークレットと [Azure Container Registry Login アクション](https://github.com/marketplace/actions/azure-container-registry-login)を使用します。

このワークフローでは、[docker-login アクション](https://github.com/Azure/docker-login)で `secrets.ACR_LOGIN_SERVER`、`secrets.REGISTRY_USERNAME` および `secrets.REGISTRY_PASSWORD` にそれぞれ格納されているレジストリの **ログイン サーバー、** **ユーザー名、および **パスワード** の詳細を使用し、Azure Container Registry Login を使って認証を行います。 ワークフロー ファイルで GitHub シークレットを参照する方法の詳細については、GitHub Docs の「[ワークフローでの暗号化されたシークレットの使用](https://docs.github.com/en/actions/reference/encrypted-secrets#using-encrypted-secrets-in-a-workflow)」をご覧ください。


```yaml
on: [push]

name: Container Scan

jobs:
  build-image:
    runs-on: ubuntu-latest
    steps:
    - name: Login to the Container Registry  
      uses: azure/docker-login@v1
      with:
        login-server: ${{ secrets.ACR_LOGIN_SERVER }}
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
```

## <a name="configure-java"></a>Java を構成する

[Java セットアップ SDK アクション](https://github.com/marketplace/actions/setup-java-jdk)を使用して java 環境を設定します。 この例では、環境を設定し、Maven でビルドしてから、イメージをビルドしてスキャンし、コンテナー レジストリにプッシュします。

[GitHub 成果物](https://docs.github.com/en/actions/guides/storing-workflow-data-as-artifacts) は、ジョブ間でワークフロー内のファイルを共有する方法です。 

```yaml
on: [push]

name: Container Scan

jobs:
  build-image:
    runs-on: ubuntu-latest    
    steps:
    - name: Checkout
      uses: actions/checkout@v2    

    - name: Login to the Container Registry  
      uses: azure/docker-login@v1
      with:
        login-server: ${{ secrets.ACR_LOGIN_SERVER }}
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}

    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        java-version: '1.8.x'
        
    - name: Build with Maven
      run: mvn package --file pom.xml
```

## <a name="build-your-image"></a>イメージのビルド 

ワークフローの次のスニペットを使用して、イメージをビルドし、タグを付けます。 次のコード スニペットでは、Docker CLI を使用して、シェル ターミナル内にイメージをビルドし、タグを付けます。 

```yaml
    - name: Build and Tag image
      run: |
        docker build -f ./Dockerfile -t ${{ secrets.ACR_LOGIN_SERVER }}/spring-petclinic:${{ github.run_number }} .
        
```

## <a name="scan-the-image"></a>イメージをスキャンする

ビルドされたイメージをコンテナー レジストリにプッシュする前に、必ず、[コンテナー イメージ スキャン アクション](https://github.com/marketplace/actions/container-image-scan)を使用してイメージをスキャンし、脆弱性がないかどうかを確認してください。

次のコード スニペットをワークフローに追加します。これにより、プッシュされるイメージがセキュリティで保護され、標準に準拠するように、イメージがスキャンされ、***重大な脆弱性*** がないかどうかが確認されます。

また、このアクションを使用して他の入力を追加したり、リポジトリに `allowedlist.yaml` ファイルを追加したりして、脆弱性やベスト プラクティス チェックを無視することもできます。 

```yaml
    - name: Scan image
      uses: Azure/container-scan@v0
      with:
        image-name: ${{ secrets.ACR_LOGIN_SERVER }}/spring-petclinic:${{ github.run_number }}
        severity-threshold: CRITICAL
        run-quality-checks: true        
```

例: `allowedlist.yaml`。

```yaml
general:
  vulnerabilities:
    - CVE-2003-1307
    - CVE-2011-3374
  bestPracticeViolations:
    - CIS-DI-0005
```
## <a name="push-the-image-to-a-private-registry"></a>イメージをプライベート レジストリにプッシュする

イメージがスキャンされ、脆弱性が見つからない場合は、ビルドされたイメージをプライベート レジストリにプッシュしてかまいません。 次のコード スニペットでは、シェル ターミナルで Docker CLI を使用してイメージをプッシュします。

```yaml
    - name: Push image
      run: |
        docker push ${{ secrets.ACR_LOGIN_SERVER }}/spring-petclinic:${{ github.run_number }}
        
```
## <a name="next-steps"></a>次のステップ
- [Azure Container Registry から Azure Container Instances へのデプロイ](../container-instances/container-instances-using-azure-container-registry.md)方法について学習します。
