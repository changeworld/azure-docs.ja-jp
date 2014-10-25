<properties linkid="mobile-services-windows-store-dotnet-get-started-data" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/19/2014" ms.author="glenga"></tags>

# モバイル サービスでのデータの使用

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy][mobile-services-selector-get-started-data-legacy]]

<div class="dev-center-tutorial-subselector">
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title=".NET バックエンド">.NET バックエンド</a> | 
<a href="/ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/" title="JavaScript バックエンド" class="current">JavaScript バックエンド</a>
</div>

このトピックでは、Azure のモバイル サービスを使用して Windows ストア アプリケーションのデータを活用する方法について説明します。このチュートリアルでは、メモリにデータを格納するアプリケーションの Visual Studio 2013 プロジェクトをダウンロードした後、新しいモバイル サービスを作成してそれをアプリケーションに統合します。その後、Azure 管理ポータルにサインインして、アプリケーションの実行中にデータに加えられた変更を表示します。

> [WACOM.NOTE]このトピックでは、Visual Studio 2013 を使用して Azure モバイル サービスを Windows ストア プロジェクトに追加する方法について説明します。ユニバーサル Windows アプリ プロジェクトに、同じ JavaScript バックエンド モバイル サービスを追加できます。詳細については、このチュートリアルの[ユニバーサル Windows アプリ バージョン][ユニバーサル Windows アプリ バージョン]を参照してください。

このチュートリアルでは、次の基本的な手順について説明します。

1.  [Windows ストア アプリ プロジェクトのダウンロード][Windows ストア アプリ プロジェクトのダウンロード]
2.  [Visual Studio からのモバイル サービスの作成][Visual Studio からのモバイル サービスの作成]
3.  [ストレージのデータ テーブルの追加][ストレージのデータ テーブルの追加]
4.  [モバイル サービスを使用するためのアプリケーションの更新][モバイル サービスを使用するためのアプリケーションの更新]
5.  [モバイル サービスに対するアプリケーションのテスト][モバイル サービスに対するアプリケーションのテスト]

このチュートリアルを完了するには、以下が必要です。

-   アクティブな Azure アカウントアカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト][Azure の無料評価版サイト]を参照してください。
-   Visual Studio 2013。Windows ストア アプリをモバイル サービスに簡単に接続できる機能を備えています。

## <a name="download-app"></a>GetStartedWithData プロジェクトのダウンロード

このチュートリアルは、Visual Studio 2013 の Windows ストア アプリ プロジェクトの [GetStartedWithMobileServices アプリケーション][GetStartedWithMobileServices アプリケーション]に基づいています。このアプリケーションの UI は、追加された項目がメモリにローカルに格納される点を除き、モバイル サービスのクイック スタートで生成したアプリケーションと同じです。

1.  C# バージョンの GetStartedWithMobileServices サンプル アプリケーションを[デベロッパー サンプル コード集のサイト][GetStartedWithMobileServices アプリケーション]からダウンロードします。

2.  Visual Studio 2013 で、ダウンロードしたプロジェクトを開き、MainPage.xaml.cs ファイルを確認します。

    追加された **TodoItem** オブジェクトはメモリ内の **ObservableCollection\<TodoItem\>** に格納されます。

3.  **F5** キーを押してプロジェクトをリビルドし、アプリケーションを開始します。

4.  アプリケーションで、**[Insert a TodoItem]** に任意のテキストを入力し、**[Save]** をクリックします。

    ![][]

    保存したテキストが、**[Query and update data]** の下の 2 番目の列に表示されます。

## <a name="create-service"></a>Visual Studio からの新しいモバイル サービスの作成

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013][mobile-services-create-new-service-vs2013]]

1.  ソリューション エクスプローラーで、App.xaml.cs コード ファイルを開き、\*\*App\*\* クラスに追加された新しい静的フィールドを確認します。次に例を示します。

        public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
                    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
                        "https://todolist.azure-mobile.net/",
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
                

    このコードは、[MobileServiceClient クラス][MobileServiceClient クラス]のインスタンスを使用して、アプリケーションの新しいモバイル サービスへのアクセスを提供します。クライアントは、新しいモバイル サービスの URI とアプリケーション キーを提供することによって作成されます。この静的フィールドは、アプリケーションのすべてのページで使用できます。

## <a name="add-table"></a>データ ストレージへの新しいテーブルの追加

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013][mobile-services-create-new-table-vs2013]]

> [WACOM.NOTE]Id、\_\_createdAt、\_\_updatedAt、および \_\_version の列を持つ新しいテーブルが作成されます。動的スキーマが有効な場合、挿入または更新の要求に含まれる JSON オブジェクトに基づいて、モバイル サービスによって自動的に新しい列が生成されます。詳細については、「[動的スキーマ][動的スキーマ]」を参照してください。

# <a name="update-app"></a>モバイル サービスを使用するためのアプリケーションの更新

