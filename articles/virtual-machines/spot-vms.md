---
title: Azure Spot Virtual Machines を使用する
description: Azure Spot Virtual Machines を使用してコストを節約する方法について説明します。
author: JagVeerappan
ms.author: jagaveer
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/05/2020
ms.reviewer: cynthn
ms.openlocfilehash: fb53fc37227e040ed7bd7fc8e47de9aed538bc2e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104721394"
---
# <a name="use-azure-spot-virtual-machines"></a>Azure Spot Virtual Machines を使用する 

Azure Spot Virtual Machines を使用すると、大幅にコストを削減して未使用の容量を利用できます。 Azure で容量の回復が必要になると、Azure インフラストラクチャによって Azure Spot Virtual Machines が削除されます。 そのため、Azure Spot Virtual Machines は、バッチ処理ジョブ、開発/テスト環境、大規模なコンピューティング ワークロードなど、中断に対応できるワークロードに最適です。

利用可能な容量は、サイズ、リージョン、時刻などによって異なります。 Azure Spot Virtual Machines をデプロイすると、利用可能な容量がある場合に Azure によって VM が割り当てられますが、このような VM には SLA がありません。 Azure Spot Virtual Machine は、高可用性を保証するものではありません。 Azure で容量の回復が必要になると、30 秒前通知の後、Azure インフラストラクチャによって Azure Spot Virtual Machines が削除されます。 


## <a name="eviction-policy"></a>削除ポリシー

VM は、容量または設定した最大価格に基づいて削除できます。 Azure スポット仮想マシン の作成では､削除ポリシーを *[割り当て解除]* (既定値) または *[削除]* に設定できます｡ 

*[割り当てを解除する]* ポリシーでは､VM が停止済み/割り当て解除済み状態に移行され､後でもう一度デプロイすることができます｡ ただし､割り当てが成功する保証はありません｡ 割り当てを解除された VM はクォータに影響を及ぼし、基になるディスクのストレージ コストが課金されます。 

VM が排除されるときに、VM を削除したい場合は、排除ポリシーを *[削除]* に設定できます。 排除された VM は基になるディスクと共に削除されるので、ストレージの料金が引き続き発生することはありません。 

[Azure Scheduled Events](./linux/scheduled-events.md) を通じて VM 内通知を受け取ることができます。 これにより、VM が排除されつつある場合には通知が送られ、排除される前にジョブを完了し、タスクのシャットダウンを実行するために 30 秒が与えられます。 


| オプション | 結果 |
|--------|---------|
| 最大価格は >= 現在の価格に設定されています。 | 容量とクォータが使用可能な場合は、VM がデプロイされます。 |
| 最大価格は < 現在の価格に設定されています。 | VM がデプロイされていません。 最大価格が >= 現在の価格である必要があることを示すエラー メッセージが表示されます。 |
| 最大価格が >= 現在の価格の場合は VM の停止/割り当て解除を再起動する | 容量とクォータがある場合は、VM がデプロイされます。 |
| 最大価格が < 現在の価格の場合は VM の停止/割り当て解除を再起動する | 最大価格が >= 現在の価格である必要があることを示すエラー メッセージが表示されます。 | 
| VM の価格が上がり、現在価格が > 最大価格になりました。 | VM が削除されます。 実際に削除する前に、30 秒前通知を受け取ります。 | 
| 削除した後、VM の料金が < 最大価格に戻ります。 | VM は自動的に再起動されません。 自分で VM を再起動することができ、現在の価格で課金されます。 |
| 最大価格が `-1` に設定されている場合 | VM は価格の理由で削除されません。 最大価格は、Standard VM の価格を上限にした現在の価格になります。 標準価格を超えて課金されることはありません。| 
| 最大価格を変更する | 最大価格を変更するには、VM の割り当てを解除する必要があります。 VM の割り当てを解除し、新しい最大価格を設定して、VM を更新します。 |


## <a name="limitations"></a>制限事項

次の VM サイズは、Azure Spot Virtual Machines ではサポートされていません。
 - B シリーズ
 - 任意のサイズのキャンペーン バージョン (Dv2、NV、NC、H キャンペーン サイズなど)

