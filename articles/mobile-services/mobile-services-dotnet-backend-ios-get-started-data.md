<properties
	pageTitle="iOS で既存のアプリにモバイル サービスを追加する"
	description="Mobile Services を使用して iOS アプリでデータを活用する方法について説明します。"
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="03/03/2015"
	ms.author="krisragh"/>

# 既存のアプリにモバイル サービスを追加

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

このチュートリアルでは、メモリ内にデータを格納する既存のアプリケーションをダウンロードし、それを Azure Mobile Service で使用できるように変更します。

このチュートリアルを開始する前に、[クイック スタート]を完了しておくことが前提条件となります。クイック スタートで作成したモバイル サービスを再利用します。


##<a name="download-app"></a>GetStartedWithData プロジェクトをダウンロードする

このチュートリアルは、[iOS の GetStartedWithData アプリケーション]に基づいています。このアプリは、追加項目がメモリに格納される点を除けば、[クイック スタート]と同じです。

[iOS の GetStartedWithData アプリケーション]をダウンロードします。Xcode で、プロジェクトを開き、**TodoService.m** を確認します。このアプリケーションを動作させる手順を指定した **//TODO** コメントが 8 つあります。

##<a name="update-app"></a>モバイル サービスをデータ アクセスに使用するためにアプリケーションを更新する

[AZURE.INCLUDE [mobile-services-ios-enable-mobile-service-access](../../includes/mobile-services-ios-enable-mobile-service-access.md)]

##<a name="test-app"></a>アプリケーションをテストする

1. Xcode で、**[実行]** をクリックして、アプリケーションを開始します。テキストを入力してから **+** をクリックすることで、todo リストに項目を追加します。

2. 変更内容が Azure のデータベースに保持されたことを確認します。Azure 管理ポータルまたは Visual Studio の SQL Server オブジェクト エクスプ ローラーを使用して、データベースを確認します。

3. ポータルを使用してデータベースを調べるには、モバイル サービスのダッシュボード ページで、データベース名をクリックし、[管理] をクリックしてデータベースを管理し、ログインします。次のクエリを実行します。ただし、`todolist` の代わりに、実際のモバイル サービスの名前を使用します。

```
        SELECT * FROM [todolist].[todoitems]
```

<!-- Anchors. -->
[Download the iOS app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps
[Download the service locally]: #download-the-service-locally
[Test the mobile service]: #test-the-service
[Publish the mobile service to Azure]: #publish-mobile-service


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-quickstart-startup-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-todoitem-data-browse.png
[17]: ./media/mobile-services-dotnet-backend-ios-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-ios-get-started-data/sql-azure-query.png


<!-- URLs. -->

[Validate and modify data with scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[JavaScript backend version]: /develop/mobile/tutorials/get-started-with-data-ios


[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=268622
[GitHub repo]: http://go.microsoft.com/fwlink/p/?LinkId=268784

[クイック スタート]: mobile-services-dotnet-backend-ios-get-started.md
[iOS の GetStartedWithData アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=268622

<!--HONumber=54--> 