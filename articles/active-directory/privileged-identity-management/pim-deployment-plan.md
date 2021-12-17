---
title: Privileged Identity Management のデプロイを計画する - Azure AD | Microsoft Docs
description: Azure AD 組織に Privileged Identity Management (PIM) をデプロイする方法について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 14109ab2eb32732d357c46182833b13bdd2c99c7
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129668525"
---
# <a name="plan-a-privileged-identity-management-deployment"></a>Privileged Identity Management のデプロイを計画する

**Privileged Identity Management (PIM)** によって、時間ベースおよび承認ベースのロールのアクティブ化が提供され、重要なリソースに対する過剰、不要、または誤用であるアクセス許可のリスクが軽減されます。 これらのリソースには、Azure Active Directory (Azure AD)、Azure、他の Microsoft Online Services (Microsoft 365 や Microsoft Intune など) 内のリソースが含まれます。

PIM を使用すると、特定のスコープで特定のアクション セットを許可することができます。 組み込まれている主な機能は次のとおりです。

* リソースに対する **Just-In-Time** の特権アクセスを提供する

* 特権アクセス グループの **メンバーシップまたは所有権の資格** を割り当てる

* 開始日と終了日を使用した **期限付き** アクセス権をリソースに割り当てる

* 特権ロールをアクティブ化するために **承認** を要求する

* ロールをアクティブ化するために **多要素認証** を強制する

* なぜユーザーをアクティブ化するのかを把握するために **理由** を使用する

* 特権ロールがアクティブ化されたときに **通知** を受ける

* 継続してユーザーにロールが必要であることを確認するために **アクセス レビュー** を実施する

* 社内監査または外部監査に使用する **監査履歴** をダウンロードする

このデプロイ計画を最大限に活用するには、[Privileged Identity Management が何であるか](pim-configure.md)についてのすべての概要を把握することが重要です。

## <a name="understand-pim"></a>PIM を理解する

このセクションの PIM の概念は、組織の特権 ID の要件を理解するために役立ちます。

### <a name="what-can-you-manage-in-pim"></a>PIM で管理できる内容

現在、PIM を使用できる対象は次のとおりです。

* **Azure AD ロール** – ディレクトリ ロールと呼ばれることもあります。Azure AD ロールには、Azure AD とその他の Microsoft 365 オンライン サービスを管理するための組み込みおよびカスタムのロールが含まれます。

* **Azure ロール** – 管理グループ、サブスクリプション、リソース グループ、リソースに対するアクセスを許可する、Azure 内のロールベースのアクセス制御 (RBAC) のロール。

* **特権アクセス グループ** – Azure AD セキュリティ グループのメンバーおよび所有者ロールに対して Just-In-Time アクセスを設定します。 特権アクセス グループを使用すると、Azure AD ロールと Azure ロール用に PIM を設定する別の手段が得られるだけでなく、Intune、Azure Key Vault、Azure Information Protection などの Microsoft オンライン サービス全体で、他のアクセス許可用に PIM を設定することもできます。 

これらのロールまたはグループには、以下を割り当てることができます。 

* **ユーザー** - Azure AD ロール、Azure ロール、特権アクセス グループに対する Just-In-Time アクセス権を取得します。 

* **グループ** - グループ内の全員が、Azure AD ロールと Azure ロールに対する Just-In-Time アクセス権を取得します。 Azure AD ロールの場合、グループは、ロールに割り当て可能であると示されている、新しく作成されたクラウド グループでなければなりません。それに対し、Azure ロールの場合、グループは、任意の Azure AD セキュリティ グループにすることができます。 特権アクセス グループにグループを割り当てたり入れ子にしたりすることはお勧めしません。 

> [!NOTE] 
>サービス プリンシパルを Azure AD ロール、Azure ロール、特権アクセス グループの有資格者として割り当てることはできませんが、3 つすべてに対する時間限定のアクティブ割り当てを許可することはできます。

### <a name="principle-of-least-privilege"></a>最小限の特権の原則

ユーザーには、[タスクの実行に必要な最小限の特権](../roles/delegate-by-task.md)を持つロールを割り当てます。 このプラクティスではグローバル管理者の数を最小限にし、代わりに特定のシナリオで特定の管理者ロールが使用されます。

> [!NOTE] 
> Microsoft には、グローバル管理者はごくわずかしか存在しません。 詳細については、[Microsoft での Privileged Identity Management の使用方法](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access)に関するページを参照してください。

### <a name="type-of-assignments"></a>割り当ての種類 

割り当てには、**資格** と **アクティブ** の 2 つの種類があります。 あるロールに対して資格があるとは、特権タスクを実行する必要が生じたときに、ユーザーがそのロールをアクティブ化できることを意味します。 

