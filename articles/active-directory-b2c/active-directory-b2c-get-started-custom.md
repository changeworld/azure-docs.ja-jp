---
title: "Azure Active Directory B2C: カスタム ポリシーの概要 | Microsoft Docs"
description: "Azure Active Directory B2C のカスタム ポリシーの作業を開始する方法に関するトピック"
services: active-directory-b2c
documentationcenter: 
author: gsacavdm
manager: krassk
editor: parakhj
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: gsacavdm
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 93fc922433f0ba60ee438db8d4dcb14527c208fc
ms.contentlocale: ja-jp
ms.lasthandoff: 05/02/2017

---
# <a name="azure-active-directory-b2c-getting-started-with-custom-policies"></a>Azure Active Directory B2C: カスタム ポリシーの概要

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事の手順を完了すると、電子メール アドレスとパスワードを使用した "ローカル アカウント" サインアップまたはサインインがカスタム ポリシーでサポートされるようになります。 また、追加の ID プロバイダー (Facebook や Azure AD など) を追加するために環境を準備します。  Azure AD B2C の Identity Experience Engine のその他すべての使用法と、多くの基本的な概念について説明する前に、以下の手順を完了する必要があります。

## <a name="prerequisites"></a>前提条件

次に進む前に、Azure AD B2C テナントがあることを確認します。 Azure AD B2C テナントは、すべてのユーザー、アプリ、ポリシーなどのコンテナーです。 まだ存在しない場合、[作成する](active-directory-b2c-get-started.md)必要があります。

### <a name="confirming-your-b2c-tenant"></a>B2C テナントの確認

カスタム ポリシーはまだプライベート プレビュー段階のため、カスタム ポリシーのアップロードのために Azure AD B2C テナントが有効になっていることを確認します。

