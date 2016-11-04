---
title: iOS アプリ用 Azure Mobile Services の使用 | Microsoft Docs
description: 次のチュートリアルに従って、iOS 開発用の Azure Mobile Services を使用します。
services: mobile-services
documentationcenter: ios
author: krisragh
manager: dwrede
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 07/21/2016
ms.author: krisragh

---
# <a name="getting-started"> </a>Mobile Services の使用
[!INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> このトピックの Mobile Apps バージョンについては、[Azure Mobile Apps での iOS アプリの作成](../app-service-mobile/app-service-mobile-ios-get-started.md)に関する記事を参照してください。
> 
> 

このチュートリアルでは、Azure Mobile Services を使用して iOS アプリにクラウドベースのバックエンド サービスを追加する方法を示します。

このチュートリアルでは、新しいモバイル サービスと、新しいモバイル サービスにアプリケーション データを保存する簡単な *To do list* アプリケーションの両方を作成します。作成するモバイル サービスは、サーバー側ビジネス ロジックのために JavaScript を使用します。.NET でサーバー側ビジネス ロジックを記述できるモバイル サービスを作成する方法については、このトピックの [.NET バックエンド バージョン]をご覧ください。

> [!NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合、Azure 評価版にサインアップして、[無料 Mobile Services を入手できます。このサービスは評価終了後も使用できます](https://azure.microsoft.com/pricing/details/mobile-services/)。詳細については、[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-JP%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F%20)を参照してください。
> 
> 

## <a name="create-new-service"> </a>新しいモバイル サービスを作成する
[!INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## 新しい iOS アプリを作成する
Azure クラシック ポータルで簡単なクイック スタートに従い、モバイル サービスに接続された新しいアプリを作成できます。

1. [Azure クラシック ポータル]で、**[Mobile Services]** をクリックし、先ほど作成したモバイル サービスをクリックします。
2. [クイック スタート] タブの [**プラットフォームの選択**] で [**iOS**] を選択し、[**新しい iOS アプリを作成する**] を展開します。これにより、モバイル サービスに接続された iOS アプリを作成するための手順が表示されます。
3. **[TodoItem テーブルを作成する]** をクリックして、アプリケーション データを格納するテーブルを作成します。
4. **[アプリケーションをダウンロードして実行する]** の下の **[ダウンロード]** をクリックします。これにより、モバイル サービスに接続されている *To do list* サンプル アプリケーションのプロジェクトが、Mobile Services iOS SDK と共にダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

## 新しい iOS アプリを実行する
[!INCLUDE [mobile-services-ios-run-app](../../includes/mobile-services-ios-run-app.md)]

<ol start="4"><li><p>[Azure クラシック ポータル]に戻り、**[データ]** タブ、**TodoItems** テーブルの順にクリックします。これで、アプリケーションによってテーブルに挿入されたデータを参照できます。<p></li></ol></p>

## <a name="next-steps"> </a>次のステップ
Mobile Services で重要になるこれ以外の作業については、以下のトピックをご覧ください。

* [オフライン データの同期の使用]<br/> オフライン データの同期を使用して、アプリの応答性と信頼性を高める方法について説明します。
* [既存の Azure Mobile Services アプリへの認証の追加]<br/> ID プロバイダーを使用してアプリケーションのユーザーを認証する方法について説明します。
* [Mobile Services アプリへのプッシュ通知の追加] <br/>アプリにごく基本的なプッシュ通知を送信する方法について説明します。

[!INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[6]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[オフライン データの同期の使用]: mobile-services-ios-get-started-offline-data.md
[既存の Azure Mobile Services アプリへの認証の追加]: mobile-services-dotnet-backend-ios-get-started-users.md
[Mobile Services アプリへのプッシュ通知の追加]: mobile-services-dotnet-backend-ios-get-started-push.md


[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Azure クラシック ポータル]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[.NET バックエンド バージョン]: mobile-services-dotnet-backend-ios-get-started.md

<!---HONumber=AcomDC_0727_2016-->