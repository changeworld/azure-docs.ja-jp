---
title: Seller Insights リリース ノート | Microsoft Docs
description: Seller Insights の機能への変更に関する情報を提供します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
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
ms.openlocfilehash: ec3784a91f8aeb7f0fedd13c9ab86a844832a578
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807179"
---
<a name="seller-insights-release-notes"></a>Seller Insights リリース ノート 
===============================
(リリース日: 2018 年 7 月 28 日)

この記事では、[Cloud パートナー ポータル](https://cloudpartner.azure.com/#insights)で Seller Insights 機能への変更に関する情報を提供します。


<a name="release-highlights"></a>リリースの概要
------------------

-   *見積価格*では、通貨換算の影響と共に顧客請求金額のビューを提供します。
-   *予測支払額*では、可能性のある支払額に以前のビューを提供します。
-  *使用状況の参照識別子*では、顧客の使用状況と支払額を含む注文の間でデータの忠実性を提供します。
-   *1 日単位の使用状況*では、顧客の使用状況により細かく、より優れた分析情報を提供します。


<a name="changes-to-data-structure-and-taxonomy"></a>データ構造と分類への変更
--------------------------------------

次の表では、このリリースで追加または著しく変更されたメトリックを一覧します。 

| **新しい用語**                   |    **定義**                                                             |
|--------------------------------|  ---------------------------------------------------------------------------- |
| 価格 (CC)                     | 指定された SKU に対する使用状況のユニットの価格 (顧客の通貨)。       |
| 見積拡張価格 (CC) | 指定された SKU に対する使用状況のユニット数の見積拡張価格 (顧客の通貨)。 この値は、丸めエラーまたはトランザクション エラーのため、正確ではない可能性があります。   |
| 発行元の通貨 (PC)        | 支払に対して発行元によって指定される通貨。                               |
| 見積価格 (PC)           | データの使用状況が計算された外国為替換算に基づく指定された SKU に対する使用状況のユニットの見積価格 (発行元の通貨)。 この値は、丸めエラーまたはトランザクション エラーのため、正確ではない可能性があります。   |
| 見積拡張価格 (PC) | データの使用状況が計算された外国為替換算に基づく指定された SKU に対する使用状況のユニット数の見積拡張価格 (発行元の通貨)。 この値は、丸めエラーまたはトランザクション エラーのため、正確ではない可能性があります。 |
| 見積支払額 (PC)          | データの使用状況が計算された外国為替換算に基づく指定された SKU に対する使用状況のユニット数の見積支払額 (発行元の通貨)。 この値は、丸めエラーまたはトランザクション エラーのため、正確ではない可能性があります。   |
| 使用状況の参照                | 支払額レポートのエントリと関連付けられた、指定の SKU に対して顧客使用状況の 1 日または複数の日数の識別子。 |
|  |  |
