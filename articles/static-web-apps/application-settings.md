---
title: Azure Static Web Apps のアプリケーション設定を構成する
description: Azure Static Web Apps のアプリケーション設定を構成する方法について説明します
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 09/23/2021
ms.author: buhollan
ms.custom: devx-track-js
ms.openlocfilehash: a104860eb72a6376c2ab337f31bb06fd041f42a0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128597096"
---
# <a name="configure-application-settings-for-azure-static-web-apps"></a>Azure Static Web Apps のアプリケーション設定を構成する

アプリケーション設定には、データベース接続文字列など、変更される可能性のある値の構成設定が保持されます。 アプリケーション設定を追加すると、アプリケーション コードを変更せずに、アプリへの構成入力を変更できます。

アプリケーション設定:

- 静的 Web アプリのバックエンド API で、環境変数として使用できます
- [認証構成](key-vault-secrets.md)で使用されるシークレットを格納するために使用できます
- 保存時に暗号化される
- [ステージング](review-publish-pull-requests.md)環境と運用環境にコピーされる
- 英数字、`.`、および `_` のみ使用可能

> [!IMPORTANT]
> この記事で説明しているアプリケーション設定は、Azure Static Web App のバックエンド API にのみ適用されます。
>
> フロントエンド Web アプリケーションの構築に必要な環境変数を構成するには、「[ビルドの構成](build-configuration.md#environment-variables)」を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure Static Web Apps アプリケーション
- [Azure CLI](/cli/azure/install-azure-cli) — コマンド ラインを使用する場合に必要です

## <a name="configure-api-application-settings-for-local-development"></a>ローカル開発用に API アプリケーション設定を構成する

Azure Static Web Apps の API では Azure Functions の機能が利用されています。これにより、アプリケーションをローカル環境で実行するときに、_local.settings.json_ ファイルでアプリケーションの設定を定義できます。 このファイルは、構成の `Values` プロパティでアプリケーション設定を定義します。

> [!NOTE]
> _local.settings.json_ ファイルは、ローカル開発にのみ使用されます。 運用環境用のアプリケーション設定を構成するには、[Azure portal](https://portal.azure.com) を使用します。

次のサンプル _local.settings.json_ は、`DATABASE_CONNECTION_STRING` の値を追加する方法を示しています。

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
  }
}
```

`Values` プロパティで定義されている設定は、環境変数としてコードから参照できます。 たとえば Node.js 関数では、`process.env` オブジェクトで使用できます。

```js
const connectionString = process.env.DATABASE_CONNECTION_STRING;
```

`local.settings.json` ファイルは GitHub リポジトリで追跡されていません。このファイルには、データベース接続文字列などの機密情報が含まれていることが多いためです。 ローカル設定はコンピューター上に残っているため、Azure では設定を手動で構成する必要があります。

一般に、設定の構成は頻繁には行われず、ビルドのたびに行う必要はありません。

## <a name="configure-application-settings"></a>アプリケーション設定の構成

アプリケーション設定を構成するには、Azure portal または Azure CLI を使用します。

### <a name="use-the-azure-portal"></a>Azure ポータルの使用

Azure portal には、アプリケーション設定を作成、更新、および削除するためのインターフェイスが用意されています。

1. [Azure Portal](https://portal.azure.com) に移動します。

1. 検索バーで **Static Web Apps** を検索して選択します。

1. サイドバーの **[構成]** オプションをクリックします。

1. アプリケーション設定を適用する環境を選択します。 ステージング環境は pull request が生成されると自動的に作成され、pull request がマージされると、運用環境に昇格されます。 アプリケーション設定は、環境ごとに設定できます。

1. **[追加]** ボタンをクリックして、新しいアプリケーション設定を追加します。

    :::image type="content" source="media/application-settings/configuration.png" alt-text="Azure Static Web Apps の構成ビュー":::

1. **[名前]** と **[値]** を入力します。

1. **[OK]** をクリックします。

1. **[保存]** をクリックします。

### <a name="use-the-azure-cli"></a>Azure CLI の使用

`az rest` コマンドを使用すると、設定を Azure に一括アップロードできます。 このコマンドは、`properties` という親プロパティで JSON オブジェクトとしてアプリケーション設定を受け入れます。

適切な値を使用して JSON ファイルを作成する方法として最も簡単なのは、_local.settings.json_ ファイルの変更バージョンを作成することです。

1. 機密データを含む新しいファイルが公開されないようにするには、 _.gitignore_ ファイルに次のエントリを追加します。

   ```bash
   local.settings*.json
   ```

1. 次に、_local.settings.json_ ファイルのコピーを作成して、_local.settings.properties.json_ という名前を付けます。

1. この新しいファイル内で、アプリケーション設定を除く他のすべてのデータをファイルから削除し、名前を `Values` から `properties` に変更します。

   これで、ファイルは次の例のようになります。

   ```json
   {
     "properties": {
       "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
     }
   }
   ```

1. 次のコマンドを実行して、サブスクリプション内の静的 Web アプリの一覧と、それらの詳細を表示します。

    ```bash
    az staticwebapp list -o json
    ```

    構成する静的 Web アプリを見つけて、その ID を記録します。

1. ターミナルまたはコマンド ラインから次のコマンドを実行して、設定をアップロードします。 `<YOUR_APP_ID>` を、前のステップで取得したアプリの ID に置き換えます。

   ```bash
   az rest --method put --headers "Content-Type=application/json" --uri "<YOUR_APP_ID>/config/functionappsettings?api-version=2019-12-01-preview" --body @local.settings.properties.json
   ```

  > [!IMPORTANT]
  > "local.settings.properties.json" ファイルは、このコマンドを実行するのと同じディレクトリに置かれている必要があります。 このファイルには任意の名前を付けることができます。 この名前は重要ではありません。

### <a name="view-application-settings-with-the-azure-cli"></a>Azure CLI を使用してアプリケーション設定を表示する

アプリケーション設定は、Azure CLI を使用して表示できます。

- ターミナルまたはコマンド ラインから、次のコマンドを実行します。 プレースホルダー `<YOUR_APP_ID>` は、必ず実際の値に置き換えてください。

   ```bash
   az rest --method post --uri "<YOUR_APP_ID>/listFunctionAppSettings?api-version=2019-12-01-preview"
   ```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [フロントエンド フレームワークの構成](front-end-frameworks.md)
