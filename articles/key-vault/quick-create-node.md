---
title: キー コンテナーからシークレットを読み取るように Azure Web アプリケーションを構成するためのチュートリアル | Microsoft Docs
description: 'チュートリアル: Key vault からシークレットを読み取るように Node.js アプリケーションを構成する'
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: sumedhb
ms.service: key-vault
ms.workload: identity
ms.topic: quickstart
ms.date: 08/01/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: cc43081463667eba06af6538f3d78f16544ed2a5
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412244"
---
# <a name="quickstart-how-to-set-and-read-a-secret-from-key-vault-in-a-node-web-app"></a>クイック スタート: Node Web アプリで Key Vault にシークレットを設定する方法と Key Vault からシークレットを読み取る方法 

このクイック スタートでは、Web アプリを使って Key Vault にシークレットを保存する方法とシークレットを取得する方法について説明します。 この Web アプリは、ローカルで実行する場合と Azure で実行する場合とがあります。 このクイック スタートでは Node.js とマネージド サービス ID (MSI) を使用します。

> [!div class="checklist"]
> * キー コンテナーを作成する
> * キー コンテナーにシークレットを格納する
> * キー コンテナーからシークレットを取得する
> * Azure AD Web アプリケーションを作成する
> * [マネージド サービス ID を有効にする](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)
> * Web アプリケーションに必要なアクセス許可を付与して、キー コンテナーからデータを読み取る

