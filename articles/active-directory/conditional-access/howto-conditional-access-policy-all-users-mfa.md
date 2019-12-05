---
title: 条件付きアクセス - すべてのユーザーに対して MFA を必須にする - Azure Active Directory
description: カスタムの条件付きアクセス ポリシーを作成して、すべてのユーザーに対して多要素認証の実行を必須にします
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc0d22e2e6478c265ba9219ae4df5d5ddb34d481
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74803888"
---
# <a name="conditional-access-require-mfa-for-all-users"></a>条件付きアクセス:すべてのユーザーに対して MFA を必須にする

Microsoft の ID セキュリティ責任者、Alex Weinert は彼のブログ投稿「[Your Pa$$word doesn't matter](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)」で次のように言っています。

> 「重要なのはパスワードではなく、MFA です!」 私たちの研究によると、MFA を使用すると、アカウントは 99.9% 以上侵害されにくくなります。

この記事のガイダンスは、組織が環境に適したバランスの MFA ポリシーを作成する際に役立ちます。

## <a name="user-exclusions"></a>ユーザーの除外

条件付きアクセス ポリシーは強力なツールであり、以下のアカウントはポリシーから除外することをお勧めします。

* **緊急アクセス用**アカウントまたは**非常用**アカウント。テナント全体でアカウントがロックアウトされるのを防ぎます。 発生する可能性は低いシナリオですが、すべての管理者がテナントからロックアウトされた場合に、ご自身の緊急アクセス用管理アカウントを使用してテナントにログインし、アクセスを復旧する手順を実行できます。
   * 詳細については、「[Azure AD で緊急アクセス用アカウントを管理する](../users-groups-roles/directory-emergency-access.md)」を参照してください。
* **サービス アカウント**と**サービス プリンシパル** (Azure AD Connect 同期アカウントなど)。 サービス アカウントは、特定のユーザーに関連付けられていない非対話型のアカウントです。 通常、バックエンド サービスで使用され、アプリケーションへのプログラムによるアクセスが許可されます。 プログラムでは MFA を完了できないため、サービス アカウントは対象外とする必要があります。
   * 組織のスクリプトまたはコードでこれらのアカウントが使用されている場合は、それを[マネージド ID](../managed-identities-azure-resources/overview.md) に置き換えることを検討してください。 これらの特定のアカウントは、一時的な回避策として、ベースライン ポリシーの対象外にすることができます。

## <a name="application-exclusions"></a>アプリケーションの除外

組織には、使用中のクラウド アプリケーションが多数あります。 これらのアプリケーションすべてで、同等のセキュリティが必要なわけではありません。 たとえば、給与と勤怠のアプリケーションには MFA が必要でも、カフェのアプリケーションでは必要ない可能性があります。 管理者は、ポリシーから特定のアプリケーションを除外することを選択できます。

## <a name="create-a-conditional-access-policy"></a>条件付きアクセス ポリシーを作成する

次の手順では、割り当てられた管理者ロールに対して、多要素認証の実行を必須にする条件付きアクセス ポリシーを作成します。

1. **Azure portal** にグローバル管理者、セキュリティ管理者、または条件付きアクセス管理者としてサインインします。
1. **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[条件付きアクセス]** の順に移動します。
1. **[新しいポリシー]** を選択します。
1. ポリシーに名前を付けます。 ポリシーの名前に対する意味のある標準を組織で作成することをお勧めします。
1. **[割り当て]** で、 **[ユーザーとグループ]** を選択します。
   1. **[Include]\(含める\)** で、 **[すべてのユーザー]** を選択します。
   1. **[除外]** で、 **[ユーザーとグループ]** を選択し、組織の緊急アクセス用または非常用アカウントを選択します。 
   1. **[完了]** を選択します。
1. **[Cloud apps or actions]\(クラウド アプリまたはアクション\)**  >  **[Include]\(含める\)** で、 **[すべてのクラウド アプリ]** を選択します。
   1. **[除外]** で、多要素認証を必要としないアプリケーションを選択します。
1. **[アクセス制御]**  >  **[許可]** で、 **[アクセス権の付与]** 、 **[Require multi-factor authentication]\(多要素認証を要求する\)** の順に選択し、 **[Select]\(選択する\)** を選択します。
1. 設定を確認し、 **[Enable policy]\(ポリシーの有効化\)** を **[オン]** に設定します。
1. **[作成]** を選択して、ポリシーを作成および有効化します。

## <a name="next-steps"></a>次の手順

[Conditional Access common policies](concept-conditional-access-policy-common.md) (条件付きアクセスの一般的なポリシー)

[Simulate sign in behavior using the Conditional Access What If tool](troubleshoot-conditional-access-what-if.md) (条件付きアクセスの What If ツールを使用したサインイン動作のシミュレート)
