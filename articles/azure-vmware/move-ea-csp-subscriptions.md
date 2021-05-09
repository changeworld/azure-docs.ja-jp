---
title: Azure VMware Solution サブスクリプションを別のサブスクリプションに移動する
description: この記事では、Azure VMware Solution サブスクリプションを別のサブスクリプションに移動する方法について説明します。 課金などのさまざまな理由で、リソースを移動する場合があります。
ms.custom: subject-moving-resources
ms.topic: how-to
ms.date: 04/26/2021
ms.openlocfilehash: 0cd06eb72f8ed93cc5a491070baded76f9dc9f6f
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108145568"
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

1. 移動することを選択したリソースの検証を確認します。  これにより、移動対象として選択したすべてのリソースが検証されます。 選択したリソースの検証中、[検証状態] に **[検証を保留しています]** が表示されます。 

   :::image type="content" source="media/move-subscriptions/pending-move-resources-subscription-target.png" alt-text="移動するリソースを示すスクリーンショット。":::

1. 検証が成功したら、 **[次へ]** を選択してプライベート クラウドの移行を開始します。

   :::image type="content" source="media/move-subscriptions/move-resources-succeeded.png" alt-text="[成功] の検証状態を示すスクリーンショット。":::

1. 「関連付けられているツールとスクリプトは、それらを更新して新しいリソース ID を使用するまで機能しない旨を理解している」ことを示すチェックボックスをオンにします。 次に、 **[移動]** を選択します。

   :::image type="content" source="media/move-subscriptions/review-move-resources-subscription-target.png" alt-text="移動対象として選択したリソースの概要を示すスクリーンショット。":::

## <a name="verify-the-move"></a>移動を確認する

リソースの移動が完了すると、通知が表示されます。 

:::image type="content" source="media/move-subscriptions/notification-move-resources-subscription-target.png" alt-text="リソースの移動が完了した後の通知のスクリーンショット。":::

新しいサブスクリプションがプライベート クラウドの概要に表示されます。

:::image type="content" source="media/move-subscriptions/moved-subscription-target.png" alt-text="新しいサブスクリプションを示すスクリーンショット。":::

## <a name="next-steps"></a>次のステップ
各項目の詳細情報

- [ネットワーク リソースの移動ガイダンス](/azure/azure-resource-manager/management/move-limitations/networking-move-limitations)
- [仮想マシンの移動に関するガイダンス](/azure/azure-resource-manager/management/move-limitations/virtual-machines-move-limitations)
- [App Service リソースの移動に関するガイダンス](/azure/azure-resource-manager/management/move-limitations/app-service-move-limitations)



