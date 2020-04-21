---
title: チュートリアル ‐ Azure Synapse Analytics:Synapse 用の Spark ジョブ定義
description: チュートリアル - Azure Synapse Analytics を使用して Spark ジョブ定義を作成し、Synapse Spark プールに送信します。
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: d5443a2db6f4fecbd84ef51166f44c3a6e920aee
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81425911"
---
# <a name="tutorial-use-azure-synapse-analytics-to-create-apache-spark-job-definitions-for-synapse-spark-pools"></a>チュートリアル:Azure Synapse Analytics を使用して Synapse Spark プールの Apache Spark ジョブ定義を作成する

このチュートリアルでは、Azure Synapse Analytics を使用して Spark ジョブ定義を作成し、その後 Synapse Spark プールに送信する方法を示します。 このプラグインには、次のような使い方があります。

* Synapse Spark プールで Spark ジョブ定義を開発して送信します。
* 送信後にジョブの詳細を表示します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * Synapse Spark プールで Spark ジョブ定義を開発して送信します。
> * 送信後にジョブの詳細を表示します。

## <a name="prerequisites"></a>前提条件

* Azure Synapse Analytics ワークスペース。 手順については、[Azure Synapse Analytics ワークスペースの作成](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace)に関するページを参照してください。

## <a name="get-started"></a>はじめに

Spark ジョブ定義を送信する前に、使用する ADLS Gen2 ファイルシステムのストレージ BLOB データ所有者である必要があります。 そうでない場合は、手動でアクセス許可を追加する必要があります。

### <a name="scenario-1-add-permission"></a>シナリオ 1:アクセス許可の追加

