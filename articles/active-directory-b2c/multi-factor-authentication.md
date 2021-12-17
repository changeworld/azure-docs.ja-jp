---
title: Azure Active Directory B2C の Multi-Factor Authentication | Microsoft Docs
description: Azure Active Directory B2C によってセキュリティ保護されているコンシューマー向けアプリケーションで Multi-factor Authentication を有効にする方法。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 97d11b195f263b5d2bec1a729d92d72a3ed90812
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128592814"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Azure Active Directory B2C の多要素認証 | Microsoft Docs

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) は [Azure AD Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md) と直接統合されているため、ご自分のアプリケーションのサインアップおよびサインイン エクスペリエンスに第 2 のセキュリティ層を簡単に追加できます。 コードを 1 行も記述する必要なく多要素認証を有効にできます。 サインアップおよびサインイン ユーザー フローを既に作成していても、多要素認証を有効にできます。

この機能を利用すると、次のようなシナリオをアプリケーションで処理するのに役立ちます。

- 多要素認証が、あるアプリケーションのアクセスには必要ないが、別のアプリケーションのアクセスには必要な場合。 たとえば、顧客が自動車保険アプリケーションにサインインするにはソーシャルまたはローカル アカウントを使用できますが、同じディレクトリに登録されている住宅保険アプリケーションにアクセスするには事前に電話番号を確認する必要があるような場合です。
- 通常のアプリケーションへのアクセスには多要素認証は必要ないが、アプリケーション内の機密性が高い部分へのアクセスにはそれが必要である場合。 たとえば、顧客が銀行取引アプリケーションにサインインし、口座の残高を確認するにはソーシャルまたはローカル アカウントを使用できますが、ネット送金を行うには事前に電話番号の確認が必要になるような場合です。

### <a name="verification-methods"></a>検証方法

[条件付きアクセス](conditional-access-identity-protection-overview.md)を使用して、ユーザーは、管理者が行う構成上の決定に基づいて、MFA で認証が行われる場合と行われない場合があります。 多要素認証の方法は次のとおりです。

- Email
- SMS
- 電話

## <a name="set-multi-factor-authentication"></a>多要素認証の設定

::: zone pivot="b2c-user-flow"

1. [Azure ポータル](https://portal.azure.com)
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[ユーザー フロー]** を選択します。
1. MFA を有効にするユーザー フローを選択します。 たとえば、*B2C_1_signinsignup* です。
1. **[プロパティ]** を選択します。
1. **[多要素認証]** セクションで、目的の **[方法の種類]** を選択します。 次に、 **[MFA の適用]** でオプションを選択します。

   - **[オフ]** - サインイン時に MFA が適用されることはありません。ユーザーは、サインアップ時またはサインイン時に MFA への登録を求められません。
   - **[常にオン]** - 条件付きアクセスの設定に関係なく、MFA が常に必須になります。 サインアップ時に、ユーザーは MFA に登録するよう求められます。 サインインの間に、ユーザーが MFA にまだ登録されていない場合は、登録するよう求められます。
   - **[Conditional]\(条件付き\)** - サインアップとサインインの間に、ユーザーは MFA への登録を求められます (新規ユーザーと、MFA に未登録の既存ユーザーの両方)。 サインイン時には、アクティブな条件付きアクセス ポリシーの評価で要求されている場合にのみ、MFA が適用されます。

    - 結果がリスクを伴わない MFA チャレンジである場合は、MFA が適用されます。 ユーザーが MFA にまだ登録されていない場合は、登録するよう求められます。
    - 結果がリスクによる MFA チャレンジであり、"*なおかつ*" ユーザーが MFA に登録されていない場合は、サインインがブロックされます。

   > [!NOTE]
   >
   > - Azure AD B2C での条件付きアクセスの一般提供では、サインアップの間にユーザーは MFA の方法での登録を求められるようになります。 一般提供の前に作成したサインアップ ユーザー フローには、この新しい動作は自動的には反映されませんが、新しいユーザー フローを作成することで動作を組み込むことができます。
   > - **[条件付き]** を選択した場合、[条件付きアクセスをユーザー フローに追加](conditional-access-user-flow.md)し、ポリシーを適用するアプリを指定する必要もあります。
   > - サインアップのユーザー フローでは、多要素認証 (MFA) は既定で無効になっています。 電話でのサインアップを使用したユーザー フローで MFA を有効にすることはできますが、電話番号がプライマリ ID として使用されるため、2 番目の認証要素に使用できる唯一のオプションは電子メールによるワンタイム パスコードです。

1. **[保存]** を選択します。 このユーザー フローに対して MFA が有効になります。

エクスペリエンスを検証するには、**[ユーザー フローを実行します]** を使用します。 次のシナリオで確認してみましょう。

多要素認証の手順が実行される前に、顧客のアカウントがテナントに作成されます。 この手順の過程で、顧客は自分の電話番号を入力し、確認することを求められます。 検証が成功した場合、後で使用できるように電話番号がアカウントに関連付けられます。 顧客がキャンセルまたは中止した場合であっても、多要素認証が有効になっている場合、次にサインインするときに顧客は電話番号の確認を再度求められる場合があります。

::: zone-end

::: zone pivot="b2c-custom-policy"

多要素認証を有効にするには、GitHub からカスタム ポリシー スターター パックを取得し、**SocialAndLocalAccountsWithMFA** スターター パック内の XML ファイルを Azure AD B2C テナント名で更新します。 **SocialAndLocalAccountsWithMFA** を使用すると、ソーシャル、ローカル、および多要素認証の各オプションを有効にできます。 詳細については、「[Active Directory B2C でのカスタム ポリシーの概要](tutorial-create-user-flows.md?pivots=b2c-custom-policy)」を参照してください。 

::: zone-end
