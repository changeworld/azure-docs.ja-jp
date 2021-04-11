---
title: Synapse Studio を使用して Apache Spark アプリケーションを監視する
description: Synapse Studio を使用して Apache Spark アプリケーションを監視します。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: ea08c82ed5772be7e3a6094f5477c4450975c1fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104775880"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-applications"></a>Synapse Studio を使用して Apache Spark アプリケーションを監視する

Azure Synapse Analytics では、Apache Spark を使用して、ワークスペース内の Apache Spark プールでノートブック、ジョブ、およびその他の種類のアプリケーションを実行できます。

この記事では、Apache Spark アプリケーションを監視する方法を説明し、最新の状態、問題、および進捗状況を監視できるようにします。

このチュートリアルに含まれるタスクは次のとおりです。

* 実行中の Apache Spark アプリケーションを監視する
* 完了した Apache Spark アプリケーションを表示する
* 取り消された Apache Spark アプリケーションを表示する
* 失敗した Apache Spark アプリケーションをデバッグする

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、次の要件を満たしてください。

- Synapse Studio ワークスペース。 手順については、[Synapse Studio ワークスペースの作成](../../machine-learning/how-to-manage-workspace.md#create-a-workspace)に関する記事を参照してください。

- Apache Spark プール。

## <a name="view-apache-spark-applications"></a>Apache Spark アプリケーションを表示する 
すべての Apache Spark アプリケーションは、 **[モニター]**  ->  **[Apache Spark アプリケーション]** から表示できます。
   ![Apache Spark アプリケーション](./media/how-to-monitor-spark-applications/apache-spark-applications.png)

## <a name="view-completed-apache-spark-application"></a>完了した Apache Spark アプリケーションを表示する

**[モニター]** を開き、次に **[Apache Spark アプリケーション]** を選択します。 完了した Apache Spark アプリケーションの詳細を表示するには、Apache Spark アプリケーションを選択し、詳細を表示します。

  ![完了したジョブを選択する](./media/how-to-monitor-spark-applications/select-completed-job.png)

1. **完了したタスク**、**状態**、および **合計実行時間** を確認します。

2. ログ クエリを更新します。

3. **[Spark History Server]** をクリックして、Apache Spark History Server のリンクを開きます。

4. **概要** 情報を確認します。

5. **ログ** を確認します。 ドロップダウン リストからさまざまな種類のログを選択できます。また、 **[ログのダウンロード]** をクリックすることでログ情報をダウンロードでき、さらに **[Filter errors and warnings]\(エラーと警告をフィルター処理する\)** のチェックボックスをオンにすると必要なエラーや警告をフィルタリングすることができます。

6. 生成されたジョブ グラフで、ジョブの概要を表示できます。 既定で、グラフにはすべてのジョブが表示されます。 **ジョブ ID** で、このビューをフィルター処理できます。

7. 既定で、 **[進行状況]** 表示が選択されています。 データ フローを確認するには、 **[表示]** ドロップダウン リストで **[進行状況]** / **[読み取り済み]** / **[書き込み済み]** / **[実行時間]** を選択します。

8. ジョブを再生するには、 **[再生]** ボタンをクリックします。 いつでも **[停止]** ボタンをクリックして停止できます。

9. マウスのスクロールまたはスクロール バーを使用して、ジョブ グラフを拡大したり縮小したりできます。また、 **[ウィンドウのサイズに合わせて大きさを変更]** を選択して、画面に合わせて調整することもできます。

10. ジョブ グラフ ノードには、各ステージの次の情報が表示されます。

    * ID。

    * 名前または説明。

    * 合計タスク数。

    * データの読み取り: 入力サイズとシャッフル読み取りサイズの合計。

    * データ書き込み: 出力サイズとシャッフル書き込みサイズの合計。

    * 実行時間: 最初の試行の開始時刻と最後の試行の完了時刻の間の時間。

    * 行数: 入力レコード、出力レコード、シャッフル読み取りレコード、シャッフル書き込みレコードの合計。

    * 進行状況。

     ![完了したジョブを表示する](./media/how-to-monitor-spark-applications/view-completed-job.png)
    
11. グラフの **[詳細の表示]** をクリックすると、ステージの詳細が表示されます。

    ![ステージの詳細](./media/how-to-monitor-spark-applications/details-for-stage.png)


## <a name="monitor-running-apache-spark-application"></a>実行中の Apache Spark アプリケーションを監視する

**[モニター]** を開き、次に **[Apache Spark アプリケーション]** を選択します。 実行中の Apache Spark アプリケーションの詳細を表示するには、送信している Apache Spark アプリケーションを選択し、詳細を表示します。 Apache Spark アプリケーションがまだ実行中の場合は、進捗状況を監視できます。

   ![実行中のジョブを選択する](./media/how-to-monitor-spark-applications/select-running-job.png)

1. **完了したタスク**、**状態**、および **合計実行時間** を確認します。

2. Apache Spark アプリケーションを **[取り消し]** ます。

3. ログ クエリを **[更新]** します。

4. **[Spark UI]** ボタンをクリックして、Spark ジョブ ページにアクセスします。

5. グラフを表示します。 生成されたジョブ グラフで、ジョブの概要を表示できます。 「[完了した Apache Spark アプリケーションを表示する](#view-completed-apache-spark-application)」の手順 6、7、8、9、10 を参照してください。

6. **概要** 情報を確認します。

7. **[診断]** タブで診断を確認します。

8. このタブで **[ログ]** を確認します。ドロップダウン リストからさまざまな種類のログを選択できます。また、 **[ログのダウンロード]** をクリックすることでログ情報をダウンロードでき、さらに **[Filter errors and warnings]\(エラーと警告をフィルター処理する\)** のチェックボックスをオンにすると必要なエラーや警告をフィルタリングすることができます。

    ![実行中のジョブを表示する](./media/how-to-monitor-spark-applications/view-running-job.png)

## <a name="view-canceled-apache-spark-application"></a>取り消された Apache Spark アプリケーションを表示する

**[モニター]** を開き、次に **[Apache Spark アプリケーション]** を選択します。 取り消された Apache Spark アプリケーションの詳細を表示するには、Apache Spark アプリケーションを選択し、詳細を表示します。

 ![取り消されたジョブを選択する](./media/how-to-monitor-spark-applications/select-cancelled-job.png) 

1. **完了したタスク**、**状態**、および **合計実行時間** を確認します。

2. ログ クエリを更新します。

3. **[Spark History Server]** をクリックして、Apache Spark History Server のリンクを開きます。

4. グラフを表示します。 生成されたジョブ グラフで、ジョブの概要を表示できます。 「[完了した Apache Spark アプリケーションを表示する](#view-completed-apache-spark-application)」の手順 6、7、8、9、10 を参照してください。

5. **概要** 情報を確認します。

6. **ログ** を確認します。 ドロップダウン リストからさまざまな種類のログを選択できます。また、[ログの **ダウンロード**] をクリックすることでログ情報をダウンロードでき、さらに **[Filter errors and warnings]\(エラーと警告をフィルター処理する\)** のチェックボックスをオンにすると必要なエラーや警告をフィルタリングすることができます。

7. グラフの **[詳細の表示]** をクリックすると、ステージの詳細が表示されます。

   ![取り消されたジョブを表示する](./media/how-to-monitor-spark-applications/view-cancelled-job.png)

## <a name="debug-failed-apache-spark-application"></a>失敗した Apache Spark アプリケーションをデバッグする

**[モニター]** を開き、次に **[Apache Spark アプリケーション]** を選択します。 失敗した Apache Spark アプリケーションの詳細を表示するには、Apache Spark アプリケーションを選択し、詳細を表示します。

![失敗したジョブを選択する](./media/how-to-monitor-spark-applications/select-failed-job.png)

1. **完了したタスク**、**状態**、および **合計実行時間** を確認します。

2. ログ クエリを更新します。

3. **[Spark History Server]** をクリックして、Apache Spark History Server のリンクを開きます。

4. グラフを表示します。 生成されたジョブ グラフで、ジョブの概要を表示できます。 「[完了した Apache Spark アプリケーションを表示する](#view-completed-apache-spark-application)」の手順 6、7、8、9、10 を参照してください

5. **概要** 情報を確認します。

6. エラー情報を確認します。

   ![失敗したジョブ情報](./media/how-to-monitor-spark-applications/failed-job-info.png)

## <a name="compare-apache-spark-applications"></a>Apache Spark アプリケーションの比較

アプリケーションを比較する 2 つの方法があります。 **[Compare Application]\(アプリケーションの比較\)** を選択して比較するか、 **[Compare in notebook]\(ノートブックで比較\)** ボタンをクリックしてノートブックで表示することができます。

### <a name="compare-by-choose-an-application"></a>アプリケーションを選択して比較する

**[Compare applications]\(アプリケーションの比較\)** ボタンをクリックし、パフォーマンスを比較するアプリケーションを選択すると、2 つのアプリケーションの違いを直感的に確認できます。

![アプリケーションの比較](./media/how-to-monitor-spark-applications/compare-applications.png)

![アプリケーションの比較の詳細](./media/how-to-monitor-spark-applications/details-compare-applications.png)

1. マウスを使用してアプリケーションをポイントすると、 **[Compare applications]\(アプリケーションの比較\)** アイコンが表示されます。

2. **[Compare applications]\(アプリケーションの比較\)** アイコンをクリックすると、[Compare applications]\(アプリケーションの比較\) ページがポップアップ表示されます。

3. **[アプリケーションの選択]** ボタンをクリックして **[Choose application]\(比較対象アプリケーションの選択\)** ページを開きます。

4. 比較対象のアプリケーションを選択する場合は、アプリケーションの URL を入力するか、繰り返しの一覧から選択する必要があります。 次に、 **[OK]** ボタンをクリックします。 

   ![比較対象アプリケーションの選択](./media/how-to-monitor-spark-applications/choose-comparison-application.png)

5. 比較結果が [compare applications]\(アプリケーションの比較\) ページに表示されます。

   ![比較結果](./media/how-to-monitor-spark-applications/comparison-result.png)

### <a name="compare-by-compare-in-notebook"></a>ノートブックでの比較によって比較する

**[Compare Application]\(アプリケーションの比較\)** ページで **[Compare in notebook]\(ノートブックで比較\)** ボタンをクリックしてノートブックを開きます。 .ipynb ファイルの既定の名前は **Recurrent Application Analytics** です。

![ノートブックで比較](./media/how-to-monitor-spark-applications/compare-in-notebook.png)

Notebook では、Spark プールと言語を設定した後に、Recurrent Application Analytics ファイルを直接実行できます。

![循環アプリケーション分析](./media/how-to-monitor-spark-applications/recurrent-application-analytics.png)

## <a name="next-steps"></a>次のステップ

パイプラインの実行の監視に関する詳細については、[Synapse Studio を使用したパイプラインの実行の監視](how-to-monitor-pipeline-runs.md)に関する記事を参照してください。
