---
title: クイックスタート - Python 用の Azure Key Vault クライアント ライブラリ - キーの管理
description: Python クライアント ライブラリを使用して Azure Key Vault からキーを作成、取得、削除する方法について学習します。
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 2c15090551ad9d84282f65925ff9c2cfbab7e14f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773353"
---
# <a name="quickstart-azure-key-vault-keys-client-library-for-python"></a>クイック スタート:Python 用 Azure Key Vault キー クライアント ライブラリ

Python 用 Azure Key Vault クライアント ライブラリを使ってみます。 以下の手順に従ってパッケージをインストールし、基本タスクのコード例を試してみましょう。 Key Vault を使用して暗号キーを格納すると、そのようなキーをコードに格納する必要がなくなり、アプリのセキュリティが向上します。

[API のリファレンスのドキュメント](/python/api/overview/azure/keyvault-keys-readme) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys) | [パッケージ (Python Package Index)](https://pypi.org/project/azure-keyvault-keys/)

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Python 2.7 以降または 3.6 以降](/azure/developer/python/configure-local-development-environment)
- [Azure CLI](/cli/azure/install-azure-cli)

このクイックスタートは、Linux ターミナル ウィンドウで [Azure CLI](/cli/azure/install-azure-cli) を実行していることを前提としています。

## <a name="set-up-your-local-environment"></a>ローカル環境を設定する

このクイックスタートでは、Azure CLI と Azure Identity ライブラリを使用して、Azure サービスに対するユーザーの認証を行います。 開発者は、Visual Studio または Visual Studio Code を使用してその呼び出しを認証することもできます。詳細については、[Azure Identity クライアント ライブラリを使用してクライアントを認証する](/python/api/overview/azure/identity-readme)方法に関するページを参照してください。

### <a name="sign-in-to-azure"></a>Azure へのサインイン

1. `login` コマンドを実行します。

    ```azurecli-interactive
    az login
    ```

    CLI で既定のブラウザーを開くことができる場合、開いたブラウザに Azure サインイン ページが読み込まれます。

    それ以外の場合は、[https://aka.ms/devicelogin](https://aka.ms/devicelogin) でブラウザー ページを開き、ターミナルに表示されている認証コードを入力します。

2. ブラウザーでアカウントの資格情報を使用してサインインします。

### <a name="install-the-packages"></a>パッケージのインストール

1. ターミナルまたはコマンド プロンプトで、適切なプロジェクト フォルダーを作成したら、「[Python 仮想環境を使用する](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments)」で説明されているように、Python 仮想環境を作成し、アクティブ化します。

1. Azure Active Directory ID ライブラリをインストールします。

    ```terminal
    pip install azure.identity
    ```


1. Key Vault キー クライアント ライブラリをインストールします。

    ```terminal
    pip install azure-keyvault-keys
    ```

### <a name="create-a-resource-group-and-key-vault"></a>リソース グループとキー コンテナーを作成する

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### <a name="grant-access-to-your-key-vault"></a>キー コンテナーへのアクセス許可を付与する

自分のユーザー アカウントにシークレットのアクセス許可を付与するアクセス ポリシーをキー コンテナーに対して作成します。

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set
```

#### <a name="set-environment-variables"></a>環境変数の設定

このアプリケーションでは、`KEY_VAULT_NAME` という環境変数にキー コンテナーの名前を使用します。

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS または Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="create-the-sample-code"></a>サンプル コードを作成する

暗号化キーは、Python 用 Azure Key Vault キー クライアント ライブラリを使用して管理できます。 次のコード サンプルは、クライアントの作成、キーの設定、キーの取得、キーの削除を行う方法を示しています。

このコードを含めた *kv_keys.py* という名前のファイルを作成します。

```python
import os
from azure.keyvault.keys import KeyClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)

keyName = input("Input a name for your key > ")

print(f"Creating a key in {keyVaultName} called '{keyName}' ...")

rsa_key = client.create_rsa_key(keyName, size=2048)

print(" done.")

print(f"Retrieving your key from {keyVaultName}.")

retrieved_key = client.get_key(keyName)

print(f"Key with name '{retrieved_key.name}' was found.")
print(f"Deleting your key from {keyVaultName} ...")

