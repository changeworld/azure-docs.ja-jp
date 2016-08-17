<properties 
	pageTitle="Logic Apps の機能を使用する |Microsoft Azure" 
	description="ロジック アプリの高度な機能の使用方法について説明します。" 
	authors="stepsic-microsoft-com" 
	manager="erikre" 
	editor="" 
	services="logic-apps" 
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/28/2016"
	ms.author="stepsic"/>
	
# Logic Apps の機能を使用する

[前のトピック](app-service-logic-create-a-logic-app.md)では、初めてのロジック アプリを作成しました。ここでは、App Services Logic Apps を使用してさらに詳細なプロセスを構築する方法を説明します。このトピックでは、新しい Logic Apps の概念について説明します。

- 条件付きロジック。特定の条件が満たされる場合にのみアクションを実行します。
- 既存のロジック アプリを編集するためのコード ビュー。
- ワークフローを開始するためのオプション。

このトピックを完了する前に、「[新しいロジック アプリを作成する](app-service-logic-create-a-logic-app.md)」の手順を完了する必要があります。[Azure ポータル]でロジック アプリを参照し、概要の **[トリガーとアクション]** をクリックしてロジック アプリの定義を編集します。

## 参考資料

次の資料が役立つ場合があります。

- [Management and runtime REST APIs (管理 REST API およびランタイム REST API)](https://msdn.microsoft.com/library/azure/mt643787.aspx) - Logic Apps を直接起動する方法が記載されています。
- [Language reference (言語リファレンス)](https://msdn.microsoft.com/library/azure/mt643789.aspx) - サポートされるすべての関数/式の包括的な一覧
- [Trigger and action types (トリガーおよびアクションのタイプ)](https://msdn.microsoft.com/library/azure/mt643939.aspx) - 各種アクションとそれらで使用される入力値
- [Overview of App Service (App Service の概要)](../app-service/app-service-value-prop-what-is.md) - ソリューションを作成するときに選択するコンポーネントの説明

## 条件付きロジックの追加

元のフローが機能していても、改良できる領域もあります。


### 条件付き
このロジック アプリにより、多数の電子メールが届く場合があります。次の手順では、ロジックを追加して、ツイートが特定の数のフォロワーを持つユーザーから届いた場合にのみ電子メールを受信するようにします。

1. プラス記号をクリックし、Twitter の "*ユーザーの取得*" アクションを探します。

2. トリガーから **[Tweeted by]** フィールドに入り、Twitter ユーザーに関する情報を取得します。

	![ユーザーの取得](./media/app-service-logic-use-logic-app-features/getuser.png)

3. プラス記号をもう一度クリックし、今度は **[Add Condition]** を選択します。

4. 最初のボックスで、**[Get User]** の下の **[...]** をクリックして **[Followers count]** フィールドを探します。

5. ドロップダウンで **[Greater than]** を選択します。

6. 2 番目のボックスで、ユーザーの目的のフォロワー数を入力します。

	![条件付き](./media/app-service-logic-use-logic-app-features/conditional.png)

7.  最後に、電子メール ボックスを **[If Yes]** ボックスにドラッグ アンド ドロップします。これにより、このフォロワー数を満たした場合にのみ電子メールが届くようになります。

## ForEach によるリストの繰り返し処理

ForEach ループでは、アクションを繰り返す配列を指定します。配列ではない場合、フローは失敗します。たとえば、メッセージの配列を出力する action1 があり、各メッセージを送信する場合は、アクションのプロパティに forEach : "@action('action1').outputs.messages" のような forEach ステートメントを組み込むことができます。
 

## コード ビューを使用したロジック アプリの編集

デザイナー以外でも、次のようにロジック アプリを定義するコードを直接編集することができます。

1. コマンド バーの **[コード ビュー]** をクリックします。

	全画面のエディターが開き、先ほど編集した定義が表示されます。

	![コード ビュー](./media/app-service-logic-use-logic-app-features/codeview.png)

    テキスト エディターを使用することで、同じロジック アプリ内やロジック アプリ間で任意の数のアクションをコピーして貼り付けることができます。定義からセクション全体を追加または削除することも簡単です。他のユーザーと定義を共有することもできます。

2. コード ビューで変更を加えた後に、**[保存]** をクリックします。

### パラメーター
Logic Apps の一部の機能はコード ビューのみで使用できます。その例がパラメーターです。パラメーターを使用すると、ロジック アプリ全体にわたって簡単に値を再利用できます。たとえば、いくつかの操作で使用する電子メール アドレスがある場合、電子メール アドレスをパラメーターとして定義します。

次に、クエリ用語でパラメーターを使用するように既存のロジック アプリを更新します。

1. コード ビューで `parameters : {}` オブジェクトを検索し、次の topic オブジェクトを挿入します。

	    "topic" : {
		    "type" : "string",
		    "defaultValue" : "MicrosoftAzure"
	    }
    
2. `twitterconnector` アクションまでスクロールし、クエリの値を探して `#@{parameters('topic')}` に置き換えます。**concat** 関数を使用して 2 つ以上の文字列を結合することもできます。たとえば `@concat('#',parameters('topic'))` は上記と同じです。
 
パラメーターは、よく変更する可能性がある値を抜き出すための優れた手段です。さまざまな環境でパラメーターを上書きする必要がある場合に特に便利です。環境に基づいてパラメーターを上書きする方法の詳細については、[REST API のドキュメント](https://msdn.microsoft.com/library/mt643787.aspx)を参照してください。

次に、**[保存]** をクリックすると、新しいツイートが 6 回以上リツイートされるたびに Dropbox の **tweets** というフォルダーに配信されます。

Logic App 定義の詳細については、[「Author Logic App definitions (Logic App 定義のオーサリング](app-service-logic-author-definitions.md)」を参照してください。

## ロジック アプリ ワークフローの開始
ロジック アプリで定義されているワークフローを開始するためのオプションは数種類あります。ワークフローはいつでも、[Azure ポータル]からオンデマンドで開始できます。

### 定期実行のトリガー
定期実行のトリガーは、指定した間隔で実行されます。トリガーに条件付きロジックが設定されている場合は、トリガーによって、ワークフローを実行する必要があるかどうかが判断されます。`200` の状態コードが返された場合は、トリガーを実行する必要があることを示しています。実行する必要がないときは、`202` の状態コードが返されます。

### REST API を使用したコールバック
サービスは、ロジック アプリのエンドポイントを呼び出して、ワークフローを開始することができます。詳細については、「[呼び出し可能なエンドポイントとしての Logic Apps](app-service-logic-connector-http.md)」を参照してください。この種のロジック アプリをオンデマンドで開始するには、コマンド バーの **[今すぐ実行]** をクリックします。

<!-- Shared links -->
[Azure ポータル]: https://portal.azure.com

<!---HONumber=AcomDC_0803_2016-->