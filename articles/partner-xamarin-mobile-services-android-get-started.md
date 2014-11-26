<properties pageTitle="Get Started with Mobile Services for Xamarin.Android" metaKeywords="Azure Xamarin.Android application, mobile service XamarinAndroid, getting started Azure Xamarin.Android" writer="craigd" description="Learn how to use Azure Mobile Services with your Xamarin.Android app." metaCanonical="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="donnam" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="hero-article" ms.date="01/01/1900" ms.author="donnam" />

# <a name="getting-started"></a>Mobile Services の使用

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">

<div class="dev-onpage-left-content">

<p>このチュートリアルでは、Azure のモバイル サービスを使用して Xamarin.Android アプリにクラウドベースのバックエンド サービスを追加する方法を示します。このチュートリアルでは、新しいモバイル サービスと、新しいモバイル サービスにアプリケーション データを保存する簡単な *To do list* アプリケーションの両方を作成します。</p>

完成したアプリケーションのスクリーンショットは次のようになります。

</div>

<div class="dev-onpage-video-wrapper">

[チュートリアルを見る][チュートリアルを見る] [<span class="icon">ビデオを再生する</span>][チュートリアルを見る] <span class="time">10:05</span>

</div>

</div>

![][0]

このチュートリアルを完了するには、(Windows 上に) Xamarin Studio と Visual Studio プラグインをインストールする [Xamarin.Android][Xamarin.Android] と、最新の Android プラットフォームが必要です。このほか、Android SDK のバージョン 4.2 以降が必要になります。

ダウンロードしたクイック スタート プロジェクトには、Xamarin.Android 向けのモバイル サービス SDK が含まれています。このプロジェクトではバージョン 4.2 以降の Android をターゲットにしていますが、モバイル サービス SDK ではバージョン 2.2 以降があれば問題ありません。

<div class="dev-callout">

<b>注</b>
<p>このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、Azure 試用版にサインアップして最大 10 の無料モバイル サービスを取得し、試用期間が終わった後でも使用し続けることができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A9C9624B5" target="_blank">Azure の無料評価版サイト</a>を参照してください。</p>

</div>

## <a name="create-new-service"> </a> 新しいモバイル サービスを作成する

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## 

## <span class="short-header">新しいアプリケーションを作成する</span>新しい Xamarin.Android アプリを作成する
モバイル サービスを作成したら、管理ポータルの簡単なクイック スタートに従って、新しいアプリケーションを作成するか、既存のアプリケーションを変更してモバイル サービスに接続することができます。

ここでは、モバイル サービスに接続された新しい Xamarin.Android アプリを作成します。

1.  管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

2.  [クイック スタート] タブの **[プラットフォームの選択]** で **[Xamarin.Android]** を選択し、**[新しい Android アプリを作成する]** を展開します。

    ![][1]

    これにより、モバイル サービスに接続された Xamarin.Android アプリケーションを作成するための簡単な 3 つの手順が表示されます。

    ![][2]

3.  **[TodoItem テーブルを作成する]** をクリックして、アプリケーション データを格納するテーブルを作成します。

4.  **[アプリをダウンロードして実行する]** の下の **[ダウンロード]** をクリックします。

    これにより、モバイル サービスに接続する、*To do list* サンプル アプリケーションのプロジェクトがダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

## Android アプリを実行する

このチュートリアルの最後に、新しいアプリケーションをビルドして実行します。

1.  圧縮されたプロジェクト ファイルの保存場所を参照し、ファイルをコンピューター上に展開します。

2.  Xamarin Studio または Visual Studio で、**[File]**、**[Open]** の順にクリックし、サンプル ファイルを展開した場所を参照します。**XamarinTodoQuickStart.Android.sln** を選択して開きます。

    ![][3]

    ![][4]

3.  **[Run]** ボタンを押してプロジェクトをビルドし、アプリケーションを開始します。エミュレーターまたは接続されている USB デバイスを選択するよう求めるメッセージが表示されます。

    <div class="dev-callout">

    <b>注</b>
    <p>プロジェクトを Android エミュレーターで実行するには、Android Virtual Device (AVD) を 1 つ以上定義する必要があります。これらのデバイスを作成および管理するには、AVD Manager を使用します。</p>

    </div>

4.  アプリケーションで、意味のあるテキスト (たとえば、「*チュートリアルの完了*」) を入力し、**[Add]** をクリックします。

    ![][5]

    これで、Azure でホストされている新しいモバイル サービスに POST 要求が送信されます。要求のデータは TodoItem テーブルに挿入されます。テーブルに格納された項目がモバイル サービスによって返され、データが一覧に表示されます。

    > [WACOM.NOTE]
    > モバイル サービスにアクセスして ToDoActivity.cs C# ファイルにあるデータを照会および挿入するコードを確認できます。

5.  管理ポータルに戻り、**[データ]** タブ、**TodoItems** テーブルの順にクリックします。

    ![][6]

    これで、アプリケーションによってテーブルに挿入されたデータを参照できます。

    ![][7]

## <a name="next-steps"> </a> 次のステップ

クイック スタートはこれで完了です。モバイル サービスで重要になるこれ以外の作業については、以下のトピックを参照してください。

-   [データの使用][データの使用]
   
    Mobile Services を使用してデータの格納およびクエリを実行する方法について説明します。

-   [オフライン データの同期の使用][オフライン データの同期の使用]
   
    オフライン データの同期を使用して、アプリの応答性と信頼性を高める方法について説明します。

-   [認証の使用][認証の使用]
   
    ID プロバイダーを使用してアプリケーションのユーザーを認証する方法について説明します。

-   [プッシュ通知の使用][プッシュ通知の使用]
   
    アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。



  [チュートリアルを見る]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Getting-Started-with-Xamarin-and-Windows-Azure-Mobile-Services
  [0]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-completed-android.png
  [1]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-portal-quickstart-xamarin-android.png
  [2]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-steps-xamarin-android.png
  [3]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-xs.png
  [4]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-vs.png
  [5]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-startup-android.png
  [6]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-tab.png
  [7]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-browse.png
  [データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-xamarin-android
  [オフライン データの同期の使用]: /ja-jp/documentation/articles/mobile-services-xamarin-android-get-started-offline-data
  [認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-xamarin-android
  [プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-xamarin-android
