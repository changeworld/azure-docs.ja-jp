---
title: データのコピー ツールでメタデータ駆動型の方法を使用して大規模なデータ コピー パイプラインを作成する
description: ADF のデータのコピー ツールのメタデータ駆動型の方法について説明します
author: dearandyxu
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.date: 06/19/2021
ms.author: yexu
ms.openlocfilehash: 02d7b741ec0c3fb9547d10bde759900ce3a69dd6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128663347"
---
# <a name="build-large-scale-data-copy-pipelines-with-metadata-driven-approach-in-copy-data-tool-preview"></a>データのコピー ツール (プレビュー) でメタデータ駆動型の方法を使用して大規模なデータ コピー パイプラインを作成する
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

大量のオブジェクト (数千のテーブルなど) をコピーしたり、さまざまなソースからデータを読み込んだりするには、適切な方法として、必要なコピー動作を持つオブジェクトの名前リストを制御テーブルに入力し、パラメーター化されたパイプラインを使用して制御テーブルから同じものを読み取り、それらを対応するジョブに適用する方法があります。  これにより、パイプラインを再配置するのでなく、制御テーブル内のオブジェクト名を更新するだけで、オブジェクトの一覧が簡単にコピーされるように (追加や削除など) 維持できます。 さらに、コピー動作が定義されているパイプラインまたはトリガーによって、どのオブジェクトがコピーされたかを単一の場所で簡単に確認できるようになります。 

ADF のデータのコピー ツールによって、このようなメタデータ駆動型データ コピー パイプラインの構築の過程が容易になります。 ウィザードベースのエクスペリエンスから直感的なフローに従って実行すると、このツールは、パラメーター化されたパイプラインと、それに応じて外部制御テーブルを作成するための SQL スクリプトを生成できます。 生成されたスクリプトを実行して SQL データベースに制御テーブルを作成すると、パイプラインは制御テーブルからメタデータを読み取って、コピー ジョブに自動的に適用します。

## <a name="create-metadata-driven-copy-jobs-from-copy-data-tool"></a>データのコピー ツールからメタデータ駆動型コピー ジョブを作成する

1. データのコピー ツールで **[Metadata-driven copy task]\(メタデータ駆動型コピー タスク\)** を選択します。

   生成されたパイプラインによってメタデータが読み込まれるように、制御テーブルの接続とテーブル名を入力する必要があります。

   :::image type="content" source="./media/copy-data-tool-metadata-driven/select-task-type.png" alt-text="タスクの種類を選択する":::

2. **接続元データベースの接続** を入力します。 [パラメーター化されリンクされたサービス](parameterize-linked-services.md)も使用できます。

   :::image type="content" source="./media/copy-data-tool-metadata-driven/select-parameterized-linked-service.png" alt-text="パラメーター化されリンクされたサービスを選択する":::

3. コピーする **テーブル名** を選択します。

   :::image type="content" source="./media/copy-data-tool-metadata-driven/select-table.png" alt-text="テーブルの選択":::

   > [!NOTE]
   > 表形式データ ストアを選択した場合は、次のページで全体の読み込みまたは増分読み込みをさらに選択することができます。 ストレージ ストアを選択した場合は、次のページで全体の読み込みのみを選択できます。 ストレージ ストアからの新しいファイルのみの増分読み込みは、現在サポートされていません。  

4. **[loading behavior]\(読み込み動作\)** を選択します。
   >[!TIP]
   >すべてのテーブルに対して完全コピーを実行する場合は、 **[Full load all tables]\(すべてのテーブルを全体の読み込み\)** を選択します。 増分コピーを実行する場合は、 **[configure for each table individually]\(各テーブルに対して個別に構成する\)** を選択し、 **[Delta load]\(差分読み込み\)** と、各テーブルに対して開始する基準値列名と値を選択します。 

5. **[配布先データ ストア]** を選択します。 

6. **[設定]** ページで、 **[Number of concurrent copy tasks]\(同時コピー タスク数\)** によって、ソース ストアからデータを同時にコピーするコピー アクティビティの最大数を決定できます。 既定値は 20 です。 

   :::image type="content" source="./media/copy-data-tool-metadata-driven/settings.png" alt-text="設定ページ":::

