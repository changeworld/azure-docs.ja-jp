<properties linkid="develop-mobile-tutorials-get-started-wp8" urlDisplayName="Get Started (WP8)" pageTitle="Get Started with Azure Mobile Services for Windows Phone apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Phone development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# <a name="getting-started"> </a>Mobile Services の使用

[WACOM.INCLUDE [mobile-services-selector-get-started][mobile-services-selector-get-started]]

このチュートリアルでは、Azure のモバイル サービスを使用して Windows Phone 8 アプリにクラウドベースのバックエンド サービスを追加する方法を示します。このチュートリアルでは、新しいモバイル サービスと、新しいモバイル サービスにアプリケーション データを保存する簡単な *To do list* アプリケーションの両方を作成します。作成するモバイル サービスは、サーバー側ビジネス ロジックのために Visual Studio を使用してサポートされる .NET 言語を使用し、モバイル サービスを管理します。JavaScript でサーバー側ビジネス ロジックを記述できるモバイル サービスを作成する方法については、このトピックの「[JavaScript バックエンド バージョン][JavaScript バックエンド バージョン]」を参照してください。

完成したアプリケーションのスクリーンショットは次のようになります。

![][]

> [WACOM.NOTE] このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合、Azure 評価版にサインアップして、最大 10 件の無料モバイル サービスを入手できます。このサービスは評価終了後も使用できます。詳細については、[Azure の無料評価版サイト][Azure の無料評価版サイト]を参照してください。
> このチュートリアルには、[Visual Studio Professional 2013][Visual Studio Professional 2013] が必要です。無料評価版が利用できます。新しい Windows Phone 8.1 アプリケーションを作成するには、Visual Studio 2013 Update 2 以降が必要です。

## <a name="create-new-service"> </a> 新しいモバイル サービスを作成する

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service][mobile-services-dotnet-backend-create-new-service]]

## 新しい Windows Phone アプリケーションを作成する

モバイル サービスを作成したら、管理ポータルの簡単なクイック スタートに従って、新しいアプリケーションを作成するか、既存のアプリケーションを変更してモバイル サービスに接続することができます。

ここでは、モバイル サービスに接続された新しい Windows Phone 8 アプリを作成します。

1.  管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

2.  [クイック スタート] タブの **[プラットフォームの選択]** で **[Windows Phone 8]** を選択し、**[新しい Windows Phone 8 アプリを作成する]** を展開します。

    ![][1]

    これにより、モバイル サービスに接続された Windows Phone アプリを作成するための簡単な 3 つの手順が表示されます。

    ![][2]

3.  まだインストールしていない場合は、[Visual Studio Professional 2013][Visual Studio Professional 2013] をダウンロードし、ローカル コンピューターまたは仮想マシンにインストールします。

4.  **[サービスのダウンロードとクラウドへの発行]** で **[ダウンロード]** をクリックします。

    これにより、モバイル サービスとモバイル サービスに接続されている *To do list* サンプル アプリケーションの両方のプロジェクトを含むソリューションがダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

5.  **[クラウドへのサービスの発行]** で、発行プロファイルをダウンロードし、ダウンロードしたファイルをローカル コンピューターに保存して、保存場所を書き留めておきます。

## ローカル コンピューターでモバイル サービスをテストする

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service][mobile-services-dotnet-backend-test-local-service]]

> [WACOM.NOTE] このほかにも、ローカル サービスを接続する Windows Phone アプリケーションの実行に必要な構成手順があります。このトピックではこの方法については説明しませんが、詳細については [Windows Phone 8 エミュレーターからローカル Web サービスに接続する方法][Windows Phone 8 エミュレーターからローカル Web サービスに接続する方法]を参照してください。

## モバイル サービスを発行する

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][mobile-services-dotnet-backend-publish-service]]

