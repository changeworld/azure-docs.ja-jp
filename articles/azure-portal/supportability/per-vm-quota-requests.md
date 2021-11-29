---
title: VM ファミリの vCPU クォータの増加
description: Azure portal で VM ファミリの vCPU クォータ制限の引き上げ (これにより、リージョン全体の vCPU の制限が同じ量だけ引き上げられる) を要求する方法について説明します。
ms.date: 11/15/2021
ms.topic: how-to
ms.openlocfilehash: 7941c19de7143bc794ffd6d197d31c6db6f0a0d5
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132714371"
---
# <a name="increase-vm-family-vcpu-quotas"></a>VM ファミリの vCPU クォータの増加

Azure Resource Manager では、仮想マシンに次の 2 種類の vCPU クォータを適用します。

- 標準 vCPU クォータ
- スポット vCPU クォータ

標準 vCPU クォータは、従量課金制 VM と予約 VM インスタンスに適用されます。 これらは、各リージョンのサブスクリプションごとに 2 つのレベルで適用されます。

- 最初のレベルは、リージョン vCPU クォータの合計です。
- 2 番目のレベルは、VM ファミリ vCPU クォータ (D シリーズ vCPU など) です。

この記事では、VM ファミリ vCPU クォータの引き上げを要求する方法について示します。 [リージョン別の vCPU クォータ](regional-quota-requests.md)または[スポット vCPU クォータ](spot-quota.md)の引き上げを要求することもできます。

## <a name="increase-a-vm-family-vcpu-quota"></a>VM ファミリの vCPU クォータの引き上げ

**[使用状況とクォータ]** から VM ファミリあたりの標準 vCPU クォータの引き上げを要求するには、次の手順を実行します。

1. Azure portal で、**サブスクリプション** を検索して選択します。
1. クォータを増やしたいサブスクリプションを選択します。
1. 左側のペインで、 **[使用量 + クォータ]** を選択します。
1. メイン ウィンドウで、増やす VM ファミリ vCPU クォータを見つけて、鉛筆アイコンを選択します。 次の例は、米国東部リージョンに導入された標準 DSv3 ファミリ vCPU を示しています。 **[使用状況]** 列には、現在のクォータ使用量と現在のクォータ制限が表示されます。
1. **[クォータの詳細]** に、新しいクォータ制限を入力し、 **[保存してから続ける]** を選択します。

   :::image type="content" source="media/resource-manager-core-quotas-request/quota-increase-example.png" alt-text="[使用状況とクォータ] ウィンドウのスクリーンショット。" lightbox="media/resource-manager-core-quotas-request/quota-increase-example.png":::

要求が確認され、要求が承認 (または拒否) されたかどうかが通知されます。 これは通常、数分以内に通知されます。 要求が拒否された場合は、サポート エンジニアが引き上げを支援できるようにサポート リクエストを開くことができるリンクが表示されます。

> [!NOTE]
> VM ファミリのクォータを引き上げる要求が承認されると、Azure では、VM がデプロイされているリージョンのリージョン vCPU クォータが自動的に増やされます。

> [!TIP]
> 仮想マシンを作成またはサイズ変更して VM のサイズを選択すると、 **[クォータの不足 - ファミリの制限]** の下にいくつかのオプションが表示される場合があります。 その場合は、 **[クォータの要求]** リンクを選択して、VM の作成ページから直接クォータの引き上げを要求できます。

## <a name="increase-a-vm-family-vcpu-quota-from-help--support"></a>VM ファミリの vCPU クォータを [ヘルプとサポート] から引き上げる

**[ヘルプとサポート]** から VM ファミリあたりの標準 vCPU クォータの引き上げを要求するには、Azure portal で新しいサポート要求を作成します。

1. **[問題の種類]** で、 **[サービスとサブスクリプションの制限 (クォータ)]** を選択します。
1. **[サブスクリプション]** で、クォータを引き上げるサブスクリプションを選択します。
1. **[クォータの種類]** で、 **[Compute-VM (cores-vCPUs) subscription limit increases]/(Compute-VM (cores-vCPU) サブスクリプションの制限の引き上げ/)** を選択します。

   :::image type="content" source="media/resource-manager-core-quotas-request/new-per-vm-quota-request.png" alt-text="Azure portal での VM ファミリの vCPU クォータの引き上げのサポート要求を示すスクリーンショット。":::

そこから、上記の手順に従って、クォータの引き上げ要求を完了します。

## <a name="increase-multiple-vm-family-cpu-quotas-in-one-request"></a>1 つの要求で複数の VM ファミリ CPU クォータを引き上げる

同時に複数の引き上げを要求することもできます (一括要求)。 クォータの引き上げを一括要求する場合、1 つのクォータを引き上げる要求よりも時間がかかる場合があります。

複数の引き上げをまとめて要求するには、最初に前述のように **[使用量とクォータ]** ページに移動します。 次に、次を実行します。

1. 画面の上部付近の **[引き上げを要求する]** を選択します。
1. **[クォータの種類]** で、 **[Compute-VM (cores-vCPUs) subscription limit increases]/(Compute-VM (cores-vCPU) サブスクリプションの制限の引き上げ/)** を選択します。
1. **[次へ]** を選び、 **[その他の詳細]** 画面に移動し、 **[詳細を入力]** を選択します。
1. **[クォータの詳細]** 画面で、次のようにします。

   :::image type="content" source="media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png" alt-text="[クォータの詳細] 画面と選択を示すスクリーンショット。":::

   1. **[デプロイ モデル]** で、 **[Resource Manager]** が選択されていることを確認します。
   1. **[場所]** で、クォータを引き上げるすべてのリージョンを選択します。
   1. 選択したリージョンごとに、 **[クォータ]** ドロップダウン リストから 1 つ以上の VM シリーズを選択します。
   1. 選択した **VM シリーズ** ごとに、このサブスクリプションに適用する新しい vCPU 制限を入力します。
   1. 完了したら、 **[保存して続行]** をクリックします。
1. 連絡先の詳細を入力または確認し、 **[次へ]** を選択します。
1. 最後に、 **[確認と作成]** ページですべてが正しく表示されていることを確認し、 **[作成]** を選択して要求を送信します。

## <a name="next-steps"></a>次のステップ

- [vCPU クォータ](/azure/virtual-machines/windows/quotas)の詳細を確認します。
- [Azure サブスクリプションとサービスの制限、クォータ、制約](/azure/azure-resource-manager/management/azure-subscription-service-limits)について学習します。