それぞれの割り当ての種類に、開始と終了の時間を設定することもできます。 この追加により、次の 4 種類の割り当てが可能になります。

* 永続的に有資格

* 永続的にアクティブ

* 期間限定で有資格 (割り当ての開始と終了の日付が指定されている)

* 期間限定でアクティブ (割り当ての開始と終了の日付が指定されている)

ロールが期限切れになった場合は、これらの割り当てを **延長** または **更新** することができます。 

永続的なグローバル管理者ロールが必要な、推奨されている [2 つの非常時の緊急アクセス アカウント](../roles/security-emergency-access.md)を除き、ロールに対する永続的にアクティブな割り当てはゼロに維持することを **お勧め** します。 

## <a name="plan-the-project"></a>プロジェクトを計画する

テクノロジ プロジェクトが失敗した場合、その原因は通常、影響、結果、および責任に対する想定の不一致です。 これらの落とし穴を回避するには、[適切な利害関係者が担当していることを確認](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders)し、プロジェクトにおけるその利害関係者の役割がよく理解されていることを確認します。

### <a name="plan-a-pilot"></a>パイロットを計画する

デプロイの各段階で、結果が期待どおりであるか評価します。 「[パイロットのベスト プラクティス](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot)」を参照してください。

* 少数のユーザー (パイロット グループ) から始めて、PIM が想定どおりに動作することを確認します。

* ロールまたは特権アクセス グループに対して設定した構成がすべて正しく機能するかどうかを確認します。 

* 十分にテストした後にのみ、運用環境に進んでください。 

### <a name="plan-communications"></a>連絡を計画する

コミュニケーションは、新しいサービスの成功に必要不可欠です。 ユーザー エクスペリエンスがどのように変わるのか、いつ変わるのか、問題が発生したときにサポートを受ける方法について、ユーザーに事前に連絡します。

内部 IT サポートとの時間を設定し、PIM ワークフローについて説明します。 適切なドキュメントと連絡先情報を提供します。

## <a name="plan-testing-and-rollback"></a>テストとロールバックを計画する

> [!NOTE] 
> Azure AD ロールでは、多くの場合、組織はまずグローバル管理者をテストおよびロールアウトします。それに対し、Azure リソースでは、通常、一度に 1 つの Azure サブスクリプションで PIM をテストします。 

### <a name="plan-testing"></a>テストを計画する

テスト ユーザーを作成して、実際のユーザーに影響が及んでアプリやリソースへのアクセスが中断される前に、PIM の設定が想定どおりに機能するか確認します。 テスト計画を作成して、予想される結果と実際の結果を比較します。 

次の表にテスト ケースの例を示します。 

| Role| アクティブ化中に予期される動作| 実際の結果 |
| --- | --- | --- |
|グローバル管理者| <li> Require MFA (MFA が必須) <br><li>  承認を要求する <br><li>  承認者が通知を受け取り、承認できる <br><li>  ロールの有効期限が事前に設定された時間後に切れる|

Azure AD と Azure リソースのどちらのロールについても、これらのロールを取得するユーザーが示されていることを確認します。 さらに、ステージング環境で PIM をテストするときは、次のロールを考慮してください。

| ロール| Azure AD ロール| Azure リソース ロール| 特権アクセス グループ |
| --- | --- | --- |--- |
| グループのメンバー| | | x |
| ロールのメンバー| x| x|  |
| IT サービス所有者| x| | x |
| サブスクリプションまたはリソース所有者| | x| x |
| 特権アクセス グループ所有者| | | x |

### <a name="plan-rollback"></a>ロールバックを計画する

PIM が運用環境で必要とされているとおりに動作しない場合は、ロールの割り当てをもう一度資格からアクティブに変更することができます。 構成した各ロールについて、割り当ての種類が **資格** であるすべてのユーザーで、省略記号 (...) を選択します。 それから、 **[Make active]\(アクティブにする\)** オプションを選択し、ロールの割り当てを **アクティブ** にし直すことができます。

## <a name="plan-and-implement-pim-for-azure-ad-roles"></a>Azure AD ロール用の PIM を計画して実装する

Azure AD ロールを管理するように PIM を準備するには、これらのタスクに従います。 

### <a name="discover-and-mitigate-privileged-roles"></a>特権ロールを検出して解除する

組織内の特権ロールを持つユーザーのリスト。 割り当て対象のユーザーを確認し、ロールが不要になった管理者を特定して、割り当てから削除します。 

