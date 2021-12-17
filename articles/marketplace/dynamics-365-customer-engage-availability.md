---
title: Microsoft AppSource (Azure Marketplace) で利用可能な Dynamics 365 for Customer Engagement & Power Apps プランを構成します。
description: Microsoft AppSource (Azure Marketplace) で利用可能な Dynamics 365 for Customer Engagement & Power Apps プランを構成します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 04/20/2021
ms.openlocfilehash: 779b580c38152967d7bbe64bca3455ec304be266
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864661"
---
# <a name="configure-dynamics-365-for-customer-engagement--power-apps-offer-availability"></a>Dynamics 365 for Customer Engagement & Power Apps プランの構成の利用可能性

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

左側のナビゲーション メニューの次のタブに進む前に、 **[下書きの保存]** を選択します。

## <a name="next-steps"></a>次のステップ

次のいずれかの操作を行います。

- Microsoft によるアプリ ライセンス管理を有効にすることを選択した場合は、「[Dynamics 365 for Customer Engagement & Power Apps プランの作成](dynamics-365-customer-engage-plans.md)」に進んでください。
- それ以外の場合は、[オファーの技術的な構成の設定](dynamics-365-customer-engage-technical-configuration.md)に関する記事に進んでください。
