---
title: Log Analytics と Excel を統合する
description: Excel に Log Analytics クエリを取り込み、Excel 内で結果を更新します。
ms.topic: conceptual
author: roygalMS
ms.author: roygal
ms.date: 06/10/2021
ms.openlocfilehash: 7cfe8ac2badd292b73b77dd13d7e7ca4bf24a428
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114474127"
---
# <a name="integrate-log-analytics-and-excel"></a>Log Analytics と Excel を統合する

M クエリと Log Analytics API を使用して、Azure Monitor Log Analytics と Microsoft Excel を統合できます。 この統合により、特定の数のレコードと MB のデータを送信できるようになります。 これらの制限は、Azure portal のセクションにある [Azure Monitor Log Analytics ワークスペースの制限](../service-limits.md#log-analytics-workspaces)に関するページに記載されています。 

> [!NOTE]
> Excel はローカル クライアント アプリケーションであるため、大量のデータ セットを処理する際のパフォーマンスと機能は、ローカルのハードウェアとソフトウェアの制限に影響されます。

## <a name="create-your-m-query-in-log-analytics"></a>Log Analytics で M クエリを作成する 

1. 通常どおり、Log Analytics で **クエリを作成して実行します**。 ユーザー インターフェイスでレコードの制限数に達しても心配はいりません。  ‘ago’ 関数や UI タイム ピッカーなどの相対日付を使用することをお勧めします。これにより、Excel で適切なデータ セットが更新されます。
  
2. **クエリをエクスポートします**。クエリとその結果に問題がなければ、Log Analytics の *[エクスポート]* メニューの下にある **[Power BI へエクスポート (M Query)]** メニュー選択を使用して M にクエリをエクスポートします。

:::image type="content" source="media/log-excel/export-query.png" alt-text="Log Analytics クエリのデータおよび [エクスポート] オプション" border="true":::



このオプションを選択すると、Excel で使用できる M コードを含む .txt ファイルがダウンロードされます。

上のクエリは、次の M コードをエクスポートします。 この例のクエリでエクスポートされた M コードの例をこちらに示します。

```m
/*
The exported Power Query Formula Language (M Language ) can be used with Power Query in Excel
and Power BI Desktop.
For Power BI Desktop follow the instructions below: 
1) Download Power BI Desktop from https://powerbi.microsoft.com/desktop/
2) In Power BI Desktop select: 'Get Data' -> 'Blank Query'->'Advanced Query Editor'
3) Paste the M Language script into the Advanced Query Editor and select 'Done'
*/

let AnalyticsQuery =
let Source = Json.Document(Web.Contents("https://api.loganalytics.io/v1/workspaces/ddcfc599-cae0-48ee-9026-fffffffffffff/query", 
[Query=[#"query"="

Heartbeat 
| summarize dcount(ComputerIP) by bin(TimeGenerated, 1h)    
| render timechart
",#"x-ms-app"="OmsAnalyticsPBI",#"timespan"="P1D",#"prefer"="ai.response-thinning=true"],Timeout=#duration(0,0,4,0)])),
TypeMap = #table(
{ "AnalyticsTypes", "Type" }, 
{ 
{ "string",   Text.Type },
{ "int",      Int32.Type },
{ "long",     Int64.Type },
{ "real",     Double.Type },
{ "timespan", Duration.Type },
{ "datetime", DateTimeZone.Type },
{ "bool",     Logical.Type },
{ "guid",     Text.Type },
{ "dynamic",  Text.Type }
}),
DataTable = Source[tables]{0},
Columns = Table.FromRecords(DataTable[columns]),
ColumnsWithType = Table.Join(Columns, {"type"}, TypeMap , {"AnalyticsTypes"}),
Rows = Table.FromRows(DataTable[rows], Columns[name]), 
Table = Table.TransformColumnTypes(Rows, Table.ToList(ColumnsWithType, (c) => { c{0}, c{3}}))
in
Table
in AnalyticsQuery
```

## <a name="connect-query-to-excel"></a>クエリを Excel に接続する 

クエリをインポートするには、次のようにします。 

1. Microsoft Excel を開きます。 
1. リボンで、 **[データ]** メニューに移動します。 **[データの取得]** を選択します。 **[その他のソース]** から **[空のクエリ]** を選択します。
 
   :::image type="content" source="media/log-excel/excel-import-blank-query.png" alt-text="Excel オプションで空からインポート" border="true":::

1. Power Query ウィンドウで、 **[詳細エディター]** を選択します。

   :::image type="content" source="media/log-excel/advanced-editor.png" alt-text="Excel の詳細クエリ エディター" border="true":::

 
1. 詳細エディターのテキストを Log Analytics からエクスポートしたクエリに置き換えます。

   :::image type="content" source="media/log-excel/advanced-editor-2.png" alt-text="空のクエリの作成" border="true":::
 
1. **[完了]** を選択し、次に **[Load and close]\(ロードして閉じる\)** を選択します。 Excel で、Log analytics API を使用してクエリが実行され、結果セットが表示されます。
 

   :::image type="content" source="media/log-excel/excel-query-result.png" alt-text="Excel でのクエリ結果" border="true":::

> [!Note]
> レコード数が予想より少ない場合、結果の量は 61 MiB の制限を超える可能性があります。 クエリで `project` または `project-away` を使用して、列を必要なものに制限してみてください。

##  <a name="refreshing--data"></a>データの更新

Excel から直接データを更新できます。 Excel リボンの **[データ]** メニュー グループで、 **[更新]** ボタンを選択します。
 
## <a name="next-steps"></a>次のステップ

Excel と外部データ ソースの統合について詳しくは、「[外部データ ソースからデータをインポートする (Power Query)](https://support.office.com/article/import-data-from-external-data-sources-power-query-be4330b3-5356-486c-a168-b68e9e616f5a)」を参照してください。
