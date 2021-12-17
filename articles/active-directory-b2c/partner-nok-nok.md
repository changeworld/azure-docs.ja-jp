---
title: Azure Active Directory B2C と Nok Nok を構成するチュートリアル
titleSuffix: Azure AD B2C
description: パスワードレス FIDO2 認証を有効にするために Azure Active Directory B2C で Nok Nok を構成するチュートリアル
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 26842d40034527e5b7b785cfc94e4f7a37e36e04
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587216"
---
# <a name="tutorial-configure-nok-nok-with-azure-active-directory-b2c-to-enable-passwordless-fido2-authentication"></a>チュートリアル: パスワードレス FIDO2 認証を有効にするために Azure Active Directory B2C で Nok Nok を構成する

このサンプル チュートリアルでは、Nok Nok S3 認証スイートを Azure Active Directory (AD) B2C テナントに統合する方法について説明します。 [Nok Nok](https://noknok.com/) を使用すると、モバイルおよび Web アプリケーション用の FIDO 認定の多要素認証 (FIDO UAF、FIDO U2F、WebAuthn、FIDO2 など) が有効になります。 Nok Nok を使用しているお客様は、ユーザー エクスペリエンスのバランスを取りながら、セキュリティ態勢を改善できます。

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

- Azure サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。

- お使いの Azure サブスクリプションにリンクされている [Azure AD B2C テナント](tutorial-create-tenant.md)。

- 無料の Nok Nok [試用版テナント](https://noknok.com/products/strong-authentication-service/)を取得します。

## <a name="scenario-description"></a>シナリオの説明

ユーザーに対してパスワードレス FIDO 認証を有効にするため、Azure AD B2C テナントの ID プロバイダーとして Nok Nok を有効にします。 Nok Nok の統合には、次のコンポーネントが含まれています。

- **Azure AD B2C** – ユーザーの資格情報の検証を担当する承認サーバー。

- **Web およびモバイル アプリケーション** – Nok Nok と Azure AD B2C を使用して保護することを選択したモバイルまたは Web アプリケーション。

- **Nok Nok アプリ SDK または Nok Nok Passport アプリ** – Azure AD B2C が有効なアプリケーションの認証に使用されるアプリケーション。 これらのアプリケーションは、[Apple App Store](https://apps.apple.com/us/app/nok-nok-passport/id1050437340) および [Google Play ストア](https://play.google.com/store/apps/details?id=com.noknok.android.passport2&hl=en&gl=US)で入手できます。

次のアーキテクチャの図に、この実装を示します。 Nok Nok は、パスワードレス認証を有効にするために Open ID Connect (OIDC) を使用する Azure AD B2C 用の ID プロバイダーとして機能しています。

![Nok Nok と Azure AD B2C のアーキテクチャを示す図](./media/partner-nok-nok/nok-nok-architecture-diagram.png)

| 手順 | 説明 |
|:------|:-----------|
| 1. | ユーザーがログイン ページにアクセスします。 ユーザーは、サインインまたはサインアップを選択して、ユーザー名を入力します |
| 2. | Azure AD B2C により、ユーザーは Nok Nok OIDC 認証プロバイダーにリダイレクトされます。 |
| 3a. | モバイル ベースの認証の場合は、Nok Nok によって、QR コードが表示されるか、プッシュ通知要求がエンド ユーザーのモバイル デバイスに送信されます。 |
| 3b. | デスクトップおよび PC ベースのログインの場合は、Nok Nok によってエンド ユーザーは Web アプリケーションのログイン ページにリダイレクトされ、パスワードレス認証プロンプトが開始されます。 |
|4a. | ユーザーは、Nok Nok アプリ SDK または Nok Nok Passport アプリを使用して、スマートフォンに表示された QR コードをスキャンします。|
| 4b. | ユーザーは、Web アプリケーションのログイン ページでユーザー名を入力し、[次へ] を選択します。 |
| 5a. | ユーザーは、スマートフォンでの認証を求められます。 <BR> ユーザーは、生体認証、デバイス PIN、ローミング認証システムなど、好みの方法を使用してパスワードレス認証を行います。|
| 5b. | ユーザーは、Web アプリケーションでの認証を求められます。 <BR> ユーザーは、生体認証、デバイス PIN、ローミング認証システムなど、好みの方法を使用してパスワードレス認証を行います。 |
| 6. | Nok Nok サーバーによって、FIDO アサーションが検証され、検証が済むと Azure AD B2C に OIDC 認証応答が送信されます。|
| 7. | 応答に基づき、ユーザーはアクセスを許可または拒否されます。 |

## <a name="onboard-with-nok-nok"></a>Nok Nok を使用したオンボード

[Nok Nok クラウド フォーム](https://noknok.com/contact/)に入力して、独自の Nok Nok テナントを作成します。 フォームを送信すると、テナントへのアクセス方法を説明するメールが届きます。 そのメールには、Nok Nok ガイドへのアクセスも含まれます。 Nok Nok 統合ガイドで示されている手順に従って、Nok Nok クラウド テナントの OIDC 構成を完了します。

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C との統合

### <a name="add-a-new-identity-provider"></a>新しい ID プロバイダーの追加

新しい ID プロバイダーを追加するには、次の手順を実行します。

1. Azure AD B2C テナントのグローバル管理者として **[Azure portal](https://portal.azure.com/#home)** にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[ダッシュボード]**  >  **[Azure Active Directory B2C]**  >   **[ID プロバイダー]** の順に移動します
1. **[Identity Providers]** を選択します。
1. **[追加]** を選択します。

### <a name="configure-an-identity-provider"></a>ID プロバイダーの構成 

ID プロバイダーを構成するには、次の手順のようにします。

1. **[ID プロバイダーの種類]**  >  **[OpenID Connect (Preview)]\(OpenID Connect (プレビュー))** の順に選択します
1. フォームに入力して、ID プロバイダーを設定します。

   |プロパティ | 値 |
   |:-----| :-----------|
   | 名前   | Nok Nok 認証プロバイダー |
   | メタデータ URL | ホストされている Nok Nok Authentication アプリの URI を挿入し、その後に特定のパスを入力します ("https://demo.noknok.com/mytenant/oidc/.well-known/openid-configuration" など) |
   | クライアント シークレット | Nok Nok プラットフォームによって提供されたクライアント シークレットを使用します。|
   | クライアント ID | Nok Nok プラットフォームによって提供されたクライアント ID を使用します。|
   | Scope | OpenID プロファイルの電子メール |
   | 応答の種類 | code |
   | 応答モード | form_post|

1. **[OK]** を選択します。

1. **[Map this identity provider's claims]** を選択します。

1. フォームに入力して、ID プロバイダーをマップします。

   |プロパティ | 値 |
   |:-----| :-----------|
   | UserID    | サブスクリプションから |
   | 表示名 | サブスクリプションから |
   | 応答モード | サブスクリプションから |

1. **[保存]** を選択して、新しい OIDC ID プロバイダーのセットアップを完了します。

### <a name="create-a-user-flow-policy"></a>ユーザー フロー ポリシーの作成

これで、B2C ID プロバイダー内に Nok Nok が新しい OIDC ID プロバイダーとして表示されるはずです。

1. Azure AD B2C テナントの **[ポリシー]** で、 **[ユーザー フロー]** を選択します。

2. **[新規]** のユーザー フローを選択します。

3. **[サインアップとサインイン]** を選択してから、 **[バージョン]** 、 **[作成]** の順に選択します。

4. ポリシーの **[名前]** を入力します。

5. [ID プロバイダー] セクションで、新しく作成した Nok Nok ID プロバイダーを選択します。

6. ユーザー フローのパラメーターを設定します。 名前を挿入し、作成した ID プロバイダーを選択します。 また、メール アドレスを追加することもできます。 この場合、Azure によってログイン プロシージャが直接 Nok Nok にリダイレクトされる代わりに、使用するオプションをユーザーが選択できる画面が表示されます。

7. **[多要素認証]** フィールドはそのままにしておきます。

8. **[Enforce conditional access policies]\(条件付きアクセス ポリシーを強制\)** を選択します

9. **[User attributes and token claims]\(ユーザー属性とトークン要求\)** で、[属性の収集] オプションの **[メール アドレス]** を選択します。 Azure AD B2C からクライアント アプリケーションに返すことのできるクレームと一緒に、Azure AD で収集できるユーザーについてのすべての属性を追加できます。

10. **［作成］** を選択します

11. 正常に作成できたら、新しい **[ユーザー フロー]** を選択します。

12. 左側のパネルで、 **[アプリケーション クレーム]** を選択します。 [オプション] で、 **[メール]** チェックボックスをオンにし、 **[保存]** を選択します。

## <a name="test-the-user-flow"></a>ユーザー フローをテストする

1. Azure AD B2C テナントを開き、[ポリシー] の下にある [Identity Experience Framework] を選択します。

2. 以前に作成した SignUpSignIn を選択します。

3. [ユーザー フローを実行します] を選択し、設定を選択します。

   a. [アプリケーション] : 登録済みのアプリを選択します (サンプルは JWT)

   b. [応答 URL] : リダイレクト URL を選択します

   c. [ユーザー フローを実行します] を選択します。

4. サインアップ フローを実行し、アカウントを作成します

5. ユーザー属性が作成された後、フローの間に、Nok Nok が呼び出されます。 フローが不完全な場合は、ユーザーがディレクトリに保存されていないことを確認してください。

## <a name="next-steps"></a>次のステップ

追加情報については、次の記事を参照してください。

- [Azure AD B2C のカスタム ポリシー](./custom-policy-overview.md)

- [Azure AD B2C のカスタム ポリシーの概要](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