1. [Microsoft Azure](https://ms.portal.azure.com) を開き、[ストレージ アカウント] を開きます。

2. **[コンテナー]** をクリックし、**ファイル システム**を作成します。 このチュートリアルでは `sparkjob` を使用します。

    ![[送信] ボタンをクリックして、Spark ジョブ定義を送信します](./media/apache-spark-job-definitions/open-azure-container.png)

    ![[Spark Submission]\(Spark 送信\) ダイアログ ボックス](./media/apache-spark-job-definitions/create-new-filesystem.png)

3. `sparkjob` を開き、 **[アクセス制御 (IAM)]** をクリックし、 **[追加]** を選択して **[ロールの割り当ての追加]** を選択します。

    ![[送信] ボタンをクリックして、Spark ジョブ定義を送信します](./media/apache-spark-job-definitions/add-role-assignment-01.png)

    ![[送信] ボタンをクリックして、Spark ジョブ定義を送信します](./media/apache-spark-job-definitions/add-role-assignment-02.png)

4. **[ロールの割り当て]** をクリックし、ユーザー名を入力して、ユーザー ロールを確認します。

    ![[送信] ボタンをクリックして、Spark ジョブ定義を送信します](./media/apache-spark-job-definitions/verify-user-role.png)

### <a name="scenario-2-prepare-folder-structure"></a>シナリオ 2: フォルダー構造の準備

Spark ジョブ定義を送信する前に、ファイルを ADLS Gen2 にアップロードし、そこにフォルダー構造を準備する必要があります。 Synapse Studio のストレージ ノードを使用して、ファイルを格納します。

1. [Azure Synapse Analytics](https://web.azuresynapse.net/) を開きます。

2. **[データ]** をクリックし、 **[ストレージ アカウント]** を選択して、関連するファイルを ADLS Gen2 ファイル システムにアップロードします。 Scala、Java、.NET、Python がサポートされています。 このチュートリアルでは図の例をデモとして使用しますが、プロジェクト構造は必要に応じて変更できます。

    ![Spark ジョブ定義の値を設定する](./media/apache-spark-job-definitions/prepare-project-structure.png)

## <a name="create-a-spark-job-definition"></a>Spark ジョブ定義を作成する

1. [Azure Synapse Analytics](https://web.azuresynapse.net/) を開き、 **[開発]** を選択します。

2. 左側のウィンドウで **[Spark job definitions]\(Spark ジョブ定義\)** を選択します。

3. [Spark job definitions]\(Spark ジョブ定義\) の右側にある **[アクション]** ノードをクリックします。

     ![新しい Spark ジョブ定義を作成する](./media/apache-spark-job-definitions/create-new-definition-01.png)

4. **[アクション]** ドロップダウン リストから **[New Spark job definition]\(新しい Spark ジョブ定義\)** を選択します。

     ![新しい Spark ジョブ定義を作成する](./media/apache-spark-job-definitions/create-new-definition-02.png)

5. [New Spark job definition]\(新しい Spark ジョブ定義\) ウィンドウで、言語を選択し、次の情報を指定します。  

   * **[言語]** で **[Spark(Scala)]** を選択します。

    |  プロパティ   | 説明   |  
    | ----- | ----- |  
    |Job definition name (ジョブ定義名)| Spark ジョブ定義の名前を入力します。  このチュートリアルでは `job definition sample` を使用します。 この名前は公開されるまでいつでも更新できます。|  
    |Main definition file (メイン定義ファイル)| ジョブに使用されるメイン ファイルです。 ストレージから JAR ファイルを選択します。 **[ファイルのアップロード]** を選択して、ファイルをストレージ アカウントにアップロードできます。 |
    |メイン クラス名| 完全修飾識別子またはメイン定義ファイル内のメイン クラス。|
    |コマンドライン引数| ジョブに対する省略可能な引数。|
    |参照ファイル| メイン定義ファイル内で参照に使用される追加ファイル。 **[ファイルのアップロード]** を選択して、ファイルをストレージ アカウントにアップロードできます。|
    |Spark プール| 選択した Spark プールにジョブが送信されます。|
    |Spark のバージョン| Spark プールが実行されている Spark のバージョン。|
    |エグゼキュータ| ジョブ用の指定された Spark プール内で提供されるエグゼキュータの数。|
    |Executor size (エグゼキュータのサイズ)| ジョブ用の指定された Spark プール内で提供される、エグゼキュータに使用するコアとメモリの数。|  
    |Driver size (ドライバー サイズ)| ジョブ用の指定された Spark プール内で提供される、ドライバーに使用するコアとメモリの数。|

    ![Spark ジョブ定義の値を設定する](./media/apache-spark-job-definitions/create-scala-definition.png)

   * **[言語]** で **[PySpark (Python)]** を選択します。

    |  プロパティ   | 説明   |  
    | ----- | ----- |  
    |Job definition name (ジョブ定義名)| Spark ジョブ定義の名前を入力します。  このチュートリアルでは `job definition sample` を使用します。 この名前は公開されるまでいつでも更新できます。|  
    |Main definition file (メイン定義ファイル)| ジョブに使用されるメイン ファイルです。 ストレージから PY ファイルを選択します。 **[ファイルのアップロード]** を選択して、ファイルをストレージ アカウントにアップロードできます。|
    |コマンドライン引数| ジョブに対する省略可能な引数。|
    |参照ファイル| メイン定義ファイル内で参照に使用される追加ファイル。 **[ファイルのアップロード]** を選択して、ファイルをストレージ アカウントにアップロードできます。|
    |Spark プール| 選択した Spark プールにジョブが送信されます。|
    |Spark のバージョン| Spark プールが実行されている Spark のバージョン。|
    |エグゼキュータ| ジョブ用の指定された Spark プール内で提供されるエグゼキュータの数。|
    |Executor size (エグゼキュータのサイズ)| ジョブ用の指定された Spark プール内で提供される、エグゼキュータに使用するコアとメモリの数。|  
    |Driver size (ドライバー サイズ)| ジョブ用の指定された Spark プール内で提供される、ドライバーに使用するコアとメモリの数。|

    ![Spark ジョブ定義の値を設定する](./media/apache-spark-job-definitions/create-py-definition.png)

   * **[言語]** で **[.NET Spark(C#/F#)]** を選択します。

    |  プロパティ   | 説明   |  
    | ----- | ----- |  
    |Job definition name (ジョブ定義名)| Spark ジョブ定義の名前を入力します。  このチュートリアルでは `job definition sample` を使用します。 この名前は公開されるまでいつでも更新できます。|  
    |Main definition file (メイン定義ファイル)| ジョブに使用されるメイン ファイルです。 ストレージからの .NET for Spark アプリケーション (メインの実行可能ファイル、ユーザー定義関数を含む DLL、およびその他の必要なファイル) を含む ZIP ファイルを選択します。 **[ファイルのアップロード]** を選択して、ファイルをストレージ アカウントにアップロードできます。|
    |Main executable file (メイン実行可能ファイル)| メイン定義 ZIP ファイル内のメインの実行可能ファイル。|
    |コマンドライン引数| ジョブに対する省略可能な引数。|
    |参照ファイル| メイン定義 ZIP ファイル (つまり、依存 jar ファイル、追加のユーザー定義関数 DLL ファイル、およびその他の構成ファイル) に含まれていない .NET for Spark アプリケーションを実行するために、ワーカー ノードによって必要とされる追加のファイル。 **[ファイルのアップロード]** を選択して、ファイルをストレージ アカウントにアップロードできます。|
    |Spark プール| 選択した Spark プールにジョブが送信されます。|
    |Spark のバージョン| Spark プールが実行されている Spark のバージョン。|
    |エグゼキュータ| ジョブ用の指定された Spark プール内で提供されるエグゼキュータの数。|
    |Executor size (エグゼキュータのサイズ)| ジョブ用の指定された Spark プール内で提供される、エグゼキュータに使用するコアとメモリの数。|  
    |Driver size (ドライバー サイズ)| ジョブ用の指定された Spark プール内で提供される、ドライバーに使用するコアとメモリの数。|

    ![Spark ジョブ定義の値を設定する](./media/apache-spark-job-definitions/create-net-definition.png)

6. **[Publish]\(公開\)** を選択して、Spark ジョブ定義を保存します。

    ![Spark ジョブ定義を公開する](./media/apache-spark-job-definitions/publish-net-definition.png)

## <a name="submit-a-spark-job-definition"></a>Spark ジョブ定義を送信する

Spark ジョブ定義を作成したら、それを Synapse Spark プールに送信できます。 ここでサンプルを試す前に、「**はじめに**」の手順を実行していることを確認してください。

### <a name="scenario-1-submit-spark-job-definition"></a>シナリオ 1:Spark ジョブ定義を送信する

1. Spark ジョブ定義ウィンドウをクリックして開きます。

      ![送信する Spark ジョブ定義を開く ](./media/apache-spark-job-definitions/open-spark-definition.png)

2. **[送信]** アイコンをクリックし、選択した Spark プールにプロジェクトを送信します。 **[Spark monitoring URL]\(Spark 監視 URL\)** タブをクリックして、Spark アプリケーションの LogQuery を表示できます。

    ![[送信] ボタンをクリックして、Spark ジョブ定義を送信します](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![[Spark Submission]\(Spark 送信\) ダイアログ ボックス](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-spark-job-running-progress"></a>シナリオ 2: Spark ジョブの実行の進行状況を表示する

1. **[モニター]** をクリックし、 **[Spark アプリケーション]** オプションを選択します。 送信された Spark アプリケーションを見つけることができます。

    ![Spark アプリケーションを表示する](./media/apache-spark-job-definitions/view-spark-application.png)

2. 次に、[Spark アプリケーション] をクリックすると、 **[LogQuery]** ウィンドウが表示されます。 **[LogQuery]** ではジョブ実行の進行状況を表示できます。

    ![Spark アプリケーションの LogQuery を表示する](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>シナリオ 3: 出力ファイルを確認する

 1. **[データ]** をクリックし、 **[ストレージ アカウント]** を選択します。 正常に実行された後、ADLS Gen2 ストレージにアクセスして、出力が生成されたことを確認できます。

    ![出力ファイルを表示する](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Synapse Analytics を使用して Spark ジョブ定義を作成し、その後 Synapse Spark プールに送信する方法を示しました。 次に、Azure Synapse Analytics を使用して Power BI データセットを作成し、Power BI データを管理できます。 

- [Power BI Desktop のデータに接続](https://docs.microsoft.com/power-bi/desktop-quickstart-connect-to-data)
- [Power BI による視覚化](../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
