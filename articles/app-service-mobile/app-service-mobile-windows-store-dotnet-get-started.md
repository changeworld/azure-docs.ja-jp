<properties
	pageTitle="Azure App Service Mobile Apps で Windows Runtime 8.1 アプリを作成する | Microsoft Azure"
	description="このチュートリアルに従って、Azure モバイル アプリ バックエンドを使用して、C#、Visual Basic、または JavaScript で Windows ストア用の開発を開始します。"
	services="app-service\mobile"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="11/16/2015"
	ms.author="glenga"/>

#Create a Windows app (Windows アプリの作成)

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##概要

このチュートリアルでは、ユニバーサル Windows アプリにクラウドベースのバックエンド サービスを追加する方法を示します。詳細については、「[モバイル アプリとは](app-service-mobile-value-prop.md)」を参照してください。

[AZURE.INCLUDE [app-service-mobile-windows-universal-get-started](../../includes/app-service-mobile-windows-universal-get-started.md)]

##前提条件

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料モバイル アプリを入手できます。このアプリは評価終了後も使用できます。詳細については、[Azure の無料試用版サイト](http://azure.microsoft.com/pricing/free-trial/)を参照してください。

* [Visual Studio Community 2013] 以降のバージョン。

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service を開始する場合は、「[Azure App Service アプリケーションの作成](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile)」にアクセスしてください。有効期間が短いスターター モバイル アプリを App Service ですぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

##新しい Azure Mobile App バックエンドの作成

新しいモバイル アプリ バックエンドを作成するには、次の手順に従います。

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

これで、モバイル クライアント アプリケーションで使用できる Azure モバイル アプリ バックエンドのプロビジョニングが完了しました。次は、簡単な "todo list" バックエンドのサーバー プロジェクトをダウンロードして、それを Azure に発行します。

## サーバー プロジェクトの構成

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

##クライアント プロジェクトのダウンロードおよび実行

モバイル アプリ バックエンドを構成した後は、新しいクライアント アプリを作成したり、既存のアプリを Azure に接続するように変更したりできます。このセクションでは、モバイル アプリ バックエンドに接続するようにカスタマイズされたユニバーサル Windows アプリ テンプレート プロジェクトをダウンロードします。

1. モバイル アプリ バックエンドの **[開始]** ブレードに戻り、**[新しいアプリの作成]**、**[ダウンロード]** の順にクリックして、圧縮されたプロジェクト ファイルをローカル コンピューターに抽出します。

3. (省略可能) サーバー プロジェクトとともに、ユニバーサル Windows アプリ プロジェクトをソリューションに追加します。これにより、必要であれば、同じ Visual Studio ソリューションでアプリとバックエンドの両方をより簡単にデバッグおよびテストできるようになります。

4. Windows ストア アプリをスタートアップ プロジェクトとして使用し、F5 キーを押してプロジェクトをリビルドし、Windows ストア アプリを起動します。

5. アプリで、**[Insert a TodoItem]** テキスト ボックスに意味のあるテキスト (たとえば、「*チュートリアルの完了*」) を入力し、**[Save]** をクリックします。

	![](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

	これで、Azure でホストされている新しいモバイル アプリ バックエンドに POST 要求が送信されます。

6. デバッグを停止し、`<your app name>.WindowsPhone` プロジェクトを右クリックして、**[スタートアップ プロジェクトに設定]** をクリックしてから F5 キーをもう一度押します。

	![](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-wp8.png)

	Windows アプリの開始後に、前の手順で保存したデータがモバイル アプリから読み込まれることに注目してください。

##次のステップ

* [アプリへの認証の追加](app-service-mobile-windows-store-dotnet-get-started-users.md)<br/> ID プロバイダーを使用してアプリケーションのユーザーを認証する方法について説明します。

* [アプリケーションにプッシュ通知を追加する](app-service-mobile-windows-store-dotnet-get-started-push.md) <br/>アプリにごく基本的なプッシュ通知を送信する方法について説明します。

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=AcomDC_1125_2015-->