---
title: "Visual Studio の Azure Stream Analytics ツールの使用 | Microsoft Docs"
description: "Visual Studio の Azure Stream Analytics ツールに関する入門チュートリアル"
keywords: Visual Studio
documentationcenter: 
services: stream-analytics
author: su-jie
manager: jhubbard
editor: cgronlun
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: sujie
ms.openlocfilehash: 8e3f1ae6739896dfd1329561dbcede38a6069546
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2017
---
# <a name="use-azure-stream-analytics-tools-for-visual-studio"></a>Visual Studio の Azure Stream Analytics ツールの使用
Visual Studio の Azure Stream Analytics ツールが一般公開されます。 これらのツールを使用すると、Stream Analytics ユーザーのトラブルシューティング エクスペリエンスがさらに充実すると共に、複雑なクエリを記述したり、ローカルでクエリを記述することができます。 また、Stream Analytics ジョブを Visual Studio プロジェクトにエクスポートすることもできます。

## <a name="introduction"></a>はじめに
このチュートリアルでは、Visual Studio の Stream Analytics ツールを使用して、Stream Analytics ジョブの作成、ローカルでのテスト、管理、デバッグを行う方法について説明します。 

このチュートリアルを読むと、次のことができるようになります。

* Visual Studio の Stream Analytics ツールについて理解を深める。
* Stream Analytics ジョブを構成、デプロイする。
* ローカル サンプル データを使用してジョブをローカルでテストする。
* 監視機能を使用して問題をトラブルシューティングする。
* 既存のジョブをプロジェクトにエクスポートする。

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、次の前提条件を満たしておく必要があります。

* チュートリアル「[Stream Analytics を使って IoT ソリューションを構築する](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics)」の「Stream Analytics ジョブの作成」までの手順を完了します。 
* Visual Studio 2017、Visual Studio 2015、または Visual Studio 2013 Update 4 をインストールします。 Enterprise (Ultimate/Premium)、Professional、Community の各エディションがサポートされています。 Express エディションはサポートされていません。 
* [インストール手順](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install)に従って、Visual Studio の Stream Analytics ツールをインストールします。

## <a name="create-a-stream-analytics-project"></a>Stream Analytics プロジェクトを作成する
Visual Studio で、**[ファイル]** > **[新しいプロジェクト]** を選択します。 左側のテンプレートの一覧で **[Stream Analytics]** を選択し、**[Azure Stream Analytics アプリケーション]** を選択します。
他のプロジェクトの場合と同じように、ページの下部にプロジェクトの**名前**、**場所**、および**ソリューション名**を入力します。

![新しいプロジェクトの作成](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-01.png)

**ソリューション エクスプローラー**にプロジェクト **Toll** が生成されます。

![ソリューション エクスプローラーの Toll プロジェクト](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-02.png)

## <a name="choose-the-correct-subscription"></a>適切なサブスクリプションを選択する
1. Visual Studio の **[表示]** メニューで **[サーバー エクスプローラー]** を選択します。

2. Azure アカウントを使用してログインします。 

## <a name="define-input-sources"></a>入力ソースの定義
1. **ソリューション エクスプローラー**で **[入力]** ノードを展開し、**Input.json** の名前を **EntryStream.json** に変更します。 **[EntryStream.json]** をダブルクリックします。

2. **[入力のエイリアス]** に「**EntryStream**」と入力します。 入力のエイリアスはクエリ スクリプトで使用されていることに注意してください。

3. **[ソースの種類]** で、**[データ ストリーム]** を選択します。

4. **[ソース]** で **[イベント ハブ]** を選択します。

5. **[Service Bus 名前空間]** で、ドロップダウン リストの **[TollData]** オプションを選択します。

6. **[イベント ハブ名]** で **[entry]** を選択します。

7. **[イベント ハブ ポリシー名]** で、**[RootManageSharedAccessKey]** (既定値) を選択します。

8. **[イベントのシリアル化の形式]** で、**[Json]** を、**[エンコード]** で **[UTF8]** を選択します。
   
   設定は次のようになります。
   
   ![入力設定](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-01.png)
   
9. ページの下部の **[保存]** をクリックしてウィザードを終了します。 入力ソースをもう 1 つ追加して、終了ストリームを作成できます。 **[入力]** ノードを右クリックし、**[新しい項目]** を選択します。
   
   ![New Item](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-02.png)
   
10. ポップアップ ウィンドウで **[Stream Analytics Input]**(Azure Stream Analytics の入力) を選択し、**[名前]** を **ExitStream.json** に変更します。 **[追加]**を選択します。
   
    ![新しい項目の追加](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-03.png)
   
