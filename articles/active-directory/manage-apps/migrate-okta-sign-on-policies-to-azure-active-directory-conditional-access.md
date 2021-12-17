---
title: Okta サインオン ポリシーを Azure Active Directory の条件付きアクセスに移行するチュートリアル
titleSuffix: Active Directory
description: このチュートリアルでは、Okta サインオン ポリシーを Azure Active Directory の条件付きアクセスに移行する方法について説明します。
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: 5cca89b361152d3a4a5635f008f70f145d5d68a0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131459438"
---
# <a name="tutorial-migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access"></a>チュートリアル: Okta サインオン ポリシーを Azure Active Directory の条件付きアクセスに移行する

このチュートリアルでは、組織で、Okta のグローバルまたはアプリケーションレベルのサインオン ポリシーから Azure Active Directory (Azure AD) の条件付きアクセス ポリシーに移行して、Azure AD や接続されたアプリケーションでのユーザー アクセスを保護する方法について説明します。

このチュートリアルでは、サインインと多要素認証 (MFA) 用に Okta と連携する Office 365 テナントがあることを前提としています。 また、Azure AD へのユーザー プロビジョニングのために、Azure Active Directory Connect サーバーまたは Azure AD Connect クラウド プロビジョニング エージェントを構成している必要があります。

## <a name="prerequisites"></a>前提条件

Okta サインオンから条件付きアクセスに切り替える場合は、ライセンス要件を理解しておくことが重要です。 条件付きアクセスで Azure AD Multi-Factor Authentication を登録するには、ユーザーに Azure AD Premium P1 ライセンスが割り当てられている必要があります。

Hybrid Azure AD 結合の手順を実行する前に、まずはサービス接続ポイント (SCP) レコードを構成するため、オンプレミス フォレストのエンタープライズ管理者の資格情報が必要になります。

## <a name="catalog-current-okta-sign-on-policies"></a>現在の Okta サインオン ポリシーをカタログ化する

条件付きアクセスへの移行を成功させるには、既存の Okta サインオン ポリシーを評価して、Azure AD に移行するユース ケースと要件を決定します。

1. **[セキュリティ]**  >  **[認証]**  >  **[サインオン]** の順に移動して、グローバル サインオン ポリシーを確認します。

   ![グローバル サインオン ポリシーを示すスクリーンショット。](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/global-sign-on-policies.png)

   この例では、グローバル サインオン ポリシーによって、Microsoft の構成済みのネットワーク ゾーン以外のすべてのセッションに MFA が適用されています。

   ![MFA を適用するグローバル サインオン ポリシーを示すスクリーンショット。](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/global-sign-on-policies-enforce-mfa.png)

2. **[アプリケーション]** に移動し、アプリケーションレベルのサインオン ポリシーを確認します。 サブメニューから **[アプリケーション]** を選択し、 **[アクティブなアプリ] の一覧** から Office 365 接続されているインスタンスを選択します。

3. **[サインオン]** を選択して、ページの下部までスクロールします。

次の例の Office 365 アプリケーションのサインオン ポリシーには、4 つの異なる規則があります。

- **モバイル セッションに MFA を適用する**: iOS または Android で、すべての最新の認証またはブラウザー セッションの MFA が必要です。
- **信頼されている Windows デバイスを許可する**: 信頼されている Okta デバイスに追加の検証または要素の入力が求められないようにします。
- **信頼されていない Windows デバイスから MFA を要求する**: 信頼されていない Windows デバイスで、最新の認証やブラウザー セッションごとに MFA を要求します。
- **レガシ認証をブロックする**: レガシ認証クライアントがサービスに接続できないようにします。

  ![Office 365 のサインオン規則を示すスクリーンショット。](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/sign-on-rules.png)

## <a name="configure-condition-prerequisites"></a>条件の前提条件を構成する

条件付きアクセス ポリシーは、ほとんどのシナリオで追加構成なしに Okta の条件に合わせて構成することができます。

いくつかのシナリオでは、条件付きアクセス ポリシーを構成する前に追加の設定が必要な場合があります。 この記事の執筆時点では、以下の 2 つの既知のシナリオがあります。