[Azure AD ロールのアクセス レビュー](./pim-create-azure-ad-roles-and-resource-roles-review.md)を使用して、割り当ての検出、レビュー、承認または削除を自動化することができます。

### <a name="determine-roles-to-be-managed-by-pim"></a>PIM によって管理されるロールを決定する

最も多くのアクセス許可を持つ Azure AD ロールの保護を優先します。 組織でどのデータとアクセス許可を最も機密性の高いものと見なすかも重要です。 

まず、すべてのグローバルおよびセキュリティ管理者ロールが PIM を使用して管理されていることを確認します。これらは、侵害された場合に最も大きな損害を生じさせる可能性のあるユーザーであるためです。 次に、攻撃に対して脆弱であると考えられる、管理が必要なその他のロールについて検討します。

### <a name="configure-pim-settings-for-azure-ad-roles"></a>Azure AD ロールの PIM 設定を構成する

組織で使用される特権付きの Azure AD ロールごとに、[PIM 設定を作成して構成](pim-how-to-change-default-settings.md)します。 

次の表は、設定の例を示しています。

| Role| Require MFA (MFA が必須)| Notification| インシデント チケット| 承認を要求する| 承認者| アクティブ化期間| 永続的管理者 |
| --- | --- | --- |--- |--- |--- |--- |--- |
| グローバル管理者| :heavy_check_mark:| :heavy_check_mark:| :heavy_check_mark:| :heavy_check_mark:| その他のグローバル管理者| 1 時間| 緊急アクセス アカウント |
| Exchange 管理者| :heavy_check_mark:| :heavy_check_mark:| :x:| :x:| なし| 2 時間| なし |
| ヘルプデスク管理者| :x:| :x:| :heavy_check_mark:| :x:| なし| 8 時間| なし |


### <a name="assign-and-activate-azure-ad-roles"></a>Azure AD ロールを割り当ててアクティブ化する 

PIM での Azure AD ロールの場合、他の管理者の割り当てを管理できるのは、特権ロール管理者またはグローバル管理者ロールに属しているユーザーだけです。 グローバル管理者、セキュリティ管理者、グローバル閲覧者、セキュリティ閲覧者も、PIM における Azure AD ロールへの割り当てを表示できます。 

以下のリンクの指示に従います。

1. [資格に応じて割り当てる](pim-how-to-add-role-to-user.md)。

2. [資格のあるユーザーが Azure AD ロールを Just-In-Time でアクティブ化できるようにする](pim-how-to-activate-role.md)

ロールが有効期限に近づいたら、[PIM を使用してロールを延長または更新します](pim-resource-roles-renew-extend.md)。 ユーザーが行うこれらの操作には、グローバル管理者または特権ロール管理者からの承認が必要です。

これらの重要なイベントが Azure AD ロールで発生すると、PIM から特権管理者に、ロール、イベント、通知の設定に応じて[電子メール通知と週刊ダイジェスト メールが送信](pim-email-notifications.md)されます。 これらの電子メールには、関連するタスク (ロールのアクティブ化や更新など) へのリンクが含まれる場合もあります。 

> [!NOTE] 
>これらの PIM タスクは、[Azure AD ロールの Microsoft Graph API を使用して](pim-apis.md)実行することもできます。 

### <a name="approve-or-deny-pim-activation-requests"></a>PIM アクティブ化要求を承認または拒否する 

代理承認者は、承認の要求が保留されると電子メール通知を受け取ります。 [Azure リソース ロールをアクティブ化する要求を承認または拒否](pim-resource-roles-approval-workflow.md)するには、こちらの手順に従います。

### <a name="view-audit-history-for-azure-ad-roles"></a>Azure AD ロールに対する監査履歴を表示する

Azure AD ロールに対する過去 30 日間における[すべてのロール割り当てとアクティブ化の監査履歴を表示](pim-how-to-use-audit-log.md)します。 グローバル管理者または特権ロール管理者である場合は、監査ログにアクセスできます。 

1 人以上の管理者が、毎週、すべての監査イベントに目を通し、毎月、監査イベントをエクスポートすることを **お勧めします**。

### <a name="security-alerts-for-azure-ad-roles"></a>Azure AD ロールに対するセキュリティ アラート

疑わしいまたは安全でないアクティビティが発生した場合にアラートをトリガーする、[Azure AD ロールに対するセキュリティ アラートを構成](pim-how-to-configure-security-alerts.md)します。

## <a name="plan-and-implement-pim-for-azure-resource-roles"></a>Azure リソース ロール用の PIM を計画して実装する

Azure リソース ロールを管理するように PIM を準備するには、これらのタスクに従います。

### <a name="discover-and-mitigate-privileged-roles"></a>特権ロールを検出して解除する

