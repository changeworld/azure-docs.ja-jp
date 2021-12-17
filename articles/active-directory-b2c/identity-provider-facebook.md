---
title: Facebook アカウントでのサインアップおよびサインインを設定する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C を使用するアプリケーションで Facebook アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: d9fb37bd05c6cddf1509d40433783692864e588d
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130137836"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して Facebook アカウントでのサインアップおよびサインインを設定する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-facebook-application"></a>Facebook アプリケーションを作成する

Azure Active Directory B2C (Azure AD B2C) でユーザーが Facebook アカウントを使用してサインインできるようにするには、[Facebook アプリのダッシュボード](https://developers.facebook.com/)でアプリケーションを作成する必要があります。 詳細については、[アプリ開発](https://developers.facebook.com/docs/development)のページを参照してください。

まだ Facebook アカウントを持っていない場合は、[https://www.facebook.com](https://www.facebook.com) でサインアップしてください。 Facebook アカウントにサインアップまたはサインインした後、[Facebook 開発者アカウント登録プロセス](https://developers.facebook.com/async/registration)を開始します。 詳細については、[Facebook 開発者登録](https://developers.facebook.com/docs/development/register)に関するページを参照してください。

1. Facebook 開発者アカウントの資格情報を使用して、[開発者向けの Facebook](https://developers.facebook.com/apps)にサインインします。
1. **[Create app]\(アプリの作成\)** を選択します。
1. **[アプリの種類を選択]** で、**[コンシューマー]** を選択して **[次へ]** を選択します。
1. **[アプリの表示名]** と、有効な **[アプリの連絡先メール アドレス]** を入力します。
1. **[Create app]\(アプリの作成\)** を選択します。 この手順では、Facebook プラットフォームのポリシーを受け入れてオンライン セキュリティ チェックを完了することが必要な場合があります。
1. **[設定]**  >  **[基本]** を選択します。
    1. **[App ID]** の値をコピーします。
    1. **[Show (表示)]** を選択し、 **[App Secret (アプリ シークレット)]** の値をコピーします。 テナントで ID プロバイダーとして Facebook を構成するには、この両方を使用します。 **[App Secret]** は、重要なセキュリティ資格情報です。
    1. **[Privacy Policy URL] (プライバシー ポリシーの URL)** に URL (`https://www.contoso.com/privacy` など) を入力します。 ポリシーの URL は、アプリケーションのプライバシーに関する情報を提供するために維持されるページです。
    1. **[サービス利用規約 URL]** に URL を入力します (例: `https://www.contoso.com/tos`)。 ポリシーの URL は、アプリケーションの利用規約を提供するために維持されるページです。
    1. **[ユーザー データ削除]** の URL を入力します (例: `https://www.contoso.com/delete_my_data`)。 ユーザー データ削除 URL は、自分のデータの削除を要求する手段をユーザーに提供するために維持するページです。 
    1. **カテゴリ** を選択します。たとえば`Business and Pages`。 この値は Facebook では必須ですが、Azure AD B2C では使用されません。
1. ページの下部で、 **[プラットフォームの追加]** 、 **[Web サイト]** の順に選択します。
1. **[サイトの URL]** に、Web サイトのアドレス (たとえば `https://contoso.com`) を入力します。 
1. **[変更の保存]** を選択します。
1. メニューから **[製品]** の横にある **プラス** 記号または **[製品の追加]** リンクを選択します。 **[アプリに製品を追加する]** で、 **[Facebook ログイン]** の下にある **[設定]** を選択します。
1. メニューから、 **[Facebook ログイン]** 、 **[設定]** の順に選択します。
1. **[有効な OAuth リダイレクト URI]** に「`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`」を入力します。 [カスタム ドメイン](custom-domain.md)を使用する場合は、「`https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`」と入力します。 `your-tenant-name` を実際のテナントの名前に、`your-domain-name` を実際のカスタム ドメインに置き換えます。 
1. ページの下部にある **[Save Changes]\(変更の保存\)** を選択します。
1. Facebook アプリケーションを Azure AD B2C で使用できるようにするには、ページの右上にある状態セレクターを選択し、 **[オン]** に設定してアプリケーションを公開し、 **[スイッチ モード]** を選択します。 この時点で、状態は **開発** から **ライブ** に変更されます。 詳細については、[Facebook アプリ開発](https://developers.facebook.com/docs/development/release)に関するページを参照してください。

::: zone pivot="b2c-user-flow"

## <a name="configure-facebook-as-an-identity-provider"></a>Facebook を ID プロバイダーとして構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[ID プロバイダー]** を選択してから、 **[Facebook]** を選択します。
1. **[名前]** を入力します。 たとえば、「*Facebook*」とします。
1. **[クライアント ID]** には、前に作成した Facebook アプリケーションのアプリ ID を入力します。
1. **[クライアント シークレット]** には、記録したアプリ シークレットを入力します。
1. **[保存]** を選択します。

## <a name="add-facebook-identity-provider-to-a-user-flow"></a>ユーザー フローに Facebook ID プロバイダーを追加する 

この時点では、Facebook ID プロバイダーは設定されていますが、サインイン ページではまだ使用できません。 Facebook ID プロバイダーをユーザー フローに追加するには、次の手順を実行します。

1. Azure AD B2C テナントで、 **[ユーザー フロー]** を選択します。
1. Facebook ID プロバイダーを追加するユーザー フローをクリックします。
1. **[ソーシャル ID プロバイダー]** から、 **[Facebook]** を選択します。
1. **[保存]** を選択します。
1. ポリシーをテストするには、 **[ユーザー フローを実行します]** を選択します。
1. **[アプリケーション]** には、以前に登録した *testapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローを実行します]** ボタンを選択します。
1. サインアップまたはサインイン ページで、 **[Facebook]** を選択して、Facebook アカウントでサインインします。

サインイン プロセスが成功すると、ブラウザーは `https://jwt.ms` にリダイレクトされ、Azure AD B2C によって返されたトークンの内容が表示されます。


::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>ポリシー キーを作成する

Azure AD B2C テナントで前に記録したアプリ シークレットを格納する必要があります。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. [概要] ページで、 **[Identity Experience Framework]** を選択します。
1. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
1. **オプション** については、`Manual`を選択します。
1. ポリシー キーの **名前** を入力します。 たとえば、「 `FacebookSecret` 」のように入力します。 プレフィックス `B2C_1A_` がキーの名前に自動的に追加されます。
1. **[シークレット]** に、前に記録したアプリ シークレットを入力します。
1. **[キー使用法]** として [`Signature`] を選択します。
1. **Create** をクリックしてください。

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>ID プロバイダーとして Facebook アカウントを構成する

1. `SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** ファイルで、`client_id` の値を Facebook アプリケーション ID に置き換えます。

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

## <a name="upload-and-test-the-policy"></a>ポリシーをアップロードしてテストします。

作成したユーザー体験を開始する証明書利用者 (RP) ファイルを更新します。

1. *TrustFrameworkExtensions.xml* ファイルをテナントにアップロードします。
1. **[カスタム ポリシー]** ページで、**B2C_1A_signup_signin** を選択します。
1. **[アプリケーションの選択]** には、以前に登録した *testapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[今すぐ実行]** ボタンを選択します。
1. サインアップまたはサインイン ページで、 **[Facebook]** を選択して、Facebook アカウントでサインインします。

サインイン プロセスが成功すると、ブラウザーは `https://jwt.ms` にリダイレクトされ、Azure AD B2C によって返されたトークンの内容が表示されます。

::: zone-end

## <a name="next-steps"></a>次のステップ

[Facebook トークンをアプリケーションに渡す](idp-pass-through-user-flow.md)方法について説明します。
