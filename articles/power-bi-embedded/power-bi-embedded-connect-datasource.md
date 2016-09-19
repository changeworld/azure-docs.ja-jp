<properties
   pageTitle="Microsoft Power BI Embedded - データ ソースへの接続"
   description="Power BI Embedded、データ ソースへの接続"
   services="power-bi-embedded"
   documentationCenter=""
   authors="mgblythe"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="07/05/2016"
   ms.author="mblythe"/>

# データ ソースへの接続

**Power BI Embedded** を使用することで、独自のアプリにレポートを組み込むことができます。Power BI レポートをアプリに組み込むと、レポートは基になるデータに接続する際に、データのコピーを**インポート**するか、**DirectQuery** を使用してデータ ソースに**直接接続**します。

**インポート**と **DirectQuery** の違いは次のとおりです。

|インポート | DirectQuery
|---|---
|テーブル、列、*およびデータ*がレポートのデータセットにインポートまたはコピーされます。基になるデータに加えられた変更を表示するには、更新する (現在の完全なデータセットをもう一度インポートする) 必要があります。|*テーブルと列*のみがレポートのデータセットにインポートまたはコピーされます。常に最新のデータが表示されます。
Power BI Embedded を使用して、クラウドのデータ ソースで DirectQuery を使用することができますが、現時点ではオンプレミスのデータ ソースを使用することはできません。

## DirectQuery を使用する利点

**DirectQuery** を使用する場合、2 つの主な利点があります。

   -	**DirectQuery** では大規模なデータセットを視覚化することができますが、他の方法では最初にすべてのデータをインポートすることは困難です。
   -	基になるデータが変更されると、データの更新が必要になる場合があります。一部のレポートでは、最新のデータを表示するために大規模なデータ転送が必要になる場合があり、データを再インポートすることが困難になります。これに対し、**DirectQuery** レポートでは常に最新のデータが使用されます。

## DirectQuery の制限事項

   **DirectQuery** を使用する場合、いくつかの制限事項があります。

   -	すべてのテーブルは 1 つのデータベースから取得する必要があります。
   -	クエリが複雑すぎると、エラーが発生します。エラーを解決するために、クエリをリファクタリングして複雑さを減らす必要があります。複雑なクエリを使用する必要がある場合は、**DirectQuery** を使用する代わりにデータをインポートする必要があります。
   -	リレーションシップのフィルター処理は、双方向ではなく、単一の方向に制限されます。
   -	列のデータ型を変更することはできません。
   -	既定では、メジャーで許可される DAX 式に制限が課されます。「[DirectQuery とメジャー](#measures)」を参照してください。

<a name="measures"/>
## DirectQuery とメジャー

基になるデータ ソースに送信されるクエリのパフォーマンスを許容可能なものにするために、メジャーには制限が課されます。**Power BI Desktop** を使用する上級ユーザーは、**[ファイル] > [オプションと設定] > [オプション]** で、この制限を回避することもできます。**[オプション]** ダイアログで **[DirectQuery]** を選択し、**[DirectQuery モードで無制限のメジャーを許可する]** を選択します。このオプションを選択した場合、メジャーで有効な任意の DAX 式を使用できます。ただし、データをインポートしたときは快適に動作していた式であっても、**DirectQuery** モードになると、バックエンド ソースに対するクエリの速度が大幅に低下する場合があることに注意する必要があります。

## 関連項目
- [Microsoft Power BI Embedded の概要](power-bi-embedded-get-started.md)
- [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)

<!---HONumber=AcomDC_0907_2016-->