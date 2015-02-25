<properties pageTitle="データへのページングの追加 (iOS) | モバイル デベロッパー センター" description="ページングを使用して、Mobile Services から iOS アプリに返されるデータの量を管理する方法について説明します。" services="mobile-services" documentationCenter="ios" authors="krisragh" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh"/>

# ページングを使用したモバイル サービス クエリの改善

[AZURE.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

このトピックでは、ページングを使用して、Azure Mobile Services から iOS アプリケーションに返されるデータの量を管理する方法について説明します。このチュートリアルでは、クライアントで **fetchLimit** および **fetchOffset** クエリ プロパティを使用して、データの特定の "ページ" を要求します。

> [AZURE.NOTE] モバイル デバイス クライアントでデータがオーバーフローしないように、モバイル サービスでは、自動ページ制限を実装しています。既定では、1 つの応答で最大 50 項目に設定されます。ページ サイズを指定することで、1 つの応答で 1,000 項目まで明示的に要求できます。

このチュートリアルは、前の[データの使用]に関するチュートリアルの手順およびサンプル アプリケーションを基に作成されています。このチュートリアルを開始する前に、少なくとも、データ操作シリーズの最初のチュートリアル (「[データの使用]」) を完了している必要があります。

1. Xcode で、チュートリアル「[データの使用]」を実行したときに変更したプロジェクトを開きます。

2. **Run** (Command + R キー) を押して、プロジェクトをビルドし、アプリケーションを開始します。次に、テキスト ボックスにテキストを入力し、プラス (**[+]**) アイコンをクリックします。

3. 前の手順を少なくとも 3 回繰り返して、TodoItem テーブルに項目を 3 つ以上保存します。

4. QSTodoService.m ファイルを開き、次のメソッドを見つけます。

        - (void)refreshDataOnSuccess:(QSCompletionBlock)completion

   	メソッド全体を次のコードに置き換えます。

        // Create a predicate that finds active items in which complete is false
        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
	
        // Retrieve the MSTable's MSQuery instance with the predicate you just created.
        MSQuery * query = [self.table queryWithPredicate:predicate];

        query.includeTotalCount = YES; // Request the total item count

        // Start with the first item, and retrieve only three items
        query.fetchOffset = 0;
        query.fetchLimit = 3;

        // Invoke the MSQuery instance directly, rather than using the MSTable helper methods.
        [query readWithCompletion:^(NSArray *results, NSInteger totalCount, NSError *error) {

            [self logErrorIfNotNil:error];
            if (!error)
            {
                // Log total count.
                NSLog(@"Total item count: %@",[NSString stringWithFormat:@"%zd", (ssize_t) totalCount]);
            }

            items = [results mutableCopy];

            // Let the caller know that we finished
            completion();
        }];

   	このクエリでは、完了マークが付けられていない上位 3 つの項目を返します。

5. アプリケーションをリビルドして開始します。

    TodoItem テーブルから最初の 3 つの結果だけが表示されることに注目してください。

7. 次のコード行を見つけて、再度 **refreshDataOnSuccess** メソッドを更新します。

        query.fetchOffset = 0;

   	今回は、**query.fetchOffset** の値を 3 に設定します。

   	このクエリでは、最初の 3 つの結果をスキップし、その後の 3 つを返します。ページ サイズが 3 つの項目である場合、これは実質的にデータの 2 番目の "ページ" になります。

    > [AZURE.NOTE] このチュートリアルでは、**fetchOffset** と **fetchLimit** プロパティにハードコーディングされたページング値を設定することで、簡略化したシナリオを使用しています。実際のアプリケーションでは、ユーザーが前後のページに移動できるように、ページャー コントロールまたは同等の UI と共に上記と同様のクエリを使用することができます。また、**query.includeTotalCount = YES** を設定して、ページングされたデータと共に、サーバーで使用できるすべての項目の合計数を取得することもできます。

## <a name="next-steps"></a>次のステップ

これで、モバイル サービスのデータを操作するための基本について説明する一連のチュートリアルは終了です。次のモバイル サービスのトピックの詳細を確認することをお勧めします。

* [認証の使用]
  <br/>Windows アカウントを使用してアプリケーションのユーザーを認証する方法について説明します。

<!--
* [プッシュ通知の使用]
  <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。
-->

<!-- Anchors. -->

[次のステップ]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started-ios
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-ios
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-ios
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-ios

[管理ポータル]: https://manage.windowsazure.com/


<!--HONumber=42-->
