---
title: "Azure AD v2 JS SPA ガイドのセットアップ - 構成 |Microsoft ドキュメント"
description: "JavaScript SPA アプリケーションで、アクセス トークンを必要とする API を Azure Active Directory v2 エンドポイントから呼び出す方法"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/01/2017
ms.author: andret
ms.openlocfilehash: adff529bfdc40006666cc643d49a302d7f1d09ad
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2017
---
## <a name="register-your-application"></a>アプリケーションの登録

さまざまな方法でアプリケーションを作成するには、それらのいずれかを選択してください。

### <a name="option-1-register-your-application-express-mode"></a>オプション 1: アプリケーション (高速モード) の登録します。
次の手順に従って *Microsoft アプリケーション登録ポータル*でアプリケーションを登録する必要があります。

1.  [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure)でアプリケーションを登録します。
2.  アプリケーションの名前とお使いのメール アドレスを入力します
3.  *ガイド付きセットアップ*のオプションがオンになっていることを確認します
4.  手順に従ってアプリケーション ID を取得し、それをコードに貼り付けます。

### <a name="option-2-register-your-application-advanced-mode"></a>オプション 2: アプリケーション (高度なモード) の登録します。

1. [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/portal/register-app)に移動して、アプリケーションを登録します
2. アプリケーションの名前とお使いのメール アドレスを入力します 
3. *ガイド付きセットアップ*のオプションがオフになっていることを確認します
4.  [`Add Platform`] \(プラットフォームの追加) をクリックし、[`Web`] を選択します
5. 追加、 `Redirect URL` web サーバーに基づくアプリケーションの URL に対応します。 Visual Studio および Python でリダイレクト URL を取得または設定する方法の手順については、次のセクションを参照してください。
6. *[保存]*

> #### <a name="visual-studio-instructions-for-obtaining-redirect-url"></a>Visual Studio で、リダイレクト URL を取得する手順
> リダイレクト URL を取得する手順に従います。
> 1.    *ソリューション エクスプローラー*で、プロジェクト選択して [`Properties`] ウィンドウを確認します ([プロパティ] ウィンドウが表示されない場合は `F4` を押します)
> 2.    `URL` からクリップボードに値をコピーします<br/> ![プロジェクトのプロパティ](media/active-directory-singlepageapp-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    戻り、*アプリケーション登録ポータル*として値を貼り付けます、 `Redirect URL` [上書き保存] をクリック

<p/>

> #### <a name="setting-redirect-url-for-python"></a>Python のリダイレクト URL の設定
> Python の場合、コマンド ラインで Web サーバー ポートを設定できます。 このガイドの設定では、参照用にポート 8080 を使用しますが、他の利用可能なポートを自由にお使いいただけます。 いずれの場合も、下記の手順に従って、アプリケーションの登録情報にリダイレクト URL を設定します。<br/>
> - 戻る、*アプリケーション登録ポータル*設定と`http://localhost:8080/`として、 `Redirect URL`、使用または`http://localhost:[port]/`カスタム TCP ポートを使用している場合 (ここで*[port]*カスタム TCP ポート番号) し、[上書き保存] をクリックして


#### <a name="configure-your-javascript-spa"></a>JavaScript SPA を構成します。

1.  アプリケーション登録情報を含む `msalconfig.js` という名前のファイルを作成します。 Visual Studio を使用している場合は、プロジェクト (プロジェクト ルート フォルダー) を選択し、右クリックして `Add` > `New Item` > `JavaScript File` を選択します。 これに `msalconfig.js` という名前を付けます。
2.  次のコードを `msalconfig.js` ファイルに追加します。

```javascript
var msalconfig = {
    clientID: "Enter_the_Application_Id_here",
    redirectUri: location.origin
};
```
<ol start="3">
<li>
<code>Enter_the_Application_Id_here</code> を、さきほど登録したアプリケーション ID に置き換えます
</li>
</ol>
