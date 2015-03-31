<properties 
	pageTitle="5 分で始める、Azure の BLOB、テーブル、キューの使用" 
	description="Azure QuickStarts と Visual Studio を使用して、Microsoft Azure の BLOB、テーブル、キューをすばやく導入する方法について説明します。" 
	services="storage" 
	documentationCenter=".net" 
	authors="Selcin" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="02/18/2015" 
	ms.author="selcint"/>

# 5 分で始める、Azure の BLOB、テーブル、キューの使用 

このチュートリアルでは、Visual Studio で簡単な Azure アプリケーションを開発することにより、**Azure Storage の BLOB**、**テーブル**、**キュー**に対してすばやくプログラミングを行う方法を説明します。 

チュートリアルには、Azure Storage をすばやく導入するための、2 つの主なシナリオが含まれています。

- Azure ストレージ エミュレーターで最初の Azure Storage アプリケーションを実行する
- Azure Storage サービスで最初の Azure Storage アプリケーションを実行する

コードの説明に入る前に Azure Storage について学習するには、「[次のステップ][]」をご覧ください。

## Azure ストレージ エミュレーターで最初の Azure Storage アプリケーションを実行する

このセクションでは、[Azure ストレージ エミュレーター](https://msdn.microsoft.com/library/azure/hh403989.aspx)にアクセスするサンプル アプリケーションを開発することにより、**Azure Storage の BLOB**、**テーブル**、**キュー**に対してプログラミングを行う方法を説明します。Microsoft Azure ストレージ エミュレーターでは、Azure の BLOB、キュー、テーブル サービスを開発用にエミュレートするローカル環境が利用できます。ストレージ エミュレーターを使用すると、ストレージ サービスに対するアプリケーションのローカル テストが、コストをかけずに実施できます。

このセクションを完了するには、前提条件である次のタスクを必ず先に実行してください。

1. アプリケーションをコンパイルし、ビルドするには、コンピューターに [Visual Studio](http://www.visualstudio.com/visual-studio-homepage-vs.aspx) をインストールしている必要があります。Visual Studio をインストールしていない場合、[Azure SDK 2.5 for Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) 以降をインストールすると、Visual Studio Express for Web がインストールできます。 
2. コンピューターに [Azure SDK 2.5 for Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) 以降がインストールされていることをご確認ください。これには、Azure QuickStart サンプル プロジェクトや [Azure Storage Client Library for .NET](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx) が含まれています。  
3. コンピューターに [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653) がインストールされているかご確認ください。Azure QuickStart サンプル プロジェクトにはこれが必要です。コンピューターにどのバージョンの .NET Framework がインストールされているか不明な場合、「[方法: インストールされている .NET Framework バージョンを確認する](https://msdn.microsoft.com/vstudio/hh925568.aspx)」をご覧ください。または、**[スタート]** をクリックするか Windows キーを押し、**「コントロール パネル」**と入力します。その後、**[プログラム]**、**[プログラムと機能]** の順にクリックします。インストールされているすべてのプログラムの中に .NET Framework 4.5 が表示されているか確認します。

では、Visual Studio で Azure QuickStarts サンプル プロジェクトの 1 つを使用する簡単な Azure Storage アプリケーションを作成しましょう。このチュートリアルでは、**Azure BLOB ストレージ**、**Azure テーブル ストレージ**、**Azure Storage キュー**のサンプル プロジェクトに着目します。各サンプル プロジェクトに対して次の手順が適用できますが、手順 3.a では別々のテンプレートを選択します。

1. **[スタート]** をクリックするか Windows キーを押し、「Visual Studio 2013」または「VS Express 2013 for Web」と入力します。開始するプログラムをクリックします。
2. **[ファイル]** メニューの **[新しいプロジェクト]** をクリックします。
3. **[新しいプロジェクト]** ダイアログ ボックスで、**[インストール済み]**、**[テンプレート]**、**[Visual C#]**、**[Cloud]**、**[Quick Starts]**、**[Data Services]** の順にクリックします。
	- 3.a.次のテンプレートのいずれかを選択します。Azure BLOB ストレージ、Azure テーブル ストレージ、または Azure Storage キュー。 
	- 3.b.ターゲット フレームワークとして **.NET Framework 4.5** が選択されていることを確認します。	
	- 3.c.選択するテンプレートに応じて、**DataBlobStorage**、**DataTableStorage**、**DataStorageQueue** などのように、アプリケーションに名前を付けます。**[OK]** をクリックします。これにより、新しい Visual Studio ソリューションが作成されます。次のスクリーンショットを例としてご確認ください。
	
	![Azure QuickStarts][Image1]

アプリケーションを実行する前に、ソース コードを確認して Azure Storage に対するプログラミングの方法について学ぶことをお勧めします。コードを確認するには、Visual Studio の **[表示]** メニューにある **[ソリューション エクスプローラー]** を選択します。その後、Program.cs ファイルをダブルクリックします。 

では、Azure SDK の一部としてインストールする [Azure ストレージ エミュレーター](https://msdn.microsoft.com/library/azure/hh403989.aspx)を使用することにより、サンプル アプリケーションを実行します。

1.	Azure ストレージ エミュレーターを起動します。**[スタート]** をクリックするか Windows キーを押し、「Azure ストレージ エミュレーター」と入力してこれを検索します。アプリケーションの一覧からこれを選択し、起動します。
2.	Visual Studio で、**[ビルド]** メニューにある **[ソリューションのビルド]** をクリックします。 
3.	**[デバッグ]** メニューで、**F11** キーを押してソリューションを段階的に実行するか、**F5** キーを押してソリューションを実行します。

## Azure Storage サービスで最初の Azure Storage アプリケーションを実行する
このセクションでは、[Azure Storage サービス](http://azure.microsoft.com/documentation/services/storage/)にアクセスするサンプル アプリケーションを開発することにより、**Azure Storage の BLOB**、**テーブル**、**キュー**に対してプログラミングを行う方法を説明します。

このセクションを完了するには、前提条件である次のタスクを必ず先に実行してください。

1. アプリケーションをコンパイルし、ビルドするには、コンピューターに [Visual Studio](http://www.visualstudio.com/visual-studio-homepage-vs.aspx) をインストールしている必要があります。Visual Studio をインストールしていない場合、[Azure SDK 2.5 for Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) 以降をインストールすると、Visual Studio Express for Web がインストールできます。 
2. コンピューターに [Azure SDK 2.5 for Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) 以降がインストールされていることをご確認ください。これには、Azure QuickStart サンプル プロジェクトや [Azure Storage Client Library for .NET](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx) が含まれています。  
3. コンピューターに [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653) がインストールされているかご確認ください。Azure QuickStart サンプル プロジェクトにはこれが必要です。コンピューターにどのバージョンの .NET Framework がインストールされているか不明な場合、「[方法:インストールされている .NET Framework バージョンを確認する](https://msdn.microsoft.com/vstudio/hh925568.aspx)」をご覧ください。または、**[スタート]** をクリックするか Windows キーを押し、**「コントロール パネル」**と入力します。その後、**[プログラム]**、**[プログラムと機能]** の順にクリックします。インストールされているすべてのプログラムの中に .NET Framework 4.5 が表示されているか確認します。
4.	Azure サブスクリプションを入手 (まだ入手していない場合) し、**Standard Storage** アカウントの作成も行います。
	- TAzure サブスクリプションを入手するには、[無料評価版](http://azure.microsoft.com/pricing/free-trial/)、[購入オプション](http://azure.microsoft.com/pricing/purchase-options/)、[メンバー プラン](http://azure.microsoft.com/pricing/member-offers/) (MSDN、Microsoft Partner Network、BizSpark、その他の Microsoft プログラムのメンバーの場合) の各ページをご覧ください。
	- Azure で **Standard Storage** アカウントを作成するには、[ストレージ アカウントを作成、管理、または削除する方法]に関するページをご覧ください(./storage-create-storage-account.md)。**注:** Azure のストレージ アカウントには、次の 2 種類があります。Standard Storage アカウントと Premium Storage アカウント。Standard Storage アカウントでは、Azure の BLOB、テーブル、キュー ストレージにアクセスできます。Premium Storage アカウントは、現在、Azure 仮想マシンが使用するディスクにデータを格納する場合にのみ利用できます。詳細については、「[Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ]」をご覧ください(./storage-premium-storage-preview-portal.md)。

では、Visual Studio で Azure QuickStarts サンプル プロジェクトの 1 つを使用する簡単な Azure Storage アプリケーションを作成しましょう。このチュートリアルでは、**Azure BLOB ストレージ**、**Azure テーブル ストレージ**、**Azure Storage キュー**のサンプル プロジェクトに着目します。各サンプル プロジェクトに対して次の手順が適用できますが、手順 3.a では別々のテンプレートを選択します。

1. **[スタート]** をクリックするか Windows キーを押し、「Visual Studio 2013」または「VS Express 2013 for Web」と入力します。開始するプログラムをクリックします。
2. **[ファイル]** メニューの **[新しいプロジェクト]** をクリックします。
3. **[新しいプロジェクト]** ダイアログ ボックスで、**[インストール済み]**、**[テンプレート]**、**[Visual C#]**、**[Cloud]**、**[Quick Starts]**、**[Data Services]** の順にクリックします。
	- 3.a.次のテンプレートのいずれかを選択します。Azure BLOB ストレージ、Azure テーブル ストレージ、または Azure Storage キュー。 
	- 3.b.ターゲット フレームワークとして **.NET Framework 4.5** が選択されていることを確認します。
	- 3.c.選択するテンプレートに応じて、**DataBlobStorage**、**DataTableStorage**、**DataStorageQueue** などのように、アプリケーションに名前を付けます。**[OK]** をクリックします。これにより、新しい Visual Studio ソリューションが作成されます。 

アプリケーションを実行する前に、ソース コードを確認して Azure Storage に対するプログラミングの方法について学ぶことをお勧めします。コードを確認するには、Visual Studio の **[表示]** メニューにある **[ソリューション エクスプローラー]** を選択します。その後、Program.cs ファイルをダブルクリックします。 

では、サンプル アプリケーションを実行します。

1.	Visual Studio で、**[表示]** メニューにある **[ソリューション エクスプローラー]** を選択します。App.config ファイルをダブルクリックし、次の Azure SDK ストレージ エミュレーターの接続文字列をコメント アウトします。 

	`<!--<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>-->`

2.	Azure Storage サービスの接続文字列をコメント解除し、次のようにストレージ アカウント名とアクセス キーを App.config ファイルに入力します。
	`<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]"` 

	ストレージ アカウント名とアクセス キーを見つけるには、[ストレージ アカウント]に関するページをご覧ください(./storage-whatis-account.md)。 

3.	App.config ファイルにストレージ アカウント名とアクセス キーを入力した後、**[ファイル]** メニューの **[すべてを保存]** をクリックし、すべてのプロジェクト ファイルを保存します。 
4.	**[ビルド]** メニューの、**[ソリューションのビルド]** をクリックします。 
5.	**[デバッグ]** メニューで、**F11** キーを押してソリューションを段階的に実行するか、**F5** キーを押してソリューションを実行します。


## 次のステップ
このチュートリアルでは、Azure BLOB ストレージ、Azure テーブル ストレージ、Azure Storage キューに対するプログラミングの方法を学びました。 

さらに詳細を学びたい場合は、以下のリンクをご覧ください。

* [Microsoft Azure Storage の概要](./storage-introduction.md)
* [.NET から BLOB ストレージを使用する方法](./storage-dotnet-how-to-use-blobs.md)
* [.NET からテーブル ストレージを使用する方法](./storage-dotnet-how-to-use-tables.md)
* [.NET からキュー ストレージを使用する方法](./storage-dotnet-how-to-use-queues.md)
* [Azure ストレージのドキュメント](http://azure.microsoft.com/documentation/services/storage/)
* [Azure Storage の MSDN リファレンス](http://msdn.microsoft.com/library/azure/gg433040.aspx)
* [Azure Storage Client Library](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
* [Azure Storage REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[Image1]: ./media/storage-getting-started-guide/QuickStart.png


<!--HONumber=47-->