[WACOM.INCLUDE [mobile-services-windows-dotnet-update-data-app][mobile-services-windows-dotnet-update-data-app]]

## <a name="test-app"></a>新しいモバイル サービスに対するアプリケーションのテスト

1.  Visual Studio で、F5 キーを押してアプリケーションを実行します。

2.  ここでも、**[Insert a TodoItem]** にテキストを入力し、**[Save]** をクリックします。

    これにより、新しい項目が挿入としてモバイル サービスに送信されます。

3.  [管理ポータル][管理ポータル]で、**[モバイル サービス]** をクリックし、対象のモバイル サービスをクリックします。

4.  **[データ]** タブをクリックし、**[参照]** をクリックします。

    ![][1]

    **TodoItem** テーブルに、モバイル サービスによって生成された ID 値を持つデータが含まれ、アプリケーションの TodoItem クラスに対応してその列が自動的にテーブルに追加されていることに注目してください。

5.  アプリケーションで、リストの項目の 1 つをチェックします。ポータルの参照タブに戻り、**[最新の情報に更新]** をクリックします。

    "complete" 値が **false** から **true** に変更されます。

6.  MainPage.xaml.cs プロジェクト ファイルで、既存の **RefreshTodoItems** メソッドを、完了済み項目をフィルター処理で除外する次のコード行で置き換えます。

        private async void RefreshTodoItems()
        {                       
            // This query filters out completed TodoItems. 
            items = await todoTable
               .Where(todoItem => todoItem.Complete == false)
               .ToCollectionAsync();

            ListItems.ItemsSource = items;            
        }

7.  アプリケーションで、リストの項目をもう 1 つチェックし、**[最新の情報に更新]** をクリックします。

    リスト内のチェックされた項目が非表示になります。最新の情報への更新のたびにモバイル サービスへの往復が発生し、その結果、フィルター処理されたデータが返されます。

これで、**データの使用**に関するチュートリアルはおしまいです。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、Windows ストア アプリでモバイル サービスのデータを操作できるようにするための基本について説明しました。次は、このチュートリアルで作成した GetStartedWithData アプリケーションに基づく次のいずれかのチュートリアルを行うことをお勧めします。

-   [サーバー スクリプトを使用したモバイル サービスのデータの検証および変更][サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]

    Mobile Services でサーバー スクリプトを使用して、アプリケーションから送信されたデータを検証および変更する方法について説明します。

-   [ページングを使用したモバイル サービス クエリの改善][ページングを使用したモバイル サービス クエリの改善]

    クエリ内でページングを使用して、1 回の要求で渡されるデータの量を制御する方法について説明します。

データ シリーズを完了した後は、次に示す他のチュートリアルのいずれかを行うことをお勧めします。

-   [認証の使用][認証の使用]

    アプリケーションのユーザーを認証する方法について説明します。

-   [プッシュ通知の使用][プッシュ通知の使用]

    アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

-   [モバイル サービス .NET の使用方法の概念リファレンス][モバイル サービス .NET の使用方法の概念リファレンス]

    .NET で Mobile Services を使用する方法について説明します。



  [mobile-services-selector-get-started-data-legacy]: ../includes/mobile-services-selector-get-started-data-legacy.md
  [.NET バックエンド]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/ ".NET バックエンド"
  [JavaScript バックエンド]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/ "JavaScript バックエンド"
  [ユニバーサル Windows アプリ バージョン]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data
  [Windows ストア アプリ プロジェクトのダウンロード]: #download-app
  [Visual Studio からのモバイル サービスの作成]: #create-service
  [ストレージのデータ テーブルの追加]: #add-table
  [モバイル サービスを使用するためのアプリケーションの更新]: #update-app
  [モバイル サービスに対するアプリケーションのテスト]: #test-app
  [Azure の無料評価版サイト]: http://azure.microsoft.com/ja-jp/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-jp%2Fdocumentation%2Farticles%2Fmobile-services-windows-store-dotnet-get-started-data%2F
  [GetStartedWithMobileServices アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=328660
  []: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-quickstart-startup.png
  [mobile-services-create-new-service-vs2013]: ../includes/mobile-services-create-new-service-vs2013.md
  [MobileServiceClient クラス]: http://go.microsoft.com/fwlink/p/?LinkId=302030
  [mobile-services-create-new-table-vs2013]: ../includes/mobile-services-create-new-table-vs2013.md
  [動的スキーマ]: http://msdn.microsoft.com/ja-jp/library/windowsazure/jj193175.aspx
  [mobile-services-windows-dotnet-update-data-app]: ../includes/mobile-services-windows-dotnet-update-data-app.md
  [管理ポータル]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-todoitem-data-browse.png
  [サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/
  [ページングを使用したモバイル サービス クエリの改善]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-add-paging-data/
  [認証の使用]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
  [プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/
  [モバイル サービス .NET の使用方法の概念リファレンス]: /ja-jp/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
