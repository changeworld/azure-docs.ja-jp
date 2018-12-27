---
title: Azure Marketplace からプラン/SKU を削除する
description: Azure Marketplace からプラン/SKU を削除する
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: pbutlerm
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 5ad106d45c8bae2d41e0bde74b27f80f4d8ab79b
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2018
ms.locfileid: "51241278"
---
<a name="delete-an-offersku-from-azure-marketplace"></a>Azure Marketplace からプラン/SKU を削除する 
==========================================

さまざまな理由から、Marketplace からのプランの削除を決定する場合があります。 新規のお客様がプランを購入したり、デプロイしたりできなくなりますが、既存のお客様に影響が及ぶことはありません。
プランの終了とは、既存の顧客との間で、サービスやライセンス契約を終了するプロセスのことです。 サービス/データの削除と終了に関するガイダンスおよびポリシーは、[Microsoft Marketplace Publisher Agreement](https://go.microsoft.com/fwlink/?LinkID=699560) (第 
7) 条参照) および[参加ポリシー](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (第 6.2 条参照) で定められています。 サポートされているいくつかの削除のシナリオと、実行できる手順が説明されています。

<a name="delete-a-live-sku-from-azure-marketplace"></a>Azure Marketplace からライブ SKU を削除する 
----------------------------------------

ライブ SKU を Azure Marketplace から削除するには、次の手順に従います。

1.  [クラウド パートナー ポータル](http://cloudpartner.azure.com)にサインインします。
2.  [すべてのプラン] タブでプランを選択します。
3.  画面の左側にあるウィンドウで、[SKU] タブをクリックします。
4.  削除する SKU を選択し、その SKU について [削除] ボタンをクリックします。
5.  Azure Marketplace のプランを再発行します。

プランが Azure Marketplace でライブになると、Azure Marketplace および Azure Portal から SKU が削除されます。

<a name="roll-back-to-a-previous-sku-version"></a>以前のバージョンの SKU にロールバックする 
----------------------------------

ライブの現在のバージョンの SKU を Azure Marketplace から削除するには、ここでの手順に従います。 プロセスが完了すると、SKU が以前のバージョンにロールバックされます。

1.  [クラウド パートナー ポータル](http://cloudpartner.azure.com)にサインインします。
2.  [すべてのプラン] タブでプランを選択します。
3.  画面の左側にあるウィンドウで、[SKU] タブをクリックします。
4.  発行済みパッケージの一覧から最新のパッケージ バージョンを削除します。
5.  Azure Marketplace のプランを再発行します。

Azure Marketplace でプランがライブになると、表示されている現在のバージョンの SKU が Azure Marketplace と Azure Portal から削除されます。
SKU は、以前のバージョンにロールバックされます。

<a name="delete-a-live-offer"></a>ライブのプランを削除する 
-------------------

ライブのプランを Azure Marketplace から削除するには、次の手順に従ってサポート チームからガイダンスを入手してください。

1.  こちらの[リンク](https://go.microsoft.com/fwlink/?linkid=844975)を使用して、サポート チケットを作成します
2.  [問題の種類] の一覧で [Managing offers \(プランの管理\)] を選択し、[カテゴリ] の一覧で [Modifying an offer and/or SKU already in production \(既に運用中のプランまたは SKU の変更\)] を選択します。
3.  要求を送信します。

サポート チームがプランの削除手順を説明します。

SKU/プランを削除しても既存の SKU/プランの購入には影響しません。 これまでのように機能し続けますが、今後の新しい購入では使用できません。
