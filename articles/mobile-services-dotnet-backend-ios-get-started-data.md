<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-ios" urlDisplayName="Get Started with Data" pageTitle="Get started with data (iOS) | Mobile Dev Center" metaKeywords="Azure iOS data, Azure mobile services data, " description="Learn how to get started using Mobile Services to leverage data in your iOS app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# モバイル サービスでのデータの使用

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title="Windows ストア JavaScript">Windows ストア JavaScript</a><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/" title="Windows Phone">Windows Phone</a><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/" title="iOS" class="current">iOS</a><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/" title="Android" class="current">Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/" title=".NET バックエンド" class="current">.NET バックエンド</a> |  <a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-ios/"  title="JavaScript バックエンド">JavaScript バックエンド</a></div>

このトピックでは、Azure Mobile Services を使用して iOS アプリケーションのデータを活用する方法について説明します。このチュートリアルでは、メモリにデータを格納するアプリケーションをダウンロードした後、新しいモバイル サービスを作成してそれをアプリケーションに統合し、アプリケーションの実行時にデータに加えられた変更を表示します。

このチュートリアルで作成するモバイル サービスは、Mobile Services の .NET ランタイムをサポートします。これによって、モバイル サービスのサーバー側のビジネス ロジックに .NET 言語と Visual Studio を使用できます。JavaScript でサーバー側ビジネス ロジックを記述できるモバイル サービスを作成する方法については、このトピックの「[JavaScript バックエンド バージョン][JavaScript バックエンド バージョン]」を参照してください。

<div class="dev-callout"><b>注</b>
<p>このチュートリアルには、Visual Studio 2013 が必要です。</p>
</div>

<div class="dev-callout"><b>注</b>
<p>このチュートリアルの目的は、モバイル サービスが iOS アプリケーションのデータを Azure に格納および取得できるようにするしくみを説明することにあります。したがって、このトピックでは、モバイル サービスのクイック スタートで完了している手順の多くについても説明します。モバイル サービスを初めて使用する場合は、最初にチュートリアル「<a href="/ja-jp/develop/mobile/tutorials/get-started-ios">モバイル サービスの使用</a>」を完了することをお勧めします。</p>
</div>

このチュートリアルでは、次の基本的な手順について説明します。

1.  [iOS アプリ プロジェクトのダウンロード][iOS アプリ プロジェクトのダウンロード]
2.  [モバイル サービスの作成][モバイル サービスの作成]
3.  [サービスをローカルにダウンロードする][サービスをローカルにダウンロードする]
4.  [モバイル サービスをテストする][モバイル サービスをテストする]
5.  [モバイル サービスを Azure に発行する][モバイル サービスを Azure に発行する]
6.  [モバイル サービスを使用するためのアプリケーションの更新][モバイル サービスを使用するためのアプリケーションの更新]
7.  [モバイル サービスに対するアプリケーションのテスト][モバイル サービスに対するアプリケーションのテスト]

このチュートリアルには、次のものが必要です。

-   [モバイル サービス iOS SDK][モバイル サービス iOS SDK] と [XCode 4.5][XCode 4.5] および iOS 5.0 以降のバージョン。
-   Visual Studio 2013 (無償版の [Visual Studio Express for Web][Visual Studio Express for Web] が入手可能)。
-   Microsoft Azure アカウント。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト][Azure の無料評価版サイト]を参照してください。

## <a name="download-app"></a>GetStartedWithData プロジェクトをダウンロードする

このチュートリアルは、iOS アプリの [GetStartedWithData][GetStartedWithData] アプリケーションに基づいています。このアプリケーションの UI は、追加された項目がメモリにローカルに格納される点を除き、モバイル サービス iOS のクイック スタートで生成したアプリケーションと同じです。

1.  GetStartedWithData [サンプル アプリケーション][GetStartedWithData]をダウンロードします。

2.  Xcode で、ダウンロードしたプロジェクトを開き、TodoService.m ファイルを確認します。

    8 つの **// TODO** コメントに、このアプリケーションをモバイル サービスで実行するために必要な手順が指定されている点に注目してください。

3.  **[Run]** ボタン (または Command + R キー) を押してプロジェクトをリビルドし、アプリケーションを起動します。

4.  アプリケーションで、テキスト ボックスにテキストを入力し、**[+]** をクリックします。

    ![][0]

    保存されたテキストが下のリストに表示されます。

## <a name="create-service"></a>管理ポータルでの新しいモバイル サービスの作成

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## <a name="download-the-service-locally"></a>ローカル コンピューターにサービスをダウンロードする

[WACOM.INCLUDE [mobile-services-ios-download-service-locally](../includes/mobile-services-ios-download-service-locally.md)]

