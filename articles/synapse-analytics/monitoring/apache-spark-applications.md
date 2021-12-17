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
ms.custom: contperf-fy21q4
ms.openlocfilehash: f83e84e3aa747c41d0e45a5650c704c796c62447
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2021
ms.locfileid: "112968269"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-applications"></a>Synapse Studio を使用して Apache Spark アプリケーションを監視する

Azure Synapse Analytics では、Apache Spark を使用して、ワークスペース内の Apache Spark プールでノートブック、ジョブ、およびその他の種類のアプリケーションを実行できます。

この記事では、Apache Spark アプリケーションを監視する方法を説明し、最新の状態、問題、および進捗状況を監視できるようにします。

## <a name="view-apache-spark-applications"></a>Apache Spark アプリケーションを表示する 
すべての Apache Spark アプリケーションは、 **[モニター]**  ->  **[Apache Spark アプリケーション]** から表示できます。
   ![Apache Spark アプリケーション](./media/how-to-monitor-spark-applications/apache-spark-applications.png)

## <a name="view-completed-apache-spark-application"></a>完了した Apache Spark アプリケーションを表示する

**[モニター]** を開き、次に **[Apache Spark アプリケーション]** を選択します。 完了した Apache Spark アプリケーションの詳細を表示するには、Apache Spark アプリケーションを選択し、詳細を表示します。

  ![完了したジョブを選択する](./media/how-to-monitor-spark-applications/select-completed-job.png)

1. **完了したタスク**、**状態**、および **合計実行時間** を確認します。

2. ジョブを **更新** します。

