<properties 
	pageTitle="Azure Storage を 5 分で使い始める | Microsoft Azure" 
	description="Azure QuickStarts、Visual Studio、Azure ストレージ エミュレーターを使用して、Microsoft Azure の BLOB、テーブル、キューをすばやく導入する方法について説明します。初めての Azure Storage アプリケーションを 5 分で実行します。" 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="05/28/2015" 
	ms.author="tamram;selcint"/>

# Azure Storage を 5 分で使い始める 

Azure Storage の開発は簡単です。このチュートリアルでは、Azure Storage アプリケーションを短時間で稼動させる方法について説明します。Azure Storage で簡単に準備できる 2 つのシナリオを紹介します。

- [Azure ストレージ エミュレーターで最初の Azure Storage アプリケーションをローカルに実行する](#run-your-first-azure-storage-application-locally-against-the-azure-storage-emulator)
- [クラウドの Azure Storage で最初の Azure Storage アプリケーションを実行する](#run-your-first-azure-storage-application-against-azure-storage-in-the-cloud)

コードの説明に入る前に Azure Storage の詳細について学習するには、[次のステップ](#next-steps)をご覧ください

## 前提条件

開始する前に、以下の前提条件を確認します。

1. アプリケーションをコンパイルし、ビルドするには、コンピューターに [Visual Studio](https://www.visualstudio.com/) をインストールしておく必要があります。 
2. 最新バージョンの [Azure SDK for .NET](http://azure.microsoft.com/downloads/) をインストールします。SDK には、Azure QuickStart サンプル プロジェクト、Azure ストレージ エミュレーター、および [Azure Storage Client Library for .NET](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx) が含まれます。
3. [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653) がコンピューターにインストールされていることを確認します。このチュートリアルで使用する Azure QuickStart サンプル プロジェクトで必要です。コンピューターにどのバージョンの .NET Framework がインストールされているか不明な場合、「[方法: インストールされている .NET Framework バージョンを確認する](https://msdn.microsoft.com/vstudio/hh925568.aspx)」を参照してください。または、[**スタート**] をクリックするか Windows キーを押し、「**コントロール パネル**」と入力します。次に、[**プログラム**]、[**プログラムと機能**] の順にクリックして、インストールされているプログラムの一覧に .NET Framework 4.5 が含まれることを確認します。

最新バージョンの Azure Storage Client Library のバイナリは、[NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) で入手できます。


## Azure ストレージ エミュレーターで最初の Azure Storage アプリケーションをローカルに実行する

Azure Storage を使用するアプリケーションを開発するときは、[Azure ストレージ エミュレーター](storage-use-emulator.md)で実行できます。ストレージ エミュレーターでは、Azure の BLOB、キュー、テーブル サービスを開発用にエミュレートするローカル環境が利用できます。ストレージ エミュレーターを使用すると、Azure サブスクリプションまたはストレージ アカウントを作成せずにストレージ アプリケーションをローカルでテストでき、コストもかかりません。

それでは、Visual Studio で Azure QuickStarts サンプル プロジェクトの 1 つを使用する簡単な Azure Storage アプリケーションを作成しましょう。このチュートリアルでは、**Azure BLOB ストレージ**、**Azure テーブル ストレージ**、**Azure キュー ストレージ**の各サンプル プロジェクトに着目します。

1. Visual Studio を起動します。
2. **[ファイル]** メニューの **[新しいプロジェクト]** をクリックします。
3. [**新しいプロジェクト**] ダイアログ ボックスで、[**インストール済み**]、[**テンプレート**]、[**Visual C#**]、[**Cloud**]、[**Quick Starts**]、[**Data Services**] の順にクリックします。
	- 3\.a.次のテンプレートのいずれかを選択します。Azure BLOB ストレージ、Azure テーブル ストレージ、または Azure Storage キュー。 
	- 3\.b.ターゲット フレームワークとして **.NET Framework 4.5** が選択されていることを確認します。	
	- 3\.c.プロジェクトの名前を指定し、次のように新しい Visual Studio ソリューションを作成します。
	
	![Azure QuickStarts][Image1]

アプリケーションを実行する前に、ソース コードを確認できます。コードを確認するには、Visual Studio の [**表示**] メニューにある [**ソリューション エクスプローラー**] を選択します。その後、Program.cs ファイルをダブルクリックします。

次に、Azure ストレージ エミュレーターでサンプル アプリケーションを実行します。

1.	[**スタート**] ボタンを押すか Windows キーを押し、「*Azure ストレージ エミュレーター*」を検索してアプリケーションを開始します。
2.	Visual Studio で、[**ビルド**] メニューにある [**ソリューションのビルド**] をクリックします。 
3.	[**デバッグ**] メニューで、**F11** キーを押してソリューションを段階的に実行するか、**F5** キーを押してソリューションを開始から終了まで実行します。

## クラウドの Azure Storage で最初の Azure Storage アプリケーションを実行する

クラウドの Azure Storage に対して実行するには、Azure サブスクリプションとストレージ アカウントが必要です (既に持っていない場合)。

- Azure サブスクリプションを入手するには、[無料評価版](http://azure.microsoft.com/pricing/free-trial/)、[購入オプション](http://azure.microsoft.com/pricing/purchase-options/)、[メンバー プラン](http://azure.microsoft.com/pricing/member-offers/) (MSDN、Microsoft Partner Network、BizSpark、その他の Microsoft プログラムのメンバーの場合) の各ページをご覧ください。
- Azure でストレージ アカウントを作成するには、「[ストレージ アカウントを作成、管理、または削除する方法](storage-create-storage-account.md)」をご覧ください

アカウントができたら、Visual Studio で Azure QuickStarts サンプル プロジェクトの 1 つを使用する簡単な Azure Storage アプリケーションを作成できます。このチュートリアルでは、**Azure BLOB ストレージ**、**Azure テーブル ストレージ**、**Azure キュー ストレージ**の各サンプル プロジェクトに着目します。

1. Visual Studio を起動します。
2. **[ファイル]** メニューの **[新しいプロジェクト]** をクリックします。
3. [**新しいプロジェクト**] ダイアログ ボックスで、[**インストール済み**]、[**テンプレート**]、[**Visual C#**]、[**Cloud**]、[**Quick Starts**]、[**Data Services**] の順にクリックします。
	- 3\.a.次のテンプレートのいずれかを選択します。Azure BLOB ストレージ、Azure テーブル ストレージ、または Azure Storage キュー。
	- 3\.b.ターゲット フレームワークとして **.NET Framework 4.5** が選択されていることを確認します。
	- 3\.c.プロジェクトの名前を指定し、新しい Visual Studio ソリューションを作成します。 

アプリケーションを実行する前に、ソース コードを確認できます。コードを確認するには、Visual Studio の [**表示**] メニューにある [**ソリューション エクスプローラー**] を選択します。その後、Program.cs ファイルをダブルクリックします。

次に、サンプル アプリケーションを実行します。

1.	Visual Studio で、[**表示**] メニューにある [**ソリューション エクスプローラー**] を選択します。App.config ファイルをダブルクリックし、次の Azure SDK ストレージ エミュレーターの接続文字列をコメント アウトします。

	`<!--<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>-->`

2.	Azure Storage サービスの接続文字列をコメント解除し、次のようにストレージ アカウント名とアクセス キーを App.config ファイルに入力します。`<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]"`

	ストレージ アカウント名とアクセス キーを見つけるには、「[ストレージ アカウントとは](storage-whatis-account.md)」をご覧ください。

3.	App.config ファイルにストレージ アカウント名とアクセス キーを入力した後、[**ファイル**] メニューの [**すべてを保存**] をクリックし、すべてのプロジェクト ファイルを保存します。
4.	[**ビルド**] メニューの、[**ソリューションのビルド**] をクリックします。
5.	[**デバッグ**] メニューで、**F11** キーを押してソリューションを段階的に実行するか、**F5** キーを押してソリューションを実行します。


## 次のステップ

Azure Storage の詳細については、次のリソースをご覧ください。

* [Microsoft Azure Storage の概要](storage-introduction.md)
* [.NET から BLOB ストレージを使用する方法](storage-dotnet-how-to-use-blobs.md)
* [.NET からテーブル ストレージを使用する方法](storage-dotnet-how-to-use-tables.md)
* [.NET からキュー ストレージを使用する方法](storage-dotnet-how-to-use-queues.md)
* [Azure Storage のドキュメント](http://azure.microsoft.com/documentation/services/storage/)
* [Azure Storage Client Library](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
* [Azure Storage REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[Image1]: ./media/storage-getting-started-guide/QuickStart.png
 

<!---HONumber=July15_HO3-->