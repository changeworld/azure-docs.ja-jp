---
title: グループ チャットのヒーロー サンプル
titleSuffix: An Azure Communication Services sample overview
description: Azure Communication Services を使用したチャットのヒーロー サンプルの概要。これにより、開発者はサンプルの内部動作とその変更方法をより詳しく学習することができます。
author: ddematheu
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/20/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 295c4bde64ad21a19d21fd48f2556114b26b202d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945279"
---
# <a name="get-started-with-the-group-chat-hero-sample"></a>グループ チャットのヒーロー サンプルを使ってみる

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!----
> [!WARNING]
> links to our Hero Sample repo need to be updated when the sample is publicly available.
---->

Azure Communication Services の**グループ チャットのヒーロー サンプル**は、Communication Services のチャット Web クライアント ライブラリーを使用して、グループ通話エクスペリエンスを構築する方法を示します。

このサンプルのクイックスタートでは、サンプルをローカル コンピューターで実行する前に、サンプルがどのように動作するかを学習します。 次に、独自の Azure Communication Services リソースを使用して、サンプルを Azure にデプロイします。

> [!IMPORTANT]
> [GitHub からサンプルをダウンロードする](https://github.com/Azure/Communication/tree/master/samples)

## <a name="overview"></a>概要

このサンプルには、クライアント側アプリケーションとサーバー側アプリケーションの両方があります。 **クライアント側アプリケーション**は、Microsoft の Fluent UI フレームワークを使用する React/Redux Web アプリケーションです。 このアプリケーションは、クライアント側アプリケーションが Azure に接続するのを助ける ASP.NET Core の**サーバー側アプリケーション**に要求を送信します。 

サンプルは次のようになります。

:::image type="content" source="./media/chat/landing-page.png" alt-text="サンプル アプリケーションのランディング ページを示すスクリーンショット。":::

[Start a Chat]\(チャットを開始\) ボタンを押すと、Web アプリケーションは、サーバー側アプリケーションからユーザー アクセス トークンをフェッチします。 このトークンは、クライアント アプリを Azure Communication Services に接続するために使用されます。 トークンが取得されると、チャットで自分を表す名前と絵文字を指定するように求められます。 

:::image type="content" source="./media/chat/pre-chat.png" alt-text="アプリケーションのチャット前の画面を示すスクリーンショット。":::

表示名と絵文字を構成すると、チャット セッションに参加できるようになります。 これで、コア チャット エクスペリエンスを提供するメインのチャット キャンバスが表示されます。

:::image type="content" source="./media/chat/main-app.png" alt-text="サンプル アプリケーションのメイン画面を示すスクリーンショット。":::

メイン チャット画面のコンポーネント:

- **メイン チャット領域**: これは、ユーザーがメッセージを送受信できるコア チャット エクスペリエンスです。 メッセージを送信するには、入力領域を使用して Enter キーを押します (または、[送信] ボタンを使用します)。 受信されたチャット メッセージは、適切な名前と絵文字を使って送信者によって分類されます。 チャット領域には、次の 2 種類の通知が表示されます。1) ユーザーが入力中の入力通知と、2) メッセージの送信済みと既読の通知。
- **ヘッダー**:ここには、チャット スレッドのタイトルと、参加者と設定のサイド バーを切り替えるためのコントロール、およびチャット セッションを終了するための退出ボタンがユーザーに表示されます。
- **サイド バー**: ヘッダーでコントロールを使用して切り替えると、ここに参加者と設定情報が表示されます。 参加者のサイド バーには、チャットの参加者の一覧と、チャット セッションに参加者を招待するためのリンクが含まれています。 設定のサイド バーでは、チャット スレッドのタイトルを構成できます。 

