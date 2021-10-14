---
title: Internet Explorer および Microsoft Edge での問題 (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: JavaScript 用 Microsoft Authentication Library (MSAL.js) を Internet Explorer および Microsoft Edge ブラウザーで使用するときの既知の問題について説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/18/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: ef1cce7905e07c1fa407c0194d585bb196ecdfd6
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129232206"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Internet Explorer および Microsoft Edge ブラウザーに関する既知の問題 (MSAL.js)

## <a name="issues-due-to-security-zones"></a>セキュリティ ゾーンに起因する問題
IE と Microsoft Edge での認証に関する問題が複数報告されています (*Microsoft Edge ブラウザー バージョン 40.15063.0.0* へのアップデート以来)。 当社はこれらの問題を追跡中で、Microsoft Edge チームに通知済みです。 Microsoft Edge は解決に向けて取り組んでいますが、頻繁に発生する問題と、実行できる可能な回避策について以下に説明します。

### <a name="cause"></a>原因
これらのほとんどの問題の原因は次のとおりです。 Microsoft Edge ブラウザーでは、セッション ストレージとローカル ストレージはセキュリティ ゾーンによって分割されています。 この特定のバージョンの Microsoft Edge では、アプリケーションがゾーンをまたいでリダイレクトされたときに、セッション ストレージとローカル ストレージがクリアされます。 具体的には、セッション ストレージは通常のブラウザー ナビゲーションでクリアされ、セッション ストレージとローカル ストレージの両方がブラウザーの InPrivate モードでクリアされます。 MSAL.js は特定の状態をセッション ストレージに保存し、認証フロー中にこの状態のチェックに依存します。 セッション ストレージがクリアされると、この状態が失われるためエクスペリエンスが途切れることになります。

### <a name="issues"></a>発行

- **認証時に無限のリダイレクト ループとページの再読み込みが発生する**。 ユーザーが Microsoft Edge 上のアプリケーションにサインインすると、AAD ログイン ページからリダイレクトされ、無限のリダイレクト ループに陥ってページの再読み込みが繰り返し発生します。 これは通常、セッション ストレージの `invalid_state` エラーを伴います。

- **トークンの取得の無限ループと AADSTS50058 エラー**。 Microsoft Edge で実行されているアプリケーションがリソースのトークンを取得しようとすると、アプリケーションはトークン取得呼び出しの無限ループに陥り、ネットワーク トレースで AAD からの次のエラーが発生します。

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **ポップアップ ウィンドウ経由のログインを使用して認証するとき、ポップアップ ウィンドウが閉じない、または動作が停止する**。 Microsoft Edge または IE (InPrivate) でポップアップ ウィンドウを使用して認証するとき、資格情報を入力してサインインした後、セキュリティ ゾーンをまたぐ複数のドメインがナビゲーションに関係している場合、`MSAL.js` がポップアップ ウィンドウのハンドルを失うためポップアップ ウィンドウが閉じなくなります。

### <a name="update-fix-available-in-msaljs-023"></a>Update: MSAL.js 0.2.3 で使用可能な修正プログラム
認証のリダイレクト ループの問題に対する修正プログラムが [MSAL.js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases) でリリースされました。 この修正プログラムを利用するには、MSAL.js 構成ファイルのフラグ `storeAuthStateInCookie` を有効にします。 既定では、このフラグは false に設定されています。

`storeAuthStateInCookie` フラグが有効になると、MSAL.js はブラウザーの Cookie を使用して、認証フローを検証するために必要な要求の状態を保存します。

> [!NOTE]
> `msal-angular` と `msal-angularjs` のラッパーについては、この修正プログラムはまだ利用できません。 この修正プログラムはポップアップ ウィンドウの問題には対処していません。

以下の回避策を使用してください。

#### <a name="other-workarounds"></a>その他の回避策
これらの回避策を採用する前に、この問題が Microsoft Edge ブラウザーの特定のバージョンでのみ発生していて、その他のブラウザーは動作することをテストしてください。
1. これらの問題を回避する最初の手順として、アプリケーション ドメインと、認証フローのリダイレクトに関与するその他のすべてのサイトが、ブラウザーのセキュリティ設定の信頼済みサイトとして追加されており、これらが同じセキュリティ ゾーンに属していることを確認します。
これを行うには、次のステップに従います。
    - **Internet Explorer** を開き、右上隅にある **[設定]** (歯車アイコン) をクリックします。
    - **[インターネット オプション]** を選択します
    - **[セキュリティ]** タブを選択します
    - **[信頼済みサイト]** オプションで、 **[サイト]** ボタンをクリックし、表示されたダイアログ ボックスに URL を追加します。

2. 前述した通り、通常のナビゲーション中にセッション ストレージのみがクリアされるため、代わりにローカル ストレージを使用するように MSAL.js を構成できます。 これは MSAL の初期化中に `cacheLocation` 構成パラメーターとして設定できます。

ただし、この方法ではセッション ストレージとローカル ストレージの両方がクリアされるため、InPrivate での参照の問題は解決されないことに注意してください。

## <a name="issues-due-to-popup-blockers"></a>ポップアップ ブロック機能に起因する問題

たとえば、[多要素認証](../authentication/concept-mfa-howitworks.md)中に 2 つ目のポップアップが開いたときに、IE や Microsoft Edge でポップアップがブロックされることがあります。 ポップアップ ウィンドウを許可するのを 1 回のみにするか、常時にするかについてブラウザーでアラートが表示されます。 許可することを選択した場合、ブラウザーにより自動的にポップアップ ウィンドウが開かれ、`null` ハンドルが返されます。 その結果、ライブラリにウィンドウのハンドルがないため、ポップアップ ウィンドウを閉じる方法がなくなります。 Chrome では、ポップアップ ウィンドウが自動的に開かないため、ポップアップ ウィンドウを許可するプロンプトを表示するときに、同じ問題が発生しません。

**回避策** としては、アプリの使用を開始する前に、この問題を回避するために、開発者は IE と Microsoft Edge でポップアップを許可する必要があります。

## <a name="next-steps"></a>次のステップ
[Internet Explorer での MSAL.js の使用](msal-js-use-ie-browser.md)について詳しく学習します。
