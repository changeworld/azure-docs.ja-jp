---
title: 拡張された Spark History Server を使用して Spark アプリケーションのデバッグと診断を行う - Azure HDInsight
description: 拡張された Spark History Server を使用して Spark アプリケーションのデバッグと診断を行う - Azure HDInsight
services: hdinsight
ms.service: hdinsight
author: jejiang
ms.author: jejiang
editor: jasonwhowell
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2018
ms.openlocfilehash: df196286297c7f8961e58d2196554b00d0281c18
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622339"
---
# <a name="use-extended-spark-history-server-to-debug-and-diagnose-spark-applications"></a>拡張された Spark History Server を使用して Spark アプリケーションのデバッグと診断を行う

この記事では、拡張された Spark History Server を使用して、完成して実行されている Spark アプリケーションのデバッグおよび診断を行う方法に関するガイダンスを提供します。 この拡張には、現時点では、[データ] タブと [グラフ] タブが含まれています。[データ] タブでは、ユーザーは、Spark ジョブの入力データと出力データをチェックできます。 [グラフ] タブでは、ユーザーは、データ フローをチェックし、ジョブ グラフを再生できます。

## <a name="open-the-spark-history-server"></a>Spark History Server を開く

Spark History Server は、完了および実行中の Spark アプリケーションの Web UI です。 

### <a name="to-open-the-spark-history-server-web-ui-from-azure-portal"></a>Spark History Server Web UI を Azure Portal から開くには

