---
title: 'チュートリアル: マネージド ID を使用して Azure Functions を呼び出す'
description: マネージド ID を使用して Azure Spring Cloud アプリから Azure Functions を呼び出す
author: MarkGardner
ms.author: margard
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/10/2020
ms.openlocfilehash: b737ea751d3b3d2132691e04a1a2cd853748db65
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792509"
---
# <a name="tutorial-use-a-managed-identity-to-invoke-azure-functions-from-an-azure-spring-cloud-app"></a>チュートリアル:マネージド ID を使用して Azure Spring Cloud アプリから Azure Functions を呼び出す

この記事では、Azure Spring Cloud アプリ用のマネージド ID を作成し、それを使用して HTTP によってトリガーされる関数を呼び出す方法を説明します。

Azure Functions と App Service には、どちらも Azure Active Directory (Azure AD) 認証の機能が組み込まれています。 この組み込みの認証機能と併せて、Azure Spring Cloud のマネージド ID を活用することで、最新の OAuth のセマンティックを使用して RESTful サービスを呼び出すことができます。 この方法では、シークレットをコード中に保存する必要がなく、外部リソースへのアクセスに対し、より粒度の細かい制御が可能となります。 


## <a name="prerequisites"></a>前提条件

* [Azure サブスクリプションにサインアップする](https://azure.microsoft.com/free/)
* [Azure CLI バージョン 2.0.67 以上をインストールする](/cli/azure/install-azure-cli)
* [Maven 3.0 以上をインストールする](https://maven.apache.org/download.cgi)
* [Azure Functions Core Tools バージョン 3.0.2009 以降をインストールする](../azure-functions/functions-run-local.md#install-the-azure-functions-core-tools)


## <a name="create-a-resource-group"></a>リソース グループを作成する
リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 コマンド [az group create](/cli/azure/group#az_group_create) を使用して、関数アプリと Spring Cloud の両方を含むリソース グループを作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```


## <a name="create-a-function-app"></a>関数アプリを作成する
関数アプリを作成するには、まず、[az storage account create](/cli/azure/storage/account#az_storage_account_create) コマンドを使用してバッキング ストレージ アカウントを作成する必要があります。

> [!Important]
> 関数アプリとストレージ アカウントには、それぞれ一意の名前が必要です。 次の例で、<your-functionapp-name> は実際の関数アプリの名前に、<your-storageaccount-name> は実際のストレージ アカウントの名前に置き換えてください。

```azurecli-interactive
az storage account create --name <your-storageaccount-name> --resource-group myResourceGroup --location eastus --sku Standard_LRS
```

ストレージ アカウントを作成したら、関数アプリを作成できます。

```azurecli-interactive
az functionapp create --name <your-functionapp-name> --resource-group myResourceGroup --consumption-plan-location eastus --os-type windows --runtime node --storage-account <your-storageaccount-name> --functions-version 3
```

"https://<your-functionapp-name>.azurewebsites.net" 形式で返される **hostNames** を書き留めてください。 後の手順で使用します。


## <a name="enable-azure-active-directory-authentication"></a>Azure Active Directory 認証を有効にする

[Azure portal](https://portal.azure.com) から、新たに作成された関数アプリにアクセスし、設定メニューから [認証/承認] を選択します。 [App Service 認証] を有効にし、[要求が認証されない場合に実行するアクション] を [Azure Active Directory でのログイン] に設定します。 この設定により、認証されていない要求が確実にすべて拒否されます (401 応答)。

![Azure Active Directory を既定のプロバイダーとして表示する認証設定](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-1.jpg)

[認証プロバイダー] の [Azure Active Directory] を選択して、アプリケーションの登録を構成します。 [管理モード] に [簡易] を選択すると、自動的に正しい構成で Azure AD テナントにアプリケーションの登録が作成されます。

![Azure Active Directory プロバイダーを簡易管理モードに設定](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-2.jpg)

設定を保存すると関数アプリが再起動し、以後すべての要求は、Azure AD でのログインを求められるようになります。 認証されていない要求が拒否されるようになったことは、関数アプリの (上記の手順の **hostNames** 出力で返される) ルート URL に移動することでテストできます。 所属する組織の Azure AD ログイン画面にリダイレクトされるはずです。


## <a name="create-an-http-triggered-function"></a>HTTP によってトリガーされる関数を作成する

空のローカル ディレクトリに新しい関数アプリを作成し、HTTP によってトリガーされる関数を追加します。

```console
func init --worker-runtime node
func new --template HttpTrigger --name HttpTrigger
```

Functions は HTTP エンドポイントを保護するために、既定ではキーベースの認証を使用します。 ここでは Azure AD Authentication を有効にすることで Functions へのアクセスを保護するため、[関数の承認レベルを anonymous に設定](../azure-functions/functions-bindings-http-webhook-trigger.md#secure-an-http-endpoint-in-production)する必要があります。

```json function.json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      ...
    }
  ]
}
```

前の手順で作成した[関数アプリ](#create-a-function-app)のインスタンスにアプリを発行する準備が整いました。

```console
func azure functionapp publish <your-functionapp-name>
```

publish コマンドからの出力には、新しく作成された関数への URL が表示されているはずです。

```output
Deployment completed successfully.
Syncing triggers...
Functions in <your-functionapp-name>:
    HttpTrigger - [httpTrigger]
        Invoke url: https://<your-functionapp-name>.azurewebsites.net/api/httptrigger
```


## <a name="create-azure-spring-cloud-service-and-app"></a>Azure Spring Cloud サービスおよびアプリを作成する
Spring Cloud 拡張機能をインストールした後、Azure CLI コマンド `az spring-cloud create` を使用して Azure Spring Cloud インスタンスを作成します。 

```azurecli-interactive
az extension add --name spring-cloud
az spring-cloud create --name mymsispringcloud --resource-group myResourceGroup --location eastus
```

次の例では、`--assign-identity` パラメーターの要求どおりに、システム割り当てマネージド ID を持つ `msiapp` という名前のアプリを作成します。

```azurecli
az spring-cloud app create --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --assign-endpoint true --assign-identity
```

## <a name="build-sample-spring-boot-app-to-invoke-the-function"></a>関数を呼び出すサンプル Spring Boot アプリを作成する

このサンプルは、HTTP によってトリガーされる関数を呼び出します。まず、[MSI エンドポイント](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md#get-a-token-using-http)にアクセス トークンを要求し、そのトークンを使用して関数の HTTP 要求を認証します。

1. サンプル プロジェクトをクローンします。 

    ```console
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. アプリのプロパティで、関数の URI とトリガー名を指定します。 

    ```bash
    cd Azure-Spring-Cloud-Samples/managed-identity-function
    vim src/main/resources/application.properties
    ```

    Azure Spring Cloud アプリでマネージド ID を使用するには、次の内容のプロパティを *src/main/resources/application.properties* に追加します。

    ```
    azure.function.uri=https://<your-functionapp-name>.azurewebsites.net
    azure.function.triggerPath=httptrigger
    ```

3. サンプル アプリをパッケージ化します。 

    ```console
    mvn clean package
    ```

4. 次に、Azure CLI コマンド `az spring-cloud app deploy` を使用してアプリを Azure にデプロイします。 

    ```azurecli
    az spring-cloud app deploy  --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --jar-path target/sc-managed-identity-function-sample-0.1.0.jar
    ```

5. パブリック エンドポイントまたはテスト エンドポイントにアクセスして、対象のアプリをテストします。 

    ```console
    curl https://mymsispringcloud-msiapp.azuremicroservices.io/func/springcloud
    ```

    応答本文で次のメッセージが返されていることがわかります。
    ```output
    Function Response: Hello, springcloud. This HTTP triggered function executed successfully.
    ```
    
    path パラメーターを変更して関数に別の値を渡してみましょう。

## <a name="next-steps"></a>次の手順

* [Azure Spring Cloud アプリケーションのシステム割り当てマネージド ID を有効にする方法](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [Azure リソース用マネージド ID の詳細](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [App Service にアクセスするようにクライアント アプリを構成する](../app-service/configure-authentication-provider-aad.md#configure-client-apps-to-access-your-app-service)
