<properties pageTitle="Get started with push notification using a .NET backend mobile service" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your universal Windows app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/15/2014" ms.author="glenga" />

# モバイル サービスでのプッシュ通知の使用

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

このトピックでは、Azure のモバイル サービスを .NET バックエンドで使用してユニバーサル Windows アプリにプッシュ通知を送信する方法について説明します。このチュートリアルでは、ユニバーサル Windows アプリ プロジェクトの Azure 通知ハブを使用したプッシュ通知を有効にします。完了すると、TodoList テーブルにレコードが挿入されるたびに、モバイル サービスは、.NET バックエンドから、登録されたすべての Windows ストア アプリおよび Windows Phone ストア アプリにプッシュ通知を送信します。作成する通知ハブはモバイル サービスでは無料で、モバイル サービスから独立して管理することができ、他のアプリケーションおよびサービスで使用できます。

> [WACOM.NOTE] このトピックでは、Visual Studio Professional 2013 with Update 3 でツーリングを使用して、モバイル サービスからユニバーサル Windows アプリへのプッシュ通知のサポートを追加する方法について説明します。同じ手順を使用して、モバイル サービスから Windows ストアまたは Windows Phone ストア 8.1 アプリへのプッシュ通知を追加することができます。Visual Studio Professional 2013 Update 3 にアップグレードできない場合や、モバイル サービス プロジェクトを手動で Windows ストア アプリ ソリューションに追加する場合は、トピックの「[このバージョン][このバージョン]」を参照してください。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1.  [アプリケーションをプッシュ通知に登録する][アプリケーションをプッシュ通知に登録する]
2.  [サービスを更新してプッシュ通知を送信する][サービスを更新してプッシュ通知を送信する]
3.  [ローカル テストのためにプッシュ通知を有効にする][ローカル テストのためにプッシュ通知を有効にする]
4.  [アプリケーションでプッシュ通知をテストする][アプリケーションでプッシュ通知をテストする]

このチュートリアルを完了するには、以下が必要です。

-   アクティブな [Microsoft ストア アカウント][Microsoft ストア アカウント]。
-   [Visual Studio Professional 2013][Visual Studio Professional 2013] (Update 3 以降のバージョン)。
    無料評価版が利用できます。

## <span id="register"></span></a> アプリケーションをプッシュ通知に登録する

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

1.  `\services\mobileServices\scripts` プロジェクト フォルダーに移動し、生成された \<*your\_service\_name*\>.push.register.js スクリプト ファイルを共有 `\js` フォルダーにコピーしてから、Windows アプリ プロジェクトと WindowsPhone アプリ プロジェクトの両方からこのファイルを削除します。

2.  このスクリプト ファイルを共有 `\js` プロジェクト フォルダーで開き、デバイスのチャネル URL を通知ハブに登録する *activated* イベント リスナーのコードを見つけ、**done** promise 関数を削除します。

    このチュートリアルでは、カスタム API が呼び出されたときではなく、新しい項目が挿入されたときに通知を送信します。

3.  Windows アプリ プロジェクトで、default.html ファイルを開き、スクリプト ファイル参照のパスを次のように共有 `\js` プロジェクト フォルダーに変更します。

        <script src="/js/your_service_name.push.register.js"></script>

4.  WindowsPhone アプリ プロジェクトでこの手順を繰り返します。

    これで、両方のプロジェクトがプッシュ登録スクリプトの共有バージョンを使用することになります。

アプリでプッシュ通知が有効にされたので、プッシュ通知を送信するにはモバイル サービスを更新する必要があります。

## <span id="update-service"></span></a>サービスを更新してプッシュ通知を送信する

次の手順では、既存の TodoItemController クラスを更新して、新しい項目が挿入されたときにプッシュ通知を登録済みのすべてのデバイスに送信します。同様のコードは、任意のカスタム [ApiController]、[TableController]、またはバックエンド サービスの任意の場所に実装できます。

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

## <span id="local-testing"></span></a> ローカル テストのためにプッシュ通知を有効にする

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push-vs2013](../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md)]

このセクションの残りの手順はオプションです。これらの手順により、ローカル コンピューターで実行されているモバイル サービスに対してアプリをテストできます。Azure で実行されているモバイル サービスを使用してプッシュ通知をテストする場合は、最後のセクションまでスキップしてもかまいません。これは、既にプッシュ通知の追加ウィザードで、Azure で実行されているサービスに接続するようにアプリが構成されているためです。