7. パイプラインのデプロイ後、制御テーブルとストア プロシージャを作成するための UI から SQL スクリプトをコピーまたはダウンロードできます。 

   :::image type="content" source="./media/copy-data-tool-metadata-driven/download-scripts.png" alt-text="スクリプトのダウンロード":::

   2 つの SQL スクリプトが表示されます。
   
    - 最初の SQL スクリプトは、2 つの制御テーブルを作成するために使用されます。 メインの制御テーブルには、テーブルの一覧、ファイルのパス、またはコピー動作が格納されます。 パラメーター化されリンクされたサービスを使用した場合、接続制御テーブルはデータ ストアの接続値を格納します。  
    - 2 番目の SQL スクリプトは、ストア プロシージャを作成するために使用します。 これは、増分コピー ジョブが毎回完了したときに、メインの制御テーブルの基準値を更新するために使用されます。 

8. **SSMS** を開いて制御テーブル サーバーに接続し、2 つの SQL スクリプトを実行して制御テーブルとストア プロシージャを作成します。

   :::image type="content" source="./media/copy-data-tool-metadata-driven/create-control-table-script.png" alt-text="制御テーブル スクリプトを作成する":::

9. メインの制御テーブルと接続制御テーブルに対してクエリを実行し、その中のメタデータを確認します。

   **メインの制御テーブル**
   :::image type="content" source="./media/copy-data-tool-metadata-driven/query-control-table.png" alt-text="クエリ制御テーブル script1":::

   **接続制御テーブル**
   :::image type="content" source="./media/copy-data-tool-metadata-driven/query-connection-control-table.png" alt-text="クエリ制御テーブル script2":::

10. ADF ポータルに戻り、パイプラインを表示およびデバッグします。 "MetadataDrivenCopyTask_### _######" という名前を付けることによって作成されたフォルダーが表示されます。"MetadataDrivenCopyTask_###_TopLevel" という名前のパイプラインを **クリック** し、 **[debug run]\(デバッグの実行\)** をクリックします。 

    次のパラメーターを入力する必要があります。
   
    | パラメーター名 | 説明 | 
    |:--- |:--- |
    |MaxNumberOfConcurrentTasks |パイプラインを実行する前に実行される同時コピー アクティビティの最大数は、いつでも変更できます。 既定値は、データのコピー ツールで入力した値になります。 |
    |MainControlTableName | メインの制御テーブル名はいつでも変更できるので、パイプラインは実行前にそのテーブルからメタデータを取得します。  |
    |ConnectionControlTableName |接続制御テーブル名 (省略可能) はいつでも変更できるので、パイプラインは実行前にデータストア接続に関連するメタデータを取得します。 |
    |MaxNumberOfObjectsReturnedFromLookupActivity |出力検索アクティビティの制限に到達しないようにするために、検索アクティビティによって返されるオブジェクトの最大数を定義する方法があります。 ほとんどの場合、既定値を変更する必要はありません。  |
    |windowStart |フォルダー パスとして動的な値 (yyyy/mm/dd など) を入力すると、このパラメーターを使用して、動的フォルダー パスを埋めるために現在のトリガー時間をパイプラインに渡すことができます。 パイプラインがスケジュール トリガーまたはタンブリング ウィンドウ トリガーによってトリガーされる場合、ユーザーはこのパラメーターの値を入力する必要はありません。 サンプル値: 2021-01-25T01:49:28Z | 
   

11. パイプラインを運用化するトリガーを有効にします。

    :::image type="content" source="./media/copy-data-tool-metadata-driven/enable-trigger.png" alt-text="トリガーを有効にする":::


## <a name="update-control-table-by-copy-data-tool"></a>データのコピー ツールによって制御テーブルを更新する
コピーするオブジェクトを追加または削除するか、各テーブルのコピー動作を変更することで、いつでも制御テーブルを直接更新できます。 また、データのコピー ツールで UI エクスペリエンスを作成して、制御テーブルを編集するための作業を容易にします。