以下で、サンプルを設定するための前提条件と手順について詳しく説明します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウントを作成します。 詳細については、[無料アカウントの作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)に関するページを参照してください。
- [Node.js (8.11.2 以上)](https://nodejs.org/en/download/)
- [Visual Studio (2017 以上)](https://visualstudio.microsoft.com/vs/)
- [.NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core/2.2) (使用する Visual Studio インスタンス、32 または 64 ビットに対応するバージョンを必ずインストールしてください)
- Azure Communication Services リソースを作成します。 詳細については、[Azure Communication リソースの作成](../quickstarts/create-communication-resource.md)に関するページを参照してください。 このクイックスタートで使用するリソースの**接続文字列**を記録する必要があります。

## <a name="locally-deploying-the-service--client-app"></a>サービスおよびクライアント アプリをローカルにデプロイする

シングル スレッドのチャット サンプルは、基本的に 2 つの "アプリケーション" (クライアントとサーバー アプリケーション) です。

chat.csproj で Visual Studio を開き、デバッグ モードで実行します。これにより、チャットのフロントエンド サービスが開始されます。 ブラウザーからサーバー アプリにアクセスすると、ローカルにデプロイされているチャットのフロントエンド サービスにトラフィックがリダイレクトします。

このサンプルをローカルでテストするには、チャットの URL を使用して複数のブラウザー セッションを開き、マルチユーザー チャットをシミュレートします。

### <a name="before-running-the-sample-for-the-first-time"></a>サンプルを初めて実行する前

1. PowerShell、Windows ターミナル、コマンド プロンプト、またはそれと同等のインスタンスを開き、サンプルの複製先のディレクトリに移動します。
2. `git clone`
3. **Chat/ClientApp** フォルダーにアクセスし、`npm run setup` を実行します。
   1. エラー 1 が表示された場合は、上記の出力で、クライアントを承認するためにアクセスする必要がある URL を確認します (URL は次のようになります: `app.vssps.visualstudio.com/oauth2/authorize?clientid=...`)。ブラウザーで URL にアクセスしたら、ブラウザー ウィンドウからコマンドをコピーして実行します。
   2. 前の手順を完了したら、コマンド `npm run setup` をもう一度実行します。
4. Azure portal から `Connection String` を取得します。 接続文字列の詳細については、[Azure Communication リソースの作成](../quickstarts/create-communication-resource.md)に関するページを参照してください。
5. `Connection String` を取得したら、Chat フォルダーにある **Chat/appsettings.json** ファイルに接続文字列を追加します。 変数 `ResourceConnectionString` に接続文字列を入力します。

### <a name="local-run"></a>ローカルの実行

1. Chat フォルダーに移動します。
2. Visual Studio で `Chat.csproj` ソリューションを開きます。
3. `Chat` プロジェクトを実行します。*

\* ブラウザーは、localhost: 5000 (ノードがクライアント アプリをデプロイしている場所) で開きます。 アプリは Internet Explorer ではサポートされていません。

#### <a name="troubleshooting"></a>トラブルシューティング

- ソリューションがビルドされない。NPM のインストール/ビルド中にエラーがスローされる

C# ソリューションをクリーン/リビルドする

## <a name="publish-the-sample-to-azure"></a>サンプルを Azure に発行する

1. `Chat` プロジェクトを右クリックし、[発行] を選択します。
2. 新しい発行プロファイルを作成し、Azure サブスクリプションを選択します。
3. 発行する前に、`Edit App Service Settings` を使用して接続文字列を追加し、`ResourceConnectionString` をキーとして入力し、(appsettings.json からコピーした) 接続文字列を値として指定します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Communication Services サブスクリプションをクリーンアップして削除する場合は、リソースまたはリソース グループを削除できます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。 詳細については、[リソースのクリーンアップ](../quickstarts/create-communication-resource.md#clean-up-resources)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。

- [チャットの概念](../concepts/chat/concepts.md)について学習する
- [チャット クライアント ライブラリ](../concepts/chat/sdk-features.md)について理解する

## <a name="additional-reading"></a>その他の情報

- [Azure Communication のプレビュー](https://github.com/Azure/communication-preview) - チャット Web SDK の詳細
- [Redux](https://redux.js.org/) - クライアント側の状態管理
- [FluentUI](https://developer.microsoft.com/fluentui#/) - Microsoft が提供する UI ライブラリ
- [React](https://reactjs.org/) - ユーザー インターフェイスを構築するためのライブラリ
- [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1&preserve-view=true) - Web アプリケーションを構築するためのフレームワーク
