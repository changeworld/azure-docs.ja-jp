---
title: SKU のアップグレード
titleSuffix: Azure Bastion
description: レベルを Basic から Standard SKU に変更する方法について説明します。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 08/30/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
ms.openlocfilehash: f88ab798618608b8dbb0e27fb772a82a5199a889
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131080861"
---
# <a name="upgrade-a-sku"></a>SKU のアップグレード

この記事は、Basic レベル (SKU) から Standard レベルにアップグレードする方法について説明します。 アップグレードした後は、Bastion を削除または再構成せずに Basic SKU に戻すことはできません。 現在、この設定は Azure portal のみで構成できます。 ホストのスケーリングの詳細については、[SKU の構成設定](configuration-settings.md#skus) に関する記事を参照してください。 

## <a name="configuration-steps"></a>構成の手順

1. [Azure portal](https://ms.portal.azure.com) にサインインします。
1. Azure portal 上で、Bastion ホストに移動します。
1. **[構成]** ページ の **[レベル]** で、ドロップダウンから **[Standard]** を選択します。

   :::image type="content" source="./media/upgrade-sku/select-sku.png" alt-text="[Standard] が選択された、レベル選択ドロップダウンのスクリーンショット。" lightbox="./media/upgrade-sku/select-sku-expand.png":::

1. **[適用]** をクリックして変更を適用します。

## <a name="next-steps"></a>次のステップ

* その他の構成情報については、[構成設定](configuration-settings.md)に関するページを参照してください。
* [Azure Bastion に関する FAQ](bastion-faq.md) を読む。
