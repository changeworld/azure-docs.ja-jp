---
title: Azure Marketplace からオファーまたは SKU を削除する | Microsoft Docs
description: オファーまたは SKU を削除するための手順について説明します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: cc172e35e8964fad3b1a1410d1f1f3240c423ab3
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807606"
---
<a name="delete-an-offer-or-sku-from-azure-marketplace"></a>Azure Marketplace からオファーまたは SKU を削除する
==========================================

さまざまな理由から、Marketplace からのプランの削除を決定する場合があります。 プランの削除により、新しい顧客のプラン購入やデプロイがなくなる場合はありますが、既存の顧客への影響はありません。
プランの終了とは、既存の顧客との間で、サービスやライセンス契約を終了するプロセスのことです。 サービス/データの削除と終了に関するガイダンスおよびポリシーは、[Microsoft Marketplace Publisher Agreement](http://go.microsoft.com/fwlink/?LinkID=699560) (第 
7) 条参照) および[参加ポリシー](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (第 6.2 条参照) で定められています。 この記事では、サポートされているいくつかの削除のシナリオと、実行できる手順について説明します。

<a name="delete-a-live-sku-from-azure-marketplace"></a>Azure Marketplace からライブ SKU を削除する
----------------------------------------

次の手順を使用して、ライブ SKU を Azure Marketplace から削除できます。

1.  [クラウド パートナー ポータル](https://cloudpartner.azure.com/)にサインインします。

2.  **[すべてのプラン]** タブでプランを選択します。

3.  画面の左側にあるウィンドウで、**[SKU]** タブをクリックします。

4.  削除する SKU を選択し、その SKU の削除ボタンをクリックします。

5.  Azure Marketplace のプランを[再発行](./cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md)します。

プランが Azure Marketplace でライブになると、Azure Marketplace および Azure Portal から SKU が削除されます。

<a name="roll-back-to-a-previous-sku-version"></a>以前のバージョンの SKU にロールバックする
----------------------------------

ライブの現在のバージョンの SKU を Azure Marketplace から削除するには、ここでの手順に従います。 プロセスが完了すると、SKU が以前のバージョンにロールバックされます。

1.  [クラウド パートナー ポータル](https://cloudpartner.azure.com/)にサインインします。

2.  **[すべてのプラン]** タブでプランを選択します。

3.  画面の左側にあるウィンドウで、**[SKU]** タブをクリックします。

4.  発行済みディスク バージョンの一覧から最新の**ディスク バージョン**を削除します。

5.  Azure Marketplace のプランを[再発行](./cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md)します。

Azure Marketplace でプランがライブになると、表示されている現在のバージョンの SKU が Azure Marketplace と Azure Portal から削除されます。
SKU は、以前のバージョンにロールバックされます。

<a name="delete-a-live-offer"></a>ライブのプランを削除する
-------------------

発行済みのプランの削除を要求する場合は、さまざまな注意事項があります。 ライブのプランを Azure Marketplace から削除するには、次の手順に従ってサポート チームからガイダンスを入手してください。

1.  この[リンク](https://go.microsoft.com/fwlink/?linkid=844975)を使用してサポート チケットを提出するか、または CLoud パートナー ポータルの右上隅にある [サポート] をクリックします。

2.  **[問題の種類]** の一覧で特定のオファーの種類を選択し、**[カテゴリ]** の一覧で **[Remove a published offer]\(発行されたオファーの削除\)** を選択します。

3.  要求を送信します。

サポート チームがプランの削除手順を説明します。

>[!NOTE]
>SKU/オファーを削除しても、SKU/オファーの現在の購入には影響しません。 これらの SKU/オファーは引き続き、以前と同様に有効です。 ただし、今後の新しい購入では使用できません。