## <a name="test-the-service"></a> モバイル サービスをテストする

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## <a name="publish-mobile-service"></a>モバイル サービスを Azure に発行する

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## <a name="update-app"></a>モバイル サービスをデータ アクセスに使用するためにアプリケーションを更新する

[WACOM.INCLUDE [mobile-services-ios-enable-mobile-service-access](../includes/mobile-services-ios-enable-mobile-service-access.md)]

## <a name="test-app"></a>新しいモバイル サービスに対するアプリケーションのテスト

1.  Xcode で、iPhone または iPad にデプロイするエミュレーターを選択し、**[Run]** ボタン (または Command + R キー) を押してプロジェクトをリビルドし、アプリケーションを起動します。

    これにより、モバイル サービスから項目にクエリを行う Azure Mobile Services クライアントが、iOS SDK でビルドされ、実行されます。

2.  前と同様に、テキスト ボックスにテキストを入力し、**[+]** をクリックします。

    これにより、新しい項目が挿入としてモバイル サービスに送信されます。それぞれの新しい todoItem は、Azure 管理ポータルでモバイル サービスに以前に構成した SQL データベースで保存および更新されます。

3.  アプリケーションを停止してから再起動して、変更内容が Azure のデータベースに保持されたことを確認できます。

    また、Azure 管理ポータルまたは Visual Studio の SQL Server オブジェクト エクスプ ローラーを使用して、データベースを確認することもできます。次の 2 つのステップでは、[Azure 管理ポータル][Azure 管理ポータル]を使用してデータベースの変更を表示します。

4.  Azure の管理ポータルで、モバイル サービスに関連付けられたデータベースの [管理] をクリックします。

    ![][1]

5.  管理ポータルでクエリを実行して、アプリケーションによって加えられた変更を表示します。クエリは次のようになりますが、`todolist` の代わりにデータベースの名前を使用します。

        SELECT * FROM [todolist].[todoitems]

    ![][2]

これで、**データの使用**に関するチュートリアルはおしまいです。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、iOS アプリでモバイル サービスのデータを操作できるようにするための基本について説明しました。

次は、このチュートリアルで作成した GetStartedWithData アプリケーションに基づく次のいずれかのチュートリアルを行うことをお勧めします。

-   [サーバー スクリプトを使用したモバイル サービスのデータの検証および変更][サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]<br/>
    Mobile Services でサーバー スクリプトを使用して、アプリケーションから送信されたデータを検証および変更する方法について説明します。

-   [ページングを使用したモバイル サービス クエリの改善][ページングを使用したモバイル サービス クエリの改善]<br/>
    クエリ内でページングを使用して、1 回の要求で渡されるデータの量を制御する方法について説明します。

データ シリーズを完了した後は、iOS に関する他のチュートリアルを参照してください。

-   [認証の使用][認証の使用]<br/>
    アプリケーションのユーザーを認証する方法について説明します。

-   [プッシュ通知の使用][プッシュ通知の使用]<br/>
    Mobile Services を使用してアプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。



  [JavaScript バックエンド バージョン]: /ja-jp/develop/mobile/tutorials/get-started-with-data-ios
  [iOS アプリ プロジェクトのダウンロード]: #download-app
  [モバイル サービスの作成]: #create-service
  [サービスをローカルにダウンロードする]: #download-the-service-locally
  [モバイル サービスをテストする]: #test-the-service
  [モバイル サービスを Azure に発行する]: #publish-mobile-service
  [モバイル サービスを使用するためのアプリケーションの更新]: #update-app
  [モバイル サービスに対するアプリケーションのテスト]: #test-app
  [モバイル サービス iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Visual Studio Express for Web]: http://go.microsoft.com/p/?linkid=9832232
  [Azure の無料評価版サイト]: http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F
  [GetStartedWithData]: http://go.microsoft.com/fwlink/p/?LinkId=268622
  [0]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-quickstart-startup-ios.png
  [mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
  [mobile-services-ios-download-service-locally]: ../includes/mobile-services-ios-download-service-locally.md
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [mobile-services-ios-enable-mobile-service-access]: ../includes/mobile-services-ios-enable-mobile-service-access.md
  [Azure 管理ポータル]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-dotnet-backend-ios-get-started-data/manage-sql-azure-database.png
  [2]: ./media/mobile-services-dotnet-backend-ios-get-started-data/sql-azure-query.png
  [サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [ページングを使用したモバイル サービス クエリの改善]: /ja-jp/develop/mobile/tutorials/add-paging-to-data-ios
  [認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-ios
  [プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-ios
