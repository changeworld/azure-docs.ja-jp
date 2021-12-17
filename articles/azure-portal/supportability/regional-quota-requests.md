---
title: リージョンの vCPU クォータの増加
description: Azure portal でリージョンの vCPU クォータ制限の引き上げを要求する方法について説明します。
ms.date: 11/15/2021
ms.topic: how-to
ms.custom: references-regions
ms.openlocfilehash: 45337a6ce029fe3bf8442ac5343ce9145faf904f
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132717089"
---
# <a name="increase-regional-vcpu-quotas"></a>リージョンの vCPU クォータの増加

Azure Resource Manager では、仮想マシンに次の 2 種類の vCPU クォータを適用します。

- 標準 vCPU クォータ
- スポット vCPU クォータ

標準 vCPU クォータは、従量課金制 VM と予約 VM インスタンスに適用されます。 これらは、各リージョンのサブスクリプションごとに 2 つのレベルで適用されます。

- 最初のレベルは、リージョン vCPU クォータの合計です。
- 2 番目のレベルは、VM ファミリ vCPU クォータ (D シリーズ vCPU など) です。

この記事では、特定のリージョン内のすべての VM に対してリージョン vCPU クォータの引き上げを要求する方法について説明します。 [VM ファミリ vCPU クォータ](per-vm-quota-requests.md)または[スポット vCPU クォータ](spot-quota.md)の引き上げを要求することもできます。

## <a name="special-considerations"></a>特別な考慮事項

リージョン間で vCPU のニーズを検討する場合は、次の点を考慮してください。

- リージョン vCPU クォータは、特定のリージョン内のすべての VM シリーズに適用されます。 結果として、サブスクリプション内の各リージョンに必要な vCPU の数が決定されます。 各リージョンに十分な vCPU クォータがない場合は、そのリージョンの vCPU クォータを引き上げる要求を送信してください。 たとえば、西ヨーロッパで 30 の vCPU が必要で、クォータが十分ではない場合は、特に西ヨーロッパで 30 の vCPU のクォータを要求します。 そうすると、他のリージョンのサブスクリプションの vCPU クォータは引き上げられません。 西ヨーロッパの vCPU クォータ制限だけが 30 vCPU に引き上げられます。

- VM シリーズの vCPU クォータの引き上げを要求すると、Azure はリージョンの vCPU クォータ制限を同じ量だけ引き上げます。

- 新しいサブスクリプションを作成するとき、リージョンの vCPU の合計の既定値が、すべての個別 VM シリーズに対する既定の vCPU クォータの合計と等しくない場合があります。 この不一致により、サブスクリプションで、デプロイしたい個別の VM シリーズごとのクォータは十分になる可能性があります。 一方、すべてのデプロイに対するリージョンの vCPU の合計に対応するクォータは十分でない可能性があります。 この場合、リージョンの vCPU クォータのクォータ制限を明示的に引き上げるための要求を送信する必要があります。

## <a name="increase-a-regional-vcpu-quota"></a>リージョンの vCPU クォータを引き上げる

**[使用状況とクォータ]** からリージョン vCPU クォータを要求するには、次の手順を実行します。

1. Azure portal で、**サブスクリプション** を検索して選択します。

1. クォータを増やしたいサブスクリプションを選択します。

1. 左側のペインで、 **[使用量 + クォータ]** を選択します。 フィルターを使用して、使用量別にクォータを表示します。

1. メイン ウィンドウで、 **[リージョンにおける合計 vCPU]** を選択し、鉛筆アイコンを選択します。 次の例は、米国北部リージョンのリージョン vCPU クォータを示しています。

   :::image type="content" source="media/resource-manager-core-quotas-request/regional-quota-total.png" alt-text="Azure portal で、[リージョンにおける合計 vCPU] を示す [使用状況とクォータ] 画面のスクリーンショット。" lightbox="media/resource-manager-core-quotas-request/regional-quota-total.png":::

1. **[クォータの詳細]** に、新しいクォータ制限を入力し、 **[保存してから続ける]** を選択します。

   要求が確認され、要求が承認 (または拒否) されたかどうかが通知されます。 これは通常、数分以内に通知されます。 要求が拒否された場合は、サポート エンジニアが引き上げを支援できるようにサポート リクエストを開くことができるリンクが表示されます。

> [!TIP]
> 同時に複数の引き上げを要求することもできます。 詳細については、「[1 つの要求で複数の VM ファミリ CPU クォータを引き上げる](per-vm-quota-requests.md#increase-multiple-vm-family-cpu-quotas-in-one-request)」を参照してください。

## <a name="increase-a-regional-quota-from-help--support"></a>[ヘルプとサポート] からリージョン クォータを引き上げる

**[ヘルプとサポート]** から VM ファミリあたりの標準 vCPU クォータの引き上げを要求するには、Azure portal で新しいサポート要求を作成します。

1. **[問題の種類]** で、 **[サービスとサブスクリプションの制限 (クォータ)]** を選択します。
1. **[サブスクリプション]** で、クォータを引き上げるサブスクリプションを選択します。
1. **[クォータの種類]** で、 **[Compute-VM (cores-vCPUs) subscription limit increases]/(Compute-VM (cores-vCPU) サブスクリプションの制限の引き上げ/)** を選択します。

   :::image type="content" source="media/resource-manager-core-quotas-request/new-per-vm-quota-request.png" alt-text="Azure portal での VM ファミリの vCPU クォータの引き上げのサポート要求を示すスクリーンショット。":::

そこから、上記の手順に従って、リージョン クォータの引き上げ要求を完了します。

## <a name="next-steps"></a>次のステップ

- [Azure リージョンとその場所の一覧](https://azure.microsoft.com/regions/)をご確認ください。
- [仮想マシンの Azure リージョン](../../virtual-machines/regions.md)の概要と、特定のリージョンで VM のパフォーマンス、可用性、冗長性を最大化する方法について説明します。