poller = client.begin_delete_key(keyName)
deleted_key = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>コードの実行

前のセクションのコードが *kv_keys.py* という名前のファイルに含まれていることを確認します。 次のコマンドを使用して、コードを実行します。

```terminal
python kv_keys.py
```

- アクセス許可エラーが発生した場合は、[`az keyvault set-policy` コマンド](#grant-access-to-your-key-vault)を実行したことを確認してください。
- 同じキー名を使用してコードを再実行すると、"(Conflict) Key <name> is currently in a deleted but recoverable state ((競合) キー <name> は現在削除されているが、回復可能な状態です)" というエラーが生成されることがあります。 別のキー名を使用してください。

## <a name="code-details"></a>コードの詳細

### <a name="authenticate-and-create-a-client"></a>クライアントの認証と作成

このクイックスタートでは、ログイン ユーザーを使用してキー コンテナーに対する認証を行います。ローカル開発では、これが推奨される方法となります。 Azure にデプロイされるアプリケーションの場合は、App Service または仮想マシンにマネージド ID を割り当てる必要があります。詳細については、[マネージド ID の概要](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)に関するページを参照してください。

以下の例では、キー コンテナーの名前は、"https://\<your-key-vault-name\>.vault.azure.net" という形式で、キー コンテナーの URI に展開されます。 この例では、["DefaultAzureCredential()"](/python/api/azure-identity/azure.identity.defaultazurecredential) クラスを使用しています。環境や使用するオプションが変わっても、同じコードを使用して ID を提供することができます。 詳細については、[DefaultAzureCredential 認証](https://docs.microsoft.com/python/api/overview/azure/identity-readme)に関するセクションを参照してください。 


```python
credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)
```

## <a name="save-a-key"></a>キーの保存

キー コンテナーのクライアント オブジェクトを取得したら、[create_rsa_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-rsa-key-name----kwargs-) メソッドを使用してキーを格納することができます。 

```python
rsa_key = client.create_rsa_key(keyName, size=2048)
```

[create_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-key-name--key-type----kwargs-) または [create_ec_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-ec-key-name----kwargs-) を使用することもできます。

`create` メソッドを呼び出すと、キー コンテナーに対する Azure REST API への呼び出しが生成されます。

要求を処理するとき、クライアントに提供した資格情報オブジェクトを使用して、Azure により、呼び出し元の ID (サービス プリンシパル) が認証されます。

## <a name="retrieve-a-key"></a>キーの取得

Key Vault からキーを読み取るには、[get_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#get-key-name--version-none----kwargs-) メソッドを使用します。

```python
retrieved_key = client.get_key(keyName)
 ```

また、Azure CLI コマンド [az keyvault key show](/cli/azure/keyvault/key?#az_keyvault_key_show) を使用してキーが設定されていることを確認することもできます。

### <a name="delete-a-key"></a>キーの削除

キーを削除するには、[begin_delete_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#begin-delete-key-name----kwargs-) メソッドを使用します。

```python
poller = client.begin_delete_key(keyName)
deleted_key = poller.result()
```

`begin_delete_key` メソッドは非同期であり、ポーラー オブジェクトを返します。 ポーラーの `result` メソッドを呼び出して、その完了を待機します。

キーが削除されたことを確認するには、Azure CLI コマンド [az keyvault key show](/cli/azure/keyvault/key?#az_keyvault_key_show) を使用します。

削除されると、キーは削除されたが回復可能な状態がしばらく維持されます。 コードをもう一度実行する場合は、別のキー名を使用します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[証明書](../certificates/quick-create-python.md)と[シークレット](../secrets/quick-create-python.md)も試してみる場合は、この記事で作成した Key Vault を再利用できます。

それ以外の場合は、この記事で作成したリソースが完了したら、次のコマンドを使用して、リソース グループとそれに含まれるすべてのリソースを削除します。

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>次のステップ

- [Azure Key Vault の概要](../general/overview.md)
- [キー コンテナーへのアクセスをセキュリティで保護する](../general/security-overview.md)
- [Azure Key Vault 開発者ガイド](../general/developers-guide.md)
- [Key Vault のセキュリティの概要](../general/security-overview.md)
- [Key Vault を使用した認証](../general/authentication.md)