1. [Azure Portal](https://portal.azure.com/) で Spark クラスターを開きます。 詳細については、「[クラスターの一覧と表示](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters)」を参照してください。
2. **クイック リンク**の **[クラスター ダッシュボード]** をクリックし、**[Spark History Server]** をクリックします。 入力を求められたら、Spark クラスターの管理者資格情報を入力します。 

    ![Spark History Server](./media/apache-azure-spark-history-server/launch-history-server.png "Spark History Server")

### <a name="to-open-the-spark-history-server-web-ui-by-url"></a>Spark History Server Web UI を URL で開くには
次の URL を参照することで、Spark History Server を開きます。<ClusterName> を、お客様の Spark クラスター名に置き換えてください。

   ```
   https://<ClusterName>.azurehdinsight.net/sparkhistory
   ```

Spark History Server Web UI は次のようになります。

![HDInsight Spark History Server](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)


## <a name="open-the-data-tab-from-spark-history-server"></a>Spark History Server から [データ] タブを開く
[データ] ビューを取得するには、ジョブ ID を選択し、[ツール] メニューの **[データ]** をクリックします。

+ **[入力]**、**[出力]**、および **[テーブル操作]** の各タブを個別に選択することで、その内容をチェックできます。

    ![[データ] タブ](./media/apache-azure-spark-history-server/sparkui-data-tabs.png)

+ **[コピー]** ボタンをクリックすることで、すべての行をコピーできます。

    ![データのコピー](./media/apache-azure-spark-history-server/sparkui-data-copy.png)

+ **[csv]** ボタンをクリックすることで、すべてのデータを CSV ファイルとして保存できます。

    ![データの保存](./media/apache-azure-spark-history-server/sparkui-data-save.png)

+ **[検索]** フィールドにキーワードを入力することで検索できます。検索結果はすぐに表示されます。

    ![データの検索](./media/apache-azure-spark-history-server/sparkui-data-search.png)

+ テーブルを並べ替えるには、列ヘッダーをクリックします。行を展開して行の詳細を表示するには、プラス記号をクリックします。行を折りたたむには、マイナス記号をクリックします。

    ![データ テーブル](./media/apache-azure-spark-history-server/sparkui-data-table.png)

+ 右側に配置されている **[一部ダウンロード]** ボタンをクリックすることで、1 つのファイルをダウンロードできます。選択したファイルはローカルにダウンロードされます。ファイルがもう存在していない場合は、新しいタブが開いてエラー メッセージが表示されます。

    ![データのダウンロード行](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ [ダウンロード] メニューを展開して、**[完全パスのコピー]** または **[相対パスのコピー]** を選択することで、完全パスまたは相対パスをコピーできます。 Azure Data Lake Storage ファイルの場合は、**[Azure Storage Explorer で開く]** を選択すると、Azure Storage Explorer が開き、サインイン時のフォルダーにアクセスします。

    ![データのコピー パス](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ 1 ページに表示できる行数を超える行がある場合は、テーブルの下に表示されている数値をクリックすると、該当するページに移動できます。 

    ![[データ] ページ](./media/apache-azure-spark-history-server/sparkui-data-page.png)

+ [データ] の横の疑問符をポイントすると、ツールチップが表示されます。疑問符をクリックすると、詳細を取得できます。

    ![データの詳細](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+ **[フィードバックをお寄せください]** をクリックすることで、問題のフィードバックを送信できます。

    ![グラフのフィードバック](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="open-the-graph-tab-from-spark-history-server"></a>Spark History Server から [グラフ] タブを開く
ジョブの [グラフ] ビューを取得するには、ジョブ ID を選択し、[ツール] メニューの **[グラフ]** をクリックします。

+ 生成されたジョブ グラフで、ジョブの概要をチェックします。 

+ 既定では、すべてのジョブが表示され、**[ジョブ ID]** によってフィルター処理できます。

    ![グラフのジョブ ID](./media/apache-azure-spark-history-server/sparkui-graph-jobid.png)

+ 既定では、**[進行状況]** が選択され、ユーザーは、**[表示]** ドロップダウン リストの **[読み取り済み] または [書き込み済み]** を選択することで、データ フローをチェックできます。

    ![グラフの表示](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

    グラフ ノードは、ヒートマップを示す色を使用して表示されます。

    ![グラフのヒートマップ](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)

+ **[再生]** ボタンをクリックすることで、ジョブを再生できます。[停止] ボタンをクリックすることで、いつでも停止できます。 タスクは、再生時の状態を示す色を使用して表示されます。

    + 緑 (成功) : ジョブは正常に完了しています。
    + オレンジ (再試行) : 失敗はしたが、ジョブの最終結果には影響しないタスクのインスタンス。 これらのタスクには、重複しているものや、後で成功した可能性がある再試行インスタンスがあります。
    + 赤 (失敗) : タスクは失敗しています。
    + 青 (実行中) : タスクは実行されています。
    + 白 (スキップまたは待機中) : タスクが実行されるために待機しているか、ステージがスキップされています。

    ![グラフの色のサンプル (実行中)](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)
 
    ![グラフの色のサンプル (失敗)](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)
 
    > [!NOTE]
    > 各ジョブの再生が許可されます。 ステージがないジョブ、または完了していないジョブの再生はサポートされません。


+ マウスをスクロールすると、ジョブ グラフが拡大/縮小されます。画面に合うように拡大/縮小するには、**[画面に合わせてサイズを変更]** をクリックします。
 
    ![グラフを画面に合わせてサイズを変更](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ 失敗したタスクがあるときにグラフ ノードをポイントすると、ツールヒントが表示され、ステージをクリックすると、[ステージ] ページが開きます。

    ![グラフのツールヒント](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ ジョブ グラフ ノードには、各ステージの次の情報が表示されます。
    + ID。
    + 名前または説明。
    + タスクの総数。
    + データの読み取り: 入力サイズとシャッフル読み取りサイズの合計。
    + データの書き込み: 出力サイズとシャッフル書き込みサイズの合計。
    + 実行時間: 最初の試行の開始時刻から最後の試行の完了時刻までの時間。
    + 行数: 入力レコード、出力レコード、シャッフル読み取りレコード、およびシャッフル書き込みレコードの合計。
    + 進行状況。

    > [!NOTE]
    > 既定では、ジョブ グラフ ノードには、各ステージの最後の試行に関する情報 (ステージの実行時間以外) が表示されますが、再生中は各試行の情報が表示されます。

    > [!NOTE]
    > 読み取りと書き込みのデータ サイズでは、1MB = 1000 KB = 1000 * 1000 バイトが使用されています。

+ **[フィードバックをお寄せください]** をクリックすることで、問題のフィードバックを送信できます。

    ![グラフのフィードバック](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="faq"></a>FAQ

### <a name="1-revert-to-community-version"></a>1.コミュニティ バージョンに戻す

コミュニティ バージョンに戻すには、次の手順を実行します。

1. Ambari でクラスターを開きます。 左側のパネルで **[Spark2]** をクリックします。
2. **[Configs]\(構成\)** タブを選択します。
3. **Custom spark2-defaults**グループを展開します。
4. **[Add Property]\(プロパティの追加\)** をクリックし、**spark.ui.enhancement.enabled=false** を追加して保存します。
5. これで、プロパティが **false** に設定されます。
6. **[保存]** をクリックして構成を保存します。

    ![機能がオフになる](./media/apache-azure-spark-history-server/sparkui-turn-off.png)

7. 左側のパネルで **[Spark2]** をクリックし、**[Summary]\(概要\)** タブで **Spark2 History Server** をクリックします。

    ![server1 の再起動](./media/apache-azure-spark-history-server/sparkui-restart-1.png) 

8. **Spark2 History Server** の **[Restart]\(再起動\)** をクリックして、History Server を再起動します。

    ![server2 の再起動](./media/apache-azure-spark-history-server/sparkui-restart-2.png)  

9. Spark History Server Web UI 更新すると、コミュニティ バージョンに戻ります。

### <a name="2-upload-history-server-event"></a>2.History Server イベントをアップロードする

History Server エラーが発生した場合は、次の手順に従ってイベントを提供します。
1. History Server Web UI で **[ダウンロード]** をクリックすることで、イベントをダウンロードします。

    ![イベントのダウンロード](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. [データ] または [グラフ] タブで **[フィードバックをお寄せください]** をクリックします。

    ![グラフのフィードバック](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. エラーのタイトルと説明を入力し、zip ファイルを編集フィールドにドラッグした後、**[Submit new issue]\(新しい問題を投稿\)** をクリックします。

    ![ファイルの問題](./media/apache-azure-spark-history-server/sparkui-file-issue.png)


### <a name="3-upgrade-jar-file-for-hotfix-scenario"></a>手順 3.修正プログラム用の jar ファイルをアップグレードするシナリオ

修正プログラムを使用してアップグレードする場合は、spark enhancement.jar* をアップグレードする次のスクリプトを使用します。

**upgrade_spark_enhancement.sh**:

   ```bash
    #!/usr/bin/env bash

    # Copyright (C) Microsoft Corporation. All rights reserved.

    # Arguments:
    # $1 Enhancement jar path

    if [ "$#" -ne 1 ]; then
        >&2 echo "Please provide the upgrade jar path."
        exit 1
    fi

    install_jar() {
        tmp_jar_path="/tmp/spark-enhancement-hotfix-$( date +%s )"

        if wget -O "$tmp_jar_path" "$2"; then
            for FILE in "$1"/spark-enhancement*.jar
            do
                back_up_path="$FILE.original.$( date +%s )"
                echo "Back up $FILE to $back_up_path"
                mv "$FILE" "$back_up_path"
                echo "Copy the hotfix jar file from $tmp_jar_path   to $FILE"
                cp "$tmp_jar_path" "$FILE"

                "Hotfix done."
                break
            done
        else    
            >&2 echo "Download jar file failed."
            exit 1
        fi
    }

    jars_folder="/usr/hdp/current/spark2-client/jars"
    jar_path=$1

    if ls ${jars_folder}/spark-enhancement*.jar 1>/dev/null 2>&1;   then
        install_jar "$jars_folder" "$jar_path"
    else
        >&2 echo "There is no target jar on this node. Exit with no action."
        exit 0
    fi
   ```

**[使用状況]**: 

`upgrade_spark_enhancement.sh https://${jar_path}`

**例**:

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.tgz` 

**bash ファイルを Azure Portal から使用するには**

1. [Azure Portal](https://ms.portal.azure.com) を起動し、クラスターを選択します。
2. **[スクリプト アクション]**、**[新規で送信]** の順にクリックします。 **[スクリプト操作を追加]** フォームを完成させた後、**[作成]** ボタンをクリックします。
    
    + **スクリプトの種類**: **[カスタム]** を選択します。
    + **名前**: スクリプト名を指定します。
    + **Bash スクリプト URI**: bash ファイルをプライベート クラスターにアップロードした後、ここに URL をコピーします。 または、提供された URI を使用します。
    
   ```upgrade_spark_enhancement
    https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh
   ```

    + **[ヘッド]** と **[ワーカー]** をオンにします。
    + **パラメーター**: bash の使用法に従ってパラメーターを設定します。

    ![ログまたは修正プログラムのアップグレード](./media/apache-azure-spark-history-server/sparkui-upload2.png)


## <a name="known-issue"></a>既知の問題

1.  現時点では、Spark 2.3 クラスターのみで機能します。

2.  RDD を使用している入力/出力データは、[データ] タブには表示されません。

## <a name="next-steps"></a>次の手順

* [HDInsight での Spark クラスターのリソースの管理](apache-spark-resource-manager.md)
* [Spark の設定を構成する](apache-spark-settings.md)


## <a name="contact-us"></a>お問い合わせ

フィードバックがある場合やこのツールの使用中に問題が発生した場合は、[hdivstool@microsoft.com](mailto:hdivstool@microsoft.com) に電子メールをお送りください。
