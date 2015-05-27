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
	ms.date="05/04/2015" 
	ms.author="ricksal"/>

# 既存のアプリケーションへの Mobile Services の追加

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

## 概要

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>このトピックでは、Azure のモバイル サービスを使用して Android アプリのデータを活用する方法について説明します。このチュートリアルでは、メモリにデータを格納するアプリケーションをダウンロードした後、新しいモバイル サービスを作成してそれをアプリケーションに統合します。その後、Azure 管理ポータルにサインインして、アプリケーションの実行中にデータに加えられた変更を表示します。</p>

</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">チュートリアルを見る</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-data-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">ビデオを再生する</span></a><span class="time">15:32:00</span></div>
</div>

このチュートリアルの目的は、モバイル サービスが Android アプリのデータを Azure に格納および取得できるようにするしくみを説明することにあります。したがって、このトピックでは、モバイル サービスのクイック スタートで完了している手順の多くについても説明します。モバイル サービスを初めて使用する場合は、最初にチュートリアル「[モバイル サービスの使用](mobile-services-android-get-started.md)」を完了することをお勧めします。

> [AZURE.NOTE]完成したアプリケーションのソース コードは、[ここ](https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStartedWithData/Android/GetStartedWithData)で確認できます。

このチュートリアルの Eclipse バージョンを確認する場合は、「[Eclipse を使用したデータの使用](mobile-services-android-get-started-data-EC.md)」をご覧ください。

このチュートリアルを完了するには、以下が必要です。

+ Azure アカウント。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、「<a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AED8DE357" target="_blank">Azure の無料評価版サイト</a>」をご覧ください。
 
+ [Mobile Services Android SDK]、Android SDK を含む <a  href="https://developer.android.com/sdk/index.html" target="_blank">Android Studio 統合開発環境</a>、Android 4.2 以降のバージョン。

> [AZURE.NOTE]このチュートリアルでは、Android SDK とモバイル サービス Android SDK の両方をインストールする手順について説明します。ダウンロードした GetStartedWithData プロジェクトを実行するには、Android 4.2 以降のバージョンが必要です。ただし、Mobile Services SDK は、Android 2.2 以降で動作します。<!-- -->

> [AZURE.NOTE]このチュートリアルでは、Mobile Services SDK の最新バージョンを使用します。互換性を確保する目的で<a href="http://go.microsoft.com/fwlink/p/?LinkID=280126">ここ</a>から以前のバージョンを入手できますが、これらのチュートリアルに含まれているコードは以前のバージョンでは動作しません。

##<a name="download-app"></a>GetStartedWithData プロジェクトをダウンロードする

###サンプル コードの取得

[AZURE.INCLUDE [download-android-sample-code](../includes/download-android-sample-code.md)]

###Android SDK バージョンの検証

[AZURE.INCLUDE [SDK の確認](../includes/mobile-services-verify-android-sdk-version.md)]


###サンプル コードの確認と実行

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../includes/mobile-services-android-run-sample-code.md)]

##<a name="create-service"></a>管理ポータルで新規モバイル サービスを作成する

[AZURE.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

##<a name="add-table"></a>新しいテーブルをモバイル サービスに追加する

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

##<a name="update-app"></a>モバイル サービスをデータ アクセスに使用するためにアプリケーションを更新する

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../includes/mobile-services-android-getting-started-with-data.md)]


##<a name="test-app"></a>新しいモバイル サービスに対するアプリケーションのテスト

バック エンド ストレージのモバイル サービスを使用するようにアプリケーションを更新した後は、Android エミュレーターまたは Android フォンを使用して、モバイル サービスに対してアプリケーションをテストします。

1. **[Run]** メニューの **[Run app]** をクリックして、プロジェクトを開始します。

	これにより、モバイル サービスから項目を取得するクエリを、クライアント ライブラリを使用して送信するアプリケーションが Android SDK でビルドされ、実行されます。

5. 前回と同様に、意味のあるテキストを入力し、**[Add]** をクリックします。

   	これにより、新しい項目が挿入としてモバイル サービスに送信されます。

3. [管理ポータル]で、**[モバイル サービス]** をクリックし、対象のモバイル サービスをクリックします。

4. **[データ]** タブをクリックし、**[参照]** をクリックします。

   	![][9]
  
   	**TodoItem** テーブルに、モバイル サービスによって生成されたいくつかの値を持つデータが含まれ、アプリケーションの TodoItem クラスに対応してその列が自動的にテーブルに追加されていることに注目してください。

これで、Android 向けの**データの使用**に関するチュートリアルは終了です。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、Android アプリでモバイル サービスのデータを操作できるようにするための基本について説明しました。他の Android チュートリアルで以下を試します。

* [認証の使用] <br/>アプリケーションのユーザーを認証する方法について説明します。

* [プッシュ通知の使用] <br/>Mobile Services を使用してアプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

<!-- Anchors. -->
[Download the Android app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[8]: ./media/mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
[12]: ./media/mobile-services-android-get-started-data/mobile-eclipse-project.png
[13]: ./media/mobile-services-android-get-started-data/mobile-quickstart-startup-android.png
[14]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-project.png


<!-- URLs. -->
[Get started with data (Eclipse)]: mobile-services-android-get-started-data-EC.md
[認証の使用]: mobile-services-android-get-started-users.md
[プッシュ通知の使用]: mobile-services-javascript-backend-android-get-started-push.md

[Azure Management Portal]: https://manage.windowsazure.com/
[管理ポータル]: https://manage.windowsazure.com/
[Mobile Services Android SDK]: http://aka.ms/Iajk6q
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkID=282122
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125

<!--HONumber=54-->