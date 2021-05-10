---
title: Azure AD ロールのベスト プラクティス - Azure Active Directory
description: Azure Active Directory ロールを使用するためのベスト プラクティス。
services: active-directory
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: conceptual
ms.date: 03/28/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78f4642b8f9f1ede65766a0026940c0af0f01ec2
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111087"
---
# <a name="best-practices-for-azure-ad-roles"></a>Azure AD ロールのベスト プラクティス

この記事では、Azure Active Directory のロールベースのアクセス制御 (Azure AD RBAC) を使用するためのベスト プラクティスの一部について説明します。 これらのベスト プラクティスは、Azure AD RBAC に関して Microsoft が蓄積してきたノウハウと、ユーザーの皆様の経験に基づいています。 「[Azure AD でのハイブリッドおよびクラウド デプロイ用の特権アクセスをセキュリティで保護する](security-planning.md)」にある詳細なセキュリティ ガイダンスも参照することをお勧めします。

## <a name="1-manage-to-least-privilege"></a>1. 最小特権を管理する

アクセスの制御戦略を計画するときは、最小特権を管理することをお勧めします。 最小特権とは、ジョブを実行するために必要な特権を管理者に正確に付与することを意味します。 管理者にロールを割り当てる際に考慮すべき 3 つのアスペクトがあります。特定の期間、特定のスコープに対する、特定の権限セットです。 当初、より広範なスコープでより広範なロールを割り当てる方が便利に思われたとしても、避けてください。 ロールとスコープを制限することで、セキュリティ プリンシパルが侵害された場合にリスクにさらされるリソースを制限することができます。 Azure AD RBAC では、65 を超える[組み込みロールが](permissions-reference.md)サポートされています。 Azure AD には、ユーザー、グループ、アプリケーションなどのディレクトリ オブジェクトを管理したり、Exchange、SharePoint、Intune などの Microsoft 365 サービスを管理したりするためのロールがあります。 Azure AD の組み込みロールについて理解を深めるには、「[Azure Active Directory のロールについて](concept-understand-roles.md)」を参照してください。 自分のニーズに合った組み込みロールがない場合は、独自の[カスタム ロール](custom-create.md)を作成することができます。  
 
### <a name="finding-the-right-roles"></a>適切なロールを見つける

適切なロールを見つけるには、次の手順に従います。

1. Azure portal で、[[ロールと管理者]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) を開いて Azure AD ロールの一覧を表示します。

1. **[サービス]** フィルターを使用して、ロールの一覧を絞り込みます。

    ![[サービス] フィルターが開いた状態の Azure AD の [ロールと管理者] ページ](./media/best-practices/roles-administrators.png)

