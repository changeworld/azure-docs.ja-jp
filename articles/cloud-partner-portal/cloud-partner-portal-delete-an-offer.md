---
title: "Azure Marketplace からのプランの削除 | Microsoft ドキュメント"
description: "この記事は Azure Marketplace からのプランの削除に関する詳細な情報を提供"
services: cloud-partner-portal
documentationcenter: 
author: anuragdalmia
manager: hamidm
ms.service: cloud-partner-portal
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2017
ms.author: andalmia
translationtype: Human Translation
ms.sourcegitcommit: 233d62b176303ca384aae9453c2e125c0c70fad2
ms.openlocfilehash: f09be39b75bd0f39094a2ec08609c0c7ff03bc8e
ms.lasthandoff: 04/12/2017


---
# <a name="delete-an-offersku-from-azure-marketplace"></a>Azure Marketplace からプラン/SKU を削除する

さまざまな理由から、Marketplace からのプランの削除を決定する場合があります。  プランの削除により、新しい顧客のプラン購入やデプロイがなくなる場合はありますが、既存の顧客への影響はありません。 プランの終了とは、既存の顧客との間で、サービスやライセンス契約を終了するプロセスのことです。  プランの削除と終了に関するガイダンスとポリシーは、[Microsoft Marketplace パブリッシャー契約](http://go.microsoft.com/fwlink/?LinkID=699560) (セクション 7 を参照) および[参加ポリシー](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)に関するページ (セクション 6.2 を参照) で管理されています。   この記事では、サポートされているいくつかの削除のシナリオと、実行できる手順について説明します。

## <a name="delete-a-live-sku-from-azure-marketplace"></a>Azure Marketplace からライブ SKU を削除する

ライブ SKU を Azure Marketplace から削除するには、次の手順に従います。

1.  [クラウド パートナー ポータル](https://cloudpartner.azure.com/)にサインインします。

2.  **[すべてのプラン]** タブでプランを選択します。

3.  画面の左側にあるウィンドウで、**[SKU]** タブをクリックします。

4.  削除する SKU を選択し、その SKU について [削除] ボタンをクリックします。

5.  Azure Marketplace のプランを[再発行](./Cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md)します。

プランが Azure Marketplace でライブになると、Azure Marketplace および Azure Portal から SKU が削除されます。

## <a name="rollback-to-a-previous-sku-version"></a>以前のバージョンの SKU にロールバックする

ライブの現在のバージョンの SKU を Azure Marketplace から削除するには、ここでの手順に従います。 プロセスが完了すると、SKU が以前のバージョンにロールバックされます。

1.  [クラウド パートナー ポータル](https://cloudpartner.azure.com/)にサインインします。

2.  **[すべてのプラン]** タブでプランを選択します。

3.  画面の左側にあるウィンドウで、**[SKU]** タブをクリックします。

4.  発行済みディスク バージョンの一覧から最新の**ディスク バージョン**を削除します。

5.  Azure Marketplace のプランを[再発行](./Cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md)します。

Azure Marketplace でプランがライブになると、表示されている現在のバージョンの SKU が Azure Marketplace と Azure Portal から削除されます。 SKU は、以前のバージョンにロールバックされます。

## <a name="delete-a-live-offer"></a>ライブのプランを削除する

発行済みのプランの削除を要求する場合は、さまざまな注意事項があります。 ライブのプランを Azure Marketplace から削除するには、次の手順に従ってサポート チームからガイダンスを入手してください。

1.  こちらの[リンク](https://go.microsoft.com/fwlink/?linkid=844975)を使用して、サポート チケットを作成します

2.  **[問題の種類]** の一覧で **[Managing offers (プランの管理)]** を選択し、**[カテゴリ]** の一覧で **[Modifying an offer and/or SKU already in production (既に運用中のプランまたは SKU の変更)]** を選択します。

3.  要求を送信します。

サポート チームがプランの削除手順を説明します。

SKU/プランを削除しても既存の SKU/プランの購入には影響しないことに注意してください。 これらは引き続き、以前と同様に有効です。 ただし、今後の新しい購入では使用できません。  
