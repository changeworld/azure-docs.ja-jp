---
title: コマーシャル マーケットプレース プランの共同販売ステータスを確認する | Azure Marketplace
description: Microsoft コマーシャル マーケットプレース内のプランの共同販売ステータスを確認する方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.reviewer: stmummer
ms.date: 09/27/2021
ms.openlocfilehash: ab4eb4f35f23aeb00e136cd1e3668861aa6703ed
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2021
ms.locfileid: "129079772"
---
# <a name="verify-co-sell-status-of-a-commercial-marketplace-offer"></a>コマーシャル マーケットプレース プランの共同販売ステータスを確認する

パートナー センターのコマーシャル マーケットプレース プログラムにあるプランの **[オファーの概要]** ページで、プランの共同販売ステータスを確認できます。

## <a name="verify-co-sell-status"></a>共同販売ステータスを確認する

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[ワークスペース ビュー](#tab/workspaces-view)

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。
1. ホーム ページで、 **[マーケットプレースのオファー]** タイルを選択します。

    [ ![パートナー センターのホーム ページにある [マーケットプレースのオファー] タイルを示す図。](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. **[オファーのエイリアス]** 列で、目的のプランを選択します。 共同販売ステータスは、ページの [Marketplace Programs]\(マーケットプレース プログラム\) セクションに表示されます。

    [![パートナー センターの概要ページの [Marketplace Programs]\(マーケットプレース プログラム\) にある共同販売ステータスの表示。](./media/co-sell/co-sell-status.png)](./media/co-sell/co-sell-status.png#lightbox)

#### <a name="current-view"></a>[現在のビュー](#tab/current-view)

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。
1. 左側のナビゲーション メニューで、 **[商業マーケットプレース]**  >  **[概要]** の順に選択します。
1. **[オファーのエイリアス]** 列で、目的のプランを選択します。 共同販売ステータスは、ページの [Marketplace Programs]\(マーケットプレース プログラム\) セクションに表示されます。

    [![パートナー センターの概要ページの [Marketplace Programs]\(マーケットプレース プログラム\) にある共同販売ステータスの表示。](./media/co-sell/co-sell-status.png)](./media/co-sell/co-sell-status.png#lightbox)

---

次の表には、考えられるすべての共同販売ステータスが示されています。 各共同販売ステータスの要件については、「[共同販売の要件](co-sell-requirements.md)」を参照してください。

| Status | 解説 |
| ------------ | ------------- |
| 共同販売の準備が未完了 | [共同販売の準備完了ステータスの最小要件](co-sell-requirements.md#requirements-for-co-sell-ready-status)が満たされていません。 |
| 共同販売の準備完了 | [共同販売の準備完了ステータスの要件](co-sell-requirements.md#requirements-for-co-sell-ready-status)がすべて満たされています。 |
| Azure IP 共同販売インセンティブ | [これらの追加の要件](co-sell-requirements.md#requirements-for-azure-ip-co-sell-incentive-status)に加えて、共同販売の準備完了の要件が満たされています。 |
| Business Applications 共同販売インセンティブ | このステータスは、[ISV コネクト プログラム](business-applications-isv-program.md)の Dynamics 365 と Power Apps のプランに適用されます。[このステータスの要件](co-sell-requirements.md#requirements-for-business-applications-co-sell-incentive-status)がすべて満たされていることを示します。 |
|||

## <a name="next-steps"></a>次のステップ

- 共同販売の要件については、[共同販売の準備完了の要件](co-sell-requirements.md)に関するページを参照してください。
- 共同販売のためのプランの構成については、「[コマーシャル マーケットプレース プランの共同販売を構成する](co-sell-configure.md)」を参照してください。
