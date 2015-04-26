<properties 
	pageTitle=".Net バックエンドを使用したデータの検証および変更 (Windows Phone 8) | モバイル デベロッパー センター" 
	description=".Net バックエンド Microsoft Azure Mobile Services を使用して Windows Phone アプリのデータを検証、変更、強化する方法について説明します。" 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="wesmc"/>

# .NET バックエンドを使用した Mobile Services のデータの検証および変更

[AZURE.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

このトピックでは、.NET バックエンド Azure Mobile Services 内でコードを使用してデータを検証および変更する方法について説明します。.NET バックエンド サービスは、Web API フレームワークを使用して構築した HTTP サービスです。Web API フレームワークで定義されている  `ApiController` クラスを扱ったことがある方であれば、Mobile Services で提供される  `TableController` クラスを直感的に理解できます。`TableController` は、`ApiController` クラスから派生し、データベース テーブルとやり取りするための追加機能を提供します。挿入や更新の対象となるデータを操作する目的で使用でき、このチュートリアルで示す検証やデータの変更もその中に含まれます。 

このチュートリアルでは、次の基本的な手順について説明します。:

1. [文字列の長さの検証の追加]
2. [検証をサポートするためのクライアントの更新]
3. [長さの検証テスト]
4. [CompleteDate に関するタイムスタンプの追加]
5. [CompleteDate を表示するためのクライアントの更新]

このチュートリアルは、前のチュートリアル「[既存のアプリケーションへの Mobile Services の追加](/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/)」の手順およびサンプル アプリケーションを基に作成されています。このため、このチュートリアルの前に、前のチュートリアルを完了している必要があります。  

## <a name="string-length-validation"></a>検証の追加

[AZURE.INCLUDE [mobile-services-dotnet-backend-add-validation](../includes/mobile-services-dotnet-backend-add-validation.md)]


## <a name="update-client-validation"></a>クライアントの更新

今度は、データを検証し、テキストの長さが無効な場合はエラー応答を送信するようにモバイル サービスを設定するため、検証からのエラー応答を処理できるようにアプリケーションを更新する必要があります。エラーは、クライアント アプリケーションによる `IMobileServiceTable<TodoItem].InsertAsync()` の呼び出しからの `MobileServiceInvalidOperationException` としてキャッチされます。

1. Visual Studio のソリューション エクスプローラー ウィンドウで、クライアント プロジェクトに移動し、MainPage.xaml.cs ファイルを開きます。次の using ステートメントをファイルに追加します。

        using Newtonsoft.Json.Linq;

2.MainPage.xaml.cs で、既存の **InsertTodoItem** メソッドを次のコードに置き換えます。:

        private async void InsertTodoItem(TodoItem todoItem)
        {
            // This code inserts a new TodoItem into the database. When the operation completes
            // and Mobile Services has assigned an Id, the item is added to the CollectionView
            MobileServiceInvalidOperationException invalidOpException = null;
            try
            {
                await todoTable.InsertAsync(todoItem);
                ite
	ms.Add(todoItem);
            }
            catch (MobileServiceInvalidOperationException e)
            {
                invalidOpException = e;
            }
            if (invalidOpException != null)
            {
                string strJsonContent = await invalidOpException.Response.Content.ReadAsStringAsync();
                var responseContent = JObject.Parse(strJsonContent);
                MessageBox.Show(string.Format("{0} (HTTP {1})",
                    (string)responseContent["message"], (int)invalidOpException.Response.StatusCode),
                    invalidOpException.Message, MessageBoxButton.OK);
            }
        }

   	このバージョンのメソッドには、**MobileServiceInvalidOperationException** に対するエラー処理が含まれていて、応答内容から取得した逆シリアル化エラー メッセージをメッセージ ボックス内に表示します。

## <a name="test-length-validation"></a>長さの検証テスト

1. Visual Studio で、希望する Windows Phone 展開ターゲットを構成します。ソリューション エクスプローラー ウィンドウでクライアント アプリケーション プロジェクトを右クリックし、**[デバッグ]**、**[新しいインスタンスを開始]** の順にクリックします。

2. 新しい todo 項目に対して 10 文字を超える長さのテキストを入力し、**[保存]** をクリックします。

    ![][1]

3. 無効なテキストへの応答として、次のようなメッセージ ダイアログが表示されます。

    ![][2]

## <a name="add-timestamp"></a>CompleteDate に関するタイムスタンプ フィールドの追加

[AZURE.INCLUDE [mobile-services-dotnet-backend-add-completedate](../includes/mobile-services-dotnet-backend-add-completedate.md)]


## <a name="update-client-timestamp"></a>CompleteDate を表示するためのクライアントの更新

最後の手順は、クライアントを更新して新しい **CompleteDate** データを表示することです。 


1. Visual Studio のソリューション エクスプローラーで todolist クライアント プロジェクトを使用し、MainPage.xaml ファイルを開き、その中の **StackPanel** 要素を、次に示す定義で置き換えます。その後、ファイルを保存します。これにより、**CheckBoxComplete** に対応するイベント ハンドラーが変更され、`click` イベントをコード内で処理することになります。また、チェック ボックスの横にテキスト ブロックを追加して、完全な日付タイムスタンプにバインドします。
	      
        <StackPanel Orientation="Horizontal">
          <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}"
            Click="CheckBoxComplete_Clicked" Content="{Binding Text}" Margin="10,5" 
            VerticalAlignment="Center"/>
          <TextBlock Name="textCompleteDate" Text="{Binding CompleteDate}" 
            VerticalAlignment="Center" />
        </StackPanel>


