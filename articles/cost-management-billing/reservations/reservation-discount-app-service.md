---
title: Azure App Service の予約割引
description: Azure App Service の Premium v3 および Premium v2 インスタンスと Isolated スタンプに予約割引を適用する方法について説明します。
author: bandersmsft
ms.reviewer: primittal
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 05/13/2021
ms.author: banders
ms.openlocfilehash: 2158a99d12b39891c9e52220fd2b84821b742c58
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131446317"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service"></a>Azure App Service に予約割引を適用する方法

この記事では、Azure App Service の Premium v3 および Premium v2 インスタンスと Isolated スタンプに割引を適用する方法について説明します。

## <a name="how-reservation-discounts-apply-to-premium-v3-instances"></a>Premium v3 インスタンスに予約割引を適用する方法

Azure App Service Premium v3 予約インスタンスを購入すると、予約の属性や数量に合致する App Service インスタンスに対して予約割引が自動的に適用されます。 予約は、Premium v3 インスタンスのコストを対象にしています。 

### <a name="how-the-discount-is-applied-to-azure-app-service"></a>Azure App Service に割引を適用する方法 

予約割引は、*使用しないと失われます*。 したがって、ある時間、一致するリソースがない場合は、その時間に対する予約量は失われます。 未使用の予約済み時間を繰り越すことはできません。
リソースをシャットダウンすると、予約割引は、指定されたスコープ内の別の一致するリソースに自動的に適用されます。 指定したスコープ内に一致するリソースが見つからない場合、予約済み時間は失われます。

### <a name="reservation-discount-for-premium-v3-instances"></a>Premium v3 インスタンスの予約割引

Azure 予約割引は、実行中の Premium v3 インスタンスに 1 時間単位で適用されます。 購入済みの予約は、実行中の Premium v3 インスタンスによって生成された使用量と照合され、予約割引が適用されます。 実行時間が 1 時間に満たない可能性のある Premium v3 インスタンスの場合、予約は、同時に実行されているインスタンスを含め、予約を使用していない他のインスタンスから満たされます。 1 時間の最後には、その時間のインスタンスの予約の適用がロックされます。 インスタンスの実行が 1 時間に満たない場合、またはその時間内のコンカレント インスタンスが予約の時間を満たさない場合、その時間の予約は十分に活用されていません。 以下のグラフは、課金対象の VM 使用量に予約を適用した例を示しています。 この例は、単一の予約購入で、合致する VM インスタンスが 2 つあることを前提としています。

![課金対象の VM 使用量に予約を適用した例を示す図](./media/reservation-discount-app-service/reserved-premium-v3-instance-application.png)

1.  予約のラインを超える使用量には、通常の従量課金制の料金が適用されます。 予約のラインを下回る使用量については、予約購入分として前払い済みであるため課金されません。
2.  Hour 1 では、インスタンス 1 の実行時間が 0.75 時間、インスタンス 2 の実行時間が 0.5 時間です。 Hour 1 の合計使用量は 1.25 時間となります。 残りの 0.25 時間については従量課金制の料金が発生します。
3.  Hour 2 と Hour 3 では、どちらのインスタンスも実行時間はそれぞれ 1 時間です。 一方のインスタンスには予約購入分が充当されますが、もう一方には従量課金制の料金が発生します。
4.  Hour 4 では、インスタンス 1 の実行時間が 0.5 時間、インスタンス 2 の実行時間が 1 時間です。 インスタンス 1 は予約購入分で全額充当されます。またインスタンス 2 の 0.5 時間も充当されます。 残りの 0.5 時間については従量課金制の料金が発生します。

Azure の予約の適用状況を把握し、課金の使用状況レポートで確認する方法については、[予約の使用状況](understand-reserved-instance-usage-ea.md)に関するページを参照してください。

## <a name="how-reservation-discounts-apply-to-isolated-v2-instances"></a>Isolated v2 インスタンスに予約割引を適用する方法

Azure App Service Isolated v2 予約インスタンスを購入すると、予約の属性や数量に合致する App Service インスタンスに対して予約割引が自動的に適用されます。 予約は、Isolated v2 インスタンスのコストを対象にしています。

### <a name="how-the-discount-is-applied-to-azure-app-service"></a>Azure App Service に割引を適用する方法

予約割引は、_使用しないと失われます_。 したがって、ある時間、一致するリソースがない場合は、その時間に対する予約量は失われます。 未使用の予約済み時間を繰り越すことはできません。 リソースをシャットダウンすると、予約割引は、指定されたスコープ内の別の一致するリソースに自動的に適用されます。 指定したスコープ内に一致するリソースが見つからない場合、予約済み時間は失われます。

### <a name="reservation-discount-for-isolated-v2-instances"></a>Isolated v2 インスタンスの予約割引

Azure 予約割引は、実行中の Isolated v2 インスタンスに 1 時間単位で適用されます。 購入済みの予約は、実行中の Isolated v2 インスタンスによって生成された使用量と照合され、予約割引が適用されます。 実行時間が 1 時間に満たない可能性のある Isolated v2 インスタンスの場合、予約は、同時に実行されているインスタンスを含め、予約を使用していない他のインスタンスから満たされます。 1 時間の最後には、その時間のインスタンスの予約の適用がロックされます。 インスタンスの実行が 1 時間に満たない場合、またはその時間内のコンカレント インスタンスが予約の時間を満たさない場合、その時間の予約は十分に活用されていません。

