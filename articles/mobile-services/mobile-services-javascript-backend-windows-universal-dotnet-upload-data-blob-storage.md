<properties 
	pageTitle="ユニバーサル Windows アプリからの Azure BLOB ストレージへのイメージのアップロード | Microsoft Azure" 
	description="JavaScript バックエンド モバイル サービスを使用してイメージを Azure BLOB ストレージにアップロードし、そのイメージにユニバーサル Windows アプリからアクセスする方法を説明します。" 
	services="mobile-services,storage" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="glenga"/>

# Mobile Services を使用した Azure Blob ストレージへのイメージのアップロード

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../../includes/mobile-services-selector-upload-data-blob-storage.md)]

##概要 

このトピックでは、Azure モバイル サービスを使用して、ユーザーが生成したイメージをアプリケーションが Azure ストレージにアップロードおよび保存する方法を紹介します。モバイル サービスでは、SQL データベースを使用してデータを保存します。ただし、BLOB (Binary Large Object) データは、Azure BLOB ストレージ サービスに、より効率的に保存されます。

BLOB ストレージ サービスにデータを安全にアップロードするために必要な資格情報をクライアント アプリケーションで安全に配信できません。代わりに、これらの資格情報をモバイル サービスに保存し、それらを使用して、新しいイメージをアップロードするために使用される SAS (Shared Access Signature) を生成する必要があります。SAS は有効期間が短い資格情報 (この場合は 5 分間) であり、モバイル サービスによりクライアント アプリケーションに安全に返されます。アプリケーションは、この一時的な資格情報を使用してイメージをアップロードします。この例では、BLOB サービスからのダウンロードはパブリックです。

このチュートリアルでは、モバイル サービスにより生成された SAS を使用して、写真を撮影してイメージを Azure にアップロードする機能を、モバイル サービス クイック スタート アプリケーションに追加します。

##前提条件

このチュートリアルには、次のものが必要です。

+ Microsoft Visual Studio 2013 Update 3 以降のバージョン
+ [Azure ストレージ アカウント](../storage-create-storage-account.md)
+ コンピューターに接続された、カメラなどのイメージ キャプチャ デバイス

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[モバイル サービスの使用]」を完了している必要があります。

##Azure クラシック ポータルで登録されている挿入スクリプトを更新する

[AZURE.INCLUDE [mobile-services-configure-blob-storage](../../includes/mobile-services-configure-blob-storage.md)]

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-upload-to-blob-storage](../../includes/mobile-services-windows-universal-dotnet-upload-to-blob-storage.md)]

##次のステップ

モバイル サービスを Blob サービスと統合することによりイメージを安全にアップロードできました。他のバックエンド サービスおよび統合のトピックを参照してください。

+ [Mobile Services でのバックエンド ジョブの計画]

  	Mobile Services のジョブ スケジューラ機能を使用して、定義したスケジュールに従って実行されるサーバー スクリプト コードを定義する方法について説明します。

+ [Mobile Services のサーバー スクリプト リファレンス]

    サーバー側のタスクを実行するサーバー スクリプトの使用および他の Azure コンポーネントおよび外部リソースとの統合に関するトピックを参照してください。
 
+ [Mobile Services .NET の使用方法の概念リファレンス]

    .NET で Mobile Services を使用する方法について説明します
  
 
<!-- Anchors. -->
[Install the Storage Client library]: #install-storage-client
[Update the client app to capture images]: #add-select-images
[Update the insert script to generate an SAS]: #update-scripts
[Upload images to test the app]: #test
[Next Steps]: #next-steps

<!-- Images. -->

[2]: ./media/mobile-services-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png


<!-- URLs. -->
[Send email from Mobile Services with SendGrid]: store-sendgrid-mobile-services-send-email-scripts.md
[Mobile Services でのバックエンド ジョブの計画]: mobile-services-schedule-recurring-tasks.md
[Send push notifications to Windows Store apps using Service Bus from a .NET back-end]: http://go.microsoft.com/fwlink/?LinkId=277073&clcid=0x409
[Mobile Services のサーバー スクリプト リファレンス]: mobile-services-how-to-use-server-scripts.md
[モバイル サービスの使用]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md
[How To Create a Storage Account]: ../storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[Mobile Services .NET の使用方法の概念リファレンス]: mobile-services-windows-dotnet-how-to-use-client-library.md
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
 

<!---HONumber=AcomDC_1203_2015-->