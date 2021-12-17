---
title: Azure AD グループを使用してロールの割り当てを管理する - Azure Active Directory
description: Azure AD グループを使用して、Azure Active Directory でのロール割り当ての管理を簡略化します。
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 09/28/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3826c7a2cc50e90b42ff1a4f0047dc5a6ca1cf6
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520733"
---
# <a name="use-azure-ad-groups-to-manage-role-assignments"></a>Azure AD グループを使用してロールの割り当てを管理する

Azure Active Directory (Azure AD) を使用すると、Azure AD グループをロールの割り当ての対象にすることができます。 グループにロールを割り当てることで、グローバル管理者と特権ロール管理者の作業を最小限に抑えて、Azure AD でのロール割り当ての管理を簡略化できます。

## <a name="why-assign-roles-to-groups"></a>ロールをグループに割り当てる理由

次の例を考えてみましょう。Contoso 社では地域を超えて従業員を雇っており、従業員のパスワードの管理およびリセットを Azure AD 組織内で行っています。 特権ロール管理者またはグローバル管理者に対して、各ユーザーにヘルプデスク管理者ロールを個別に割り当てるよう依頼する代わりに、Contoso_Helpdesk_Administrators グループを作成し、このグループにロールを割り当てることができます。 ユーザーがこのグループに参加すると、このロールが間接的に割り当てられます。 その後、既存のガバナンス ワークフローによってグループのメンバーシップの承認プロセスと監査を行い、正当なユーザーだけがグループのメンバーとなっていて、ヘルプデスク管理者ロールを割り当てられていることを確認できます。

## <a name="how-role-assignments-to-groups-work"></a>グループへのロール割り当てのしくみ

グループにロールを割り当てるには、`isAssignableToRole` プロパティが `true` に設定された新しいセキュリティ グループまたは Microsoft 365 グループを作成する必要があります。 Azure portal で、 **[グループに Azure AD ロールを割り当てることができる]** オプションを **[はい]** に設定します。 どちらの場合も、ユーザーにロールを割り当てるのと同じ方法で、1 つまたは複数の Azure AD ロールをグループに割り当てることができます。

![[ロールと管理者] ページのスクリーンショット](./media/groups-concept/role-assignable-group.png)

## <a name="restrictions-for-role-assignable-groups"></a>ロール割り当て可能なグループに関する制限事項

ロール割り当て可能なグループには、以下の制限があります。

- 新しいグループには、`isAssignableToRole` プロパティまたは **[グループに Azure AD ロールを割り当てることができる]** オプションのみを設定できます。
- `isAssignableToRole` プロパティは **変更できません**。 このプロパティを設定してグループを作成した後に、これを変更することはできません。
- 既存のグループを、ロール割り当て可能なグループにすることはできません。
- 1 つの Azure AD 組織 (テナント) には、最大 400 個のロール割り当て可能なグループを作成できます。

## <a name="how-are-role-assignable-groups-protected"></a>ロール割り当て可能グループはどのように保護されているか

グループにロールが割り当てられている場合、グループ メンバーシップを管理できるすべての IT 管理者も、そのロールのメンバーシップを間接的に管理できます。 たとえば、Contoso_User_Administrators グループにユーザー管理者ロールが割り当てられているとします。 グループ メンバーシップを変更できる Exchange 管理者は、自身を Contoso_User_Administrators グループに追加することで、ユーザー管理者になることができます。 このように、管理者は意図していない方法で特権を昇格させることができます。

作成時に `isAssignableToRole` プロパティが `true` に設定されているグループに対してのみ、ロールを割り当てることができます。 このプロパティは変更できません。 このプロパティを設定してグループを作成した後に、これを変更することはできません。 既存のグループにこのプロパティを設定することはできません。

ロール割り当て可能なグループには以下の制限があり、潜在的な侵害を防げるように設計されています。

- グローバル管理者と特権ロール管理者だけが、ロール割り当て可能なグループを作成できます。
- ロール割り当て可能なグループのメンバーシップの種類を [割り当て済み] とする必要があり、Azure AD 動的グループとすることはできません。 動的なグループの自動作成により、不要なアカウントがグループに追加され、そのロールに割り当てられる可能性があります。
- 既定では、ロール割り当て可能なグループのメンバーシップを管理できるのは、グローバル管理者と特権ロール管理者だけですが、グループ所有者を追加して、ロール割り当て可能なグループの管理を委任することができます。
- このようなグループのメンバーシップを管理できるようにするには、RoleManagement.ReadWrite.All の Microsoft Graph アクセス許可が必要です。Group.ReadWrite.All は機能しません。
- 特権の昇格を防ぐために、ロール割り当て可能なグループのメンバーと所有者の資格情報を変更したり、MFA をリセットしたりできるのは、特権認証管理者またはグローバル管理者に限られます。
- グループの入れ子化はサポートされていません。 グループは、ロール割り当て可能なグループのメンバーとして追加することはできません。

## <a name="use-pim-to-make-a-group-eligible-for-a-role-assignment"></a>PIM を使用して、グループをロール割り当ての対象にする

グループのメンバーにロールへの継続的なアクセスを許可したくない場合は、[Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) を使用して、グループをロール割り当ての対象にすることができます。 その後、グループの各メンバーは定められた期間、ロール割り当てのアクティブ化の対象となります。

> [!NOTE]
> Azure AD ロールへの昇格に使用される特権アクセス グループについては、対象となるメンバーの割り当てに承認プロセスを求めることをお勧めします。 承認なしで有効化できる割り当てでは、より低いレベルのアクセス許可を持つ管理者によるセキュリティ リスクが発生する場合があります。 たとえば、ヘルプデスク管理者には、対象となるユーザーのパスワードをリセットするアクセス許可があります。

## <a name="scenarios-not-supported"></a>サポートされていないシナリオ

次のシナリオはサポートされていません。  

- Azure AD のロール (組み込みまたはカスタム) をオンプレミスのグループに割り当てる。

## <a name="known-issues"></a>既知の問題

ロール割り当て可能なグループに関する既知の問題を以下に示します。

- *Azure AD P2 ライセンス供与されたお客様のみ*: グループを削除した後でも、PIM UI でそのロールの有資格メンバーとして表示されます。 機能的には問題ありません。これは、Azure portal のキャッシュの問題にすぎません。  
- グループ メンバーシップによるロールの割り当てには、新しい [Exchange 管理センター](https://admin.exchange.microsoft.com/)を使用してください。 以前の Exchange 管理センターでは、この機能はまだサポートされていません。 Exchange PowerShell コマンドレットは想定どおりに動作します。
- Azure Information Protection ポータル (クラシック ポータル) では、グループを介したロール メンバーシップがまだ認識されません。 [統合型の秘密度ラベル付けプラットフォームに移行](/azure/information-protection/configure-policy-migrate-labels)してから、Office 365 Security & Compliance センターを使用して、グループの割り当てを使用してロールを管理することができます。
- [アプリ管理センター](https://config.office.com/)では、この機能はまだサポートされていません。 ユーザーに直接 Office アプリ管理者ロールを割り当てます。

## <a name="license-requirements"></a>ライセンスの要件

この機能を使用するには、Azure AD Premium P1 ライセンスが必要です。 Just-In-Time のロールのアクティブ化に Privileged Identity Management も使用するには、Azure AD Premium P2 ライセンスが必要です。 ご自分の要件に対して適切なライセンスを探すには、[一般公開されている Free および Premium エディションの機能比較](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

- [ロールを割り当て可能なグループを作成する](groups-create-eligible.md)
- [Azure AD ロールをグループに割り当てる](groups-assign-role.md)
