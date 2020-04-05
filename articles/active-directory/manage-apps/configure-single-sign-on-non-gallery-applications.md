---
title: SAML シングル サインオン - 非ギャラリー アプリケーション - Microsoft ID プラットフォーム | Microsoft Docs
description: Microsoft ID プラットフォーム (Azure AD) の非ギャラリー アプリケーションへのシングル サインオン (SSO) を構成する
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 07/19/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad27ad5e34d9f44fe7d7be80e05e33dd6fb5e7b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79224927"
---
# <a name="configure-saml-based-single-sign-on-to-non-gallery-applications"></a>ギャラリー以外のアプリケーションに SAML ベースのシングル サインオンを構成する

[ギャラリー アプリ](add-gallery-app.md)または[ギャラリー以外の Web アプリ](add-non-gallery-app.md)を Azure AD エンタープライズ アプリケーションに追加するときに利用できるシングル サインオン オプションの 1 つに、[SAML ベースのシングル サインオン](what-is-single-sign-on.md#saml-sso)があります。 SAML プロトコルのいずれかを使用して認証するアプリケーションの場合は常に SAML を選択します。 SAML によるシングル サインオンでは、ユーザーの Azure AD アカウントを使用して、Azure AD がアプリケーションに対して認証を行います。 Azure AD は、接続プロトコルを通してアプリケーションにシングル サインオンの情報を伝達します。 SAML 要求で定義するルールに基づいて、ユーザーを特定のアプリケーション ロールにマップできます。 この記事では、ギャラリー以外のアプリ用に SAML ベースのシングル サインオンを構成する方法について説明します。 

> [!NOTE]
> ギャラリー アプリを追加しますか? 詳細な設定手順は、[SaaS アプリのチュートリアルの一覧](../saas-apps/tutorial-list.md)で探してください。

"コードの記述なしで" ギャラリー以外のアプリケーションの SAML シングル サインオンを構成するには、サブスクリプションと Azure AD Premium ライセンスを持っており、アプリケーションが SAML 2.0 をサポートしている必要があります。 Azure AD のバージョンの詳細については、[Azure AD の価格](https://azure.microsoft.com/pricing/details/active-directory/)に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

アプリケーションが Azure AD テナントに追加されていない場合は、[ギャラリー以外のアプリの追加](add-non-gallery-app.md)に関するページを参照してください。

## <a name="step-1-edit-the-basic-saml-configuration"></a>手順 1. [基本的な SAML 構成] を編集する

1. クラウド アプリケーション管理者か Azure AD テナントのアプリケーション管理者として [Azure portal](https://portal.azure.com) にサインインします。

2. **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** に移動し、一覧からアプリケーションを選択します。 
   
   - アプリケーションを検索するには、 **[アプリケーションの種類]** メニューの **[すべてのアプリケーション]** を選択した後、 **[適用]** を選択します。 検索ボックスにアプリケーションの名前を入力し、結果からアプリケーションを選択します。

3. **[管理]** セクションの **[シングル サインオン]** を選択します。 

4. **[SAML]** を選択します。 **[SAML によるシングル サインオンのセットアップ - プレビュー]** ページが表示されます。

   ![手順 1. [基本的な SAML 構成] を編集する](media/configure-single-sign-on-non-gallery-applications/step-one-basic-saml-config.png)

5. 基本的な SAML 構成オプションを編集するには、 **[基本的な SAML 構成]** セクションの右上隅にある **[編集]** アイコン (鉛筆) を選択します。

1. 次の設定を入力します。 これらの値は、アプリケーション ベンダーから取得する必要があります。 値を手動で入力するか、メタデータ ファイルをアップロードしてフィールドの値を抽出できます。

    | [基本的な SAML 構成] の設定 | SP-Initiated | idP-Initiated | 説明 |
    |:--|:--|:--|:--|
    | **識別子 (エンティティ ID)** | 一部のアプリでは必須 | 一部のアプリでは必須 | アプリケーションを一意に識別します。 Azure AD から ID が SAML トークンの Audience パラメーターとしてアプリケーションに送信されます。 アプリケーションではこの ID を検証する必要があります。 また、この値はアプリケーションによって提供される SAML メタデータ内に Entity ID として表示されます。 "'https://<subdomain>.contoso.com' *この値は、アプリケーションから送信された **AuthnRequest** (SAML 要求) の **Issuer** 要素として見つけることができます。"* パターンを使用する URL を入力します。 |
    | **応答 URL** | 必須 | 必須 | アプリケーションが SAML トークンを受け取ることになっている場所を指定します。 応答 URL は Assertion Consumer Service (ACS) URL とも呼ばれています。 追加の応答 URL フィールドを使用して、複数の応答 URL を指定できます。 たとえば複数のサブドメインで、追加の応答 URL が必要となります。 またはテスト目的で、複数の応答 URL (ローカル ホストとパブリック URL) を一度に指定できます。 |
    | **サインオン URL** | 必須 | 指定しません | この URL をユーザーが開くと、サービス プロバイダーは、ユーザーの認証とサインインを行う Azure AD にそのユーザーをリダイレクトします。 Azure AD はその URL を使用して Office 365 または Azure AD アクセス パネルからアプリケーションを起動します。 何も入力されていない場合、ユーザーが Office 365、Azure AD アクセス パネル、または Azure AD SSO URL からアプリケーションを起動したとき、Azure AD により IdP-Initiated のサインオンが実行されます。|
    | **リレー状態** | 省略可能 | 省略可能 | 認証が完了した後にユーザーをリダイレクトする場所をアプリケーションに指示します。 通常、値はアプリケーションで有効な URL です。 ただし、一部のアプリケーションでは、このフィールドを異なる方法で使用します。 詳細については、アプリケーションのベンダーに問い合わせてください。
    | **ログアウト URL** | 省略可能 | 省略可能 | SAML ログアウト応答をアプリケーションに返送するために使用します。

詳細については、「[シングル サインオンの SAML プロトコル](../develop/single-sign-on-saml-protocol.md)」を参照してください。

## <a name="step-2-configure-user-attributes-and-claims"></a>手順 2. ユーザー属性と要求を構成する 

アプリケーションに対するユーザーの認証時に、Azure AD は、ユーザーを一意に識別する情報 (要求) を伴う SAML トークンをアプリケーションに発行します。 既定では、この情報には、ユーザーのユーザー名、電子メール アドレス、名、および姓が含まれます。 たとえば、特定の要求値またはユーザー名以外の**名前**形式をアプリケーションが必要とする場合に、これらの要求をカスタマイズする必要があります。 ギャラリー アプリの要件については、[アプリケーション固有のチュートリアル](../saas-apps/tutorial-list.md)で説明しています。または、該当するアプリケーションのベンダーにお問い合わせください。 ユーザー属性と要求を構成するための一般的な手順について以下で説明します。

1. **[ユーザー属性と要求]** セクションの右上隅にある **[編集]** アイコン (鉛筆) を選択します。

   ![手順 2. ユーザー属性と要求を構成する](media/configure-single-sign-on-non-gallery-applications/step-two-user-attributes-claims.png)

2. **[名前識別子の値]** を確認します。 既定値は *user.principalname* です。 アプリケーション内の各ユーザーは、ユーザー識別子によって一意に識別されます。 たとえば、メール アドレスがユーザー名と一意識別子を兼ねている場合、この値を *user.mail* に設定します。

3. **[名前識別子の値]** を変更するには、 **[名前識別子の値]** フィールドの **[編集]** アイコン (鉛筆) を選択します。 必要に応じて、識別子の形式とソースに適切な変更を加えます。 詳細については、「[NameID の編集](../develop/active-directory-saml-claims-customization.md#editing-nameid)」を参照してください。 完了したら、変更を保存します。 
 
4. グループ要求を構成するには、 **[要求で返されるグループ]** フィールドの **[編集]** アイコンを選択します。 詳細については、[グループ要求の構成](../hybrid/how-to-connect-fed-group-claims.md)に関するページを参照してください。

5. 要求を追加するには、ページの上部にある **[新しいクレームの追加]** を選択します。 **[名前]** を入力し、適切なソースを選択します。 **[属性]** ソースを選択した場合、使用する**ソース属性**を選択する必要があります。 **[翻訳]** ソースを選択した場合、使用する**変換**と**パラメーター 1** を選択する必要があります。 詳細については、「[アプリケーション固有の要求の追加](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims)」を参照してください。 完了したら、変更を保存します。 

6. **[保存]** を選択します。 新しい要求が表に表示されます。

   > [!NOTE]
   > Azure AD からお使いのアプリケーションへの SAML トークンをカスタマイズするその他の方法については、次のリソースを参照してください。
   >- Azure portal でカスタム ロールを作成するには、[ロール要求の構成](../develop/active-directory-enterprise-app-role-management.md)に関するページを参照してください。
   >- PowerShell を使用して要求をカスタマイズするには、[要求のカスタマイズ (PowerShell)](../develop/active-directory-claims-mapping.md) に関するページを参照してください。
   >- お使いのアプリケーション向けに省略可能な要求を構成するようアプリケーション マニフェストを変更するには、[省略可能な要求の構成](../develop/active-directory-optional-claims.md)に関するページを参照してください。
   >- 更新トークン、アクセス トークン、セッション トークン、ID トークンのトークンの有効期間ポリシーを設定するには、[トークンの有効期間の構成](../develop/active-directory-configurable-token-lifetimes.md)に関するページを参照してください。 また、Azure AD の条件付きアクセスを使用して認証セッションを制限するには、[認証セッションの管理機能](https://go.microsoft.com/fwlink/?linkid=2083106)に関するページを参照してください。

## <a name="step-3-manage-the-saml-signing-certificate"></a>手順 3. SAML 署名証明書を管理する

Azure AD では、アプリケーションに送信する SAML トークンの署名に証明書を使用します。 この証明書を使用して、Azure AD とアプリケーション間の信頼を設定する必要があります。 証明書の形式の詳細については、アプリケーションの SAML ドキュメントを参照してください。 詳細については、[フェデレーション シングル サインオンの証明書の管理](manage-certificates-for-federated-single-sign-on.md)と[SAML トークンの詳細な証明書署名オプション](certificate-signing-options.md)に関するページを参照してください。

Azure AD の **[SAML によるシングル サインオンのセットアップ]** メイン ページから直接、Base64 または未加工形式でアクティブな証明書をダウンロードできます。 または、証明書は、アプリケーションのメタデータ XML ファイルをダウンロードするか、アプリのフェデレーション メタデータ URL を使用して取得できます。 証明書 (アクティブまたは非アクティブ) を表示、作成、またはダウンロードするには、次の手順を実行します。

1. **[SAML 署名証明書]** セクションに移動します。 

   ![手順 3. SAML 署名証明書を管理する](./media/configure-single-sign-on-non-gallery-applications/step-three-certificate.png)

2. 証明書に以下が含まれていることを確認します。

   - *期待どおりの有効期限。* 最大 3 年間の有効期限を構成できます。
   - *必要な証明書のアクティブな状態。* 状態が**非アクティブ**の場合は、**アクティブ**に変更します。 状態を変更するには、目的の証明書の行を右クリックし、 **[証明書をアクティブにする]** を選択します。
   - *適切な署名オプションおよびアルゴリズム。*
   - *適切な通知用メール アドレス。* アクティブな証明書の有効期限が近づくと、Azure AD は、このフィールドに構成されている電子メール アドレスに通知を送信します。

2. 証明書をダウンロードするには、Base64 形式、未加工の形式、フェデレーション メタデータ XML のいずれかのオプションを選択します。 また、Azure AD では、アプリケーション固有のメタデータにアクセスできる**アプリのフェデレーション メタデータ URL** が `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>` 形式で提供されます。

3. 証明書を管理、作成、またはインポートするには、 **[SAML 署名証明書]** セクションの右上隅にある **[編集]** アイコン (鉛筆) を選択します。

   ![SAML 署名証明書](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)


   次のいずれかの操作を実行します。

   - 新しい証明書を作成するには、 **[新しい証明書]** を選択し、 **[有効期限]** を選択して、 **[保存]** を選択します。 証明書をアクティブ化するには、コンテキスト メニュー ( **...** ) を選択し、 **[証明書をアクティブにする]** を選択します。
   - 秘密キーと pfx の資格情報を含む証明書をアップロードするには、 **[証明書のインポート]** を選択し、その証明書を参照します。 **[PFX パスワード]** を入力し、 **[追加]** を選択します。  
   - 詳細な証明書署名オプションを構成するには、次のオプションを使用します。 これらのオプションの説明については、[詳細な証明書署名オプション](certificate-signing-options.md)に関する記事を参照してください。
      - **[署名オプション]** ドロップダウン リストで、 **[SAML 応答への署名]** 、 **[SAML アサーションへの署名]** 、または **[SAML 応答とアサーションへの署名]** を選択します。
      - **[署名アルゴリズム]** ドロップダウン リストで、 **[SHA-1]** または **[SHA-256]** を選択します。
   - アクティブな証明書の有効期限が近づいたときに他のユーザーに通知するには、 **[通知の電子メール アドレス]** フィールドにメール アドレスを入力します。

4. 変更を加えた場合は、 **[SAML 署名証明書]** セクションの上部にある **[保存]** を選択します。 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>手順 4. Azure AD を使用するようにアプリケーションを設定する

**[\<applicationName> のセットアップ]** セクションには、Azure AD が SAML ID プロバイダーとして使用されるようにアプリケーションで構成する必要のある値の一覧が表示されます。 必要な値は、アプリケーションによって異なります。 詳細については、アプリケーションの SAML ドキュメントを参照してください。 ドキュメントを検索するには、 **[\<アプリケーション名> のセットアップ]** の見出しに移動し、 **[ステップ バイ ステップの手順を表示]** を選択します。 ドキュメントが **[サインオンの構成]** ページに表示されます。 そのページでは、 **[** アプリケーション名> のセットアップ]**の見出しで**[ログイン URL] **、** [Azure AD 識別子] **、および \<[ログアウト URL]** の値を入力する手順が説明されています。

1. **[\<applicationName> のセットアップ]** セクションまで下にスクロールします。 
   
   ![手順 4. アプリケーションをセットアップする](media/configure-single-sign-on-non-gallery-applications/step-four-app-config.png)

1. 必要に応じてこのセクションの各行から値をコピーし、アプリケーション固有の手順に従ってその値をアプリケーションに追加します。 ギャラリー アプリの場合は、 **[ステップ バイ ステップの手順を表示]** を選択するとドキュメントを表示できます。 
   - **ログイン URL** と**ログアウト URL** の両方が、同じエンドポイント (Azure AD のインスタンスの SAML 要求処理エンドポイント) に解決されます。 
   - **Azure AD 識別子**は、アプリケーションに発行された SAML トークンでの **[発行者]** の値です。
2. すべての値を適切なフィールドに貼り付けたら、 **[保存]** を選択します。

## <a name="step-5-validate-single-sign-on"></a>手順 5. シングル サインオンを検証する

SAML ベースの ID プロバイダーとして Azure AD を使用するようにアプリケーションを構成し終えたら、設定をテストし、ご利用のアカウントでシングル サインオンが機能するかどうかを確認できます。 

2. **[<applicationName> でシングル サインオンを検証]** セクションまでスクロールします。

   ![手順 5. シングル サインオンを検証する](media/configure-single-sign-on-non-gallery-applications/step-five-validate.png)

3. **[検証]** を選択します。 テストのオプションが表示されます。

4. **[現在のユーザーとしてサインイン]** を選択します。 

サインオンに成功した場合は、SAML アプリケーションにユーザーとグループを割り当てることができます。
エラー メッセージが表示されたら、次の手順を完了してください。

1. 詳細をコピーして **[エラーの説明]** ボックスに貼り付けます。

    ![解決ガイダンスを入手する](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. **[解決ガイダンスを入手する]** を選択します。 根本原因と解決ガイダンスが表示されます。  この例では、アプリケーションにユーザーが割り当てられていませんでした。

3. 解決ガイダンスを読み、可能であれば問題を解決します。

4. 成功するまでテストを再実行します。

詳細については、「[Azure Active Directory のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグする](../azuread-dev/howto-v1-debug-saml-sso-issues.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [アプリケーションにユーザーまたはグループを割り当てる](methods-for-assigning-users-and-groups.md)
- [自動ユーザー アカウント プロビジョニングを構成する](../app-provisioning/configure-automatic-user-provisioning-portal.md)
