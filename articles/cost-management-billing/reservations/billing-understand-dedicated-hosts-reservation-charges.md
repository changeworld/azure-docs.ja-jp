---
title: Azure Dedicated Host 予約インスタンスの割引について
description: Azure 予約 VM インスタンスの割引が、Azure Dedicated Host にどのように適用されるのかを説明します。
author: bandersmsft
ms.reviewer: primittal
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: c6b4f4fbb37cf360dc235408e0ff54925492fade
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131453822"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-azure-dedicated-hosts"></a>Azure Dedicated Host への Azure 予約割引の適用

Azure Dedicated Host で予約インスタンスを購入すると、予約の属性や数量に合致する専用ホストに対して予約割引が自動的に適用されます。 予約購入分は、ご利用の専用ホストの計算コストに充当されます。

## <a name="how-reservation-discount-is-applied"></a>予約割引の適用方法

予約割引は、"*使用しないと失われます*"。 したがって、ある時間、一致するリソースがない場合は、その時間に対する予約量は失われます。 未使用の予約済み時間を繰り越すことはできません。

専用ホストを削除すると、指定されたスコープ内の別の一致するリソースに予約割引が自動的に適用されます。 指定したスコープ内に一致するリソースが見つからない場合、予約済み時間は "*失われます*"。

## <a name="reservation-discount-for-dedicated-hosts"></a>専用ホストに対する予約割引

Azure Dedicated Host での予約インスタンスによって、専用ホストで使用されるコンピューティング インフラストラクチャのコストに割引が適用されます。 割引は、専用ホストが仮想マシンによって使用されているかどうかに関係なく、専用ホストに対して適用されます。 予約には、追加コスト (ライセンス、専用ホストにデプロイされた仮想マシンによるネットワーク使用量やストレージ消費量など) は含まれません。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

Azure の予約の詳細については、次の記事を参照してください。

- [Azure の予約とは](./save-compute-costs-reservations.md)

- [Azure Dedicated Host の使用](../../virtual-machines/dedicated-hosts.md)

- [専用ホストの価格](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [Azure の予約の管理](./manage-reserved-vm-instance.md)

- [従量課金制サブスクリプションの予約使用量について](./understand-reserved-instance-usage.md)

- [エンタープライズ加入契約の予約使用量について](./understand-reserved-instance-usage-ea.md)

- [CSP サブスクリプションの予約の使用状況について](/partner-center/azure-reservations)