先に進む前に、[基本的な概念](key-vault-whatis.md#basic-concepts)を理解しておいてください。

## <a name="prerequisites"></a>前提条件

* [Node JS](https://nodejs.org/en/)
* [Git](https://www.git-scm.com/)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4 以降
* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="login-to-azure"></a>Azure にログインする

CLI を使用して Azure にログインするには、次のように入力します。

```azurecli
az login
```

## <a name="create-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#az_group_create) コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

リソース グループ名を選択し、プレース ホルダーを入力してください。
次の例では、*<YourResourceGroupName>* という名前のリソース グループを *eastus* の場所に作成します。

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

作成したリソース グループは、このチュートリアルの全体を通して使用します。

## <a name="create-an-azure-key-vault"></a>Azure Key Vault を作成する

次に、前の手順で作成したリソース グループを使って Key Vault を作成します。 この記事全体で Key Vault の名前として "ContosoKeyVault" を使用していますが、一意の名前を使用する必要があります。 次の情報を指定します。

* Key Vault 名 - **こちらで Key Vault 名を選択してください**。
* リソース グループ名 -**こちらでリソース グループ名を選択してください**。
* 場所: **米国東部**。

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

この時点で、自分の Azure アカウントが唯一、この新しいコンテナーで任意の操作を実行することを許可されています。

## <a name="add-a-secret-to-key-vault"></a>キー コンテナーにシークレットを追加する

シークレットのしくみをよく理解できるように、シークレットを追加します。 SQL 接続文字列やその他の情報を機密として保持する必要があるのに、アプリケーションで使用可能になるように保管している場合があります。 このチュートリアルでは、パスワードを **AppSecret** と呼び、このパスワード内に **MySecret** という値を格納します。

次のコマンドを入力して、値 **MySecret** を保存する **AppSecret** というシークレットをキー コンテナーに作成します。

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

シークレットに格納されている値をプレーンテキストとして表示するには:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

このコマンドは、URI を含むシークレットの情報を表示します。 これらの手順を完了すると、Azure Key Vault のシークレットへの URI がわかります。 この情報を書き留めておいてください。 後の手順で必要になります。

## <a name="clone-the-repo"></a>リポジトリを複製する

次のコマンドを実行してリポジトリを複製し、ソースを編集するためのローカル コピーを作成します。

```
git clone https://github.com/Azure-Samples/key-vault-node-quickstart.git
```

## <a name="install-dependencies"></a>依存関係をインストールする

ここで依存関係をインストールします。 次のコマンドを実行します。cd key-vault-node-quickstart npm install

このプロジェクトでは、次の 2 つのノード モジュールを使用しました。

* [ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure) 
* [azure-keyvault](https://www.npmjs.com/package/azure-keyvault)

## <a name="publish-the-web-application-to-azure"></a>Azure に Web アプリケーションを発行する

以下に示したいくつかの手順を実行する必要があります。

- 1 つ目の手順は、[Azure App Service](https://azure.microsoft.com/services/app-service/) プランの作成です。 このプランには複数の Web アプリを格納することができます。

    ```
    az appservice plan create --name myAppServicePlan --resource-group myResourceGroup
    ```
- 次に、Web アプリを作成します。 次の例では、<app_name> をグローバルに一意のアプリ名に置き換えてください (有効な文字は a-z、0-9、-)。 ランタイムは NODE|6.9 に設定されています。 サポートされているすべてのランタイムを確認するには、az webapp list-runtimes を実行します。
    ```
    # Bash
    az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "NODE|6.9" --deployment-local-git
    # PowerShell
    az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "NODE|6.9"
    ```
    Web アプリが作成されると、Azure CLI によって次の例のような出力が表示されます。
    ```
    {
      "availabilityState": "Normal",
      "clientAffinityEnabled": true,
      "clientCertEnabled": false,
      "cloningInfo": null,
      "containerSize": 0,
      "dailyMemoryTimeQuota": 0,
      "defaultHostName": "<app_name>.azurewebsites.net",
      "enabled": true,
      "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
      < JSON data removed for brevity. >
    }
    ```
    新しく作成された Web アプリに移動すると、Web アプリが稼働していることがわかります。 <app_name> は、アプリの一意の名前に置き換えてください。

    ```
    http://<app name>.azurewebsites.net
    ```
    また、上のコマンドを実行すると、ローカル Git から Azure にデプロイすることができる Git 対応のアプリが作成されます。 
    ローカル Git は、"https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git" という URL を使って構成されています。

- デプロイ ユーザーを作成します。前のコマンドが完了したら、ローカル Git リポジトリに Azure リモートを追加できます。 <url> を、「アプリの Git を有効にする」で取得した Git リモートの URL で置き換えます。

    ```
    git remote add azure <url>
    ```

## <a name="enable-managed-service-identity"></a>マネージド サービス ID を有効にする

Azure Key Vault は、資格情報およびその他のキーやシークレットを安全に保管する方法を提供しますが、コードは Key Vault に認証してそれらを取得する必要があります。 管理対象サービス ID (MSI) は、Azure Active Directory (Azure AD) で自動的に管理されている ID を Azure サービスに付与することで、この問題を簡単に解決します。 この ID を使用して、コードに資格情報が含まれていなくても、Key Vault を含む Azure AD の認証をサポートする任意のサービスに認証することができます。

assign-identity コマンドを実行して、このアプリケーションの ID を作成します。

```azurecli
az webapp identity assign --name <app_name> --resource-group "<YourResourceGroupName>"
```

このコマンドは、ポータルに移動して、Web アプリケーション プロパティの **[マネージド サービス ID]** を **[オン]** に切り替えることと同等です。

### <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>アプリケーションにアクセス許可を割り当ててキー コンテナーからシークレットを読み取る

前のコマンドの出力は、書き留めるかコピーしておいてください。 次のような形式になっています。
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
次に、上でコピーしたキー コンテナーの名前と PrincipalId の値を使用してこのコマンドを実行します。

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get
```

## <a name="deploy-the-node-app-to-azure-and-retrieve-the-secret-value"></a>Node アプリを Azure にデプロイしてシークレット値を取得する

以上で準備は完了です。 次のコマンドを実行してアプリを Azure にデプロイします。

```
git push azure master
```

その後ブラウザーで https://<app_name>.azurewebsites.net にアクセスすると、シークレット値が確認できます。
<YourKeyVaultName> という名前を実際のコンテナー名に置き換えたことを確認してください。

## <a name="next-steps"></a>次の手順

* [Azure Key Vault のホーム ページ](https://azure.microsoft.com/services/key-vault/)
* [Azure Key Vault のドキュメント](https://docs.microsoft.com/azure/key-vault/)
* [Azure SDK For Node](https://docs.microsoft.com/javascript/api/overview/azure/key-vault)
* [Azure REST API リファレンス](https://docs.microsoft.com/rest/api/keyvault/)