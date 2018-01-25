---
title: "Azure Data Factory で Spark を使用してデータを変換する | Microsoft Docs"
description: "このチュートリアルでは、Azure Data Factory で Spark アクティビティを使用してデータを変換するための詳細な手順を説明します。"
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/10/2018
ms.author: shengc
ms.openlocfilehash: c2ec6706c92f229bb05ad9a19246c6ffe5f615c9
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/18/2018
---
# <a name="transform-data-in-the-cloud-by-using-spark-activity-in-azure-data-factory"></a>Azure Data Factory で Spark アクティビティを使用してクラウドのデータを変換する
このチュートリアルでは、Azure Portal を使用して Data Factory パイプラインを作成します。このパイプラインで、Spark アクティビティとオンデマンドの HDInsight のリンクされたサービスを使用してデータを変換します。 このチュートリアルでは、以下の手順を実行します。

> [!div class="checklist"]
> * データ ファクトリを作成します。 
> * Spark アクティビティを含むパイプラインを作成する
> * パイプラインの実行をトリガーする
> * パイプラインの実行を監視します。

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[Data Factory バージョン 1 のドキュメント](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="prerequisites"></a>前提条件
* **Azure Storage アカウント**。 Python スクリプトと入力ファイルを作成し、Azure ストレージにアップロードします。 Spark プログラムからの出力は、このストレージ アカウントに格納されます。 オンデマンドの Spark クラスターでは、同じストレージ アカウントがプライマリ ストレージとして使用されます。  
* **Azure PowerShell**。 [Azure PowerShell のインストールと構成の方法](/powershell/azure/install-azurerm-ps)に関するページに記載されている手順に従います。


### <a name="upload-python-script-to-your-blob-storage-account"></a>Python スクリプトを BLOB ストレージ アカウントにアップロードする
1. 次の内容が含まれた、**WordCount_Spark.py** という名前の Python ファイルを作成します。 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. **&lt;storageAccountName&gt;** を Azure ストレージ アカウントの名前に置き換えます。 その後、ファイルを保存します。 
3. Azure BLOB ストレージで、**adftutorial** という名前のコンテナーを作成します (存在しない場合)。 
4. **spark** という名前のフォルダーを作成します。
5. **spark** フォルダーの下に、**script** という名前のサブフォルダーを作成します。 
6. **WordCount_Spark.py** ファイルを **script** サブフォルダーにアップロードします。 


### <a name="upload-the-input-file"></a>入力ファイルをアップロードする
1. **minecraftstory.txt** という名前のファイルを作成し、任意のテキストを入力しておきます。 このテキストの単語数が Spark プログラムによってカウントされます。 
2. `spark` フォルダーに、`inputfiles` という名前のサブフォルダーを作成します。 
3. `minecraftstory.txt` を `inputfiles` サブフォルダーにアップロードします。 

## <a name="create-a-data-factory"></a>Data Factory を作成する。

1. 左側のメニューで **[新規]** をクリックし、**[データ + 分析]**、**[Data Factory]** の順にクリックします。 
   
   ![New->DataFactory](./media/tutorial-transform-data-spark-portal/new-azure-data-factory-menu.png)
2. **[新しいデータ ファクトリ]** ページで、**[名前]** に「**ADFTutorialDataFactory**」と入力します。 
      
     ![[新しいデータ ファクトリ] ページ](./media/tutorial-transform-data-spark-portal/new-azure-data-factory.png)
 
   Azure データ ファクトリの名前は **グローバルに一意**にする必要があります。 名前フィールドで次のエラーが発生した場合は、データ ファクトリの名前を変更してください (yournameADFTutorialDataFactory など)。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事を参照してください。
  
     ![名前は使用できません - エラー](./media/tutorial-transform-data-spark-portal/name-not-available-error.png)
3. データ ファクトリを作成する Azure **サブスクリプション**を選択します。 
4. **[リソース グループ]** について、次の手順のいずれかを行います。
     
      - **[Use existing (既存のものを使用)]**を選択し、ドロップダウン リストから既存のリソース グループを選択します。 
      - **[新規作成]**を選択し、リソース グループの名前を入力します。   
         
      このクイック スタートの一部の手順は、**ADFTutorialResourceGroup** という名前のリソース グループを使用することを前提としています。 リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/resource-group-overview.md)に関するページを参照してください。  
