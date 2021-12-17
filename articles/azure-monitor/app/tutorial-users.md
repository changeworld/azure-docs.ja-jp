---
title: Application Insights で顧客を理解する | Microsoft Docs
description: Application Insights を使用して、アプリケーションを顧客がどのように使用しているかを理解するためのチュートリアルです。
ms.topic: tutorial
ms.date: 07/30/2021
ms.custom: mvc
ms.openlocfilehash: f06522ffe257bc7e20fe587b7c0a4479f6677240
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129626"
---
# <a name="use-azure-application-insights-to-understand-how-customers-are-using-your-application"></a>Azure Application Insights を使用して顧客がアプリケーションをどのように使用しているかを理解する

 Application Insights では、ユーザーがアプリケーションとどのようなやり取りをしているかを理解するために役立つ使用状況情報が収集されます。  このチュートリアルでは、この情報を分析するために使用できるさまざまなリソースについて説明します。  学習内容は次のとおりです。

> [!div class="checklist"]
> * アプリケーションにアクセスしているユーザーの詳細を分析する
> * セッション情報を使用して、顧客がアプリケーションをどのように使用しているかを分析する
> * 望ましいユーザー アクティビティと実際のユーザー アクティビティを比較できるファネルを定義する 
> * グラフとクエリを 1 つのドキュメントに統合するブックを作成する
> * 類似するユーザーをグループ化してまとめて分析する
> * どのユーザーがアプリケーションに再来しているを知る
> * ユーザーがアプリケーション内をどのように移動しているかを調べる


## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