1. 最上位のパイプライン **MetadataDrivenCopyTask_xxx_TopLevel** を右クリックして、 **[編集コントロール テーブル]** を選択します。

   :::image type="content" source="./media/copy-data-tool-metadata-driven/edit-control-table.png" alt-text="制御 table1 を編集する":::

2. 編集する制御テーブルから行を選択します。

   :::image type="content" source="./media/copy-data-tool-metadata-driven/edit-control-table-select-tables.png" alt-text="制御 table2 を編集する":::

3. データのコピー ツールのスループットを使用すると、新しい SQL スクリプトが表示されます。 SQL スクリプトを再実行して、制御テーブルを更新します。

   :::image type="content" source="./media/copy-data-tool-metadata-driven/edit-control-table-create-script.png" alt-text="制御 table3 を編集する":::

   > [!NOTE]
   > パイプラインは再デプロイされません。 新しく作成された SQL スクリプトを使用すると、制御テーブルのみを更新できます。 

## <a name="control-tables"></a>制御テーブル

### <a name="main-control-table"></a>メインの制御テーブル
制御テーブルの各行には、コピーする 1 つのオブジェクト (1 つのテーブルなど) のメタデータが含まれます。

| 列名 | 説明 | 
|:--- |:--- |
| Id | コピー対象オブジェクトの一意の ID。 |
| SourceObjectSettings | ソース データセットのメタデータ。 スキーマ名、テーブル名などにすることができます。次に[例](connector-azure-sql-database.md#dataset-properties)を示します。 |
| SourceConnectionSettingsName | 接続制御テーブルの接続元接続設定の名前です。 これはオプションです。 |
| CopySourceSettings | コピー アクティビティのソース プロパティのメタデータ。 クエリ、パーティションなどにすることができます。次に[例](connector-azure-sql-database.md#azure-sql-database-as-the-source)を示します。 |
| SinkObjectSettings | コピー先のデータセットのメタデータ。 ファイル名、フォルダーのパス、テーブル名などにできます。次に[例](connector-azure-data-lake-storage.md#azure-data-lake-storage-gen2-as-a-sink-type)を示します。 動的フォルダー パスが指定されている場合、変数の値は制御テーブルに書き込まれません。 |
| SinkConnectionSettingsName | 接続制御テーブルの接続先接続設定の名前です。 これはオプションです。 |
| CopySinkSettings | コピー アクティビティのシンク プロパティのメタデータ。 preCopyScript、tableOption などにできます。次に[例](connector-azure-sql-database.md#azure-sql-database-as-the-sink)を示します。 |
| CopyActivitySettings | コピー アクティビティの翻訳プロパティのメタデータ。 列マッピングを定義するために使用されます。 |
| TopLevelPipelineName | このオブジェクトをコピーできる上位パイプライン名。 |
| TriggerName | トリガー名。このオブジェクトをコピーするパイプラインをトリガーできます。 デバッグを実行する場合、この名前は Sandbox です。 手動実行の場合、この名前は Manual です。 スケジュールされた実行の場合、名前は関連付けられているトリガー名です。 複数の名前を入力できます。 |
| DataLoadingBehaviorSettings |全体の読み込み対差分読み込み。 |
| TaskId | 制御テーブルの TaskId の後にコピーするオブジェクトの順序 (ORDER BY [TaskId] DESC)。 大量のオブジェクトをコピーするが、同時に許容されるオブジェクトの数が限られる場合は、各オブジェクトの TaskId を変更して、先にコピーできるオブジェクトを決定できます。 既定値は 0 です。 |
| CopyEnabled | データ インジェスト プロセスで項目が有効になっている場合に指定します。 使用できる値: 1 (有効)、0 (無効)。 既定値は 1 です。 |

### <a name="connection-control-table"></a>接続制御テーブル
制御テーブルの各行には、データ ストアに対して 1 つの接続設定が含まれます。

| 列名 | 説明 | 
|:--- |:--- |
| 名前 | メインの制御テーブル内のパラメーター化された接続の名前。 |
| ConnectionSettings | 接続設定。 DB 名、サーバー名などにできます。 |

## <a name="pipelines"></a>パイプライン
データのコピー ツールによって生成されるパイプラインの 3 つのレベルが表示されます。

### <a name="metadatadrivencopytask_xxx_toplevel"></a>MetadataDrivenCopyTask_xxx_TopLevel
このパイプラインは、この実行でコピーする必要があるオブジェクト (テーブルなど) の総数を計算し、最大許容同時コピー タスク数に基づいて連続したバッチの数を算出し、別のパイプラインを実行して異なるバッチを順番にコピーします。 

#### <a name="parameters"></a>パラメーター
| パラメーター名 | 説明 | 
|:--- |:--- |
| MaxNumberOfConcurrentTasks | パイプラインを実行する前に実行される同時コピー アクティビティの最大数は、いつでも変更できます。 既定値は、データのコピー ツールで入力した値になります。 |
| MainControlTableName | メインの制御テーブルのテーブル名。 パイプラインは、実行前にこのテーブルからメタデータを取得します |
| ConnectionControlTableName | 接続制御テーブルのテーブル名 (省略可能)。 パイプラインは、実行前にデータ ストア接続に関連するメタデータを取得します |
| MaxNumberOfObjectsReturnedFromLookupActivity | 出力検索アクティビティの制限に到達しないようにするために、検索アクティビティによって返されるオブジェクトの最大数を定義する方法があります。 ほとんどの場合、既定値を変更する必要はありません。  |
| windowStart | フォルダー パスとして動的な値 (yyyy/mm/dd など) を入力すると、このパラメーターを使用して、動的フォルダー パスを埋めるために現在のトリガー時間をパイプラインに渡すことができます。 パイプラインがスケジュール トリガーまたはタンブリング ウィンドウ トリガーによってトリガーされる場合、ユーザーはこのパラメーターの値を入力する必要はありません。 サンプル値: 2021-01-25T01:49:28Z |

#### <a name="activities"></a>Activities
| アクティビティ名 | アクティビティの種類 | 説明 |
|:--- |:--- |:--- |
| GetSumOfObjectsToCopy  | 参照 | この実行でコピーする必要があるオブジェクト (テーブルなど) の総数を計算します。 |
| CopyBatchesOfObjectsSequentially | ForEach | 最大許容同時コピー タスク数に基づいて連続したバッチの数を算出し、別のパイプラインを実行して異なるバッチを順番にコピーします。  |
| CopyObjectsInOneBtach | パイプラインの実行 | 別のパイプラインを実行して、オブジェクトの 1 つのバッチをコピーします。 このバッチに属するオブジェクトは、並行してコピーされます。 | 


### <a name="metadatadrivencopytask_xxx_-middlelevel"></a>MetadataDrivenCopyTask_xxx_ MiddleLevel
このパイプラインは、オブジェクトの 1 つのバッチをコピーします。 このバッチに属するオブジェクトは、並行してコピーされます。 

#### <a name="parameters"></a>パラメーター
| パラメーター名 | 説明 | 
|:--- |:--- |
| MaxNumberOfObjectsReturnedFromLookupActivity | 出力検索アクティビティの制限に到達しないようにするために、検索アクティビティによって返されるオブジェクトの最大数を定義する方法があります。  ほとんどの場合、既定値を変更する必要はありません。  | 
| TopLayerPipelineName | 上位レイヤー パイプラインの名前。 | 
| TriggerName | トリガーの名前。 | 
| CurrentSequentialNumberOfBatch | 連続したバッチの ID。 | 
| SumOfObjectsToCopy | コピーするオブジェクトの総数。 | 
| SumOfObjectsToCopyForCurrentBatch | 現在のバッチでコピーするオブジェクトの数。 | 
| MainControlTableName | メインの制御テーブルの名前。 |
| ConnectionControlTableName | 接続制御テーブルの名前。 |

#### <a name="activities"></a>Activities
| アクティビティ名 | アクティビティの種類 | 説明 |
|:--- |:--- |:--- |
| DivideOneBatchIntoMultipleGroups | ForEach  | 検索アクティビティの出力制限に達しないように、オブジェクトを 1 つのバッチから複数の並列グループに分割します。 |
| GetObjectsPerGroupToCopy | 参照 | このグループにコピーする必要がある制御テーブルからオブジェクト (テーブルなど) を取得します。 制御テーブルの TaskId の後にコピーするオブジェクトの順序 (ORDER BY [TaskId] DESC)。 |
| CopyObjectsInOneGroup | パイプラインの実行 | 別のパイプラインを実行して、1 つのグループからオブジェクトをコピーします。 このグループに属するオブジェクトは並行してコピーされます。 |


### <a name="metadatadrivencopytask_xxx_-bottomlevel"></a>MetadataDrivenCopyTask_xxx_ BottomLevel
このパイプラインは、1 つのグループからオブジェクトをコピーします。 このグループに属するオブジェクトは並行してコピーされます。  

#### <a name="parameters"></a>パラメーター
| パラメーター名 | 説明 | 
|:--- |:--- |
| ObjectsPerGroupToCopy | 現在のグループにコピーするオブジェクトの数。 | 
| ConnectionControlTableName | 接続制御テーブルの名前。 | 
| windowStart | ユーザーによって構成されている場合、動的フォルダー パスを埋める目的で、現在のトリガー時間をパイプラインに渡すために使用されます。 | 

#### <a name="activities"></a>Activities
| アクティビティ名 | アクティビティの種類 | 説明 |
|:--- |:--- |:--- |
| ListObjectsFromOneGroup | ForEach | 1 つのグループからオブジェクトを一覧表示し、各オブジェクトにつき、ダウンストリーム アクティビティに反復処理します。 |
| RouteJobsBasedOnLoadingBehavior | Switch | 各オブジェクトの読み込み動作を確認します。 既定値または FullLoad の場合は、全体の読み込みを実行します。 DeltaLoad の場合は、基準値列を使用して増分読み込みを実行し、変更を特定します。 |
| FullLoadOneObject | コピー | このオブジェクトの完全なスナップショットを取得し、宛先にコピーします。 |
| DeltaLoadOneObject | コピー | 基準値列の値を比較して変更内容を識別することで、前回から変更されたデータのみをコピーします。 |
| GetMaxWatermarkValue | 参照 | ソース オブジェクトに対してクエリを実行し、基準値列から最大値を取得します。 |
| UpdateWatermarkColumnValue | StoreProcedure | 新しい基準値を書き戻して、次回使用するテーブルを制御します。 |

### <a name="known-limitations"></a>既知の制限事項
- データのコピー ツールでは、現在、新しいファイルだけを増分コピーするためのメタデータ駆動型の取り込みはサポートされていません。 ただし、独自のパラメーター化されたパイプラインを使用して実現できます。
- ADF では、IR 名、データベースの種類、ファイル形式の種類をパラメーター化できません。 たとえば、Oracle Server と SQL Server の両方からデータを取り込む場合は、2 つの異なるパラメーター化されたパイプラインが必要です。 ただし、1 つの制御テーブルは、2 つのパイプライン セットで共有できます。 
- OPENJSON は、データ コピー ツールによって生成される SQL スクリプトで使用されます。 SQL Server を使用して制御テーブルをホストする場合、OPENJSON 関数をサポートするには、SQL Server 2016 (13.x) 以降である必要があります。


## <a name="next-steps"></a>次のステップ
データのコピー ツールを使う以下のチュートリアルを試してください。

- [クイック スタート: データのコピー ツールを使用してデータ ファクトリを作成する](quickstart-create-data-factory-copy-data-tool.md)
- [チュートリアル: データのコピー ツールを使用して Azure 内のデータをコピーする](tutorial-copy-data-tool.md) 
- [チュートリアル: データのコピー ツールを使用してオンプレミスのデータを Azure にコピーする](tutorial-hybrid-copy-data-tool.md)
