---
title: ワンクリックでのインジェストを使用して Azure Data Explorer にデータを取り込む
description: ワンクリックでのインジェストを使用するだけで、Azure Data Explorer にデータを取り込む (読み込む) 方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: c4ee4ed81cd4cc443a8f412462a5a7f204c91898
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688190"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>ワンクリックでのインジェストを使用して Azure Data Explorer にデータを取り込む

この記事では、ワンクリックでのインジェストを使用して json 形式または csv 形式の新しいテーブルをストレージまたはローカル ファイルから Azure Data Explorer の既存のテーブルまたは新しいテーブルにすばやく取り込む方法について説明します。 直感的なウィザードを使用することで、数分以内にデータを取り込み、テーブルを編集し、Web UI を使用してクエリを実行することができます。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* [アプリケーション](https://dataexplorer.azure.com/)にサインインします。
* [Azure Data Explorer クラスターとデータベース](create-cluster-database-portal.md)を作成する
* [Web UI](https://dataexplorer.azure.com/) にサインインし、クラスターへの接続を追加[する](/azure/data-explorer/web-query-data#add-clusters)
* Azure Storage 内のデータのソース。

## <a name="ingest-new-data"></a>新しいデータを取り込む

1. Web UI の左側のメニューで*データベース*または*テーブル*の行を右クリックし、 **[Ingest new data (Preview)] (新しいデータの取り込み (プレビュー))** を選択します

    ![Web UI でワンクリックでのインジェストを選択する](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. **[Ingest new data (preview)] (新しいデータの取り込み (プレビュー))** ウィンドウの **[ソース]** タブで、 **[プロジェクトの詳細]** を入力します。

    * **テーブル**:ドロップダウンから既存のテーブル名を選択するか、 **[新規作成]** を選択して新しいテーブルを作成します。
    * **[Ingestion type] (インジェストの種類)**  >  として、 **[ストレージから]** または **[ファイルから]** を選択します。
        * **[ストレージから]** を選択した場合は、 **[ストレージへのリンク]** を入力してストレージへの URL を追加します。 プライベート ストレージ アカウントには [BLOB SAS URL](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) を使用します。 
        * **[ファイルから]** を選択した場合は、 **[参照]** を選択し、ファイルをボックスにドラッグします。
    * テーブル列の構成を表示および編集するには、 **[スキーマの編集]** を選択します。
 
    ![ワンクリックでのインジェスト ソースの詳細](media/ingest-data-one-click/one-click-ingestion-source.png) 

    > [!TIP]
    > *テーブル*の行で **[Ingest new data (Preview)] (新しいデータの取り込み (プレビュー))** を選択した場合は、選択したテーブル名が **[プロジェクトの詳細]** に表示されます。

1. 既存のテーブルを選択した場合は、ソース データの列をターゲット テーブルの列にマップする **[Map columns] (マップ列)** ウィンドウが開きます。 
    * テーブルからターゲット列を削除するには、 **[Omit column] (列の省略)** を使用します。 
    * テーブルに新しい列を追加するには、 **[新しい列]** を使用します。 

    ![[Map columns] (マップ列) ウィンドウ](media/ingest-data-one-click/one-click-map-columns-window.png)

1. **[スキーマ]** タブで次のようにします。

    * **[圧縮の種類]** ドロップダウンから、 **[非圧縮]** または **[GZip]** を選択します。
    * **データ形式** ドロップダウンから **JSON**、**CSV**、**TSV**、**SCSV**、**SOHSV**、**TSVE**、または **PSV** を選択します。 
        * **[JSON]** 形式を選択した場合は、**JSON レベル**を選択します。1 ～ 10 です。 このレベルは、テーブル列でのデータ表現に影響します。 
        * JSON 以外の形式を選択した場合、 **[Include column names] (列名を含める)** を選択すると、ファイルの見出し行が無視されます。    
    * **[マッピング名]** は自動的に設定されますが、編集できます。
    * 既存のテーブルを選択した場合は、 **[Map columns] (マップ列)** をクリックして **[Map columns] (マップ列)** ウィンドウを開くことができます。

    ![ワンクリックでのインジェスト csv 形式スキーマ.png](media/ingest-data-one-click/one-click-csv-format.png)

1. **[エディター]** で、右側の **[V]** を選択してエディターを開きます。 エディターでは、ご自分の入力から生成された自動クエリを表示およびコピーできます。 

1.  テーブル内で: 
    * 新しい列ヘッダーを右クリックして、 **[データ型の変更]** 、 **[列名の変更]** 、 **[列の削除]** 、 **[昇順で並べ替え]** 、または　 **[降順で並べ替え]** を選択します。 既存の列で選択できるのはデータの並べ替えのみです。 
    * 編集するには、新しい列の名前をダブルクリックします。

1. テーブルの作成、マッピングの作成、およびデータ インジェストを行うには、 **[Start ingestion]\(インジェストの開始\)** を選択します。

    ![ワンクリックでのインジェスト json 形式スキーマ](media/ingest-data-one-click/one-click-json-format.png) 
 
## <a name="query-data"></a>データのクエリを実行する

1. **[Data ingestion completed]\(データ インジェストが完了しました\)** ウィンドウでは、データ インジェストが正常に完了した場合、3 つのステップすべてに緑色のチェックマークが表示されます。 
 
    ![ワンクリックでのデータ インジェストの完了](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. **[V]** を選択してクエリを開きます。 クエリを編集するには、Web UI にコピーします。

1. 右側のメニューには、 **[Quick queries]\(クイック クエリ\)** と **[ツール]** が含まれています。 

    * **[Quick queries]\(クイック クエリ\)** には、クエリの例を備えた Web UI へのリンクが含まれています。
    * **[ツール]** には、**Drop コマンド** を備えた Web UI へのリンクが含まれています。これにより、関連する `.drop` コマンドを実行して問題のトラブルシューティングを行うことができます。

    > [!TIP]
    > `.drop` コマンドを使用した場合は、データが失われることがあります。 慎重に使用してください。

## <a name="next-steps"></a>次の手順

* [Azure Data Explorer の Web UI でデータのクエリを実行する](web-query-data.md)
* [Kusto クエリ言語を使用して Azure Data Explorer のクエリを作成する](write-queries.md)
