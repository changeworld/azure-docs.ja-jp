---
title: Google を B2B 用 ID プロバイダーとして追加する - Azure AD
description: Google とのフェデレーションを行って、ゲスト ユーザーが独自の Gmail アカウントを使用して Azure AD アプリにサインインできるようにします。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 10/01/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: 510e2207a2c25c5da5f4de08f3bf16fbf6cf4c7d
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129354276"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Google を B2B ゲスト ユーザーの ID プロバイダーとして追加する

Google とのフェデレーションを設定することで、招待されたユーザーが Microsoft アカウントを作成することなく、独自の Gmail アカウントを使用して共有アプリおよびリソースにサインインできるようにできます。 アプリケーションのサインイン オプションの 1 つとして Google を追加すると、ユーザーは **[サインイン]** ページで、Google へのサインインに使用するメール アドレスを入力できます。

![Google ユーザーのサインイン オプション](media/google-federation/sign-in-with-google-overview.png)

> [!NOTE]
> Google フェデレーションは Gmail ユーザー専用に設計されています。 Google Workspace ドメインとのフェデレーションを行うには、[SAML/WS-Fed ID プロバイダー フェデレーション](direct-federation.md)を使用します。

> [!IMPORTANT]
>
> - **2021 年 7 月 12 日以降では**、Azure AD B2B のお客様がセルフサービス サインアップで使用するため、あるいはカスタム アプリケーションまたは基幹業務アプリケーションに外部ユーザーを招待するために、新しい Google 統合をセットアップした場合、Gmail ユーザーの認証がブロックされる可能性があります (以下の「[予期される事柄](#what-to-expect)」に示すようなエラー画面が表示されます)。 この問題が発生するのは、2021 年 7 月 12日 以降にセルフサービス サインアップ ユーザー フローまたは招待のために Google 統合を作成し、カスタム アプリケーションまたは基幹業務アプリケーションの Gmail 認証がシステム Web ビューに移動されていない場合のみです。 システム Web ビューは既定で有効になっているため、ほとんどのアプリは影響を受けません。 この問題を回避するには、セルフサービス サインアップのために新しい Google 統合を作成する前に、Gmail 認証をシステム ブラウザーに移動することを強くお勧めします。 「[埋め込み Web ビューに必要な対応](#action-needed-for-embedded-frameworks)」を参照してください。
> - **2021 年 9 月 30 日以降**、Google では [Web ビューのサインイン サポートが非推奨](https://developers.googleblog.com/2021/06/upcoming-security-changes-to-googles-oauth-2.0-authorization-endpoint.html)になります。 アプリで埋め込み Web ビューを使用してユーザーを認証していて、Google フェデレーションを [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md)、Azure AD B2B (外部ユーザーの招待用)、または[セルフサービス サインアップ](identity-providers.md)で使用している場合、Google Gmail ユーザーが認証されなくなります。 [詳細については、こちらを参照してください](#deprecation-of-web-view-sign-in-support)。

## <a name="what-is-the-experience-for-the-google-user"></a>Google ユーザーのエクスペリエンスの内容

Google ユーザーが招待を引き換えたときのエクスペリエンスは、Google に既にサインインしているかどうかによって異なります。

- Google にサインインしていないゲストユーザーには、そうするように求めるメッセージが表示されます。
- Google に既にサインインしているゲスト ユーザーには、使用するアカウントの選択を求めるメッセージが表示されます。 招待に使用されたアカウントを選択する必要があります。

"ヘッダーが長すぎます" というエラーが表示されたゲスト ユーザーは、Cookie をクリアするか、プライベートまたは匿名のウィンドウを開いて、もう一度サインインを試すことができます。

![Google のサインイン ページを示すスクリーンショット。](media/google-federation/google-sign-in.png)

## <a name="sign-in-endpoints"></a>サインインのエンドポイント

Google ゲスト ユーザーは、[共通のエンドポイント](redemption-experience.md#redemption-and-sign-in-through-a-common-endpoint) (つまり、テナント コンテキストを含まない一般的なアプリ URL) を使用して、マルチテナントまたは Microsoft のファーストパーティ アプリにサインインできるようになりました。 サインイン プロセス中に、ゲスト ユーザーは **[サインイン オプション]** を選択してから、 **[Sign in to an organization]\(組織にサインイン\)** を選択します。 次に、ユーザーは組織の名前を入力し、Google 資格情報を使用してサインインを続行します。

Google ゲスト ユーザーは、テナント情報を含むアプリケーション エンドポイントを使用することもできます。次に例を示します。

  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`
  * `https://portal.azure.com/<your tenant ID>`

また、`https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>` のようにテナント情報を含めることによって、アプリケーションまたはリソースへの直接リンクを Google ゲスト ユーザーに提供することもできます。

## <a name="deprecation-of-web-view-sign-in-support"></a>Web ビュー サインイン サポートの廃止

2021 年の 9 月 30 日より、Google は[埋め込み Web ビューのサインイン サポートを廃止](https://developers.googleblog.com/2021/06/upcoming-security-changes-to-googles-oauth-2.0-authorization-endpoint.html)します。 アプリで埋め込み Web ビューを使用してユーザーを認証していて、Google フェデレーションを [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md)、Azure AD B2B [(外部ユーザーの招待用)](google-federation.md)、または[セルフサービス サインアップ](identity-providers.md)で使用している場合、Google Gmail ユーザーが認証されなくなります。

Gmail ユーザーに影響を与える既知のシナリオを次に示します。
- Windows 上の Microsoft アプリ (Teams や Power Apps など) 
- [WebView](/windows/communitytoolkit/controls/wpf-winforms/webview) コントロール、[WebView2](/microsoft-edge/webview2/)、または古い WebBrowser コントロールを認証で使用する Windows アプリ。 これらのアプリは、Web アカウント マネージャー (WAM) フローの使用へ移行する必要があります。
- WebView UI 要素を使用した Android アプリケーション 
- UIWebView または WKWebview を使用した iOS アプリケーション 
- [ADAL を使用したアプリ](../develop/howto-get-list-of-all-active-directory-auth-library-apps.md)

この変更は以下のものには影響しません。
- Web アプリ
- web サイト経由でアクセスされる Microsoft 365 サービス (SharePoint オンライン、Office Web アプリ、Teams Web アプリなど)
- 認証でシステム Web ビューを使用したモバイルアプリ (iOS の [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller)、Android の[カスタム タブ](https://developer.chrome.com/docs/android/custom-tabs/overview/))。  
- Google Workspace ID (たとえば、Google Workspace との [SAML ベースのフェデレーション](direct-federation.md)を使用している場合)
- Web アカウント マネージャー (WAM) または Web 認証ブローカー (WAB) を使用する Windows アプリ。  

### <a name="action-needed-for-embedded-web-views"></a>埋め込み Web ビューに必要な対応

サインインにシステム ブラウザーを使用するようにアプリを変更します。 詳細については、MSAL.NET のドキュメントの「[埋め込み Web UI とシステム Web UI の比較](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui)」を参照してください。 すべての MSAL SDK が既定でシステム Web ビューを使用します。

### <a name="what-to-expect"></a>ウィザードの内容

Microsoft は、9 月 30 日より、埋め込み Web ビューを現在も使用しているアプリケーションの認証がブロックされないようにするための回避策となる、デバイスのサインイン フローをグローバルに展開します。

### <a name="how-to-sign-in-with-the-device-sign-in-flow"></a>デバイスのサインイン フローでサインインする方法

デバイスのサインイン フローでは、Gmail アカウントを使用して埋め込み Web ビューからサインインを行ったユーザーに対して、サインインを完了する前に別のブラウザーでコードを入力するように求めるメッセージを表示します。 ユーザーが、ブラウザー上にアクティブなセッションがない状態で初めて Gmail アカウントでサインインした場合、次のような一連の画面が表示されます。 既存の Gmail アカウントで既にサインインしている場合は、これらの手順の一部が省略される可能性があります。

1. **[サインイン]** 画面で、ユーザーが自身の Gmail のアドレスを入力し、 **[次へ]** を選択します。

   ![サインイン画面を示すスクリーンショット](media/google-federation/1-sign-in.png)

1. ユーザーに、新しいウィンドウを開いて https://microsoft.com/devicelogin に移動し、表示された 9 桁の英数字を入力するよう求める次のような画面が表示されます。

   ![9 桁のコードを示すスクリーンショット](media/google-federation/2-sign-in-code.png)

1. ユーザーがコードを入力できる、デバイスのサインイン ページが開きます。 

   ![デバイスのサインイン ページを示すスクリーンショット](media/google-federation/3-enter-code.png)

1. コードが一致する場合、セキュリティ上の理由から、ユーザーはアプリとサインインの場所を確認するためにメールアドレスを再入力するように求められます。

   ![メールアドレスを再表示するための画面を示すスクリーンショット](media/google-federation/4-sign-in-reenter-email.png)

1. ユーザーは、自身のメールアドレスとパスワードを使用して Google にサインインします。

   ![Google のサインイン画面を示すスクリーンショット](media/google-federation/5-sign-in-with-google.png)

1. ここでも、サインインしようとしているアプリを確認するように求められます。

   ![アプリケーションの確認画面を示すスクリーンショット](media/google-federation/6-confirm-sign-in.png)

1. ユーザーが **[続行]** を選択します。 サインインが完了したことを確認するメッセージが表示されます。 ユーザーがタブまたはウィンドウを閉じると、アプリにサインインした最初の画面に戻ります。

   ![サインインの確認を示すスクリーンショット](media/google-federation/7-app-sign-in-confirmed.png)

または、既存の Gmail ユーザーと新しい Gmail ユーザーに、電子メールのワンタイム パスコードを使用してサインインさせることもできます。 Gmail ユーザーが電子メールのワンタイム パスコードを使用するようにするには、次のようにします。

1. [電子メールのワンタイム パスコードを有効にする](one-time-passcode.md#enable-email-one-time-passcode)
2. [Google フェデレーションを削除する](google-federation.md#how-do-i-remove-google-federation)
3. Gmail ユーザーの[利用状態をリセット](reset-redemption-status.md)して、今後、電子メールのワンタイム パスコードを使用できるようにします。

延長を申請する場合、影響を受ける OAuth クライアント ID をお持ちのお客様には、Google Developers から、2022 年 1 月 31 日までに完了しなければならない 1 回限りのポリシー施行延長に関する以下の情報が記載されたメールが届いているはずです。

- "If necessary, you may request a one-time **policy enforcement extension for embedded webviews** for each listed OAuth client ID until January 31, 2022. (必要に応じて、記載されている OAuth クライアント ID ごとに、2022 年 1 月 31 日までであれば、埋め込み Web ビューのポリシー施行延長を 1 回に限り、要求することができます。) For clarity, the policy for embedded webviews will be enforced on February 1, 2022 with no exceptions or extensions." (明確にするために、埋め込み Web ビューのポリシーは 2022 年 2 月 1 日に施行され、例外や延長はありません。)

許可された認証用 Web ビューに移行しているアプリケーションには影響はなく、ユーザーは通常どおり Google 経由で認証することができます。

許可された認証用 Web ビューにアプリケーションが移行されない場合、影響を受ける Gmail ユーザーには次の画面が表示されます。

![アプリがシステム ブラウザーに移行されない場合の Google サインイン エラー](media/google-federation/google-sign-in-error-ewv.png)

### <a name="distinguishing-between-cefelectron-and-embedded-web-views"></a>CEF/Electron と埋め込み Web ビューを区別する

[埋め込み Web ビューとフレームワーク サインイン サポートの廃止](#deprecation-of-web-view-sign-in-support)に加えて、Google は [Chromium Embedded Framework (CEF) ベースの Gmail 認証の廃止](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html)も行います。 Electron アプリなど、CEF で構築されたアプリケーションについて、Google は 2021 年 6 月 30 日に認証を無効にします。 影響を受けるアプリケーションには Google から直接通知が送られており、このドキュメントでは説明していません。  このドキュメントは、Google が別の日付 (2021 年 9 月 30 日) に制限する予定である、上で説明した埋め込み Web ビューに関連しています。

### <a name="action-needed-for-embedded-frameworks"></a>埋め込みフレームワークに必要な対応

[Google のガイダンス](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)に従って、ご利用のアプリが影響を受けるかどうかを判断します。

## <a name="step-1-configure-a-google-developer-project"></a>手順 1:Google 開発者プロジェクトを構成する

最初に、Google Developers Console で新しいプロジェクトを作成して、Azure Active Directory (Azure AD) に後で追加できるクライアント ID とクライアント シークレットを取得します。 
1. https://console.developers.google.com で Google API に移動し、Google アカウントでサインインします。 共有のチーム Google アカウントを使用することをお勧めします。
2. サービスの使用条件への同意を求めるメッセージが表示されたらそのようにします。
3. 新しいプロジェクトを作成する: ページの左上隅にあるプロジェクトの一覧を選択し、 **[プロジェクトの選択]** ページで **[新しいプロジェクト]** を選択します。
4. **[新しいプロジェクト]** ページで、プロジェクトに名前 (「**Azure AD B2B**」など) を付け、 **[作成]** を選択します。 
   
   ![[新しいプロジェクト] ページを示すスクリーンショット。](media/google-federation/google-new-project.png)

4. **[API とサービス]** ページで、新しいプロジェクトの下にある **[View]\(表示\)** を選択します。

5. API カードで **[Go to APIs overview]\(API の概要に移動\)** を選択します。 **[OAuth 同意画面]** を選択します。

6. **[External]\(外部\)** を選択し、 **[Create]\(作成\)** を選択します。 

7. **[OAuth 同意画面]** で、 **[Application name]\(アプリケーション名\)** を入力します。

   ![Google の [OAuth 同意画面] を示すスクリーンショット。](media/google-federation/google-oauth-consent-screen.png)

8. **[Authorized domains]\(承認済みドメイン\)** セクションまでスクロールし、「**microsoftonline.com**」と入力します。

   ![[Authorized domains]\(承認済みドメイン\) セクションを示すスクリーンショット。](media/google-federation/google-oauth-authorized-domains.PNG)

9. **[保存]** を選択します。

10. **[Credentials]\(資格情報\)** を選択します。 **[Create credentials]\(認証情報の作成\)** メニューで、 **[OAuth client ID]\(OAuth クライアント ID\)** を選択します。

    ![Google API の資格情報作成メニューを示すスクリーンショット。](media/google-federation/google-api-credentials.png)

11. **[アプリケーションの種類]** で **[Web アプリケーション]** を選択します。 アプリケーションの適切な名前を指定します (**Azure AD B2B** など)。 **[Authorized redirect URIs]\(承認されたリダイレクト URI\)** に、次の URI を入力します。
    - `https://login.microsoftonline.com`
    - `https://login.microsoftonline.com/te/<tenant ID>/oauth2/authresp` <br>(`<tenant ID>` はご利用のテナントの ID です)
    - `https://login.microsoftonline.com/te/<tenant name>.onmicrosoft.com/oauth2/authresp` <br>(`<tenant name>` はご利用のテナント名です)
   
    > [!NOTE]
    > テナント ID を確認するには、[Azure portal](https://portal.azure.com) に移動します。 **[Azure Active Directory]** で **[プロパティ]** を選択し、 **[テナント ID]** をコピーします。

    ![[Authorized redirect URIs]\(承認されたリダイレクト URI\) セクションを示すスクリーンショット。](media/google-federation/google-create-oauth-client-id.png)

12. **［作成］** を選択します クライアント ID とクライアント シークレットをコピーします。 これらは、Azure portal で ID プロバイダーを追加するときに使用します。

    ![OAuth クライアント ID とクライアント シークレットを示すスクリーンショット。](media/google-federation/google-auth-client-id-secret.png)

13. **テスト** の発行ステータスでプロジェクトを終了し、OAuth 同意画面にテスト ユーザーを追加することができます。 または、OAuth の同意画面 で **[アプリの発行]** ボタンを選択して、Google アカウントを持つ任意のユーザーがアプリを使用できます。

## <a name="step-2-configure-google-federation-in-azure-ad"></a>手順 2:Azure AD で Google フェデレーションを構成する 

次に、Google クライアント ID とクライアント シークレットを設定します。 Azure portal または PowerShell を使用して行うことができます。 自分自身を招待することで、Google フェデレーションの構成をテストしてください。 Gmail アドレスを使用し、招待を自分の招待された Google アカウントと引き換えてみます。 

**Azure portal で Google フェデレーションを構成するには** 
1. [Azure ポータル](https://portal.azure.com)にアクセスします。 左側のペインで、 **[Azure Active Directory]** を選択します。 
2. **[外部 ID]** を選択します。
3. **[すべての ID プロバイダー]** を選択し、 **[Google]** ボタンを選択します。
4. 前に取得したクライアント ID とクライアント シークレットを入力します。 **[保存]** を選択します。 

   ![Google ID プロバイダーの追加ページを示すスクリーンショット。](media/google-federation/google-identity-provider.png)

**PowerShell を使用して Google フェデレーションを構成するには**
1. 最新バージョンの Azure AD PowerShell for Graph モジュールをインストールします ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。
2. 次のコマンドを実行します。`Connect-AzureAD`
3. サインイン プロンプトで、マネージド グローバル管理者アカウントを使用してサインインします。  
4. 次のコマンドを実行します。 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId <client ID> -ClientSecret <client secret>`
 
   > [!NOTE]
   > クライアント ID とクライアント シークレットは、「手順 1: Google 開発者プロジェクトを構成する」で作成したアプリのものを使用します。 詳細については、「[New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview&preserve-view=true)」を参照してください。 

## <a name="how-do-i-remove-google-federation"></a>Google フェデレーションを削除する方法

Google フェデレーション セットアップは削除できます。 そのようにした場合、既に招待を引き換え済みの Google ゲスト ユーザーは、サインインできなくなります。 ただし、[ユーザーの引き換え状態をリセットする](reset-redemption-status.md)ことで、リソースへのアクセスを再度許可することができます。
 
**Azure AD ポータルで Google フェデレーションを削除するには**
1. [Azure ポータル](https://portal.azure.com)にアクセスします。 左側のペインで、 **[Azure Active Directory]** を選択します。 
2. **[外部 ID]** を選択します。
3. **[すべての ID プロバイダー]** を選択します。
4. **[Google]** の行で、省略記号ボタン **[...]** を選択して、 **[削除]** を選択します。 
   
   ![ソーシャル ID プロバイダーの [削除] ボタンを示すスクリーンショット。](media/google-federation/google-social-identity-providers.png)

1. **[はい]** を選択して、削除を確認します。 

**PowerShell を使用して Google フェデレーションを削除するには** 
1. 最新バージョンの Azure AD PowerShell for Graph モジュールをインストールします ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。
2. `Connect-AzureAD` を実行します。  
4. サインイン プロンプトで、マネージド グローバル管理者アカウントを使用してサインインします。  
5. 次のコマンドを入力します。

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > 詳細については、「[Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview&preserve-view=true)」を参照してください。