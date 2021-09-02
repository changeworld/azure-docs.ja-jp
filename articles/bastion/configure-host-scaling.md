---
title: ホスト スケーリング用のスケール ユニットを追加する
titleSuffix: Azure Bastion
description: Azure Bastion にインスタンス (スケール ユニット) を追加する方法について説明します。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 08/30/2021
ms.author: cherylmc
ms.openlocfilehash: d5087994a72b52ce091dac98987b8e0bc60e287e
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224742"
---
# <a name="configure-host-scaling-preview"></a>ホストのスケーリングの構成 (プレビュー)

この記事は、追加の同時クライアント接続に対応するために、Azure Bastion にスケール ユニット (インスタンス) を追加するために役立ちます。 プレビュー期間中、この設定は、Azure portal のみで構成できます。 詳細については、[構成設定](configuration-settings.md#instance)に関するページを参照してください。 

## <a name="configuration-steps"></a>構成の手順

1. [Azure portal](https://ms.portal.azure.com) にサインインします。
1. Azure portal 上で、Bastion ホストに移動します。
1. ホスト スケーリング インスタンス数には Standard レベルが必要です。 **[構成]** ページ の **[レベル]** で、レベルが **Standard** であることを確認します。 レベルが Basic である場合は、ドロップダウンから **[Standard]** を選択します。 

   :::image type="content" source="./media/configure-host-scaling/select-sku.png" alt-text="レベルの選択のスクリーンショット。" lightbox="./media/configure-host-scaling/select-sku.png":::
1. スケーリングを構成するには、インスタンス数を調整します。 各インスタンスはスケール ユニットです。

   :::image type="content" source="./media/configure-host-scaling/instance-count.png" alt-text="インスタンス数スライダーのスクリーンショット。" lightbox="./media/configure-host-scaling/instance-count.png":::
1. **[適用]** をクリックして変更を適用します。

## <a name="next-steps"></a>次のステップ

* 詳細については、[Bastion に関する FAQ](bastion-faq.md) に関するページを参照してください。
