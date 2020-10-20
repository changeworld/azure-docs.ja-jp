---
title: インクルード ファイル
description: インクルード ファイル
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: include
ms.date: 10/09/2020
ms.author: banders
ms.reviwer: ''
ms.custom: include file
ms.openlocfilehash: fb34b4ef1cf209a15c7e6ac9a59cc11dd4b0056e
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92026478"
---
## <a name="transform-data-before-using-large-usage-files"></a>大きな使用状況ファイルを使用する前にデータを変換する

使用状況または調整ファイルが大きすぎて開けない場合があります。 また、問題をトラブルシューティングするには、情報の一部だけが必要な場合もあります。 たとえば、特定のリソースのみ、または一部のサービスやリソース グループの消費量のみに関する情報のみが必要な場合があります。 ピボット テーブルを作成する前に、データを変換して集計することができます。

1. Excel で空のブックを開きます。
1. 上部のメニューで、 **[データ]**  >  **[テキスト/CSV から]** を選択し、使用状況ファイルを選択して、 **[インポート]** を選択します。
1. ウィンドウの下部で、 **[データの変換]** を選択します。 新しいウィンドウにデータの概要が表示されます。  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/summarized-data.png" alt-text="集計データを示す例" lightbox="./media/cost-management-billing-transform-data-before-using-large-usage-files/summarized-data.png" :::
1. 通常、MCA 使用状況ファイルには最初の行に列タイトルがあるため、Microsoft 顧客契約をお持ちの場合は、この手順をスキップして、次に進みます。 テーブルを作成してデータを準備します。 上位の行を削除して、タイトルだけを残します。 **[行の削除]**  >  **[上位の行の削除]** を選択します。  
     :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/remove-top-rows.png" alt-text="集計データを示す例" :::
1. [上位の行の削除] ウィンドウに、上部の削除する行数を入力します。 EA の場合は、通常 2 で、CSP の場合は、通常 1 です。 **[OK]** を選択します。
1. **[先頭の行を見出しとして使用]** を選択します。  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/use-first-row-as-header.png" alt-text="集計データを示す例" :::
    
    テーブル ビューには、上部に列タイトルが表示されます。
1. 次に、フィルターを追加します。 フィルター処理するには、各列タイトルの右側にあるセレクター矢印を使用します。 推奨されるフィルターは、サブスクリプション ID、サービス名 (Meter category)、インスタンス ID、リソース グループです。 同じドキュメント内で複数のフィルターを使用できます。 可能なすべてのフィルターを適用してドキュメントのサイズを縮小することをお勧めします。これにより、後の作業が容易になります。
1. フィルターを適用したら、 **[閉じて読み込む]** を選択します。  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/close-and-load.png" alt-text="集計データを示す例" :::

ファイルが読み込まれ、フィルター処理された使用状況データを含むテーブルが表示されます。 これで、新しいピボット テーブルを作成して、使用状況の問題をトラブルシューティングすることができます。