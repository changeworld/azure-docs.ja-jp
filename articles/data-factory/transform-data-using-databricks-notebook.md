---
title: Azure Data Factory で Databricks Notebook アクティビティを使用して Databricks Notebook を実行する
description: Azure Data Factory で Databricks Notebook アクティビティを使用して、Databricks ジョブ クラスターに対して Databricks ノートブックを実行する方法について説明します。
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/12/2018
ms.author: abnarain
ms.reviewer: douglasl
ms.openlocfilehash: 6c3eb1e86d2dd98590d3a17dedf76c2a694f1a6d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
---
# <a name="run-a-databricks-notebook-with-the-databricks-notebook-activity-in-azure-data-factory"></a>Azure Data Factory で Databricks Notebook アクティビティを使用して Databricks ノートブックを実行する

このチュートリアルでは、Azure Portal を使用して、Databricks ジョブ クラスターに対して Databricks ノートブックを実行する Azure Data Factory パイプラインを作成します。 また、Databricks ノートブックの実行中に、Azure Data Factory パラメーターを渡します。

このチュートリアルでは、以下の手順を実行します。

  - データ ファクトリを作成します。

  - Databricks Notebook アクティビティを使用するパイプラインを作成します。

  - パイプラインの実行をトリガーする。

  - パイプラインの実行を監視します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

## <a name="prerequisites"></a>前提条件

  - **Azure Databricks ワークスペース**。 [Databricks ワークスペースを作成する](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)か、既存のワークスペースを使用します。 Azure Databricks ワークスペースに、Python ノートブックを作成します。 その後、Azure Data Factory を使用してノートブックを実行し、パラメーターを渡します。

## <a name="create-a-data-factory"></a>Data Factory を作成する。

1.  Web ブラウザー (**Microsoft Edge** または **Google Chrome**) を起動します。 現在、Data Factory の UI がサポートされる Web ブラウザーは Microsoft Edge と Google Chrome だけです。

2.  左側のメニューの **[新規]** を選択し、**[データ + 分析]** を選択して、**[Data Factory]** を選択します。

    ![新しいデータ ファクトリの作成](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image1.png)

3.  **[新しいデータ ファクトリ]** ウィンドウで、**[名前]** に「**ADFTutorialDataFactory**」と入力します。

    Azure データ ファクトリの名前は *グローバルに一意*にする必要があります。 次のエラーが発生した場合は、データ ファクトリの名前を変更してください  (**\<yourname\>ADFTutorialDataFactory** など)。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](https://docs.microsoft.com/azure/data-factory/naming-rules)に関する記事を参照してください。

    ![新しいデータ ファクトリの名前を指定する](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image2.png)

4.  **[サブスクリプション]** で、データ ファクトリを作成する Azure サブスクリプションを選択します。

5.  **[リソース グループ]** で、次の手順のいずれかを行います。
    
    - **[Use existing]\(既存のものを使用\)** を選択し、ドロップダウン リストから既存のリソース グループを選択します。
    
    - **[新規作成]** を選択し、リソース グループの名前を入力します。

    このクイックスタートの一部の手順は、**ADFTutorialResourceGroup** という名前のリソース グループを使用することを前提としています。 リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)に関するページを参照してください。

1.  **[バージョン]** では、**[V2 (プレビュー)]** を選択します。

2.  **[場所]** で、データ ファクトリの場所を選択します。

    現在、Data Factory V2 でデータ ファクトリを作成できるリージョンは、米国東部、米国東部 2、および西ヨーロッパだけです。 Data Factory で使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は他のリージョンに配置できます。

3.  **[ダッシュボードにピン留めする]** をオンにします。

4.  **[作成]** を選択します。

5.  ダッシュボードに、**[Deploying Data Factory]\(Data Factory をデプロイしています\)** というステータスを示した次のタイルが表示されます。

    ![](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image3.png)

