---
title: クイックスタート - Python 用の Azure Key Vault クライアント ライブラリ - シークレットの管理
description: Python クライアント ライブラリを使用して Azure キー コンテナーからシークレットを作成、取得、削除する方法について学習します。
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: cd8a5751c018b9b3b3b2ef96765545f2edab685b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2020
ms.locfileid: "89489206"
---
# <a name="quickstart-azure-key-vault-secrets-client-library-for-python"></a>クイック スタート:Python 用 Azure Key Vault シークレット クライアント ライブラリ

Python 用 Azure Key Vault クライアント ライブラリを使ってみます。 以下の手順に従ってパッケージをインストールし、基本タスクのコード例を試してみましょう。 Key Vault を使用してシークレットを保存することで、シークレットをコードに保存しなくて済むため、アプリのセキュリティが向上します。

[API のリファレンスのドキュメント](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets) | [パッケージ (Python Package Index)](https://pypi.org/project/azure-keyvault-secrets/)

## <a name="set-up-your-local-environment"></a>ローカル環境を設定する

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. Key Vault シークレット ライブラリをインストールします。

    ```terminal
    pip install azure-keyvault-secrets
    ```

## <a name="create-a-resource-group-and-key-vault"></a>リソース グループとキー コンテナーを作成する

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>サービス プリンシパルにキー コンテナーへのアクセス権を付与する

次の [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) コマンドを実行して、シークレットに対する get、list、および set 操作のサービス プリンシパルを承認します。 このコマンドは、前の手順で作成した `KEY_VAULT_NAME` および `AZURE_CLIENT_ID` 環境変数に依存しています。

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --secret-permissions delete get list set 
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --secret-permissions delete get list set 
```

---

このコマンドは、前の手順で作成した `KEY_VAULT_NAME` および `AZURE_CLIENT_ID` 環境変数に依存しています。

詳細については、[アクセス ポリシーの割り当て - CLI](../general/assign-access-policy-cli.md) に関するページを参照してください。

## <a name="create-the-sample-code"></a>サンプル コードを作成する

Python 用 Azure Key Vault クライアント ライブラリを使用すると、シークレットおよび関連するアセット (証明書、暗号化キーなど) を管理できます。 以下のコード サンプルでは、クライアントの作成、シークレットの設定、シークレットの取得、シークレットの削除を行う方法を示します。

このコードを含めた *kv_secrets.py* という名前のファイルを作成します。

```python
import os
import cmd
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = f"https://{keyVaultName}.vault.azure.net"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)

secretName = input("Input a name for your secret > ")
secretValue = input("Input a value for your secret > ")

print(f"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...")

client.set_secret(secretName, secretValue)

print(" done.")

print(f"Retrieving your secret from {keyVaultName}.")

retrieved_secret = client.get_secret(secretName)

print(f"Your secret is '{retrieved_secret.value}'.")
print(f"Deleting your secret from {keyVaultName} ...")

poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>コードの実行

前のセクションのコードが *kv_secrets.py* という名前のファイルに含まれていることを確認します。 次のコマンドを使用して、コードを実行します。

```terminal
python kv_secrets.py
```

- アクセス許可エラーが発生した場合は、[`az keyvault set-policy` コマンド](#give-the-service-principal-access-to-your-key-vault)を実行したことを確認してください。
- 同じシークレット名を使用してコードを再実行すると、"(競合) シークレット <name> は現在削除されているが、回復可能な状態です" というエラーが生成されることがあります。 別のシークレット名を使用してください。

## <a name="code-details"></a>コードの詳細

### <a name="authenticate-and-create-a-client"></a>クライアントの認証と作成

前述のコードでは、[`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) オブジェクトにより、サービス プリンシパル用に作成した環境変数が使用されます。 この資格情報は、[`SecretClient`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python) など、Azure ライブラリからクライアント オブジェクトを作成するたびに、そのクライアントを使用して操作するリソースの URI と共に指定します。

```python
credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>シークレットを保存する

キー コンテナーのクライアント オブジェクトを取得したら、[set_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#set-secret-name--value----kwargs-) メソッドを使用してシークレットを格納できます。 

```python
client.set_secret(secretName, secretValue)
```

`set_secret` を呼び出すと、キー コンテナーに対する Azure REST API への呼び出しが生成されます。

要求を処理するとき、クライアントに提供した資格情報オブジェクトを使用して、Azure により、呼び出し元の ID (サービス プリンシパル) が認証されます。

また、呼び出し元が、要求されたアクションの実行を認可されているかどうかも確認されます。 この認可は、先ほど [`az keyvault set-policy` コマンド](#give-the-service-principal-access-to-your-key-vault)を使用して、サービス プリンシパルに付与しています。

### <a name="retrieve-a-secret"></a>シークレットを取得する

Key Vault からシークレットを読み取るには、[get_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#get-secret-name--version-none----kwargs-) メソッドを使用します。

```python
retrieved_secret = client.get_secret(secretName)
 ```

シークレット値は `retrieved_secret.value` に含まれています。

また、Azure CLI コマンド [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) を使用してシークレットを取得することもできます。

### <a name="delete-a-secret"></a>シークレットを削除します

シークレットを削除するには、[begin_delete_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#begin-delete-secret-name----kwargs-) メソッドを使用します。

```python
poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()
```

`begin_delete_secret` メソッドは非同期であり、ポーラー オブジェクトを返します。 ポーラーの `result` メソッドを呼び出して、その完了を待機します。

シークレットが削除されたことを確認するには、Azure CLI コマンド [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) を使用します。

削除されると、シークレットは削除されたが回復可能な状態がしばらく維持されます。 コードをもう一度実行する場合は、別のシークレット名を使用します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[証明書](../certificates/quick-create-python.md)と[キー](../keys/quick-create-python.md)も試してみる場合は、この記事で作成した Key Vault を再利用できます。

それ以外の場合は、この記事で作成したリソースが完了したら、次のコマンドを使用して、リソース グループとそれに含まれるすべてのリソースを削除します。

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>次のステップ

- [Azure Key Vault の概要](../general/overview.md)
- [Azure Key Vault 開発者ガイド](../general/developers-guide.md)
- [Azure Key Vault のベスト プラクティス](../general/best-practices.md)
- [Key Vault を使用した認証](../general/authentication.md)
