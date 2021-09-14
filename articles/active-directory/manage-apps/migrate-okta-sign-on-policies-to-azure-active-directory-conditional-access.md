---
title: Okta サインオン ポリシーを Azure Active Directory の条件付きアクセスに移行するチュートリアル
titleSuffix: Active Directory
description: Okta サインオン ポリシーを Azure Active Directory の条件付きアクセスに移行する方法について説明します
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: f8967d0674689328bf0f761a5f39c17c505d1bd6
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440166"
---
# <a name="tutorial-migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access"></a>チュートリアル: Okta サインオン ポリシーを Azure Active Directory の条件付きアクセスに移行する

このチュートリアルでは、組織で、Okta のグローバルまたはアプリケーションレベルのサインオン ポリシーから Azure Active Directory (AD) の条件付きアクセス (CA) ポリシーに移行して、Azure AD や接続されたアプリケーションでのユーザー アクセスを保護する方法について説明します。

このチュートリアルでは、サインオンと多要素認証 (MFA) 用に Okta と連携する Office 365 テナントがあることを前提としています。 また、Azure AD へのユーザー プロビジョニングのために、Azure Active Directory Connect サーバーまたは Azure AD Connect クラウド プロビジョニング エージェントを構成している必要があります。

## <a name="prerequisites"></a>前提条件

Okta サインオンから Azure AD の CA に切り替えるときは、ライセンス要件を理解しておくことが重要です。 Azure AD の CA で Azure AD Multi-Factor Authentication を登録するには、ユーザーに Azure AD Premium P1 ライセンスが割り当てられている必要があります。

Hybrid Azure AD 参加の手順を実行する前に、まずはサービス接続ポイント (SCP) レコードを構成するため、オンプレミス フォレストのエンタープライズ管理者の資格情報が必要になります。

## <a name="step-1---catalog-current-okta-sign-on-policies"></a>手順 1 - 現在の Okta サインオン ポリシーをカタログ化する

CA への移行を成功させるには、既存の Okta サインオン ポリシーを評価して、Azure AD に移行するユース ケースと要件を決定する必要があります。

1. **[セキュリティ]** に移動し、 **[認証]** を選択してから **[サインオン]** を選択して、グローバル サインオン ポリシーを確認します。

   ![グローバル サインオン ポリシーを示す画像](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/global-sign-on-policies.png)

   この例では、Microsoft のグローバル サインオン ポリシーによって、構成されたネットワーク ゾーン以外のすべてのセッションに MFA が適用されています。

   ![グローバル サインオン ポリシー enforc mfa を示す画像](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/global-sign-on-policies-enforce-mfa.png)

2. 次に、 **[アプリケーション]** に移動し、アプリケーションレベルのサインオン ポリシーを確認します。 サブメニューから **[アプリケーション]** を選択し、 **[アクティブなアプリ] の一覧** から Office 365 接続されているインスタンスを選択します。

3. 最後に、 **[サインオン]** を選択して、ページの下部までスクロールします。

次の例の Office 365 アプリケーションのサインオン ポリシーには、4 つの異なる規則があります。

- **モバイル セッションに MFA を適用する** - iOS または Android で、すべての最新の認証またはブラウザー セッションの MFA が必要です。

- **信頼されている Windows デバイスを許可する** - 信頼されている Okta デバイスに追加の検証または要素の入力が求められないようにします。

- **信頼されていない Windows デバイスから MFA を要求する** - 信頼されていない Windows デバイスで、最新の認証やブラウザー セッションごとに MFA を要求します。

- **レガシ認証をブロックする** - レガシ認証クライアントがサービスに接続できないようにします。

  ![o365 のサインオン規則を示す画像](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/sign-on-rules.png)

## <a name="step-2---configure-condition-pre-requisites"></a>手順 2 - 条件の前提条件を構成する

Azure AD の CA ポリシーは、ほとんどのシナリオで追加構成なしに Okta の条件に合わせて構成することができます。

いくつかのシナリオでは、CA ポリシーを構成する前に追加の設定が必要な場合があります。 この記事の執筆時点では、以下の 2 つの既知のシナリオがあります。

