<properties 
	pageTitle="データへのページングの追加 (Xamarin iOS) | モバイル デベロッパー センター" 
	description="ページングを使用して、Mobile Services から Xamarin iOS アプリに返されるデータの量を管理する方法について説明します。" 
	services="mobile-services" 
	authors="lindydonna" 
	manager="dwrede" 
	editor="" 
	documentationCenter="xamarin"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/24/2014" 
	ms.author="donnam"/>


# ページングを使用したモバイル サービス クエリの改善

[AZURE.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

このトピックでは、ページングを使用して、Azure Mobile Services から iOS アプリケーションに返されるデータの量を管理する方法について説明します。このチュートリアルでは、クライアントで **Skip** および **Take** クエリ プロパティを使用して、データの特定の "ページ" を要求します。

> [AZURE.NOTE] モバイル デバイス クライアントでデータがオーバーフローしないように、モバイル サービスでは、自動ページ制限を実装しています。既定では、1 つの応答で最大 50 項目に設定されます。ページ サイズを指定することで、1 つの応答で 1,000 項目まで明示的に要求できます。

このチュートリアルは、前の[データの使用]に関するチュートリアルの手順およびサンプル アプリケーションを基に作成されています。このチュートリアルを開始する前に、少なくとも、データ操作シリーズの最初のチュートリアル (「[データの使用]」) を完了している必要があります。 

1. Xamarin Studio で、「[モバイル サービスでのデータの使用][データの使用]」チュートリアルを実行したときに修正したプロジェクトを開きます。

2. **[Run]** を押してプロジェクトをビルドし、アプリケーションを開始します。次に、テキスト ボックスにテキストを入力し、プラス (**[+]**) アイコンをクリックします。

3. 前の手順を少なくとも 3 回繰り返して、TodoItem テーブルに項目を 3 つ以上保存します。 

4. **TodoService.cs** ファイルを開き、**RefreshDataAsync** メソッドを見つけ、<code>todoTable</code> の LINQ クエリを次のクエリで置き換えます。 

			Items = await todoTable
							.Where (todoItem => todoItem.Complete == false)
        		            .Take(3)
                		    .ToListAsync();

   	このクエリでは、完了マークが付けられていない上位 3 つの項目を返します。 

5. アプリケーションをリビルドして開始します。 
   
    TodoItem テーブルから最初の 3 つの結果だけが表示されることに注目してください。 

7. 再度、**RefreshDataAsync** メソッドの LINQ クエリを次のクエリに置き換えます。

			Items = await todoTable
							.Where (todoItem => todoItem.Complete == false)
		                    .Skip(3)
		                    .Take(3)
		                    .ToListAsync();

   	今回は、**Skip** の値を 3 に設定します。 

   	このクエリでは、最初の 3 つの結果をスキップし、その後の 3 つを返します。ページ サイズが 3 つの項目である場合、これは実質的にデータの 2 番目の "ページ" になります。

    > [AZURE.NOTE] このチュートリアルでは、**Skip** および **Take** プロパティにハードコーディングされたページング値を設定することで簡略化したシナリオを使用しています。実際のアプリケーションでは、ユーザーが前後のページに移動できるように、ページャー コントロールまたは同等の UI と共に上記と同様のクエリを使用できます。また、**IncludeTotalCount** メソッドを呼び出して、ページングされたデータと共に、サーバーで使用できるすべての項目の合計数を取得することもできます。

## <a name="next-steps"> </a>次のステップ

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
[モバイル サービスの使用]: ./ja-jp/develop/mobile/tutorials/get-started-xamarin-ios
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-xamarin-ios

[管理ポータル]: https://manage.windowsazure.com/



<!--HONumber=42-->
