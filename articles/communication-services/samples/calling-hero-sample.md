---
title: Group Calling Hero Sample
titleSuffix: An Azure Communication Services sample overview
description: Azure Communication Services を使用する Calling Hero Sample の概要。サンプルの内部動作をより詳しく学習できます。
author: ddematheu
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/20/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: caee5686695594604f49dcbade54342a9134abc0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945462"
---
# <a name="get-started-with-the-group-calling-hero-sample"></a>Group Calling Hero Sample を使ってみる

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!----
> [!WARNING]
> Add links to our Hero Sample repo when the sample is publicly available.
---->

Azure Communication Services の **Group Calling Hero Sample** は、Communication Services の Calling Web クライアント ライブラリを使用して、グループ通話エクスペリエンスを構築する方法を示します。

このサンプルのクイックスタートでは、サンプルがどのように動作するかを学習した後で、サンプルをローカル コンピューターで実行します。 次に、独自の Azure Communication Services リソースを使用して、サンプルを Azure にデプロイします。

> [!IMPORTANT]
> [GitHub からサンプルをダウンロードする](https://github.com/Azure/Communication/tree/master/samples)

## <a name="overview"></a>概要

このサンプルには、クライアント側アプリケーションとサーバー側アプリケーションの両方があります。 **クライアント側アプリケーション**は、Microsoft の Fluent UI フレームワークを使用する React/Redux Web アプリケーションです。 このアプリケーションが ASP.NET Core の**サーバー側アプリケーション**に要求を送信することで、クライアント側アプリケーションが Azure に接続できるようにします。 

サンプルは次のようになります。

:::image type="content" source="./media/calling/landing-page.png" alt-text="サンプル アプリケーションのランディング ページを示すスクリーンショット。":::

[Start a Chat]\(通話を開始\) ボタンを押すと、サーバー側アプリケーションからユーザー アクセス トークンがフェッチされます。 このトークンは、クライアント アプリを Azure Communication Services に接続するために使用されます。 トークンの取得後、使用するカメラとマイクを指定するように求められます。 切り替えコントロールを使用して、これらのデバイスを無効または有効にできます。

:::image type="content" source="./media/calling/pre-call.png" alt-text="サンプル アプリケーションの通話開始前の画面を示すスクリーンショット。":::

表示名とデバイスを構成すると、通話セッションに参加できるようになります。 これで、メインの通話キャンバスが表示され、通話のコア エクスペリエンスが提供されます。

:::image type="content" source="./media/calling/main-app.png" alt-text="サンプル アプリケーションのメイン画面を示すスクリーンショット。":::

通話のメイン画面のコンポーネントは以下のとおりです。

- **メディア ギャラリー**: 参加者が表示されるメイン領域。 参加者のカメラが有効になっている場合は、そのビデオ フィードがここに表示されます。 各参加者の表示名とビデオ ストリーム (ある場合) がタイル表示されます。
- **ヘッダー**:ここには通話のメイン コントロールがあり、設定と参加者のサイド バーの切り替え、ビデオとマイクのオン/オフ切り替え、画面の共有、通話の終了を行えます。
- **サイド バー**: ヘッダーのコントロールを使用して、参加者と設定情報の表示を切り替えます。 このコンポーネントは、右上隅にある 'X' を使用して閉じることができます。 参加者のサイド バーには参加者の一覧と、チャットするユーザーをさらに招待するためのリンクが表示されます。 [設定] サイドバーでは、マイクとカメラの設定を構成できます。

前提条件、サンプルの設定手順、さまざまなコンポーネントについて理解を深めるためのステップバイステップのチュートリアルの詳細については、以下を参照してください。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウントを作成します。 詳細については、[無料アカウントの作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)に関するページをご覧ください。
- [Node.js (12.18.4 以上)](https://nodejs.org/en/download/)
- [Visual Studio (2019 以上)](https://visualstudio.microsoft.com/vs/)
- [.NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core/2.2) (使用する Visual Studio インスタンス (32 または 64 ビット) に対応するバージョンを必ずインストールしてください)
- Azure Communication Services リソースを作成します。 詳細については、[Azure Communication リソースの作成](../quickstarts/create-communication-resource.md)に関するページをご覧ください。 このクイックスタートで使用するリソースの**接続文字列**を記録する必要があります。

## <a name="locally-deploy-the-service--client-applications"></a>サービスおよびクライアント アプリケーションをローカルにデプロイする

グループ通話のサンプルは、基本的には、ClientApp と Service.NET アプリの 2 つのアプリケーションです。

ローカルにデプロイする場合は、両方のアプリケーションを起動する必要があります。 ブラウザーからサーバー アプリにアクセスすると、ローカルにデプロイされた ClientApp がユーザー エクスペリエンスとして使用されます。

このサンプルをローカルでテストするには、通話の URL を使用して複数のブラウザー セッションを開き、マルチユーザー通話をシミュレートします。

### <a name="before-running-the-sample-for-the-first-time"></a>サンプルを初めて実行する前に行う手順

1. PowerShell、Windows ターミナル、コマンド プロンプト、またはそれと同等のインスタンスを開き、サンプルの複製先のディレクトリに移動します。
2. `git clone`
3. **Calling/ClientApp** フォルダーにアクセスし、`npm run setup` を実行します。
   1. エラー 1 が表示された場合は、出力の上部で、クライアントを承認するためにアクセスする必要がある URL を確認します。 (URL は次のようになります: `app.vssps.visualstudio.com/oauth2/authorize?clientid=...`) ブラウザーで URL にアクセスしたら、ブラウザー ウィンドウからコマンドをコピーして実行します。
   2. 前の手順を完了後、コマンド `npm run setup-vsts-auth` をもう一度実行します。
4. Azure portal から `Connection String` を取得します。 接続文字列の詳細については、[Azure Communication リソースの作成](../quickstarts/create-communication-resource.md)に関するページをご覧ください。
5. 接続文字列を取得後、サービスの .NET フォルダーの下にある **Calling/appsetting.json** ファイルに接続文字列を追加します。 変数 `ResourceConnectionString` に接続文字列を入力します。

### <a name="local-run"></a>ローカル実行

1. Calling フォルダーに移動します。
2. Visual Studio で `Calling.csproj` ソリューションを開きます。
2. `Calling` プロジェクトを実行します。*

\* ブラウザーが、`localhost:5000` (ノードがクライアント アプリをデプロイしている場所) で開きます。 アプリは Internet Explorer ではサポートされていません。

#### <a name="troubleshooting"></a>トラブルシューティング

- ソリューションがビルドされない。NPM のインストール/ビルド中にエラーがスローされる。

   プロジェクトのクリーン/リビルドを試してください。

## <a name="publish-the-sample-to-azure"></a>サンプルを Azure に発行する

1. `Calling` プロジェクトを右クリックし、[発行] を選択します。
2. 新しい発行プロファイルを作成し、Azure サブスクリプションを選択します。
3. 発行する前に、`Edit App Service Settings` を使用して接続文字列を追加し、`ResourceConnectionString` をキーとして入力し、(appsettings.json からコピーした) 接続文字列を値として指定します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Communication Services サブスクリプションをクリーンアップして解除したい場合は、リソースまたはリソース グループを削除できます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。 詳細については、[リソースのクリーンアップ](../quickstarts/create-communication-resource.md#clean-up-resources)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。

- [Calling クライアント ライブラリ](../quickstarts/voice-video-calling/calling-client-samples.md)について理解する
- [Calling クライアント ライブラリの機能](../quickstarts/voice-video-calling/calling-client-samples.md)について学習する
- [通話のしくみ](../concepts/voice-video-calling/about-call-types.md)の詳細について確認する

## <a name="additional-reading"></a>その他の情報

- [Azure Communication のプレビュー](https://github.com/Azure/communication-preview) - 通話 Web SDK の詳細
- [Redux](https://redux.js.org/) - クライアント側の状態管理
- [FluentUI](https://developer.microsoft.com/fluentui#/) - Microsoft が提供する UI ライブラリ
- [React](https://reactjs.org/) - ユーザー インターフェイスを構築するためのライブラリ
- [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1&preserve-view=true) - Web アプリケーションを構築するためのフレームワーク
