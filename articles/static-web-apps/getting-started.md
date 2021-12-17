---
title: 'クイックスタート: Azure Static Web Apps を使用して静的サイトを初めて構築する'
description: Azure Static Web Apps に静的サイトをデプロイする方法について説明します。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 06/16/2021
ms.author: cshoe
ms.openlocfilehash: a7b7504a99be6784e2767abe0daf9ba64066d0c1
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132134609"
---
# <a name="quickstart-building-your-first-static-site-with-azure-static-web-apps"></a>クイックスタート: Azure Static Web Apps を使用して静的サイトを初めて構築する

Azure Static Web Apps は、コード リポジトリからアプリをビルドすることによって、Web サイトを発行します。 このクイックスタートでは、Visual Studio Code 拡張機能を使用して、Azure Static Web Apps にアプリケーションをデプロイします。

Azure サブスクリプションを持っていない場合は、[無料試用版アカウントを作成できます](https://azure.microsoft.com/free)。

## <a name="prerequisites"></a>前提条件

- [GitHub](https://github.com) アカウント
- [Azure](https://portal.azure.com) アカウント
- [Visual Studio Code](https://code.visualstudio.com)
- [Visual Studio Code 用 Azure Static Web Apps 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- [Git をインストールする](https://www.git-scm.com/downloads)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

次に、Visual Studio Code を開いて **[ファイル] > [フォルダーを開く]** に移動し、クローンしたリポジトリをエディターで開きます。

## <a name="create-a-static-web-app"></a>静的 Web アプリを作成する

1. Visual Studio Code 内で、アクティビティ バーの Azure ロゴを選択して、Azure 拡張機能ウィンドウを開きます。

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Azure ロゴ":::

    > [!NOTE]
    > 続行するには、Visual Studio Code から Azure および GitHub にサインインする必要があります。 まだ認証されていない場合は、両方のサービスにサインインするよう、作成プロセス中に拡張機能から求められます。

1. _[Static Web Apps]_ ラベルで、**プラス記号** を選択します。

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="アプリケーション名":::
    
    > [!NOTE]
    > Azure Static Web Apps の Visual Studio Code 拡張機能では、一連の既定値を使用して作成プロセスが効率化されます。 作成プロセスをきめ細かく制御する場合は、コマンド パレットを開き、 **[Azure Static Web Apps: Create Static Web App... (Advanced)]\(Azure Static Web Apps: 静的 Web アプリの作成 (詳細)\)** を選択します。

1. エディターの上部にコマンド パレットが開き、サブスクリプションの名前を選択するように求められます。

    サブスクリプションを選択し、<kbd>Enter</kbd> キーを押します。

    :::image type="content" source="media/getting-started/extension-subscription.png" alt-text="Azure サブスクリプションを選択する":::

1. 次に、アプリケーションの名前を指定します。

    「**my-first-static-web-app**」と入力し、<kbd>Enter</kbd> キーを押します。

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="静的 Web アプリを作成する":::

1. 近くのリージョンを選択します。

    > [!NOTE]
    > Azure Static Web Apps によって静的アセットはグローバルに分散されます。 選択したリージョンによって、オプションのステージング環境と API 関数アプリが配置される場所が決まります。

1. アプリケーションの種類に一致するプリセットを選択します。

    # <a name="no-framework"></a>[フレームワークなし](#tab/vanilla-javascript)

    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="アプリケーション プリセット: フレームワークなし":::

    アプリケーション ファイルの場所として「 **/src**」を入力し、<kbd>Enter</kbd> キーを押します。

    このアプリでは、ビルド出力は生成されません。 ビルド出力場所が空であることを確認し、<kbd>Enter</kbd> キーを押します。

    # <a name="angular"></a>[Angular](#tab/angular)

    :::image type="content" source="media/getting-started/extension-presets-angular.png" alt-text="アプリケーションプ リセット: Angular":::

    アプリケーション ファイルの場所として「 **/** 」と入力します。

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="アプリケーション ファイルの場所: Angular":::

    ビルドの出力場所として「**dist/angular-basic**」と入力します。

    :::image type="content" source="media/getting-started/extension-angular.png" alt-text="アプリケーション ビルドの出力場所: Angular":::

    # <a name="react"></a>[React](#tab/react)

    :::image type="content" source="media/getting-started/extension-presets-react.png" alt-text="アプリケーションプ リセット: React":::

    アプリケーション ファイルの場所として「 **/** 」と入力します。

    ビルドの出力場所として「**build**」と入力します。

    # <a name="vue"></a>[Vue](#tab/vue)

    :::image type="content" source="media/getting-started/extension-presets-vue.png" alt-text="アプリケーションプ リセット: Vue":::

    アプリケーション ファイルの場所として「 **/** 」と入力します。

    ビルドの出力場所として「**dist**」と入力します。

    ---

1. アプリが作成されると、確認通知が Visual Studio Code に表示されます。

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="作成された確認":::

    デプロイの進行中、Visual Studio Code 拡張機能からビルドの状態がレポートされます。

    :::image type="content" source="media/getting-started/extension-waiting-for-deployment.png" alt-text="デプロイの待機中":::

    デプロイが完了したら、Web サイトに直接移動できます。

1. ブラウザーで Web サイトを表示するには、Static Web Apps 拡張機能でプロジェクトを右クリックし、 **[Browse Site]\(サイトの参照\)** を選択します。

    :::image type="content" source="media/getting-started/extension-browse-site.png" alt-text="サイトの参照":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを引き続き使用しない場合は、拡張機能を使用して Azure Static Web Apps インスタンスを削除することができます。

Visual Studio Code エクスプローラー ウィンドウで、 _[Static Web Apps]_ セクションに戻ります。 **[my-first-static-web-app]** を右クリックし、 **[削除]** を選択します。

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="アプリの削除":::

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [API を追加する](add-api.md)
