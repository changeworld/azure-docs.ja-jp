---
title: "クイックスタート: Azure Portal を使用して Azure Databricks で Spark ジョブを実行する | Microsoft Docs"
description: "このクイックスタートでは、Azure Portal を使用して Azure Databricks ワークスペースと Apache Spark クラスターを作成し、Spark ジョブを実行する方法を示します。"
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/22/2018
ms.author: nitinme
ms.custom: mvc
ms.openlocfilehash: 8a91e92e0e535b047ddfcfeff53de72d769f05c7
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="quickstart-run-a-spark-job-on-azure-databricks-using-the-azure-portal"></a>クイックスタート: Azure Portal を使用して Azure Databricks で Spark ジョブを実行する

このクイックスタートでは、Azure Databricks ワークスペースと、そのワークスペース内の Apache Spark クラスターを作成する方法を示します。 最後に、Databricks クラスターで Spark ジョブを実行する方法を説明します。 Azure Databricks の詳細については、「[Azure Databricks とは](what-is-azure-databricks.md)」を参照してください。

## <a name="log-in-to-the-azure-portal"></a>Azure Portal にログインする

[Azure Portal](https://portal.azure.com) にログインします。

## <a name="create-a-databricks-workspace"></a>Databricks ワークスペースを作成する

このセクションでは、Azure Portal を使って Azure Databricks ワークスペースを作成します。 

1. Azure Portal で、**[+]**、**[データ + 分析]**、**[Azure Databricks (Preview)]\(Azure Databricks (プレビュー)\)** の順にクリックします。 

    ![Azure Portal の Databricks](./media/quickstart-create-databricks-workspace-portal/azure-databricks-on-portal.png "Azure Portal の Databricks")

2. **[Azure Databricks (Preview)]\(Azure Databricks (プレビュー)\)** で **[作成]** をクリックします。

3. **[Azure Databricks Service]\(Azure Databricks サービス\)** で、次の値を指定します。

    ![Azure Databricks ワークスペースを作成する](./media/quickstart-create-databricks-workspace-portal/create-databricks-workspace.png "Azure Databricks ワークスペースを作成する")

    * **[ワークスペース名]** では、Databricks ワークスペースの名前を指定します。
    * **[サブスクリプション]** では、ドロップダウン リストから Azure サブスクリプションを選びます。
    * **[リソース グループ]** で、新しいリソース グループを作成するか、既存のリソース グループを使用するかを指定します。 リソース グループは、Azure ソリューションの関連するリソースを保持するコンテナーです。 詳しくは、[Azure リソース グループの概要](../azure-resource-manager/resource-group-overview.md)に関するページをご覧ください。
    * **[場所]** では、**[米国東部 2]** を選びます。 使用可能な他のリージョンについては、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」をご覧ください。

4. **Create** をクリックしてください。

## <a name="create-a-spark-cluster-in-databricks"></a>Databricks に Spark クラスターを作成する

1. Azure Portal で、作成した Databricks ワークスペースに移動して、**[Initialize Workspace]\(ワークスペースの初期化\)** をクリックします。

2. Azure Databricks ポータルにリダイレクトされます。 ポータルで **[クラスター]** をクリックします。

    ![Azure の Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-on-azure.png "Azure の Databricks")

3. **[New cluster]\(新しいクラスター\)** ページで、クラスターを作成するための値を指定します。

    ![Azure で Databricks Spark クラスターを作成する](./media/quickstart-create-databricks-workspace-portal/create-databricks-spark-cluster.png "Azure で Databricks Spark クラスターを作成する")

    * クラスターの名前を入力します。
    * **[Terminate after ___ minutes of activity]\(アクティビティが ___ 分ない場合は終了する\)** チェック ボックスをオンにします。 クラスターが使われていない場合にクラスターを終了するまでの時間 (分単位) を指定します。
    * 他の値はすべて既定値のままにします。 
    * **[Create cluster]\(クラスターの作成\)** をクリックします。 クラスターが実行されたら、ノートブックをクラスターにアタッチして、Spark ジョブを実行できます。

クラスターの作成について詳しくは、[Azure Databricks での Spark クラスターの作成に関するページ](https://docs.azuredatabricks.net/user-guide/clusters/create.html)をご覧ください。

## <a name="run-a-spark-sql-job"></a>Spark SQL ジョブを実行する

このセクションで始める前に、次のことを行う必要があります。

* [Azure Storage アカウント](../storage/common/storage-create-storage-account.md#create-a-storage-account)を作成します。 
* [GitHub から](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json)サンプルの JSON ファイルをダウンロードします。 
* サンプルの JSON ファイルを、作成した Azure ストレージ アカウントにアップロードします。 [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) を使って、ファイルをアップロードできます。

次の手順で、Databricks にノートブックを作成し、Azure BLOB ストレージ アカウントからデータを読み取るようにノートブックを構成して、データに対して Spark SQL ジョブを実行します。

1. 左側のウィンドウで、**[ワークスペース]** をクリックします。 **[ワークスペース]** ドロップダウンの **[作成]** をクリックして、**[ノートブック]** をクリックします。

    ![Databricks でノートブックを作成する](./media/quickstart-create-databricks-workspace-portal/databricks-create-notebook.png "Databricks でノートブックを作成する")

2. **[Create Notebook]\(ノートブックの作成\)** ダイアログ ボックスで、名前を入力し、言語として **[Scala]** を選んで、前に作成した Spark クラスターを選びます。

    ![Databricks でノートブックを作成する](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-details.png "Databricks でノートブックを作成する")

    **Create** をクリックしてください。

3. 次のスニペットで、`{YOUR STORAGE ACCOUNT NAME}` を作成した Azure ストレージ アカウント名に、`{YOUR STORAGE ACCOUNT ACCESS KEY}` をストレージ アカウントのアクセス キーに置き換えます。 スニペットをノートブックの空のセルに貼り付け、Shift + Enter キーを押してコードのセルを実行します。 このスニペットは、Azure Blob Storage からデータを読み取るようにノートブックを構成します。

       spark.conf.set("fs.azure.account.key.{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net", "{YOUR STORAGE ACCOUNT ACCESS KEY}")
    
    ストレージ アカウント キーを取得する方法については、「[ストレージ アクセス キーの管理](../storage/common/storage-create-storage-account.md#manage-your-storage-account)」をご覧ください。

    > [!NOTE]
    > Azure Databricks 上の Spark クラスターで Azure Data Lake Store を使うこともできます。 方法については、[Azure Databricks での Data Lake Store の使用に関するページ](https://go.microsoft.com/fwlink/?linkid=864084)をご覧ください。

4. SQL ステートメントを実行し、サンプルの JSON データ ファイル **small_radio_json.json** のデータを使って、一時テーブルを作成します。 次のスニペットでは、プレースホルダーの値をコンテナー名およびストレージ アカウント名に置き換えます。 スニペットをノートブックのコード セルに貼り付けて、Shift + Enter キーを押します。 スニペットの `path` は、Azure ストレージ アカウントにアップロードしたサンプルの JSON ファイルの場所を示します。

    ```sql
    %sql 
    CREATE TEMPORARY TABLE radio_sample_data
    USING json
    OPTIONS (
     path "wasbs://{YOUR CONTAINER NAME}@{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net/small_radio_json.json"
    )
    ```

    コマンドが正常に完了すると、JSON ファイルのすべてのデータが Databricks クラスター内のテーブルとして取り込まれます。

    `%sql` 言語のマジック コマンドを使うと、ノートブックから SQL コードを実行できます (ノートブックが別の種類であっても)。 詳しくは、「[Mixing languages in a notebook](https://docs.azuredatabricks.net/user-guide/notebooks/index.html#mixing-languages-in-a-notebook)」(ノートブックに言語を混在させる) をご覧ください。

5. 実行するクエリについて詳しく理解するため、サンプルの JSON データのスナップショットを見てみます。 次のスニペットをコード セルに貼り付けて、**Shift + Enter** キーを押します。

    ```sql
    %sql 
    SELECT * from radio_sample_data
    ```

6. 次のスクリーンショットのような表形式の出力が表示されます (一部の列のみ示してあります)。

    ![サンプルの JSON データ](./media/quickstart-create-databricks-workspace-portal/databricks-sample-csv-data.png "サンプルの JSON データ")

    サンプルには、ラジオ チャンネルの視聴者の性別 (列名: **gender**) および登録が無料か有料か (列名: **level**) に関するデータが含まれます。

7. このデータのビジュアル表現を作成し、各性別について、無料アカウント ユーザーの数と有料登録者の数がわかるようにします。 表形式の出力の下部で、**[Bar chart]\(棒グラフ\)** アイコンをクリックした後、**[Plot Options]\(プロット オプション\)** をクリックします。

    ![棒グラフを作成する](./media/quickstart-create-databricks-workspace-portal/create-plots-databricks-notebook.png "棒グラフを作成する")

8. **[Customize Plot]\(プロットのカスタマイズ\)** で、スクリーンショットに示すように値をドラッグ アンド ドロップします。

    ![棒グラフをカスタマイズする](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-customize-plot.png "棒グラフをカスタマイズする")

    * **[Keys]\(キー\)** を **gender** に設定します。
    * **[Series groupings]\(系列グループ\)** を **level** に設定します。
    * **[Values]\(値\)** を **level** に設定します。
    * **[Aggregation]\(集計\)** を **[COUNT]\(個数\)** に設定します。

    **[Apply]**をクリックします。

9. 出力は、次のスクリーンショットのようなビジュアル表現になります。

     ![棒グラフをカスタマイズする](./media/quickstart-create-databricks-workspace-portal/databricks-sql-query-output-bar-chart.png "棒グラフをカスタマイズする")

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Spark クラスターを作成するときに **[Terminate after __ minutes of inactivity]\(アクティビティが __ 分ない場合は終了する\)** チェック ボックスをオンにした場合、非アクティブ状態で指定した時間が経過すると、クラスターは自動的に終了します。

チェック ボックスをオンにしなかった場合は、クラスターを手動で終了する必要があります。 そのためには、Azure Databricks ワークスペースの左側のウィンドウで、**[クラスター]** をクリックします。 終了するクラスターで、**[Actions]\(アクション\)** 列の下にある省略記号をポイントし、**[Terminate]\(終了\)** アイコンをクリックします。

![Databricks クラスターを終了する](./media/quickstart-create-databricks-workspace-portal/terminate-databricks-cluster.png "Databricks クラスターを終了する")

## <a name="next-steps"></a>次の手順

この記事では、Azure Databricks に Spark クラスターを作成し、Azure Storage のデータを使って Spark ジョブを実行しました。 [Spark のデータ ソース](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html)を見て、他のデータ ソースから Azure Databricks にデータをインポートする方法を学習することもできます。 Azure Databricks で Azure Data Lake Store を使う方法を学習する場合は、次の記事に進んでください。

> [!div class="nextstepaction"]
>[Azure Databricks で Data Lake Store を使う](https://go.microsoft.com/fwlink/?linkid=864084)
