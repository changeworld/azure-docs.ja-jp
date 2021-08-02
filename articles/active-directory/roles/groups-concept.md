---
title: クラウド グループを使用して Azure Active Directory でロールの割り当てを管理する | Microsoft Docs
description: ID 管理を委任するためのカスタム Azure AD ロールをプレビューします。 Azure portal、PowerShell、または Graph API で Azure ロールの割り当てを管理します。
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 05/05/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d037f93b1381fcd7f1816104f6fd7b2e1200c852
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2021
ms.locfileid: "111438938"
---
# <a name="use-cloud-groups-to-manage-role-assignments-in-azure-active-directory-preview"></a>クラウド グループを使用して Azure Active Directory でロールの割り当てを管理する (プレビュー)

Azure Active Directory (Azure AD) には、Azure AD の組み込みロールにクラウド グループを割り当てることができるパブリック プレビューが導入されています。 この機能を使用すると、グループを使用して、グローバル管理者と特権ロール管理者の労力を最小限に抑えながら、Azure AD で管理者アクセス権を付与できます。

次の例を考えてみましょう。Contoso は、Azure AD 組織内の従業員のパスワードを管理およびリセットするために、地域を超えて従業員を雇っています。 特権ロール管理者またはグローバル管理者に対して、各ユーザーにヘルプデスク管理者ロールを個別に割り当てるよう依頼する代わりに、Contoso_Helpdesk_Administrators グループを作成し、これをロールに割り当てることができます。 ユーザーがこのグループに参加すると、このロールが間接的に割り当てられます。 その後、既存のガバナンス ワークフローによって、グループのメンバーシップの承認プロセスと監査を行い、正当なユーザーだけがグループのメンバーとなり、ヘルプデスク管理者ロールに割り当てられるようにすることができます。

## <a name="how-this-feature-works"></a>この機能のしくみ

"isAssignableToRole" プロパティを "true" に設定して、新しい Microsoft 365 またはセキュリティ グループを作成します。 このプロパティは、Azure portal でグループを作成するときに、 **[グループに Azure AD ロールを割り当てることができる]** をオンにすることによって有効にすることもできます。 どちらの場合も、ユーザーにロールを割り当てるのと同じ方法で、1 つまたは複数の Azure AD ロールにグループを割り当てることができます。 1 つの Azure AD 組織 (テナント) には、最大 300 個のロール割り当て可能なグループを作成できます。

グループのメンバーにロールへの継続的なアクセスを許可しない場合は、Azure AD Privileged Identity Management を使用できます。 Azure AD ロールの有資格メンバーとしてグループを割り当てます。 その後、グループの各メンバーは、そのグループが割り当てられているロールに対して割り当てをアクティブにすることができます。 その後、一定期間、ロールの割り当てをアクティブ化できます。

> [!Note]
> PIM で Azure AD ロールにグループを割り当てられるようにするには、Privileged Identity Management の更新バージョンが必要です。 Azure AD 組織で Privileged Identity Management API を利用しているため、以前のバージョンの PIM を使用している可能性があります。 エイリアス pim_preview@microsoft.com に連絡して、組織を動かし、API を更新してください。 詳細については、[PIM の Azure AD ロールと機能](../privileged-identity-management/azure-ad-roles-features.md)に関する記事を参照してください。

## <a name="why-we-enforce-creation-of-a-special-group-for-assigning-it-to-a-role"></a>ロールに割り当てるための特別なグループの作成を強制する理由

グループにロールが割り当てられている場合、グループ メンバーシップを管理できるすべての IT 管理者は、そのロールのメンバーシップを間接的に管理することもできます。 たとえば、Contoso_User_Administrators グループにユーザー管理者ロールが割り当てられているとします。 グループ メンバーシップを変更できる Exchange 管理者は、自身を Contoso_User_Administrators グループに追加することで、ユーザー管理者になることができます。 このように、管理者は、あなたが意図しない方法で特権を昇格させることができます。

Azure AD を使用すると、グループに対して isAssignableToRole という新しいプロパティを使用して、ロールに割り当てられたグループを保護することができます。 作成時に isAssignableToRole プロパティが "true" に設定されていたクラウド グループのみをロールに割り当てることができます。 このプロパティは変更できません。このプロパティを "true" に設定してグループを作成した後は、それを変更できません。 既存のグループにこのプロパティを設定することはできません。 潜在的な違反が発生しないようにするために、グループをロールに割り当てる方法が設計されています。