> [WACOM.NOTE] 運用環境のモバイル サービスをテストや開発作業を行う目的で使用しないでください。モバイル サービス プロジェクトは必ずテスト用の個別のステージング サービスに発行してください。

1.  `\services\mobileServices\settings` プロジェクト フォルダーに移動し、生成された \<*your\_service\_name*\>.js スクリプト ファイルを共有 `\js` フォルダーにコピーしてから、Windows アプリ プロジェクトと WindowsPhone アプリ プロジェクトの両方からこのファイルを削除します。さらに、このファイルが各アプリ プロジェクトの `\services\mobileServices\scripts` フォルダーにも存在する場合は、そこからも削除します。

2.  このスクリプト ファイルを共有 `\js` プロジェクト フォルダーで開き、Azure で実行されているモバイル サービスへのアクセスに使用する [MobileServiceClient オブジェクト][MobileServiceClient オブジェクト]を定義する既存のコードをコメント アウトします。

3.  同じ名前で、ローカル ホストの URL を使用する新しい **MobileServiceClient** オブジェクト定義を次のようにコンストラクターに追加します。

        // This MobileServiceClient has been configured to communicate with your local
        // test project for debugging purposes.
        var todolistClient = new WindowsAzure.MobileServiceClient(
            "http://localhost:4584");

    この **MobileServiceClient** オブジェクトを使用して、アプリは Azure でホストされているバージョンではなく、ローカル サービスに接続するようになります。この設定を戻して、Azure でホストされているモバイル サービスに対してアプリを実行する場合は、元の **MobileServiceClient** オブジェクト定義に戻します。

## <span id="test"></span></a>アプリケーションでプッシュ通知をテストする

[WACOM.INCLUDE [mobile-services-dotnet-backend-windows-universal-test-push](../includes/mobile-services-dotnet-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、Windows ストア アプリでモバイル サービスおよび通知ハブを使用してプッシュ通知を送信できるようにするための基本について説明しました。次は、タグを使用して、プッシュ通知をモバイル サービスから認証ユーザーにのみ送信する方法を説明した、次のチュートリアル「[プッシュ通知を認証ユーザーに送信する][プッシュ通知を認証ユーザーに送信する]」を行うことをお勧めします。

Mobile Services と通知ハブについては次のトピックを参照してください。

-   [データの使用][データの使用]
    モバイル サービスを使用してデータの格納およびクエリの実行の方法について説明します。

-   [認証の使用][認証の使用]
    モバイル サービスを使用して別のアカウントの種類のアプリケーションのユーザーを認証する方法について説明します。

-   [Windows Azure 通知ハブとは][Windows Azure 通知ハブとは]
    通知ハブがすべての主要なクライアント プラットフォーム全体のアプリケーションに通知を配信するための動作を説明します。

-   [Azure モバイル サービス向け HTML/JavaScript クライアントを使用する方法][Azure モバイル サービス向け HTML/JavaScript クライアントを使用する方法]
    モバイル サービスを HTML および JavaScript アプリから使用する方法について説明します。


  [mobile-services-selector-get-started-push]: ../includes/mobile-services-selector-get-started-push.md
  [このバージョン]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push
  [アプリケーションをプッシュ通知に登録する]: #register
  [サービスを更新してプッシュ通知を送信する]: #update-service
  [ローカル テストのためにプッシュ通知を有効にする]: #local-testing
  [アプリケーションでプッシュ通知をテストする]: #test
  [Microsoft ストア アカウント]: http://go.microsoft.com/fwlink/p/?LinkId=280045
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=391934
  [mobile-services-create-new-push-vs2013]: ../includes/mobile-services-create-new-push-vs2013.md
  [mobile-services-dotnet-backend-update-server-push]: ../includes/mobile-services-dotnet-backend-update-server-push.md
  [mobile-services-dotnet-backend-configure-local-push-vs2013]: ../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md
  [MobileServiceClient オブジェクト]: http://msdn.microsoft.com/ja-jp/library/azure/jj554219.aspx
  [mobile-services-dotnet-backend-windows-universal-test-push]: ../includes/mobile-services-dotnet-backend-windows-universal-test-push.md
  [プッシュ通知を認証ユーザーに送信する]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users/
  [データの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-data
  [認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-users
  [Windows Azure 通知ハブとは]: /ja-jp/documentation/articles/notification-hubs-overview/
  [Azure モバイル サービス向け HTML/JavaScript クライアントを使用する方法]: /ja-jp/documentation/articles/mobile-services-html-how-to-use-client-library
