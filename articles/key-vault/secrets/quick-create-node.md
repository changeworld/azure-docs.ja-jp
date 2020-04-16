---
title: クイックスタート - Node.js 用 Azure Key Vault クライアント ライブラリ (v4)
description: Node.js クライアント ライブラリを使用して Azure キー コンテナーからシークレットを作成、取得、削除する方法について説明します
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 609076cd204457bb7c952c91cd249b1aece58022
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421456"
---
# <a name="quickstart-azure-key-vault-client-library-for-nodejs-v4"></a>クイック スタート:Node.js 用 Azure Key Vault クライアント ライブラリ (v4)

Node.js 用 Azure Key Vault クライアント ライブラリを使ってみます。 以下の手順に従ってパッケージをインストールし、基本タスクのコード例を試してみましょう。

Azure Key Vault は、クラウド アプリケーションやサービスで使用される暗号化キーとシークレットをセキュリティで保護するために役立ちます。 Node.js 用 Key Vault クライアント ライブラリは、次の目的で使用します。

- キーとパスワードのセキュリティと制御を強化する。
- 暗号化キーの作成とインポートを数分で実行する。
- クラウド スケールおよびグローバルな冗長性により待ち時間を短縮する。
- TLS または SSL 証明書のタスクを簡略化および自動化する。
- FIPS 140-2 レベル 2 への準拠が検証済みの HSM を使用する。

