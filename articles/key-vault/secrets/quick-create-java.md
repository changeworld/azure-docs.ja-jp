---
title: クイックスタート -  Java 用 Azure Key Vault クライアント ライブラリ
description: Azure SDK クライアント ライブラリのクイックスタートを作成するための形式とコンテンツの基準を示します。
author: msmbaldwin
ms.custom: devx-track-java
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 87d7bbaa40226e02726b92cf7f7705c8028149f7
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019632"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-java"></a>クイックスタート: Java 用 Azure Key Vault シークレット クライアント ライブラリ

Java 用 Azure Key Vault シークレット クライアント ライブラリを使ってみます。 以下の手順に従ってパッケージをインストールし、基本タスクのコード例を試してみましょう。

その他のリソース:

* [ソース コード](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault)
* [API リファレンス ドキュメント](https://azure.github.io/azure-sdk-for-java)
* [製品ドキュメント](index.yml)
* [サンプル](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets/src/samples/java/com/azure/security/keyvault/secrets)

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Java Development Kit (JDK)](/java/azure/jdk/) バージョン 8 以降
- [Apache Maven](https://maven.apache.org)
- [Azure CLI](/cli/azure/install-azure-cli)

このクイックスタートは、Linux ターミナル ウィンドウで [Azure CLI](/cli/azure/install-azure-cli) と [Apache Maven](https://maven.apache.org) を実行していることを前提としています。

## <a name="setting-up"></a>設定

このクイックスタートでは、Azure CLI と Azure Identity ライブラリを使用して、Azure サービスに対するユーザーの認証を行います。 開発者は、Visual Studio または Visual Studio Code を使用してその呼び出しを認証することもできます。詳細については、[Azure Identity クライアント ライブラリを使用してクライアントを認証する](https://docs.microsoft.com/java/api/overview/azure/identity-readme)方法に関するページを参照してください。

### <a name="sign-in-to-azure"></a>Azure へのサインイン

1. `login` コマンドを実行します。

    ```azurecli-interactive
    az login
    ```

    CLI で既定のブラウザーを開くことができる場合、開いたブラウザに Azure サインイン ページが読み込まれます。

    それ以外の場合は、[https://aka.ms/devicelogin](https://aka.ms/devicelogin) でブラウザー ページを開き、ターミナルに表示されている認証コードを入力します。

2. ブラウザーでアカウントの資格情報を使用してサインインします。

### <a name="create-new-java-console-app"></a>新しい Java コンソール アプリを作成する

コンソール ウィンドウで、`mvn` コマンドを使用し、`akv-java` という名前で新しい Java コンソール アプリを作成します。

```console
mvn archetype:generate -DgroupId=com.keyvault.quickstart
                       -DartifactId=akv-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

プロジェクトの生成からの出力は、次のようになります。

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.quickstart
[INFO] Parameter: artifactId, Value: akv-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.quickstart
[INFO] Parameter: artifactId, Value: akv-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

新しく作成された akv-java/ フォルダーにディレクトリを変更します。

```console
cd akv-java
```

### <a name="install-the-package"></a>パッケージをインストールする

テキスト エディターで *pom.xml* ファイルを開きます。 依存関係のグループに、次の dependency 要素を追加します。

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-secrets</artifactId>
      <version>4.0.0</version>
    </dependency>

    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-identity</artifactId>
      <version>1.0.0</version>
    </dependency>
```

### <a name="create-a-resource-group-and-key-vault"></a>リソース グループとキー コンテナーを作成する

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

#### <a name="grant-access-to-your-key-vault"></a>キー コンテナーへのアクセス許可を付与する

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
$Env:KEY_VAULT_NAME=<your-key-vault-name>
```

macOS または Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>オブジェクト モデル

シークレットは、Java 用 Azure Key Vault シークレット クライアント ライブラリを使用して管理できます。 クライアントの作成、シークレットの設定、シークレットの取得、シークレットの削除を行う方法を「[コード例](#code-examples)」セクションで紹介しています。

コンソール アプリ全体は[以下](#sample-code)にあります。

## <a name="code-examples"></a>コード例

### <a name="add-directives"></a>ディレクティブの追加

コードの先頭に次のディレクティブを追加します。

```java
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret;
```

### <a name="authenticate-and-create-a-client"></a>クライアントの認証と作成

このクイックスタートでは、ログイン ユーザーを使用してキー コンテナーに対する認証を行います。ローカル開発では、これが推奨される方法となります。 Azure にデプロイされるアプリケーションの場合は、App Service または仮想マシンにマネージド ID を割り当てる必要があります。詳細については、[マネージド ID の概要](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)に関するページを参照してください。

以下の例では、キー コンテナーの名前は、"https://\<your-key-vault-name\>.vault.azure.net" という形式で、キー コンテナーの URI に展開されます。 この例では、["DefaultAzureCredential()"](https://docs.microsoft.com/java/api/com.azure.identity.defaultazurecredential) クラスを使用しています。環境や使用するオプションが変わっても、同じコードを使用して ID を提供することができます。 詳細については、[DefaultAzureCredential 認証](https://docs.microsoft.com/java/api/overview/azure/identity-readme)に関するセクションを参照してください。 

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String kvUri = "https://" + keyVaultName + ".vault.azure.net";

SecretClient secretClient = new SecretClientBuilder()
    .vaultUrl(kvUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="save-a-secret"></a>シークレットを保存する

アプリケーションが認証されたら、`secretClient.setSecret` メソッドを使用して、キー コンテナーにシークレットを設定できます。 これにはシークレットの名前が必要です。このサンプルでは、`secretName` 変数に "mySecret" という値を代入しました。  

```java
secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));
```

シークレットが設定されたことは、[az keyvault secret show](/cli/azure/keyvault/secret?#az-keyvault-secret-show) コマンドを使用して確認できます。

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>シークレットを取得する

先ほど設定した値は、`secretClient.getSecret` メソッドを使用して取得できます。

```java
KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);
 ```

取得したシークレットの値には、`retrievedSecret.getValue()` を使用してアクセスできます。

### <a name="delete-a-secret"></a>シークレットを削除します

最後に、`secretClient.beginDeleteSecret` メソッドを使用して、キー コンテナーからシークレットを削除してみましょう。

```java
secretClient.beginDeleteSecret(secretName);
```

[az keyvault secret show](/cli/azure/keyvault/secret?#az-keyvault-secret-show) コマンドを使用して、シークレットがなくなったことを確認できます。

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

```java
package com.keyvault.quickstart;

import java.io.Console;   

import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret;

public class App {

    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {

        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String kvUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and kv uri = %s \n", keyVaultName, kvUri);

        SecretClient secretClient = new SecretClientBuilder()
            .vaultUrl(kvUri)
            .credential(new DefaultAzureCredentialBuilder().build())
            .buildClient();


        Console con = System.console();  

        String secretName = "mySecret";

        System.out.println("Input the value of your secret > ");
        String secretValue = con.readLine();

        System.out.print("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ... ");

        secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));

        System.out.println("done.");

        System.out.println("Forgetting your secret.");
        secretValue = "";
        System.out.println("Your secret is '" + secretValue + "'.");

        System.out.println("Retrieving your secret from " + keyVaultName + ".");

        KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);

        System.out.println("Your secret is '" + retrievedSecret.getValue() + "'.");
        System.out.print("Deleting your secret from " + keyVaultName + " ... ");

        secretClient.beginDeleteSecret(secretName);

        System.out.println("done.");


    }
}
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、キー コンテナーを作成し、シークレットを格納して、そのシークレットを取得しました。 Key Vault およびアプリケーションとの統合方法の詳細については、引き続き以下の記事を参照してください。

- [Azure Key Vault の概要](../general/overview.md)を確認する
- 「[Azure Key Vault 開発者ガイド](../general/developers-guide.md)」を参照する
- [キー コンテナーへのアクセスをセキュリティで保護する](../general/secure-your-key-vault.md)方法
- [Azure Key Vault のベスト プラクティス](../general/best-practices.md)を確認する
