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
ms.openlocfilehash: 038ea48bedeb31416627f99b38ebb083846747e4
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843527"
---
## <a name="register-your-application"></a>アプリケーションの登録

アプリケーションを作成する方法は複数あります。いずれかを選択してください。

### <a name="option-1-register-your-application-express-mode"></a>オプション 1: アプリケーションを登録する (簡易モード)
次の手順に従って *Microsoft アプリケーション登録ポータル*でアプリケーションを登録する必要があります。

1.  [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure)からアプリケーションを登録します。
2.  アプリケーションの名前とお使いのメール アドレスを入力します。
3.  **ガイド付きセットアップ**のオプションがオンになっていることを確認します。
4.  手順に従ってアプリケーション ID を取得し、それをコードに貼り付けます。

### <a name="option-2-register-your-application-advanced-mode"></a>オプション 2: アプリケーションを登録する (詳細モード)

1. [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/portal/register-app)に移動して、アプリケーションを登録します。
2. アプリケーションの名前とお使いのメール アドレスを入力します。
3. **ガイド付きセットアップ**のオプションがオフになっていることを確認します。
4.  **[プラットフォームの追加]** をクリックし、**[Web]** を選択します。
5. Web サーバーに基づくアプリケーションの URL に対応する **リダイレクト URL** を追加します。 Visual Studio と Node でリダイレクト URL を設定および取得する方法の手順については、次のセクションを参照してください。
6. **[保存]** を選択します。

> #### <a name="setting-redirect-url-for-node"></a>Node のリダイレクト URL の設定
> Node.js の場合は、Web サーバーのポートを *server.js* ファイルで設定できます。 このチュートリアルでは、参照用にポート 30662 を使用しますが、他の利用可能なポートを自由にお使いいただけます。 いずれの場合も、下記の手順に従って、アプリケーションの登録情報にリダイレクト URL を設定します。<br/>
> - "*アプリケーション登録ポータル*" に戻り、`http://localhost:30662/` を `Redirect URL` として設定するか、カスタム TCP ポートを使用する場合は `http://localhost:[port]/` (*[port]* はカスタム TCP ポート番号) を使用した後、[保存] をクリックします。

<p/>

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>リダイレクト URL を取得するための Visual Studio での手順
> リダイレクト URL を取得するには、次の手順に従います。
> 1.    **ソリューション エクスプローラー**でプロジェクトを選択し、**[プロパティ]** ウィンドウを確認します。 **[プロパティ]** ウィンドウが表示されない場合は、**F4** キーを押します。
> 2.    **URL** からクリップボードに値をコピーします。<br/> ![プロジェクトのプロパティ](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    *アプリケーション登録ポータル*に戻り、**リダイレクト URL** として値を貼り付け、**[保存]** をクリックします


#### <a name="configure-your-javascript-spa"></a>JavaScript SPA の構成

1.  プロジェクトの設定中に作成された `index.html` ファイルで、アプリケーション登録情報を追加します。 `index.html` ファイルの本文の `<script></script>` タグ内で、先頭に次のコードを追加します。

```javascript
var applicationConfig = {
    clientID: "[Enter the application Id here]",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
<ol start="3">
<li>
<code>Enter the application Id here</code> を、先ほど登録したアプリケーション ID に置き換えます。
</li>
</ol>
