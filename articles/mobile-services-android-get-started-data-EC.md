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
	ms.date="02/06/2015" 
	ms.author="ricksal,glenga"/>

# 既存のアプリにモバイル サービスを追加する

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data-EC.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>このトピックでは、Azure のモバイル サービスを使用して Android アプリのデータを活用する方法について説明します。このチュートリアルでは、メモリにデータを格納するアプリケーションをダウンロードした後、新しいモバイル サービスを作成してそれをアプリケーションに統合します。その後、Azure 管理ポータルにログインして、アプリケーションの実行中にデータに加えられた変更を表示します。</p>

</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">チュートリアルを見る</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-data-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">ビデオを再生する</span></a><span class="time">15:32</span></div>
</div>

> [AZURE.NOTE] このチュートリアルの目的は、モバイル サービスが Android アプリのデータを Azure に格納および取得できるようにするしくみを説明することにあります。したがって、このトピックでは、モバイル サービスのクイック スタートで完了している手順の多くについても説明します。モバイル サービスを初めて使用する場合は、最初にチュートリアル「[モバイル サービスの使用](/develop/mobile/tutorials/get-started-android-EC)」を完了することをお勧めします。
> 
> 完成したアプリケーションのソース コードは、[ここ](https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStartedWithData/Android/GetStartedWithData)で確認できます。


このチュートリアルでは、次の基本的な手順について説明します。

1. [Android アプリ プロジェクトのダウンロード]
2. [モバイル サービスの作成]
3. [ストレージへのデータ テーブルの追加]
4. [モバイル サービスを使用するためのアプリケーションの更新]
5. [モバイル サービスに対するアプリケーションのテスト]

> [AZURE.IMPORTANT] このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト](http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AED8DE357"%20target="_blank)をご覧ください。 

このチュートリアルを実行するには、[Mobile Services Android SDK]、Eclipse 統合開発環境 (IDE) と Android Developer Tools (ADT) プラグインを含む <a href="https://go.microsoft.com/fwLink/p/?LinkID=280125" target="_blank">Android SDK</a>、Android 4.2 以降のバージョンが必要です。 

> [AZURE.NOTE] このチュートリアルでは、Android SDK とモバイル サービス Android SDK の両方をインストールする手順について説明します。ダウンロードした GetStartedWithData プロジェクトを実行するには、Android 4.2 以降のバージョンが必要です。ただし、Mobile Services SDK は、Android 2.2 以降で動作します。

<!-- -->

> [AZURE.NOTE] このチュートリアルでは、Mobile Services SDK の最新バージョンを使用します。互換性を確保する目的で<a href="http://go.microsoft.com/fwlink/p/?LinkID=280126">ここ</a>から以前のバージョンを入手できますが、これらのチュートリアルに含まれているコードは以前のバージョンでは動作しません。

<h2><a name="download-app"></a>GetStartedWithData プロジェクトをダウンロードする</h2>

### サンプル コードの取得

[AZURE.INCLUDE [download-android-sample-code](../includes/download-android-sample-code-EC.md)]

### Android SDK バージョンの検証

[AZURE.INCLUDE [Verify SDK](../includes/mobile-services-verify-android-sdk-version-EC.md)]


### サンプル コードの確認と実行

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../includes/mobile-services-android-run-sample-code-EC.md)]

<h2><a name="create-service"></a>管理ポータルで新規モバイル サービスを作成する</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>新しいテーブルをモバイル サービスに追加する</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

<h2><a name="update-app"></a>モバイル サービスをデータ アクセスに使用するためにアプリケーションを更新する</h2>

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../includes/mobile-services-android-getting-started-with-data-EC.md)]


<h2><a name="test-app"></a>新しいモバイル サービスに対するアプリケーションのテスト</h2>

バック エンド ストレージのモバイル サービスを使用するようにアプリケーションを更新した後は、Android エミュレーターまたは Android フォンを使用して、モバイル サービスに対してアプリケーションをテストします。

1. **[実行]** メニューの **[実行]** をクリックして、プロジェクトを開始します。

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
  <br/>モバイル サービスでサーバー スクリプトを使用して、アプリケーションから送信されたデータを検証および変更する方法について説明します。

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
[モバイル サービスの使用]: /develop/mobile/tutorials/get-started-android
[データの使用]: /develop/mobile/tutorials/get-started-with-data-android
[認証の使用]: /develop/mobile/tutorials/get-started-with-users-android
[プッシュ通知の使用]: /develop/mobile/tutorials/get-started-with-push-android

[Azure 管理ポータル]: https://manage.windowsazure.com/
[管理ポータル]: https://manage.windowsazure.com/
[Mobile Services Android SDK]: http://aka.ms/Iajk6q
[GitHub]:  http://go.microsoft.com/fwlink/p/?LinkID=282122
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125

<!--HONumber=47-->
