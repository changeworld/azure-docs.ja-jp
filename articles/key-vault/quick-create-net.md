---
title: クイック スタート:ノード Web アプリを使用して Azure Key Vault との間でシークレットの設定と取得を行う - Azure Key Vault | Microsoft Docs
description: クイック スタート:.NET Web アプリを使用して Azure Key Vault との間でシークレットの設定と取得を行う
services: key-vault
author: prashanthyv
manager: sumedhb
ms.service: key-vault
ms.topic: quickstart
ms.date: 01/02/2019
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 20d47ecaea8ce393f60cba93c3dbcf7ca4a076c8
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2019
ms.locfileid: "54002605"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-by-using-a-net-web-app"></a>クイック スタート:.NET Web アプリを使用して Azure Key Vault との間でシークレットの設定と取得を行う

このクイック スタートでは、Azure Web アプリケーションを取得し、Azure リソースのマネージド ID を使用して Azure Key Vault から情報を読み取るために必要な手順を学習します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * Key Vault を作成します。
> * キー コンテナーにシークレットを格納する。
> * キー コンテナーからシークレットを取得する。
> * Azure AD Web アプリケーションを作成する。
> * Web アプリの[マネージド サービス ID](../active-directory/managed-identities-azure-resources/overview.md) を有効にする。
> * Web アプリケーションに必要なアクセス許可を付与して、キー コンテナーからデータを読み取る

