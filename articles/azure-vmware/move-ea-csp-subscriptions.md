---
title: Azure VMware Solution サブスクリプションを別のサブスクリプションに移動する
description: この記事では、Azure VMware Solution サブスクリプションを別のサブスクリプションに移動する方法について説明します。 課金などのさまざまな理由で、リソースを移動する場合があります。
ms.custom: subject-moving-resources
ms.topic: how-to
ms.date: 04/26/2021
ms.openlocfilehash: b4541bf1cf13a8b4c090365c31c2403685a69c7f
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322229"
---
# <a name="move-azure-vmware-solution-subscription-to-another-subscription"></a>Azure VMware Solution サブスクリプションを別のサブスクリプションに移動する

この記事では、Azure VMware Solution サブスクリプションを別のサブスクリプションに移動する方法について説明します。 サブスクリプションは、課金などのさまざまな理由で移動する場合があります。

## <a name="prerequisites"></a>[前提条件]
**ソース** と **ターゲット** の両方のサブスクリプションに対して、少なくとも共同作成者権限を持っている必要があります。 

>[!IMPORTANT]
>VNet と VNet ゲートウェイを、あるサブスクリプションから別のサブスクリプションに移動することはできません。 また、サブスクリプションを移動しても、vCenter、NSX、ワークロード仮想マシンなどの管理とワークロードには影響しません。

## <a name="prepare-and-move"></a>準備と移動 

1. Azure portal で、移動するプライベート クラウドを選択します。

   :::image type="content" source="media/move-subscriptions/source-subscription-id.png" alt-text="選択したプライベート クラウドの概要の詳細を示すスクリーンショット。":::

1. コマンド プロンプトで、コンポーネントとワークロードに対して ping を実行し、それらが同じサブスクリプションから ping を実行していることを確認します。  

   :::image type="content" source="media/move-subscriptions/verify-components-workloads.png" alt-text="ping コマンドと ping の結果を示すスクリーンショット。":::

1. **[サブスクリプション (変更)]** リンクを選択します。

   :::image type="content" source="media/move-subscriptions/private-cloud-overview-subscription-id.png" alt-text="プライベート クラウドの詳細を示すスクリーンショット。"::: 

1. **[ターゲット]** のサブスクリプションの詳細を指定し、 **[次へ]** を選択します。

   :::image type="content" source="media/move-subscriptions/move-resources-subscription-target.png" alt-text="ターゲット リソースのスクリーンショット。":::

1. 移動することを選択したリソースの検証を確認します。 検証中、状態に **[検証を保留しています]** が表示されます。 

   :::image type="content" source="media/move-subscriptions/pending-move-resources-subscription-target.png" alt-text="移動するリソースを示すスクリーンショット。":::

1. 検証が成功したら、 **[次へ]** を選択してプライベート クラウドの移行を開始します。

   :::image type="content" source="media/move-subscriptions/move-resources-succeeded.png" alt-text=" &quot;成功&quot; の検証状態を示すスクリーンショット。":::

1. 「関連付けられているツールとスクリプトは、それらを更新して新しいリソース ID を使用するまで機能しない旨を理解している」ことを示すチェック ボックスをオンにします。 次に、 **[移動]** を選択します。

   :::image type="content" source="media/move-subscriptions/review-move-resources-subscription-target.png" alt-text="移動対象として選択したリソースの概要を示すスクリーンショット。":::

## <a name="verify-the-move"></a>移動を確認する

リソースの移動が完了すると、通知が表示されます。 

:::image type="content" source="media/move-subscriptions/notification-move-resources-subscription-target.png" alt-text="リソースの移動が完了した後の通知のスクリーンショット。":::

新しいサブスクリプションがプライベート クラウドの概要に表示されます。

:::image type="content" source="media/move-subscriptions/moved-subscription-target.png" alt-text="新しいサブスクリプションを示すスクリーンショット。":::

## <a name="next-steps"></a>次のステップ
各項目の詳細情報

- [リージョン間で Azure VMware Solution を移動する](move-azure-vmware-solution-across-regions.md)
- [ネットワーク リソースの移動ガイダンス](../azure-resource-manager/management/move-limitations/networking-move-limitations.md)
- [仮想マシンの移動に関するガイダンス](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md)
- [App Service リソースの移動に関するガイダンス](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)
