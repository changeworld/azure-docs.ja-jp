---
title: Azure の Enterprise 価格を表示する | Microsoft Docs
description: Enterprise Agreement の価格で価格を表示またはダウンロードしたりコストを見積もったりする方法について説明します。
services: ''
documentationcenter: ''
author: adpick
manager: dougeby
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: cwatson
ms.custom: seodec18
ms.openlocfilehash: b1f3d6ec2a8563ccfffd1e1c3c7cf1869a42769a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090858"
---
# <a name="view-your-organizations-azure-enterprise-agreement-pricing"></a>組織の Azure Enterprise Agreement の価格を表示する 

Enterprise Agreement (EA) をお持ちの Azure カスタマーは Azure portal から EA 価格を表示したり、ダウンロードしたりできます。 組織の EA 価格と Azure 料金計算ツールを利用してコストを見積もることもできます。

組織のエンタープライズ管理者が設定したポリシーに基づきますが、特定の管理者ロールのみが組織の EA 価格情報へのアクセスを提供します。 詳細については、「[Understand Azure Enterprise Agreement administrative roles in Azure](billing-understand-ea-roles.md)」(Azure の Azure Enterprise Agreement 管理者ロールを理解する) を参照してください。

## <a name="view-and-download-ea-pricing"></a>EA 価格を表示し、ダウンロードする

 
1. エンタープライズ管理者として [Azure portal](https://portal.azure.com/) にサインインします。 
1. **[コストの管理と請求]** で検索します。

   ![Azure portal の検索を表示するスクリーンショット](./media/billing-ea-pricing/portal-cm-billing-search.png) 

1. 課金アカウントの下で、**[使用量 + 請求金額]** を選択します。

   ![[請求] で使用量と請求金額を確認できることを示すスクリーンショット](./media/billing-ea-pricing/ea-pricing-usage-charges-nav.png)

1. その月の ![[Azure portal の検索を表示するスクリーンショット]](./media/billing-ea-pricing/download-icon.png) を選択し、**[ダウンロード]** を選択します。
1. **[価格シート]** の下で **[CSV のダウンロード]** を選択します。

   ![価格シートの [CSV のダウンロード] ボタンを示すスクリーンショット](./media/billing-ea-pricing/download-ea-price-sheet.png)

## <a name="estimate-costs-with-ea-pricing"></a>EA 価格でコストを見積もる

1. [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator)に移動します。
1. 右上にある **[サインイン]** を選択します。
1. **[Programs and Offer]\(プログラムとオファー\)** の **[ライセンス プログラム]** の下で、**[Enterprise Agreement (EA)]** を選択します。
1. **[Programs and Offer]\(プログラムとオファー\)** の **[選択された契約]** の下で、**[選択されていません]** を選択します。

    ![価格シートの [CSV のダウンロード] ボタンを示すスクリーンショット](./media/billing-ea-pricing/ea-pricing-calculator-estimate.png)

1. 組織を選択します。
1. **[適用]** を選択します。
1. 製品を検索し、見積もりに追加します。
1. 表示された見積価格は、選択した組織に対する価格に基づきます。

## <a name="next-steps"></a>次の手順

- [Azure の課金情報へのアクセスの管理](billing-manage-access.md)
- [Enterprise Agreement をお持ちの Azure カスタマーに対する請求について](billing-understand-your-bill-ea.md)
