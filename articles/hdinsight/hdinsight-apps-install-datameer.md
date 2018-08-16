---
title: 発行済みアプリケーションのインストール - Datameer - Azure HDInsight
description: Datameer サードパーティ Hadoop アプリケーションをインストールして使用します。
services: hdinsight
author: ashishthaps
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 7b75170349f41b0fa611d5e748353484c7fea2e0
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39598590"
---
# <a name="install-published-application---datameer"></a>発行済みアプリケーションのインストール - Datameer

この記事では、[Datameer](https://www.datameer.com/) の発行済み Hadoop アプリケーションを Azure HDInsight にインストールして実行する方法について説明します。 HDInsight アプリケーション プラットフォームの概要と、独立系ソフトウェア ベンダー (ISV) によって発行された、利用可能なアプリケーションの一覧については、[サードパーティ Hadoop アプリケーションのインストール](hdinsight-apps-install-applications.md)に関するページを参照してください。 独自のアプリケーションのインストール手順については、[カスタム HDInsight アプリケーションのインストール](hdinsight-apps-install-custom-applications.md)のページを参照してください。

## <a name="about-datameer"></a>Datameer について

Datameer は Hadoop プラットフォーム向けのネイティブ アプリケーションであり、Azure HDInsight の既存の機能を拡張すると共に、構造化データと非構造化データの迅速な統合、準備、分析を可能にします。 Datameer がアクセスできるソースと形式 (構造化、半構造化、非構造化) は 70 を超えています。 データを直接アップロードすることも、固有のデータ リンクを使用して必要に応じてデータをプルすることもできます。 Datameer のセルフサービス機能と使い慣れたスプレッドシート インターフェイスにより、ビッグ データ テクノロジの複雑さが低減され、洞察を得られるまでの時間が短くなります。 このスプレッドシート インターフェイスは、最適化された Hadoop ジョブに変換される宣言型の式を入力するための簡単なメカニズムを備えています。 Datameer に加えて、ビジネス インテリジェンス (BI) と Excel に関するスキルがあれば、Hadoop をクラウドですばやく使用できます。 詳細については、[Datameer のドキュメント](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft)を参照してください。

## <a name="prerequisites"></a>前提条件

新しい HDInsight クラスター (または既存のクラスター) にこのアプリをインストールするには、次の構成が必要です。

* クラスター レベル: Standard
* クラスターの種類: Hadoop
* クラスターのバージョン: 3.4

## <a name="install-the-datameer-published-application"></a>Datameer の発行済みアプリケーションのインストール

このアプリケーションと利用可能な他の ISV アプリケーションをインストールする詳細な手順については、[サードパーティ Hadoop アプリケーションのインストール](hdinsight-apps-install-applications.md)に関するページを参照してください。

## <a name="launch-datameer"></a>Datameer の起動

1. インストール後、Azure Portal でクラスターから Datameer を起動できます。**[設定]** ウィンドウに移動し、**[全般]** カテゴリにある **[アプリケーション]** をクリックします。 [インストール済みアプリ] ウィンドウには、インストール済みのアプリケーションが一覧表示されます。

    ![インストール済み Datameer アプリ](./media/hdinsight-apps-install-datameer/datameer-app.png)

2. Datameer を選択すると、Web ページへのリンクと SSH エンドポイント パスが表示されます。 [Web ページ] リンクを選択します。

3. 初回起動時には、14 日間無料試用版を使用するか、既存のライセンスをアクティブ化するという 2 つのライセンス オプションがあります。

    ![ライセンス オプション](./media/hdinsight-apps-install-datameer/license.png)

4. 選択したライセンス オプションの処理が終わると、ログイン フォームが表示されます。 前に表示された既定の資格情報をログイン フォームに入力します。 ログインしたら、ソフトウェア契約に同意して続行します。

    ![ログイン](./media/hdinsight-apps-install-datameer/login.png)

次の手順では、"Hello World" のデモを示します。

1. [サンプル CSV をダウンロードします](https://datameer.box.com/s/wzzw27za3agic4yjj8zrn6vfrph0ppnf)。

2. Datameer ダッシュボードの上部にある **+** 記号をクリックし、**[ファイルのアップロード]** をクリックします。

    ![ファイルのアップロード](./media/hdinsight-apps-install-datameer/upload.png)

3. アップロード ダイアログで、ダウンロードした **Hello World.csv** ファイルを参照して選択します。 **[ファイルの種類]** が [CSV / TSV] に設定されていることを確認します。 **[次へ]** を選択します。 ウィザードの最後に至るまで **[次へ]** をクリックし続けます。

    ![ファイルのアップロード](./media/hdinsight-apps-install-datameer/upload-browse.png)

4. 新しいフォルダーにあるファイルに **Hello World** という名前を付けます。 新しいフォルダーの名前を "Demo" に変更します。 **[保存]** を選択します。

    ![保存](./media/hdinsight-apps-install-datameer/save.png)

5. **+** 記号をもう一度クリックし、**[ブック]** を選択して、データ用の新しいブックを作成します。

    ![ブックの追加](./media/hdinsight-apps-install-datameer/add-workbook.png)

6. **Data** フォルダー、**FileUploads** の順に展開し、"Hello World" ファイルを保存したときに作成した **Demo** フォルダーを展開します。 ファイルの一覧から **[Hello World]** を選択し、**[データの追加]** をクリックします。

    ![保存](./media/hdinsight-apps-install-datameer/select-file.png)

7. スプレッドシート インターフェイスに読み込まれたデータが表示されます。 データのサブセットを選択するには、ツールバーの **[フィルター]** ボタンをクリックします。

    ![[フィルター] ボタン](./media/hdinsight-apps-install-datameer/filter-button.png)

8. [フィルターの適用] ダイアログで、**[市区町村]** 列、**[equals]\(等しい\)** 演算子の順に選択し、フィルター テキスト ボックスに「**Chicago**」と入力します。 **[Create filter in new sheet]\(新しいシートにフィルターを作成する\)** チェック ボックスをオンにし、**[フィルターの作成]** を選択します。

    ![フィルターの適用](./media/hdinsight-apps-install-datameer/apply-filter.png)

9. **[ファイル]**、**[保存]** の順にクリックしてブックを保存します。 "Hello World Workbook" などの名前を付けます。

10. ブックを実行する方法とタイミングに関するオプションが表示されます。 この時点では、すべてのオプションを既定値のままにし、**[Start calculation process immediately after save]\(保存後すぐに計算処理を開始する\)** をオンにして、**[保存]** を選択します。

    ![ブックの保存](./media/hdinsight-apps-install-datameer/save-workbook.png)

11. Datameer には、強力な視覚化ツールが用意されています。 データを表示するには、インフォグラフィックを作成します。 ダッシュボードの上部にある **+** 記号を選択し、**[インフォグラフィック]** を選択します。

    ![インフォグラフィックの追加](./media/hdinsight-apps-install-datameer/infographic-button.png)

12. 次の図の手順 1. に示すとおり、左側にあるウィジェットの一覧から棒グラフ ウィジェットをドラッグします。 次に、右側にあるデータ ブラウザーの Data フォルダーで、ブックを展開し、フィルターと共に追加したワークシートを展開します (手順 2.)。 棒グラフ上部に **[名前]** 列をドラッグし、**[ラベル]** ターゲットにドロップして、ブックの名前列をグラフのラベル フィールドとして設定します (手順 3.)。

    ![インフォグラフィック](./media/hdinsight-apps-install-datameer/infographic.png)

13. 年齢をグラフの Y 軸として設定するには、**[年齢]** 列をグラフの **[データ]** フィールドにドラッグします。

    ![インフォグラフィック](./media/hdinsight-apps-install-datameer/infographic-age.png)

お疲れさまでした。 コードを記述せずに、データを視覚化することができました。 次に、バリエーションと他の視覚化を調べることができます。

## <a name="next-steps"></a>次の手順

* [Datameer のドキュメント](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft)。
* [カスタム HDInsight アプリケーションをインストールする](hdinsight-apps-install-custom-applications.md): 未発行の HDInsight アプリケーションを HDInsight にデプロイする方法について確認します。
* [HDInsight アプリケーションを発行する](hdinsight-apps-publish-applications.md): カスタム HDInsight アプリケーションを Azure Marketplace に発行する方法について確認します。
* [MSDN: HDInsight アプリケーションをインストールする](https://msdn.microsoft.com/library/mt706515.aspx): HDInsight アプリケーションを定義する方法について確認します。
* [スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md): スクリプト アクションを使用してアプリケーションを追加インストールする方法を確認します。
* [HDInsight で空のエッジ ノードを使用する](hdinsight-apps-use-edge-node.md): HDInsight クラスターへのアクセスのほか、HDInsight アプリケーションのテストとホストに空のエッジ ノードを使用する方法を確認します。
