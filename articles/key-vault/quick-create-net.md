---
title: Azure クイック スタート - Key Vault との間でシークレットの設定と取得を行う Azure Web アプリケーションの構成 | Microsoft Docs
description: Key Vault との間でシークレットの設定と取得を行う ASP.Net Core アプリケーションの構成方法を紹介したクイック スタート
services: key-vault
author: prashanthyv
manager: sumedhb
ms.service: key-vault
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 8b5624ae3083d92213b4ee919dc0860bf5ff4ab7
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39480204"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-a-net-web-app"></a>クイック スタート: .NET Web アプリを使用して Azure Key Vault との間でシークレットの設定と取得を行う

このクイック スタートでは、Azure Web アプリケーションを取得し、マネージド サービス ID を使用してキー コンテナーから情報を読み取るために必要な手順を学習します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * キー コンテナーを作成する
> * キー コンテナーにシークレットを格納する
> * キー コンテナーからシークレットを取得する
> * Azure AD Web アプリケーションを作成する
> * [マネージド サービス ID を有効にする](../active-directory/managed-service-identity/overview.md)
> * Web アプリケーションに必要なアクセス許可を付与して、キー コンテナーからデータを読み取る

先に進める前に、特に[マネージド サービス ID](../active-directory/managed-service-identity/overview.md) の[基本概念](key-vault-whatis.md#basic-concepts)を確認してください。

## <a name="prerequisites"></a>前提条件

* Windows の場合:
  * 次のワークロードでは [Visual Studio 2017 バージョン 15.7.3 以降](https://www.microsoft.com/net/download/windows)。
    * ASP.NET および Web の開発
    * .NET Core クロスプラットフォームの開発
  * [.NET Core 2.1 SDK 以降](https://www.microsoft.com/net/download/windows)

* Mac の場合:
  * https://visualstudio.microsoft.com/vs/mac/

* すべてのプラットフォーム:
  * Git は[こちら](https://git-scm.com/downloads)からダウンロードしてください。
  * Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Azure CLI バージョン 2.0.4 以降が必要です。 これは、Windows、Mac、Linux に対応しています。

## <a name="login-to-azure"></a>Azure にログインする

   CLI を使用して Azure にログインするには、次のように入力します。

```azurecli
az login
```

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#az-group-create) コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

リソース グループ名を選択し、プレース ホルダーを入力してください。
次の例では、*<YourResourceGroupName>* という名前のリソース グループを *eastus* の場所に作成します。

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

作成したリソース グループは、この記事の全体を通して使用します。

## <a name="create-an-azure-key-vault"></a>Azure Key Vault を作成する

次に、前の手順で作成したリソース グループにキー コンテナーを作成します。 次の情報を指定します。

* コンテナー名 - **こちらでキー コンテナー名を選択してください**。 キー コンテナーの名前は、0 ～ 9、a ～ z、A ～ Z、- のみを使った 3 ～ 24 文字の文字列である必要があります。
* リソース グループ名 -**こちらでリソース グループ名を選択してください**。
* 場所: **米国東部**。

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

この時点で、自分の Azure アカウントが唯一、この新しいコンテナーで任意の操作を実行することを許可されています。

## <a name="add-a-secret-to-key-vault"></a>Key Vault にシークレットを追加する

シークレットのしくみをよく理解できるように、シークレットを追加します。 SQL 接続文字列やその他の情報を機密として保持する必要があるのに、アプリケーションで使用可能になるように保管している場合があります。 このチュートリアルでは、パスワードを **AppSecret** と呼び、このパスワード内に **MySecret** という値を格納します。

次のコマンドを入力して、値 **MySecret** を保存する **AppSecret** というシークレットをキー コンテナーに作成します。

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

シークレットに格納されている値をプレーンテキストとして表示するには:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

このコマンドは、URI を含むシークレットの情報を表示します。 これらの手順を完了すると、Azure Key Vault のシークレットへの URI がわかります。 この情報をメモしてください。 後の手順で必要になります。

## <a name="clone-the-repo"></a>リポジトリを複製する

次のコマンドを実行してリポジトリを複製し、ソースを編集するためのローカル コピーを作成します。

```
git clone https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart.git
```

## <a name="open-and-edit-the-solution"></a>ソリューションを開いて編集する

program.cs ファイルを編集し、特定のキー コンテナー名でサンプルを実行します。

1. フォルダー key-vault-dotnet-core-quickstart に移動する
2. Visual Studio 2017 で、key-vault-dotnet-core-quickstart.sln ファイルを開く
3. Program.cs ファイルを開き、プレースホルダー <YourKeyVaultName> を先ほど作成したキー コンテナーの名前に更新する

このソリューションでは、[AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) ライブラリと [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet ライブラリが使用されます。

## <a name="run-the-app"></a>アプリの実行

Visual Studio 2017 のメイン メニューで、[デバッグ] > [デバッグなしで開始] と選択します。 ブラウザーが表示されたら、[About]\(詳細\) ページに移動します。 AppSecret の値が表示されます。

## <a name="publish-the-web-application-to-azure"></a>Azure に Web アプリケーションを発行する

このアプリを Azure に発行し、Web アプリとしてライブであることと、シークレット値を取得することを確認します。

1. Visual Studio で、**key-vault-dotnet-core-quickstart** プロジェクトを選択します。
2. **[発行]**、**[開始]** の順に選択します。
3. 新しい **App Service** を作成し、**[発行]** を選択します。
4. アプリ名を "keyvaultdotnetcorequickstart" に変更します。
5. **作成**を選択します。

>[!VIDEO https://sec.ch9.ms/ch9/e93d/a6ac417f-2e63-4125-a37a-8f34bf0fe93d/KeyVault_high.mp4]

## <a name="enable-managed-service-identities-msi"></a>マネージド サービス ID (MSI) を有効にする

Azure Key Vault は、資格情報およびその他のキーやシークレットを安全に保管する方法を提供しますが、コードは Azure Key Vault に認証してそれらを取得する必要があります。 マネージド サービス ID (MSI) は、Azure Active Directory (Azure AD) で自動的に管理されている ID を Azure サービスに付与することで、これを簡単にします。 この ID を使用して、コードに資格情報が含まれていなくても、Key Vault を含む Azure AD の認証をサポートする任意のサービスに認証することができます。

1. Azure CLI に戻ります。
2. assign-identity コマンドを実行して、このアプリケーションの ID を作成します。

```azurecli
az webapp identity assign --name "keyvaultdotnetcorequickstart" --resource-group "<YourResourceGroupName>"
```

>[!NOTE]
>このコマンドは、ポータルに移動して、Web アプリケーション プロパティの **[マネージド サービス ID]** を **[オン]** に切り替えることと同等です。

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>アプリケーションにアクセス許可を割り当ててキー コンテナーからシークレットを読み取る

[アプリケーションを Azure から発行する][]ときの出力をメモします。 次の形式にする必要があります。
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
次に、上でコピーしたキー コンテナーの名前と PrincipalId の値を使用してこのコマンドを実行します。

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get

```

## <a name="next-steps"></a>次の手順

* [Azure Key Vault のホーム ページ](https://azure.microsoft.com/services/key-vault/)
* [Azure Key Vault のドキュメント](https://docs.microsoft.com/azure/key-vault/)
* [Azure SDK for .NET](https://github.com/Azure/azure-sdk-for-net)
* [Azure REST API リファレンス](https://docs.microsoft.com/rest/api/keyvault/)
