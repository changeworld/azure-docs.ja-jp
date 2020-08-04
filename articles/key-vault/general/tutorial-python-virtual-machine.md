---
title: チュートリアル - Python で仮想マシンを使用して Azure Key Vault を使用する | Microsoft Docs
description: このチュートリアルでは、キー コンテナーからシークレットを読み取るように ASP.NET Core アプリケーションを構成します。
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, tracking-python
ms.openlocfilehash: 453307b304c4cb1899b1de31117c944ac66fcddb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093937"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-python"></a>チュートリアル:Python で仮想マシンを使用して Azure Key Vault を使用する

Azure Key Vault は、API キーや、アプリケーション、サービス、IT リソースへのアクセスに必要なデータベース接続文字列などのシークレットを保護するのに役立ちます。

このチュートリアルでは、コンソール アプリケーションで Azure Key Vault から情報を読み取る方法について学習します。 そのためには、Azure リソースのマネージド ID を使用します。 

このチュートリアルでは、次の操作方法について説明します。

> [!div class="checklist"]
> * Key Vault を作成します。
> * キー コンテナーにシークレットを追加します。
> * キー コンテナーからシークレットを取得する。
> * Azure 仮想マシンを作成します。
> * マネージド ID を有効化します。
> * VM ID にアクセス許可を割り当てます。

始める前に、[Key Vault の基本的な概念](basic-concepts.md)を確認してください。 

Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

Windows、Mac、Linux:
  * [Git](https://git-scm.com/downloads)
  * このチュートリアルでは、Azure CLI をローカルで実行する必要があります。 Azure CLI バージョン 2.0.4 以降がインストールされている必要があります。 バージョンを確認するには、`az --version` を実行します。 CLI をインストールまたはアップグレードする必要がある場合は、「[Install Azure CLI 2.0 (Azure CLI 2.0 のインストール)](/cli/azure/install-azure-cli)」を参照してください。

## <a name="log-in-to-azure"></a>Azure にログインする

Azure CLI を使用して Azure にログインするには、次のように入力します。

```azurecli
az login
```

### <a name="create-a-resource-group-and-key-vault"></a>リソース グループとキー コンテナーを作成する

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="add-a-secret-to-the-key-vault"></a>キー コンテナーにシークレットを追加する

シークレットのしくみをよく理解できるように、シークレットを追加します。 シークレットとしては、SQL 接続文字列など、安全性と、アプリケーションから利用できる状態の両方を維持するために必要な情報が考えられます。

**AppSecret** というキー コンテナーにシークレットを作成するには、次のコマンドを入力します。

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

このシークレットには、**MySecret** という値が格納されます。

## <a name="create-a-virtual-machine"></a>仮想マシンの作成
次のいずれかの方法を使用して、仮想マシンを作成できます。

* [Azure CLI](../../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
* [Azure ポータル](../../virtual-machines/windows/quick-create-portal.md)

## <a name="assign-an-identity-to-the-vm"></a>VM に ID を割り当てる
この手順では、Azure CLI で次のコマンドを実行して、仮想マシンに対するシステム割り当て ID を作成します。

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

システムによって割り当てられた ID が次のコードに表示されていることにご注意ください。 上記のコマンドの出力は次のようになります。 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>VM ID にアクセス許可を割り当てる
ここで、次のコマンドを実行して、以前に作成した ID アクセス許可をキー コンテナーに割り当てることができます。

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-on-to-the-virtual-machine"></a>仮想マシンへのログオン

仮想マシンにログオンするには、[Windows が実行されている Azure 仮想マシンへの接続とサインオン](../../virtual-machines/windows/connect-logon.md)に関する記事の手順に従ってください。

## <a name="create-and-run-a-sample-python-app"></a>サンプルの Python アプリを作成して実行する

次のセクションに、*Sample.py* という名前のファイルの例があります。 ここでは、HTTP GET 呼び出しを行うために[要求](https://2.python-requests.org/en/master/)ライブラリが使用されます。

## <a name="edit-samplepy"></a>Sample.py を編集する

*Sample.py* の作成後、ファイルを開いてこのセクションのコードをコピーします。 

このコードは、2 段階のプロセスを表します。
1. VM 上のローカル MSI エンドポイントからトークンをフェッチします。  
  それにより、Azure AD からもトークンがフェッチされます。
1. トークンをキー コンテナーに渡し、シークレットをフェッチします。 

```python
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from a Managed Identity enabled azure resource.
    # Resources with an MSI configured recieve an AAD access token by using the Azure Instance Metadata Service (IMDS)
    # IMDS provides an endpoint accessible to all IaaS VMs using a non-routable well-known IP Address
    # To learn more about IMDS and MSI Authentication see the following link: https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service
    # Note that the resource here is https://vault.azure.net for public cloud and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # extracting data in json format 
    # This request gets an access_token from Azure AD by using the local MSI endpoint.
    data = r.json() 
    
    # Step 2: Pass the access_token received from previous HTTP GET call to your key vault.
    KeyVaultURL = "https://{YOUR KEY VAULT NAME}.vault.azure.net/secrets/{YOUR SECRET NAME}?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

次のコードを実行して、シークレット値を表示できます。 

```console
python Sample.py
```

前出のコードは、Windows 仮想マシンで Azure Key Vault を操作する方法を示しています。 

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、仮想マシンとキー コンテナーを削除します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
