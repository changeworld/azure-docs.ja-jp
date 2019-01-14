---
title: Azure Digital Twins 用に Postman を構成する方法 | Microsoft Docs
description: Azure Digital Twins 用に Postman を構成する方法
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: adgera
ms.openlocfilehash: 92ff8cb732c7c10c525d8a8ec76180cb435bd466
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2019
ms.locfileid: "53975017"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Azure Digital Twins 用に Postman を構成する方法

この記事では、OAuth 2.0 の暗黙的な許可フローを使用するように Azure Active Directory (Azure AD) アプリケーションを構成する方法について説明します。 その後で、Management API に対してトークンを使用する HTTP 要求を行うように Postman REST クライアントを構成する方法について説明します。

## <a name="postman-summary"></a>Postman の概要

ローカル テスト環境を準備するには、[Postman](https://www.getpostman.com/) などの REST クライアント ツールを使用して Azure Digital Twins を開始します。 Postman クライアントは、複雑な HTTP 要求を簡単に作成するのに役立ちます。 Postman クライアントのデスクトップ バージョンをダウンロードするには、[www.getpostman.com/apps](https://www.getpostman.com/apps) にアクセスします。

[Postman](https://www.getpostman.com/) は、便利なデスクトップとプラグイン ベースの GUI に重要な HTTP 要求機能を配置する REST テスト ツールです。 ソリューション開発者は、Postman クライアントを使用して、HTTP 要求の種類 (POST、GET、UPDATE、PATCH、DELETE)、呼び出す API エンドポイント、SSL の使用を指定できます。 また、Postman では、HTTP 要求ヘッダー、パラメーター、フォーム データ、本文の追加もサポートされています。

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>OAuth 2.0 の暗黙的な許可フローを使用するように Azure Active Directory を構成する

OAuth 2.0 の暗黙的な許可フローを使用するように、Azure AD アプリを構成します。

1. [こちらのクイック スタート](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad)の手順に従って、種類がネイティブの Azure AD アプリケーションを作成します。 または、既存のネイティブ アプリの登録を再利用することもできます。

1. **[必要なアクセス許可]** で、**[追加]** を選択し、**[API アクセスの追加]** に「**Azure Digital Twins**」と入力します。 検索で API が見つからない場合は、代わりに **Azure Smart Spaces** を検索します。 次に、**[アクセス許可の付与] > [委任されたアクセス許可]** を選択し、**[完了]** を選択します。

    ![Azure AD のアプリ登録追加 API](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)

1. **[マニフェスト]** をクリックして、アプリのアプリケーション マニフェストを開きます。 *oauth2AllowImplicitFlow* を `true` に設定します。

      ![Azure AD の暗黙的なフロー][1]

1. **[応答 URL]** を [`https://www.getpostman.com/oauth2/callback`](https://www.getpostman.com/oauth2/callback) に構成します。

      ![Azure AD の応答 URL][2]

1. Azure AD アプリの**アプリケーション ID** をコピーして保管します。 後で使用します。

## <a name="configure-the-postman-client"></a>Postman クライアントを構成する

次に、Postman をセットアップし、Azure AD トークンを取得するように構成します。 その後、取得したトークンを使用し、Azure Digital Twins に対して認証済みの HTTP 要求を行います。

1. [www.getpostman.com]([https://www.getpostman.com/) に移動してアプリをダウンロードします。
1. **[承認 URL]** が正しいことを確認します。 次のような形式になっている必要があります。

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Name  | 置換後の文字列 | 例 |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | テナントまたは組織の名前 | `microsoft` |

1. **[承認] タブ**を選択し、**[OAuth 2.0]** を選択して **[Get New Access Token]\(新しいアクセス トークンの取得\)** を選択します。

    | フィールド  | 値 |
    |---------|---------|
    | 付与タイプ | `Implicit` |
    | コールバック URL | `https://www.getpostman.com/oauth2/callback` |
    | 認証 URL | 上のステップ 2 の**承認 URL**を使用します |
    | クライアント ID | 前のセクションで作成または別の用途のために作り変えた Azure AD アプリの**アプリケーション ID** を使用します |
    | Scope (スコープ) | 空白 |
    | 状態 | 空白 |
    | クライアント認証 | `Send as Basic Auth header` |

1. クライアントは次のようになります。

   ![Postman クライアントの例][3]

1. **[Request Token]\(要求トークン\)** を選択します。

    >[!NOTE]
    >「OAuth 2 couldn’t be completed」(OAuth 2 を完了できませんでした) というエラー メッセージを受け取った場合は、次の操作を試してください。
    > * Postman を閉じて再起動し、もう一度実行する。
  
1. 下へスクロールし、**[Use Token]\(トークンの使用\)** を選択します。

## <a name="next-steps"></a>次の手順

Management API を使用した認証については、[API を使用した認証](./security-authenticating-apis.md)に関するページをご覧ください。

<!-- Images -->
[1]: media/how-to-configure-postman/implicit-flow.png
[2]: media/how-to-configure-postman/reply-url.png
[3]: media/how-to-configure-postman/postman-oauth-token.png