- 次のワークロードを使って、[Visual Studio 2019](https://www.visualstudio.com/downloads/) をインストールします。
    - ASP.NET および Web の開発
    - Azure の開発
- [Visual Studio Snapshot Debugger](https://aka.ms/snapshotdebugger) をダウンロードしてインストールします。
- .NET アプリケーションを Azure にデプロイし、[Application Insights SDK の有効化](../app/asp-net.md)を実行します。 
- [アプリケーションからのテレメトリの送信](../app/usage-overview.md#send-telemetry-from-your-app)を実行して、カスタム イベント/ページ ビューに追加します。
- [ユーザー コンテキスト](./usage-overview.md)を送信して、一定期間にユーザーが何をしているかを追跡し、使用状況機能をフルに活用します。

## <a name="log-in-to-azure"></a>Azure にログインする
Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にログインします。

## <a name="get-information-about-your-users"></a>ユーザーに関する情報を取得する
**[ユーザー]** パネルでは、ユーザーに関する重要な詳細をさまざまな方法で理解することができます。 このパネルを使用して、ユーザーがどこから接続しているか、ユーザーのクライアントの詳細、ユーザーがアプリケーションのどの領域にアクセスしているかといった情報を理解できます。 

1. *[使用法]* の下の Application Insights リソースで、メニューの **[ユーザー]** を選択します。
2. 既定のビューには、過去 24 時間でアプリケーションに接続していた一意のユーザーの数が表示されます。  この時間枠は変更でき、この情報をフィルター処理するためのさまざまな条件を設定できます。

3. **[期間]** ドロップダウンをクリックし、時間枠を 7 日間に変更します。  これにより、パネルのさまざまなグラフに含まれるデータが増加します。

4. **[Split by]\(分割単位\)** ドロップダウンをクリックして、ユーザーのプロパティ別の内訳をグラフに追加します。  **[国または地域]** を選択します。  グラフには同じデータが含まれていますが、それぞれの国/地域のユーザー数の内訳を確認できます。

      :::image type="content" source="./media/tutorial-users/user-1.png" alt-text="[ユーザー] タブのクエリ ビルダーのスクリーンショット。" lightbox="./media/tutorial-users/user-1.png":::

5. グラフの縦棒にカーソルを置くと、それぞれの国/地域のユーザー数は、その縦棒によって表われている時間枠のみを反映していることがわかります。
6. 詳細を表示するには、 **[他の分析情報を表示]** を選択します。 

      :::image type="content" source="./media/tutorial-users/user-2.png" alt-text="[他の分析情報を表示] の [ユーザー] タブのスクリーンショット。" lightbox="./media/tutorial-users/user-2.png":::


## <a name="analyze-user-sessions"></a>ユーザー セッションを分析する
**[セッション]** パネルは **[ユーザー]** パネルに似ています。  **[ユーザー]** はアプリケーションにアクセスしているユーザーの詳細を理解するのに役立ちますが、 **[セッション]** は、それらのユーザーがアプリケーションをどのように使用したかを理解するのに役立ちます。  

1. ユーザーの *[使用法]* で **[セッション]** を選択します。
2. グラフを見ると、 **[ユーザー]** パネルと同じオプションでデータのフィルター処理と分割が行われていることがわかります。

     :::image type="content" source="./media/tutorial-users/sessions.png" alt-text="棒グラフが表示されている [セッション] タブのスクリーンショット。" lightbox="./media/tutorial-users/sessions.png":::

4. セッション タイムラインを表示するには、 **[他の分析情報を表示]** を選択し、アクティブなセッションの下のいずれかのタイムラインで **[セッション タイムラインの表示]** を選択します。 セッション タイムラインには、そのセッションのすべてのアクションが表示されます。 これにより、例外の数が多いセッションなどの情報を識別できます。

     :::image type="content" source="./media/tutorial-users/timeline.png" alt-text="タイムラインが選択されている [セッション] タブのスクリーンショット。" lightbox="./media/tutorial-users/timeline.png":::

## <a name="group-together-similar-users"></a>類似するユーザーをグループ化する
**[コーホート]** は、類似する特性でグループ化されたユーザーのセットです。  コーホートを使用して、他のパネルのデータをフィルター処理することで、特定のユーザー グループを分析できます。  たとえば、購入を完了したユーザーのみを分析できます。

1.  ([ユーザー]、[セッション]、[イベント] などのいずれかの) [使用法] タブの上部にある **[コーホートの作成]** を選択します。

1.  ギャラリーからテンプレートを選択します。

    :::image type="content" source="./media/tutorial-users/cohort.png" alt-text="コーホートのテンプレート ギャラリーのスクリーンショット。" lightbox="./media/tutorial-users/cohort.png":::
1.  コーホートを編集し、 **[保存]** を選択します。
1.  **[表示]** ドロップダウン メニューからコーホートを選択して表示します。 

    :::image type="content" source="./media/tutorial-users/cohort-2.png" alt-text="コーホートを示している [表示] ドロップダウンのスクリーンショット。" lightbox="./media/tutorial-users/cohort-2.png":::


## <a name="compare-desired-activity-to-reality"></a>望ましいアクティビティと現実を比較する
前のパネルでは、アプリケーションのユーザーが実行したことに注目していますが、**ファネル** では、ユーザーに実行してほしいことに注目します。  ファネルは、アプリケーションの一連の手順とこれらの手順の間を移動したユーザーの割合を表します。  たとえば、アプリケーションに接続して製品を検索したユーザーの割合を測定するファネルを作成できます。  その製品を買い物かごに追加したユーザーの割合と、購入を完了したユーザーの割合を確認できます。

1. メニューの **[ファネル]** を選択し、 **[編集]** を選択します。 

3. 各手順のアクションを選択して、少なくとも 2 つの手順があるファネルを作成します。  アクションの一覧は、Application Insights によって収集された使用状況データから作成されます。

    :::image type="content" source="./media/tutorial-users/funnel.png" alt-text="[ファネル] タブのスクリーンショット。[編集] タブでステップを選択しています。" lightbox="./media/tutorial-users/funnel.png":::

4. **[表示]** タブを選択して、結果を表示します。 右側のウィンドウには、最初のアクティビティの前と最後のアクティビティの後に発生した最も一般的なイベントが表示され、特定のシーケンスを中心とするユーザーの傾向を理解するために役立ちます。

     :::image type="content" source="./media/tutorial-users/funnel-2.png" alt-text="表示されている [ファネル] タブのスクリーンショット。" lightbox="./media/tutorial-users/funnel-2.png":::

4. ファネルを保存するには、 **[保存]** を選択します。 

## <a name="learn-which-customers-return"></a>どの顧客が再来しているかを知る

**[保持]** は、どのユーザーがアプリケーションに再来しているかを理解するために役立ちます。  

1. メニューの **[リテンション期間]** 、[Retention Analysis Workbook]\(リテンション期間分析ブック\) の順に選択します。
2. 既定では、分析された情報には、何らかのアクションを実行した後、何らかのアクションを実行するために再来したユーザーが含まれます。  このフィルターを、たとえば購入の完了後に再来したユーザーのみを含めるように変更できます。

      :::image type="content" source="./media/tutorial-users/retention.png" alt-text="保持フィルターに対して設定された条件に一致するユーザーのグラフを示すスクリーンショット。" lightbox="./media/tutorial-users/retention.png":::

3. 条件と一致する再来ユーザーが、グラフの形で期間別に表示されます。 一般的なパターンでは、時間の経過と共に再来ユーザーは緩やかに減少します。  ある期間から次の期間への急激な低下には、重要な意味がある場合があります。 

      :::image type="content" source="./media/tutorial-users/retention-2.png" alt-text="リテンション期間ブックのスクリーンショット。# 週後に再来するユーザーのグラフを確認できます。" lightbox="./media/tutorial-users/retention-2.png":::

## <a name="analyze-user-navigation"></a>ユーザーの移動を分析する
**ユーザー フロー** は、ユーザーがサイトのページと機能をどのように移動しているかを視覚化します。  これにより、ユーザーがどの特定のページから移動することが多いか、アプリケーションをどのように終了することが多いか、繰り返し実行される何らかのアクションがあるかどうかといった疑問の回答を得ることができます。

1.  メニューの **[ユーザー フロー]** を選択します。
2.  **[新規]** をクリックして新しいユーザー フローを作成した後、 **[編集]** を選択してその詳細を編集します。
3.  **[時間の範囲]** を [7 日間] に増やした後、最初のイベントを選択します。  フローは、そのイベントで開始したユーザー セッションを追跡します。

     :::image type="content" source="./media/tutorial-users/flowsedit.png" alt-text="新しいユーザー フローを作成する方法を示すスクリーンショット。" lightbox="./media/tutorial-users/flowsedit.png":::
    
4.  ユーザー フローが表示され、複数のユーザー パスとそれらのセッション数を確認できます。  青い線は、ユーザーが現在のアクションの後で実行したアクションを示します。  赤い線は、ユーザー セッションの終了を示します。

   :::image type="content" source="./media/tutorial-users/flows.png" alt-text="ユーザー フローのユーザー パスとセッション数の表示を示すスクリーンショット。" lightbox="./media/tutorial-users/flows.png":::

5.  フローからイベントを削除するには、アクションの角にある **[x]** を選択した後、 **[グラフの作成]** を選択します。  グラフは、そのイベントが削除されたインスタンスを使用して再描画されます。 **[編集]** を選択して、そのイベントが **[除外されたイベント]** に追加されていることを確認します。

    :::image type="content" source="./media/tutorial-users/flowsexclude.png" alt-text="ユーザー フローの除外されたイベントの一覧を示すスクリーンショット。" lightbox="./media/tutorial-users/flowsexclude.png":::

## <a name="consolidate-usage-data"></a>使用状況データを統合する
**Workbooks** は、データの視覚化、Analytics のクエリ、およびテキストをインタラクティブなドキュメントに統合します。  Workbooks を使用して、一般的な使用状況情報のグループ化、特定のインシデントからの情報の統合、またはアプリケーションの使用状況に関するレポートのチームへの返送を実行できます。

1.  メニューの **[Workbooks]** を選択します。
2.  **[新規]** を選択して新しいブックを作成します。
3.  前日のすべての使用状況データを棒グラフとして表示するクエリがすでに用意されています。  このクエリを使用することも、手動で編集することも、または **[サンプル]** を選択して他の便利なクエリを選択することもできます。

    :::image type="content" source="./media/tutorial-users/sample-queries.png" alt-text="[サンプル] ボタンと使用できるサンプル クエリの一覧を示すスクリーンショット。" lightbox="./media/tutorial-users/sample-queries.png":::

4.  **[編集完了]** を選択します。
5.  ブックの上部にあるテキストを編集するには、上部ペインの **[編集]** を選択します。  これはマークダウンを使用してフォーマットされます。

6.  ユーザー情報をグラフに追加するには、 **[ユーザーの追加]** を選択します。  必要に応じてグラフの詳細を編集した後、 **[編集完了]** を選択して保存します。

ブックの詳細については、[ブックの概要](../visualize/workbooks-overview.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
ここでは、ユーザーを分析する方法を学習しました。次のチュートリアルに進んで、この情報をアプリケーションに関するその他の有用なデータと組み合わせるカスタム ダッシュ ボードの作成方法を学習してください。

> [!div class="nextstepaction"]
> [カスタム ダッシュボードを作成する](./tutorial-app-dashboards.md)