1. [Azure AD の組み込みロール](permissions-reference.md)に関するドキュメントを参照してください。 各ロールに関連付けられている権限は、読みやすくするためにまとめて表示されます。 ロールの権限の構造と意味を理解するには、「[ロールの権限を理解する方法](permissions-reference.md#how-to-understand-role-permissions)」を参照してください。

1. 「[タスク別の最小特権のロール](delegate-by-task.md)」のドキュメントを参照してください。

## <a name="2-use-privileged-identity-management-to-grant-just-in-time-access"></a>2. Privileged Identity Management を使用して Just-In-Time アクセスを付与する

最小特権の原則の 1 つは、特定の期間だけアクセス許可を付与することです。 [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) を使用すると、管理者に Just-In-Time アクセスを付与することができます。 Azure AD で PIM を有効にすることをお勧めします。 PIM を使用すると、ユーザーを Azure AD ロールの対象メンバーにすることができます。 その後、使用する必要があるたびに、限られた時間枠でロールをアクティブにできます。 期間が経過すると、特権アクセスは自動的に削除されます。 [PIM 設定を構成](../privileged-identity-management/pim-how-to-change-default-settings.md)して、承認を要求したり、誰かがロールの割り当てをアクティブにしたときに通知メールを受信したりすることもできます。 通知により、高度な特権ロールに新しいユーザーが追加された場合にアラートを受け取れます。 

## <a name="3-turn-on-multi-factor-authentication-for-all-your-administrator-accounts"></a>3. すべての管理者アカウントに対して多要素認証を有効にする

[私たちの研究によると](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)、多要素認証 (MFA) を使用した場合、アカウントが侵害されるリスクは 99.9% 低下します。 
 
次の 2 つの方法を使用して、Azure AD ロールで MFA を有効にすることができます。
- Privileged Identity Management での[ロールの設定](../privileged-identity-management/pim-how-to-change-default-settings.md)
- [条件付きアクセス](../conditional-access/howto-conditional-access-policy-admin-mfa.md)

## <a name="4-configure-recurring-access-reviews-to-revoke-unneeded-permissions-over-time"></a>4. 定期的なアクセス レビューを構成して、時間の経過に伴う不要なアクセス許可を取り消す

アクセス レビューを使用すると、組織は管理者のアクセス権を定期的に確認して、適切なユーザーのみが継続的なアクセス権を持つようにすることができます。 通常の監査は、次の理由により、管理者が非常に重要です。
- 悪意のあるアクターによってアカウントが侵害される可能性があります。
- 企業内でチームが移動します。 監査がない場合、時間の経過と共に不要なアクセスが行われる可能性があります。
 
ロールのアクセス レビューの詳細については、「[PIM で Azure AD ロールのアクセス レビューを作成する](../privileged-identity-management/pim-how-to-start-security-review.md)」を参照してください。 ロールが割り当てられているグループのアクセス レビューの詳細については、「[グループとアプリケーションのアクセス レビューを Azure AD アクセス レビューで作成する](../governance/create-access-review.md)」を参照してください。

## <a name="5-limit-the-number-of-global-administrators-to-less-than-5"></a>5. グローバル管理者の数を 5 人未満に制限する

ベスト プラクティスとして、組織内でグローバル管理者ロールを割り当てるユーザーは **5 人未満** にすることをお勧めします。 全体管理者は領域のキーを保持しており、攻撃対象領域を低く抑えることが最大の目的です。 前述のように、これらのアカウントはすべて多要素認証で保護する必要があります。

既定では、ユーザーが Microsoft のクラウド サービスに新規登録すると、Azure AD テナントが作成され、そのユーザーはグローバル管理者ロールのメンバーになります。 グローバル管理者ロールに割り当てられているユーザーは、Azure AD 組織内のすべての管理設定の読み取りと変更を行うことができます。 いくつかの例外を除き、グローバル管理者は Microsoft 365 組織内のすべての構成設定を読み取り、変更することもできます。 グローバル管理者は、アクセス権を昇格させてデータを読み取ることもできます。

Microsoft は、グローバル管理者ロールに永続的に割り当てられる、緊急用アカウントを 2 つ保持することをお勧めします。 「[Azure AD で緊急アクセス用管理者アカウントを管理する](../roles/security-emergency-access.md)」で説明されているように、これらのアカウントが、サインインする通常の管理者アカウントと同じ多要素認証メカニズムを必要としないようにしてください。 

## <a name="6-use-groups-for-azure-ad-role-assignments-and-delegate-the-role-assignment"></a>6. Azure AD ロールの割り当てにグループを使用し、ロールの割り当てを委任する

グループを利用する外部ガバナンス システムがある場合は、個々のユーザーではなく、Azure AD グループにロールを割り当てることを検討してください。 また、PIM でロール割り当て可能なグループを管理して、これらの特権グループに継続的な所有者またはメンバーがいないことを確認することもできます。 詳細については、「[特権アクセス Azure AD グループの管理機能](../privileged-identity-management/groups-features.md)」を参照してください。

ロールを割り当て可能なグループに所有者を割り当てることができます。 その所有者は、グループに対して追加または削除されるユーザーを決定し、間接的には、ロールの割り当てを取得するユーザーを決定します。 このようにして、全体管理者または特権ロール管理者は、グループを使用して役割ごとにロール管理を委任できます。 詳細については、「[クラウド グループを使用して Azure Active Directory でロールの割り当てを管理する](groups-concept.md)」を参照してください。

## <a name="7-activate-multiple-roles-at-once-using-privileged-access-groups"></a>7. 特権アクセス グループを使用して一度に複数のロールをアクティブにする

PIM を通じて、個人が Azure AD ロールに対して 5 つまたは 6 つの資格のある割り当てを持っている場合があります。 各ロールを個別にアクティブにする必要があるため、生産性が低下する可能性があります。 さらに悪いことに、多数の Azure リソースが割り当てられている場合もあり、問題が悪化します。
 
この場合は、 [特権アクセス グループ](../privileged-identity-management/groups-features.md)を使用する必要があります。 特権アクセス グループを作成し、複数のロール (Azure AD または Azure) に永続的にアクセスできるようにします。 このユーザーを、このグループの有資格メンバーまたは所有者にします。 アクティブ化を 1 回行うだけで、リンクされたすべてのリソースにアクセスできます。

![同時に複数のロールをアクティブ化する特権アクセス グループ ダイアグラム](./media/best-practices/privileged-access-group.png)

## <a name="8-use-cloud-native-accounts-for-azure-ad-roles"></a>8. Azure AD ロールにクラウド ネイティブ アカウントを使用する

Azure AD ロールの割り当てには、オンプレミスの同期されたアカウントを使用しないでください。 オンプレミスのアカウントが侵害された場合、Azure AD リソースも危険にさらされる可能性があります。

## <a name="next-steps"></a>次のステップ

- [Azure AD でのハイブリッドおよびクラウド デプロイ用の特権アクセスをセキュリティで保護する](security-planning.md)