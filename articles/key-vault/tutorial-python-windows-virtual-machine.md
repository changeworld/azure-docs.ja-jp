---
title: チュートリアル - Python で Azure Windows 仮想マシンを使用して Azure Key Vault を使用する方法 | Microsoft Docs
description: 'チュートリアル: キー コンテナーからシークレットを読み取るように ASP.Net Core アプリケーションを構成する'
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: cced3d363f9eb7418d6f453eccb1bf1d7ac20ead
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2019
ms.locfileid: "53972347"
---
# <a name="tutorial-how-to-use-azure-key-vault-with-azure-windows-virtual-machine-in-python"></a>チュートリアル:Python で Azure Windows 仮想マシンを使用して Azure Key Vault を使用する方法

Azure Key Vault は、API キーや、アプリケーション、サービス、IT リソースへのアクセスに必要なデータベース接続文字列などのシークレットを保護するのに役立ちます。

このチュートリアルでは、Azure リソースのマネージド ID を使用して Azure Key Vault から情報を読み取るように Azure Web アプリケーションを設定するために必要な手順を学習します。 ここでは、次の操作を行う方法について学習します。

> [!div class="checklist"]
> * Key Vault を作成します。
> * キー コンテナーにシークレットを格納する。
> * キー コンテナーからシークレットを取得する。
> * Azure 仮想マシンを作成する。
> * 仮想マシンに対して[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を有効にする。
> * キー コンテナーからデータを読み取るために必要な権限をコンソール アプリケーションに付与する。
> * キー コンテナーからシークレットを取得する

先に進む前に、[基本概念](key-vault-whatis.md#basic-concepts)を確認してください。

## <a name="prerequisites"></a>前提条件
* すべてのプラットフォーム:
  * Git ([ダウンロード](https://git-scm.com/downloads))。
  * Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) バージョン 2.0.4 以降。 これは、Windows、Mac、Linux に対応しています。

このチュートリアルでは、マネージド サービス ID を使用します

## <a name="what-is-managed-service-identity-and-how-does-it-work"></a>マネージド サービス ID とは何か、およびそのしくみ
本題に入る前に、MSI について理解しましょう。 Azure Key Vault を使用すると、資格情報を安全に保存することができます。したがって、コードには資格情報を含めませんが、資格情報を取得するために Azure Key Vault に対して認証する必要があります。 そして、Key Vault に対して認証するには資格情報が必要になります。 これは、古典的なブートストラップ問題です。 MSI は、Azure と Azure AD を利用して "ブートストラップ ID" を提供します。これにより、物事がはるかに簡単になります。

そのしくみについて説明しましょう。 Virtual Machines、App Service、Functions などの Azure サービスに対して MSI を有効にすると、Azure によってサービス インスタンスの[サービス プリンシパル](key-vault-whatis.md#basic-concepts)が Azure Active Directory に作成され、サービス プリンシパルの資格情報がサービスのインスタンスに挿入されます。 

![MSI](media/MSI.png)

次に、ユーザーのコードが、Azure リソース上で利用可能なローカル メタデータ サービスを呼び出してアクセス トークンを取得します。
コードは、ローカルの MSI_ENDPOINT から取得したアクセス トークンを使用して、Azure Key Vault サービスに対して認証します。 

## <a name="log-in-to-azure"></a>Azure にログインする

Azure CLI を使用して Azure にログインするには、次のように入力します。

```azurecli
az login
```

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#az-group-create) コマンドを使ってリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

リソース グループ名を選択し、プレース ホルダーを入力します。
次の例では、米国西部の場所にリソース グループを作成します。

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

作成したリソース グループは、この記事の全体を通して使用します。

## <a name="create-a-key-vault"></a>Key Vault を作成します

次に、前の手順で作成したリソース グループにキー コンテナーを作成します。 次の情報を指定します。

* キー コンテナー名:名前の文字数は 3 から 24 文字です。使用できる文字は 0-9、a-z、A-Z、および - のみです。
* リソース グループ名。
* 場所:**米国西部**。

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
この時点で、使用している Azure アカウントのみが、この新しいコンテナーで任意の操作を実行することが許可されます。

## <a name="add-a-secret-to-the-key-vault"></a>キー コンテナーにシークレットを追加する

シークレットのしくみをよく理解できるように、シークレットを追加します。 SQL 接続文字列やその他の情報を機密として保持する必要があるのに、アプリケーションで使用可能になるように保管している場合があります。

次のコマンドを入力して、**AppSecret** というキー コンテナーにシークレットを作成します。 このシークレットには、値 **MySecret** が格納されます。

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-virtual-machine"></a>仮想マシンを作成する
次のリンクに従って Windows 仮想マシンを作成します

[Azure CLI](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-cli) 

[Powershell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell)

[ポータル](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

## <a name="assign-identity-to-virtual-machine"></a>仮想マシンに ID を割り当てる
この手順では、Azure CLI で次のコマンドを実行して、仮想マシンに対するシステム割り当て ID を作成しています

```
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

次に示す systemAssignedIdentity に注目してください。 上記のコマンドの出力は次のようになります 

```
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="give-virtual-machine-identity-permission-to-key-vault"></a>仮想マシン ID のアクセス許可を Key Vault に付与する
ここで、次のコマンドを実行して、上記の手順で作成した ID のアクセス許可を Key Vault に付与することができます

```
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="login-to-the-virtual-machine"></a>仮想マシンにログインする

この[チュートリアル](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)に従うことができます

## <a name="create-and-run-sample-python-app"></a>サンプルの Python アプリを作成して実行する

以下のサンプルは、"Sample.py" という名前のファイルです。 ここでは、HTTP GET 呼び出しを行うために[要求](http://docs.python-requests.org/en/master/)ライブラリが使用されます。

## <a name="edit-samplepy"></a>Sample.py を編集する
Sample.py の作成後、ファイルを開いて以下のコードをコピーします

```
The below is a 2 step process. 
1. Fetch a token from the local MSI endpoint on the VM which in turn fetches a token from Azure Active Directory
2. Pass the token to Key Vault and fetch your secret 
```
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from a Managed Identity enabled azure resource      
    # Note that the resource here is https://vault.azure.net for public cloud and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # extracting data in json format 
    # This request gets a access_token from Azure Active Directory using the local MSI endpoint
    data = r.json() 
    
    # Step 2: Pass the access_token received from previous HTTP GET call to Key Vault
    KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

By running you should see the secret value 
```
Python Sample.py
```

The above code shows you how to do operations with Azure Key Vault in an Azure Windows Virtual Machine. 

## Next steps

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
