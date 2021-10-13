---
title: PIM で Azure リソース ロールと Azure AD ロールのアクセス レビューを作成する - Azure Active Directory | Microsoft Docs
description: Azure Active Directory Privileged Identity Management (PIM) で Azure リソースと Azure AD ロールに対するアクセス レビューを作成する方法を説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c44c27c0d99c67c4102167f924a1dcb6f87e871
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669703"
---
# <a name="create-an-access-review-of-azure-resource-and-azure-ad-roles-in-pim"></a>PIM で Azure リソース ロールと Azure AD ロールのアクセス レビューを作成する

従業員による特権 Azure リソース ロールと Azure AD ロールへのアクセスの必要性は、時間の経過に伴って変化します。 古くなったロールの割り当てに関連するリスクを軽減するために、アクセスを定期的に確認する必要があります。 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) を使用して、Azure リソース ロールと Azure AD ロールへの特権アクセスのアクセス レビューを作成できます。 自動的に実行される定期的なアクセス レビューを構成することもできます。 この記事では、1 つ以上のアクセス レビューを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]PIM のライセンスの詳細は「[Privileged Identity Management を使用するためのライセンスの要件](subscription-requirements.md)」をご覧ください。

 Azure リソースのアクセス レビューを作成するには、Azure リソースのロールの[所有者](../../role-based-access-control/built-in-roles.md#owner)または[ユーザー アクセス管理者](../../role-based-access-control/built-in-roles.md#user-access-administrator)に割り当てられている必要があります。 Azure AD ロールのアクセス レビューを作成するには、[グローバル管理者](../roles/permissions-reference.md#global-administrator)または[特権ロール管理者](../roles/permissions-reference.md#privileged-role-administrator)ロールに割り当てられている必要があります。

## <a name="create-access-reviews"></a>アクセス レビューを作成する

1. いずれかの事前に必要なロールに割り当てられているユーザーとして、[Azure portal](https://portal.azure.com/) にサインインします。

2. **[Identity Governance]** を選択します。
 
3. **Azure AD ロール** の場合、 **[Azure AD ロール]** を **[Privileged Identity Management]** で選択します。 **Azure リソース** の場合、 **[Azure リソース]** を **[Privileged Identity Management]** で選択します。

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/identity-governance.png" alt-text="Azure portal で Identity Governance を選択するスクリーンショット。" lightbox="./media/pim-create-azure-ad-roles-and-resource-roles-review/identity-governance.png"::: 
 
4. **Azure AD ロール** の場合、再度 **[Azure AD ロール]** を **[管理]** で選択します。 **Azure リソース** の場合、サブスクリプションなど、管理するリソースを選択します。


5. [管理] の下で **[アクセス レビュー]** を選択し、次に **[新規]** を選択して新しいアクセス レビューを作成します。

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/access-reviews.png" alt-text="Azure AD ロール - すべてのレビューの状態を示す [アクセス レビュー] スクリーンショット。":::
 
6. アクセス レビューに名前を付けます。 必要に応じて、そのレビューに説明を加えます。 その名前と説明がレビュアーに示されます。

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/name-description.png" alt-text="アクセス レビューの作成 - レビューの名前と説明のスクリーンショット。":::

7. **[開始日]** を設定します。 既定では、アクセス レビューは 1 回実行されます。作成されたのと同じ時間に開始され、1 か月で終了します。 この開始日と終了日を変更することで、アクセス レビューを後で開始し、必要な日数を好きなだけ確保することができます。

   :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/start-end-dates.png" alt-text="開始日、頻度、期間、終了、回数、および終了日のスクリーンショット。":::

8. アクセス レビューを繰り返すには、 **[頻度]** 設定を **[1 回]** から **[毎週]** 、 **[毎月]** 、 **[四半期に 1 回]** 、 **[毎年]** 、 **[Semi-annually]\(半年に 1 回\)** に変更します。 **[期間]** スライダーまたはテキスト ボックスを使用し、繰り返しの系列の各レビューでレビュー担当者からの入力を受け付ける日数を決めます。 たとえば、レビューの重複を避けるために、月 1 回のレビューに設定できる最大期間は 27 日です。

9. **[終了]** の設定を使用して、アクセス レビューの繰り返し系列を終了する方法を指定します。 系列は 3 つの方法で終了できます。つまり、継続的に実行して無期限にレビューを開始する、特定の日付まで実行する、または定義された実行回数が完了するまで実行する、です。 レビューを管理できる管理者は、 **[設定]** で日付を変更することによって作成後に系列を停止し、それがその日付に終了するようにできます。


10. **[ユーザーのスコープ]** セクションで、レビューのスコープを選択します。 **Azure AD ロール** の場合、最初のスコープ オプションは [ユーザーとグループ] です。 直接割り当てられたユーザーと、[ロールを割り当て可能なグループ](../roles/groups-concept.md)がこの選択に含まれます。 **Azure リソース ロール** の場合、最初のスコープは [ユーザー] です。 Azure リソース ロールに割り当てられたグループは、この選択によってレビューで推移的なユーザーの割り当てを表示するように展開されます。 また、 **[サービス プリンシパル]** を選択して、Azure リソース ロールまたは Azure AD ロールに直接アクセスできるコンピューター アカウントを確認することもできます。

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/users.png" alt-text="ロール メンバーシップをレビューするためのユーザー スコープのスクリーンショット。":::

11. **[ロール メンバーシップをレビュー]** で、レビューする特権 Azure リソース ロールまたは Azure AD ロールを選択します。

    > [!NOTE]
    > 複数のロールを選択すると、複数のアクセス レビューが作成されます。 たとえば、5 つのロールを選択すると、5 つの別々のアクセス レビューが作成されます。

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/review-role-membership.png" alt-text="ロール メンバーシップのレビューのスクリーン ショット。":::

12. **[割り当ての種類]** で、プリンシパルがロールに割り当てられた方法によってレビューの範囲を指定します。 (レビューが作成されたときのアクティブ化の状態に関係なく) 対象の割り当てをレビューする場合は **[対象の割り当てのみ]** 、アクティブな割り当てをレビューする場合は **[アクティブな割り当てのみ]** を選択します。 種類に関係なくすべての割り当てをレビューするには、 **[アクティブおよび対象のすべての割り当て]** を選択します。

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/assignment-type-select.png" alt-text="割り当ての種類のレビュー担当者の一覧のスクリーンショット。":::

13. **[レビュー担当者]** セクションで、全ユーザーをレビューする担当者 (複数可) を選びます。 メンバー自身にそのアクセス権をレビューしてもらうことができます。

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/reviewers.png" alt-text="選択したユーザーまたはメンバー (セルフ) のレビュー担当者の一覧":::

    - **選択したユーザー** - レビューを実行する特定のユーザーを指定するには、このオプションを使用します。 このオプションは、レビューのスコープに関係なく使用できます。選択したレビュー担当者は、ユーザー、グループ、サービス プリンシパルをレビューできます。
    - **メンバー (セルフ)** - ユーザーに自分のロール割り当てを確認してもらう場合は、このオプションを使用します。 このオプションは、レビューのスコープが **[ユーザーとグループ]** または **[ユーザー]** に設定されている場合にのみ使用できます。 **Azure AD ロール** の場合、このオプションが選択されているときに、ロールを割り当て可能なグループはレビューに含まれません。 
    - **マネージャー** – ユーザーのマネージャーにロールの割り当てをレビューしてもらうには、このオプションを使用します。 このオプションは、レビューのスコープが **[ユーザーとグループ]** または **[ユーザー]** に設定されている場合にのみ使用できます。 マネージャーを選択すると、フォールバック レビュー担当者を指定するオプションも表示されます。 フォールバック レビュー担当者は、ディレクトリにマネージャーが指定されていない場合に、ユーザーをレビューするように求められます。 **Azure AD ロール** の場合、レビュー担当者が選択されているときに、ロールを割り当て可能なグループは、フォールバック レビュー担当者によってレビューされます。 

### <a name="upon-completion-settings"></a>完了時の設定

1. レビュー完了後の動作を指定するには、 **[設定完了時]** セクションを展開します。

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/upon-completion-settings.png" alt-text="[設定完了時] で [自動適用] および [レビュー担当者からの応答なし] の指定のスクリーンショット。":::

2. 拒否されたユーザーのアクセスを自動的に削除する場合は、 **[リソースへの結果の自動適用]** を **[有効]** に設定します。 レビューが完了したときに結果を手動で適用する場合は、スイッチを **[無効]** に設定します。

3. レビュー期間内にレビュー担当者によってレビューされなかったユーザーに対する処理を指定するには、 **[レビュー担当者が応答しない場合]** の一覧を使用します。 この設定は、レビュー担当者によってレビューされたユーザーには影響しません。

    - **[変更なし]** - ユーザーのアクセスをそのまま変更しないでおきます
    - **[アクセスの削除]** - ユーザーのアクセスを削除します
    - **[アクセスを承認する]** - ユーザーのアクセスを承認します
    - **[推奨事項の実行]** - ユーザーの継続的なアクセスの拒否または承認に関するシステムの推奨事項を実行します
    
4. **[拒否されたゲスト ユーザーに適用するアクション]** リストを使用して、ゲスト ユーザーが拒否された場合の動作を指定します。 現時点では、この設定は Azure AD ロールと Azure リソース ロールのレビューでは編集できません。ゲスト ユーザーは、すべてのユーザーと同様に、拒否された場合は常にリソースにアクセスできなくなります。

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/action-to-apply-on-denied-guest-users.png" alt-text="完了時の設定 - 拒否されたゲスト ユーザーに適用するアクションのスクリーンショット。":::

5. 追加のユーザーやグループにも通知を送信して、レビュー完了の更新を知らせることができます。 この機能により、レビュー作成者以外の利害関係者も、通知を受け取ってレビューの進行状況を把握できます。 この機能を使用するには **[ユーザーまたはグループの選択]** を選択し、完了通知を受け取るユーザーまたはグループを追加します。

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/upon-completion-settings-additional-receivers.png" alt-text="完了設定時 - 通知を受信するユーザーを追加するスクリーンショット。":::

### <a name="advanced-settings"></a>詳細設定

1. 他の設定を指定するには、 **[詳細設定]** セクションを展開します。

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/advanced-settings.png" alt-text="推奨事項の表示、承認理由の必須化、メール通知、およびリマインダーのための [詳細設定] のスクリーンショット。":::

1. ユーザーの情報に基づくシステムの推奨事項をレビュー担当者に表示するには、 **[推奨事項を表示]** を **[有効]** に設定します。

1. レビュー担当者による承認理由の指定を必須にするには、 **[承認時に理由が必要]** を **[有効]** に設定します。

1. アクセス レビュー開始時に Azure AD からレビュー担当者にメール通知を送信し、レビュー完了時に管理者にメール通知を送信するには、 **[メール通知]** を **[有効]** に設定します。

1. レビューを完了していないレビュー担当者に、進行中のアクセス レビューに関するリマインダーを Azure AD から送信するには、 **[リマインダー]** を **[有効]** に設定します。
1. レビュー担当者に送信されるメールの内容は、レビュー名、リソース名、期限などのレビューの詳細に基づいて自動生成されます。追加の指示や連絡先情報などの追加情報を伝達する方法が必要な場合は、割り当てられたレビュー担当者に送信される招待メールと通知メールに含まれる、**レビュー担当者のメールに関する追加のコンテンツ** でこれらの詳細を指定することができます。 下の強調表示されているセクションは、この情報が表示される場所です。

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/email-info.png" alt-text="レビュー担当者に送信される電子メールの内容 (ハイライトを含む)"::: 

## <a name="manage-the-access-review"></a>アクセスレビューを管理する

アクセス レビューの **[概要]** ページでは、レビュー担当者が完了したレビューの進捗状況を追跡できます。 ディレクトリのアクセス権は、レビューが完了するまで変更されません。 以下は **Azure リソース** と **Azure AD ロール** のアクセス レビューの概要ページを示すスクリーンショット。

:::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/access-review-overview.png" alt-text="Azure AD ロールのアクセス レビューの詳細を示すアクセス レビューの概要ページのスクリーンショット。" lightbox="./media/pim-create-azure-ad-roles-and-resource-roles-review/access-review-overview.png":::

これが 1 回限りのレビューである場合は、アクセス レビュー期間が終了するか、管理者がアクセス レビューを停止した後に、「[PIM で Azure リソース ロールと Azure AD ロールのアクセス レビューを完了する](pim-complete-azure-ad-roles-and-resource-roles-review.md)」の手順に従って結果を確認および適用します。  

アクセス レビューの系列を管理するには、アクセス レビューに移動し、[Scheduled]\(スケジュール済み\) レビューで今後予定されている実行を見つけ、それに応じて終了日を編集したり、レビュー担当者を追加/削除したりします。

**[Upon completion]\(完了時\)** 設定での選択に基づいて、レビューの終了日の後、またはレビューを手動で停止したときに自動適用が実行されます。 レビューの状態は、 **[完了]** から **[適用中]** などの中間状態を経由して、最後に状態 **[適用済み]** まで変化します。 拒否されたユーザーが存在する場合は、それらのユーザーが数分以内にロールから削除されることを確認できます。

> [!IMPORTANT]
> グループが **Azure リソース ロール** に割り当てられている場合、Azure リソース ロールのレビュー担当者には、入れ子になったグループを介して割り当てられたアクセス許可を持つ間接ユーザーの展開された一覧が表示されます。 レビュー担当者が入れ子になったグループのメンバーを拒否した場合、そのユーザーは入れ子になったグループから削除されないため、その拒否結果はロールに対して正常に適用されません。 **Azure AD ロール** の場合、[ロールを割り当て可能なグループ](../roles/groups-concept.md)は、グループのメンバーを展開する代わりにレビューに表示され、レビュー担当者はグループ全体へのアクセスを承認または拒否します。

## <a name="update-the-access-review"></a>アクセス レビューを更新する

1 つ以上のアクセス レビューが開始された後、既存のアクセス レビューの設定を変更または更新したい場合があります。 考慮する必要があるいくつかの一般的なシナリオを次に示します。

- **レビュー担当者の追加と削除** - アクセス レビューを更新するときに、プライマリ レビュー担当者に加えて、フォールバック レビュー担当者を追加することができます。 プライマリ レビュー担当者は、アクセス レビューを更新するときに削除される可能性があります。 しかし、フォールバック レビュー担当者は、設計上削除可能ではありません。

    > [!Note]
    > フォールバック レビュー担当者は、レビュー担当者の種類がマネージャーの場合にのみ追加できます。 レビュー担当者の種類が選択されたユーザーの場合は、プライマリ レビュー担当者を追加できます。

- **レビュー担当者へのリマインダー** - アクセス レビューを更新するときに、[詳細設定] タブでリマインダー オプションを有効にすることを選択できます。 有効にすると、レビューを完了したかどうかに関係なく、ユーザーはレビュー期間の中間で電子メール通知を受け取ります。 

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/reminder-setting.png" alt-text="アクセス レビュー設定のリマインダー オプションのスクリーンショット。":::

- **設定の更新** - アクセス レビューが定期的な場合は、[現在] と [シリーズ] の下に別々の設定があります。 [現在] の設定を更新すると、現在のアクセス レビューに対する変更だけが適用され、[シリーズ] の設定を更新すると、今後のすべての定期的発生の設定が更新されます。

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/current-v-series-setting.png" alt-text="アクセス レビューの設定ページのスクリーンショット。" lightbox="./media/pim-create-azure-ad-roles-and-resource-roles-review/current-v-series-setting.png":::
    
## <a name="next-steps"></a>次のステップ

- [PIM で Azure リソース ロールと Azure AD ロールのアクセス レビューを実行する](pim-perform-azure-ad-roles-and-resource-roles-review.md)
- [PIM で Azure リソース ロールと Azure AD ロールのアクセス レビューを完了する](pim-complete-azure-ad-roles-and-resource-roles-review.md)
