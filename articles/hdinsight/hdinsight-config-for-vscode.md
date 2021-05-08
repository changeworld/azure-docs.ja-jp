---
title: Azure HDInsight の構成設定のリファレンス
description: Azure HDInsight 拡張機能の構成を紹介します。
ms.service: hdinsight
ms.topic: how-to
ms.date: 04/07/2021
ms.custom: devx-track-python
ms.openlocfilehash: a9a444c2557ea17114123cbd5084cbbd9fe6dac6
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259933"
---
# <a name="azure-hdinsight-configuration-settings-reference"></a>Azure HDInsight の構成設定のリファレンス

Visual Studio Code 用の Spark & Hive ツールの拡張機能は、高度な構成が可能です。 このページでは、使用できるキー設定について説明します。  

Visual Studio Code の設定を扱う際の一般的な情報については、[ユーザーとワークスペースの設定](https://code.visualstudio.com/docs/getstarted/settings)に関するページを参照してください。定義済みの変数のサポートについては、[変数リファレンス](https://code.visualstudio.com/docs/editor/variables-reference)を参照してください。

## <a name="open-the-azure-hdinsight-configuration"></a>Azure HDInsight の構成を開く

1. まずフォルダーを開き、ワークスペース設定を作成します。
2. **Ctrl + Shift + P** を押すか、 **[表示]**  ->  **[コマンド パレット]** に移動してすべてのコマンドを表示します。
3. **構成の設定** を検索します。
4. 左側のディレクトリで **[拡張機能]** を展開し、 **[HDInsight 構成]** を選択します。

 ![HDI 構成の画像](./media/HDInsight-config-for-vscode/HDInsight-config-for-vscode.png)

## <a name="general-settings"></a>全般設定   

|  プロパティ   | Default | 説明   |
| ----- | ----- |----- |
| HDInsight: Azure Environment | Azure | Azure 環境 |
| HDInsight: Disable Open Survey Link | オン | HDInsight アンケートのオープンを有効/無効にします |
| HDInsight: Enable Skip Pyspark Installation | オフ | pyspark インストールのスキップを有効/無効にします |
| HDInsight: Login Tips Enable | オフ | このオプションをオンにすると、Azure へのログイン時にプロンプトが表示されます |
| HDInsight: Previous Extension Version | 現在の拡張機能のバージョン番号を表示します | 以前の拡張機能のバージョンを表示します|
| HDInsight: Results Font Family | -apple-system,BlinkMacSystemFont,Segoe WPC,Segoe UI,HelveticaNeue-Light,Ubuntu,Droid Sans,sans-serif | 結果グリッドのフォント ファミリを設定します。エディターのフォントを使用するには、空白に設定します |
| HDInsight: Results Font Size | 13 |結果グリッドのフォント サイズを設定します。エディターのサイズを使用するには、空白に設定します |
| HDInsight Cluster: Linked Cluster | -- | リンクされたクラスターの URL。 設定する JSON ファイルを編集することもできます |
| HDInsight Hive: Apply Localization | オフ | [省略可能] VSCode の構成済みロケールにローカライズするための構成オプション (設定を有効にするには VSCode を再起動する必要があります)|
| HDInsight Hive: Copy Include Headers | オフ | [省略可能] 結果を結果ビューからコピーするための構成オプション |
| HDInsight Hive: Copy Remove New Line | オン | [省略可能] 複数行の結果を結果ビューからコピーするための構成オプション |
| HDInsight Hive › Format: Align Column Definitions In Columns | オフ | 列定義を揃えるかどうかを指定します |
| HDInsight Hive › Format: Datatype Casing | なし | データ型を大文字、小文字、または "なし" (元のまま) のいずれにフォーマットするかを指定します |
| HDInsight Hive › Format: Keyword Casing | なし | キーワードを大文字、小文字、または "なし" (元のまま) のいずれにフォーマットするかを指定します |
| HDInsight Hive › Format: Place Commas Before Next Statement | オフ | コンマを、'mycolumn1,' のようにリスト内の各ステートメントの末尾に配置する代わりに ',mycolumn2' のように先頭に配置するかどうかを指定します|
| HDInsight Hive › Format: Place Select Statement References On New Line | オフ | Select ステートメント内のオブジェクトへの参照を別々の行に分割するかどうかを指定します。 たとえば、'SELECT C1, C2 FROM T1' の場合に C1 と C2 を別々の行にするかどうかなどです
| HDInsight Hive: Log Debug Info | オフ | [省略可能] デバッグ出力を VS Code コンソールにログとして表示します (ヘルプ -> 開発者ツールの切り替え) 
| HDInsight Hive: Messages Default Open | オン | 既定で開いているメッセージ ウィンドウの場合は true。閉じている場合は false|
| HDInsight Hive: Results Font Family | -apple-system,BlinkMacSystemFont,Segoe WPC,Segoe UI,HelveticaNeue-Light,Ubuntu,Droid Sans,sans-serif | 結果グリッドのフォント ファミリを設定します。エディターのフォントを使用するには、空白に設定します |
| HDInsight Hive: Results Font Size | 13 | 結果グリッドのフォント サイズを設定します。エディターのサイズを使用するには、空白に設定します |
| HDInsight Hive › Save As Csv: Include Headers | オン | [省略可能] true の場合、CSV として結果を保存する際に列ヘッダーが組み込まれます |
| HDInsight Hive: Shortcuts | -- | 結果ウィンドウに関連するショートカット |
| HDInsight Hive: Show Batch Time| オフ | [省略可能] 各バッチの実行時間を表示するかどうかを指定します |
| HDInsight Hive: Split Pane Selection | [次へ] | [省略可能] どの列を新しい結果ウィンドウで開く必要があるかについての設定オプション |
| HDInsight Job Submission: Cluster Conf | -- | クラスタ構成 |
| HDInsight Job Submission: Livy Conf | -- | Livy の構成。 POST/batches |
| HDInsight Jupyter: Append Results| オン | 結果を結果ウィンドウに追加するかどうかを指定します。指定しない場合はクリアして表示します。 |
| HDInsight Jupyter: Languages | -- | 言語ごとの既定の設定。 |
| HDInsight Jupyter › Log: Verbose | オフ | 詳細ログを有効にするかどうかを指定します |
| HDInsight Jupyter › Notebook: Startup Args | 項目を追加可能 | 'jupyter notebook' コマンドライン引数。 各引数は、配列内の個別の項目です。 完全な一覧を表示するには、ターミナル ウィンドウで「jupyter notebook--help」と入力します。 |
| HDInsight Jupyter › Notebook: Startup Folder | ${workspaceRoot} |-- |
| HDInsight Jupyter: Python Extension Enabled | オン | pySpark Interactive ジョブを送信する場合は、Python-Interactive-Window of ms-python 拡張機能を使用します。 それ以外の場合は、独自の jupyter ウィンドウを使用します |
| HDInsight Spark.NET: 7z | C:\Program Files\7-Zip | <7z.exe へのパス> |
| HDInsight Spark.NET: HADOOP_HOME | D:\winutils | <bin\winutils.exe へのパス> Windows OS のみ |
| HDInsight Spark.NET: JAVA_HOME | C:\Program Files\Java\jdk1.8.0_201\ | Java Home へのパス|
| HDInsight Spark.NET: SCALA_HOME | C:\Program Files (x86)\scala\ | Scala Home へのパス |
| HDInsight Spark.NET: SPARK_HOME | D:\spark-2.3.3-bin-hadoop2.7\ | Spark Home へのパス |
| Hive: Persist Query Result Tabs | オフ | Hive PersistQueryResultTabs |
| Hive: Split Pane Selection | [次へ] | [省略可能] どの列を新しい結果ウィンドウで開く必要があるかについての設定オプション |
| Hive Interactive: Copy Executable Folder | オフ | Hive インタラクティブ サービス ランタイム フォルダーをユーザーの一時フォルダーにコピーするかどうかを指定します |
| Hql Interactive Server: Wrapper Port | 13424 | Hive インタラクティブ サービス ポート |
| Hql Language Server: Language Wrapper Port | 12342 | リッスンする Hive 言語サービス ポート サーバー。 |
| Hql Language Server: Max Number Of Problems | 100 | サーバーによって生成される問題の最大数を制御します。 |
| Synapse Spark Compute: Synapse Spark Compute Azure Environment | blank | synapse Spark Compute Azure 環境 |
| Synapse Spark pool Job Submission: Livy Conf | -- | Livy の構成。 POST/batches
| Synapse Spark pool Job Submission: Synapse Spark Pool Cluster Conf | -- | Synapse Spark プール構成 |


## <a name="next-steps"></a>次のステップ

- Azure HDInsight for VSCode の詳細については、[Spark & Hive for Visual Studio Code ツール](https://docs.microsoft.com/sql/big-data-cluster/spark-hive-tools-vscode)に関する記事を参照してください。
- Spark & Hive for Visual Studio Code の使用については、[Spark & Hive for Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706) のデモ ビデオをご覧ください。