---
title: クイック スタート:Azure portal で Azure Static Web Apps を使用して静的 Web アプリを初めてビルドする
description: Azure portal を使用して、静的サイトを Azure Static Web Apps にデプロイする方法について説明します。
services: static-web-apps
author: craigshoemaker
ms.author: cshoe
ms.date: 05/07/2021
ms.topic: quickstart
ms.service: static-web-apps
ms.custom: mode-portal
ms.openlocfilehash: a1f193ded9424361a0ee4afcdbd3566b6e990fe4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131008885"
---
# <a name="quickstart-building-your-first-static-site-in-the-azure-portal"></a>クイックスタート: Azure portal で最初の静的サイトを構築する

Azure Static Web Apps では、GitHub リポジトリからアプリをビルドすることによって、運用環境に Web サイトが発行されます。 このクイックスタートでは、Azure portal を使用して Web アプリケーションを Azure Static Web Apps にデプロイします。

Azure サブスクリプションを持っていない場合は、[無料試用版アカウントを作成できます](https://azure.microsoft.com/free)。

## <a name="prerequisites"></a>前提条件

- [GitHub](https://github.com) アカウント
- [Azure](https://portal.azure.com) アカウント

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

## <a name="create-a-static-web-app"></a>静的 Web アプリを作成する

リポジトリが作成されたので、Azure portal から静的 Web アプリを作成できます。

1. [Azure Portal](https://portal.azure.com) に移動します。
1. **[リソースの作成]** を選択します。
1. **Static Web Apps** を検索します。
1. **Static Web Apps** を選択します。
1. **［作成］** を選択します

_[基本]_ セクションで、新しいアプリを構成し、それを GitHub リポジトリにリンクすることから始めます。

:::image type="content" source="media/getting-started-portal/quickstart-portal-basics.png" alt-text="[基本] セクション":::

1. _Azure サブスクリプション_ を選択します。
1. _[リソース グループ]_ の横にある **[新規作成]** リンクを選択します。
1. テキスト ボックスに「**static-web-apps-test**」と入力します。
1. _[静的 Web アプリの詳細]_ で、テキスト ボックスに「**my-first-static-web-App**」と入力します。
1. _[Azure Functions とステージングの詳細]_ で、最も近いリージョンを選択します。
1. _[デプロイの詳細]_ で、 **[GitHub]** を選択します。
1. **[GitHub アカウントでサインイン]** ボタンを選択し、GitHub で認証を行います。

GitHub にサインインした後、リポジトリ情報を入力します。

:::image type="content" source="media/getting-started-portal/quickstart-portal-source-control.png" alt-text="リポジトリの詳細":::

1. 希望する "_組織_" 名を選択します。
1. _[リポジトリ]_ ドロップダウンから **my-first-web-static-app** を選択します。
1. _[ブランチ]_ ドロップダウンから **main** を選択します。

   > [!NOTE]
   > リポジトリが表示されない場合は、GitHub で Azure Static Web Apps を承認しなければならない場合があります。 GitHub リポジトリを参照し、 **[設定]、[アプリケーション]、[認可された OAuth アプリ]** の順に移動して、 **[Azure Static Web Apps]** 、 **[許可]** の順に選択します。 組織リポジトリの場合は、アクセス許可を付与する組織の所有者である必要があります。

1. _[ビルドの詳細]_ セクションで、使用するフロントエンド フレームワークに固有の構成の詳細を追加します。

    # <a name="no-framework"></a>[フレームワークなし](#tab/vanilla-javascript)

    1. _[ビルドのプリセット]_ ドロップダウンから **[カスタム]** を選択します。
    1. _[App location]\(アプリの場所\)_ ボックスに「 **./src**」と入力します。
    1. _[Api location]\(API の場所\)_ ボックスはからのままにします。
    1. _[App artifact location]\(アプリ成果物の場所\)_ ボックスに「 **./src**」と入力します。

    # <a name="angular"></a>[Angular](#tab/angular)

    1. _[ビルドのプリセット]_ ドロップダウンから **[Angular]** を選択します。
    1. _[App location]\(アプリの場所\)_ ボックスは既定値のままにします。
    1. _[Api location]\(API の場所\)_ ボックスは空のままにします。
    1. _[App artifact location]\(アプリ成果物の場所\)_ ボックスに「**dist/angular-basic**」と入力します。

    # <a name="react"></a>[React](#tab/react)

    1. _[ビルドのプリセット]_ ドロップダウンから **[React]** を選択します。
    1. _[App location]\(アプリの場所\)_ ボックスは既定値のままにします。
    1. _[Api location]\(API の場所\)_ ボックスは空のままにします。
    1. _[App artifact location]\(アプリ成果物の場所\)_ ボックスに「**build**」と入力します。

    # <a name="vue"></a>[Vue](#tab/vue)

    1. _[ビルドのプリセット]_ ドロップダウンから **[Vue.js]** を選択します。
    1. _[App location]\(アプリの場所\)_ ボックスは既定値のままにします。
    1. _[Api location]\(API の場所\)_ ボックスは空のままにします。
    1. _[App artifact location]\(アプリ成果物の場所\)_ ボックスは既定値のままにします。

    ---

1. **[Review + create]\(レビュー + 作成\)** を選択します。

    :::image type="content" source="media/getting-started-portal/review-create.png" alt-text="[確認および作成] ボタン":::

    > [!NOTE]
    > アプリを作成した後にこれらの値を変更するには、[ワークフロー ファイル](build-configuration.md)を編集します。

1. **［作成］** を選択します

    :::image type="content" source="media/getting-started-portal/create-button.png" alt-text="[作成] ボタン":::

1. **[リソースに移動]** を選択します。

    :::image type="content" source="media/getting-started-portal/resource-button.png" alt-text="[リソース グループ] ボタン":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを引き続き使用しない場合は、次の手順を使用して Azure Static Web Apps インスタンスを削除することができます。

1. [Azure portal](https://portal.azure.com) を開きます。
1. 上部の検索バーから **my-first-web-static-app** を検索します。
1. アプリの名前を選択します。
1. **[削除]** ボタンを選択します。
1. **[はい]** を選択して削除アクションを確定します (このアクションが完了するまでにしばらく時間がかかる場合があります)。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [API を追加する](add-api.md)
