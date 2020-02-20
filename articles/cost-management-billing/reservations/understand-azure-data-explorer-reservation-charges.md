---
title: Azure Data Explorer に予約割引が適用されるしくみについて
description: Azure Data Explorer のマークアップ メーターに予約割引が適用されるしくみについて説明します。
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: ab107a0afe8be1d95de8dafb21f239e6da733271
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199332"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-data-explorer"></a>Azure Data Explorer に予約割引が適用されるしくみについて

Azure Data Explorer の予約容量を購入すると、予約の属性や数量に合致する Azure Data Explorer リソースに対して予約割引が自動的に適用されます。 予約には、Azure Data Explorer マークアップ料金が含まれますが、 Azure Data Explorer クラスターを操作するために使用されるコンピューティング、ネットワーク、ストレージ、またはその他の Azure リソースは含まれません。 これらのリソースの予約は別途購入する必要があります。

## <a name="how-reservation-discount-is-applied"></a>予約割引の適用方法

予約割引は、"*使用しないと失われます*"。 したがって、ある時間、一致するリソースがない場合は、その時間に対する予約量は失われます。 未使用の予約済み時間を繰り越すことはできません。

リソースをシャットダウンすると、予約割引は、指定されたスコープ内の別の一致するリソースに自動的に適用されます。 指定したスコープ内に一致するリソースが見つからない場合、予約済み時間は "*失われます*"。

## <a name="reservation-discount-applied-to-azure-data-explorer-clusters"></a>Azure Data Explorer クラスターに適用される予約割引

予約割引は、Azure Data Explorer マークアップ使用量に対して時間単位で適用されます。 Azure Data Explorer リソースの実行時間が 1 時間に満たない合、予約は予約の属性に一致する他の Data Explorer リソースに自動的に適用されます。 この割引は、同時に実行されている Azure Data Explorer リソースに適用できます。 1 時間実行され、予約の属性に一致する Azure Data Explorer リソースがない場合は、その時間について予約割引の特典を完全に活用することができません。

> [!NOTE]
> * 予約容量による節約額を最大にするために、Azure Data Explorer クラスターに使用される仮想マシンの[予約容量](../../virtual-machines/windows/prepay-reserved-vm-instances.md)を購入することを**強くお勧めします**。
> * 予約を購入すると、すべてのリージョンに割引が適用されます。

## <a name="examples"></a>例

次の例は、購入したマークアップ ユニット数と実行する時間に応じて、Azure Data Explorer の予約容量割引がどのように適用されるかを示しています。
たとえば、エンジン クラスターのサイズが **2 台の D11_v2 VM**の場合、合計オンデマンド料金は、1 時間あたり Azure Data Explorer マークアップ メーター × 4 ユニットになります。

**シナリオ 1**

Azure Data Explorer マークアップ × 8 ユニット用の Azure Data Explorer 予約容量を購入するとします。 残りの予約の属性に一致し、合計 16 コアを搭載した 2 台の D13_v2 VM のエンジン クラスターを実行しており、1 時間あたり Azure Data Explorer マークアップ × 16 ユニット分が課金されます。 Azure Data Explorer のコンピューティング使用量の 8 コアに対して従量課金制の料金が請求され、1 時間分の 8 コアの Azure Data Explorer マークアップ ユニットの使用量に対して予約割引が適用されます。

以降の例では、購入する Azure Data Explorer の予約容量は、16 コアの Azure Data Explorer 用であり、残りの予約属性は実行中の Azure Data Explorer クラスターと一致するものとします。

**シナリオ 2**

それぞれ 8 コアを搭載した 2 台の Azure Data Explorer エンジン クラスターを、2 つの異なるリージョンで 1 時間実行します。 16 コアの予約割引は、両方のクラスターとそれらが使用する Azure Data Explorer マークアップの 16 ユニットに適用されます。

**シナリオ 3**

一方の 16 コア Azure Data Explorer エンジン クラスターを午後 1 時から午後 1 時 30 分まで実行します。 もう一方の 16 コア Azure Data Explorer エンジン クラスターを午後 1 時 30 分から午後 2 時まで実行します。 いずれも予約割引が適用されます。

**シナリオ 4**

一方の 16 コア Azure Data Explorer エンジン クラスターを午後 1 時から午後 1 時 45 分まで実行します。 もう一方の 16 コア Azure Data Explorer エンジン クラスターを午後 1 時 30 分から午後 2 時まで実行します。 15 分間の重複分には、従量課金制の料金が適用されます。 残りの時間の Azure Data Explorer マークアップ使用量には、予約割引が適用されます。

Azure の予約の適用状況を把握し、課金の使用状況レポートで確認する方法については、[Azure の予約の使用状況](understand-reserved-instance-usage-ea.md)に関するページを参照してください。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

Azure の予約の詳細については、次の記事を参照してください。

* [Azure Data Explorer 予約容量を使用して Azure Data Explorer コンピューティング リソースの料金を前払いする](../../data-explorer/pricing-reserved-capacity.md)  
* [Azure の予約とは](save-compute-costs-reservations.md)  
* [Azure の予約の管理](manage-reserved-vm-instance.md)  
* [従量課金制サブスクリプションの予約使用量について](understand-reserved-instance-usage.md)
* [エンタープライズ加入契約の予約使用量について](understand-reserved-instance-usage-ea.md)
* [CSP サブスクリプションの予約の使用状況について](https://docs.microsoft.com/partner-center/azure-reservations)
