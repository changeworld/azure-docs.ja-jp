<properties pageTitle="Get Started with Azure Mobile Services for iOS apps" metaKeywords="Azure iOS application, mobile service iOS, getting started Azure iOS" description="Follow this tutorial to get started using Azure Mobile Services for iOS development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# <a name="getting-started"> </a>Mobile Services の使用

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

このチュートリアルでは、Azure モバイル サービスを使用して iOS アプリにクラウドベースのバックエンド サービスを追加する方法を示します。このチュートリアルでは、新しいモバイル サービスと、新しいモバイル サービスにアプリケーション データを保存する簡単な *To do list* アプリケーションの両方を作成します。作成するモバイル サービスは、サーバー側ビジネス ロジックのために Visual Studio を使用してサポートされる .NET 言語を使用し、モバイル サービスを管理します。JavaScript でサーバー側ビジネス ロジックを記述できるモバイル サービスを作成する方法については、このトピックの「[JavaScript バックエンド バージョン][JavaScript バックエンド バージョン]」を参照してください。

完成したアプリケーションのスクリーンショットは次のようになります。

![][0]

このチュートリアルを完了するには、XCode 4.5 および iOS 5.0 以降のバージョンが必要です。

<div class="dev-callout"><strong>注</strong> <p>このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合、Azure 評価版にサインアップして、最大 10 件の無料モバイル サービスを入手できます。このサービスは評価終了後も使用できます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F" target="_blank">Azure の無料評価版サイト</a>を参照してください。</p></div>

## <a name="create-new-service"> </a> 新しいモバイル サービスを作成する

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## ローカル コンピューターにモバイル サービスをダウンロードする

モバイル サービスを作成したので、個人用のモバイル サービス プロジェクトをダウンロードして、ローカル コンピューターまたは仮想マシンで動かすことができます。

1.  作成したモバイル サービスをクリックし、[クイック スタート] タブの **[プラットフォームの選択]** で **[iOS]** をクリックして、**[新しい iOS アプリを作成する]** を展開します。

    ![][1]

2.  Visual Studio Professional 2013 以降をダウンロードしてインストールします (まだインストールしていない場合)。

3.  **[サービスのダウンロードとクラウドへの発行]** の下の **[ダウンロード]** をクリックします。

    これにより、モバイル サービスを実装する Visual Studio プロジェクトがダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

4.  また、発行プロファイルをダウンロードしてローカル コンピューターに保存し、保存場所を書き留めておいてください。

## モバイル サービスをテストする

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## モバイル サービスを発行する

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## 新しい iOS アプリを作成する

ここでは、モバイル サービスに接続される新しい iOS アプリを作成します。

1.  管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

2.  [クイック スタート] タブの **[プラットフォームの選択]** で **[iOS]** を選択し、**[新しい iOS アプリを作成する]** を展開します。

3.  [Xcode][Xcode] v4.4 以降をダウンロードしてインストールします (まだインストールしていない場合)。

4.  **[アプリケーションをダウンロードして実行する]** の下の **[ダウンロード]** をクリックします。

    これにより、モバイル サービスに接続されている *To do list* サンプル アプリケーションのプロジェクトが、モバイル サービス iOS SDK と共にダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

## 新しい iOS アプリを実行する

[WACOM.INCLUDE [mobile-services-ios-run-app](../includes/mobile-services-ios-run-app.md)]

これで、Azure で実行されているモバイル サービスに対して新しいクライアント アプリケーションを実行する方法が示されます。iOS アプリをローカル コンピューターで実行しているモバイル サービスでテストする前に、iOS 開発コンピューターからのアクセスを許可するように Web サーバーおよびファイアウォールを構成する必要があります。詳細については、「[Configure the local web server to allow connections to a local mobile service (ローカル モバイル サービスへの接続を許可するようにローカル Web サーバーを構成する)][Configure the local web server to allow connections to a local mobile service (ローカル モバイル サービスへの接続を許可するようにローカル Web サーバーを構成する)]」を参照してください。

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

 
 


  [JavaScript バックエンド バージョン]: /ja-jp/documentation/articles/mobile-services-ios-get-started
  [0]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
  [1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png
  [Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [データの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
  [オフライン データの同期の使用]: /ja-jp/documentation/articles/mobile-services-ios-get-started-offline-data
  [認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
  [プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push
  [モバイル サービス .NET バックエンドのトラブルシューティング]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/