11. プロジェクトで **[ExitStream.json]** をダブルクリックし、入力ストリームと同じ手順に従って、フィールドに入力します。 次のスクリーンショットに示すように、**[イベント ハブ名]** には「**exit**」と入力してください。
   
    ![ExitStream 設定](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-04.png)
   
   以下のように、2 つの入力ストリームを定義しました。
   
   ![2 つの入力ストリーム](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-05.png)
   
   次に、車両登録データが含まれた BLOB ファイルの参照データ入力を追加します。
   
12. プロジェクトで **[入力]** ノードを右クリックし、ストリーム入力の場合と同じプロセスに従います。 **[ソースの種類]** で **[参照データ]** を選択し、**[入力のエイリアス]** に「**Registration**」と入力します。
   
    ![登録設定](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-06.png)
   
13. **TollData** を含むオプションが格納されている**ストレージ** アカウントを選択します。 コンテナー名は **TollData** で、**[パス パターン]** は **registration.json** です。 このファイル名は大文字と小文字が区別されるため、小文字で入力してください。

14. **[保存]** を選択してウィザードを終了します。

これで、すべての入力が定義されました。

## <a name="define-output"></a>出力の定義
1. **ソリューション エクスプローラー**で **[入力]** ノードを展開し、**[Output.json]** をダブルクリックします。

2. **[出力のエイリアス]** に「**output**」と入力します。 **[シンク]** で **[SQL Database]** を選択します。

3. **[データベース]**名に「**TollDataDB**」と入力します。

4. **[ユーザー名]**に「**tolladmin**」と入力します。 **[パスワード]** に「**123toll!**」と入力します。 **[テーブル]** に「**TollDataRefJoin**」と入力します。

5. [ **保存**] を選択します。

   ![出力設定](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-output-01.png)
 
## <a name="stream-analytics-query"></a>Stream Analytics クエリ
このチュートリアルでは、通行料金データに関するビジネス上の質問に答えます。 適切な答えを得るために、Stream Analytics で使用できるクエリを作成しました。 初めての Stream Analytics ジョブを開始する前に、簡単なシナリオとクエリ構文を確認しましょう。

### <a name="introduction-to-stream-analytics-query-language"></a>Stream Analytics クエリ言語の概要
料金所ブースに入る車両の台数をカウントする必要があるとします。 絶えず発生するイベントであるため、期間の定義が必要となります。 そこで質問を変更し、"3 分ごとに料金所ブースに入る車両の台数" にします。 この測定を一般にタンブリング カウントといいます。

この質問に答える Stream Analytics クエリを見てみましょう。

        SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count 
        FROM EntryStream TIMESTAMP BY EntryTime 
        GROUP BY TUMBLINGWINDOW(minute, 3), TollId 

ご覧のように、Stream Analytics は、SQL のようなクエリ言語を使用します。 これには、クエリの時間関連の側面を指定するいくつかの拡張機能が追加されています。