- グローバル管理者と特権ロール管理者だけが、("isAssignableToRole" プロパティを有効にして) ロール割り当て可能なグループを作成できます。
- Azure AD 動的グループにすることはできません。つまり、メンバーシップの種類が "割り当て済み" である必要があります。 動的なグループの自動作成により、不要なアカウントがグループに追加され、そのロールに割り当てられる可能性があります。
- 既定では、ロール割り当て可能なグループのメンバーシップを管理できるのは、グローバル管理者と特権ロール管理者だけですが、グループ所有者を追加して、ロール割り当て可能なグループの管理を委任することができます。
- 特権の昇格を防ぐために、ロール割り当て可能なグループのメンバーと所有者の資格情報を変更したり、MFA をリセットしたりできるのは、特権認証管理者またはグローバル管理者に限られます。
- 入れ子にすることはできません。 グループは、ロール割り当て可能なグループのメンバーとして追加することはできません。

## <a name="limitations"></a>制限事項

次のシナリオは現在サポートされていません。  

- オンプレミスのグループを Azure AD のロール (組み込みまたはカスタム) に割り当てる

## <a name="known-issues"></a>既知の問題

- *Azure AD P2 ライセンス供与されたお客様のみ*:Azure AD と Privileged Identity Management (PIM) の両方を使用して、グループをアクティブとしてロールに割り当てないでください。 具体的に言うと、作成時にはロール割り当て可能グループにロールを割り当てません。"*そして*" PIM を使用して後でそのグループにロールを割り当てます。 これにより、ユーザーが PIM でアクティブなロールの割り当てを確認できなくなる問題や、その PIM 割り当てを削除できなくなる問題が発生します。 このシナリオでは、資格のある割り当ては影響を受けません。 この割り当てを試みると、次のような予期しない動作が発生する可能性があります。
  - ロールの割り当ての終了時刻が正しく表示されない可能性があります。
  - PIM ポータルで、 **[自分のロール]** に、割り当ての付与を行った方法の数 (1 つまたは複数のグループを介して、および直接) に関係なく、1 つのロールの割り当てのみが表示される可能性があります。
- *Azure AD P2 ライセンス供与されたお客様のみ*: グループを削除した後でも、PIM UI でそのロールの有資格メンバーとして表示されます。 機能的には問題ありません。これは、Azure portal のキャッシュの問題にすぎません。  
- グループ メンバーシップによるロールの割り当てには、新しい [Exchange 管理センター](https://admin.exchange.microsoft.com/)を使用してください。 以前の Exchange 管理センターでは、この機能はまだサポートされていません。 Exchange PowerShell コマンドレットは想定どおりに動作します。
- Azure Information Protection ポータル (クラシック ポータル) では、グループを介したロール メンバーシップがまだ認識されません。 [統合型の秘密度ラベル付けプラットフォームに移行](/azure/information-protection/configure-policy-migrate-labels)してから、Office 365 Security & Compliance センターを使用して、グループの割り当てを使用してロールを管理することができます。
- [アプリ管理センター](https://config.office.com/)では、この機能はまだサポートされていません。 ユーザーに直接 Office アプリ管理者ロールを割り当てます。
- [Microsoft 365 コンプライアンス センター](https://compliance.microsoft.com/)では、この機能はまだサポートされていません。 このポータルを使用するには、適切な Azure AD ロールに直接ユーザーを割り当てます。

これらの問題は現在修正中です。

## <a name="license-requirements"></a>ライセンスの要件

この機能を使用するには、Azure AD Premium P1 ライセンスが必要です。 Just-In-Time のロールのアクティブ化に Privileged Identity Management も使用するには、Azure AD Premium P2 ライセンスが必要です。 ご自分の要件に対して適切なライセンスを探すには、[一般公開されている Free および Premium エディションの機能比較](https://azure.microsoft.com/pricing/details/active-directory/)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

- [ロールを割り当て可能なグループを作成する](groups-create-eligible.md)
- [ロール割り当て可能なグループにロールを割り当てる](groups-assign-role.md)
