<properties linkid="mobile-services-dotnet-backend-xamarin-ios-get-started" urlDisplayName="Get Started with Mobile Services for Xamarin iOS apps" pageTitle="Get Started with Mobile Services for Xamarin iOS apps - Azure Mobile Services" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Xamarin iOS development" metaCanonical="" services="" documentationCenter="Mobile" title="Get Started with Mobile Services for Xamarin iOS apps" authors="donnam" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam"></tags>

# <a name="getting-started"> </a>Mobile Services の使用

[WACOM.INCLUDE [mobile-services-selector-get-started][mobile-services-selector-get-started]]

このチュートリアルでは、Azure Mobile Services を使用して Xamarin iOS アプリケーションにクラウドベースのバックエンド サービスを追加する方法を示します。このチュートリアルでは、新しいモバイル サービスと、新しいモバイル サービスにアプリケーション データを保存する簡単な *To do list* アプリケーションの両方を作成します。作成するモバイル サービスは、サーバー側ビジネス ロジックのために Visual Studio を使用してサポートされる .NET 言語を使用し、モバイル サービスを管理します。JavaScript でサーバー側ビジネス ロジックを記述できるモバイル サービスを作成する方法については、このトピックの「[JavaScript バックエンド バージョン][JavaScript バックエンド バージョン]」を参照してください。

> [WACOM.NOTE] このトピックでは、Azure の管理ポータルを使用して、新しいモバイル サービス プロジェクトを作成する方法を示します。Visual Studio 2013 更新プログラム 2 を使用して、新しいモバイル サービス プロジェクトを既存の Visual Studio ソリューションに追加することもできます。詳細については、「[クイック スタート: モバイル サービスの追加 (.NET バックエンド)][クイック スタート: モバイル サービスの追加 (.NET バックエンド)]」を参照してください。

完成したアプリケーションのスクリーンショットは次のようになります。

![][]

このチュートリアルを完了することは、Xamarin iOS アプリケーションの他のすべての Mobile Services チュートリアルの前提条件です。

> [WACOM.NOTE] このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[無料評価版のサイト][無料評価版のサイト]を参照してください。
> このチュートリアルには [Visual Studio Professional 2013][Visual Studio Professional 2013] が必要です。無料評価版が利用できます。

## 新しいモバイル サービスを作成する

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service][mobile-services-dotnet-backend-create-new-service]]

## 新しい Xamarin iOS アプリケーションを作成する

モバイル サービスを作成したら、管理ポータルの簡単なクイック スタートに従って、新しいアプリケーションを作成するか、既存のアプリケーションを変更してモバイル サービスに接続することができます。

ここでは、モバイル サービス用の新しい Xamarin iOS アプリケーションとサービス プロジェクトをダウンロードします。

1.  管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

2.  [クイック スタート] タブの **[プラットフォームの選択]** で **[Xamarin]** を選択し、**[新しい Xamarin アプリケーションを作成する]** を展開します。

    ![][1]

    これにより、モバイル サービスに接続された Xamarin iOS アプリケーションを作成するための簡単な 3 つの手順が表示されます。

    ![][2]

3.  まだインストールしていない場合は、[Visual Studio Professional 2013][Visual Studio Professional 2013] をダウンロードし、ローカル コンピューターまたは仮想マシンにインストールします。

4.  [Xcode][Xcode] v4.4 以降および [Xamarin Studio][Xamarin Studio] をダウンロードしてインストールします。また、Xamarin for Visual Studio を使用することもできます。

5.  **[サービスのダウンロードとクラウドへの発行]** で **[iOS]** を選択し、**[ダウンロード]** をクリックします。

    これにより、モバイル サービスとモバイル サービスに接続されている *To do list* サンプル アプリケーションの両方のプロジェクトを含むソリューションがダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

6.  発行プロファイルをダウンロードしてローカル コンピューターに保存し、保存場所を書き留めておいてください。

## モバイル サービスをテストする

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service][mobile-services-dotnet-backend-test-local-service]]

## モバイル サービスを発行する

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][mobile-services-dotnet-backend-publish-service]]

## Xamarin iOS アプリケーションを実行する

このチュートリアルの最後に、新しいアプリケーションをビルドして実行します。

1.  Visual Studio または Xamarin Studio で、モバイル サービス ソリューション内のクライアント プロジェクトに移動します。

    ![][3]

    ![][4]

2.  **[実行]** ボタンを押してクライアント プロジェクトをビルドし、iPhone エミュレーターでアプリケーションを開始します。

3.  アプリケーションで、意味のあるテキスト (たとえば、「*チュートリアルの完了*」) を入力し、正符号 (**[+]**) アイコンをクリックします。

    ![][5]

    これで、Azure でホストされている新しいモバイル サービスに POST 要求が送信されます。要求のデータは TodoItem テーブルに挿入されます。テーブルに格納された項目がモバイル サービスによって返され、データが一覧に表示されます。

> [WACOM.NOTE] モバイル サービスにアクセスして QSTodoService.cs C# ファイルにあるデータを照会および挿入するコードを確認できます。

## 次のステップ

クイック スタートはこれで完了です。モバイル サービスで重要になるこれ以外の作業については、以下のトピックを参照してください。

-   [認証の使用][認証の使用]<br/>
    ID プロバイダーを使用してアプリケーションのユーザーを認証する方法について説明します。

-   [プッシュ通知の使用][プッシュ通知の使用]<br/>
    アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。



  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [JavaScript バックエンド バージョン]: /ja-jp/documentation/articles/partner-xamarin-mobile-services-ios-get-started
  [クイック スタート: モバイル サービスの追加 (.NET バックエンド)]: http://msdn.microsoft.com/ja-jp/library/windows/apps/dn629482.aspx
  []: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-completed-ios.png
  [無料評価版のサイト]: http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-xamarin-ios-get-started
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
  [1]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
  [2]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
  [Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
  [Xamarin Studio]: http://xamarin.com/download
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [3]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
  [4]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
  [5]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-startup-ios.png
  [認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users
  [プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-push
