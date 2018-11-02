---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: b3d46e10facdef26b36c910a5c23b40a415a2894
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988432"
---
## <a name="register-your-application"></a>アプリケーションの登録

アプリケーションを登録するための複数の方法があります。 ニーズに最適なオプションを以下から選択します。
* [簡易モード - SPA クイック スタートを使用して、アプリを構成します](#option-1-register-your-application-express-mode)
* [詳細設定モード ‐ アプリの設定を手動で構成します](#option-2-register-your-application-advanced-mode)

### <a name="option-1-register-your-application-express-mode"></a>オプション 1: アプリケーションを登録する (簡易モード)

1. [[Azure Portal アプリの登録 (プレビュー)]](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) にサインインして、アプリケーションを登録します。
1. **[アプリケーションの登録]** ページにアプリケーションの名前を入力します。
1. **[サポートされているアカウントの種類]** で、**[Accounts in any organizational directory and personal Microsoft accounts]\(任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント\)** を選択します。
1. 終了したら、**[登録]** を選択します。
1. クイック スタートの手順に従ってダウンロードし、1 回クリックするだけで、新しいアプリケーションが自動的に構成されます。

### <a name="option-2-register-your-application-advanced-mode"></a>オプション 2: アプリケーションを登録する (詳細モード)

1. アプリケーションを登録するために、[Azure portal](https://portal.azure.com/) にサインインします。
1. ご利用のアカウントで複数の Azure AD テナントにアクセスできる場合は、右上隅でアカウントを選択し、ポータルのセッションを目的のテナントに設定します。
1. 左側のナビゲーション ウィンドウで、**[Azure Active Directory]** サービスを選択し、**[アプリの登録 (プレビュー)] > [新規登録]** を選択します。
1. **[アプリケーションの登録]** ページが表示されたら、アプリケーションの名前を入力します。
1. **[サポートされているアカウントの種類]** で、**[Accounts in any organizational directory and personal Microsoft accounts]\(任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント\)** を選択します。
1. **[リダイレクト URI]** セクションで、**Web**プラットフォームを選択し、ご使用の Web サーバーに基づいてアプリケーションの URL に値を設定します。 Visual Studio と Node でリダイレクト URL を設定および取得する方法の手順については、次のセクションを参照してください。
1. 終了したら、**[登録]** を選択します。
1. アプリの **[概要]** ページで、**[Application (client) ID]\(アプリケーション (クライアント) ID\)** の値を書き留めます。
1. このクイック スタートでは、[暗黙的な許可フロー](../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md)を有効にする必要があります。 登録済みのアプリケーションの左側のナビゲーション ウィンドウで、**[認証]** を選択します。
1. **[詳細設定]** の **[暗黙的な許可]** で、**[ID トークン]** と **[アクセス トークン]** の両方のチェック ボックスをオンにします。 このアプリではユーザーをサインインし、API を呼び出す必要があるため、ID トークンとアクセス トークンが必要になります。
1. **[保存]** を選択します。

> #### <a name="setting-the-redirect-url-for-node"></a>Node のリダイレクト URL の設定
> Node.js の場合は、Web サーバーのポートを *server.js* ファイルで設定できます。 このチュートリアルでは、参照用にポート 30662 を使用しますが、その他に使用可能なポートも使用できます。 下記の手順に従って、アプリケーションの登録情報内にリダイレクト URL を設定します。<br/>
> - *[アプリケーションの登録]* に戻り、`http://localhost:30662/` を `Redirect URL` として設定するか、カスタム TCP ポートを使用する場合は `http://localhost:[port]/` (*[port]* はカスタム TCP ポート番号) を使用します。

<p/>

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>リダイレクト URL を取得するための Visual Studio での手順
> リダイレクト URL を取得するには、次の手順に従います。
> 1. **ソリューション エクスプローラー**でプロジェクトを選択し、**[プロパティ]** ウィンドウを確認します。 **[プロパティ]** ウィンドウが表示されない場合は、**F4** キーを押します。
> 2. **URL** からクリップボードに値をコピーします。<br/> ![プロジェクトのプロパティ](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3. *[アプリケーションの登録]* に戻り、**[リダイレクト URL]** として値を設定します。

#### <a name="configure-your-javascript-spa"></a>JavaScript SPA の構成

1. プロジェクトの設定中に作成された `index.html` ファイルで、アプリケーション登録情報を追加します。 `index.html` ファイルの本文の `<script></script>` タグ内で、先頭に次のコードを追加します。

    ```javascript
    var applicationConfig = {
        clientID: "[Enter the application Id here]",
        graphScopes: ["user.read"],
        graphEndpoint: "https://graph.microsoft.com/v1.0/me"
    };
    ```

<ol start="2">
<li>
<code>Enter the application Id here</code> を、先ほど登録したアプリケーション ID に置き換えます。
</li>
</ol>
