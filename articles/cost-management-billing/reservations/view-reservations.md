---
title: Azure リソースの予約を表示する
description: Azure portal で Azure の予約を表示する方法について説明します。 API、PowerShell、CLI、Power BI を使用して予約と使用率を参照してください。
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 12/15/2020
ms.author: banders
ms.openlocfilehash: 8c69f477f363654b8bd707949f0a5b4c46a4e8df
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561106"
---
# <a name="view-azure-reservations"></a>Azure の予約を表示する

この記事では、Azure portal で Azure の予約を表示する方法について説明します。 購入した予約は、Azure portal で表示して管理することができます。

## <a name="who-can-manage-a-reservation-by-default"></a>既定で予約を管理できるユーザー

既定では、次のユーザーが予約を表示および管理できます。

- 予約を購入した担当者と、予約の購入に使用した課金サブスクリプションのアカウント管理者が、予約注文に追加されます。
- マイクロソフト エンタープライズ契約および Microsoft 顧客契約の課金管理者。

他のユーザーが予約を管理できるようにするには、次の 2 つのオプションがあります。

- 個々の予約注文のアクセス管理を委任するには、次の手順を実行します。
    1. [Azure portal](https://portal.azure.com) にサインインします。
    1. **[すべてのサービス]**  >  **[予約]** の順に選択し、自分にアクセス権がある予約を一覧表示します。
    1. どの予約のアクセス権を他のユーザーに委任するかを選択します。
    1. [予約の詳細] で、予約注文を選択します。
    1. **[アクセス制御 (IAM)]** を選択します。
    1. **[ロールの割り当ての追加]**  >  **[ロール]**  >  **[所有者]** を選択します。 制限付きアクセス権を付与する場合は、別のロールを選択します。
    1. 所有者として追加するユーザーの電子メール アドレスを入力します。
    1. ユーザーを選択し、 **[保存]** を選択します。

- ユーザーを課金管理者としてマイクロソフト エンタープライズ契約または Microsoft 顧客契約に追加するには、次の手順を実行します。
    - マイクロソフト エンタープライズ契約の場合は、"_エンタープライズ管理者_" ロールを持つユーザーを追加することで、マイクロソフト エンタープライズ契約に適用されるすべての予約注文を表示および管理できます。 "_エンタープライズ管理者 (読み取り専用)_ " ロールを持つユーザーは、予約の表示のみを実行できます。 部門管理者およびアカウント所有者は、アクセス制御 (IAM) を使用して明示的にこれらに追加されて "_いない限り_"、予約を表示することはできません。 詳細については、「[Azure エンタープライズ ロールの管理](../manage/understand-ea-roles.md)」を参照してください。

        "_エンタープライズ管理者は、予約注文の所有権を取得し、アクセス制御 (IAM) を使用して他のユーザーを予約に追加することができます。_ "
    - Microsoft 顧客契約の場合は、課金プロファイル所有者ロールまたは課金プロファイル共同作成者ロールを持つユーザーが、課金プロファイルを使用して行われたすべての予約購入を管理できます。 課金プロファイル閲覧者と請求書管理者は、請求プロファイルを使用して支払われるすべての予約を表示できます。 ただし、予約に変更を加えることはできません。
    詳細については、「[課金プロファイルのロールとタスク](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks)」を参照してください。

### <a name="how-billing-administrators-view-or-manage-reservations"></a>課金管理者が予約を表示または管理する方法

1. **[コストの管理と請求]** に移動し、ページの左側にある **[予約トランザクション]** を選択します。
2. 必要な課金アクセス許可がある場合は、予約を表示および管理できます。 予約が表示されない場合は、予約が作成された Azure AD テナントを使用してサインインしていることを確認してください。

## <a name="view-reservation-and-utilization-in-the-azure-portal"></a>Azure portal で予約と使用率を表示する

所有者または閲覧者として予約を表示するには

1. [Azure portal](https://portal.azure.com) にサインインします。
2. [[予約]](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) に移動します。
3. 一覧には、ご自分が所有者または閲覧者ロールになっている予約がすべて表示されます。 各予約には、最新の既知の使用率のパーセンテージが示されます。
4. 使用率のパーセンテージを選択すると、使用率の履歴と詳細が表示されます。 詳細については、以下のビデオをご覧ください。
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="get-reservations-and-utilization-using-apis-powershell-and-cli"></a>API、PowerShell、CLI を使用して予約と使用率を取得する

すべての予約の一覧は、次のリソースを使用して取得します。

- [API: 予約注文 - 一覧](/rest/api/reserved-vm-instances/reservationorder/list)
- [PowerShell: 予約注文 - 一覧](/powershell/module/azurerm.reservations/get-azurermreservationorder)
- [CLI: 予約注文 - 一覧](/cli/azure/reservations/reservation-order#az-reservations-reservation-order-list)

Reserved Instance 使用量 API を使用して、[予約の使用率](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)を取得することもできます。 

## <a name="see-reservations-and-utilization-in-power-bi"></a>Power BI で予約と使用率を確認する

Power BI ユーザーには、次の 2 とおりの方法があります。
- コンテンツパック: [Consumption Insights Power BI コンテンツ パック](/power-bi/desktop-connect-azure-cost-management)で、予約の購入日と使用率のデータを入手できます。 コンテンツ パックを使用して、目的のレポートを作成します。 
- コスト管理アプリ: 事前に作成されたレポートを [Cost Management アプリ](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)を使用して入手できます。このレポートをさらにカスタマイズすることもできます。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

- [Azure の予約を管理する](manage-reserved-vm-instance.md)。
- [従量課金制サブスクリプションの予約使用量について](understand-reserved-instance-usage.md)。
- [エンタープライズ加入契約の予約使用量について](understand-reserved-instance-usage-ea.md)。
- [CSP サブスクリプションの予約使用量について](/partner-center/azure-reservations)。