1.  Windows Phone アプリケーション プロジェクトで、App.xaml.cs ファイルを開き、[MobileServiceClient][MobileServiceClient] インスタンスを作成するコードを見つけて、*localhost* を使用してこのクライアントを作成するコードをコメント アウトし、リモート モバイル サービス URL を使用してクライアントを作成するコードのコメントを解除すると、次のようになります。

        public static MobileServiceClient MobileService = new MobileServiceClient(
                    "https://todolist.azure-mobile.net/",
                    "XXXXXXX-APPLICATION-KEY-XXXXXXXX");

    クライアントは Azure に発行されたモバイル サービスにアクセスするようになります。

2.  (省略可能) Windows Phone 8.1 アプリケーションを作成する場合、ソリューション エクスプローラーでプロジェクトを右クリックし、**[プロパティ]** をクリックして **[Windows Phone OS バージョンの対象]** を **[Windows Phone 8.1]** に設定し、**[はい]** をクリックしてプロジェクトをアップグレードします。

3.  **F5** キーを押してプロジェクトをリビルドし、アプリケーションを開始します。

4.  アプリケーションで、意味のあるテキスト (たとえば、「*チュートリアルの完了*」) を入力し、**[保存]** をクリックします。

    これで、Azure でホストされている新しいモバイル サービスに POST 要求が送信されます。要求のデータは TodoItem テーブルに挿入されます。テーブルに格納された項目がモバイル サービスによって返され、データが一覧に表示されます。

    <div class="dev-callout"> 
<b>注</b> 
<p>モバイル サービスにアクセスして MainPage.xaml.cs ファイルにあるデータを照会および挿入するコードを確認できます。</p> 
    </div>

![][3]

これで、Azure で実行されているモバイル サービスに対して新しいクライアント アプリケーションを実行する方法が示されます。Windows Phone アプリケーションをローカル コンピューターで実行しているモバイル サービスでテストする前に、Windows Phone デバイスまたはエミュレーターからのアクセスを許可するように Web サーバーおよびファイアウォールを構成する必要があります。詳細については、「[Configure the local web server to allow connections to a local mobile service (ローカル モバイル サービスへの接続を許可するようにローカル Web サーバーを構成する)][Configure the local web server to allow connections to a local mobile service (ローカル モバイル サービスへの接続を許可するようにローカル Web サーバーを構成する)]」を参照してください。

## <a name="next-steps"> </a> 次のステップ

クイック スタートはこれで完了です。モバイル サービスで重要になるこれ以外の作業については、以下のトピックを参照してください。

-   [データの使用][データの使用]
    Mobile Services を使用してデータの格納およびクエリを実行する方法について説明します。

-   [オフライン データの同期の使用][オフライン データの同期の使用]
    オフライン データの同期を使用してアプリケーションの反応と信頼性を高める方法について説明します。

-   [認証の使用][認証の使用]
    ID プロバイダーを使用してアプリケーションのユーザーを認証する方法について説明します。

-   [プッシュ通知の使用][プッシュ通知の使用]
    アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

-   [モバイル サービス .NET バックエンドのトラブルシューティング][モバイル サービス .NET バックエンドのトラブルシューティング]
     モバイル サービスの .NET バックエンドで発生する問題を診断して解決する方法について説明します。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [JavaScript バックエンド バージョン]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started
  []: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png
  [Azure の無料評価版サイト]: http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A30A4DDE2&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-phone-get-started%2F
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
  [1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-portal-quickstart-wp8.png
  [2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-steps-wp8.png
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [Windows Phone 8 エミュレーターからローカル Web サービスに接続する方法]: http://go.microsoft.com/fwlink/p/?LinkId=391930
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [MobileServiceClient]: http://msdn.microsoft.com/ja-jp/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx
  [3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-startup-wp8.png
  [Configure the local web server to allow connections to a local mobile service (ローカル モバイル サービスへの接続を許可するようにローカル Web サーバーを構成する)]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express
  [データの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
  [オフライン データの同期の使用]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started-offline-data
  [認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users
  [プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push
  [モバイル サービス .NET バックエンドのトラブルシューティング]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/