- **Okta のネットワークの場所から Azure AD のネームド ロケーションへ** - [こちらの記事](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition#named-locations)に従って、Azure AD でネームド ロケーションを構成します。

- **Okta のデバイスの信頼からデバイスベースの CA へ** - CA は、ユーザーのデバイスを評価する際に 2 つのオプションを提供します。

  - [Hybrid Azure AD 参加](#hybrid-azure-ad-join-configuration) - Azure AD Connect サーバー内で有効な機能で、Windows 10、Server 2016、2019 などの Windows の現行デバイスを Azure AD に同期します。

  - [デバイスを Microsoft エンドポイント マネージャーに登録](#configure-device-compliance)し、コンプライアンス ポリシーを割り当てます。

### <a name="hybrid-azure-ad-join-configuration"></a>Hybrid Azure AD 参加の構成

Hybrid Azure AD 参加を有効にするには、Azure AD Connect サーバーで構成ウィザードを実行します。 設定後、デバイスを自動的に登録するための手順が必要となります。

>[!NOTE]
>Hybrid Azure AD 参加は、Azure AD Connect のクラウド プロビジョニング エージェントではサポートされていません。

1. Hybrid Azure AD 参加を有効にするには、これらの[手順](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-managed-domains#configure-hybrid-azure-ad-join)に従ってください。

2. [SCP の構成] ページで、 **[認証サービス]** ドロップダウンを選択します。 Okta フェデレーション プロバイダーの URL を選択してから、 **[追加]** を選択します。 オンプレミスのエンタープライズ管理者の資格情報を入力し、 **[次へ]** を選択します。

   ![scp の構成を示す画像](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/scp-configuration.png)

3. グローバルまたはアプリ レベルのサインオン ポリシーによって、Windows クライアントでレガシ認証がブロックされている場合は、Hybrid Azure AD 参加プロセスの終了を許可する規則を作成します。

4. すべての Windows クライアントのレガシ認証スタック全体を許可するか、Okta のサポートに連絡して取得したカスタム クライアント文字列を既存のアプリ ポリシーで有効にすることができます。

### <a name="configure-device-compliance"></a>デバイス コンプライアンスの構成

Hybrid Azure AD 参加は、Windows の Okta のデバイスの信頼に直接代わるものですが、CA ポリシーは、Microsoft エンドポイント マネージャーに完全に登録されているデバイスのデバイス コンプライアンスを確認することもできます。

- **コンプライアンスの概要** - [Microsoft Intune のデバイス コンプライアンス ポリシー](https://docs.microsoft.com/mem/intune/protect/device-compliance-get-started#:~:text=Reference%20for%20non-compliance%20and%20Conditional%20Access%20on%20the,applicable%20%20...%20%203%20more%20rows)に関するページを参照してください。

- **デバイス コンプライアンス** - [Microsoft Intune でポリシー](https://docs.microsoft.com/mem/intune/protect/create-compliance-policy)を作成します。

- **Windows の登録** - Hybrid Azure AD 参加を展開することを選択した場合は、追加のグループ ポリシーを展開して、[これらのデバイスの Microsoft Intune への自動登録プロセス](https://docs.microsoft.com/windows/client-management/mdm/enroll-a-windows-10-device-automatically-using-group-policy)を完了できます。

- **iOS または iPadOS の登録** - ios デバイスを登録する前に、エンドポイント管理コンソールで[追加の構成](https://docs.microsoft.com/mem/intune/enrollment/ios-enroll)を行う必要があります。

- **Android の登録** - Android デバイスを登録する前に、エンドポイント管理コンソールで[追加の構成](https://docs.microsoft.com/mem/intune/enrollment/android-enroll)を行う必要があります。

## <a name="step-3---configure-azure-ad-multi-factor-authentication-tenant-settings"></a>手順 3 - Azure AD Multi-Factor Authentication のテナント設定を構成する

CA に変換する前に、組織の Azure AD Multi-Factor Authentication テナントの基本設定を確認します。

1. [Azure portal](https://portal.azure.com) に移動して、全体管理者アカウントを使用してサインインします。

2. **[Azure Active Directory]** 、 **[ユーザー]** 、 **[多要素認証]** の順に選択すると、レガシの Azure MFA ポータルが表示されます。

   ![レガシの Azure AD Multi-Factor Authentication ポータルを示す画像](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/legacy-azure-ad-portal.png)

代わりに、 **<https://aka.ms/mfaportal>** を使用することができます。

4. **[Legacy Azure MFA]\(レガシの Azure MFA\)** メニューで、状態メニューを **[enabled]\(有効\)** から **[enforced]\(適用済み\)** に変更して、レガシの MFA を有効にしているユーザーがいないことを確認します。 テナントに以下のように表示されているユーザーがいる場合は、レガシ メニューでそれらを無効にする必要があります。 その後、そのアカウントで CA ポリシーが有効になります。

   ![レガシの Azure AD Multi-Factor Authentication ポータルで無効になったユーザーを示す画像](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/disable-user-legacy-azure-ad-portal.png)

   **[enforced]\(適用済み\)** フィールドも空にする必要があります。

   ![レガシの Azure AD Multi-Factor Authentication ポータルの空の [enforced]\(適用済み\) フィールドを示す画像](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/enforced-empty-legacy-azure-ad-portal.png)

5. レガシ MFA で構成されているユーザーがいないことを確認したら、 **[サービス設定]** オプションを選択します。 **[アプリ パスワード]** の選択を **[ブラウザーではないアプリケーションへのサインイン用にアプリケーション パスワードの作成を許可しない]** に変更します。

6. **[イントラネット内のフェデレーション ユーザーからのリクエストの場合、多要素認証をスキップする]** と **[信頼済みデバイスでユーザーが多要素認証を記憶できるようにする (1 から 365 日)]** のボックスのチェックがオフになっていることを確認してから、 **[保存]** を選択します。

   >[!NOTE]
   >詳細については、[MFA プロンプトの設定を構成するためのベスト プラクティス](https://aka.ms/mfaprompts)に関するページを参照してください。

   ![レガシの Azure AD Multi-Factor Authentication ポータルのオフになっているフィールドを示す画像](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/uncheck-fields-legacy-azure-ad-portal.png)

## <a name="step-4---configure-ca-policies"></a>手順 4 - CA ポリシーを構成する

前提条件を構成し、基本設定を確立したら、次は最初の CA ポリシーを作成しましょう。

1. Azure AD で CA ポリシーを構成するには、[Azure portal](https://portal.azure.com) に移動します。 [Azure Active Directory の管理] で、 **[表示]** を選択します。

2. CA ポリシーを構成する際は、[CA を導入して設計する際のベストプラクティス](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access#understand-conditional-access-policy-components)を念頭に置く必要があります。

3. Okta からのグローバル サインオン MFA ポリシーを模倣するために、[ポリシーを作成](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-all-users-mfa)します。

4. [デバイスの信頼ベースの CA 規則](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)を作成します。

5. このポリシーは、このチュートリアルの他の項目と同様に、特定のアプリケーション、テスト グループのユーザー、またはその両方を対象とすることができます。

   ![テスト ユーザーを示す画像](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/test-user.png)

   ![テスト ユーザーの成功を示す画像](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/success-test-user.png)

6. 場所ベースのポリシーとデバイスの信頼ポリシーを構成したら、今度は同等の [**ブロック レガシ認証**](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-block-legacy)ポリシーを構成します。

これら 3 つの CA ポリシーにより、元の Okta サインオン ポリシーのエクスペリエンスが Azure AD にレプリケートされました。 次の手順では、ユーザーを Azure MFA に登録し、ポリシーをテストします。

## <a name="step-5---enroll-pilot-members-in-azure-ad-multi-factor-authentication"></a>手順 5 - Azure AD Multi-Factor Authentication にパイロット メンバーを登録する

CA ポリシーを構成したら、ユーザーを Azure MFA メソッドに登録する必要があります。 ユーザーには、いくつかの異なる方法で登録を要求することができます。

1. 個別に登録するには、ユーザーを <https://aka.ms/mfasetup> に誘導して、登録情報を手動で入力してもらいます。

2. ユーザーは、<https://aka.ms/mysecurityinfo> にアクセスして情報を入力したり、MFA 登録のフォームを管理したりできます。

MFA の登録プロセスについてより詳しく理解するには、[こちらのガイド](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined)を参照してください。  

Okta MFA でサインインした後に <https://aka.ms/mfasetup> に移動すると、Azure AD で MFA を登録するように指示されます。

>[!NOTE]
>そのユーザーが既に登録されている場合は、MFA プロンプトに対応した後、 **[My Security]\(マイ セキュリティ\)** の情報ページが表示されます。

詳しくは、[MFA 登録に関するエンドユーザー向けのドキュメント](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-signin)を参照してください。

## <a name="step-6---enable-ca-policies"></a>手順 6 - CA ポリシーを有効にする

1. テストをロールアウトするには、前の例で作成したポリシーを、 **[Enabled test user login]\(テスト ユーザーによるログインを有効にする\)** に変更します。 

   ![テスト ユーザーの有効化を示す画像](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/enable-test-user.png)

2. 次回、テスト ユーザーの John Smith で Office 365 にサインインすると、Okta MFA および Azure AD Multi-Factor Authentication でのサインインが求められます。

   ![Okta を使用したサインインを示す画像](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/sign-in-through-okta.png)

3. Okta による MFA の検証を完了します。

   ![Okta による MFA の検証を示す画像](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/mfa-verification-through-okta.png)

4. ユーザーが Okta MFA プロンプトを完了すると、CA の入力を求めるメッセージが表示されます。 ポリシーが適切に構成され、MFA のトリガーとなる条件を満たしていることを確認します。

   ![Okta での MFA 検証で CA の入力を求められているプロンプトの画像](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/mfa-verification-through-okta-prompted-ca.png)

## <a name="step-7---cutover-from-sign-on-to-ca-policies"></a>手順 7 - サインオンから CA ポリシーに切り替える

パイロット メンバーに対して徹底的なテストを行い、CA が期待どおりに動作することを確認したら、登録完了後に残りの組織メンバーを CA ポリシーに追加することができます。

Azure MFA と Okta MFA の間で二重にプロンプトが表示されるのを避けるために、サインオン ポリシーを変更して Okta MFA からオプトアウトする必要があります。

CA への移行の最後のステップは、段階的またはカットオーバー方式で行うことができます。

1. Okta の管理コンソールに移動し、 **[セキュリティ]** 、 **[認証]** の順に選択してから、 **[サインオン ポリシー]** に移動します。

>[!NOTE]
>グローバル ポリシーは、Okta のすべてのアプリケーションがそれぞれ独自のアプリケーション サインオン ポリシーによって保護されている場合にのみ、非アクティブに設定する必要があります。

2. MFA ポリシーの適用を **[非アクティブ]** に設定するか、Azure AD のユーザーが含まれていない新しいグループにポリシーを割り当てます。

   ![MFA ポリシーが非アクティブになっていることを示す画像](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/mfa-policy-inactive.png)

3. アプリケーションレベルのサインオン ポリシーで **[ルールを無効にする]** オプションを選択して、ポリシーを非アクティブに更新します。 また、Azure AD ユーザーを含まない新しいグループにポリシーを割り当てることもできます。

4. MFA なしでのアクセスを許可するアプリケーションに対して、有効なアプリケーション レベルのサインオン ポリシーが少なくとも 1 つあることを確認します。

   ![MFA を使用しないアプリケーション アクセスを示す画像](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/application-access-without-mfa.png)

5. Okta のサインオン ポリシーを無効にしたり、移行した Azure AD のユーザーを強制グループから除外したりすると、そのユーザーには、次回のサインイン時に CA の入力 "**のみ**" が求められます。

## <a name="next-steps"></a>次のステップ

- [Okta から Azure AD にアプリケーションを移行する](migrate-applications-from-okta-to-azure-active-directory.md)

- [Okta フェデレーションを Azure AD に移行する](migrate-okta-federation-to-azure-active-directory.md)

- [Okta 同期プロビジョニングを Azure AD Connect ベースの同期に移行する](migrate-okta-sync-provisioning-to-azure-active-directory.md)
