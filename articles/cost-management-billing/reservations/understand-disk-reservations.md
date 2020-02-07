---
title: Azure Disk Storage に予約割引が適用されるしくみについて
description: Premium SSD マネージド ディスクに Azure ディスク予約割引が適用されるしくみについて説明します。
author: roygara
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2020
ms.author: rogarana
ms.openlocfilehash: 7f23aaebc20b562768fb04ae988e4aff1b62fb21
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902133"
---
# <a name="understand-how-your-reservation-discount-is-applied-to-azure-disk-storage"></a>Azure Disk Storage に予約割引が適用されるしくみについて

Azure ディスク予約容量を購入すると、予約の条件に一致するディスク リソースに予約割引が自動的に適用されます。 予約割引はディスク容量のみに適用され、ディスク スナップショットは従量課金制で課金されます。

Azure ディスク予約の詳細については、「[Azure ディスク予約によるコストの削減](../../virtual-machines/linux/disks-reserved-capacity.md)」を参照してください。
Azure ディスク予約の価格の詳細については、[Azure ディスクの価格](https://azure.microsoft.com/pricing/details/managed-disks/)に関するページを参照してください。

## <a name="how-the-reservation-discount-is-applied"></a>予約割引の適用方法

Azure ディスク予約割引は "使用しないと失われる" 割引で、マネージド ディスク リソースに時間単位で適用されます。 特定の 1 時間に予約の条件を満たすマネージド ディスク リソースがない場合、その時間に対する予約量は失われ、未使用の時間は繰り越されません。

リソースを削除すると、指定されたスコープ内の別の一致するリソースに予約割引が自動的に適用されます。 指定したスコープ内に一致するリソースが見つからない場合、予約済み時間は失われます。

## <a name="discount-examples"></a>割引の例

次の例は、デプロイに応じて、Azure ディスク予約割引が適用される方法を示しています。

米国西部 2 リージョンで 1 年間 100 P30 ディスク (ディスクあたり 1 TiB) の予約容量を購入したとします。 このサンプル予約のコストは 140,100 ドルであると仮定します。 次の 12 か月間について、全額を前払いするか、または固定月額分割払いで毎月 11,675 ドルを支払うかを選択できます。
以下の例では、毎月の予約支払いプランに登録したと仮定します。 次のシナリオでは、予約容量の過少使用、過多使用、または積み上げの場合にどうなるかを説明します。

### <a name="underusing-your-capacity"></a>容量の使用量が過少だった場合

予約期間内の特定の 1 時間に、100 P30 ディスク予約のうち 99 P30 のみの Premium SSD をデプロイしたとします。 残りの 1 P30 は、その時間には適用されず、引き継がれることもありません。

### <a name="overusing-your-capacity"></a>容量の使用量が過多だった場合

予約期間内の特定の 1 時間に、101 P30 の Premium SSD を使用したとします。 予約割引は 100 P30 ディスクにのみ適用され、残りの 1 P30 ディスクについては、その時間の従量課金制の料金で課金されます。 次の 1 時間の使用量が 100 P30 ディスクに減少した場合は、すべての使用量が予約の対象になります。

### <a name="tiering-your-capacity"></a>容量の使用量を積み上げた場合

予約期間内の特定の 1 時間に、合計 200 P30 の Premium SSD を使用したいとします。 最初の 30 分間に、100 のみを使用します。 100 P30 ディスクを予約しているため、この間の使用量がすべて対象になります。 この後、最初の 100 の使用を中止し (このため、使用量はゼロになります)、残りの 30 分間に別の 100 の使用を開始すると、その使用量も予約の対象になります。

![容量の過少使用、過多使用、積み上げの例を示す図](media/understand-disk-reservations/reserved-disks-example-scenarios.png)

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

- [Azure ディスク予約によるコストの削減 (Linux)](../../virtual-machines/linux/disks-reserved-capacity.md)
- [Azure ディスク予約によるコストの削減 (Windows)](../../virtual-machines/windows/disks-reserved-capacity.md)
- [Azure の予約とは](save-compute-costs-reservations.md)