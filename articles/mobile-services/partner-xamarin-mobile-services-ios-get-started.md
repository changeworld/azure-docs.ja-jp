<properties
	pageTitle="Mobile Services の使用 (Xamarin iOS アプリケーション)"
	description="次のチュートリアルに従って、Xamarin iOS 開発用の Azure Mobile Services を使用します。"
	services="mobile-services"
	documentationCenter="xamarin"
	authors="conceptdev"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/16/2015"
	ms.author="craig.dunn@xamarin.com"/>

# <a name="getting-started"> </a>Mobile Services の使用

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

このチュートリアルでは、Azure のモバイル サービスを使用して Xamarin.iOS アプリにクラウドベースのバックエンド サービスを追加する方法を示します。このチュートリアルでは、新しいモバイル サービスと、新しいモバイル サービスにアプリ データを保存する簡単な <em>To do list</em> アプリの両方を作成します。

このチュートリアルのビデオを見る場合は、下のクリップでこのチュートリアルと同じ手順が表示されます。

ビデオ: "Getting Started with Xamarin and Azure Mobile Services" (Xamarin と Azure モバイル サービスの使用)。Xamarin のデベロッパー エバンジェリスト Craig Dunn 氏 (再生時間: 10 分 05 秒)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]



完成したアプリケーションのスクリーンショットは次のようになります。

![][0]

このチュートリアルを完了するには、XCode と、OS X 用 [Xamarin Studio] または Visual Studio 用 Xamarin Visual Studio プラグイン (Windows) が必要です。サンプルは iOS 5.0 以降で実行します。

> [AZURE.IMPORTANT]このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、Azure 試用版にサインアップして最大 10 の無料モバイル サービスを取得し、試用期間が終わった後でも使用し続けることができます。詳細については、[Azure の無料試用版サイト](http://azure.microsoft.com/pricing/free-trial/)を参照してください。

## <a name="create-new-service"> </a>新しいモバイル サービスを作成する

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## 新しい Xamarin.iOS アプリケーションを作成する

モバイル サービスを作成したら、管理ポータルの簡単なクイック スタートに従って、新しいアプリケーションを作成するか、既存のアプリケーションを変更してモバイル サービスに接続することができます。

ここでは、モバイル サービスに接続された新しい Xamarin.iOS アプリを作成します。

1.  管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

2. [クイック スタート] タブの **[プラットフォームの選択]** で **[Xamarin.iOS]** を選択し、**[新しい Xamarin.iOS アプリを作成する]** を展開します。

	![][6]

	これにより、モバイル サービスに接続された Xamarin.iOS アプリケーションを作成するための簡単な 3 つの手順が表示されます。

  	![][7]

3. まだインストールしていない場合は、Xcode (最新バージョンの Xcode 6.0 以降をお勧めします) と [Xamarin Studio] をダウンロードしてインストールします。

4. **[TodoItems テーブルを作成する]** をクリックして、アプリケーション データを格納するテーブルを作成します。

5. **[アプリをダウンロードして実行する]** の下の **[ダウンロード]** をクリックします。

	これにより、Mobile Services に接続され、Xamarin.iOS 用の Azure Mobile Services コンポーネントを参照する "To do list” サンプル アプリケーション用のプロジェクトがダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

## 新しい Xamarin.iOS アプリケーションを実行する

このチュートリアルの最後に、新しいアプリケーションをビルドして実行します。

1. 圧縮されたプロジェクト ファイルの保存場所を参照し、ファイルをコンピューター上に展開して、Xamarin Studio または Visual Studio で **XamarinTodoQuickStart.iOS.sln** ソリューション ファイルを開きます。

	![][8]

	![][9]

2. **[実行]** をクリックしてプロジェクトをビルドし、このプロジェクトの既定である iPhone エミュレーターでアプリケーションを開始します。

3. アプリケーションで、意味のあるテキスト (たとえば、「_チュートリアルの完了_」) を入力し、プラス (**+**) アイコンをクリックします。

	![][10]

	これで、Azure でホストされている新しいモバイル サービスに POST 要求が送信されます。要求のデータは TodoItem テーブルに挿入されます。テーブルに格納された項目がモバイル サービスによって返され、データが一覧に表示されます。

	> [AZURE.NOTE]モバイル サービスにアクセスして TodoService.cs C# ファイルにあるデータを照会および挿入するコードを確認できます。

4. 管理ポータルに戻り、**[データ]** タブ、**TodoItems** テーブルの順にクリックします。

	![][11]

	これで、アプリケーションによってテーブルに挿入されたデータを参照できます。

	![][12]


## 次のステップ
クイック スタートはこれで完了です。Mobile Services で重要になるこれ以外の作業については、以下のトピックを参照してください。

* [オフライン データの同期の使用]<br/>クイックスタートでオフライン データの同期を使用して、アプリの応答性と信頼性を高める方法について説明します。

* [認証の使用] <br/>ID プロバイダーを使用してアプリケーションのユーザーを認証する方法について説明します。

* [プッシュ通知の使用] <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-completed-ios.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[7]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-xs.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-vs.png
[10]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-xamarin-ios
[オフライン データの同期の使用]: /develop/mobile/tutorials/mobile-services-xamarin-ios-get-started-offline-data
[認証の使用]: /develop/mobile/tutorials/get-started-with-users-xamarin-ios
[プッシュ通知の使用]: /develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Xamarin Studio]: http://xamarin.com/download
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO1-->