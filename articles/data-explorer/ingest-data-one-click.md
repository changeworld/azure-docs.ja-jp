---
title: ワンクリックでのインジェストを使用して Azure Data Explorer にデータを取り込む
description: ワンクリックでのインジェストを使用するだけで、Azure Data Explorer にデータを取り込む (読み込む) 方法の概要を説明します。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: 5bde63427ce76f14832551864bbf2c3d8e015fd6
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521581"
---
# <a name="what-is-one-click-ingestion"></a>ワンクリックでのインジェストとは 

ワンクリックでのインジェストを使用すると、Azure Data Explorer にデータをすばやく取り込み、テーブルとマッピングの構造をデータ ソースに基づいて自動的に提案することができます。 

Azure Data Explorer の Web UI を使用して、ストレージ (BLOB ファイル)、ローカル ファイル、またはコンテナー (最大 10,000 BLOB) からデータを取り込むことができます。 コンテナーにイベント グリッドを定義して、継続的にデータを取り込むこともできます。 データは、JSON や CSV、[その他の形式](#file-formats)で、既存のテーブルまたは新しいテーブルに取り込むことができます。 ワンクリックでのインジェストは、新しいテーブルとテーブル マッピングの構造をそのデータ ソースに基づいて提示できるほか、既存のテーブルとテーブル マッピングの構造を直感的に調整できるプラットフォームとなっています。 ワンクリックでのインジェストを使用すれば、ほんの数分でテーブルにデータを取り込むことができます。

データの初回取り込み時やデータのスキーマに不慣れな場合に、ワンクリックでのインジェストは特に効果的です。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* [Azure Data Explorer クラスターとデータベース](create-cluster-database-portal.md)を作成します。
* [Azure Data Explorer の Web UI にサインイン](https://dataexplorer.azure.com/)して、[クラスターへの接続を追加](/azure/data-explorer/web-query-data#add-clusters)します。

## <a name="file-formats"></a>ファイル形式

ワンクリックでのインジェストでは、次の形式のソース データから新しいテーブルにデータを取り込むことができます。
* JSON
* CSV
* TSV
* SCSV
* SOHSV
* TSVE
* PSV

## <a name="one-click-ingestion-wizard"></a>ワンクリックでのインジェスト ウィザード

ワンクリックでのインジェスト ウィザードを使用すると、ワンクリックでのデータ取り込み手順を画面の指示に従って行うことができます。 

> [!Note]
> ここでは、ウィザード全般について説明します。 選択するオプションは、データを新しいテーブルに取り込むか、既存のテーブルに取り込むかによって異なります。 詳細については、次を参照してください。
    > * [新しいテーブル](one-click-ingestion-new-table.md)への取り込み
    > * [既存のテーブル](one-click-ingestion-existing-table.md)への取り込み 
    
1. ウィザードにアクセスするには、Azure Data Explorer の Web UI の左側のメニューで "*データベース*" または "*テーブル*" の行を右クリックし、 **[Ingest new data (preview)]\(新しいデータの取り込み (プレビュー)\)** を選択します。

    ![Web UI 上でワンクリックでのインジェストを選択します。](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   

1. ウィザードの指示に従って、次のオプションを選択します。
       * [既存のテーブル](one-click-ingestion-existing-table.md)への取り込み
       * [新しいテーブル](one-click-ingestion-new-table.md)への取り込み
       * 取り込むデータ:      * Blob Storage      * ローカル ファイル      * コンテナー
       * 1 行から 10,000 行のサンプル サイズを入力します (コンテナーからのみ)。
       
1. データ ソースを正しく選択すると、データのプレビューが表示されます。 
    データをコンテナーから取り込む場合は、データにフィルターを適用して、特定のプレフィックスや拡張子を持つファイルだけを取り込むことができます。 たとえば、名前が *Europe* で始まるファイルや拡張子が *.json* であるファイルのみを取り込みの対象とすることができます。 

1. **[スキーマの編集]** をクリックします。 特定のテーブルにデータを取り込む場合、ソース列をターゲット列にマップしたり、列名を含めるかどうかを選択したりすることができます。

1. データ インジェスト プロセスを開始します。

> [!Note]
> データ ソースがコンテナーである場合、Azure Data Explorer のデータ インジェスト集計 (バッチ処理) ポリシーは、インジェスト プロセスを最適化するように設計されていることに注意してください。 既定では、このポリシーは 5 分または 500 MB のデータに構成されているため、待ち時間が生じることがあります。 集計オプションについては、[バッチ処理のポリシー](/azure/kusto/concepts/batchingpolicy)に関するページを参照してください。 他のソースからのデータ取り込みは、直ちに結果が反映されます。

## <a name="next-steps"></a>次のステップ

* ワンクリックでのインジェストを使用してデータを[既存のテーブル](one-click-ingestion-existing-table.md)に取り込むか、[新しいテーブル](one-click-ingestion-new-table.md)に取り込むかを決めます。
* [Azure Data Explorer の Web UI でデータのクエリを実行する](/azure/data-explorer/web-query-data)
* [Kusto クエリ言語を使用して Azure Data Explorer のクエリを作成する](/azure/data-explorer/write-queries)