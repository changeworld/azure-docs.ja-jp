---
title: Azure の予約使用率を確認する
description: 予約の使用率とその詳細を取得する方法について説明します。
author: bandersmsft
ms.reviewer: primittal
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 10/07/2021
ms.author: banders
ms.openlocfilehash: f69c39205ddc465ee4634ec621905fed2d701682
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131446241"
---
# <a name="view-reservation-utilization-after-purchase"></a>購入後に予約の使用率を確認する

Azure portal と Cost Management Power BI アプリで、予約の使用率 (パーセンテージ) と予約を使用したリソースを確認することができます。

## <a name="view-utilization-in-the-azure-portal-with-azure-rbac-access"></a>Azure RBAC アクセスを使用して Azure portal で使用状況を確認する

予約の使用率を確認するには、予約への Azure RBAC アクセス権を持っているか、すべての Azure サブスクリプションと管理グループを管理するための昇格されたアクセス権を持っている必要があります。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. [[予約]](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) に移動します。
1. 一覧には、ご自分が所有者または閲覧者ロールになっている予約がすべて表示されます。 各予約には、最新の既知の使用率のパーセンテージが示されます。
1. 使用率のパーセンテージを選択すると、使用率の履歴と詳細が表示されます。 次のビデオに例が示されています。
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="view-utilization-as-billing-administrator"></a>課金管理者として使用率を確認する

Enterprise Agreement (EA) 管理者または Microsoft 顧客契約 (MCA) の課金管理者は、 **[コストの管理と請求]** から使用率を確認できます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[コストの管理と請求]**  >  **[予約]** に移動します。
1. 使用率のパーセンテージを選択すると、使用率の履歴と詳細が表示されます。

## <a name="get-reservations-and-utilization-using-apis-powershell-and-cli"></a>API、PowerShell、CLI を使用して予約と使用率を取得する

Reserved Instance Usage API を使用して、[予約の使用率](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)を取得することができます。

## <a name="see-reservations-and-utilization-in-power-bi"></a>Power BI で予約と使用率を確認する

Power BI ユーザーには、2 とおりの方法があります。

- Power BI Desktop 用の Cost Management コネクタ - 予約購入日と使用率データは、[Power BI Desktop 用の Cost Management コネクタ](/power-bi/desktop-connect-azure-cost-management)で入手できます。 コネクタを使用して、目的のレポートを作成します。
- Cost Management Power BI アプリ - 追加のカスタマイズが可能な事前作成済みレポートに対して [Cost Management Power BI アプリ](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)を使用します。

## <a name="next-steps"></a>次のステップ

- [Azure の予約を管理する](manage-reserved-vm-instance.md)。
- [従量課金制サブスクリプションの予約使用量について](understand-reserved-instance-usage.md)。
- [エンタープライズ加入契約の予約使用量について](understand-reserved-instance-usage-ea.md)。
- [CSP サブスクリプションの予約使用量について](/partner-center/azure-reservations)。
