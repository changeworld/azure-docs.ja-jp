<properties 
	pageTitle="データの使用 (Android) | モバイル デベロッパー センター" 
	description="モバイル サービスを使用して Android アプリでデータを活用する方法について説明します。" 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="02/03/2015" 
	ms.author="ricksal,glenga"/>

# 既存のアプリにモバイル サービスを追加する

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

## まとめ

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>このトピックでは、Azure のモバイル サービスを使用して Android アプリのデータを活用する方法について説明します。このチュートリアルでは、メモリにデータを格納するアプリケーションをダウンロードした後、新しいモバイル サービスを作成してそれをアプリケーションに統合します。その後、Azure 管理ポータルにログインして、アプリケーションの実行中にデータに加えられた変更を表示します。</p>

</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">チュートリアルを見る</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-data-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">ビデオを再生する</span></a><span class="time">15:32</span></div>
</div>

<p>このチュートリアルの目的は、モバイル サービスが Android アプリのデータを Azure に格納と取得できるようにするしくみを説明することにあります。したがって、このトピックでは、モバイル サービスのクイック スタートで完了している手順の多くについても説明します。モバイル サービスを初めて使用する場合は、最初にチュートリアル「 <a href="/develop/mobile/tutorials/get-started-android">Mobile Services の使用</a>」で説明されている手順を実行する必要があります。</p>

> [AZURE.NOTE] 完成したアプリケーションのソース コードを表示する場合は、 <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStartedWithData/Android/GetStartedWithData">ここ</a>をクリックしてください。

このチュートリアルの Eclipse バージョンを確認する場合は、 
「[Get started with data using Eclipse (Eclipse でデータを使用する)](mobile-services-android-get-started-data-EC.md)」をご覧ください。

> [AZURE.NOTE] このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成できます。詳細については、「 <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AED8DE357" target="_blank">Azure 無料評価版</a>」をご覧ください。

このチュートリアルでは、[モバイル サービス Android SDK]、 <a  href="https://developer.android.com/sdk/index.html" target="_blank">Android SDK を含む Android Studio 統合開発環境</a>、Android 4.2 以降のバージョンが必要です。 

> [AZURE.NOTE] このチュートリアルでは、Android SDK とモバイル サービス Android SDK の両方をインストールする手順について説明します。ダウンロードした GetStartedWithData プロジェクトを実行するには、Android 4.2 以降のバージョンが必要です。ただし、Mobile Services SDK は、Android 2.2 以降で動作します。
<!-- -->

> [AZURE.NOTE] このチュートリアルでは、Mobile Services SDK の最新バージョンを使用します。互換性を確保する目的で <a href="http://go.microsoft.com/fwlink/p/?LinkID=280126"> ここ</a> から以前のバージョンを入手できますが、これらのチュートリアルに含まれているコードは以前のバージョンでは動作しません。

<h2><a name="download-app"></a>GetStartedWithData プロジェクトをダウンロードする</h2>

### サンプル コードの取得

[AZURE.INCLUDE [download-android-sample-code](../includes/download-android-sample-code.md)]

### Android SDK バージョンの検証

[AZURE.INCLUDE [Verify SDK](../includes/mobile-services-verify-android-sdk-version.md)]


### サンプル コードの確認と実行

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../includes/mobile-services-android-run-sample-code.md)]

<h2><a name="create-service"></a>管理ポータルで新規モバイル サービスを作成する</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>新しいテーブルをモバイル サービスに追加する</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

<h2><a name="update-app"></a>モバイル サービスをデータ アクセスに使用するためにアプリケーションを更新する</h2>

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../includes/mobile-services-android-getting-started-with-data.md)]


<h2><a name="test-app"></a>新しいモバイル サービスに対するアプリケーションのテスト</h2>

バック エンド ストレージのモバイル サービスを使用するようにアプリケーションを更新した後は、Android エミュレーターまたは Android フォンを使用して、モバイル サービスに対してアプリケーションをテストします。

1. **[実行]** メニューの **[Run app (アプリを実行)]** をクリックして、プロジェクトを開始します。

	これにより、モバイル サービスから項目を取得するクエリを、クライアント ライブラリを使用して送信するアプリケーションが Android SDK でビルドされ、実行されます。

5. 前回と同様に、意味のあるテキストを入力し、**[Add]** をクリックします。

   	これにより、新しい項目が挿入としてモバイル サービスに送信されます。

3. [管理ポータル]で、**[モバイル サービス]** をクリックし、対象のモバイル サービスをクリックします。

4. **[データ]** タブをクリックし、**[参照]** をクリックします。

   	![][9]
  
   	**TodoItem** テーブルに、モバイル サービスによって生成されたいくつかの値を持つデータが含まれ、アプリケーションの TodoItem クラスに対応してその列が自動的にテーブルに追加されていることに注目してください。

これで、Android 向けの**データの使用**に関するチュートリアルは終了です。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、Android アプリでモバイル サービスのデータを操作できるようにするための基本について説明しました。 

次は、このチュートリアルで作成した GetStartedWithData アプリケーションに基づく次のいずれかのチュートリアルを行うことをお勧めします。

* [スクリプトを使用したデータの検証と変更]
  <br/>モバイル サービスでサーバー スクリプトを使用して、アプリケーションから送信されたデータを検証と変更する方法について説明します。

* [ページングを使用したクエリの改善]
  <br/>クエリ内でページングを使用して、単一の要求で渡されるデータの量を制御する方法について説明します。

データ シリーズを完了した後は、次に示す、Android に関する他のチュートリアルを行うことをお勧めします。

* [認証の使用] 
	<br/>アプリケーションのユーザーを認証する方法について説明します。

* [プッシュ通知の使用] 
  <br/>モバイル サービスを使用してアプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

<!-- Anchors. -->
[Android アプリ プロジェクトのダウンロード]: #download-app
[モバイル サービスの作成]: #create-service
[ストレージへのデータ テーブルの追加]: #add-table
[モバイル サービスを使用するためのアプリケーションの更新]: #update-app
[モバイル サービスに対するアプリケーションのテスト]: #test-app
[次のステップ]:#next-steps

<!-- Images. -->
[8]: ./media/mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
[12]: ./media/mobile-services-android-get-started-data/mobile-eclipse-project.png
[13]: ./media/mobile-services-android-get-started-data/mobile-quickstart-startup-android.png
[14]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-project.png


<!-- URLs. -->
[スクリプトを使用したデータの検証と変更]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[ページングを使用したクエリの改善]: /develop/mobile/tutorials/add-paging-to-data-android
[Mobile Services の使用]: /develop/mobile/tutorials/get-started-android
[データの使用]: /develop/mobile/tutorials/get-started-with-data-android
[データの使用 (Eclipse)]: /documentation/articles/mobile-services-android-get-started-data-EC/
[認証の使用]: /develop/mobile/tutorials/get-started-with-users-android
[プッシュ通知の使用]: /develop/mobile/tutorials/get-started-with-push-android

[Azure 管理ポータル]: https://manage.windowsazure.com/
[管理ポータル]: https://manage.windowsazure.com/
[モバイル サービス Android SDK]: http://aka.ms/Iajk6q
[GitHub]:  http://go.microsoft.com/fwlink/p/?LinkID=282122
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125

<!--HONumber=47-->
