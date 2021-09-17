---
title: JavaScript アプリで Azure App Configuration を使用するためのクイックスタート | Microsoft Docs
description: このクイックスタートでは、Azure App Configuration を使用して Node.js アプリを作成します。コードとは別に、アプリケーション設定のストレージと管理を一元化します。
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: quickstart
ms.date: 07/12/2021
ms.author: drewbat
ms.openlocfilehash: 850771db454f854243ac9ba242d02ed1911f255c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121787903"
---
# <a name="quickstart-create-a-javascript-app-with-azure-app-configuration"></a>クイックスタート: Azure App Configuration を使用した JavaScript アプリの作成

このクイックスタートでは、[JavaScript 用の App Configuration クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/appconfiguration/app-configuration/README.md)を使用して、Azure App Configuration でアプリケーション設定のストレージと管理を一元化します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)
- [Node.js の LTS バージョン](https://nodejs.org/en/about/releases/)。 Node.js を Windows に直接インストールするか、Linux 用 Windows サブシステム (WSL) を使用してインストールする方法については、「[Node.js の概要](/windows/dev-environment/javascript/nodejs-overview)」を参照してください。

## <a name="create-an-app-configuration-store"></a>App Configuration ストアを作成する

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. **[構成エクスプローラー]**  >  **[+ 作成]**  >  **[キー値]** の順に選択して、次のキーと値のペアを追加します。

    | Key | 値 |
    |---|---|
    | TestApp:Settings:Message | Azure App Configuration からのデータ |

    **[ラベル]** と **[コンテンツの種類]** は、現時点では空にしておきます。

8. **[適用]** を選択します。

## <a name="setting-up-the-nodejs-app"></a>Node.js アプリの設定

1. このチュートリアルでは、プロジェクト用に *app-configuration-quickstart* という名前の新しいディレクトリを作成します。

    ```console
    mkdir app-configuration-quickstart
    ```

1. 新しく作成した *app-configuration-quickstart* ディレクトリに切り替えます。

    ```console
    cd app-configuration-quickstart
    ```

1. `npm install` コマンドを使用して、Azure App Configuration クライアント ライブラリをインストールします。

    ```console
    npm install @azure/app-configuration
    ```

1. *app-configuration-quickstart* ディレクトリに *app.js* という名前の新しいファイルを作成し、次のコードを追加します。

   ```javascript
   const appConfig = require("@azure/app-configuration");
   ```

## <a name="configure-your-connection-string"></a>接続文字列の構成

1. **AZURE_APP_CONFIG_CONNECTION_STRING** という名前の環境変数を設定し、それを自分の App Configuration ストアへのアクセス キーに設定します。 コマンド ラインで次のコマンドを実行します。

    ### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell
    $Env:AZURE_APP_CONFIG_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    ### <a name="command-line"></a>[コマンド ライン](#tab/command-line)

    ```cmd
    setx AZURE_APP_CONFIG_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    ### <a name="macos"></a>[macOS](#tab/macOS)
    ```console
    export AZURE_APP_CONFIG_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

    ---

2. 変更を有効にするために、コマンド プロンプトを再起動します。 環境変数の値を出力して、正しく設定されていることを確認します。

## <a name="connect-to-an-app-configuration-store"></a>App Configuration ストアに接続する

次のコード スニペットでは、ご自分の環境変数に格納された接続文字列を使用して、**AppConfigurationClient** のインスタンスを作成します。

```javascript
const connection_string = process.env.AZURE_APP_CONFIG_CONNECTION_STRING;
const client = new appConfig.AppConfigurationClient(connection_string);
```

## <a name="get-a-configuration-setting"></a>構成設定を取得する

次のコード スニペットでは、`key` 名を指定して構成設定を取得します。 この例に示すキーは、この記事の前の手順で作成しました。

```javascript
async function run() {
  
  let retrievedSetting = await client.getConfigurationSetting({
    key: "TestApp:Settings:Message"
  });

  console.log("Retrieved value:", retrievedSetting.value);
}

run().catch((err) => console.log("ERROR:", err));
```

## <a name="build-and-run-the-app-locally"></a>アプリをビルドしてローカルで実行する

1. 次のコマンドを実行して、Node.js アプリを実行します。

   ```powershell
   node app.js
   ```
1. コマンド プロンプトに次の出力が表示されます。

   ```powershell
   Retrieved value: Data from Azure App Configuration
   ```
## <a name="clean-up-resources"></a>リソースをクリーンアップする


[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、新しい App Configuration ストアを作成して、Node.js アプリのキー値を操作する方法について学習しました。

その他のコード サンプルについては、次を参照してください。

> [!div class="nextstepaction"]
> [Azure App Configuration クライアント ライブラリのサンプル](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration/samples)