先に進む前に、[基本概念](key-vault-whatis.md#basic-concepts)を確認してください。

>[!NOTE]
>Key Vault は、プログラムでシークレットを格納できる中央リポジトリです。 しかしこれを実行するには、アプリケーションとユーザーが最初に Key Vault に対する認証を行う (シークレットを提示する) 必要があります。 セキュリティのベスト プラクティスに従うために、最初のシークレットのローテーションが定期的に行われる必要があります。 
>
>[Azure リソースのマネージド サービス ID](../active-directory/managed-identities-azure-resources/overview.md) を使用すると、Azure で実行されるアプリケーションには、Azure が自動的に管理する ID が付与されます。 これにより、*シークレット導入問題*が解決されます。ユーザーとアプリケーションはベスト プラクティスに従うことができ、最初のシークレットのローテーションについて心配する必要がありません

## <a name="prerequisites"></a>前提条件

* Windows の場合:
  * 次のワークロードでは [Visual Studio 2017 バージョン 15.7.3 以降](https://www.microsoft.com/net/download/windows)。
    * ASP.NET および Web の開発
    * .NET Core クロスプラットフォームの開発
  * [.NET Core 2.1 SDK 以降](https://www.microsoft.com/net/download/windows)

* Mac の場合:
  * [Visual Studio for Mac の新機能](https://visualstudio.microsoft.com/vs/mac/)に関するページを参照してください。

* すべてのプラットフォーム:
  * Git ([ダウンロード](https://git-scm.com/downloads))。
  * Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) バージョン 2.0.4 以降。 これは、Windows、Mac、Linux に対応しています。

## <a name="log-in-to-azure"></a>Azure にログインする

Azure CLI を使用して Azure にログインするには、次のように入力します。

```azurecli
az login
```

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#az-group-create) コマンドを使ってリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

リソース グループ名を選択し、プレース ホルダーを入力します。
次の例では、米国東部リージョンにリソース グループを作成します。

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

作成したリソース グループは、この記事の全体を通して使用します。

## <a name="create-a-key-vault"></a>Key Vault を作成します

次に、前の手順で作成したリソース グループにキー コンテナーを作成します。 次の情報を指定します。

* キー コンテナー名:名前の文字数は 3 から 24 文字です。使用できる文字は 0-9、a-z、A-Z、および - のみです。
* リソース グループ名。
* 場所:**米国東部**。

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

この時点で、使用している Azure アカウントのみが、この新しいコンテナーで任意の操作を実行することが許可されます。

## <a name="add-a-secret-to-the-key-vault"></a>キー コンテナーにシークレットを追加する

シークレットのしくみをよく理解できるように、シークレットを追加します。 SQL 接続文字列やその他の情報を機密として保持する必要があるのに、アプリケーションで使用可能になるように保管している場合があります。

次のコマンドを入力して、**AppSecret** というキー コンテナーにシークレットを作成します。 このシークレットには、値 **MySecret** が格納されます。

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

シークレットに格納されている値をプレーンテキストとして表示するには:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

このコマンドは、URI を含むシークレットの情報を表示します。 これらの手順を完了すると、キー コンテナーのシークレットへの URI がわかります。 この情報をメモしてください。 後の手順で必要になります。

## <a name="clone-the-repo"></a>リポジトリを複製する

リポジトリを複製して、ソースを編集できるローカル コピーを作成します。 次のコマンドを実行します。

```
git clone https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart.git
```

## <a name="open-and-edit-the-solution"></a>ソリューションを開いて編集する

program.cs ファイルを編集し、特定のキー コンテナー名でサンプルを実行します。

1. フォルダー key-vault-dotnet-core-quickstart を参照します。
2. Visual Studio 2017 で、key-vault-dotnet-core-quickstart.sln ファイルを開きます。
3. Program.cs ファイルを開き、プレースホルダー *YourKeyVaultName* を先ほど作成したキー コンテナーの名前に更新します。

このソリューションでは、[AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) ライブラリと [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet ライブラリが使用されます。

## <a name="run-the-app"></a>アプリの実行

Visual Studio 2017 のメイン メニューで、**[デバッグ]** > **[デバッグなしで開始]** と選択します。 ブラウザーが表示されたら、**[バージョン情報]** ページに移動します。 **AppSecret** の値が表示されます。

## <a name="publish-the-web-application-to-azure"></a>Azure に Web アプリケーションを発行する

このアプリを Azure に発行し、Web アプリとしてライブであることと、シークレット値を取得することを確認します。

1. Visual Studio で、**key-vault-dotnet-core-quickstart** プロジェクトを選択します。
2. **[発行]** > **[開始]** の順に選択します。
3. 新しい **App Service** を作成し、**[発行]** を選択します。
4. アプリ名を **keyvaultdotnetcorequickstart** に変更します。
5. **作成**を選択します。

>[!VIDEO https://sec.ch9.ms/ch9/e93d/a6ac417f-2e63-4125-a37a-8f34bf0fe93d/KeyVault_high.mp4]

## <a name="enable-a-managed-identity-for-the-web-app"></a>Web アプリのマネージド ID を有効にする

Azure Key Vault は、資格情報およびその他のキーやシークレットを安全に保管する方法を提供しますが、コードは Key Vault に認証してそれらを取得する必要があります。 [Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) は、Azure Active Directory (Azure AD) で自動的に管理されている ID を Azure サービスに付与することで、この問題を簡単に解決します。 この ID を使用して、コードに資格情報が含まれていなくても、Key Vault を含む Azure AD の認証をサポートする任意のサービスに認証することができます。

1. Azure CLI に戻ります。
2. assign-identity コマンドを実行して、このアプリケーションの ID を作成します。

   ```azurecli
   az webapp identity assign --name "keyvaultdotnetcorequickstart" --resource-group "<YourResourceGroupName>"
   ```

>[!NOTE]
>この手順のコマンドは、ポータルに移動して、Web アプリケーション プロパティの **[Identity / System assigned]\(ID/システム割り当て済み\)** を **[オン]** に切り替えることと同等です。

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>アプリケーションにアクセス許可を割り当ててキー コンテナーからシークレットを読み取る

アプリケーションを Azure に発行するときの出力をメモします。 次の形式にする必要があります。
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
次に、キー コンテナーの名前と **PrincipalId** の値を使用してこのコマンドを実行します。

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list

```

これで、アプリケーションを実行すると、取得されたシークレットの値が表示されます。 上記のコマンドでは、お使いのキー コンテナー上で **get** および **list** 操作を行うために、App Service アクセス許可の ID (MSI) を付与しています。

## <a name="next-steps"></a>次の手順

* [Key Vault についての詳細情報](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)
* [Azure SDK for .NET](https://github.com/Azure/azure-sdk-for-net)
* [Azure REST API リファレンス](https://docs.microsoft.com/rest/api/keyvault/)
