---
title: 'クイックスタート: Azure Static Web Apps を使用して静的サイトを初めて構築する'
description: Azure Static Web Apps に静的サイトをデプロイする方法について説明します。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: 335f78bba24947b1b6c3d6132bc38f237b3298b9
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449133"
---
# <a name="quickstart-building-your-first-static-site-with-azure-static-web-apps"></a>クイックスタート: Azure Static Web Apps を使用して静的サイトを初めて構築する

Azure Static Web Apps では、コード リポジトリからアプリをビルドすることによって、Web サイトを発行します。 このクイックスタートでは、Visual Studio Code 拡張機能を使用して、Azure Static Web Apps にアプリケーションをデプロイします。

Azure サブスクリプションを持っていない場合は、[無料試用版アカウントを作成できます](https://azure.microsoft.com/free)。

## <a name="prerequisites"></a>前提条件

- [GitHub](https://github.com) アカウント
- [Azure](https://portal.azure.com) アカウント
- [Visual Studio Code](https://code.visualstudio.com)
- [Visual Studio Code 用 Azure Static Web Apps 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- [Git をインストールする](https://www.git-scm.com/downloads)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

次に、Visual Studio Code を開き、 **[ファイル] > [フォルダーを開く]** に移動して、お使いのマシンにクローンしたリポジトリをエディターで開きます。

## <a name="create-a-static-web-app"></a>静的 Web アプリを作成する

1. Visual Studio Code 内で、アクティビティ バーの Azure ロゴを選択して、Azure 拡張機能ウィンドウを開きます。

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Azure ロゴ":::

    > [!NOTE]
    > Azure と GitHub へのサインインが必要です。 Visual Studio Code から Azure と GitHub にまだサインインしていない場合は、拡張機能により、作成プロセス中に両方にサインインするように求められます。

1. _[Static Web Apps]_ ラベルで、**プラス記号** を選択します。

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="アプリケーション名":::

1. エディターの上部にコマンド パレットが表示され、アプリケーションの名前を入力するように求められます。

    「**my-first-static-web-app**」と入力し、**Enter** キーを押します。

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="静的 Web アプリを作成する":::

1. アプリケーションの種類に一致するプリセットを選択します。

    # <a name="no-framework"></a>[フレームワークなし](#tab/vanilla-javascript)
    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="アプリケーション プリセット: フレームワークなし":::

    アプリケーション ファイルの場所として「 **./** 」と入力します

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="アプリケーション ファイルの場所":::

    Azure Functions API の場所として **[Skip for now]\(今はしない\)** を選択します

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="API の場所":::

    ビルドの出力場所として「 **./** 」と入力します

    :::image type="content" source="media/getting-started/extension-build-location.png" alt-text="アプリケーション ビルドの出力場所":::

    # <a name="angular"></a>[Angular](#tab/angular)

    :::image type="content" source="media/getting-started/extension-presets-angular.png" alt-text="アプリケーションプ リセット: Angular":::

    # <a name="react"></a>[React](#tab/react)

    :::image type="content" source="media/getting-started/extension-presets-react.png" alt-text="アプリケーションプ リセット: React":::

    # <a name="vue"></a>[Vue](#tab/vue)

    :::image type="content" source="media/getting-started/extension-presets-vue.png" alt-text="アプリケーションプ リセット: Vue":::

    ---

1. 最も近い場所を選択し、**Enter** キーを押します。

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="リソースの場所":::

1. アプリが作成されると、確認通知が Visual Studio Code に表示されます。

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="作成された確認":::

    次に、 **[Open Actions in GitHub]\(GitHub でアクションを開く\)** ボタンをクリックします。 このページには、アプリケーションのビルドの状態が表示されます。

    GitHub アクションが完了したら、発行された Web サイトを参照できます。

1. ブラウザーで Web サイトを表示するには、Static Web Apps 拡張機能でプロジェクトを右クリックし、 **[Browse Site]\(サイトの参照\)** を選択します。

    :::image type="content" source="media/getting-started/extension-browse-site.png" alt-text="サイトの参照":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを引き続き使用しない場合は、拡張機能を使用して Azure Static Web Apps インスタンスを削除することができます。

Visual Studio Code エクスプローラー ウィンドウで、 _[Static Web Apps]_ セクションに戻ります。 **[my-first-static-web-app]** を右クリックし、 **[削除]** を選択します。

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="アプリの削除":::

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [API を追加する](add-api.md)
