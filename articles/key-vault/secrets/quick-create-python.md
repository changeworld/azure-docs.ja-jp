---
title: クイックスタート - Python 用の Azure Key Vault クライアント ライブラリ - シークレットの管理
description: Python クライアント ライブラリを使用して Azure キー コンテナーからシークレットを作成、取得、削除する方法について学習します。
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: e06881d078b4e881174c3e931f7898cb622ad7f9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766355"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-python"></a>クイックスタート: Python 用 Azure Key Vault シークレット クライアント ライブラリ

Python 用 Azure Key Vault シークレット クライアント ライブラリを使ってみます。 以下の手順に従ってパッケージをインストールし、基本タスクのコード例を試してみましょう。 Key Vault を使用してシークレットを保存することで、シークレットをコードに保存しなくて済むため、アプリのセキュリティが向上します。

[API のリファレンスのドキュメント](/python/api/overview/azure/keyvault-secrets-readme) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets) | [パッケージ (Python Package Index)](https://pypi.org/project/azure-keyvault-secrets/)

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
    pip install azure-identity
    ```


1. Key Vault シークレット ライブラリをインストールします。

    ```terminal
    pip install azure-keyvault-secrets
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

```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="create-the-sample-code"></a>サンプル コードを作成する

シークレットは、Python 用 Azure Key Vault シークレット クライアント ライブラリを使用して管理できます。 以下のコード サンプルでは、クライアントの作成、シークレットの設定、シークレットの取得、シークレットの削除を行う方法を示します。

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

- アクセス許可エラーが発生した場合は、[`az keyvault set-policy` コマンド](#grant-access-to-your-key-vault)を実行したことを確認してください。
- 同じシークレット名を使用してコードを再実行すると、"(競合) シークレット <name> は現在削除されているが、回復可能な状態です" というエラーが生成されることがあります。 別のシークレット名を使用してください。

## <a name="code-details"></a>コードの詳細

### <a name="authenticate-and-create-a-client"></a>クライアントの認証と作成

このクイックスタートでは、ログイン ユーザーを使用してキー コンテナーに対する認証を行います。ローカル開発では、これが推奨される方法となります。 Azure にデプロイされるアプリケーションの場合は、App Service または仮想マシンにマネージド ID を割り当てる必要があります。詳細については、[マネージド ID の概要](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)に関するページを参照してください。

以下の例では、キー コンテナーの名前は、"https://\<your-key-vault-name\>.vault.azure.net" という形式で、キー コンテナーの URI に展開されます。 この例では、["DefaultAzureCredential()"](https://docs.microsoft.com/python/api/azure-identity/azure.identity.defaultazurecredential) クラスを使用しています。環境や使用するオプションが変わっても、同じコードを使用して ID を提供することができます。 詳細については、[DefaultAzureCredential 認証](https://docs.microsoft.com/python/api/overview/azure/identity-readme)に関するセクションを参照してください。 

```python
credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>シークレットを保存する

キー コンテナーのクライアント オブジェクトを取得したら、[set_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#set-secret-name--value----kwargs-) メソッドを使用してシークレットを格納できます。 

```python
client.set_secret(secretName, secretValue)
```

`set_secret` を呼び出すと、キー コンテナーに対する Azure REST API への呼び出しが生成されます。

要求を処理するとき、クライアントに提供した資格情報オブジェクトを使用して、Azure により、呼び出し元の ID (サービス プリンシパル) が認証されます。

### <a name="retrieve-a-secret"></a>シークレットを取得する

Key Vault からシークレットを読み取るには、[get_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#get-secret-name--version-none----kwargs-) メソッドを使用します。

```python
retrieved_secret = client.get_secret(secretName)
 ```

シークレット値は `retrieved_secret.value` に含まれています。

また、Azure CLI コマンド [az keyvault secret show](/cli/azure/keyvault/secret?#az_keyvault_secret_show) を使用してシークレットを取得することもできます。

### <a name="delete-a-secret"></a>シークレットを削除します

シークレットを削除するには、[begin_delete_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#begin-delete-secret-name----kwargs-) メソッドを使用します。

```python
poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()
```

`begin_delete_secret` メソッドは非同期であり、ポーラー オブジェクトを返します。 ポーラーの `result` メソッドを呼び出して、その完了を待機します。

シークレットが削除されたことを確認するには、Azure CLI コマンド [az keyvault secret show](/cli/azure/keyvault/secret?#az_keyvault_secret_show) を使用します。

削除されると、シークレットは削除されたが回復可能な状態がしばらく維持されます。 コードをもう一度実行する場合は、別のシークレット名を使用します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[証明書](../certificates/quick-create-python.md)と[キー](../keys/quick-create-python.md)も試してみる場合は、この記事で作成した Key Vault を再利用できます。

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