各サブスクリプションまたはリソースにアタッチされている所有者およびユーザー アクセス管理者割り当てを最小限にし、不要な割り当てを削除します。

グローバル管理者は、[すべての Azure サブスクリプションを管理するようにアクセス権を昇格](../../role-based-access-control/elevate-access-global-admin.md)できます。 その後、各サブスクリプション所有者を見つけ、協力して、それらのサブスクリプション内の不要な割り当てを削除することができます。

[Azure リソースのアクセス レビュー](./pim-create-azure-ad-roles-and-resource-roles-review.md)を使用して監査し、不要なロール割り当てを削除します。 

### <a name="determine-roles-to-be-managed-by-pim"></a>PIM によって管理されるロールを決定する

Azure リソース用の PIM を使用して管理する必要があるロール割り当てを決定するときには、まず、組織で最も重要な[管理グループ](../../governance/management-groups/overview.md)、サブスクリプション、リソース グループ、リソースを特定する必要があります。 管理グループを使用して、組織内のすべてのリソースを整理することを検討してください。

すべてのサブスクリプション所有者およびユーザー アクセス管理者のロールを、PIM を使用して管理することを **お勧めします**。 

サブスクリプション所有者と協力して、各サブスクリプションによって管理されているリソースをドキュメント化し、侵害された場合の各リソースのリスク レベルを分類します。 リスク レベルに基づいて、PIM でのリソース管理に優先順位を付けます。 これには、サブスクリプションにアタッチされているカスタム リソースも含まれます。

また、重要なサービスのサブスクリプションまたはリソース所有者と協力し、機密のサブスクリプションまたはリソース内のすべてのロールに対して、PIM ワークフローを設定することも **お勧めします**。

重要でないサブスクリプションまたはリソースの場合は、すべてのロールに対して PIM を設定する必要はありません。 しかし、所有者ロールとユーザー アクセス管理者ロールについては、PIM で引き続き保護する必要があります。

### <a name="configure-pim-settings-for-azure-resource-roles"></a>Azure リソース ロールの PIM 設定を構成する

PIM で保護しようとしている Azure リソース ロールの[設定を作成して構成](pim-resource-roles-configure-role-settings.md)します。 

次の表は、設定の例を示しています。

| Role| Require MFA (MFA が必須)| Notification| 承認を要求する| 承認者| アクティブ化期間| アクティブな管理者| アクティブな有効期限| 有資格の有効期限|
| --- | --- | --- |--- |--- |--- |--- |---|---|
| 重要なサブスクリプションの所有者| :heavy_check_mark:| :heavy_check_mark:| :heavy_check_mark:| サブスクリプションのその他の所有者| 1 時間| なし| 該当なし| 3 か月 |
| それほど重要ではないサブスクリプションのユーザー アクセス管理者| :heavy_check_mark:| :heavy_check_mark:| :x:| なし| 1 時間| なし| 該当なし| 3 か月 |

### <a name="assign-and-activate-azure-resource-role"></a>Azure リソース ロールを割り当ててアクティブ化する

PIM での Azure リソース ロールの場合、所有者またはユーザー アクセス管理者だけが、他の管理者の割り当てを管理することができます。 既定では、特権ロール管理者、セキュリティ管理者、セキュリティ閲覧者であるユーザーには、Azure リソース ロールへの割り当てを閲覧するためのアクセス権はありません。

以下のリンクの指示に従います。

1. [資格に応じて割り当てる](pim-resource-roles-assign-roles.md)

2. [資格のあるユーザーが Azure ロールを Just-In-Time でアクティブ化できるようにする](pim-resource-roles-activate-your-roles.md)

特権ロールの割り当てが有効期限に近づいたら、[PIM を使用してロールを延長または更新します](pim-resource-roles-renew-extend.md)。 どちらのユーザー開始アクションにも、リソース所有者またはユーザー アクセス管理者からの承認が必要です。 

Azure リソース ロールでこれらの重要なイベントが発生すると、PIM から所有者とユーザー アクセス管理者に、[電子メール通知](pim-email-notifications.md)が送信されます。 これらの電子メールには、関連するタスク (ロールのアクティブ化や更新など) へのリンクが含まれる場合もあります。

>[!NOTE]
>これらの PIM タスクは、[Azure リソース ロールの Microsoft Azure Resource Manager API を使用して](pim-apis.md)実行することもできます。 

### <a name="approve-or-deny-pim-activation-requests"></a>PIM アクティブ化要求を承認または拒否する

