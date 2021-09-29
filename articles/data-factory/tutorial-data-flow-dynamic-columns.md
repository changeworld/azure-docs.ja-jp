---
title: データ フローで列名を動的に設定する
description: このチュートリアルでは、データ フローで列名を動的に設定する手順について説明します
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 06/17/2021
ms.openlocfilehash: 576b11e78c8cf928863d7db700942cbeab884e2c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128617832"
---
# <a name="dynamically-set-column-names-in-data-flows"></a>データ フローで列名を動的に設定する

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

ETL ジョブのデータを処理する際は多くの場合、結果を書き込む前に列名の変更が必要になります。 あるときは、既知のターゲット スキーマに合わせて列名を調整する目的で、この処理が必要になります。 また別の場合には、スキーマの進化に合わせて、実行時に列名を設定しなければならないときもあります。 このチュートリアルでは、データ フローから外部の構成ファイルとパラメーターを使用して変換先ファイルとデータベース テーブルの列名を動的に設定する方法について説明します。

Azure Data Factory を初めて使用する場合は、「[Azure Data Factory の概要](introduction.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
* **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* **Azure ストレージ アカウント**。 ADLS ストレージを、*ソース* と *シンク* のデータ ストアとして使用します。 ストレージ アカウントがない場合の作成手順については、[Azure のストレージ アカウントの作成](../storage/common/storage-account-create.md)に関するページを参照してください。

## <a name="create-a-data-factory"></a>Data Factory の作成

この手順では、データ ファクトリを作成し、Data Factory UX を開いて、データ ファクトリにパイプラインを作成します。

1. **Microsoft Edge** または **Google Chrome** を開きます。 現在、Data Factory の UI がサポートされる Web ブラウザーは Microsoft Edge と Google Chrome だけです。
1. 左側のメニューで、 **[リソースの作成]**  >  **[統合]**  >  **[Data Factory]** を選択します。
1. **[新しいデータ ファクトリ]** ページで、 **[名前]** に「**ADFTutorialDataFactory**」と入力します。
1. データ ファクトリを作成する Azure **サブスクリプション** を選択します。
1. **[リソース グループ]** で、次の手順のいずれかを行います。
    * **[Use existing (既存のものを使用)]** を選択し、ドロップダウン リストから既存のリソース グループを選択します。
    * **[新規作成]** を選択し、リソース グループの名前を入力します。リソース グループの詳細については、[リソース グループを使用した Azure リソースの管理](../azure-resource-manager/management/overview.md)に関する記事を参照してください。    
1. **[バージョン]** で、 **[V2]** を選択します。
1. **[場所]** で、データ ファクトリの場所を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリによって使用されるデータ ストア (Azure Storage、SQL Database など) やコンピューティング (Azure HDInsight など) は、他のリージョンに存在していてもかまいません。
1. **［作成］** を選択します
1. 作成が完了すると、その旨が通知センターに表示されます。 **[リソースに移動]** を選択して、Data factory ページに移動します。
1. **[Author & Monitor]\(作成と監視\)** を選択して、別のタブで Data Factory (UI) を起動します。

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>データ フロー アクティビティが含まれるパイプラインの作成

この手順では、データ フロー アクティビティが含まれるパイプラインを作成します。

1. ADF のホーム ページから **[パイプラインを作成]** を選択します。
1. パイプラインの **[全般]** タブで、パイプラインの **名前** として「**DeltaLake**」と入力します。
1. ファクトリの上部のバーで、 **[Data Flow のデバッグ]** スライダーをオンにスライドします。 デバッグ モードを使用すると、ライブ Spark クラスターに対する変換ロジックの対話型テストが可能になります。 Data Flow クラスターのウォームアップには 5 から 7 分かかるため、ユーザーが Data Flow の開発を計画している場合は、最初にデバッグを有効にすることをお勧めします。 詳細については、[デバッグ モード](concepts-data-flow-debug-mode.md)に関するページを参照してください。

    :::image type="content" source="media/tutorial-data-flow/dataflow1.png" alt-text="Data Flow アクティビティ":::
1. **[アクティビティ]** ウィンドウで、 **[移動と変換]** アコーディオンを展開します。 ウィンドウから **Data Flow** アクティビティをパイプライン キャンバスにドラッグ アンド ドロップします。

    :::image type="content" source="media/tutorial-data-flow/activity1.png" alt-text="Data Flow アクティビティをドロップできるパイプライン キャンバスを示すスクリーンショット。":::
1. **[Data Flow の追加]** ポップアップで、 **[新しい Data Flow の作成]** を選択し、データ フローに **DynaCols** という名前を付けます。 終了したら、[完了] をクリックします。    

## <a name="build-dynamic-column-mapping-in-data-flows"></a>データ フローで動的な列マッピングを作成する

このチュートリアルでは、サンプル映画評価ファイルを使用し、ソース内のいくつかのフィールドを、新しい一連のターゲット列の名前に変更します。ターゲット列の名前は時間と共に変わる可能性があります。 以下で作成するデータセットは、Blob Storage または ADLS Gen2 ストレージ アカウント内のこの映画ファイル (CSV) を参照する必要があります。 [こちらで映画ファイルをダウンロード](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/moviesDB.csv)し、Azure ストレージ アカウントに格納してください。

:::image type="content" source="media/data-flow/dynacols-1.png" alt-text="最終的なフロー":::

### <a name="tutorial-objectives"></a>チュートリアルの目標

データ フローを使用して列名を動的に設定する方法について学習します

1. 映画の CSV ファイルのソース データセットを作成します。
1. フィールド マッピング JSON 構成ファイルのルックアップ データセットを作成します。
1. ソースの列をターゲットの列名に変換します。

### <a name="start-from-a-blank-data-flow-canvas"></a>空のデータ フロー キャンバスから開始する

まず、ADLS Gen2 のデータ配置用に、以下で説明する各メカニズムのデータ フロー環境を設定してみましょう。

1. ソース変換をクリックして ```movies1``` という名前を付けます。
1. 下部パネルのデータセットの横にある [新規] ボタンをクリックします。
1. 先ほど moviesDB.csv ファイルを保存した場所に応じて、BLOB または ADLS Gen2 を選択します。
1. 2 つ目のソースを追加します。これは、フィールドのマッピングをルックアップするための構成 JSON ファイルのソースとして使用します。
1. これに ```columnmappings``` という名前を付けます。
1. データセットについて、列マッピングの構成を格納する新しい JSON ファイルを参照します。 このチュートリアルの例の JSON ファイルに貼り付けることができます。
    ```
    [
    {"prevcolumn":"title","newcolumn":"movietitle"},
    {"prevcolumn":"year","newcolumn":"releaseyear"}
    ]
    ```

1. このソースの設定を ```array of documents``` に設定します。
1. 3 つ目のソースを追加し、```movies2``` という名前を付けます。 これは、```movies1``` とまったく同じように構成します。
   
### <a name="parameterized-column-mapping"></a>パラメーター化された列マッピング

この最初のシナリオでは、入力フィールドとパラメーター (列の文字列配列) との対応、そして各配列インデックスと入力列序数位置との対応に基づいて列マッピングを設定し、データ フローの出力列名を設定します。 このデータ フローをパイプラインから実行するとき、この文字列配列パラメーターをデータ フロー アクティビティに送信することで、パイプラインを実行するたびに異なる列名を設定できるようになります。

:::image type="content" source="media/data-flow/dynacols-3.png" alt-text="パラメーター":::

1. データ フロー デザイナーに戻り、先ほど作成したデータ フローを編集します。
1. [パラメーター] タブをクリックします
1. 新しいパラメーターを作成し、文字列配列のデータ型を選択します
1. 既定値として「```['a','b','c']```」を入力します
1. 一番上の ```movies1``` ソースを使用して、これらの配列値に対応するよう列名を変更します
1. 選択変換を追加します。 選択変換は、入力列を出力用の新しい列名にマップする目的で使用されます。
1. 最初の 3 つの列名を、パラメーターで定義された新しい名前に変更します
1. そのためには、ルールベースの 3 つのマッピング エントリを下部ペインに追加します
1. 先頭列では、一致規則が ```position==1``` で、名前は ```$parameter1[1]``` になります
1. 2 列目と 3 列目についても、同じパターンに従います
 
    :::image type="content" source="media/data-flow/dynacols-4.png" alt-text="Select transformation (選択変換)":::

1. 選択変換の [検査] タブと [Data Preview]\(データ プレビュー\) タブをクリックして、元の列名 movie、title、genres が新しい列名値 ```(a,b,c)``` に置き換わっていることを確認します
   
### <a name="create-a-cached-lookup-of-external-column-mappings"></a>外部列マッピングのキャッシュされたルックアップを作成する

次に、後でルックアップするためのキャッシュされたシンクを作成します。 このキャッシュは、データ フローの各パイプライン実行で列名を動的に変更するために使用できる外部 JSON 構成ファイルを読み取ります。

1. データ フロー デザイナーに戻り、先ほど作成したデータ フローを編集します。 ```columnmappings``` ソースにシンク変換を追加します。
1. シンクの種類を ```Cache``` に設定します。
1. [設定] で、キー列として ```prevcolumn``` を選択します。

### <a name="lookup-columns-names-from-cached-sink"></a>キャッシュされたシンクから列名をルックアップする

これで構成ファイルの内容をメモリに格納したので、新しい出力列名に対して入力列名を動的にマップすることができます。

1. データ フロー デザイナーに戻り、先に作成したデータ フローを編集します。 ```movies2``` ソース変換をクリックします。
1. 選択変換を追加します。 今回は選択変換を使用し、キャッシュされたシンクに格納される JSON 構成ファイル内のターゲット名に基づいて列名を変更します。
1. ルールベースのマッピングを追加します。 [Matching Condition]\(一致条件\) には ```!isNull(cachedSink#lookup(name).prevcolumn)``` という式を使用します。
1. 出力列名には、```cachedSink#lookup($$).newcolumn``` という式を使用します。
1. ここでは、外部 JSON 構成ファイルの ```prevcolumn``` プロパティと一致する列名をすべて検索し、一致したものそれぞれの名前を新しい ```newcolumn``` 名に変更しています。
1. 選択変換の [Data Preview]\(データ プレビュー\) タブと [検査] タブをクリックすると、今度は、外部マッピング ファイルに基づく新しい列名が表示されます。

:::image type="content" source="media/data-flow/dynacols-2.png" alt-text="ソース 2":::

## <a name="next-steps"></a>次のステップ

* このチュートリアルで作成したパイプラインは、[こちら](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/DynaColsPipe.zip)からダウンロードできます
* [データ フロー シンク](data-flow-sink.md)の詳細を確認する。
