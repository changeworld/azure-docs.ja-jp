<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-android" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Android app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# モバイル サービスでのデータの使用

<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title="Windows ストア C#">Windows ストア C#</a>
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title="Windows ストア JavaScript">Windows ストア JavaScript</a>
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/" title="Windows Phone">Windows Phone</a>
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/" title="iOS">iOS</a>
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/" title="Android" class="current">Android</a>
</div>

<div class="dev-center-tutorial-subselector">
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/" title=".NET バックエンド" class="current">.NET バックエンド</a> | 
<a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-android/"  title="JavaScript バックエンド">JavaScript バックエンド</a>
</div>

このトピックでは、Azure Mobile Services を Android アプリのバックエンド データソースとして使用する方法について説明します。このチュートリアルでは、新しいモバイル サービスの作成、メモリにデータを格納するアプリケーション用の Eclipse Android のダウンロード、モバイル サービスとアプリケーションとの統合、アプリケーションの実行時にデータに加えられた変更の表示について説明します。

このチュートリアルで作成するモバイル サービスは、Mobile Services の .NET ランタイムをサポートします。これによって、モバイル サービスのサーバー側のビジネス ロジックに .NET 言語と Visual Studio を使用できます。JavaScript でサーバー側ビジネス ロジックを記述できるモバイル サービスを作成する方法については、このトピックの「[JavaScript バックエンド バージョン][JavaScript バックエンド バージョン]」を参照してください。

<div class="dev-callout"><b>注</b>
<p>このチュートリアルには、Visual Studio 2013 が必要です。</p>
</div>

このチュートリアルでは、次の基本的な手順について説明します。

1.  [新しいモバイル サービスを作成する][新しいモバイル サービスを作成する]
2.  [サービスをローカルにダウンロードする][サービスをローカルにダウンロードする]
3.  [モバイル サービスをテストする][モバイル サービスをテストする]
4.  [モバイル サービスを Azure に発行する][モバイル サービスを Azure に発行する]
5.  [GetStartedWithData プロジェクトをダウンロードする][GetStartedWithData プロジェクトをダウンロードする]
6.  [モバイル サービスをデータ アクセスに使用するためにアプリケーションを更新する][モバイル サービスをデータ アクセスに使用するためにアプリケーションを更新する]
7.  [発行されたモバイル サービスに対してアプリケーションをテストする][発行されたモバイル サービスに対してアプリケーションをテストする]

<div class="dev-callout"><strong>注</strong> <p>このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F" target="_blank">Azure の無料評価版サイト</a>を参照してください。</p></div>

## <a name="create-service"></a><span class="short-header">新しいモバイル サービスの作成</span>新しいモバイル サービスを作成する

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## <a name="download-the-service"></a><span class="short-header">サービスのダウンロード</span>サービスをローカル コンピューターにダウンロードする

[WACOM.INCLUDE [mobile-services-download-service-locally](../includes/mobile-services-download-service-locally.md)]

## <a name="test-the-service"></a><span class="short-header">サービスのテスト</span>モバイル サービスをテストする

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## <a name="publish-the-service"></a><span class="short-header">サービスの発行</span>モバイル サービスを Azure に発行する

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## <a name="download-app"></a><span class="short-header">プロジェクトのダウンロード</span>GetStartedWithData プロジェクトのダウンロード

### サンプル コードの取得

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

### Android SDK バージョンの検証

[WACOM.INCLUDE [mobile-services-verify-android-sdk-version](../includes/mobile-services-verify-android-sdk-version.md)]

### サンプル コードの確認と実行

[WACOM.INCLUDE [mobile-services-android-run-sample-code](../includes/mobile-services-android-run-sample-code.md)]

## <a name="update-app"></a><span class="short-header">アプリケーションの更新</span>モバイル サービスをデータ アクセスに使用するためのアプリケーションの更新

[WACOM.INCLUDE [mobile-services-android-getting-started-with-data](../includes/mobile-services-android-getting-started-with-data.md)]

## <a name="test-app"></a><span class="short-header">アプリケーションのテスト</span>発行されたモバイル サービスに対してアプリケーションをテストする

バック エンド ストレージのモバイル サービスを使用するようにアプリケーションを更新した後は、Android エミュレーターまたは Android フォンを使用して、モバイル サービスに対してアプリケーションをテストします。

1.  **[Run]** メニューの **[Run]** をクリックして、プロジェクトを開始します。

    これにより、モバイル サービスから項目を取得するクエリを、クライアント ライブラリを使用して送信するアプリケーションが Android SDK でビルドされ、実行されます。

2.  前回と同様に、意味のあるテキストを入力し、**[Add]** をクリックします。

    これにより、新しい項目が挿入としてモバイル サービスに送信されます。

    アプリケーションを再起動して、変更内容が Azure のデータベースに保持されたことを確認できます。Azure の管理ポータルを使用してデータベースを確認することもできます。次の 2 つのステップでは、データベースの変更を表示します。

3.  Azure の管理ポータルで、モバイル サービスに関連付けられたデータベースの [管理] をクリックします。

    ![][0]

4.  管理ポータルで、クエリを実行して Windows ストア アプリによって加えられた変更を表示します。クエリは次のようになりますが、`todolist` の代わりにデータベースの名前を使用します。

        SELECT * FROM [todolist].[todoitems]

    ![][2]

これで、Android 向けの**データの使用**に関するチュートリアルは終了です。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、Android アプリでモバイル サービスのデータを操作できるようにするための基本について説明しました。

<!--Next, consider completing one of the following tutorials that is based on the GetStartedWithData app that you created in this tutorial:  * [Validate and modify data with scripts]   <br/>Learn more about using server scripts in Mobile Services to validate and change data sent from your app.  * [Refine queries with paging]   <br/>Learn how to use paging in queries to control the amount of data handled in a single request.  Once you have completed the data series, try -->

他のチュートリアルを 1 つ試してください。

-   [認証の使用][認証の使用]
    アプリケーションのユーザーを認証する方法について説明します。

-   [プッシュ通知の使用][プッシュ通知の使用]
    アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

-   [モバイル サービス .NET の使用方法の概念リファレンス][モバイル サービス .NET の使用方法の概念リファレンス]
    .NET で Mobile Services を使用する方法について説明します。

 

 



  [JavaScript バックエンド バージョン]: /ja-jp/develop/mobile/tutorials/get-started-with-data-android
  [新しいモバイル サービスを作成する]: #create-service
  [サービスをローカルにダウンロードする]: #download-the-service-locally
  [モバイル サービスをテストする]: #test-the-service
  [モバイル サービスを Azure に発行する]: #publish-mobile-service
  [GetStartedWithData プロジェクトをダウンロードする]: #download-app
  [モバイル サービスをデータ アクセスに使用するためにアプリケーションを更新する]: #update-app
  [発行されたモバイル サービスに対してアプリケーションをテストする]: #test-app
  [0]: ./media/mobile-services-dotnet-backend-android-get-started-data/manage-sql-azure-database.png
  [2]: ./media/mobile-services-dotnet-backend-android-get-started-data/sql-azure-query.png
  [認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-android
  [プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-android
  [モバイル サービス .NET の使用方法の概念リファレンス]: /ja-jp/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
