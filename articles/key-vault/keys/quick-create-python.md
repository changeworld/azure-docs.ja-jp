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
ms.openlocfilehash: 44942067756f82c224decc218de17bf7dbc69734
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2020
ms.locfileid: "89482133"
---
# <a name="quickstart-azure-key-vault-keys-client-library-for-python"></a>クイック スタート:Python 用 Azure Key Vault キー クライアント ライブラリ

Python 用 Azure Key Vault クライアント ライブラリを使ってみます。 以下の手順に従ってパッケージをインストールし、基本タスクのコード例を試してみましょう。 Key Vault を使用して暗号キーを格納すると、そのようなキーをコードに格納する必要がなくなり、アプリのセキュリティが向上します。

[API のリファレンスのドキュメント](/python/api/overview/azure/keyvault-keys-readme?view=azure-python) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys) | [パッケージ (Python Package Index)](https://pypi.org/project/azure-keyvault-keys/)

## <a name="set-up-your-local-environment"></a>ローカル環境を設定する

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. Key Vault キー ライブラリをインストールします。

    ```terminal
    pip install azure-keyvault-keys
    ```

## <a name="create-a-resource-group-and-key-vault"></a>リソース グループとキー コンテナーを作成する

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>サービス プリンシパルにキー コンテナーへのアクセス権を付与する

次の [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) コマンドを実行して、キーに対する delete、get、list、および create 操作のサービス プリンシパルを承認します。 

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --key-permissions delete get list create
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --key-permissions delete get list create
```

---

このコマンドは、前の手順で作成した `KEY_VAULT_NAME` および `AZURE_CLIENT_ID` 環境変数に依存しています。

詳細については、[アクセス ポリシーの割り当て - CLI](../general/assign-access-policy-cli.md) に関するページを参照してください。

## <a name="create-the-sample-code"></a>サンプル コードを作成する

Python 用 Azure Key Vault クライアント ライブラリを使用すると、暗号化キーおよび関連するアセット (証明書、シークレットなど) を管理できます。 以下のコード サンプルに、クライアントの作成、シークレットの設定、シークレットの取得、シークレットの削除を行う方法を示します。

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

- アクセス許可エラーが発生した場合は、[`az keyvault set-policy` コマンド](#give-the-service-principal-access-to-your-key-vault)を実行したことを確認してください。
- 同じキー名を使用してコードを再実行すると、"(Conflict) Key <name> is currently in a deleted but recoverable state ((競合) キー <name> は現在削除されているが、回復可能な状態です)" というエラーが生成されることがあります。 別のキー名を使用してください。

## <a name="code-details"></a>コードの詳細

### <a name="authenticate-and-create-a-client"></a>クライアントの認証と作成

前述のコードでは、[`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) オブジェクトにより、サービス プリンシパル用に作成した環境変数が使用されます。 この資格情報は、[`KeyClient`](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python) など、Azure ライブラリからクライアント オブジェクトを作成するたびに、そのクライアントを使用して操作するリソースの URI と共に指定します。

```python
credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)
```

## <a name="save-a-key"></a>キーの保存

キー コンテナーのクライアント オブジェクトを取得したら、[create_rsa_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-rsa-key-name----kwargs-) メソッドを使用してキーを格納することができます。 

```python
rsa_key = client.create_rsa_key(keyName, size=2048)
```

[create_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-key-name--key-type----kwargs-) または [create_ec_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-ec-key-name----kwargs-) を使用することもできます。

`create` メソッドを呼び出すと、キー コンテナーに対する Azure REST API への呼び出しが生成されます。

要求を処理するとき、クライアントに提供した資格情報オブジェクトを使用して、Azure により、呼び出し元の ID (サービス プリンシパル) が認証されます。

また、呼び出し元が、要求されたアクションの実行を認可されているかどうかも確認されます。 この認可は、先ほど [`az keyvault set-policy` コマンド](#give-the-service-principal-access-to-your-key-vault)を使用して、サービス プリンシパルに付与しています。

## <a name="retrieve-a-key"></a>キーの取得

Key Vault からキーを読み取るには、[get_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#get-key-name--version-none----kwargs-) メソッドを使用します。

```python
retrieved_key = client.get_key(keyName)
 ```

また、Azure CLI コマンド [az keyvault key show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show) を使用してキーが設定されていることを確認することもできます。

### <a name="delete-a-key"></a>キーの削除

キーを削除するには、[begin_delete_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#begin-delete-key-name----kwargs-) メソッドを使用します。

```python
poller = client.begin_delete_key(keyName)
deleted_key = poller.result()
```

`begin_delete_key` メソッドは非同期であり、ポーラー オブジェクトを返します。 ポーラーの `result` メソッドを呼び出して、その完了を待機します。

キーが削除されたことを確認するには、Azure CLI コマンド [az keyvault key show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show) を使用します。

削除されると、キーは削除されたが回復可能な状態がしばらく維持されます。 コードをもう一度実行する場合は、別のキー名を使用します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[証明書](../certificates/quick-create-python.md)と[シークレット](../secrets/quick-create-python.md)も試してみる場合は、この記事で作成した Key Vault を再利用できます。

それ以外の場合は、この記事で作成したリソースが完了したら、次のコマンドを使用して、リソース グループとそれに含まれるすべてのリソースを削除します。

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>次のステップ

- [Azure Key Vault の概要](../general/overview.md)
- [Azure Key Vault 開発者ガイド](../general/developers-guide.md)
- [Azure Key Vault のベスト プラクティス](../general/best-practices.md)
- [Key Vault を使用した認証](../general/authentication.md)
