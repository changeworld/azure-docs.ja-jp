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
ms.openlocfilehash: 5ed0dd6ad86b5f7758f0e1972867876eedc3b51a
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225947"
---
# <a name="upgrade-a-sku-preview"></a>SKU をアップグレードする (プレビュー)

この記事は、Basic レベル (SKU) から Standard レベルにアップグレードする方法について説明します。 アップグレードした後は、Bastion を削除または再構成せずに Basic SKU に戻すことはできません。 プレビュー期間中、この設定は、Azure portal のみで構成できます。 ホストのスケーリングの詳細については、[SKU の構成設定](configuration-settings.md#skus) に関する記事を参照してください。 

## <a name="configuration-steps"></a>構成の手順

1. [Azure portal](https://ms.portal.azure.com) にサインインします。
1. Azure portal 上で、Bastion ホストに移動します。
1. **[構成]** ページ の **[レベル]** で、ドロップダウンから **[Standard]** を選択します。

   :::image type="content" source="./media/upgrade-sku/select-sku.png" alt-text="[Standard] が選択された、レベル選択ドロップダウンのスクリーンショット。" lightbox="./media/upgrade-sku/select-sku-expand.png":::

1. **[適用]** をクリックして変更を適用します。

## <a name="next-steps"></a>次のステップ

* その他の構成情報については、[構成設定](configuration-settings.md)に関するページを参照してください。
* [Azure Bastion に関する FAQ](bastion-faq.md) を読む。
