---
title: "Azure Active Directory B2C: カスタム ポリシーの概要 | Microsoft Docs"
description: "Azure Active Directory B2C のカスタム ポリシーの使用を開始する方法"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: joroja;parahk;gsacavdm
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 42824fe10e635257681f62ab1fec9b47abd4294a
ms.contentlocale: ja-jp
ms.lasthandoff: 05/12/2017

---
# <a name="azure-active-directory-b2c-getting-started-with-custom-policies"></a>Azure Active Directory B2C: カスタム ポリシーの概要

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事の手順を完了すると、電子メール アドレスとパスワードを使用した "ローカル アカウント" サインアップまたはサインインがカスタム ポリシーでサポートされるようになります。 また、追加の ID プロバイダー (Facebook や Azure AD など) を追加するために環境を準備します。  Azure AD B2C の Identity Experience Framework のその他すべての使用法と、多くの基本的な概念についての説明に進む前に、これらの手順を完了しておくことをお勧めします。

## <a name="prerequisites"></a>前提条件

次に進む前に、Azure AD B2C テナントがあることを確認します。 Azure AD B2C テナントは、すべてのユーザー、アプリ、ポリシーなどのコンテナーです。 まだ存在しない場合、[作成する](active-directory-b2c-get-started.md)必要があります。 先に進む前に、Azure AD B2C 組み込みポリシーのチュートリアルを完了し、組み込みポリシーを使用してアプリケーションを構成することを、すべての開発者に強くお勧めします。  アプリケーションは、カスタム ポリシーを呼び出すようにポリシー名をわずかに変更するだけで、どちらの種類のポリシーでも動作します。

カスタム ポリシーの編集にアクセスするには、テナントにリンクされている有効な Azure サブスクリプションが必要です。

## <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>カスタム ポリシーで使用する署名キーと暗号化キーを B2C テナントに追加する

1. Azure AD B2C テナント設定の **[Identity Experience Framework]** ブレードに移動します。
2. **[ポリシー キー]** を選択して、テナント内で利用できるキーを表示します。
3. `B2C_1A_TokenSigningKeyContainer` が存在しない場合は作成します。
 * **[追加]** をクリックします。
 * [オプション]: `Generate`
 * [名前]: `TokenSigningKeyContainer` (B2C_1A_ というプレフィックスが自動的に追加される場合があります)
 * [キーの種類]: `RSA`
 * [日付]: 既定値を使用
 * [キー使用法]: `Signature`
 * **[作成]**
4. `B2C_1A_TokenEncryptionKeyContainer` が存在しない場合は作成します。
 * **[追加]** をクリックします。
 * [オプション]: `Generate`
 * [名前]: `TokenEncryptionKeyContainer` (B2C_1A_ というプレフィックスが自動的に追加される場合があります)
 * [キーの種類]: `RSA`
 * [日付]: 既定値を使用
 * [キー使用法]: `Encryption`
 * **[作成]**
5. `B2C_1A_FacebookSecret` を作成します。 Facebook アプリケーション シークレットが既にある場合は、それをポリシー キーとしてテナントに追加します。  ない場合は、ポリシーが検証にパスするように、プレースホルダー値を含むキーを作成する必要があります。
 * **[追加]** をクリックします。
 * [オプション]: `Manual`
 * [名前]: `FacebookSecret` (B2C_1A_ というプレフィックスが自動的に追加される場合があります)
 * [シークレット]: developers.facebook.com の FacebookSecret を入力するか、プレースホルダーとして「0」を入力します。 "*Facebook App ID ではない*" ので注意してください。
 * [キー使用法]: 署名
 * **[作成]** をクリックし、作成を確認します。

## <a name="register-identity-experience-framework-applications"></a>Identity Experience Framework アプリケーションを登録する

Azure AD B2C では、ユーザーのサインアップとサインインのためにエンジンが使用する 2 つの追加アプリケーションを登録する必要があります。

>[!NOTE]
>ローカル アカウントを使用したサインインを有効にする 2 つのアプリケーションを作成する必要があります。これは、IdentityExperienceFramework (Web アプリ) と、IdentityExperienceFramework アプリからアクセス許可を委任された ProxyIdentityExperienceFramework (ネイティブ アプリ) です。 ローカル アカウントは、テナント内にのみ存在します。 エンド ユーザーは、電子メールとパスワードの一意の組み合わせを使用してサインアップし、テナントに登録されたアプリケーションにアクセスします。

### <a name="create-the-identityexperienceframework-application"></a>IdentityExperienceFramework アプリケーションを作成する

