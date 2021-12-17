---
title: Azure AD B2C ローカル アカウント ID プロバイダーを設定する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C テナントでサインアップまたはサインインに使用できる ID の種類 (メール アドレス、ユーザー名、電話番号) を定義します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f5f72fa4dc126074177b96a484c10ade06724f83
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128574445"
---
# <a name="set-up-the-local-account-identity-provider"></a>ローカル アカウント ID プロバイダーを設定する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

この記事では、Azure AD B2C ローカル アカウントのサインイン方法を決定する方法について説明します。 ローカル アカウントとは、ユーザーがアプリケーションにサインアップするとき、または管理者がアカウントを作成するときに、Azure AD B2C ディレクトリに作成されるアカウントを指します。 ユーザー名とパスワードはローカルに格納され、Azure AD B2C はローカル アカウントの ID プロバイダーとして機能します。

ローカル アカウントでは、次に示すいくつかのサインイン方法を使用できます。

- **メール アドレス**: ユーザーは自分のメール アドレスとパスワードを使用してアプリにサインアップおよびサインインできます。 ローカル アカウント ID プロバイダーの設定では、メール アドレスでのサインアップが既定で有効になっています。
- **ユーザー名**: ユーザーはユーザー名とパスワードを使用してサインアップおよびサインインできます。
- **電話 ("パスワードレス認証")** : ユーザーはプライマリ サインイン ID として電話番号を使用してアプリにサインアップおよびサインインできます。 パスワードを作成する必要はありません。 ワンタイム パスワードが SMS テキスト メッセージを介してユーザーに送信されます。
- **電話またはメール アドレス**: ユーザーは電話番号またはメール アドレスを入力してサインアップまたはサインインできます。 ユーザー入力に基づいて、Azure AD B2C によりサインアップまたはサインインのページの対応するフローにユーザーが移動されます。
- **電話での回復**: 電話でのサインアップまたはサインインを有効にした場合は、電話での回復により、ユーザーが自分の電話を持っていないときにアカウントを回復するために使用できるメール アドレスを指定できます。

これらの方法の詳細については、[サインイン オプション](sign-in-options.md)に関する記事を参照してください。 

ユーザーの ID が Facebook や Google などのフェデレーション ID プロバイダーによって管理されるソーシャル ID またはエンタープライズ ID の設定を行うには、[ID プロバイダーの追加](add-identity-provider.md)に関する記事を参照してください。

::: zone pivot="b2c-user-flow"

## <a name="configure-local-account-identity-provider-settings"></a>ローカル アカウント ID プロバイダーの設定を構成する


Azure AD B2C の **ID プロバイダー** のリストで **ローカル アカウント** プロバイダーを構成することにより、テナントで利用できるローカル アカウントのサインイン方法 (メール アドレス、ユーザー名、電話番号) を選択できます。 続いて、ユーザー フローの設定時に、テナント全体で有効にしたローカル アカウントのサインイン方法の 1 つを選択できます。 ユーザー フローに対して選択できるローカル アカウントのサインイン方法は 1 つだけですが、ユーザー フローごとに異なるオプションを選択できます。

ローカル アカウントのサインイン オプションをテナント レベルで設定するには、次の手順を実行します。 

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. **[Azure サービス]** で、 **[Azure AD B2C]** を選択します。 または、検索ボックスを使用して検索し、 **[Azure AD B2C]** を選択します。
1. **[管理]** で、 **[ID プロバイダー]** を選択します。
1. ID プロバイダー リストで、 **[ローカル アカウント]** を選択します。
1. **[ローカル IdP を構成します]** ページで、Azure AD B2C テナント内のユーザー フローに対して有効にする 1 つ以上の ID の種類を選択します。 ここでオプションを選択すると、そのオプションをテナント全体で使用できるようになります。ユーザー フローを作成または変更する場合は、ここで有効にするオプションから選択できます。

   - **電話**: ユーザーは電話番号の入力を求められます。それがサインアップ時に検証され、ユーザー ID になります。
   - **ユーザー名**: ユーザーは独自の一意のユーザー ID を作成できます。 ユーザーからメール アドレスが収集され、検証されます。
   - **メール アドレス**: ユーザーはメール アドレスの入力を求められます。それがサインアップ時に検証され、ユーザー ID になります。