[Azure AD ロールのアクティブ化要求を承認または拒否する](azure-ad-pim-approval-workflow.md) - 代理承認者は、承認の要求が保留されると、電子メール通知を受け取ります。

### <a name="view-audit-history-for-azure-resource-roles"></a>Azure リソース ロールの監査履歴を表示する

Azure リソース ロールに対する過去 30 日間における[すべての割り当てとアクティブ化の監査履歴を表示](azure-pim-resource-rbac.md)します。

### <a name="security-alerts-for-azure-resource-roles"></a>Azure リソース ロールに対するセキュリティ アラート

疑わしいまたは安全でないアクティビティが発生した場合にアラートをトリガーする、[Azure リソース ロールに対するセキュリティ アラートを構成](pim-resource-roles-configure-alerts.md)します。

## <a name="plan-and-implement-pim-for-privileged-access-groups"></a>特権アクセス グループの PIM を計画して実装する

特権アクセス グループを管理するように PIM を準備するには、これらのタスクに従います。

### <a name="discover-privileged-access-groups"></a>特権アクセス グループを検出する

PIM を通じて、個人が Azure AD ロールに対して 5 つまたは 6 つの資格のある割り当てを持っている場合があります。 各ロールを個別にアクティブにする必要があるため、生産性が低下する可能性があります。 さらに悪いことに、多数の Azure リソースが割り当てられている場合もあり、問題が悪化します。

この場合は、 特権アクセス グループを使用する必要があります。 特権アクセス グループを作成して、複数のロールに対する永続的にアクティブなアクセス権を付与します。 [特権アクセス グループの管理機能](groups-features.md)に関するページを参照してください。

Azure AD のロールを、割り当て可能なグループを特権アクセス グループとして管理するには、[これを PIM で管理下に置く](groups-discover-groups.md)必要があります。

### <a name="configure-pim-settings-for-privileged-access-groups"></a>特権アクセス グループの PIM 設定を構成する

PIM で保護しようとしている特権アクセス グループの[設定を作成して構成](groups-role-settings.md)します。

次の表は、設定の例を示しています。

| Role| Require MFA (MFA が必須)| Notification| 承認を要求する| 承認者| アクティブ化期間| アクティブな管理者| アクティブな有効期限| 有資格の有効期限 |
| --- | --- | --- |--- |--- |--- |--- |---|---|
| 所有者| :heavy_check_mark:| :heavy_check_mark:| :heavy_check_mark:| リソースのその他の所有者| 1 時間| なし| 該当なし| 3 か月 |
| メンバー| :heavy_check_mark:| :heavy_check_mark:| :x:| なし| 5 時間| なし| 該当なし| 3 か月 |

### <a name="assign-eligibility-for-privileged-access-groups"></a>特権アクセス グループの資格を割り当てる

[特権アクセス グループのメンバーまたは所有者に資格を割り当てる](groups-assign-member-owner.md)ことができます。 アクティブ化を 1 回行うだけで、リンクされたすべてのリソースにアクセスできます。 

>[!NOTE] 
>ユーザーにロールを割り当てるのと同じ方法で、1 つまたは複数の Azure AD および Azure リソースのロールに特権グループを割り当てることができます。 1 つの Azure AD 組織 (テナント) には、最大 250 個のロール割り当て可能なグループを作成できます。

![特権アクセス グループの資格を割り当てる](media/pim-deployment-plan/privileged-access-groups.png)


特権グループの割り当てが有効期限に近づいたら、[PIM を使用してグループの割り当てを延長または更新します](groups-renew-extend.md)。 グループ所有者からの承認が必要になります。

### <a name="approve-or-deny-pim-activation-request"></a>PIM アクティブ化要求を承認または拒否する

アクティブ化の承認を必要とするように特権アクセス グループのメンバーと所有者を構成し、Azure AD 組織からユーザーまたはグループを代理承認者として選択します。 特権ロール管理者の作業負荷を減らすには、グループごとに 2 人以上の承認者を選択することをお勧めします。 

[特権アクセス グループのロール活動化要求を承認または拒否します](groups-approval-workflow.md)。 代理承認者は、承認の要求が保留されると電子メール通知を受け取ります。

### <a name="view-audit-history-for-privileged-access-groups"></a>特権アクセス グループの監査履歴を表示する

特権アクセス グループに対する過去 30 日間における[すべての割り当てとアクティブ化の監査履歴を表示](groups-audit.md)します。

## <a name="next-steps"></a>次のステップ

* PIM 関連の問題がある場合は、[PIM での問題のトラブルシューティング](pim-troubleshoot.md)に関するページを参照してください。

* [その他の ID 機能をデプロイする](../fundamentals/active-directory-deployment-plans.md)

