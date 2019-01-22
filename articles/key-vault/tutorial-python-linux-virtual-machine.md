---
title: チュートリアル - Azure 仮想マシンで Azure Key Vault を使用する (Python) | Microsoft Docs
description: このチュートリアルでは、キー コンテナーからシークレットを読み取るように Python アプリケーションを構成します。
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
ms.openlocfilehash: 8c816d17807432d75b6102190fc37d25a525d7cf
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2019
ms.locfileid: "54244173"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-virtual-machine-in-python"></a>チュートリアル:Azure 仮想マシンで Azure Key Vault を使用する (Python)

Azure Key Vault は、API キーや、アプリケーション、サービス、IT リソースへのアクセスに必要なデータベース接続文字列などのシークレットを保護するのに役立ちます。

このチュートリアルでは、Azure リソースのマネージド ID を使用して Azure Key Vault から情報を読み取るように Azure Web アプリケーションを設定する手順を学習します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * Key Vault を作成します。
> * キー コンテナーにシークレットを格納する。
> * Azure 仮想マシンを作成します。
> * 仮想マシンに対して[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を有効にする。
> * キー コンテナーからデータを読み取るために必要な権限をコンソール アプリケーションに付与する。
> * キー コンテナーからシークレットを取得する。

先に進む前に、[Key Vault の基本的な概念](key-vault-whatis.md#basic-concepts)を確認してください。

## <a name="prerequisites"></a>前提条件
すべてのプラットフォームで次の条件を満たす必要があります。

* Git ([ダウンロード](https://git-scm.com/downloads))。
* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) バージョン 2.0.4 以降。 Windows、Mac、Linux に対応しています。

### <a name="managed-service-identity-and-how-it-works"></a>マネージド サービス ID とそのしくみ
このチュートリアルでは、マネージド サービス ID (MSI) を使用します。

Azure Key Vault には、資格情報をコードに含めることなく安全に格納することができます。 それらを取得するためには、Key Vault に対して認証を行う必要があります。 Key Vault に対して認証を行うには、資格情報が必要となります。 これは、古典的なブートストラップ問題です。 MSI は、Azure と Azure Active Directory (Azure AD) を利用して "ブートストラップ ID" を提供します。これにより、物事が簡単になります。

Azure サービス (Virtual Machines、App Service、Functions など) に対して MSI を有効にすると、そのサービスのインスタンスに使用される[サービス プリンシパル](key-vault-whatis.md#basic-concepts)が Azure AD に作成されます。 サービスのインスタンスには、Azure によって、サービス プリンシパルの資格情報が挿入されます。 

![MSI](media/MSI.png)

次に、ユーザーのコードが、Azure リソース上で利用可能なローカル メタデータ サービスを呼び出してアクセス トークンを取得します。
そのコードは、Azure Key Vault サービスに対し、ローカルの MSI エンドポイントから取得したアクセス トークンを使用して認証を行います。 

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

* キー コンテナー名:名前の文字数は 3 から 24 文字です。使用できる文字は 0-9、a-z、A-Z、およびハイフン (-) のみです。
* リソース グループ名。
* 場所:**米国西部**。

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
この時点で、使用している Azure アカウントのみが、この新しいコンテナーで任意の操作を実行することが許可されます。

## <a name="add-a-secret-to-the-key-vault"></a>キー コンテナーにシークレットを追加する

シークレットのしくみをよく理解できるように、シークレットを追加します。 SQL 接続文字列やその他の情報を機密として保持する必要があるのに、アプリケーションで使用可能になるように保管している場合があります。

次のコマンドを入力して、*AppSecret* というキー コンテナーにシークレットを作成します。 このシークレットには、値 **MySecret** が格納されます。

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

[az vm create](/cli/azure/vm#az_vm_create) コマンドを使用して VM を作成します。

次の例では、*myVM* という名前の VM を作成し、*azureuser* という名前のユーザー アカウントを追加します。 `--generate-ssh-keys` パラメーターによって SSH キーが自動的に生成され、キーは既定のキーの場所 (*~/.ssh*) に配置されます。 代わりに特定のキーのセットを使用するには、`--ssh-key-value` オプションを使用します。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

VM とサポートするリソースを作成するには数分かかります。 次の出力例は、成功した VM 作成操作を示します。

```
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-00-00-00-00-00",
  "powerState": "VM running",
  "privateIpAddress": "XX.XX.XX.XX",
  "publicIpAddress": "XX.XX.XXX.XXX",
  "resourceGroup": "myResourceGroup"
}
```

作成した VM からの出力に含まれる実際の `publicIpAddress` 値を記録しておいてください。 このアドレスは、次の手順で VM にアクセスするために使います。

## <a name="assign-an-identity-to-the-virtual-machine"></a>仮想マシンに ID を割り当てる
この手順では、仮想マシンに対するシステム割り当て ID を作成します。 Azure CLI で、次のコマンドを実行します。

```
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

コマンドの出力結果は次のとおりです。 **systemAssignedIdentity** の値をメモしてください。 

```
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="give-the-virtual-machine-identity-permission-to-the-key-vault"></a>仮想マシン ID にキー コンテナーへのアクセス許可を与える
次に、対象となる ID に、キー コンテナーへのアクセス許可を与えます。 次のコマンドを実行します。

```
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-virtual-machine"></a>仮想マシンにログインします。

[このチュートリアル](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)に従って仮想マシンにログインします。

## <a name="create-and-run-the-sample-python-app"></a>サンプルの Python アプリを作成して実行する

次の例のファイルは、*Sample.py* という名前です。 ここでは、HTTP GET 呼び出しを行うために [requests](https://pypi.org/project/requests/2.7.0/) ライブラリが使用されます。

## <a name="edit-samplepy"></a>Sample.py を編集する
Sample.py の作成後、ファイルを開いて以下のコードをコピーします このコードは、次の 2 つのステップから成るプロセスです。 
1. VM 上のローカル MSI エンドポイントからトークンをフェッチします。 次にそのエンドポイントが Azure Active Directory からトークンをフェッチします
2. トークンをキー コンテナーに渡し、シークレットをフェッチします。 

```
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from an MSI-enabled Azure resource      
    # Note that the resource here is https://vault.azure.net for the public cloud, and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # Extracting data in JSON format 
    # This request gets an access token from Azure Active Directory by using the local MSI endpoint
    data = r.json() 
    
    # Step 2: Pass the access token received from the previous HTTP GET call to the key vault
    KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

次のコマンドを実行すると、シークレット値が表示されるはずです。 

```
python Sample.py
```

前出のコードは、Windows 仮想マシンで Azure Key Vault を操作する方法を示しています。 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