1. **[保存]** を選択します。

## <a name="configure-your-user-flow"></a>ユーザー フローを構成する

1. Azure portal の左側のメニューで、 **[Azure AD B2C]** を選択します。
1. **[ポリシー]** で **[ユーザー フロー (ポリシー)]** を選択します。
1. サインアップとサインインの操作を構成するユーザー フローを選択します。
1. **[ID プロバイダー]** を選択します。
1. **[ローカル アカウント]** で、次のいずれかを選択します。 **[電子メールのサインアップ]** 、 **[User ID signup]\(ユーザー ID でのサインアップ\)** 、 **[Phone signup]\(電話でのサインアップ\)** 、 **[Phone/Email signup]\(電話/電子メールでのサインアップ\)** 、または **[なし]** 。

### <a name="enable-the-recovery-email-prompt"></a>回復用メール アドレスのプロンプトを有効にする

**[Phone signup]\(電話でのサインアップ\)** 、 **[Phone/Email signup]\(電話/電子メールでのサインアップ\)** オプションを選択した場合、回復用メール アドレスのプロンプトを有効にします。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページの **[ディレクトリ名]** の一覧で自分の Azure AD B2C ディレクトリを見つけて、 **[切り替え]** を選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. Azure AD B2C の **[ポリシー]** で、 **[ユーザー フロー]** を選択します。
1. 一覧からユーザー フローを選択します。
1. **[設定]** で **[プロパティ]** を選択します。
1. **[Enable recovery email prompt for phone number signup and sign in (preview)]\(電話番号でのサインアップとサインインの回復用メール プロンプトを有効にする (プレビュー)\)** の横で、次のように選択します。
   - **[オン]** : サインアップとサインインの両方で回復用メール プロンプトを表示する。
   - **[オフ]** : 回復用メール プロンプトを非表示にする。
1. **[保存]** を選択します。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="get-the-starter-pack"></a>スターター パックを取得する

カスタム ポリシーは、ユーザー体験を定義するために Azure AD B2C テナントにアップロードされる XML ファイルのセットです。 事前に作成されたポリシーがいくつか含まれるスターター パックが用意されています。 関連するスターター パックをダウンロードします。 

- [メール アドレスでのサインイン](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts)
- [ユーザー名でのサインイン](https://github.com/azure-ad-b2c/samples/tree/master/policies/username-signup-or-signin)
- [電話でのサインイン](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless)。 [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhone.xml) 証明書利用者ポリシーを選択します。 
- [電話またはメール アドレスでのサインイン](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless)。 [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhoneOrEmail.xml) 証明書利用者ポリシーを選択します。

スターター パックをダウンロードした後:

1. 各ファイル内で、文字列 `yourtenant` を、使用している Azure AD B2C テナントの名前に置き換えます。 たとえば、B2C テナントの名前が *contosob2c* であれば、`yourtenant.onmicrosoft.com` のすべてのインスタンスは `contosob2c.onmicrosoft.com` になります。

1. 「[Azure Active Directory B2C でのカスタム ポリシーの概要](tutorial-create-user-flows.md?pivots=b2c-custom-policy)」の「[カスタム ポリシーにアプリケーション ID を追加します](tutorial-create-user-flows.md?pivots=b2c-custom-policy#add-application-ids-to-the-custom-policy)」の手順を完了します。 たとえば、前提条件を満たす際に登録した 2 つのアプリケーション (*IdentityExperienceFramework* と *ProxyIdentityExperienceFramework*) の **アプリケーション (クライアント) ID** を使用して、`/phone-number-passwordless/`**`Phone_Email_Base.xml`** を更新します。
1. ポリシー ファイルをアップロードする

::: zone-end

## <a name="next-steps"></a>次のステップ

- [外部 ID プロバイダーを追加する](add-identity-provider.md)
- [ユーザー フローを作成する](tutorial-create-user-flows.md)
