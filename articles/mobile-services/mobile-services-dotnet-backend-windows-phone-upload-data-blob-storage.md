<properties 
	pageTitle="Mobile Services を使用した画像の BLOB ストレージへのアップロード (Windows Phone) | Mobile Services" 
	description="Mobile Services を使用して画像を Azure BLOB ストレージにアップロードする方法を説明します。" 
	documentationCenter="windows" 
	authors="ggailey777" 
	services="mobile-services,storage" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="glenga"/>

# モバイル サービスを使用した Azure ストレージへのイメージのアップロード

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../../includes/mobile-services-selector-upload-data-blob-storage.md)]

##概要
このトピックでは、Azure Mobile Services を使用して、ユーザーが生成したイメージを Windows Phone 8 または Windows Phone 8.1 Silverlight アプリケーションが Azure ストレージにアップロードおよび保存する方法を紹介します。モバイル サービスでは、SQL データベースを使用してデータを保存します。ただし、BLOB (Binary Large Object) データは、Azure BLOB ストレージ サービスに、より効率的に保存されます。

BLOB ストレージ サービスにデータを安全にアップロードするために必要な資格情報をクライアント アプリケーションで安全に配信できません。代わりに、これらの資格情報をモバイル サービスに保存し、それらを使用して、新しいイメージをアップロードするために使用される SAS (Shared Access Signature) を生成する必要があります。SAS は有効期間が短い資格情報 (この場合は 5 分間) であり、モバイル サービスによりクライアント アプリケーションに安全に返されます。アプリケーションは、この一時的な資格情報を使用してイメージをアップロードします。この例では、BLOB サービスからのダウンロードはパブリックです。

このチュートリアルでは、Mobile Services により生成された SAS を使用して、写真を撮影して画像を Azure にアップロードする機能を、[GetStartedWithData サンプル アプリケーション プロジェクト](mobile-services-dotnet-backend-windows-phone-get-started-data.md)に追加します。

##前提条件 

このチュートリアルには、次のものが必要です。

+ Microsoft Visual Studio 2013 以降
+ [Windows Phone SDK 8.0] 以上
+ Microsoft Visual Studio でインストールされる NuGet パッケージ マネージャー。
+ [Azure ストレージ アカウント][How To Create a Storage Account]
+ チュートリアル「[既存のアプリへのモバイル サービスの追加](mobile-services-dotnet-backend-windows-phone-get-started-data.md)」を完了していること  

>[AZURE.NOTE]このチュートリアルが対象とするのは、Windows Phone 8 および Windows Phone 8.1 Silverlight のアプリケーションです。Windows Phone ストア 8.1 またはユニバーサル Windows 8.1 アプリはサポートされません。

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-blob-storage](../../includes/mobile-services-dotnet-backend-configure-blob-storage.md)]

##<a name="install-storage-client"></a>Windows ストア アプリ用のストレージ クライアントのインストール

SAS を使用してアプリケーションから BLOB ストレージにイメージをアップロードできるようにするには、最初に、Windows ストア アプリ用のストレージ クライアント ライブラリをインストールする NuGet パッケージを追加する必要があります。

1. Visual Studio の**ソリューション エクスプローラー**で、クライアント アプリケーション プロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。

2. 左側のウィンドウで、**[オンライン]** カテゴリ、**[プレリリースを含める]** の順に選択し、**WindowsAzure.Storage-Preview** を探します。**Azure Storage** パッケージで **[インストール]** をクリックし、使用許諾契約に同意します。

  	![][2]

  	これにより、Azure のストレージ サービス用のクライアント ライブラリがプロジェクトに追加されます。

[AZURE.INCLUDE [mobile-services-windows-phone-upload-to-blob-storage](../../includes/mobile-services-windows-phone-upload-to-blob-storage.md)]
 
<!-- Anchors. -->
[Install the Storage Client library]: #install-storage-client
[Update the client app to capture images]: #add-select-images
[Install the storage client in the mobile service project]: #storage-client-server
[Update the TodoItem definition in the data model]: #update-data-model
[Update the table controller to generate an SAS]: #update-scripts
[Upload images to test the app]: #test
[Next Steps]: #next-steps

<!-- Images. -->
[2]: ./media/mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png

<!-- URLs. -->
[Send email from Mobile Services with SendGrid]: store-sendgrid-mobile-services-send-email-scripts.md
[Schedule backend jobs in Mobile Services]: mobile-services-dotnet-backend-schedule-recurring-tasks.md
[Get started with Mobile Services]: ../mobile-services-windows-phone-get-started.md

[Azure Management Portal]: https://manage.windowsazure.com/
[How To Create a Storage Account]: ../storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Windows Phone SDK 8.0]: http://www.microsoft.com/download/details.aspx?id=35471


 

<!---HONumber=July15_HO3-->