- **Azure AD 内の名前付きの場所への Okta ネットワークの場所**: 「[条件付きアクセス ポリシーでの場所の条件の使用](../conditional-access/location-condition.md#named-locations)」の手順に従って、Azure AD で名前付きの場所を構成します。
- **Okta のデバイスの信頼からデバイスベースの CA へ**: 条件付きアクセスは、ユーザーのデバイスを評価する際に 2 つのオプションを提供します。

  - [Hybrid Azure AD 結合を使用します](#hybrid-azure-ad-join-configuration)。これは Azure AD Connect サーバー内で有効な機能で、Windows 10、Windows Server 2016、Windows Server 2019 などの Windows の現行デバイスを Azure AD に同期します。
  - [デバイスをエンドポイント マネージャーに登録](#configure-device-compliance)し、コンプライアンス ポリシーを割り当てます。

### <a name="hybrid-azure-ad-join-configuration"></a>Hybrid Azure AD 参加の構成

Azure AD Connect サーバーで Hybrid Azure AD 結合を有効にするには、構成ウィザードを実行します。 デバイスを自動的に登録するには、構成後の手順を実行する必要があります。

>[!NOTE]
>Hybrid Azure AD 参加は、Azure AD Connect のクラウド プロビジョニング エージェントではサポートされていません。

1. Hybrid Azure AD 結合を有効にするには、これらの[手順](../devices/hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join)に従ってください。

1. **[SCP の構成]** ページで、 **[認証サービス]** ドロップダウンを選択します。 Okta フェデレーション プロバイダーの URL を選択し、 **[追加]** を選択します。 オンプレミスのエンタープライズ管理者の資格情報を入力してから、 **[次へ]** を選択します。

   ![SCP の構成を示すスクリーンショット。](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/scp-configuration.png)

1. グローバルまたはアプリ レベルのサインオン ポリシーによって、Windows クライアントでレガシ認証がブロックされている場合は、Hybrid Azure AD 結合プロセスの終了を許可する規則を作成します。

1. すべての Windows クライアントのレガシ認証スタック全体を許可します。 また、Okta のサポートに連絡して、カスタム クライアント文字列を既存のアプリ ポリシーで有効にすることができます。

### <a name="configure-device-compliance"></a>デバイス コンプライアンスの構成

Hybrid Azure AD 結合は、Windows 上の Okta デバイスの信頼の直接的な置き換えです。 条件付きアクセス ポリシーでは、エンドポイント マネージャーに完全に登録されているデバイスのコンプライアンスを確認することもできます。

- **コンプライアンスの概要**: [Intune のデバイス コンプライアンス ポリシー](/mem/intune/protect/device-compliance-get-started#:~:text=Reference%20for%20non-compliance%20and%20Conditional%20Access%20on%20the,applicable%20%20...%20%203%20more%20rows)に関するページを参照してください。
- **デバイス コンプライアンス**: [Intune でポリシー](/mem/intune/protect/create-compliance-policy)を作成します。
- **Windows の登録**: Hybrid Azure AD 結合を展開することを選択した場合は、別のグループ ポリシーを展開して、[これらのデバイスの Intune への自動登録プロセス](/windows/client-management/mdm/enroll-a-windows-10-device-automatically-using-group-policy)を完了できます。
- **iOS または iPadOS の登録**: iOS デバイスを登録する前に、エンドポイント管理コンソールで [追加の構成](/mem/intune/enrollment/ios-enroll)を行う必要があります。
- **Android の登録**: Android デバイスを登録する前に、エンドポイント管理コンソールで [追加の構成](/mem/intune/enrollment/android-enroll)を行う必要があります。

## <a name="configure-azure-ad-multi-factor-authentication-tenant-settings"></a>Azure AD Multi-Factor Authentication のテナント設定を構成する

条件付きアクセスに変換する前に、組織の Azure AD Multi-Factor Authentication テナントの基本設定を確認します。

1. [Azure portal](https://portal.azure.com) に移動して、グローバル管理者アカウントを使用してサインインします。

1. **[Azure Active Directory]**  >  **[ユーザー]**  >  **[Multi-Factor Authentication]** の順に選択して、レガシの Azure AD Multi-Factor Authentication ポータルに移動します。

   ![レガシの Azure AD Multi-Factor Authentication ポータルを示すスクリーンショット。](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/legacy-azure-ad-portal.png)

   [Azure AD Multi-Factor Authentication ポータル](https://aka.ms/mfaportal)へのレガシのリンクを使用することもできます。

1. レガシの "**多要素認証**" メニューで、状態メニューを **[有効]** から **[適用済み]** に変更して、レガシの MFA を有効にしているユーザーがいないことを確認します。 テナントに次のように表示されているユーザーがいる場合は、レガシ メニューでそれらを無効にする必要があります。 そうして初めて、条件付きアクセス ポリシーがそのアカウントで有効になります。

   ![レガシの Azure AD Multi-Factor Authentication ポータルでのユーザーの無効化を示すスクリーンショット。](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/disable-user-legacy-azure-ad-portal.png)

   **[適用済み]** フィールドも空にする必要があります。

1. **[サービスの設定]** オプションを選択します。 **[アプリ パスワード]** の選択を **[ブラウザーではないアプリケーションへのサインイン用にアプリケーション パスワードの作成を許可しない]** に変更します。

   ![ユーザーがアプリ パスワードを作成することを許可しないアプリケーションパスワード設定を示すスクリーンショット。](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/app-password-selection.png)

1. **[イントラネット内のフェデレーション ユーザーからのリクエストの場合、多要素認証をスキップする]** と **[信頼済みデバイスでユーザーが多要素認証を記憶できるようにする (1 から 365 日)]** のチェックボックスがオフになっていることを確認してから、 **[保存]** を選択します。

  >[!NOTE]
   >詳細については、[MFA プロンプトの設定を構成するためのベスト プラクティス](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)に関するページを参照してください。

![レガシの Azure AD Multi-Factor Authentication ポータルでオフになっているチェックボックスを示すスクリーンショット。](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/uncheck-fields-legacy-azure-ad-portal.png)

## <a name="configure-conditional-access-policies"></a>条件付きアクセス ポリシーを構成する

前提条件を構成し、基本設定を確立したら、次は最初の条件付きアクセス ポリシーを作成しましょう。

1. Azure AD で条件付きアクセス ポリシーを構成するには、[Azure portal](https://portal.azure.com) に移動します。 **[Azure Active Directory の管理]** で、 **[表示]** を選択します。

   [条件付きアクセスをデプロイおよび設計するためのベスト プラクティス](../conditional-access/plan-conditional-access.md#understand-conditional-access-policy-components)に従って、条件付きアクセス ポリシーを構成します。

1. Okta からのグローバル サインオン MFA ポリシーを模倣するために、[ポリシーを作成](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)します。

1. [デバイスの信頼ベースの条件付きアクセス規則](../conditional-access/require-managed-devices.md)を作成します。

   このポリシーは、このチュートリアルの他の項目と同様に、特定のアプリケーション、テスト グループのユーザー、またはその両方を対象とすることができます。

   ![ユーザーのテストを示すスクリーンショット。](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/test-user.png)

   ![ユーザーのテストが成功したことを示すスクリーンショット。](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/success-test-user.png)

1. 場所ベースのポリシーとデバイスの信頼ポリシーを構成したら、今度は同等の[ブロック レガシ認証](../conditional-access/howto-conditional-access-policy-block-legacy.md)ポリシーを構成します。

これら 3 つの条件付きアクセス ポリシーにより、元の Okta サインオン ポリシーのエクスペリエンスが Azure AD にレプリケートされました。 次の手順では、Azure AD Multi-Factor Authentication を使用してユーザーを登録し、ポリシーをテストします。

## <a name="enroll-pilot-members-in-azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication にパイロット メンバーを登録する

条件付きアクセス ポリシーを構成した後、ユーザーは Azure AD Multi-Factor Authentication 方法を登録する必要があります。 ユーザーには、いくつかの異なる方法で登録を要求することができます。

1. 個別に登録するには、ユーザーを [Microsoft サインイン ウィンドウ](https://aka.ms/mfasetup)に誘導して、登録情報を手動で入力してもらいます。

1. ユーザーは、[Microsoft セキュリティ情報のページ](https://aka.ms/mysecurityinfo)にアクセスして、情報を入力したり、MFA 登録のフォームを管理したりできます。

MFA の登録プロセスについてより詳しく理解するには、[こちらのガイド](../authentication/howto-registration-mfa-sspr-combined.md)を参照してください。

[Microsoft サインイン ウィンドウ](https://aka.ms/mfasetup)に移動します。 Okta MFA でサインインした後、Azure AD で MFA を登録するように指示されます。

>[!NOTE]
>ユーザーが既に登録されている場合は、MFA プロンプトに対応した後、 **[自分のセキュリティ]** の情報ページが表示されます。
詳しくは、[MFA 登録に関するユーザー向けのドキュメント](../user-help/security-info-setup-signin.md)を参照してください。

## <a name="enable-conditional-access-policies"></a>条件付きアクセス ポリシーを有効にする

1. テストをロールアウトするには、前の例で作成したポリシーを、 **[Enabled test user login]\(テスト ユーザーによるログインを有効にする\)** に変更します。

   ![テスト ユーザーの有効化を示すスクリーンショット。](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/enable-test-user.png)

1. 次の Office 365 の **[サインイン]** ウィンドウで、テスト ユーザーの John Smith には、Okta MFA および Azure AD Multi-Factor Authentication でのサインインが求められます。

   ![Azure サインイン ウィンドウを示すスクリーンショット。](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/sign-in-through-okta.png)

1. Okta による MFA の検証を完了します。

   ![Okta による MFA の検証を示すスクリーンショット。](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/mfa-verification-through-okta.png)

1. ユーザーが Okta MFA プロンプトを完了すると、条件付きアクセスの入力を求めるメッセージが表示されます。 ポリシーが適切に構成され、MFA のトリガーとなる条件を満たしていることを確認します。

   ![Okta での MFA 検証で条件付きアクセスの入力を求められているプロンプトのスクリーンショット。](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/mfa-verification-through-okta-prompted-ca.png)

## <a name="cut-over-from-sign-on-to-conditional-access-policies"></a>サインオンから条件付きアクセス ポリシーへのカットオーバー

パイロット メンバーに対して徹底的なテストを行い、条件付きアクセスが期待どおりに動作することを確認したら、登録完了後に残りの組織メンバーを条件付きアクセス ポリシーに追加することができます。

Azure AD Multi-Factor Authentication と Okta MFA の間で二重にプロンプトが表示されるのを避けるために、サインオン ポリシーを変更して Okta MFA からオプトアウトします。

条件付きアクセスへの移行の最後のステップは、段階的またはカットオーバー方式で行うことができます。

1. Okta の管理コンソールに移動し、 **[セキュリティ]**  >  **[認証]** の順に選択してから、 **[サインオン ポリシー]** に移動します。

   >[!NOTE]
   > グローバル ポリシーは、Okta のすべてのアプリケーションがそれぞれ独自のアプリケーション サインオン ポリシーによって保護されている場合にのみ、 **[非アクティブ]** に設定します。
1. **[MFA の適用]** ポリシーを **[非アクティブ]** に設定します。 また、Azure AD ユーザーを含まない新しいグループにポリシーを割り当てることもできます。

   ![[非アクティブ] のグローバルの MFA サインオン ポリシーを示すスクリーンショット。](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/mfa-policy-inactive.png)

1. アプリケーションレベルのサインオン ポリシー ウィンドウで **[ルールを無効にする]** オプションを選択して、ポリシーを **[非アクティブ]** に更新します。 また、Azure AD ユーザーを含まない新しいグループにポリシーを割り当てることもできます。

1. MFA なしでのアクセスを許可するアプリケーションに対して、有効なアプリケーションレベルのサインオン ポリシーが少なくとも 1 つあることを確認します。

   ![MFA を使用しないアプリケーション アクセスを示すスクリーンショット。](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/application-access-without-mfa.png)

1. Okta のサインオン ポリシーを無効にしたり、移行した Azure AD のユーザーを強制グループから除外したりすると、ユーザーには、次回のサインイン時に条件付きアクセスの入力 "*のみ*" が求められます。

## <a name="next-steps"></a>次のステップ

Okta から Azure AD への移行の詳細については、以下を参照してください。

- [Okta から Azure AD にアプリケーションを移行する](migrate-applications-from-okta-to-azure-active-directory.md)
- [Okta フェデレーションを Azure AD に移行する](migrate-okta-federation-to-azure-active-directory.md)
- [Okta 同期プロビジョニングを Azure AD Connect ベースの同期に移行する](migrate-okta-sync-provisioning-to-azure-active-directory.md)