1. [Azure Portal](https://portal.azure.com) で、[Azure AD B2C テナントのコンテキストに切り替えます](active-directory-b2c-navigate-to-b2c-context.md)。
1. `Azure Active Directory` ブレードを開きます (Azure AD B2C ブレードではありません)。 **[> More Services]**(> その他のサービス) をクリックしてブレードを探すことが必要な場合があります。
1. **[アプリの登録]** を選択します。
1. **[+ New application registration]**(+ 新規アプリケーション登録) をクリックします。
   * [Name](名前): `IdentityExperienceFramework`
   * [Application type](アプリケーションの種類): `Web app/API`
   * [Sign-on URL]\(サインオン URL\): `https://login.microsoftonline.com/yourtenant.onmicrosoft.com` (`yourtenant` は Azure AD B2C テナントのドメイン名)
1. **[作成]**をクリックします。
1. 作成されたら、新しく作成されたアプリケーション `IdentityExperienceFramework` を選択して、**[プロパティ]** をクリックし、アプリケーション ID をコピーして後で使用するために保存します。

### <a name="create-the-proxy-identity-experience-framework-application"></a>ProxyIdentityExperienceFramework アプリケーションを作成する

1. **[アプリの登録]** を選択します。
1. **[+ New application registration]**(+ 新規アプリケーション登録) をクリックします。
   * [Name](名前): `ProxyIdentityExperienceFramework`
   * [Application type](アプリケーションの種類): `Native`
   * [Sign-on URL](サインオン URL): `https://login.microsoftonline.com/yourtenant.onmicrosoft.com` (`yourtenant` は Azure AD B2C テナント)
1. **[作成]**をクリックします。
1. 作成されたら、アプリケーション `ProxyIdentityExperienceFramework` を選択して、**[プロパティ]** をクリックし、アプリケーション ID をコピーして後で使用するために保存します。
1. **[Required permissions]**(必要なアクセス許可) を選択し、**[+ Add]**(+ 追加) を選択し、**API を選択**します。
1. 名前 `IdentityExperienceFramework` を検索し、結果で IdentityExperienceFramework を選択して、**[選択]** をクリックします。
1. `Access IdentityExperienceFramework` の横のチェック ボックスをオンにして **[Select]**(選択) をクリックします。
1. **[Done]**をクリックします。
1. **[アクセス許可の付与]** をクリックし、確認のために **[はい]** をクリックします。

## <a name="download-starter-pack-and-modify-policies"></a>スターター パックをダウンロードしてポリシーを変更する

カスタム ポリシーは、Azure AD B2C テナントにアップロードする必要のある XML ファイルのセットです。 操作を簡単にするために、スターター パックが用意されています。 以下の各スターター パックには、説明どおりにシナリオを実現するために必要な最小限の技術プロファイルとユーザー体験が含まれています。
 * LocalAccounts - ローカル アカウントのみの使用を可能にします
 * SocialAccounts - ソーシャル (フェデレーション) アカウントのみの使用を可能にします
 * **SocialAndLocalAccounts** - このチュートリアルでは、これを使用します
 * SocialAndLocalAccountsWithMFA - これにはソーシャル、ローカル、および MFA オプションが含まれています

各スターター パックには、以下のものが含まれています。

* ポリシーの[ベース ファイル](active-directory-b2c-overview-custom.md#policy-files)。 ベースにはいくつかの変更が必要です。
* ポリシーの[拡張ファイル](active-directory-b2c-overview-custom.md#policy-files)。  このファイルは、ほとんどの構成変更が行われる場所です。
* [証明書利用者ファイル](active-directory-b2c-overview-custom.md#policy-files)。これらはタスク固有のファイルであり、アプリケーションによって呼び出されます。

>[!NOTE]
>XML エディターが検証をサポートする場合、スターター パックのルート フォルダーにある `TrustFrameworkPolicy_0.3.0.0.xsd` XML スキーマ ファイルを使用してファイルを検証できます。 XML スキーマ検証では、アップロードする前にエラーを識別します。

次の手順で作業を開始します:

1. GitHub から "active-directory-b2c-custom-policy-starterpack" をダウンロードします。  [zip をダウンロード](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)するか、次を実行します。

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```
2. `SocialAndLocalAccounts` フォルダーを開きます。  このフォルダー内のベース ファイル (`TrustFrameworkBase.xml`) には、ローカル アカウントとソーシャル/コーポレート アカウントの両方に必要な内容が含まれています。 ソーシャル関連の内容は、ローカル アカウントを使い始めるための手順には影響しません。
3. `TrustFrameworkBase.xml`を開きます。  XML エディターが必要な場合は、軽量のクロスプラットフォーム エディターである [Visual Studio Code](https://code.visualstudio.com/download) をお試しください。
4. ルートの `TrustFrameworkPolicy` 要素で、`TenantId` および `PublicPolicyUri` 属性を更新し、`yourtenant.onmicrosoft.com` を Azure AD B2C テナントのドメイン名に置き換えます。

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="yourtenant.onmicrosoft.com"
    PolicyId="B2C_1A_TrustFrameworkBase"
    PublicPolicyUri="http://yourtenant.onmicrosoft.com">
    ```
>[!NOTE]
>`PolicyId` は、ポータルに表示されるポリシー名であり、他のポリシー ファイルがこのポリシー ファイルを参照するときに使用する名前です。

5. ファイルを保存します。
6. `TrustFrameworkExtensions.xml` を開き、`yourtenant.onmicrosoft.com` を Azure AD B2C テナントに置き換えることによって同じ 2 つの変更を行います。 同じ置換を要素 `<TenantId>` で行い、変更は合計で 3 つになります。  ファイルを保存します。
7. `SignUpOrSignIn.xml` を開き、`yourtenant.onmicrosoft.com` を Azure AD B2C テナントに置き換えることによって 3 か所で同じ変更を行います。 ファイルを保存します。
8. パスワード リセット ファイルとプロファイル編集ファイルを開き、`yourtenant.onmicrosoft.com` を Azure AD B2C テナントに置き換えることで 3 か所で同じ変更を行います。 ファイルを保存します。

### <a name="add-the-application-ids-to-your-custom-policy"></a>アプリケーション ID をカスタム ポリシーに追加する
アプリケーション ID を拡張ファイル (`TrustFrameworkExtensions.xml`) に追加します。

1. 拡張ファイル (`TrustFrameworkExtensions.xml`) で、要素 `<TechnicalProfile Id="login-NonInteractive">` を探します。
2. `IdentityExperienceFrameworkAppId` の両方のインスタンスを、前に作成した Identity Experience Framework アプリケーションのアプリケーション ID に置き換えます。 たとえば次のようになります。

```xml
<Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
```

3. `ProxyIdentityExperienceFrameworkAppId` の両方のインスタンスを、前に作成した Proxy Identity Experience Framework アプリケーションのアプリケーション ID に置き換えます。
4. 拡張ファイルを保存します。

## <a name="upload-the-policies-to-your-tenant"></a>ポリシーをテナントにアップロードする

1. [Azure Portal](https://portal.azure.com) で、[Azure AD B2C テナントのコンテキストに切り替え](active-directory-b2c-navigate-to-b2c-context.md)、[Azure AD B2C] ブレードを開きます。
2. **[Identity Experience Framework]** をクリックします。
3. **[ポリシーのアップロード]** を選択し、ポリシー ファイルをアップロードします。

    >[!WARNING]
    >カスタム ポリシー ファイルは、次の順序でアップロードする必要があります:

1. `TrustFrameworkBase.xml` をアップロードします。
2. `TrustFrameworkExtensions.xml` をアップロードします。
3. `SignUpOrSignin.xml` をアップロードします。
4. 他のポリシー ファイルをアップロードします。

ファイルがアップロードされると、ポリシー ファイルの名前の前に `B2C_1A_` が付きます。

## <a name="test-the-custom-policy-using-run-now"></a>"Run Now" を使用してカスタム ポリシーをテストする

1. **[Azure AD B2C の設定]** を開き、**[Identity Experience Framework]** に移動します。

>[!NOTE]
>**[今すぐ実行]** を使用するには、テナントに少なくとも 1 つのアプリケーションが事前登録されている必要があります。 組み込みポリシーとカスタム ポリシーの両方を呼び出すには、B2C または Identity Experience Framework で **[アプリケーション]** メニューを選択して、アプリケーションを B2C テナントに登録する必要があります。 アプリケーションごとに 1 回登録するだけです。 

アプリケーションを Azure AD B2C に登録する方法については、[概要](active-directory-b2c-get-started.md)に関する記事または[アプリケーションの登録](active-directory-b2c-app-registration.md)に関する記事を参照してください。  

2. `B2C_1A_signup_signin` をアップロードした証明書利用者 (RP) カスタム ポリシーを開き、**[今すぐ実行]** ボタンをクリックします。


3. メール アドレスを使用してサインアップできることを確認します。
4. 同じアカウントでサインインし、構成が正しく行われていることを確認します。

>[!NOTE]
>サインインが失敗する一般的な原因は、IdentityExperienceFramework アプリが正しく構成されていないことです。


## <a name="next-steps"></a>次のステップ

### <a name="add-facebook-as-an-identity-provider"></a>Facebook を ID プロバイダーとして追加する
Facebook をセットアップするには:
1. [developers.facebook.com で Facebook アプリケーションを構成します](active-directory-b2c-setup-fb-app.md)
2. [Facebook アプリケーション シークレットを Azure AD B2C テナントに追加します](#add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies)
3. Facebook アプリケーション ID を TrustFrameworkExtensions ポリシー ファイルの `Item Key="client_id"` に追加します

```xml
<TechnicalProfile Id="Facebook-OAUTH">
  <Metadata>
  <!--Replace the value of client_id in this technical profile with your the Facebook App ID"-->
    <Item Key="client_id">00000000000000</Item>
```
4. TrustFrameworkExtensions.xml ポリシー ファイルをテナントにアップロードします。
5. **[今すぐ実行]** を使用してテストするか、登録済みアプリケーションからポリシーを直接呼び出します。

### <a name="add-azure-active-directory-as-an-identity-provider"></a>Azure Active Directory を ID プロバイダーとして追加する
このファースト ステップ ガイドで使用したベース ファイルには、他の ID プロバイダーを追加するために必要な内容の一部が既に含まれています。 Azure AD アカウントを使用したログインを設定するには、[こちらの手順に進みます](active-directory-b2c-setup-aad-custom.md)。


## <a name="reference"></a>リファレンス

Identity Experience Framework を使用する Azure AD B2C のカスタム ポリシーの[概要](active-directory-b2c-overview-custom.md)

