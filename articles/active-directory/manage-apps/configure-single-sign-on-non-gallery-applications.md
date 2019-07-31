---
title: シングル サインオン - 非ギャラリー アプリケーション - Microsoft ID プラットフォーム | Microsoft Docs
description: Microsoft ID プラットフォーム (Azure AD) の非ギャラリー アプリケーションへのシングル サインオン (SSO) を構成する
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 05/08/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: a72cb7bc7feeba984d568a0465d4f23a494496e8
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807651"
---
# <a name="configure-single-sign-on-to-non-gallery-applications-in-microsoft-identity-platform"></a>Microsoft ID プラットフォームの非ギャラリー アプリケーションへのシングル サインオンを構成する

この記事では、管理者が *コードを記述せずに* Microsoft ID プラットフォーム アプリ ギャラリーに存在しないアプリケーションへのシングル サインオンを構成できるようにする機能について説明します。

コードを使用してカスタム アプリケーションと Azure AD を統合する方法に関する開発者向けガイダンスをお探しの場合は、「 [Azure AD の認証シナリオ](../develop/authentication-scenarios.md)」を参照してください。

[この記事](what-is-single-sign-on.md)で説明されているように、Microsoft ID プラットフォーム アプリケーション ギャラリーには、Microsoft ID プラットフォームによるシングル サインオンの形式をサポートすることがわかっているアプリケーションの一覧が表示されます。 (組織内の IT スペシャリストまたはシステム インテグレーターとして) 接続するアプリケーションを見つけたら、Azure Portal に示される詳細な手順に従って、シングル サインオンを有効にできます。

