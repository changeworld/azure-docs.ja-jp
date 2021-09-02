---
title: SKU のアップグレード
titleSuffix: Azure Bastion
description: レベルを Basic から Standard SKU に変更する方法について説明します。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 07/13/2021
ms.author: cherylmc
ms.openlocfilehash: 8a2c35f2ef4dafe9024deab1c26116c0ca20e01e
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2021
ms.locfileid: "113733306"
---
# <a name="upgrade-a-sku-preview"></a>SKU をアップグレードする (プレビュー)

この記事は、Basic レベル (SKU) から Standard レベルにアップグレードする方法について説明します。 アップグレードした後は、Bastion を削除または再構成せずに Basic SKU に戻すことはできません。 プレビュー期間中、この設定は、Azure portal のみで構成できます。 ホストのスケーリングの詳細については、[SKU の構成設定](configuration-settings.md#skus) に関する記事を参照してください。 

## <a name="configuration-steps"></a>構成の手順

[!INCLUDE [Azure Bastion preview portal](../../includes/bastion-preview-portal-note.md)]

1. Azure portal 上で、Bastion ホストに移動します。
1. **[構成]** ページ の **[レベル]** で、ドロップダウンから **[Standard]** を選択します。

   :::image type="content" source="./media/upgrade-sku/select-sku.png" alt-text="[Standard] が選択された、レベル選択ドロップダウンのスクリーンショット。" lightbox="./media/upgrade-sku/select-sku-expand.png":::

1. **[適用]** をクリックして変更を適用します。

## <a name="next-steps"></a>次のステップ

* その他の構成情報については、[構成設定](configuration-settings.md)に関するページを参照してください。
* [Azure Bastion に関する FAQ](bastion-faq.md) を読む。
