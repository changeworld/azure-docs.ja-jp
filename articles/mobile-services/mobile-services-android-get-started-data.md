<properties
	pageTitle="Android のデータの使用 (JavaScript バックエンド) | Microsoft Azure"
	description="Mobile Services を使用して、Android アプリのデータを活用する方法について説明します (JavaScript バックエンド)。"
	services="mobile-services"
	documentationCenter="android"
	authors="RickSaling"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="ricksal"/>

# 既存の Android アプリへの Mobile Services の追加 (JavaScript バックエンド)

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> このトピックの Mobile Apps バージョンについては、「[How to use the Android client library for Mobile Apps (Mobile Apps 向け Android クライアント ライブラリの使用方法)](../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md)」を参照してください。

## 概要

このトピックでは、Azure Mobile Services を使用して Android アプリに永続データを追加する方法について説明します。このチュートリアルでは、メモリにデータを格納するアプリケーションをダウンロードした後、新しいモバイル サービスを作成し、ローカルではなく Azure Mobile Services にデータを格納し、更新できるようにそれをアプリケーションに統合します。その後、Microsoft Azure クラシック ポータルを使用してアプリケーションの実行中にデータに加えられた変更を表示します。

このチュートリアルでは Azure Mobile Services における Android アプリケーションからのデータの保存と取得方法についての詳細を分かりやすく説明します。したがって、Mobile Services のクイック スタート チュートリアルで完了している手順の多くについても説明します。Mobile Services を初めて使用する場合は、最初にチュートリアル「[モバイル サービスの使用](mobile-services-android-get-started.md)」を完了することをお勧めします。

## 前提条件

このチュートリアルを完了するには、以下が必要です。

- Azure アカウント。アカウントがない場合は、無料試用アカウントを数分で作成することができます。詳細については、[Azure の無料試用版サイト](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AED8DE357)を参照してください。


- [Azure Mobile Services Android SDK]
- Android SDK を含む [Android Studio 統合開発環境](https://developer.android.com/sdk/index.html)、Android 4.2 以降のバージョン。ダウンロードした GetStartedWithData プロジェクトを実行するには、Android 4.2 以降のバージョンが必要です。ただし、Mobile Services SDK は、Android 2.2 以降で動作します。

## サンプル コード

完成したソース コードを表示するには、[ここ](https://github.com/Azure/mobile-services-samples/tree/master/GettingStartedWithData/AndroidStudio)に移動します。

## GetStartedWithData プロジェクトをダウンロードする

###サンプル コードの取得

[AZURE.INCLUDE [download-android-sample-code](../../includes/download-android-sample-code.md)]

### サンプル コードの確認と実行

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../../includes/mobile-services-android-run-sample-code.md)]

## Azure クラシック ポータルで新しいモバイル サービスを作成します。

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

## 新しいテーブルをモバイル サービスに追加する

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../../includes/mobile-services-create-new-service-data-2.md)]

## モバイル サービスをデータ アクセスに使用するためにアプリケーションを更新する

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../../includes/mobile-services-android-getting-started-with-data.md)]


## 新しいモバイル サービスに対するアプリケーションのテスト

バック エンド ストレージの Mobile Services を使用するようにアプリケーションを更新した後は、Android エミュレーターまたは Android フォンを使用して、Mobile Services に対してアプリケーションをテストします。

1. **[Run]** メニューの **[Run App]** をクリックして、プロジェクトを開始します。

	これにより、モバイル サービスから項目を取得するクエリを、クライアント ライブラリを使用して送信するアプリケーションが Android SDK でビルドされ、実行されます。

5. 前回と同様に、意味のあるテキストを入力し、**[Add]** をクリックします。

   	これにより、新しい項目が挿入としてモバイル サービスに送信されます。

3. [Azure クラシック ポータル]で、**[Mobile Services]** をクリックし、目的のモバイル サービスをクリックします。

4. **[データ]** タブをクリックし、**[参照]** をクリックします。

   	![][9]

   	**TodoItem** テーブルに、Mobile Services によって生成されたいくつかの値を持つデータが含まれ、アプリケーションの TodoItem クラスに対応してその列が自動的にテーブルに追加されていることに注目してください。

これで、Android 向けの**データの使用**に関するチュートリアルは終了です。

## トラブルシューティング

### Android SDK バージョンの検証

[AZURE.INCLUDE [SDK の確認](../../includes/mobile-services-verify-android-sdk-version.md)]



## 次のステップ

このチュートリアルでは、Android アプリで Mobile Services のデータを操作できるようにするための基本について説明しました。他の Android チュートリアルで以下を試します。

* [認証の使用](mobile-services-android-get-started-users.md) <br/>アプリケーションのユーザーを認証する方法について説明します。

* [プッシュ通知の使用](mobile-services-javascript-backend-android-get-started-push.md) <br/>Mobile Services を使用してアプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

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

[Azure クラシック ポータル]: https://manage.windowsazure.com/
[Azure Mobile Services Android SDK]: http://aka.ms/Iajk6q
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkID=282122
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125

<!---HONumber=AcomDC_0727_2016-->