3. **[アプリケーションの比較]** をクリックして比較機能を使用します。この機能の詳細については、「[**Apache Spark アプリケーションの比較**](#compare-apache-spark-applications)」を参照してください。

4. **[Spark History Server]** をクリックして [History Server] ページを開きます。

5. **概要** 情報を確認します。

6. **[診断]** タブで診断を確認します。

7. **ログ** を確認します。 ドロップダウン リストで対応する項目を選ぶことにより、**Livy** と **Prelaunch** の完全なログ、**Driver** のログを見ることができます。 キーワードで検索して、必要なログの情報を直接取得することもできます。 **[Download log]\(ログのダウンロード\)** をクリックしてローカルにログの情報をダウンロードし、 **[Filter errors and warnings]\(エラーと警告を絞り込む\)** のチェック ボックスに印を入れて、エラーと警告を必要なものに絞り込んでください。

8. 生成されたジョブ グラフで、ジョブの概要を表示できます。 既定で、グラフにはすべてのジョブが表示されます。 **ジョブ ID** で、このビューをフィルター処理できます。

9. 既定で、 **[進行状況]** 表示が選択されています。 **[View]\(表示\)** ドロップダウン リストで **[Progress]\(進行状況\)** / **[Read]\(読み取り\)** / **[Written]\(書き込み\)** / **[Duration]\(経過時間\)** を選ぶことで、データ フローを確認できます。

10. ジョブを再生するには、 **[Playback]\(再生\)** ボタンをクリックします。 いつでも **[停止]** ボタンをクリックして停止できます。

11. スクロール バーでジョブのグラフを拡大/縮小します。 **[Zoom to Fit]\(画面に合わせて拡大/縮小する\)** で大きさを画面に合わせることもできます。

    [![完了したジョブを表示する](./media/how-to-monitor-spark-applications/view-completed-job.png)](./media/how-to-monitor-spark-applications/view-completed-job.png#lightbox)


12. ジョブ グラフ ノードには、各ステージの次の情報が表示されます。

    - [ジョブ ID]
    - タスク数
    - 経過時間
    - [行数]
    - データ読み取り: 入力サイズとシャッフル読み取りサイズの合計
    - 書き込みデータ: 出力サイズとシャッフル書き込みサイズの合計値
    - ステージ数

       ![ジョブ グラフ ノード](./media/how-to-monitor-spark-applications/job-graph-node.png)

13. マウスのポインターをジョブに重ねると、ジョブの詳細がヒントに表示されます。
    
    - ジョブの状態のアイコン: ジョブが順調であれば、緑色の √ が表示されます。ジョブに問題が見付かった場合は、黄色の ! が表示されます。
    - ジョブ ID。
    - [General]\(全般\) 欄
      - 進捗状況
      - 経過時間
      - [Total tasks number]\(合計タスク数\)
    - [Data]\(データ\) 欄
      - [Total rows number]\(合計列数\)
      - [Read size]\(読み取りサイズ\)
      - [Written size]\(書き込みサイズ\)
    - [Skew]\(偏り\) 欄
      - データ スキュー
      - 時間のずれ
    - ステージ数

      ![マウスのポインターをジョブに重ねます](./media/how-to-monitor-spark-applications/hover-a-job.png)

14. **ステージ数** をクリックして、そのジョブに含まれるすべてのステージを表示します。 ジョブ ID の隣の **[Collapse]\(隠す\)** をクリックして、そのジョブに含まれるすべてのステージを非表示にします。

15. ステージのグラフの **[View details]\(詳細の表示\)** をクリックすると、ステージの詳細が表示されます。

    [![すべてのステージを表示](./media/how-to-monitor-spark-applications/expand-all-the-stages.png)](./media/how-to-monitor-spark-applications/expand-all-the-stages.png#lightbox)
    
## <a name="monitor-running-apache-spark-application"></a>実行中の Apache Spark アプリケーションを監視する

**[モニター]** を開き、次に **[Apache Spark アプリケーション]** を選択します。 実行中の Apache Spark アプリケーションの詳細を表示するには、送信している Apache Spark アプリケーションを選択し、詳細を表示します。 Apache Spark アプリケーションがまだ実行中の場合は、進捗状況を監視できます。

   ![実行中のジョブを選択する](./media/how-to-monitor-spark-applications/select-running-job.png)

1. **完了したタスク**、**状態**、および **合計実行時間** を確認します。

2. Apache Spark アプリケーションを **[取り消し]** ます。

3. ジョブを **更新** します。

4. **[Spark UI]** ボタンをクリックして、Spark ジョブ ページにアクセスします。

5. **ジョブのグラフ**、**概要**、**分析**、**ログ** については、 生成されたジョブ グラフで、ジョブの概要を表示できます。 「[完了した Apache Spark アプリケーションを表示する](#view-completed-apache-spark-application)」セクションのステップ 5 - 15 をご覧ください。 

    [![実行中のジョブを表示する](./media/how-to-monitor-spark-applications/view-running-job.png)](./media/how-to-monitor-spark-applications/view-running-job.png#lightbox)

## <a name="view-canceled-apache-spark-application"></a>取り消された Apache Spark アプリケーションを表示する

**[モニター]** を開き、次に **[Apache Spark アプリケーション]** を選択します。 取り消された Apache Spark アプリケーションの詳細を表示するには、Apache Spark アプリケーションを選択し、詳細を表示します。

 ![取り消されたジョブを選択する](./media/how-to-monitor-spark-applications/select-cancelled-job.png) 

1. **完了したタスク**、**状態**、および **合計実行時間** を確認します。

2. ジョブを **更新** します。

3. **[アプリケーションの比較]** をクリックして比較機能を使用します。この機能の詳細については、「[**Apache Spark アプリケーションの比較**](#compare-apache-spark-applications)」を参照してください。

4. **[Spark History Server]** をクリックして、Apache Spark History Server のリンクを開きます。

5. グラフを表示します。 生成されたジョブ グラフで、ジョブの概要を表示できます。 「[完了した Apache Spark アプリケーションを表示する](#view-completed-apache-spark-application)」セクションのステップ 5 - 15 をご覧ください。

  [![取り消されたジョブを表示する](./media/how-to-monitor-spark-applications/view-cancelled-job.png)](./media/how-to-monitor-spark-applications/view-cancelled-job.png#lightbox)

## <a name="debug-failed-apache-spark-application"></a>失敗した Apache Spark アプリケーションをデバッグする

**[モニター]** を開き、次に **[Apache Spark アプリケーション]** を選択します。 失敗した Apache Spark アプリケーションの詳細を表示するには、Apache Spark アプリケーションを選択し、詳細を表示します。

![失敗したジョブを選択する](./media/how-to-monitor-spark-applications/select-failed-job.png)

1. **完了したタスク**、**状態**、および **合計実行時間** を確認します。

2. ジョブを **更新** します。

3. **[アプリケーションの比較]** をクリックして比較機能を使用します。この機能の詳細については、「[**Apache Spark アプリケーションの比較**](#compare-apache-spark-applications)」を参照してください。

4. **[Spark History Server]** をクリックして、Apache Spark History Server のリンクを開きます。

5. グラフを表示します。 生成されたジョブ グラフで、ジョブの概要を表示できます。 「[完了した Apache Spark アプリケーションを表示する](#view-completed-apache-spark-application)」セクションのステップ 5 - 15 をご覧ください。

   [![失敗したジョブ情報](./media/how-to-monitor-spark-applications/failed-job-info.png)](./media/how-to-monitor-spark-applications/failed-job-info.png#lightbox)


## <a name="view-input-dataoutput-data-for-apache-spark-application"></a>Apache Spark Application の入力データ/出力データを見る

Apache Spark アプリケーションを選択して **[Input data/Output data]\(入力データ/出力データ\) タブ** をクリックし、Apache Spark アプリケーションの入力と出力の日付を表示します。 この機能は Spark ジョブのデバッグに役立ちます。 データ ソースは gen1、gen2、blob の 3 種類をサポートしています。
    
**[Input data]\(出力データ\) タブ**
     
1. **[Copy input]\(入力のコピー)\** ボタンをクリックして、入力ファイルをローカルに貼り付けます。

2. **[CSV にエクスポート]** ボタンをクリックして、入力ファイルを CSV 形式でエクスポートします。

3. **検索ボックス** の入力キーワードでファイルを検索できます (キーワードには、ファイル名、読み取り形式、パスが含まれます)。

4. 入力ファイルを並べ替えるには、 **[名前]** 、 **[読み取り形式]** 、および **[パス]** をクリックします。

5. マウスのポインターを入力ファイルに重ねると、**ダウンロード、パスのコピー、その他** のアイコン ボタンが表示されます。

   ![[入力] タブ](./media/how-to-monitor-spark-applications/input-tab.png)

6. **[詳細]** ボタンをクリックすると、 **[パスのコピー]/[エクスプローラーで表示]/[プロパティ]** にコンテキスト メニューが表示されます。
      
    ![詳細の入力](./media/how-to-monitor-spark-applications/input-more.png)

   * パスのコピー: **[完全なパス]** と **[相対パス]** をコピーできます。
   * エクスプローラーに表示: リンクされたストレージ アカウントに移動できます ([データ] > [リンク])。
   * プロパティ: ファイルの基本的なプロパティ ([ファイル名]/[ファイルのパス]/[読み取り形式]/[サイズ]/[変更済み]) を表示します。

     ![プロパティの画像](./media/how-to-monitor-spark-applications/properties.png)

**[Output data]\(出力データ\) タブ**

   入力と同じ機能を持っています。

   ![出力-イメージ](./media/how-to-monitor-spark-applications/output.png)

## <a name="compare-apache-spark-applications"></a>Apache Spark アプリケーションの比較

アプリケーションを比較する 2 つの方法があります。 **[Compare Application]\(アプリケーションの比較\)** を選択して比較するか、 **[Compare in notebook]\(ノートブックで比較\)** ボタンをクリックしてノートブックで表示することができます。

### <a name="compare-by-choosing-an-application"></a>アプリを選んで比較する

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

**[Compare Application]\(アプリケーションの比較\)** ページで **[Compare in notebook]\(ノートブックで比較\)** ボタンをクリックしてノートブックを開きます。 *.ipynb* ファイルの既定の名前は **Recurrent Application Analytics** です。

![ノートブックで比較](./media/how-to-monitor-spark-applications/compare-in-notebook.png)

Notebook では、Spark プールと言語を設定した後に、Recurrent Application Analytics ファイルを直接実行できます。

![循環アプリケーション分析](./media/how-to-monitor-spark-applications/recurrent-application-analytics.png)

## <a name="next-steps"></a>次のステップ

パイプラインの実行の監視に関する詳細については、[Synapse Studio を使用したパイプラインの実行の監視](how-to-monitor-pipeline-runs.md)に関する記事を参照してください。
