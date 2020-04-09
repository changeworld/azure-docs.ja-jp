---
title: Microsoft Azure Data Explorer Flow コネクタ (プレビュー)
description: Microsoft Flow コネクタを使用して、自動的にスケジュールされたタスクまたはトリガーされたタスクのフローを作成する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 198b78d5bab15057fdb6c7f6d4e8fff9f77d496e
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397094"
---
# <a name="microsoft-flow-connector-preview"></a>Microsoft Flow コネクタ (プレビュー)

Azure Data Explorer Flow コネクタを使用すると、Azure Data Explorer は [Microsoft Power Automate の Flow 機能](https://flow.microsoft.com/)を使用して、自動的に Kusto クエリとコマンドをスケジュールされたタスクまたはトリガーされたタスクの一部として実行することができます。

一般的な使用シナリオには、次のものがあります。

* テーブルとグラフを含めた日次レポートの送信
* クエリ結果に基づいて通知を設定する
* クラスターでの制御コマンドのスケジュール設定
* Azure Data Explorer と他のデータベース間のデータのエクスポートとインポート 

詳細については、「[Microsoft Flow コネクタの使用例](flow-usage.md)」を参照してください。

##  <a name="log-in"></a>ログイン 

1. [Microsoft Power Automate](https://flow.microsoft.com/) にログインします。

1. 初めて接続するときに、サインインを求めるプロンプトが表示されます。

1. **[サインイン]** を選択して、資格情報を入力します。

![[サインイン] ダイアログ](./media/flow/flow-signin.png)

## <a name="authentication"></a>認証

ユーザー資格情報または AAD アプリケーションを使用して認証を行うことができます。

> [!Note]
> アプリケーションが [AAD アプリケーション](https://docs.microsoft.com/azure/kusto/management/access-control/how-to-provision-aad-app)であり、クラスターでクエリを実行する権限が与えられていることを確認します。

1. Microsoft Flow コネクタの右上にある 3 つのドットを選択します。![接続を追加する](./media/flow/flow-addconnection.png)

1. **[新しい接続 を追加する]** を選択し、 **[サービス プリンシパルで接続]** を選択します。
![サインイン ダイアログ](./media/flow/flow-signin.png)

1. 必要な情報を入力します。
    * 接続名:一見して意味の伝わる、新しい接続の名前
    * クライアント ID: あなたのアプリケーション ID
    * クライアント シークレット: アプリケーション キー
    * テナント:アプリケーションを作成した AAD ディレクトリの ID。 たとえば、Microsoft のテナント ID は次のようになります。72f988bf-86f1-41af-91ab-2d7cd011db47

![アプリケーション認証](./media/flow/flow-appauth.png)

認証が完了すると、新しく追加された接続がフローで使用されていることがわかります。

![完成したアプリケーションの認証](./media/flow/flow-appauthcomplete.png)

このフローは今後、これらのアプリケーションの資格情報を使用して実行されます。

## <a name="find-the-azure-kusto-connector"></a>Azure Kusto コネクタを検索する

Microsoft Flow コネクタを使用するには、まず最初にトリガーを追加する必要があります。 トリガーは、一定の期間に基づいて定義することも、直前のフローアクションへの応答として定義することも可能です。

1. [[新しいフローを作成する](https://flow.microsoft.com/manage/flows/new)] または、ホームページから **[My Flows]** のアクションを選択して **[+ 新規]** を選択します。

    ![新しいフローを作成する](./media/flow/flow-newflow.png)

1. スケジュールを追加する — ゼロから作成

    ![新しい予定フローを作成する](./media/flow/flow-scheduled-from-blank.png)

1. [予定フローのビルド] ページに、必要な情報を入力します。
    ![予定フローをビルドする](./media/flow/flow-build-scheduled-flow.png)
1. **［作成］** を選択します
1. **[+ New step (+ 新しいステップ)]** を選択します。
1. 検索ボックスに「Kusto」と入力します。

    ![フロー アクションを選択する](./media/flow/flow-actions.png)

1. **Azure Data Explorer**を選択します。

## <a name="flow-actions"></a>フロー アクション

Azure Data Explorer コネクタを開くと、フローに追加可能な 3 つのアクションがあります。

このセクションでは、Microsoft Flow アクションそれぞれの機能とパラメーターについて説明します。

![Flow Azure Data Explorer のアクション](./media/flow/flow-adx-actions.png)

### <a name="run-control-command-and-visualize-results"></a>制御コマンドを実行して結果を視覚化する

[制御コマンドの実行] を使用して結果のアクションを視覚化し、[制御コマンド](https://docs.microsoft.com/azure/kusto/management/index)を実行します。

1. クラスターの URL を指定します。 たとえば、 https://clusterName.eastus.kusto.windows.net のように指定します。
1. データベースの名前を入力します。
1. 制御コマンドを指定します。
    * フローで使用されるアプリとコネクタから、動的コンテンツを選択
    * 値へのアクセス、値の変換や比較のための式を追加
1. このアクションの結果をテーブルまたはグラフとしてメール送信するには、テーブル/グラフの種類を指定します。
    * HTML テーブル
    * 円グラフ
    * 時間グラフ
    * 棒グラフ

![フロー制御コマンドを実行する](./media/flow/flow-runcontrolcommand.png)

> [!IMPORTANT]
> *[クラスター名]* フィールドに、クラスターの URL を入力します。

### <a name="run-query-and-list-results"></a>クエリを実行し、結果を一覧表示する

> [!Note]
> クエリがドットで始まっている場合には (つまりそれは[制御コマンド](https://docs.microsoft.com/azure/kusto/management/index))、[[制御コマンドを実行して結果を視覚化する](#run-control-command-and-visualize-results)] のアクションを使用します。

このアクションにより、クエリが Kusto クラスターに送信されます。 この後に追加されるアクションは、クエリの結果の各行を反復処理します。

次の例では、1 分ごとにクエリをトリガーし、そのクエリ結果に基づいてメールを送信します。 このクエリでは、データベース内の行数をチェックし、行数が 0 よりも大きい場合に限ってメールを送信します。 

![クエリを実行し、結果を一覧表示する](./media/flow/flow-runquerylistresults-2.png)

> [!Note]
> 複数の行がある列の場合、コネクタは、そこにある各行を実行します。

### <a name="run-query-and-visualize-results"></a>クエリを実行し、結果を視覚化する
        
> [!Note]
> クエリがドットで始まっている場合には (つまりそれは[制御コマンド](https://docs.microsoft.com/azure/kusto/management/index))、[[制御コマンドを実行して結果を視覚化する](#run-control-command-and-visualize-results)] のアクションを使用します。
        
Kusto クエリの結果をテーブルまたはグラフとして表示するには、[クエリを実行し、結果を視覚化する] のアクションを使用します。 例えばこのフローを使用して、日次 ICM レポートをメールで受信することができます。 
    
この例では、クエリの結果が HTML テーブルとして返されます。
            
![クエリを実行し、結果を視覚化する](./media/flow/flow-runquery.png)

> [!IMPORTANT]
> *[クラスター名]* フィールドに、クラスターの URL を入力します。

## <a name="email-kusto-query-results"></a>Kusto クエリの結果をメールで送信する

任意のフローにステップを含めることで、任意のメールアドレスに宛ててメールでレポートを送信できます。 

1. 新しいステップをフローに追加するには、 **[+ 新規のステップ]** を選択します。
1. 検索フィールドに「Office 365」と入力し、 **[Office 365 Outlook]** を選択します。
1. **[メールの送信 (V2)]** を選択します。
1. メール レポートの送信先のメールアドレスを入力します。
1. メールの件名を入力します。
1. **[コード ビュー]** . を選択します。
1. [ *本文*] フィールドにカーソルをあわせ、 **[動的コンテンツの追加]** を選択します。
1. **BodyHtml**を選択します。
    ![電子メールの送信](./media/flow/flow-send-email.png)
1. **[詳細オプションの表示]** 　を選択します。
1. [*添付の名前 -1*] フィールドで、 **[添付の名前]** を選択します。
1. [*添付コンテンツ*] フィールドで、 **[添付コンテンツ]** を選択します。
1. 必要に応じて、添付をさらに追加します。 
1. 必要に応じて、重要度レベルを設定します。
1. **[保存]** を選択します。

![電子メールの送信](./media/flow/flow-add-attachments.png)

## <a name="check-if-your-flow-succeeded"></a>フローが成功したかを確認する

フローが成功したかを確認するには、フローの実行履歴を確認してください。
1. [Microsoft Flow ホームページ](https://flow.microsoft.com/) に移動します。
1. メインメニューから、[[マイ フロー](https://flow.microsoft.com/manage/flows)] を選択します。
    ![「マイ フロー」のページ](./media/flow/flow-myflows.png)
1. 調べたいフローの行の上で、その他のコマンド アイコンを選択し、**実行履歴** を選択します。

    ![実行履歴のメニュー](./media/flow//flow-runhistory.png)

    実行されたすべてのフローの開始時刻、期間、状態が一覧表示されます。
    ![「実行履歴の結果」のページ](./media/flow/flow-runhistoryresults.png)

    フローの詳細については、「[マイフロー](https://flow.microsoft.com/manage/flows)」のページ上で調べたいフローを選択します。

    ![「実行履歴の結果」のページ](./media/flow/flow-fulldetails.png) 

実行が失敗した理由を確認するには、[実行の開始時刻] を選択します。 フローが表示され、失敗したフローのステップが赤色の感嘆符で示されます。 失敗したステップを展開し、詳細を表示します。 右側のウィンドウにはエラーに関する情報が含まれており、これをもとにトラブルシューティングが可能です。

![フローのエラー](./media/flow/flow-error.png)

## <a name="timeout-exceptions"></a>タイムアウト例外

フローが 7 分以上実行されると、そのフローは失敗し、「リクエスト タイムアウト」の例外が返されます。

[Microsoft Flow の制限事項](#limitations) の詳細を確認します。
    
Azure Data Explorer では、時間の制限なしに同じクエリを正常に実行し続けることも可能で、また、制限時間の変更も可能です。
            
以下は、「要求タイムアウト」 例外のイメージです。
    
![フローの「要求タイムアウト」例外エラー](./media/flow/flow-requesttimeout.png)
    
タイムアウトの問題を解決するには、クエリをさらに効率化してより速く実行できるようするか、または、クエリをチャンクに分割します。 各チャンクは、クエリの別のパートで実行可能です。

詳細については、「[クエリのベストプラクティス](https://docs.microsoft.com/azure/kusto/query/best-practices)」を参照してください。

## <a name="limitations"></a>制限事項

* クライアントに返される結果は、500,000レコードが上限です。 これらのレコードのメモリの合計は 64 MB を超えることはできず、実行時間の上限は 7 分間です。
* このコネクタは、[フォーク](https://docs.microsoft.com/azure/kusto/query/forkoperator)および[ファセット](https://docs.microsoft.com/azure/kusto/query/facetoperator)演算子をサポートしていません。
* フローの実行には、Microsoft Edge と Chrome が最適です。

## <a name="next-steps"></a>次のステップ

[Microsoft Azure Explorer Logic App コネクタ](https://docs.microsoft.com/azure/kusto/tools/logicapps)について説明します。これは、自動的に Kusto クエリとコマンドを予定タスクやトリガーされたタスクの一部として実行できる、もう 1 つの方法です。
