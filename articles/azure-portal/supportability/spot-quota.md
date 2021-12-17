---
title: スポット vCPU クォータを引き上げる
description: Azure portal でスポット vCPU クォータを引き上げるように要求する方法について説明します。
ms.date: 11/15/2021
ms.topic: how-to
ms.openlocfilehash: edbea31db418e3feae640e18be137b3ca09fac1f
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725434"
---
# <a name="increase-spot-vcpu-quotas"></a>スポット vCPU クォータを引き上げる

Azure Resource Manager では、仮想マシンに次の 2 種類の vCPU クォータを適用します。

- 標準 vCPU クォータ
- スポット vCPU クォータ

標準 vCPU クォータは、従量課金制 VM と予約 VM インスタンスに適用されます。 これらは、各リージョンのサブスクリプションごとに 2 つのレベルで適用されます。

- 最初のレベルは、リージョン vCPU クォータの合計です。
- 2 番目のレベルは、VM ファミリ vCPU クォータ (D シリーズ vCPU など) です。

スポット vCPU クォータは、すべての VM ファミリ (SKU) の[スポット仮想マシン (VM)](../../virtual-machines/spot-vms.md) に適用されます。

この記事では、スポット vCPU のクォータの引き上げを要求する方法について説明します。 [VM ファミリ vCPU クォータ](per-vm-quota-requests.md)または[リージョン別の vCPU クォータ](regional-quota-requests.md)の引き上げを要求することもできます。

## <a name="special-considerations"></a>特別な考慮事項

スポット vCPU のニーズを検討する場合は、次の点に注意してください。

- 新しいスポット VM をデプロイする場合は、すべてのスポット VM インスタンスの新規および既存の vCPU 使用量の合計が、承認されたスポット vCPU クォータ制限を超えてはいけません。 スポット クォータを超えた場合、スポット VM をデプロイできません。

- Azure で容量の回復が必要になると常に、Azure インフラストラクチャによってスポット VM が削除されます。

## <a name="increase-a-spot-vcpu-quota"></a>スポット vCPU クォータを引き上げる

スポット vCPU クォータの引き上げを要求するには、次の手順に従います。

1. Azure portal で、**サブスクリプション** を検索して選択します。
1. クォータを増やしたいサブスクリプションを選択します。
1. 左側のペインで、 **[使用量 + クォータ]** を選択します。
1. メイン ウィンドウでスポットを検索し、引き上げるリージョンの **[合計リージョン スポット vCPU]** を選択します。
1. **[クォータの詳細]** に、新しいクォータ制限を入力します。

   以下の例では、米国西部リージョンのすべての VM ファミリ vCPU のスポット vCPU に対して、新しいクォータ制限 103 を要求しています。

   :::image type="content" source="media/resource-manager-core-quotas-request/spot-quota.png" alt-text="Azure portal でのスポット vCPU のクォータ引き上げ要求のスクリーンショット。" lightbox="media/resource-manager-core-quotas-request/spot-quota.png":::

1. **[Save and continue]** (保存して続行) を選択します。

要求が確認され、要求が承認 (または拒否) されたかどうかが通知されます。 これは通常、数分以内に通知されます。 要求が拒否された場合は、サポート エンジニアが引き上げを支援できるようにサポート リクエストを開くことができるリンクが表示されます。

## <a name="increase-a-spot-quota-from-help--support"></a>[ヘルプとサポート] からスポット クォータを引き上げる

**[ヘルプとサポート]** からスポット vCPU クォータの引き上げを要求するには、Azure portal で新しいサポート要求を作成します。

1. **[問題の種類]** で、 **[サービスとサブスクリプションの制限 (クォータ)]** を選択します。
1. **[サブスクリプション]** で、クォータを引き上げるサブスクリプションを選択します。
1. **[クォータの種類]** で、 **[Compute-VM (cores-vCPUs) subscription limit increases]/(Compute-VM (cores-vCPU) サブスクリプションの制限の引き上げ/)** を選択します。

   :::image type="content" source="media/resource-manager-core-quotas-request/new-per-vm-quota-request.png" alt-text="Azure portal での VM ファミリの vCPU クォータの引き上げのサポート要求を示すスクリーンショット。":::

そこから、上記の手順に従って、スポット クォータの引き上げ要求を完了します。

## <a name="next-steps"></a>次のステップ

- [Azure スポット仮想マシン](../../virtual-machines/spot-vms.md)について学習します。
- [Azure サブスクリプションとサービスの制限、クォータ、制約](/azure/azure-resource-manager/management/azure-subscription-service-limits)について学習します。