Azure Spot Virtual Machines は、Microsoft Azure China 21Vianet を除き、任意のリージョンにデプロイできます。

<a name="channel"></a>

現在サポートされている[オファーの種類](https://azure.microsoft.com/support/legal/offer-details/)は次のとおりです。

-   Enterprise Agreement 
-   従量課金制プラン コード ( 003P)
-   スポンサー (0036P および 0136P)
- クラウド サービス プロバイダー (CSP) の場合、パートナーにお問い合わせください


## <a name="pricing"></a>価格

Azure Spot Virtual Machines の価格は、リージョンと SKU に基づいて変動します。 詳細については、[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) と [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) の VM 価格を参照してください。 

[Azure 小売価格 API](/rest/api/cost-management/retail-prices/azure-retail-prices) を使用してスポット価格に関する情報を問い合わせるという方法で価格情報を問い合わせることもできます。 `meterName` と `skuName` の両方に `Spot` が含まれています。

可変する価格に対して、最大 5 桁の小数点以下を使用して、最大価格を米ドル (USD) で設定することができます。 たとえば、`0.98765` の値は、1 時間あたり $0.98765 米ドルの最大価格になります。 最大価格を `-1` に設定した場合、VM は価格に基づいて削除されません。 VM の価格は、使用可能な容量とクォータがある限り、現在のスポットの価格または標準 VM の価格のいずれか低い方になります。

## <a name="pricing-and-eviction-history"></a>価格と削除の履歴

ポータルでは、あるリージョンの価格と削除率の履歴をサイズ別に表示できます。 **[View pricing history and compare prices in nearby regions]\(価格履歴を表示し、近くのリージョンの価格を比較する\)** を選択すると、特定のサイズに対して価格のテーブルまたはグラフが表示されます。  次の画像の価格と削除率は単なる例です。 

**グラフ**:

:::image type="content" source="./media/spot-chart.png" alt-text="リージョン オプションのスクリーンショット。グラフの価格と削除率に違いがあります。":::

**テーブル**:

:::image type="content" source="./media/spot-table.png" alt-text="リージョン オプションのスクリーンショット。テーブルの価格と削除率に違いがあります。":::



##  <a name="frequently-asked-questions"></a>よく寄せられる質問

**Q:** 作成後、Azure Spot Virtual Machine は通常の標準の VM と同じになりますか?

**A:** はい。ただし、Azure Spot Virtual Machines に対する SLA はなく、いつでも排除される可能性があります。


**質問:** 排除された後も容量が必要な場合はどうすればよいですか?

**A:** 容量がすぐに必要な場合は、Azure Spot Virtual Machines ではなく、標準の VM を使用することをお勧めします。


**Q:** Azure Spot Virtual Machines のクォータはどのように管理されますか?

**A:** Azure Spot Virtual Machines には、個別のクォータ プールがあります。 スポット クォータは、VM とスケール セット インスタンスの間で共有されます。 詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md)」をご覧ください。


**Q:** Azure Spot Virtual Machines に追加のクォータを要求することはできますか?

**A:** はい。[標準のクォータ要求プロセス](../azure-portal/supportability/per-vm-quota-requests.md)を通じて、Azure Spot Virtual Machines のクォータを増やす要求を送信することができます。


**質問:** どこで質問を投稿できますか。

**A:** [Q&A](/answers/topics/azure-spot.html) で質問を投稿し、`azure-spot` のタグを付けることができます。 


**質問:** スポット VM の最大価格を変更する方法はありますか。

**A:** 最大価格を変更する前に、VM の割り当てを解除する必要があります。 その後、VM の **[Configuration]\(構成\)** セクションで、ポータルの最大価格を変更できます。 

## <a name="next-steps"></a>次のステップ
[CLI](./linux/spot-cli.md)、[ポータル](spot-portal.md)、[ARM テンプレート](./linux/spot-template.md)、または [PowerShell](./windows/spot-powershell.md) を使用して、Azure Spot Virtual Machines をデプロイします。

また、[Azure Spot Virtual Machine インスタンスを使用したスケール セット](../virtual-machine-scale-sets/use-spot.md)をデプロイすることもできます。

エラーが発生した場合は、[エラー コード](./error-codes-spot.md)を参照してください。
