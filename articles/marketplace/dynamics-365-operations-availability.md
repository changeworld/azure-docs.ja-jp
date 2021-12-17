---
title: Microsoft AppSource (Azure Marketplace) で Dynamics 365 for Operations オファーの可用性を構成する
description: Microsoft AppSource (Azure Marketplace) で Dynamics 365 for Operations オファーの可用性を構成します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 05/19/2021
ms.openlocfilehash: 68e633c520b828f3cda2f2ba50f3008dccceeb3e
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110483392"
---
# <a name="configure-dynamics-365-for-operations-offer-availability"></a>Dynamics 365 for Operations オファーの可用性を構成する

このページでは、市場やプレビューの対象ユーザーを含め、プランを利用可能にする場所と方法を定義できます。

## <a name="markets"></a>市場

プランを利用できる市場を指定するには、 **[市場を編集]** を選択します。

**[市場を選択]** ポップアップウィンドウで、少なくとも 1 つの市場を選択します。 **[すべて選択]** を選んで使用可能なすべての市場でオファーを使用できるようにするか、必要な特定の市場のみを選択します。 完了したら、 **[保存]** をクリックします。

ここで選択した内容は、新規購入にのみ適用されます。ユーザーが既に特定の市場でアプリを入手しているときに、後でその市場が削除された場合、その市場で既にオファーを入手したユーザーは引き続きそれを使用できますが、その市場で新しいユーザーがオファーを入手することはできません。

> [!IMPORTANT]
> ここやパートナー センターにこれらの要件が記載されていない場合でも、あらゆる地域の法的要件を満たす必要があります。 すべての市場を選択した場合でも、国や地域によっては、地域の法律や規制、その他の要因により、特定のオファーが一覧表示されない場合があります。

## <a name="preview-audience"></a>プレビュー対象ユーザー

オファーをより広範なマーケットプレース オファーに公開する前に、まず、限定された **プレビュー対象ユーザー** に対してオファーを利用できるようにする必要があります。 ここでは、**非表示キー** (小文字と数字のみを使用する任意の文字列) を入力します。 プレビュー対象ユーザーのメンバーは、この非表示キーをトークンとして使用して、マーケットプレースでオファーのプレビューを表示できます。

オファーを利用可能にしてプレビューの制限を解除する準備ができたら、**非表示キー** を削除して再度発行する必要があります。

左側のナビゲーション メニューの次のタブである **[技術的な構成]** に進む前に、 **[下書きの保存]** を選択してください。

## <a name="next-steps"></a>次のステップ

- [プランの技術的な構成を設定する](dynamics-365-operations-technical-configuration.md)