4. **バージョン**として **[V2 (プレビュー)]** を選択します。
5. データ ファクトリの **場所** を選択します。 現在、Data Factory V2 でデータ ファクトリを作成できるリージョンは、米国東部、米国東部 2、および西ヨーロッパだけです。 データ ファクトリで使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は他のリージョンに配置できます。
6. **[ダッシュボードにピン留めする]** をオンにします。     
7. **Create** をクリックしてください。
8. ダッシュボードに、**[Deploying data factory]\(データ ファクトリをデプロイしています\)** というステータスを示したタイルが表示されます。 

    ![[Deploying data factory]\(データ ファクトリをデプロイしています\) タイル](media//tutorial-transform-data-spark-portal/deploying-data-factory.png)
9. 作成が完了すると、図に示されているような **[Data Factory]** ページが表示されます。
   
    ![データ ファクトリのホーム ページ](./media/tutorial-transform-data-spark-portal/data-factory-home-page.png)
10. **[Author & Monitor]\(作成と監視\)** タイルをクリックして、別のタブで Data Factory UI アプリケーションを起動します。

## <a name="create-linked-services"></a>リンクされたサービスを作成します
このセクションでは、次の 2 つのリンクされたサービスを作成します。 
    
- Azure Storage アカウントをデータ ファクトリにリンクする、**Azure Storage のリンクされたサービス**。 このストレージは、オンデマンドの HDInsight クラスターによって使用されます。 ここには、実行される Spark スクリプトも含まれています。 
- **オンデマンドの HDInsight のリンクされたサービス**。 Azure Data Factory によって自動的に HDInsight クラスターが作成され、Spark プログラムが実行されます。その後、アイドル状態が事前に構成した時間を超えたら、HDInsight クラスターは削除されます。 

### <a name="create-an-azure-storage-linked-service"></a>Azure Storage のリンクされたサービスを作成する

1. **開始**ページで、次の図に示すように、左パネルの **[編集]** タブに切り替えます。 

    ![[Create Pipeline]\(パイプラインの作成\) タイル](./media/tutorial-transform-data-spark-portal/get-started-page.png)

2. ウィンドウの下部にある **[接続]** をクリックし、**[+ 新規]** をクリックします。 

    ![新しい接続ボタン](./media/tutorial-transform-data-spark-portal/new-connection.png)
3. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで **[Azure Blob Storage]** を選択し、**[続行]** をクリックします。 

    ![[Azure Blob Storage] の選択](./media/tutorial-transform-data-spark-portal/select-azure-storage.png)
4. 目的の **Azure Storage アカウント名**を選択し、**[保存]** をクリックします。 

    ![BLOB ストレージ アカウントの指定](./media/tutorial-transform-data-spark-portal/new-azure-storage-linked-service.png)


### <a name="create-an-on-demand-hdinsight-linked-service"></a>オンデマンドの HDInsight のリンクされたサービスを作成する

1. **[+ 新規]** ボタンをもう一度作成して、別のリンクされたサービスを作成します。 
2. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで **[Azure HDInsight]** を選択し、**[続行]** をクリックします。 

    ![Azure HDInsight を選択する](./media/tutorial-transform-data-spark-portal/select-azure-hdinsight.png)
2. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、次の手順を行います。 

    1. **[名前]** に「**AzureHDInsightLinkedService**」と入力します。
    2. **[Type]\(タイプ\)** で **[On-demand HDInsight]\(オンデマンド HDInsight\)** が選択されていることを確認します。
    3. **[Azure Storage Linked Service]\(Azure Storage のリンクされたサービス\)** で **[AzureStorage1]** を選択します。 このリンクされたサービスは、前の手順で作成したものです。 別の名前を使用した場合は、このフィールドに適切な名前を指定します。 
    4. **[クラスターの種類]** で **[spark]** を選択します。
    5. HDInsight クラスターを作成する権限を持つ**サービス プリンシパルの ID** を入力します。 このサービス プリンシパルは、サブスクリプションまたはクラスターが作成されるリソース グループの共同作成者ロールのメンバーである必要があります。 詳細については、[Azure Active Directory のアプリケーションとサービス プリンシパルの作成](../azure-resource-manager/resource-group-create-service-principal-portal.md)に関するページを参照してください。
    6. **サービス プリンシパルのキー**を入力します。 
    7. **リソース グループ**のデータ ファクトリを作成するときに使用したのと同じリソース グループを選択します。 Spark クラスターは、このリソース グループに作成されます。 
    8. **[OS の種類]** を展開します。
    9. クラスター **ユーザー**の**名前**を入力します。 
    10. ユーザーの**パスワード**を入力します。 
    11. **[Save]** をクリックします。 

        ![HDInsight のリンクされたサービスの設定](./media/tutorial-transform-data-spark-portal/azure-hdinsight-linked-service-settings.png)

> [!NOTE]
> Azure HDInsight には、サポートされる各 Azure リージョンで使用できるコアの合計数に制限があります。 オンデマンドの HDInsight のリンクされるサービスの場合、HDInsight クラスターは、プライマリ ストレージとして使用される Azure ストレージと同じ場所に作成されます。 クラスターを正常に作成するための十分なコア クォータがあることを確認します。 詳細については、「[Hadoop、Spark、Kafka などの HDInsight クラスターをセットアップする](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。 

## <a name="create-a-pipeline"></a>パイプラインを作成する。

2. [+] (プラス) ボタンをクリックし、メニューの **[パイプライン]** をクリックします。

    ![新しいパイプライン メニュー](./media/tutorial-transform-data-spark-portal/new-pipeline-menu.png)
3. **[アクティビティ]** ツールボックスで **[HDInsight]** を展開し、**[アクティビティ]** ツールボックスからパイプライン デザイナー画面に **Spark** アクティビティをドラッグ アンド ドロップします。 

    ![Spark アクティビティをドラッグ アンド ドロップする](./media/tutorial-transform-data-spark-portal/drag-drop-spark-activity.png)
4. 下部の **Spark** アクティビティ ウィンドウのプロパティで、次の手順を実行します。 

    1. **[HDI Cluster]\(HDI クラスター\)** タブに切り替えます。
    2. 前の手順で作成した **[AzureHDInsightLinkedService]** を選択します。 
        
    ![HDInsight のリンクされたサービスを指定する](./media/tutorial-transform-data-spark-portal/select-hdinsight-linked-service.png)
5. **[Script/Jar]\(スクリプト/Jar\)** タブに切り替えた後、次の手順を実行します。 

    1. **[Job Linked Service]\(ジョブのリンクされたサービス\)** で **[AzureStorage1]** を選択します。
    2. **[ストレージを参照]** をクリックします。 
    3. **adftutorial/spark/script フォルダー**に移動します。**WordCount_Spark.py** を選択し、**[完了]** をクリックします。      

    ![Spark スクリプトを指定する](./media/tutorial-transform-data-spark-portal/specify-spark-script.png)
6. パイプラインを検証するために、ツール バーの **[検証]** ボタンをクリックします。 **右矢印** (>>) ボタンをクリックして、検証ウィンドウを閉じます。 
    
    ![検証ボタン](./media/tutorial-transform-data-spark-portal/validate-button.png)
7. **[発行]**をクリックします。 Data Factory UI により、エンティティ (リンクされたサービスとパイプライン) が Azure Data Factory サービスに発行されます。 
    
    ![[発行] ボタン](./media/tutorial-transform-data-spark-portal/publish-button.png)

## <a name="trigger-a-pipeline-run"></a>パイプラインの実行をトリガーする
ツール バーの **[トリガー]** をクリックし、**[Trigger Now]\(今すぐトリガー\)** をクリックします。 

![[Trigger Now]\(今すぐトリガー\)](./media/tutorial-transform-data-spark-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>パイプラインの実行を監視します

1. **[監視]** タブに切り替えます。パイプラインが実行されていることを確認します。 Spark クラスターの作成には約 20 分かかります。 

    ![パイプラインの実行を監視する](./media/tutorial-transform-data-spark-portal/monitor-tab.png)
2. **[更新]** を定期的にクリックして、パイプラインの実行の状態を確認します。 

    ![パイプラインの実行の状態](./media/tutorial-transform-data-spark-portal/pipeline-run-succeeded.png)
1. パイプラインの実行に関連付けられているアクティビティの実行を表示するために、[アクション] 列の **[View Activity Runs]\(アクティビティの実行の表示\)** アクションをクリックします。 上部の **[パイプライン]** リンクをクリックすると、パイプラインの実行ビューに戻ることができます。

    ![アクティビティの実行ビュー](./media/tutorial-transform-data-spark-portal/activity-runs.png)

## <a name="verify-the-output"></a>出力を検証する
adftutorial コンテナーの spark/otuputfiles/wordcount フォルダーに出力ファイルが作成されていることを確認します。 

![出力の確認](./media/tutorial-transform-data-spark-portal/verity-output.png)

このファイルには、入力テキスト ファイルに含まれている単語と、各単語がファイル内に出現する回数が含まれています。 例:  

```
(u'This', 1)
(u'a', 1)
(u'is', 1)
(u'test', 1)
(u'file', 1)
```

## <a name="next-steps"></a>次の手順
このサンプルのパイプラインは、Spark アクティビティとオンデマンドの HDInsight のリンクされたサービスを使用して、データを変換します。 以下の方法について学習しました。 

> [!div class="checklist"]
> * データ ファクトリを作成します。 
> * Spark アクティビティを含むパイプラインを作成する
> * パイプラインの実行をトリガーする
> * パイプラインの実行を監視します。

仮想ネットワークにある Azure HDInsight クラスター上で Hive スクリプトを実行してデータを変換する方法については、次のチュートリアルに進んでください。 

> [!div class="nextstepaction"]
> [チュートリアル: Azure Virtual Network で Hive を使用してデータを変換する](tutorial-transform-data-hive-virtual-network-portal.md)