1. [Azure Portal](https://portal.azure.com) で、[Azure AD B2C テナントのコンテキストに切り替え](active-directory-b2c-navigate-to-b2c-context.md)、[Azure AD B2C] ブレードを開きます。
1. **[All Policies]**(すべてのポリシー) をクリックします。
1. **[Upload Policy]**(アップロード ポリシー) が利用可能なことを確認します。  ボタンが無効になっている場合、AADB2CPreview@microsoft.com までメールでお知らせください。

## <a name="set-up-keys-for-your-custom-policy"></a>カスタム ポリシー用のキーを設定する

カスタム ポリシーを使用するには、まずキーを設定する必要があります。

[!INCLUDE [active-directory-b2c-setup-keys-custom.md](../../includes/active-directory-b2c-setup-keys-custom.md)]

## <a name="download-starter-pack-and-modify-policies"></a>スターター パックをダウンロードしてポリシーを変更する

カスタム ポリシーは、Azure AD B2C テナントにアップロードする必要のある XML ファイルのセットです。 作業を開始するために使用できるスターター パックが提供されています。 スターター パックには次のものが含まれます:

* ポリシーの[ベース ファイル](active-directory-b2c-overview-custom.md#policy-files)。 ベースにはいくつかの変更が必要です。
* ポリシーの[拡張ファイル](active-directory-b2c-overview-custom.md#policy-files)。  このファイルは、ほとんどの構成変更が行われる場所です。

次の手順で作業を開始します:

1. GitHub から "Azure AD B2C Custom Policy Starter Pack" をダウンロードします。  [zip をダウンロード](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)するか、次を実行します。

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. `SocialIDPAndLocalAccounts` フォルダーを開きます。  このフォルダー内のベース ファイル (`TrustFrameworkBase.xml`) には、ローカル アカウントとソーシャル/コーポレート アカウントの両方に必要な内容が含まれています。 ソーシャル関連の内容は、ローカル アカウントを使い始めるための手順には影響しません。
1. `TrustFrameworkBase.xml`を開きます。  XML エディターが必要な場合、軽量のクロスプラットフォーム エディターである [Visual Studio Code のダウンロード](https://code.visualstudio.com/download)をお試しください。
1. ルートの `TrustFrameworkPolicy` 要素で、`TenantId` および `PublicPolicyUri` 属性を更新し、`{tenantName}` を Azure AD B2C テナントに置き換えます:

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="{tenantName}.onmicrosoft.com"
    PolicyId="B2C_1A_TrustFrameworkBase"
    PublicPolicyUri="http://{tenantName}.onmicrosoft.com">
    ```

1. ファイルを保存します。
1. `TrustFrameworkExtensions.xml` を開き、`{tenantName}` を Azure AD B2C テナントに置き換えることによって同じ変更を行います。 ファイルを保存します。
1. `SignUpOrSignIn.xml` を開き、`{tenantName}` を Azure AD B2C テナントに置き換えることによって同じ変更を行います。 ファイルを保存します。

>[!NOTE]
>XML エディターが検証をサポートする場合、スターター パックのルート フォルダーにある `TrustFrameworkPolicy_0.3.0.0.xsd` XML スキーマ ファイルを使用してファイルを検証できます。 XML スキーマ検証では、アップロードする前にエラーを識別します。

## <a name="register-policy-engine-applications"></a>ポリシー エンジン アプリケーションを登録する

Azure AD B2C では、ユーザーのサインアップとサインインのためにエンジンが使用する 2 つの追加アプリケーションを登録する必要があります。

>[!NOTE]
>以下の手順では、ローカル アカウントを使用したサインインを有効にする 2 つのアプリケーション、PolicyEngine (Web アプリ) と PolicyEngineProxy (ネイティブ アプリ)を作成し、PolicyEngine からアクセス許可を委任します。 このセクションは、ローカル アカウントの使用が想定される Azure AD B2C テナントのみに必要です。

### <a name="create-the-policy-engine-application"></a>ポリシー エンジン アプリケーションを作成する

1. [Azure Portal](https://portal.azure.com) で、[Azure AD B2C テナントのコンテキストに切り替えます](active-directory-b2c-navigate-to-b2c-context.md)。
1. `Azure Active Directory` ブレードを開きます (Azure AD B2C ブレードではありません)。 **[> More Services]**(> その他のサービス) をクリックしてブレードを探すことが必要な場合があります。
1. **[アプリの登録]** を選択します。
1. **[+ New application registration]**(+ 新規アプリケーション登録) をクリックします。
   * [Name](名前): `PolicyEngine`
   * [Application type](アプリケーションの種類): `Web app/API`
   * [Sign-on URL](サインオン URL): `https://login.microsoftonline.com/{tenantName}.onmicrosoft.com` (`{tenantName}` は Azure AD B2C テナント)
1. **[作成]**をクリックします。
1. 作成されたら、新しく作成されたアプリケーション `PolicyEngine` を選択し、アプリケーション ID をコピーし、後で使用するために保存します。

### <a name="create-the-policy-engine-proxy-application"></a>ポリシー エンジン プロキシ アプリケーションを作成する

1. **[アプリの登録]** を選択します。
1. **[+ New application registration]**(+ 新規アプリケーション登録) をクリックします。
   * [Name](名前): `PolicyEngineProxy`
   * [Application type](アプリケーションの種類): `Native`
   * [Sign-on URL](サインオン URL): `https://login.microsoftonline.com/{tenantName}.onmicrosoft.com` (`{tenantName}` は Azure AD B2C テナント)
1. **[作成]**をクリックします。
1. 作成されたら、アプリケーション `PolicyEngineProxy` を選択し、アプリケーション ID をコピーし、後で使用するために保存します。
1. **[Required permissions]**(必要なアクセス許可) を選択し、**[+ Add]**(+ 追加) を選択し、**API を選択**します。
1. 名前 `PolicyEngine` を検索し、結果から PolicyEngine を選択し、**[Select]**(選択) をクリックします。
1. `Access PolicyEngine` の横のチェック ボックスをオンにして **[Select]**(選択) をクリックします。
1. **[Done]**をクリックします。

### <a name="add-the-application-ids-to-your-custom-policy"></a>アプリケーション ID をカスタム ポリシーに追加する

ローカル アカウントが有効なカスタム ポリシーを作成するには、アプリケーション ID を拡張ファイル (`TrustFrameworkExtensions.xml`) に追加する必要があります。

1. 拡張ファイル (`TrustFrameworkExtensions.xml`) で、要素 `<TechnicalProfile Id="login-NonInteractive">` を探します。
1. `{Policy Engine Proxy Application ID}` の両方のインスタンスを、[作成したポリシー エンジン プロキシ アプリケーション](#create-the-policy-engine-proxy-application)のアプリケーション ID に置き換えます。
1. `{Policy Engine Application ID}` の両方のインスタンスを、[作成したポリシー エンジン アプリケーション](#create-the-policy-engine-application)のアプリケーション ID に置き換えます。
1. 拡張ファイルを保存します。

## <a name="upload-the-policies-to-your-tenant"></a>ポリシーをテナントにアップロードする

1. [Azure Portal](https://portal.azure.com) で、[Azure AD B2C テナントのコンテキストに切り替え](active-directory-b2c-navigate-to-b2c-context.md)、[Azure AD B2C] ブレードを開きます。
1. **[All Policies]**(すべてのポリシー) をクリックします。
1. **[Upload Policy]**(ポリシーのアップロード) を選択します。

    >[!WARNING]
    >カスタム ポリシー ファイルは、次の順序でアップロードする必要があります:

1. `TrustFrameworkBase.xml` をアップロードします。
1. `TrustFrameworkExtensions.xml` をアップロードします。
1. `SignUpOrSignin.xml` をアップロードします。

ファイルがアップロードされると、名前の前に `B2C_1A_` が付きます。  一方、組み込みのポリシーは `B2C_1_` で始まります。

## <a name="test-the-custom-policy-using-run-now"></a>"Run Now" を使用してカスタム ポリシーをテストする

1. **Azure AD B2C ブレード**を開き、**[All polices]**(すべてのポリシー) に移動します。
1. アップロードしたカスタム ポリシーを選択し、**[Run now]**(今すぐ実行) ボタンをクリックします。
1. メール アドレスを使用してサインアップできることを確認します。

## <a name="next-steps"></a>次のステップ

このファースト ステップ ガイドで使用したベース ファイルには、他の ID プロバイダーを追加するために必要な内容の一部が既に含まれています。 Azure AD アカウントを使用したログインを設定するには、[こちらの手順に進みます](active-directory-b2c-setup-aad-custom.md)。

## <a name="reference"></a>リファレンス

* **Technical Profile (TP)** は、エンドポイントの名前、そのメタデータ、そのプロトコルと、Identity Experience Engine が実行するクレーム交換の詳細を定義する要素です。  Local Account SignIn は、ローカル アカウント ログインを実行するために Identity Experience Engine が使用する TechnicalProfile です。