2. Visual Studio のソリューション エクスプローラーで todolist クライアント プロジェクトを使用し、MainPage.xaml.cs ファイルを開き、 `CheckBoxComplete_Checked` イベント ハンドラーを、次に示す `CheckBoxComplete_Clicked` に置き換えます。これにより、項目が完成した後、完全な日付を確認できます。

        private void CheckBoxComplete_Clicked(object sender, RoutedEventArgs e)
        {
            CheckBox cb = (CheckBox)sender;
            TodoItem item = cb.DataContext as TodoItem;
            UpdateCheckedTodoItem(item);
        }


3. MainPage.xaml.cs ファイルで、既存の **TodoItem** クラスを、null 許容型として新しい **CompleteDate** プロパティを含む、次の定義に置き換えます。

        public class TodoItem
        {
            public string Id { get; set; }
            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }
            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }        
            [JsonProperty(PropertyName = "CompleteDate")]
            public DateTime? CompleteDate { get; set; }
        }
	
    >[AZURE.NOTE]   <code>DataMemberAttribute</code>  は、アプリケーション内の新しい   <code>CompleteDate</code>  プロパティを、TodoItem テーブル内で定義された  <code>CompleteDate</code>  列にマップするようにクライアントに指示します。この属性を使用することにより、アプリケーションでは、SQL データベース内の列名と異なるプロパティ名をオブジェクトに対して使用することができます。
    

	


4. MainPage.xaml.cs で、既存の **RefreshTodoItems** メソッド内にある `.Where` 句関数を削除またはコメント アウトします。その結果、完了した todoitems が結果の中に含まれるようになります。

            // This query filters out completed TodoItems and 
            // items without a timestamp. 
            items = await todoTable
               //.Where(todoItem => todoItem.Complete == false)
               .ToCollectionAsync();


5. MainPage.xaml.cs 内で、**UpdateCheckedTodoItem** メソッドを次のように更新します。その結果、更新を行った後に項目の表示が更新され、完了した項目はリストから削除されなくなります。その後、ファイルを保存します。	

        private async void UpdateCheckedTodoItem(TodoItem item)
        {
            // This code takes a freshly completed TodoItem and updates the database.
            await todoTable.UpdateAsync(item);
            //ite
	ms.Remove(item);
            RefreshTodoItems();
        }


6. Visual Studio のソリューション エクスプローラー ウィンドウで **[ソリューション]** を右クリックし、**[ソリューションのリビルド]** をクリックして、クライアントと .NET バックエンド サービスの両方をリビルドします。両方のプロジェクトがエラーなしでビルドされることを確認します。


	
7. **F5** キーを押して、クライアント アプリケーションとサービスをローカルで実行します。いくつかの新しい項目を追加し、いくつかの項目に完了マークを付けて、**CompleteDate** タイムスタンプが更新されていることを確認します。


8. Visual Studio のソリューション エクスプローラーで、todolist サービス プロジェクトを右クリックし、**[発行]** をクリックします。Azure ポータルからダウンロードした発行設定ファイルを使用して、.NET バックエンド サービスを Microsoft Azure に発行します。

9. モバイル サービス アドレスへの接続をコメント解除して、クライアント プロジェクトに対応する App.xaml.cs ファイルを更新します。Azure アカウント内でホストされている .NET バックエンドに対してアプリケーションをテストします。


## <a name="next-steps"></a>次のステップ

このチュートリアルが完了したため、データ シリーズの、[ページングを使用したモバイル サービス クエリの改善]に関する最終チュートリアルに進むことを検討してください。:

サーバー スクリプトは、ユーザーを認証するときに、およびプッシュ通知の送信のためにも使用されます。詳細については、次のチュートリアルを参照してください。

* [ユーザーのサービス側の承認]
  <br/>認証されたユーザーの ID に基づきデータをフィルター処理する方法について説明します。

* [プッシュ通知の使用]
  <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

* [Mobile Services .NET の使用方法の概念リファレンス]
  <br/>.NET で Mobile Services を使用する方法について説明します。

<!-- Anchors. -->
[文字列の長さの検証の追加]: #string-length-validation
[検証をサポートするためのクライアントの更新]: #update-client-validation
[長さの検証テスト]: #test-length-validation
[CompleteDate に関するタイムスタンプの追加]: #add-timestamp
[CompleteDate を表示するためのクライアントの更新]: #update-client-timestamp
[次のステップ]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-dotnet-backend-windows-phone-validate-modify-data/mobile-services-invalid-text-length.png
[2]: ./media/mobile-services-dotnet-backend-windows-phone-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png



<!-- URLs. -->
[モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started/#create-new-service
[ユーザーのサービス側の承認]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts/
[ページングを使用したモバイル サービス クエリの改善]: /ja-jp/develop/mobile/tutorials/add-paging-to-data-dotnet
[作業の開始]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
[プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/
[JavaScript と HTML]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-js

[管理ポータル]: https://manage.windowsazure.com/
[Azure の管理ポータル]: https://manage.windowsazure.com/
[Mobile Services .NET の使用方法の概念リファレンス]: /ja-jp/develop/mobile/how-to-guides/work-with-net-client-library



<!--HONumber=42-->