6.  作成が完了すると、**[データ ファクトリ]** ページが表示されます。 **[作成と監視]** タイルを選択して、別のタブで Data Factory UI アプリケーションを起動します。

    ![データ ファクトリ UI アプリケーションを起動する](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image4.png)

## <a name="create-linked-services"></a>リンクされたサービスを作成します

このセクションでは、Databricks のリンクされたサービスを作成します。 このリンクされたサービスには、Databricks クラスターへの接続情報が含まれています。

### <a name="create-an-azure-databricks-linked-service"></a>Azure Databricks のリンクされたサービスを作成する

1.  **[Let's get started]\(始めましょう\)** ページで、左側のパネルで **[編集]** タブに切り替えます。

    ![新しいリンクされたサービスを編集する](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image5.png)

2.  ウィンドウの下部にある **[接続]** を選択して、**[+ 新規]** を選択します。
    
    ![新しい接続を作成する](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image6.png)

3.  **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、**[データ ストア]**\>**[Azure Databricks]** の順に選択し、**[続行]** を選択します。
    
    ![Databricks のリンクされたサービスを指定する](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image7.png)

4.  **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、次の手順を完了します。
    
    1.  **[名前]** に「***AzureDatabricks\_LinkedService***」と入力します。
    
    2.  **[クラスター]** で **[新しいクラスター]** を選択します。
    
    3.  **[Domain/ Region]\(ドメイン/リージョン\)** で、Azure Databricks ワークスペースが配置されているリージョンを選択します。
    
    4.  **[Cluster node type]\(クラスター ノードの種類\)** で、このチュートリアルでは **[Standard\_D3\_v2]** を選択します。
    
    5.  **[アクセス トークン]** で、Azure Databricks ワークスペースからトークンを生成します。 手順については、[こちら](https://docs.databricks.com/api/latest/authentication.html#generate-token)を参照してください。
    
    6.  **[クラスター バージョン]** で、**[4.0 ベータ]** (最新バージョン) を選択します。
    
    7.  **[Number of worker nodes]\(worker ノードの数\)** で、「**2**」と入力します。
    
    8.  **[完了]** を選択します。

        ![リンクされたサービスの作成を完了する](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image8.png)

## <a name="create-a-pipeline"></a>パイプラインを作成する。

1.  **+** (正符号) ボタンを選択し、メニューの **[パイプライン]** を選択します。

    ![新しいパイプラインを作成するためのボタン](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image9.png)

2.  **パイプライン**で使用される**パラメーター**を作成します。 後で、このパラメーターを Databricks Notebook アクティビティに渡します。 空のパイプラインで **[パラメーター]** タブをクリックし、次に **[新規]** をクリックして、"**name**" という名前を付けます。

    ![新しいパラメーターを作成する](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image10.png)

    ![name パラメーターを作成する](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image11.png)

3.  **[アクティビティ]** ツールボックスで **[Databricks]** を展開します。 **[アクティビティ]** ツールボックスからパイプライン デザイナー画面に **Notebook** アクティビティをドラッグします。

    ![ノートブックをデザイナー画面にドラッグする](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image12.png)

4.  下部の **Databricks** **Notebook** アクティビティ ウィンドウのプロパティで、次の手順を完了します。

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[設定]** タブに切り替えます。

    b. **myAzureDatabricks\_LinkedService** (前の手順で作成したもの) を選択します。

    c. Databricks **Notebook のパス**を選択します。 ノートブックを作成し、ここでパスを指定しましょう。 次の手順に従って、ノートブックのパスを取得します。

       1. Azure Databricks ワークスペースを起動します。

       2. ワークスペースで**新しいフォルダー**を作成し、**adftutorial** という名前にします。

          ![新しいフォルダーを作成する](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image13.png)

       3. [新しいノートブックを作成](https://docs.databricks.com/user-guide/notebooks/index.html#creating-a-notebook)します (Python)。**adftutorial** フォルダー の下の **mynotebook** という名前にして、**[作成]** をクリックします。

          ![新しいノートブックを作成する](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image14.png)

          ![新しいノートブックのプロパティを設定する](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image15.png)

       4. 新しく作成されたノートブック "mynotebook" に次のコードを追加します。

           ```
           # Creating widgets for leveraging parameters, and printing the parameters

           dbutils.widgets.text("input", "","")
           dbutils.widgets.get("input")
           y = getArgument("input")
           print "Param -\'input':"
           print y
           ```

           ![パラメーターのウィジェットを作成する](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image16.png)

       5. ここでの **Notebook パス**は、**adftutorial/mynotebook** です。

5.  **Data Factory UI 作成ツール**に戻ります。 **Notebook1 アクティビティ**の **[設定]** タブに移動します。 
    
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。  Notebook アクティビティに**パラメーターを追加**します。 前に**パイプライン**に追加したのと同じパラメーターを使用します。

       ![パラメーターを追加する](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image17.png)

    b.  パラメーターの名前を **input** にして、値を式 **@pipeline().parameters.name** として指定します。

6.  パイプラインを検証するために、ツール バーの **[検証]** ボタンを選択します。 検証ウィンドウを閉じるには、**\>\>** (右矢印) ボタンを選択します。

    ![パイプラインを検証する](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image18.png)

7.  **[すべて公開]** を選択します。 Data Factory UI により、エンティティ (リンクされたサービスとパイプライン) が Azure Data Factory サービスに公開されます。

    ![新しいデータ ファクトリ エンティティを公開する](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image19.png)

## <a name="trigger-a-pipeline-run"></a>パイプラインの実行をトリガーする

ツール バーの **[トリガー]** を選択し、**[Trigger Now]\(今すぐトリガー\)** を選択します。

![[Trigger Now]\(今すぐトリガー\) コマンドを選択する](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image20.png)

**[Pipeline Run]\(パイプラインの実行\)** ダイアログ ボックスで、**name** パラメーターの指定が求められます。 ここでは、パラメーターとして **/path/filename** を使用します。 **[完了]** をクリックします。

![name パラメーターの値を指定する](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image21.png)

## <a name="monitor-the-pipeline-run"></a>パイプラインの実行を監視します

1.  **[監視]** タブに切り替えます。パイプラインが実行されていることを確認します。 ノートブックが実行される Databricks ジョブ クラスターを作成するには、5 分から 8 分ほどかかります。

    ![パイプラインの監視](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image22.png)

2.  **[最新の情報に更新]** を定期的にクリックして、パイプラインの実行の状態を確認します。

3.  パイプラインの実行に関連付けられているアクティビティの実行を表示するために、**[アクション]** 列の **[View Activity Runs]\(アクティビティの実行の表示\)** を選択します。

    ![アクティビティの実行を表示する](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image23.png)

上部の **[パイプライン]** リンクを選択すると、パイプラインの実行ビューに戻ることができます。

## <a name="verify-the-output"></a>出力を検証する

**Azure Databricks ワークスペース**にログオンし、**[クラスター]** に移動すると、**ジョブ**の状態 (*実行の保留中、実行中、または終了*) を確認できます。

![ジョブ クラスターとジョブを表示する](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image24.png)

**ジョブ名**をクリックすると、より詳しい情報が表示されます。 正常に実行されると、渡されたパラメーターと、Python ノートブックの出力を検証できます。

![実行の詳細と出力を表示する](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image25.png)

## <a name="next-steps"></a>次の手順

このサンプルのパイプラインでは、Databricks Notebook アクティビティをトリガーし、それにパラメーターを渡します。 以下の方法について学習しました。

  - データ ファクトリを作成します。

  - Databricks Notebook アクティビティを使用するパイプラインを作成します。

  - パイプラインの実行をトリガーする。

  - パイプラインの実行を監視します。
