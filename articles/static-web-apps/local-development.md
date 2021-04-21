---
title: Azure Static Web Apps 用にローカル開発環境を設定する
description: Azure Static Web Apps 用のローカル開発環境を設定する方法について説明します
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 04/02/2021
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: e19d39a32d48ec55473bb957595d47ec5148e74b
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588787"
---
# <a name="set-up-local-development-for-azure-static-web-apps-preview"></a>Azure Static Web Apps プレビュー用にローカル開発環境を設定する

Azure Static Web Apps サイトには、クラウドに発行されると、同じアプリケーションであるかのように連携して動作する多くのサービスがあります。 次のようなサービスがあります。

- 静的 Web アプリ
- Azure Functions API
- 認証と承認のサービス
- ルーティングと構成のサービス

これらのサービスは相互に通信する必要があり、Azure Static Web Apps によってクラウドでこの統合が処理されます。

ただし、ローカルで実行している場合、これらのサービスは自動的に関連付けられません。

Azure で使用した場合と同様のエクスペリエンスを提供するために、[Azure Static Web Apps CLI](https://github.com/Azure/static-web-apps-cli) には次のサービスが用意されています。

- ローカルの静的サイト サーバー
- フロントエンド フレームワーク開発サーバーへのプロキシ
- API エンドポイントへのプロキシ (Azure Functions Core Tools を通じて利用可能)
- モックの認証と承認サーバー
- ローカル ルートと構成設定の適用

## <a name="how-it-works"></a>しくみ

次のグラフは、要求がローカルで処理される方法を示しています。

:::image type="content" source="media/local-development/cli-conceptual.png" alt-text="Azure Static Web Apps CLI の要求と応答のフロー":::

> [!IMPORTANT]
> CLI によって処理されるアプリケーションにアクセスするには、`http://localhost:4280` に移動します。

- ポート `4280` に対して行われた **要求** は、要求の種類に応じて適切なサーバーに転送されます。

- HTML や CSS などの **静的コンテンツ** 要求は、内部 CLI 静的コンテンツ サーバーによって、またはデバッグのためにフロントエンド フレームワーク サーバーによって処理されます。

- **認証と承認** 要求は、アプリにフェイク ID プロファイルを提供するエミュレーターによって処理されます。

- **Functions Core Tools ランタイム** では、サイトの API への要求が処理されます。

- すべてのサービスからの **応答** は、すべて 1 つのアプリケーションであるかのようにブラウザーに返されます。

## <a name="prerequisites"></a>前提条件

- **既存の Azure Static Web Apps サイト**: お持ちでない場合は、[vanilla-api](https://github.com/staticwebdev/vanilla-api/generate?return_to=/staticwebdev/vanilla-api/generate) スターター アプリから開始してください。
- **npm を含む [Node.js](https://nodejs.org)** : [npm](https://www.npmjs.com/) へのアクセスを含む [Node.js LTS](https://nodejs.org) バージョンを実行します。
- **[Visual Studio Code](https://code.visualstudio.com/)** : API アプリケーションのデバッグに使用されますが、CLI には必要ありません。

## <a name="get-started"></a>作業開始

ターミナルで、既存の Azure Static Web Apps サイトのルート フォルダーを開きます。

1. CLI をインストールします。

    `npm install -g @azure/static-web-apps-cli`

1. アプリケーションで必要な場合は、アプリをビルドします。

    `npm run build`、またはプロジェクトに対応する同等のコマンドを実行します。

1. アプリの出力ディレクトリに移動します。 多くの場合、出力フォルダーには _build_ のような名前が付けられています。

1. CLI を起動します。

    `swa start`

1. http://localhost:4280 に移動して、ブラウザーでアプリを表示します。

### <a name="other-ways-to-start-the-cli"></a>CLI を起動するその他の方法

| 説明 | コマンド |
|--- | --- |
| 特定のフォルダーを処理する | `swa start ./output-folder` |
| 実行中のフレームワーク開発サーバーを使用する | `swa start http://localhost:3000` |
| フォルダー内の Functions アプリを起動する | `swa start ./output-folder --api ./api` |
| 実行中の Functions アプリを使用する | `swa start ./output-folder --api http://localhost:7071` |

## <a name="authorization-and-authentication-emulation"></a>承認と認証のエミュレーション

Azure Static Web Apps CLI は、Azure で実装されている[セキュリティ フロー](./authentication-authorization.md)をエミュレートします。 ユーザーがログインするときに、アプリに返されるフェイク ID プロファイルを定義できます。

たとえば、`/.auth/login/github` に移動しようとすると、ID プロファイルを定義できるページが返されます。

> [!NOTE]
> エミュレーターは、GitHub だけでなく、あらゆるセキュリティ プロバイダーで動作します。

:::image type="content" source="media/local-development/auth-emulator.png" alt-text="ローカルの認証と承認のエミュレーター":::

エミュレーターによって、次の[クライアント プリンシパル](./user-information.md#client-principal-data)の値を指定できるページが提供されます。

| 値 | 説明 |
| --- | --- |
| **ユーザー名** | セキュリティ プロバイダーに関連付けられているアカウント名。 この値は、クライアント プリンシパルの `userDetails` プロパティとして表示され、値を指定しない場合に自動生成されます。 |
| **[ユーザー ID]** | CLI によって自動生成される値。  |
| **ロール** | ロール名のリスト。各名前は新しい行に表示されます。  |

ログインすると、次のようになります。

- `/.auth/me` エンドポイント、または関数エンドポイントを使用して、ユーザーの[クライアント プリンシパル](./user-information.md)を取得できます。

- `./auth/logout` に移動すると、クライアント プリンシパルがクリアされ、モック ユーザーがログアウトします。

## <a name="debugging"></a>デバッグ

静的 Web アプリには、2 つのデバッグ コンテキストがあります。 1 つ目は静的コンテンツ サイト用で、2 つ目は API 関数用です。 Static Web Apps CLI でこれらのコンテキストのいずれかまたは両方に開発サーバーを使用できるようにすることで、ローカル デバッグを実行できます。

次の手順では、両方のデバッグ コンテキストで開発サーバーを使用する一般的なシナリオを示します。

1. 静的サイト開発サーバーを起動します。 このコマンドは、使用しているフロントエンド フレームワークに固有のものですが、多くの場合、`npm run build`、`npm start`、または `npm run dev` のようなコマンドの形式になります。

1. Visual Studio Code で API アプリケーション フォルダーを開き、デバッグ セッションを開始します。

1. 静的サーバーと API サーバーのアドレスを順番にリストして、`swa start` コマンドに渡します。

    `swa start http://localhost:<DEV-SERVER-PORT-NUMBER> --api=http://localhost:7071`

次のスクリーンショットは、一般的なデバッグ シナリオでのターミナルを示しています。

静的コンテンツ サイトは、`npm run dev` を使用して実行されています。

:::image type="content" source="media/local-development/run-dev-static-server.png" alt-text="静的サイト開発サーバー":::

Azure Functions API アプリケーションにより、Visual Studio Code でデバッグ セッションが実行されています。

:::image type="content" source="media/local-development/visual-studio-code-debugging.png" alt-text="Visual Studio Code API のデバッグ":::

Static Web Apps CLI が両方の開発サーバーを使用して起動されます。

:::image type="content" source="media/local-development/static-web-apps-cli-terminal.png" alt-text="Azure Static Web Apps CLI のターミナル":::

ポート `4280` を経由する要求は、静的コンテンツ開発サーバー、または API デバッグ セッションのいずれかにルーティングされるようになりました。

ポートとサーバー アドレスをカスタマイズする方法のガイダンスを含む、さまざまなデバッグ シナリオの詳細については、[Azure Static Web Apps CLI リポジトリ](https://github.com/Azure/static-web-apps-cli)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [アプリケーションの作成](configuration.md)