Azure の予約の適用状況を把握し、課金の使用状況レポートで確認する方法については、[予約の使用状況](understand-reserved-instance-usage-ea.md)に関するページを参照してください。

## <a name="how-reservation-discounts-apply-to-isolated-stamps"></a>Isolated スタンプに予約割引を適用する方法

App Service Isolated スタンプ料金の予約容量を購入すると、そのリージョンのスタンプ料金に予約割引が自動的に適用されます。 予約割引は、Isolated スタンプ料金の測定から生成される使用量に適用されます。 worker、追加のフロント エンド、およびスタンプに関連するその他のリソースは、通常の料金で引き続き課金されます。

### <a name="reservation-discount-application"></a>適用される予約割引

App Service Isolated スタンプ料金の割引は、1 時間ごとのIsolated スタンプの実行に適用されます。 スタンプを 1 時間デプロイしないと、その 1 時間の予約容量は無駄になります。 持ち越すことはできません。

購入後、購入した予約は、特定のリージョンで実行されている Isolated スタンプと照合されます。 そのスタンプをシャットダウンすると、そのリージョンで実行されている他のスタンプには予約割引が自動的に適用されます。 スタンプが存在しない場合、予約はそのリージョンで作成された次のスタンプに適用されます。

スタンプが丸 1 時間実行されない場合、予約は同じ時間内に同じリージョン内の他の一致するスタンプに自動的に適用されます。

### <a name="choose-a-stamp-type---windows-or-linux"></a>スタンプの種類を選択する - Windows または Linux

空の Isolated スタンプからは、既定で Windows スタンプの測定が生成されます。 たとえば、worker がデプロイされていない場合などです。 Windows worker をデプロイしている場合は、引き続き測定が出力されます。 Linux worker をデプロイしている場合、測定は Linux スタンプの測定に変わります。 Linux と Windows の両方の worker をデプロイしている場合、スタンプからは Windows の測定が生成されます。

その結果、スタンプの有効期間中、スタンプの測定は Windows と Linux とで変わる可能性があります。 一方、予約はオペレーティング システム固有です。 スタンプにデプロイする予定の worker をサポートする予約を購入する必要があります。 Windows 専用スタンプと混合スタンプには、Windows 予約を使用します。 Linux worker のみのスタンプには、Linux 予約を使用します。

Linux 予約を購入する必要があるのは、スタンプに Linux worker _のみ_ を使用する予定の場合だけです。

### <a name="discount-examples"></a>割引の例

次の例は、デプロイに応じて、Isolated スタンプ料金の予約インスタンス割引がどのように適用されるかを示しています。

- **例 1**:App Service Isolated スタンプのないリージョンで、Isolated 予約スタンプ容量の 1 インスタンスを購入します。 そのリージョンに新しいスタンプをデプロイし、そのスタンプの予約料金を支払います。
- **例 2**:App Service Isolated スタンプが既にデプロイされているリージョンで、Isolated 予約スタンプ容量の 1 インスタンスを購入します。 デプロイしたスタンプの予約料金の受け取りを開始します。
- **例 3**:App Service Isolated スタンプが既にデプロイされているリージョンで、Isolated 予約スタンプ容量の 1 インスタンスを購入します。 デプロイしたスタンプの予約料金の受け取りを開始します。 後でスタンプを削除し、新しいスタンプをデプロイします。 新しいスタンプの予約料金を受け取ります。 スタンプがデプロイされていない期間の割引は繰り越されません。
- **例 4**:あるリージョンで Isolated Linux 予約スタンプ容量の 1 インスタンスを購入してから、そのリージョンに新しいスタンプをデプロイします。 スタンプが最初にワーカーなしでデプロイされると、Windows スタンプの測定が生成されます。 割引は適用されません。 最初の Linux worker がスタンプをデプロイされると、Linux スタンプの測定が生成され、予約割引が適用されます。 Windows worker が後でスタンプにデプロイされると、スタンプの測定は Windows に戻ります。 Isolated Linux 予約スタンプの予約に対する割引は適用されなくなります。

## <a name="next-steps"></a>次のステップ

- 予約を管理する方法については、「[Azure の予約の管理](manage-reserved-vm-instance.md)」をご覧ください。
- App Service Premium v3 および Isolated スタンプの予約容量を事前に購入してコストを削減する方法の詳細については、「[予約容量を含む Azure App Service の前払い](prepay-app-service.md)」を参照してください。
- Azure の予約の詳細については、次の記事を参照してください。
  - [Azure の予約とは](save-compute-costs-reservations.md)
  - [Azure での予約の管理](manage-reserved-vm-instance.md)
  - [従量課金制料金のサブスクリプションの予約の使用状況について](understand-reserved-instance-usage.md)
  - [エンタープライズ加入契約の予約使用量について](understand-reserved-instance-usage-ea.md)
