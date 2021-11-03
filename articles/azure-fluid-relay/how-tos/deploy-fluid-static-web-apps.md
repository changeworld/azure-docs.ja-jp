---
title: '方法: Azure Static Web Apps を使用して流動的なアプリケーションをデプロイする'
description: Fluid アプリケーションを Azure Static Web Apps でホストする方法の詳細説明
author: sdeshpande3
ms.author: sdeshpande
ms.date: 08/19/2021
ms.topic: article
ms.service: azure-fluid
ms.openlocfilehash: a5b30b3097ef3e04557473dcaf42e875470b7563
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090059"
---
# <a name="how-to-deploy-fluid-applications-using-azure-static-web-apps"></a>方法: Azure Static Web Apps を使用して流動的なアプリケーションをデプロイする

この記事では、Azure Static Web Apps を使用して Fluid apps にデプロイする方法について説明します。 [FluidHelloWorld](https://github.com/microsoft/FluidHelloWorld/tree/main-azure) リポジトリには、**DiceRoller** という名前の Fluid アプリケーションが含まれています。これにより、接続されているすべてのクライアントがサイコロを振って結果を表示できます。  この方法では、Visual Studio Code 拡張機能を使用して、DiceRoller アプリケーションを Azure Static Web Apps にデプロイします。

Azure サブスクリプションを持っていない場合は、[無料試用版アカウントを作成できます](https://azure.microsoft.com/free)。

## <a name="prerequisites"></a>前提条件

- [GitHub](https://github.com) アカウント
- [Azure](https://portal.azure.com) アカウント
- [Visual Studio Code](https://code.visualstudio.com)
- [Visual Studio Code 用 Azure Static Web Apps 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- [Git をインストールする](https://www.git-scm.com/downloads)

[!INCLUDE [fork-fluidhelloworld](../includes/fork-fluidhelloworld.md)]

## <a name="connect-to-azure-fluid-relay-service"></a>Azure Fluid Relay サービスに接続する

Azure リソースの作成時に一意に生成されるテナント ID とキーを指定することで、Azure Fluid Relay サービスに接続できます。 独自のトークン プロバイダー実装を構築するか、Fluid Framework が提供する 2 つのトークン プロバイダー実装を使用できます **。InsecureTokenProvider** と **AzureFunctionTokenProvider** です。

ローカル開発に InsecureTokenProvider を使用する方法の詳細については、[サービスへの接続](connect-fluid-azure-service.md#connecting-to-the-service)と[アプリでの認証と承認](../concepts/authentication-authorization.md#the-token-provider)を参照してください。

### <a name="using-azurefunctiontokenprovider"></a>AzureFunctionTokenProvider の使用

**AzureFunctionTokenProvider** は、クライアント側コードで秘密鍵を公開しないトークン プロバイダーであり、運用のシナリオで使用できます。 このトークン プロバイダーの実装を使用して、テナント キーでアクセス トークンに署名する HTTPS エンドポイントからトークンをフェッチできます。 これにより、トークンを生成してクライアント アプリに返す安全な方法が提供されます。

```js
import { AzureClient, AzureFunctionTokenProvider } from "@fluidframework/azure-client";

const config = {
    tenantId: "myTenantId",
    tokenProvider: new AzureFunctionTokenProvider("https://myAzureAppUrl"+"/api/GetAzureToken", { userId: "test-user",userName: "Test User" }),
    orderer: "https://myOrdererUrl",
    storage: "https://myStorageUrl",
};

const clientProps = {
    connection: config,
};

const client = new AzureClient(clientProps);
```

このトークン プロバイダーを使用するには、トークンに署名する HTTPS エンドポイントをデプロイし、エンドポイントの URL を AzureFunctionTokenProvider に渡す必要があります。

### <a name="deploying-an-azure-function-using-azure-static-web-apps"></a>Azure Static Web アプリを使用した Azure 関数のデプロイ

Azure Static Web Apps は、Web ホスティング環境全体のサーバー側の構成に対処することなく、フルスタック Web サイトを開発できます。 静的 Web サイトと共に Azure Functions をデプロイできます。 この機能を使用すると、トークンに署名する HTTP によってトリガーされる Azure 関数をデプロイできます。

Azure 関数を利用した API を静的 Web アプリにデプロイする方法の詳細については、[Azure Functions を使用して Azure Static Web Apps に API を追加](../../static-web-apps/add-api.md)を参照してください。

> [!NOTE]
> 「Azure 関数の実装」の Azure 関数コードの例を使用し、[トークンに署名して関数を実装](azure-function-token-provider.md#implement-an-azure-function-to-sign-tokens)することができます。

Azure 関数をデプロイしたら、AzureFunctionTokenProvider に渡される URL を更新する必要があります。

```js
import { AzureClient } from "@fluidframework/azure-client";

const config = {
    tenantId: "myTenantId",
    tokenProvider: new AzureFunctionTokenProvider("https://myStaticWebAppUrl/api/GetAzureToken", { userId: "test-user",userName: "Test User" }),
    orderer: "https://myOrdererUrl",
    storage: "https://myStorageUrl",
};

const clientProps = {
    connection: config,
};

const client = new AzureClient(config);
```

ルート ディレクトリ `npm run build` から コマンドを実行して、アプリをリビルドします。 これにより、`dist`静的 Web アプリにデプロイする必要があるアプリケーション コードを含むフォルダーが生成されます。

[!INCLUDE [sign-in-extensions](../includes/sign-in-extensions.md)]

## <a name="create-a-static-web-app"></a>静的 Web アプリを作成する

1. Visual Studio Code 内で、アクティビティ バーの Azure ロゴを選択して、Azure 拡張機能ウィンドウを開きます。

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-azure-logo.png" alt-text="白い背景の Azure ロゴの画像。":::

    > [!NOTE]
    > 続行するには、Visual Studio Code から Azure および GitHub にサインインする必要があります。 まだ認証されていない場合は、両方のサービスにサインインするよう、作成プロセス中に拡張機能から求められます。

1. *[Static Web Apps]* ラベルで、**プラス記号** を選択します。

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-create-button.png" alt-text="Static Web Apps 拡張機能 UI の画像で、[作成] ボタンが強調表示されています。":::
    
    > [!NOTE]
    > Azure Static Web Apps の Visual Studio Code 拡張機能では、一連の既定値を使用して作成プロセスが効率化されます。 作成プロセスをきめ細かく制御する場合は、コマンド パレットを開き、 **[Azure Static Web Apps: Create Static Web App... (Advanced)]\(Azure Static Web Apps: 静的 Web アプリの作成 (詳細)\)** を選択します。

1. エディターの上部にコマンド パレットが開き、サブスクリプションの名前を選択するように求められます。

    サブスクリプションを選択し、<kbd>Enter</kbd> キーを押します。

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-subscription.png" alt-text="選択する 1 つのサブスクリプションを示す Azure サブスクリプション選択 UI の画像。":::

1. 次に、アプリケーションの名前を指定します。

    「**my-first-static-web-app**」と入力し、<kbd>Enter</kbd> キーを押します。

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-create-app.png" alt-text="アプリケーションの名前を入力するテキスト ボックスを示す Static Web Apps 拡張機能 UI の画像。":::

1. 近くのリージョンを選択します。

    > [!NOTE]
    > Azure Static Web Apps によって静的アセットはグローバルに分散されます。 選択したリージョンによって、オプションのステージング環境と API 関数アプリが配置される場所が決まります。

1. その他のデプロイ オプションを設定します。
    
    - 既定のプロジェクト構造を構成するビルド プリセットの選択を求めるメッセージが表示された場合は **カスタム** を選択します。
    - アプリケーション コードの場所: `/`
    - Azure 関数コードの場所: `api`

1. アプリが作成されると、確認通知が Visual Studio Code に表示されます。

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-confirmation.png" alt-text="アプリ作成時の Visual Studio Code 通知画像です。通知は次のように表示されます。新しい静的 Web アプリ my-first-static-web-app が正常に作成されました。GitHub Actions はアプリを構築してデプロイしています。デプロイが完了すると、アプリを使用できます。":::

    デプロイの進行中、Visual Studio Code 拡張機能からビルドの状態がレポートされます。

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-waiting-for-deployment.png" alt-text="各サブスクリプションの Static Web Apps Web アプリの一覧を示す拡張機能 UI の画像。強調表示されている静的 Web アプリの状態は、その横に [デプロイを待機しています] と表示されます。":::

    デプロイが完了したら、Web サイトに直接移動できます。

1. ブラウザーで Web サイトを表示するには、Static Web Apps 拡張機能でプロジェクトを右クリックし、 **[Browse Site]\(サイトの参照\)** を選択します。

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-browse-site.png" alt-text="静的 Web アプリを右クリックすると表示されるメニューの画像です。[サイトの参照] オプションが強調表示されています。":::

1. アプリケーション コード、Azure 関数、ビルド出力の場所は、 ディレクトリにある `azure-static-web-apps-xxx-xxx-xxx.yml` ワークフロー ファイルの一部 `/.github/workflows` です。 このファイルは、静的 Web アプリの作成時、自動的に作成されます。 静的 Web アプリをビルドしてデプロイするための GitHub アクションを定義します。


## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを引き続き使用しない場合は、拡張機能を使用して Azure Static Web Apps インスタンスを削除することができます。

Visual Studio Code エクスプローラー ウィンドウで、 _[Static Web Apps]_ セクションに戻ります。 **[my-first-static-web-app]** を右クリックし、 **[削除]** を選択します。

:::image type="content" source="../../static-web-apps/media/getting-started/extension-delete.png" alt-text="静的 Web アプリを右クリックすると表示されるメニューの画像です。[削除] オプションが強調表示されています。":::