[API リファレンスのドキュメント](/javascript/api/overview/azure/key-vault?view=azure-node-latest) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | [パッケージ (npm)](https://www.npmjs.com/package/@azure/keyvault-secrets)

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 使用するオペレーティング システム用の最新の [Node.js](https://nodejs.org)。
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) または [Azure PowerShell](/powershell/azure/overview)。

このクイックスタートは、Linux ターミナル ウィンドウで [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) を実行していることを前提としています。

## <a name="setting-up"></a>設定

### <a name="install-the-package"></a>パッケージをインストールする

コンソール ウィンドウから、Node.js 用 Azure Key Vault シークレット ライブラリをインストールします。

```console
npm install @azure/keyvault-secrets
```

このクイックスタートでは、azure.identity パッケージもインストールする必要があります。

```console
npm install @azure/identity
```

### <a name="create-a-resource-group-and-key-vault"></a>リソース グループとキー コンテナーを作成する

このクイックスタートでは、あらかじめ作成しておいた Azure キー コンテナーを使用します。 キー コンテナーは、[Azure CLI のクイックスタート](quick-create-cli.md)、[Azure PowerShell のクイックスタート](quick-create-powershell.md)、または [Azure portal のクイックスタート](quick-create-portal.md)の手順に従って作成できます。 または、以下の Azure CLI コマンドを実行するだけでもかまいません。

> [!Important]
> 各キー コンテナーには一意の名前が必要です。 次の例では、<your-unique-keyvault-name> をお使いのキー コンテナーの名前に置き換えてください。

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>サービス プリンシパルの作成

クラウドベースのアプリケーションを認証するための最も簡単な方法は、マネージド ID を使用することです。詳細については、[App Service マネージド ID を使用した Azure Key Vault へのアクセス](../general/managed-identity.md)に関するページを参照してください。 ただし、このクイックスタートではわかりやすさを重視して、コンソール アプリケーションを作成します。 Azure でデスクトップ アプリケーションを認証するには、サービス プリンシパルとアクセス制御ポリシーを使用する必要があります。

Azure CLI の [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) コマンドを使用してサービス プリンシパルを作成します。

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

この操作では、一連のキーと値のペアが返されます。 

```azurecli
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

clientId および clientSecret を書き留めておきます。これらは、下記の手順「[環境変数の設定](#set-environmental-variables)」で使用します。

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>サービス プリンシパルにキー コンテナーへのアクセス権を付与する

[az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) コマンドに clientId を渡すことでご利用のサービス プリンシパルにアクセス許可を付与するアクセス ポリシーをご利用のキー コンテナー用に作成します。 キーとシークレットの両方に対する get、list、set の各アクセス許可をサービス プリンシパルに付与します。

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

#### <a name="set-environmental-variables"></a>環境変数の設定

アプリケーションの DefaultAzureCredential メソッドは、3 つの環境変数 (`AZURE_CLIENT_ID`、`AZURE_CLIENT_SECRET`、`AZURE_TENANT_ID`) に依存しています。 これらの変数を、`export VARNAME=VALUE` 形式を使用して「[サービス プリンシパルの作成](#create-a-service-principal)」の手順に記載されている clientId、clientSecret、tenantId の値に設定します。 (これにより、現在のシェルの変数とシェルから作成されたプロセスのみが設定されます。これらの変数を環境に永続的に追加するには、`/etc/environment ` ファイルを編集します)。 

また、キー コンテナー名を `KEY_VAULT_NAME` という環境変数として保存する必要もあります。

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>オブジェクト モデル

Node.js 用 Azure Key Vault クライアント ライブラリを使用すると、キーおよび関連するアセット (証明書、シークレットなど) を管理できます。 以下のコード サンプルでは、クライアントの作成、シークレットの設定、シークレットの取得、シークレットの削除を行う方法を示します。

コンソール アプリ全体は https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app から入手できます。

## <a name="code-examples"></a>コード例

### <a name="add-directives"></a>ディレクティブの追加

コードの先頭に次のディレクティブを追加します。

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");
```

### <a name="authenticate-and-create-a-client"></a>クライアントの認証と作成

キー コンテナーに対する認証とキー コンテナー クライアントの作成には、上記の「[環境変数の設定](#set-environmental-variables)」の手順にある環境変数と、[SecretClient コンストラクター](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#secretclient-string--tokencredential--pipelineoptions-)が必要です。 

キー コンテナーの名前は、`https://<your-key-vault-name>.vault.azure.net` という形式で、キー コンテナーの URI に展開されます。 

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new SecretClient(KVUri, credential);
```

### <a name="save-a-secret"></a>シークレットを保存する

アプリケーションが認証されたら、[client.setSecret メソッド](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#setsecret-string--string--setsecretoptions-)を使用して、キー コンテナーにシークレットを設定できます。これには、シークレットの名前が必要です (この例では、"mySecret" を使用します)。  

```javascript
await client.setSecret(secretName, secretValue);
```

シークレットが設定されたことは、[az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) コマンドを使用して確認できます。

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>シークレットを取得する

先ほど設定した値は、[client.getSecret メソッド](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#getsecret-string--getsecretoptions-)を使用して取得できます。

```javascript
const retrievedSecret = await client.getSecret(secretName);
 ```

これで、シークレットが `retrievedSecret.value` として保存されました。

### <a name="delete-a-secret"></a>シークレットを削除します

最後に、[client.beginDeleteSecret メソッド](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#begindeletesecret-string--begindeletesecretoptions-)を使用して、キー コンテナーからシークレットを削除してみましょう。

```javascript
await client.beginDeleteSecret(secretName)
```

[az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) コマンドを使用して、シークレットがなくなったことを確認できます。

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、Azure CLI または Azure PowerShell を使用して、キー コンテナーとそれに対応するリソース グループを削除できます。

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>サンプル コード

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");

const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {

  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new SecretClient(KVUri, credential);

  const secretName = "mySecret";
  var secretValue = await askQuestion("Input the value of your secret > ");

  console.log("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");
  await client.setSecret(secretName, secretValue);

  console.log("Done.");

  console.log("Forgetting your secret.");
  secretValue = "";
  console.log("Your secret is '" + secretValue + "'.");

  console.log("Retrieving your secret from " + keyVaultName + ".");

  const retrievedSecret = await client.getSecret(secretName);

  console.log("Your secret is '" + retrievedSecret.value + "'.");
  console.log("Deleting your secret from " + keyVaultName + " ...");

  await client.beginDeleteSecret(secretName);

  console.log("Done.");

}

main()

```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、キー コンテナーを作成し、シークレットを格納して、そのシークレットを取得しました。 Key Vault およびアプリケーションとの統合方法の詳細については、引き続き以下の記事を参照してください。

- [Azure Key Vault の概要](../general/overview.md)を確認する
- 「[Azure Key Vault 開発者ガイド](../general/developers-guide.md)」を参照する
- [Azure Key Vault のベスト プラクティス](../general/best-practices.md)を確認する