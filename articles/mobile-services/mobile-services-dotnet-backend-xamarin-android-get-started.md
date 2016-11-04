---
title: Xamarin Android アプリケーション用 Mobile Services の使用 | Microsoft Docs
description: 次のチュートリアルに従って、Xamarin Android 開発用の Azure Mobile Services を使用します。
services: mobile-services
documentationcenter: xamarin
author: lindydonna
manager: dwrede
editor: mollybos

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 07/21/2016
ms.author: donnam

---
# <a name="getting-started"> </a>Mobile Services の使用
[!INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> このトピックの Mobile Apps バージョンについては、「[Xamarin.Android アプリの作成](../app-service-mobile/app-service-mobile-xamarin-android-get-started.md)」を参照してください。
> 
> 

このチュートリアルでは、Azure Mobile Services を使用して Xamarin Android アプリケーションにクラウドベースのバックエンド サービスを追加する方法を示します。このチュートリアルでは、新しいモバイル サービスと、新しいモバイル サービスにアプリケーション データを保存する簡単な *To do list* アプリケーションの両方を作成します。作成するモバイル サービスは、サーバー側ビジネス ロジックのために Visual Studio を使用してサポートされる .NET 言語を使用し、モバイル サービスを管理します。JavaScript でサーバー側ビジネス ロジックを記述できるモバイル サービスを作成する方法については、このトピックの「[JavaScript バックエンド バージョン]」を参照してください。

> [!NOTE]
> このトピックでは、Azure クラシック ポータルを使用して、新しいモバイル サービス プロジェクトを作成する方法について説明します。Visual Studio 2013 更新プログラム 2 を使用して、新しいモバイル サービス プロジェクトを既存の Visual Studio ソリューションに追加することもできます。詳細については、「[クイック スタート: モバイル サービスを追加する (.NET バックエンド)](http://msdn.microsoft.com/library/windows/apps/dn629482.aspx)」を参照してください。
> 
> 

完成したアプリケーションのスクリーンショットは次のようになります。

![][0]

このチュートリアルを完了することは、Xamarin Android アプリケーションの他のすべての Mobile Services チュートリアルの前提条件です。

> [!NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、Azure 試用版にサインアップして、最大 10 件の無料モバイル サービスを入手できます。このサービスは評価終了後も使用できます。詳細については、[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-JP%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-xamarin-android-get-started)を参照してください。このチュートリアルには、[Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=257546) が必要です。無料試用版が利用できます。
> 
> 

## 新しいモバイル サービスを作成する
[!INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## 新しい Xamarin Android アプリケーションを作成する
モバイル サービスを作成したら、クラシック ポータルの簡単なクイック スタートに従って、新しいアプリケーションを作成するか、既存のアプリを変更して、モバイル サービスに接続することができます。

ここでは、モバイル サービス用の新しい Xamarin Android アプリケーションとサービス プロジェクトをダウンロードします。

1. まだ実行していない場合は、Visual Studio with Xamarin をインストールします。手順については、「[Visual Studio と Xamarin の設定とインストール](https://msdn.microsoft.com/library/mt613162.aspx)」を参照してください。Mac OS X コンピューターで Xamarin Studio を使用して、「[Mac ユーザー向けのセットアップ、インストール、および検証](https://msdn.microsoft.com/library/mt488770.aspx)」を参照することもできます。
2. [クラシック ポータル]で、**[Mobile Services]** をクリックし、先ほど作成したモバイル サービスをクリックします。
3. [クイック スタート] タブの **[プラットフォームの選択]** で **[Xamarin]** を選択し、**[新しい Xamarin アプリケーションを作成する]** を展開します。
   
       ![][6]
   
       これにより、モバイル サービスに接続された Xamarin Android アプリケーションを作成するための簡単な 3 つの手順が表示されます。
   
      ![][7]
4. **[サービスのダウンロードとクラウドへの発行]** で **[Android]** を選択し、**[ダウンロード]** をクリックします。
   
      これにより、モバイル サービスとモバイル サービスに接続されている *To do list* サンプル アプリケーションの両方のプロジェクトを含むソリューションがダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。
5. 発行プロファイルをダウンロードしてローカル コンピューターに保存し、保存場所を書き留めておいてください。

## モバイル サービスをテストする
[!INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## モバイル サービスを発行する
[!INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

## Xamarin Android アプリケーションを実行する
このチュートリアルの最後に、新しいアプリケーションをビルドして実行します。

1. Visual Studio または Xamarin Studio で、モバイル サービス ソリューション内のクライアント プロジェクトに移動します。
2. **[実行]** ボタンを押してプロジェクトをビルドし、アプリケーションを開始します。エミュレーターまたは接続されている USB デバイスを選択するよう求めるメッセージが表示されます。
   
   > [!NOTE]
   > プロジェクトを Android エミュレーターで実行するには、Android Virtual Device (AVD) を 1 つ以上定義する必要があります。これらのデバイスを作成および管理するには、AVD Manager を使用します。
   > 
   > 
3. アプリケーションで、意味のあるテキスト (たとえば、「*チュートリアルの完了*」) を入力し、正符号 (**[+]**) アイコンをクリックします。
   
    ![][10]
   
    これで、Azure でホストされている新しいモバイル サービスに POST 要求が送信されます。要求のデータは TodoItem テーブルに挿入されます。テーブルに格納された項目がモバイル サービスによって返され、データが一覧に表示されます。
   
   > [!NOTE]
   > モバイル サービスにアクセスして ToDoActivity.cs C# ファイルにあるデータをクエリおよび挿入するコードを確認できます。
   > 
   > 

## 次のステップ
クイック スタートはこれで完了です。Mobile Services で重要になるこれ以外の作業については、以下のトピックを参照してください。

* [オフライン データの同期の使用]<br/>クイックスタートでオフライン データの同期を使用して、アプリの応答性と信頼性を高める方法について説明します。
* [認証の使用] <br/>ID プロバイダーを使用してアプリケーションのユーザーを認証する方法について説明します。
* [プッシュ通知の使用] <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。
* [Mobile Services .NET バックエンドのトラブルシューティング] <br/>Mobile Services .NET バックエンドで発生する問題を診断して解決する方法について説明します。

[!INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Next Steps]: #next-steps



<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[7]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-steps-xamarin-android.png
[8]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[オフライン データの同期の使用]: mobile-services-xamarin-android-get-started-offline-data.md
[認証の使用]: mobile-services-dotnet-backend-xamarin-android-get-started-users.md
[プッシュ通知の使用]: mobile-services-dotnet-backend-xamarin-android-get-started-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript and HTML]: mobile-services-win8-javascript/
[Azure classic portal]: https://manage.windowsazure.com/
[クラシック ポータル]: https://manage.windowsazure.com/
[JavaScript バックエンド バージョン]: mobile-services-android-get-started.md
[Mobile Services .NET バックエンドのトラブルシューティング]: mobile-services-dotnet-backend-how-to-troubleshoot.md

<!---HONumber=AcomDC_0727_2016-->