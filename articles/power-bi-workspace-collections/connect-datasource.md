---
title: "Power BI ワークスペース コレクション内のデータ ソースへの接続 | Microsoft Docs"
description: "Power BI ワークスペース コレクション内のデータ ソースに接続する方法を説明します。"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: 2a4caeb3-255d-4215-9554-0ca8e3568c13
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: 24600c4343e3bfebe14f25020c5a7ba02d15af64
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-a-data-source"></a>データ ソースへの接続

**Power BI ワークスペース コレクション**を使用することで、独自のアプリにレポートを埋め込むことができます。 Power BI レポートをアプリに組み込むと、レポートは基になるデータに接続する際に、データのコピーを**インポート**するか、**DirectQuery** を使用してデータ ソースに**直接接続**します。

> [!IMPORTANT]
> Power BI ワークスペース コレクションは非推奨となっており、2018 年 6 月または契約に定める日までに限り利用できます。 アプリケーションの中断を避けるため、Power BI Embedded への移行をご検討ください。 Power BI Embedded にデータを移行する方法については、「[How to migrate Power BI Workspace Collection content to Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/)」(Power BI Embedded に Power BI ワークスペース コレクション コンテンツを移行する方法) を参照してください。

**インポート**する方法と **DirectQuery** を使う方法の違いを次に示します。

| [インポート] | 直接接続 |
| --- | --- |
| テーブル、列、 *およびデータ* がレポートのデータセットにインポートまたはコピーされます。 基になるデータに加えられた変更を表示するには、更新する (現在の完全なデータセットをもう一度インポートする) 必要があります。 |*テーブルと列* のみがレポートのデータセットにインポートまたはコピーされます。 常に最新のデータが表示されます。 |

Power BI ワークスペース コレクションを使用して、クラウドのデータ ソースで DirectQuery を使用することができますが、現時点ではオンプレミスのデータ ソースを使用することはできません。

> [!NOTE]
> 現時点では、Power BI ワークスペース コレクションではオンプレミスのデータ ゲートウェイがサポートされていません。 つまり、オンプレミスの データ ソースで DirectQuery を使用することはできません。

## <a name="supported-data-sources"></a>サポートされるデータ ソース

**DirectQuery**
* Azure SQL データベース
* Azure SQL Data Warehouse

**インポート**

Power BI Desktop 内で、使用可能なすべてのデータソースをインポートできます。 Power BI ワークスペース コレクション内のデータを最新の情報に更新することは**できなくなります**。 Power BI ワークスペース コレクションに PBIX ファイルへの変更をアップロードする必要があります。 これは、使用可能なゲートウェイがないためです。 

## <a name="benefits-of-using-directquery"></a>DirectQuery を使用する利点

**DirectQuery**を使用する場合、2 つの主な利点があります。

* **DirectQuery** では大規模なデータセットを視覚化することができますが、他の方法では最初にすべてのデータをインポートすることは困難です。
* 基になるデータが変更されると、データの更新が必要になる場合があります。一部のレポートでは、最新のデータを表示するために大規模なデータ転送が必要になる場合があり、データを再インポートすることが困難になります。 これに対し、**DirectQuery** レポートでは常に最新のデータが使用されます。

## <a name="limitations-of-directquery"></a>DirectQuery の制限事項

**DirectQuery**を使用する場合、いくつかの制限事項があります。

* すべてのテーブルは 1 つのデータベースから取得する必要があります。
* クエリが複雑すぎると、エラーが発生します。 エラーを解決するために、クエリをリファクタリングして複雑さを減らす必要があります。 複雑なクエリを使用する必要がある場合は、**DirectQuery** を使用する代わりにデータをインポートする必要があります。
* リレーションシップのフィルター処理は、双方向ではなく、単一の方向に制限されます。
* 列のデータ型を変更することはできません。
* 既定では、メジャーで許可される DAX 式に制限が課されます。 「 [DirectQuery とメジャー](#measures)」を参照してください。

<a name="measures"/>

## <a name="directquery-and-measures"></a>DirectQuery とメジャー
基になるデータ ソースに送信されるクエリのパフォーマンスを許容可能なものにするために、メジャーには制限が課されます。 **Power BI Desktop** を使用する上級ユーザーは、**[ファイル] > [オプションと設定] > [オプション]** で、この制限を回避することもできます。 **[オプション]** ダイアログで **[DirectQuery]** を選択し、**[DirectQuery モードで無制限のメジャーを許可する]** を選択します。 このオプションを選択した場合、メジャーで有効な任意の DAX 式を使用できます。 ただし、データをインポートしたときは快適に動作していた式であっても、**DirectQuery** モードになると、バックエンド ソースに対するクエリの速度が低下する場合があることに注意する必要があります。 

## <a name="see-also"></a>関連項目

* [Microsoft Power BI ワークスペース コレクションの概要](get-started.md)
* [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)

ご質問は、 [Power BI コミュニティ](http://community.powerbi.com/)で尋ねてみてください。