詳細については、クエリで使われる[時間管理](https://msdn.microsoft.com/library/azure/mt582045.aspx)と[時間枠](https://msdn.microsoft.com/library/azure/dn835019.aspx)コンストラクトについて MSDN でご覧ください。

最初の Stream Analytics クエリを作成したら、TollApp フォルダーの以下のパスに格納されているサンプル データ ファイルを使ってクエリをテストします。

**..\TollApp\TollApp\Data**

このフォルダーには次のファイルが格納されています。

* Entry.json
* Exit.json
* registration.json

## <a name="question-number-of-vehicles-entering-a-toll-booth"></a>テスト: 料金所ブースに入る車両の台数
プロジェクトで **[Script.asaql]** をダブルクリックして、エディターでスクリプトを開きます。 前のセクションのスクリプトをエディターに貼り付けます。 クエリ エディターでは IntelliSense、構文の色分け、およびエラーのマーカーがサポートされています。

![クエリ エディター](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="test-stream-analytics-queries-locally"></a>Stream Analytics クエリをローカルでテストする
まずクエリをコンパイルして、構文エラーがないかを確認します。

1. サンプル データに対してこのクエリを検証するには、[入力] を右クリックして、**[ローカル入力の追加]** を選択して、ローカル サンプル データを使用します。
   
   ![ローカル入力の追加](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-01.png)
   
2. ポップアップ ウィンドウで、ローカル パスからサンプル データを選択します。 [ **保存**] を選択します。
   
   ![ローカル入力の追加](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-02.png)
   
   **local_EntryStream.json** という名前のファイルが、入力フォルダーに自動的に追加されます。
   
   ![ローカル入力フォルダー ファイル リスト](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-03.png)
   
3. クエリ エディターで、**[ローカルで実行]** をクリックします。 または F5 キーを押します。
   
   ![[ローカルで実行]](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-01.png)
   
   コンソール出力から出力パスを見つけることができます。 任意のキーを押して、結果フォルダーを開きます。
   
   ![ローカルの実行](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-02.png)
   
4. ローカル フォルダー内の結果を確認します。
   
   ![ローカル フォルダーの結果](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-03.png)
   
   
### <a name="sample-input"></a>サンプル入力
入力ソースからの入力データをローカル ファイルにサンプリングすることもできます。 入力構成ファイルを右クリックし、**[サンプル データ]** を選択します。 

![サンプル データ](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-01.png)

現時点では、サンプリングできるのはイベント ハブまたは IoT Hub のみであることに注意してください。 他の入力ソースはサポートされていません。 ポップアップ ダイアログ ボックスで、サンプル データを保存するローカル パスを入力します。 **[サンプル]** を選択します。

![サンプル データ構成](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-02.png)
 
**[出力]** ウィンドウで進行状況を確認できます。 

![サンプル データ出力](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-03.png)
 
### <a name="submit-a-stream-analytics-query-to-azure"></a>Stream Analytics クエリを Azure に送信する
1. **クエリ エディター**で、スクリプト エディターの **[Azure に送信]** を選択します。

   ![[Azure に送信]](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-01.png)
 
2. **[新しい Azure Stream Analytics ジョブを作成する]** を選択します。 **[ジョブ名]** に「**TollApp**」と入力します。 ドロップダウン リストから正しい**サブスクリプション**を選択します。 **[Submit]\(送信\)** をクリックします。

   ![ジョブの送信](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-02.png)

 
### <a name="start-the-job"></a>ジョブの開始
ジョブが作成されたら、ジョブ ビューが自動的に開きます。 
1. 緑色の矢印ボタンを選択して、ジョブを開始します。

   ![[ジョブの開始] ボタン](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-01.png)
 
2. 既定の設定を選択して **[開始]** を選択します。
 
   ![ジョブの開始](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-02.png)

   ジョブの状態が **[実行中]** に変更されて、入力/出力イベントが表示されます。

   ![ジョブの概要とメトリック](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-03.png)

## <a name="check-results-in-visual-studio"></a>Visual Studio での結果の確認
1. Visual Studio のサーバー エクスプローラーを開いて **TollDataRefJoin** テーブルを右クリックします。

2. **[テーブル データの表示]** を選択して、ジョブの出力を確認します。
   
   ![テーブル データの表示](media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-check-results.jpg)
   

### <a name="view-job-metrics"></a>ジョブのメトリックスの表示
一部の基本的なジョブの統計情報は、**ジョブ メトリック**に表示されます。 

![ジョブ メトリック](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-metrics-01.png)

 
## <a name="list-the-job-in-server-explorer"></a>サーバー エクスプローラーでジョブを表示する
**サーバー エクスプローラー**で **[Stream Analytics ジョブ]** を選択し、**[更新]** を選択します。 **[Stream Analytics ジョブ]** の下にジョブが表示されます。

![ジョブの一覧](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-list-jobs-01.png)


## <a name="open-the-job-view"></a>ジョブ ビューを開く
ジョブ ノードを展開し、**[ジョブ ビュー]** ノードをダブルクリックしてジョブ ビューを開きます。

![ジョブ ビュー](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-view-01.png)


## <a name="export-an-existing-job-to-a-project"></a>既存のジョブをプロジェクトにエクスポートする
既存のジョブをプロジェクトにエクスポートする方法は 2 つあります。
* **サーバー エクスプローラー**の **[Stream Analytics ジョブ]** ノードでジョブ ノードを右クリックします。 **[新しい Stream Analytics プロジェクトにエクスポート]** を選択します。
   
   ![[新しい Stream Analytics プロジェクトにエクスポート]](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-01.png)
   
   生成されたプロジェクトが、**ソリューション エクスプローラー**に表示されます。
   
    ![ソリューション エクスプローラー ジョブ](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-02.png)
   
* ジョブ ビューで、**[プロジェクトの生成]** を選択します。
   
   ![[プロジェクトの生成]](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-03.png)
   
## <a name="known-issues-and-limitations"></a>既知の問題と制限
 
* クエリに地理空間関数が含まれる場合、ローカル テストは機能しません。
* JavaScript UDF の追加や変更はエディターでサポートされていません。
* ローカル テストでは、JSON 形式での出力の保存はサポートされていません。 
* Power BI の出力と ADLS の出力はサポートされていません。



## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-get-started.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics クエリ言語リファレンス](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics の管理 REST API リファレンス](https://msdn.microsoft.com/library/azure/dn835031.aspx)


