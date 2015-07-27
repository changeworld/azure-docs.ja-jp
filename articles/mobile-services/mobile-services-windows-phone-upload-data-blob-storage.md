<properties 
	pageTitle="Mobile Services を使用した画像の BLOB ストレージへのアップロード (Windows Phone) | Mobile Services" 
	description="Mobile Services を使用して画像を Azure BLOB ストレージにアップロードする方法を説明します。" 
	documentationCenter="windows" 
	authors="ggailey777" 
	services="mobile-services" 
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

このトピックでは、Azure モバイル サービスを使用して、ユーザーが生成したイメージをアプリケーションが Azure ストレージにアップロードおよび保存する方法を紹介します。モバイル サービスでは、SQL データベースを使用してデータを保存します。ただし、BLOB (Binary Large Object) データは、Azure BLOB ストレージ サービスに、より効率的に保存されます。

BLOB ストレージ サービスにデータを安全にアップロードするために必要な資格情報をクライアント アプリケーションで安全に配信できません。代わりに、これらの資格情報をモバイル サービスに保存し、それらを使用して、新しいイメージをアップロードするために使用される SAS (Shared Access Signature) を生成する必要があります。SAS は有効期間が短い資格情報 (この場合は 5 分間) であり、モバイル サービスによりクライアント アプリケーションに安全に返されます。アプリケーションは、この一時的な資格情報を使用してイメージをアップロードします。この例では、BLOB サービスからのダウンロードはパブリックです。

このチュートリアルでは、Mobile Services により生成された SAS を使用して、写真を撮影して画像を Azure にアップロードする機能を、[GetStartedWithData サンプル アプリケーション プロジェクト](mobile-services-windows-phone-get-started-data.md)に追加します。


##前提条件

このチュートリアルには、次のものが必要です。

+ Microsoft Visual Studio 2012 Express for Windows 8 以降のバージョン
+ [Windows Phone SDK 8.0] 以上
+ Microsoft Visual Studio でインストールされる NuGet パッケージ マネージャー。
+ [Azure ストレージ アカウント][How To Create a Storage Account]
+ チュートリアル「[既存のアプリへのモバイル サービスの追加](mobile-services-windows-phone-get-started-data.md)」を完了していること  


##Windows Phone アプリ用のストレージ クライアントのインストール

SAS を使用して Blob ストレージにイメージをアップロードできるようにするには、最初に、Windows Phone アプリ用のストレージ クライアント ライブラリをインストールする NuGet パッケージを追加する必要があります。

1. Visual Studio の**ソリューション エクスプローラー**で、プロジェクト名を右クリックし、**[NuGet パッケージの管理]** をクリックします。

2. 左側のウィンドウで、**[オンライン]** カテゴリ、**[プレリリースを含める]** の順に選択し、**WindowsAzure.Storage-Preview** を探します。**Azure Storage** パッケージで **[インストール]** をクリックし、使用許諾契約に同意します。

  	![Azure ストレージ NuGet の追加](./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png)

  	これにより、Azure のストレージ サービス用のクライアント ライブラリがプロジェクトに追加されます。

次に、イメージをキャプチャおよびアップロードするクイック スタート アプリケーションを更新します。

##管理ポータルで登録されている挿入スクリプトを更新する


[AZURE.INCLUDE [mobile-services-configure-blob-storage](../../includes/mobile-services-configure-blob-storage.md)]

>[AZURE.NOTE]新しいプロパティを TodoItem オブジェクトに追加するには、モバイル サービスで動的スキーマを有効にする必要があります。動的スキーマが有効になっていると、TodoItem テーブルに、これらの新しいプロパティに対応する新しい列が自動的に追加されます。

[AZURE.INCLUDE [mobile-services-windows-phone-upload-to-blob-storage](../../includes/mobile-services-windows-phone-upload-to-blob-storage.md)]


##次のステップ

モバイル サービスを Blob サービスと統合することによりイメージを安全にアップロードできました。他のバックエンド サービスおよび統合のトピックを参照してください。

+ [SendGrid を使用した Mobile Services からの電子メールの送信]
 
  SendGrid 電子メール サービスを使用して、モバイル サービスに電子メール機能を追加する方法について説明します。このトピックでは、サーバー側スクリプトを追加し、SendGrid を使用して電子メールを送信する方法を示します。

+ [Mobile Services でのバックエンド ジョブの計画]

  モバイル サービスのジョブ スケジューラ機能を使用して、定義したスケジュールに従って実行されるサーバー スクリプト コードを定義する方法について説明します。

##関連項目

+ [Mobile Services のサーバー スクリプト リファレンス]

  サーバー側のタスクを実行するサーバー スクリプトの使用および他の Azure コンポーネントおよび外部リソースとの統合に関するトピックを参照してください。
 
+ [Mobile Services .NET の使用方法の概念リファレンス]

  .NET でモバイル サービスを使用する方法について説明します
  
<!-- Images. -->

<!-- URLs. -->
[SendGrid を使用した Mobile Services からの電子メールの送信]: store-sendgrid-mobile-services-send-email-scripts.md
[Mobile Services でのバックエンド ジョブの計画]: mobile-services-schedule-recurring-tasks.md
[Mobile Services のサーバー スクリプト リファレンス]: mobile-services-how-to-use-server-scripts.md
[Get started with Mobile Services]: ../mobile-services-windows-phone-get-started.md

[Azure Management Portal]: https://manage.windowsazure.com/
[How To Create a Storage Account]: ../storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[Mobile Services .NET の使用方法の概念リファレンス]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Windows Phone SDK 8.0]: http://www.microsoft.com/download/details.aspx?id=35471


 

<!---HONumber=July15_HO3-->