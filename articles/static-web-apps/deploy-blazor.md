---
title: チュートリアル:Azure Static Web Apps での Blazor を使用した静的 Web アプリのビルド
description: Blazor を使用して Azure Static Web Apps Web サイトをビルドする方法について説明します。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 11/08/2021
ms.author: cshoe
ms.openlocfilehash: ac22d5ef720fb0701cb126e6fa0cb627614dd09c
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027168"
---
# <a name="tutorial-building-a-static-web-app-with-blazor-in-azure-static-web-apps"></a>チュートリアル:Azure Static Web Apps での Blazor を使用した静的 Web アプリのビルド

Azure Static Web Apps では、GitHub リポジトリからアプリをビルドすることによって、運用環境に Web サイトが発行されます。 このチュートリアルでは、Azure portal を使用して、Web アプリケーションを Azure Static Web Apps にデプロイします。

Azure サブスクリプションを持っていない場合は、[無料試用版アカウントを作成できます](https://azure.microsoft.com/free)。

## <a name="prerequisites"></a>前提条件

- [GitHub](https://github.com) アカウント
- [Azure](https://portal.azure.com) アカウント

## <a name="application-overview"></a>アプリケーションの概要

Azure Static Web Apps を使用して、サーバーレス バックエンドでサポートされる静的 Web アプリケーションを作成できます。 次のチュートリアルでは、サーバーレス API によって返される気象データを表示する C# Blazor WebAssembly アプリケーションを配置する方法が示されています。

:::image type="content" source="./media/deploy-blazor/blazor-app-complete.png" alt-text="完全な Blazor アプリ":::

このチュートリアルで紹介するアプリは、3 つの異なる Visual Studio プロジェクトから構成されています。

- **Api**: Blazor WebAssembly アプリに気象情報を提供する API エンドポイントを実装する C# Azure Functions アプリケーション。 **WeatherForecastFunction** は、`WeatherForecast` オブジェクトの配列を返します。

- **Client**: フロントエンド Blazor WebAssembly プロジェクト。 クライアント側のルーティングが機能するように、[フォールバック ルート](#fallback-route)が実装されます。

- **共有**:Api プロジェクトと Client プロジェクトの両方のプロジェクトによって参照される共通クラスを保持します。これにより、データを API エンドポイントからフロントエンド Web アプリにフローさせることができます。 両方のアプリで、[`WeatherForecast`](https://github.com/staticwebdev/blazor-starter/blob/main/Shared/WeatherForecast.cs) クラスが共有されます。

これらのプロジェクトがまとまって、Azure Functions API バックエンドによってサポートされているブラウザーで実行される Blazor WebAssembly アプリケーションを作成するために必要な部分が構成されます。

## <a name="fallback-route"></a>フォールバック ルート

アプリケーションでは、アプリケーションの特定のルートにマップされる _/counter_ や _/fetchdata_ などの URL が公開されます。 このアプリは単一ページ アプリケーションとして実装されるため、各ルートで _index.html_ ファイルが使用されます。 任意のパスに対する要求で _index.html_ が確実に返されるようにするため、Client プロジェクトの root フォルダーにある _staticwebapp.config.json_ ファイルに[フォールバック ルート](./configuration.md#fallback-routes)が実装されます。

```json
{
  "navigationFallback": {
    "rewrite": "/index.html"
  }
}
```

上記の構成により、アプリ内の任意のルートへの要求で、_index.html_ ページが確実に返されます。

## <a name="create-a-repository"></a>リポジトリを作成する

この記事では、簡単に作業を開始できるように、GitHub テンプレート リポジトリを使用します。 テンプレートには、Azure Static Web Apps にデプロイできるスターター アプリが含まれます。

1. GitHub にサインインしていることを確認し、次の場所に移動して新しいリポジトリを作成します。
   - [https://github.com/staticwebdev/blazor-starter/generate](https://github.com/login?return_to=/staticwebdev/blazor-starter/generate)
1. ご自分のリポジトリに **my-first-static-blazor-app** という名前を付けます。

## <a name="create-a-static-web-app"></a>静的 Web アプリを作成する

リポジトリが作成されたので、Azure portal から静的 Web アプリを作成します。

1. [Azure Portal](https://portal.azure.com) に移動します。
1. **[リソースの作成]** を選択します。
1. **Static Web Apps** を検索します。
1. **Static Web Apps** を選択します。
1. **［作成］** を選択します
1. _[基本]_ タブで、次の値を入力します。

    | プロパティ | 値 |
    | --- | --- |
    | _サブスクリプション_ | Azure サブスクリプション名。 |
    | _リソース グループ_ | **my-blazor-group**  |
    | _名前_ | **my-first-static-blazor-app** |
    | _[プランの種類]_ | **Free** |
    | _Azure Functions API のリージョンとステージング環境_ | 最も近いリージョンを選択します。 |
    | _ソース_ | **GitHub** |

1. **[GitHub でサインイン]** を選択し、GitHub で認証します。

1. 次の GitHub 値を入力します。

    | プロパティ | 値 |
    | --- | --- |
    | _組織_ | ご自分の希望する GitHub 組織を選択します。 |
    | _リポジトリ_ | **my-first-static-blazor-app** を選択します。 |
    | _ブランチ_ | **[main]\(メイン\)** を選択します。 |

1. _[Build Details]\(ビルドの詳細\)_ セクションで、 _[Build Presets]\(ビルドのプリセット\)_ ドロップダウンから **[Blazor]** を選択すると、次の値が設定されます。

    | プロパティ | 値 | 説明 |
    | --- | --- | --- |
    | アプリの場所 | **Client** | Blazor WebAssembly アプリを含むフォルダー |
    | API の場所 | **Api** | Azure Functions アプリを含むフォルダー |
    | Output location (出力場所) | **wwwroot** | 発行された Blazor WebAssembly アプリケーションを含むビルド出力のフォルダー |
    
### <a name="review-and-create"></a>[Review and create] (確認および作成)

1. **[確認および作成]** ボタンを選択して、詳細がすべて正しいことを確認します。

1. **[作成]** を選択して、App Service Static Web App の作成を開始し、デプロイのための GitHub アクションをプロビジョニングします。

1. デプロイが完了したら、 **[リソースに移動]** をクリックします。

1. **[リソースに移動]** を選択します。

   :::image type="content" source="media/deploy-blazor/resource-button.png" alt-text="[リソース グループ] ボタン":::

## <a name="view-the-website"></a>Web サイトを表示する

静的アプリのデプロイには 2 つの側面があります。 まず、アプリを構成する基になる Azure リソースをプロビジョニングします。 2 つ目は、アプリケーションをビルドして発行する GitHub Actions ワークフローです。

新しい静的 Web サイトに移動する前に、デプロイ ビルドの実行が完了している必要があります。

Static Web Apps の概要ウィンドウには、Web アプリとの対話に役立つ一連のリンクが表示されます。

:::image type="content" source="./media/deploy-blazor/overview-window.png" alt-text="概要ウィンドウ":::

1. "_Click here to check the status of your GitHub Actions runs (こちらをクリックして、GitHub Actions の実行の状態を確認してください)_ " というバナーをクリックすると、リポジトリに対して実行されている GitHub アクションが表示されます。 デプロイ ジョブが完了したことを確認したら、生成された URL を使用して Web サイトに移動できます。

2. GitHub Actions ワークフローが完了したら、 _[URL]_ リンクを選択して、新しいタブで Web サイトを開くことができます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを引き続き使用しない場合は、次の手順を使用して Azure Static Web Apps インスタンスを削除することができます。

1. [Azure Portal](https://portal.azure.com)を開きます。
1. 上部の検索バーから「**my-blazor-group**」を検索します。
1. グループ名を選択します。
1. **[削除]** ボタンを選択します。
1. **[はい]** を選択して、削除操作を確定します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [認証と権限承認](./authentication-authorization.md)