使用許諾契約書に従って以下の機能を使用することもできます。 詳細については、 [価格に関するページ](https://azure.microsoft.com/pricing/details/active-directory/)を参照してください。

- ユーザーの認証と [Microsoft Graph](https://graph.microsoft.com) のトークンの取得に [OpenId Connect/OAuth](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols) などの最新のプロトコルを使用するアプリケーションのセルフサービス統合。
- [Security Assertion Markup Language (SAML) 2.0](https://wikipedia.org/wiki/SAML_2.0) ID プロバイダーをサポートする任意のアプリケーションのセルフサービス統合 (SP または IdP によって開始)
- [パスワードベースの SSO](what-is-single-sign-on.md#password-based-sso)
- [ユーザー プロビジョニング用の System for Cross-Domain Identity Management (SCIM) プロトコル](use-scim-to-provision-users-and-groups.md)を使用するアプリケーションのセルフサービス接続
- [Office 365 アプリ ランチャー](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/)または [Azure AD アクセス パネル](what-is-single-sign-on.md#linked-sign-on)での任意のアプリケーションへのリンクの追加機能

これらの機能は、まだ誰も Azure AD アプリケーション ギャラリーにアプリケーションをオンボードしていない場合でも、使用するサービスとしてのソフトウェア (SaaS) アプリケーションのセルフサービス統合を含めることができます。 もう 1 つの機能は、クラウドまたはオンプレミスのいずれかで、制御しているサーバーに組織によってデプロイされているサード パーティの Web アプリケーションのセルフサービス統合です。

これらの機能は、*アプリ統合テンプレート*とも呼ばれ、SAML、SCIM、またはフォーム ベース認証をサポートするアプリケーションに標準ベースの接続ポイントを提供しています。 機能には、さまざまなアプリケーションとの互換性のために柔軟なオプションや設定が含まれます。

## <a name="adding-an-unlisted-application"></a>一覧にないアプリケーションの追加

Microsoft ID プラットフォームには、アプリケーションを登録する 2 つのメカニズムが用意されています。

[OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) などの最新のプロトコルを使用してユーザーを認証するアプリケーションは、[アプリケーション登録ポータル](../develop/quickstart-register-app.md)を使用して登録されます。

[SAML](../develop/single-sign-on-saml-protocol.md) プロトコルなど、その他のすべての種類の[サポートされる認証メカニズム](what-is-single-sign-on.md)を使用してアプリケーションを登録するには、 **[エンタープライズ アプリケーション]** ブレードを使用して、アプリケーションを Microsoft ID プラットフォームに接続します。

アプリ統合テンプレートを使用して、一覧にないアプリケーションを接続するには、次の手順を実行します。

1. お使いの Microsoft ID プラットフォーム管理者アカウントを使用して、[Azure Active Directory ポータル](https://aad.portal.azure.com/)にサインインします。
1. **[エンタープライズ アプリケーション]**  >  **[新しいアプリケーション]** の順に選択します。
1. (省略可能だが推奨) **[ギャラリーから追加する]** 検索ボックスに、アプリケーションの表示名を入力します。 検索結果にアプリケーションが表示されたら、それを選択し、この手順の残りをスキップします。
1. **[ギャラリー以外のアプリケーション]** を選択します。 **[独自のアプリケーションの追加]** ページが表示されます。

   ![[独自のアプリケーションの追加] ページを表示します](./media/configure-single-sign-on-non-gallery-applications/add-your-own-application.png)

1. 新しいアプリケーションの表示名を入力します。
1. **[追加]** を選択します。

この方法でアプリケーションを追加することにより、事前に統合されたアプリケーションに似たエクスペリエンスを提供します。 まず、アプリケーションのサイドバーから、 **[シングル サインオン]** を選択します。 次の画面 ( **[シングル サインオン方式の選択]** ) は、SSO を構成するためのオプションを示しています。

- **SAML**
- **パスワード ベース**
- **リンク**

![[シングル サインオン方式の選択] ページを表示します](./media/configure-single-sign-on-non-gallery-applications/select-a-single-sign-on-method.png)

これらのオプションの詳細については、この記事の次のセクションを参照してください。

## <a name="saml-based-single-sign-on"></a>SAML ベースのシングル サインオン

アプリケーションの SAML ベースの認証を構成するには、 **[SAML]** オプションを選択します。 (このオプションでは、アプリケーションが SAML 2.0 をサポートしている必要があります)。 **[SAML によるシングル サインオンのセットアップ]** ページが表示されます。

![[SAML によるシングル サインオンのセットアップ] ページを表示します](./media/configure-single-sign-on-non-gallery-applications/set-up-single-sign-on-with-saml.png)

このページには、5 つの異なる見出しがあります。

| 見出し番号 | 見出し名 | この見出しの概要の参照先: |
| --- | --- | --- |
| 1 | **基本的な SAML 構成** | [基本的な SAML 構成を入力する](#enter-basic-saml-configuration) |
| 2 | **ユーザー属性とクレーム** | [SAML トークンで発行された要求を確認またはカスタマイズする](#review-or-customize-the-claims-issued-in-the-saml-token) |
| 3 | **SAML 署名証明書** | [Re証明書の有効期限データ、状態、および電子メール通知を確認する](#review-certificate-expiration-data-status-and-email-notification) |
| 4 | **\<アプリケーション名> のセットアップ** | [ターゲット アプリケーションを設定する](#set-up-target-application) |
| 5 | **\<アプリケーション名> によるシングル サインオンのテスト** | [SAML アプリケーションをテストする](#test-the-saml-application) |

先に進む前に、アプリケーションの SAML 機能の使用方法に関する情報を収集します。 アプリケーションと Azure AD 間の SSO を構成するには、以下のセクションを完了します。

### <a name="enter-basic-saml-configuration"></a>基本的な SAML 構成を入力する

Azure AD をセットアップするには、 **[基本的な SAML 構成]** の見出しに移動し、その **[編集]** アイコン (鉛筆の形) を選択します。 値を手動で入力するか、メタデータ ファイルをアップロードしてフィールドの値を抽出できます。

![[基本的な SAML 構成] ページを表示します](./media/configure-single-sign-on-non-gallery-applications/basic-saml-configuration.png)

次の 2 つのフィールドが必要です。

- **識別子**。 この値は、シングル サインオンが構成されるアプリケーションを一意に識別します。 この値は、アプリケーションによって送信された **AuthnRequest** (SAML 要求) の **Issuer** 要素として見つけることができます。 また、この値はアプリケーションによって提供される SAML メタデータ内に **Entity ID** として表示されます。 SAML トークンの **Entity ID** または **Audience** 値の詳細については、アプリケーションの SAML ドキュメントを確認してください。

  次のコードは、アプリケーションが Azure AD に送信した SAML 要求で **Identifier** または **Issuer** がどのように表示されるかを示します。

  ```xml
  <samlp:AuthnRequest
  xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
  ID="id6c1c178c166d486687be4aaf5e482730"
  Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
  xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
  </samlp:AuthnRequest>
  ```

- **応答 URL**。 応答 URL は、アプリケーションが SAML トークンを受け取ることになっている場所です。 この URL は Assertion Consumer Service (ACS) URL とも呼ばれています。 SAML トークンの応答 URL または ACS URL の詳細については、アプリケーションの SAML ドキュメントを確認してください。

  複数の応答 URL を構成するには、次の PowerShell スクリプトを使用できます。

  ```powershell
  $sp = Get-AzureADServicePrincipal -SearchString "<Exact app name>"
  $app = Get-AzureADApplication -SearchString "<Exact app name>"
  $urllist = New-Object "System.Collections.Generic.List[String]"
  $urllist.Add("<reply URL 1>")
  $urllist.Add("<reply URL 2>")
  $urllist.Add("<reply URL 3>")
  Set-AzureADApplication -ObjectId $app.ObjectId -ReplyUrls $urllist
  Set-AzureADServicePrincipal -ObjectId $sp.ObjectId -ReplyUrls $urllist
  ```

次の 3 つのフィールドは省略可能です。

- **Sign On URL (SP-initiated only)** (サインオン URL (SP によって開始される場合のみ))。 この値は、ユーザーがこのアプリケーションにサインインする場所を示します。 アプリケーションが SP によって開始される SSO を実行する場合、ユーザーがこの URL に移動すると、SP はユーザーを認証およびサインインするために、Azure AD にリダイレクトします。 このフィールドを指定した場合、Azure AD はその URL を使用して Office 365 と Azure AD アクセス パネルからアプリケーションを起動します。 このフィールドを省略した場合、Azure AD は代わりに、Office 365、Azure AD アクセス パネル、または Azure AD SSO URL ( **[ダッシュ ボード]** ページからコピー可能) からのアプリケーションの起動中に、IdP によって開始されるサインオンを実行します。

- **リレー状態**。 認証後、ユーザーをリダイレクトする場所をアプリケーションに指示するために、SAML でリレー状態を指定できます。 この値は通常、アプリケーション内の特定の場所にユーザーを移動させる URL または URL パスです。

- **[ログアウト URL]** 。 この値は、SAML ログアウト応答をアプリケーションに返送するために使用します。

詳細については、「[シングル サインオンの SAML プロトコル](../develop/single-sign-on-saml-protocol.md)」を参照してください。

### <a name="review-or-customize-the-claims-issued-in-the-saml-token"></a>SAML トークンで発行された要求を確認またはカスタマイズする

アプリケーションに対するユーザーの認証時に、Azure AD は、ユーザーを一意に識別する情報 (要求) を伴う SAML トークンをアプリケーションに発行します。 既定では、この情報には、ユーザーのユーザー名、電子メール アドレス、名、および姓が含まれます。

アプリケーションに SAML トークンで送信された要求を表示または編集するには、次の手順に従います。

- **[ユーザー属性とクレーム]** 見出しに移動して、 **[編集]** アイコンを選択します。 **[ユーザー属性とクレーム]** ページを開きます。

![[ユーザー属性とクレーム] ページを表示します](./media/configure-single-sign-on-non-gallery-applications/user-attributes-and-claims.png)

次の 2 つの理由で、SAML トークンで発行された要求を編集する必要があります。

- アプリケーションで、別の要求 URI または要求値のセットが必要である。
- アプリケーションで、 **[名前識別子の値]** 要求を、Microsoft ID プラットフォームに格納されたユーザー名 (ユーザー プリンシパル名とも呼ばれます) 以外のものにする必要がある。

詳細については、[方法:エンタープライズ アプリケーションの SAML トークンで発行された要求のカスタマイズ](../develop/active-directory-saml-claims-customization.md)」を参照してください。

### <a name="review-certificate-expiration-data-status-and-email-notification"></a>証明書の有効期限データ、状態、および電子メール通知を確認する

ギャラリーまたはギャラリー以外のアプリケーションを作成すると、Azure AD によって、有効期限が作成日の 3 年後であるアプリケーション固有の証明書が作成されます。 この証明書を使用して、Azure AD とアプリケーション間の信頼を設定する必要があります。 証明書の形式の詳細については、アプリケーションの SAML ドキュメントを参照してください。

Azure AD の **[SAML によるシングル サインオンのセットアップ]** メイン ページから直接、Base64 または未加工形式でアクティブな証明書をダウンロードできます。 または、証明書は、アプリケーションのメタデータ XML ファイルをダウンロードするか、アプリのフェデレーション メタデータ URL を使用して取得できます。

証明書 (アクティブまたは非アクティブ) を表示、作成、またはダウンロードするには、 **[SAML 署名証明書]** 見出しに移動して、 **[編集]** アイコンを選択します。 **[SAML 署名証明書]** が表示されます。

![[SAML 署名証明書] ページを表示します](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)

証明書に以下が含まれていることを確認します。

- *期待どおりの有効期限。* 最大 3 年間の有効期限を構成できます。
- *必要な証明書のアクティブな状態。* 状態が**非アクティブ**の場合は、**アクティブ**に変更します。 状態を変更するには、目的の証明書の行を右クリックし、 **[証明書をアクティブにする]** を選択します。
- *適切な署名オプションおよびアルゴリズム。*
- *適切な通知用メール アドレス。* アクティブな証明書の有効期限が近づくと、Azure AD は、このフィールドに構成されている電子メール アドレスに通知を送信します。  

詳細については、[フェデレーション シングル サインオンの証明書の管理](manage-certificates-for-federated-single-sign-on.md)と[SAML トークンの詳細な証明書署名オプション](certificate-signing-options.md)に関するページを参照してください。

### <a name="set-up-target-application"></a>ターゲット アプリケーションを設定する

SSO 用にアプリケーションを構成するには、アプリケーションのドキュメントを探します。 ドキュメントを検索するには、 **[\<アプリケーション名> のセットアップ]** の見出しに移動し、 **[ステップ バイ ステップの手順を表示]** を選択します。 ドキュメントが **[サインオンの構成]** ページに表示されます。 そのページでは、 **[\<アプリケーション名> のセットアップ]** の見出しで **[ログイン URL]** 、 **[Azure AD 識別子]** 、および **[ログアウト URL]** の値を入力する手順が説明されています。

必要な値は、アプリケーションによって異なります。 詳細については、アプリケーションの SAML ドキュメントを参照してください。 **ログイン URL** と**ログアウト URL** の両方が、同じエンドポイント (Azure AD のインスタンスの SAML 要求処理エンドポイント) に解決されます。 **Azure AD 識別子**は、アプリケーションに発行された SAML トークンでの **[発行者]** の値です。

### <a name="assign-users-and-groups-to-your-saml-application"></a>SAML アプリケーションにユーザーとグループを割り当てる

SAML ベースの ID プロバイダーとして Azure AD を使用するようにアプリケーションを構成し終われば、テストの準備はほぼできています。 セキュリティ コントロールとして、Azure AD は、Azure AD がユーザーにアクセス権を付与している場合、ユーザーのアプリケーションへのサインインを許可するトークンだけを発行します。 ユーザーには、直接、またはグループ メンバーシップを介してアクセス権を付与できます。

アプリケーションに新しいユーザーまたはグループを割り当てるには、次の手順に従います。

1. アプリケーションのサイドバーで、 **[ユーザーとグループ]** を選択します。 **[\<アプリケーション名> - ユーザーとグループ]** ページが表示され、割り当てられたユーザーとグループの現在の一覧が示されます。
1. **[ユーザーの追加]** を選択します。 **[割り当ての追加]** ページが表示されます。
1. **[ユーザーとグループ] (選択した \<数>)** を選択します。 **[ユーザーとグループ]** ページが表示され、使用可能なユーザーとグループの一覧が示されます。
1. 一覧から割り当てるユーザーまたはグループを入力するか、スクロールして見つけます。
1. 追加する各ユーザーまたはグループを選択し、 **[選択]** ボタンを選択します。 **[ユーザーとグループ]** ページは表示されなくなります。
1. **[割り当ての追加]** ページで **[割り当て]** を選択します。 **[\<アプリケーション名> - ユーザーとグループ]** ページが表示され、追加ユーザーが一覧に示されます。

   ![[アプリケーション ユーザーおよびグループ] ページを表示します](./media/configure-single-sign-on-non-gallery-applications/application-users-and-groups.png)

この一覧から、次の操作を実行できます。

- ユーザーを削除する。
- それらのロールを編集する。
- ユーザーが、自身のアクセス パネル内からアプリケーションに対して認証できるように、資格情報 (ユーザー名およびパスワード) を更新します。

一度に複数のユーザーまたはグループを編集または削除できます。

ユーザーを割り当てると、Azure AD はユーザーにトークンを発行できます。 さらに、ユーザーのアクセス パネルに、このアプリケーションのタイルも表示されます。 ユーザーが Office 365 を使用している場合、アプリケーション タイルは、Office 365 アプリケーション起動プログラムにも表示されます。

> [!NOTE]
> アプリケーションの **[構成]** タブにある **[ロゴのアップロード]** ボタンを使用して、アプリケーションのタイル ロゴをアップロードできます。

### <a name="test-the-saml-application"></a>SAML アプリケーションをテストする

SAML アプリケーションをテストする前に、Azure AD でアプリケーションを既に設定し、アプリケーションにユーザーまたはグループを割り当てている必要があります。 SAML アプリケーションをテストするには、 **[シングル サインオン]** を選択します。これにより、 **[SAML ベースのサインオン]** ページに戻ります。 (SSO の別の方法が有効であった場合、 **[シングル サインオン モードの変更]**  >  **[SAML]** も選択します)。次に、 **[\<アプリケーション名> によるシングル サインオン]** の見出しで、 **[テスト]** を選択します。 詳細については、「[Azure Active Directory のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグする](../develop/howto-v1-debug-saml-sso-issues.md)」を参照してください。

## <a name="password-single-sign-on"></a>パスワード シングル サインオン

HTML サインイン ページがある Web アプリケーションの [パスワード ベースのシングル サインオン](what-is-single-sign-on.md) を構成するには、このオプションを選択します。 パスワード ベースの SSO (パスワード保管ともいう) では、ID フェデレーションをサポートしない Web アプリケーションに対するユーザーのアクセスおよびパスワードを管理できます。 これは、複数のユーザーが、たとえば、組織のソーシャル メディア アプリ アカウントなどに、1 つのアカウントを共有する必要があるシナリオにも便利です。

**[パスワード ベース]** を選択した後、アプリケーションの Web ベースのサインイン ページの URL を入力するように求められます。

![[サインオン URL] ページを表示して、サインオン URL を入力します](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

続いて、以下の手順を行います。

1. URL を入力します。 この文字列は、ユーザー名入力フィールドを含んだページである必要があります。
1. **[保存]** を選択します。 Azure AD は、ユーザー名とパスワードを入力するためのサインイン ページを解析しようとします。
1. Azure AD の解析試行が失敗した場合、 **[\<アプリケーション名> パスワード シングル サインオン設定の構成]** を選択し、 **[サインオンの構成]** ページを表示します。 (試行が成功した場合、この手順の残りの部分は無視できます)。
1. **[サインイン フィールドの手動検出]** を選択します。 サインイン フィールドの手動検出について説明した追加手順が表示されます。

   ![パスワードベースのシングル サインオンの手動構成](./media/configure-single-sign-on-non-gallery-applications/password-configure-sign-on.png)

1. **[サインイン フィールドの取り込み]** を選択します。 キャプチャ ステータス ページが新しいタブで開き、「**メタデータ キャプチャは現在進行中です**」というメッセージが示されます。
1. **[Access Panel Extension Required]** (アクセス パネル拡張機能必要) ボックスが新しいタブに表示された場合、 **[今すぐインストール]** を選択して、 **[マイ アプリによるセキュリティで保護されたサインイン拡張機能]** ブラウザー拡張機能をインストールします。 (ブラウザー拡張機能には、Microsoft Edge、Chrome、または Firefox が必要です)。続いて拡張機能をインストールし、起動し、有効にして、キャプチャ ステータス ページを更新します。

   ブラウザー拡張機能は次に、入力した URL を表示する別のタブを開きます。

1. 入力した URL のタブで、サインイン プロセスを実行します。 ユーザー名とパスワードのフィールドを入力し、サインインを試みます。 (正しいパスワードを指定する必要がありません)。

   プロンプトでは、キャプチャされたサインイン フィールドを保存するように要求されます。

1. **[OK]** を選択します。 タブが閉じ、ブラウザー拡張機能が「**メタデータはアプリケーション用に更新されました**」というメッセージでキャプチャ ステータス ページを更新し、そのブラウザー タブも閉じます。
1. Azure AD の **[サインオンの構成]** ページで、 **[正常にアプリにサインインできました]** を選択します。
1. **[OK]** を選択します。

サインイン ページのキャプチャ後に、ユーザーとグループを割り当てることができ、通常の[パスワード SSO アプリケーション](what-is-single-sign-on.md)と同様に資格情報ポリシーを設定できます。

> [!NOTE]
> アプリケーションの **[構成]** タブにある **[ロゴのアップロード]** ボタンを使用して、アプリケーションのタイル ロゴをアップロードできます。

## <a name="existing-single-sign-on"></a>既存のシングル サインオン

組織の Azure AD アクセス パネルまたは Office 365 ポータルにアプリケーションへのリンクを追加するには、このオプションを選択します。 この方法を使用して、認証用に Azure AD の代わりに現在 Active Directory フェデレーション サービス (AD FS) (または他のフェデレーション サービス) を使用しているカスタム Web アプリケーションへのリンクを追加することができます。 または、ユーザーのアクセス パネルに表示するだけの特定の SharePoint ページまたは他の Web ページにディープ リンクを追加することもできます。

**[リンク済み]** を選択した後、リンクするアプリケーションの URL を入力するように求められます。 URL を入力し、 **[保存]** を選択します。 ユーザーとグループをアプリケーションに割り当てることができます。これにより、アプリケーションが [Office 365 アプリ ランチャー](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/)またはユーザーの [Azure AD アクセス パネル](end-user-experiences.md)に表示されるようになります。

> [!NOTE]
> アプリケーションの **[構成]** タブにある **[ロゴのアップロード]** ボタンを使用して、アプリケーションのタイル ロゴをアップロードできます。

## <a name="related-articles"></a>関連記事

- [方法: エンタープライズ アプリケーションの SAML トークンで発行された要求のカスタマイズ](../develop/active-directory-saml-claims-customization.md)」を参照してください。
- [Azure Active Directory のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグする](../develop/howto-v1-debug-saml-sso-issues.md)
- [Microsoft ID プラットフォーム (旧称: 開発者向け Azure Active Directory)](../develop/index.yml)
