<properties 
   pageTitle="Visual Studio の接続済みサービスを使用して Azure Storage を追加する | Microsoft Azure"
   description="Visual Studio の [接続済みサービスの追加] ダイアログ ボックスを使用してアプリに Azure Storage を追加する"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="mobile"
   ms.date="06/01/2016"
   ms.author="tarcher" />

# Visual Studio 接続済みサービスを使用した Azure ストレージの追加

## 概要

Visual Studio 2015 では、**[接続済みサービスの追加]** ダイアログを使用して、Azure Storage に C# クラウド サービス、.NET バックエンド モバイル サービス、ASP.NET Web サイトまたはサービス、ASP.NET 5 サービス、あるいは Azure Web ジョブ サービスを接続できます。接続済みサービス機能は、必要なすべての参照と接続コードを追加し、構成ファイルを適切に変更します。また、ダイアログでは、次の手順が BLOB ストレージ、キュー、およびテーブルの開始であることを知らせるドキュメントが示されます。

## サポートされているプロジェクトの種類

[接続済みサービス] ダイアログを使用して、次の種類のプロジェクトで Azure Storage に接続することができます。

- ASP.NET Web プロジェクト

- ASP.NET 5 プロジェクト

- .NET クラウド サービスの Web ロールと worker ロール プロジェクト

- .NET Mobile Services プロジェクト

- Azure WebJob プロジェクト


## [接続済みサービス] ダイアログを使用して Azure Storage に接続する

1. Azure アカウントがあることを確認します。Azure アカウントを持っていない場合、[無料試用版](http://go.microsoft.com/fwlink/?LinkId=518146)でサインアップできます。Azure アカウントを取得したら、ストレージ アカウントを作成し、モバイル サービスを作成して、Azure Active Directory を構成することができます。

1. Visual Studio でプロジェクトを開き、ソリューション エクスプローラーで **[参照]** ノードのコンテキスト メニューを開いて、**[接続済みサービスの追加]** を選択します。

    ![Adding a connected service](./media/vs-azure-tools-connected-services-storage/IC796702.png)

1. **[接続済みサービスの追加]** ダイアログ ボックスで、**[Azure Storage]** を選択し、**[構成]** をクリックします。Azure にまだログインしていない場合は、ログインを促すメッセージが表示される場合があります。

    ![Add Connected Service dialog box - Storage](./media/vs-azure-tools-connected-services-storage/IC796703.png)

1. **[Azure Storage]** ダイアログ ボックスで、既存のストレージ アカウントを選択し、**[追加]** を選択します。

    新しいストレージ アカウントを作成する必要がある場合は、次の手順に進みます。必要ない場合は、手順 6 に進みます。

    ![Azure Storage dialog box](./media/vs-azure-tools-connected-services-storage/IC796704.png)

1. 新しいストレージ アカウントを作成するには

    1. [Azure Storage] ダイアログ ボックスの下部にある **[ストレージ アカウントの作成]** ボタンをクリックします。

    1. **[ストレージ アカウントの作成** ] ダイアログ ボックスに入力し、**[作成]** ボタンをクリックします。
    
        ![Azure Storage dialog box](./media/vs-azure-tools-connected-services-storage/create-storage-account.png)

        **[Azure Storage]** ダイアログ ボックスに戻ると、新しいストレージが一覧に表示されます。

    1. 一覧の中から新しいストレージを選択し、**[追加]** を選択します。

1. WebJob プロジェクトの [サービス参照] ノードの下に、ストレージの接続済みサービスが表示されます。

    ![Azure storage in web jobs project](./media/vs-azure-tools-connected-services-storage/IC796705.png)

1. 表示された作業の開始ページを確認し、プロジェクトがどのように変更されたかを確認します。作業の開始ページは、接続済みサービスを追加したかどうかに関係なく、ブラウザーに表示されます。推奨される次の手順とコード例を確認したり、[変更内容] ページに切り替えて、プロジェクトに追加された参照と、コードと構成ファイルがどのように変更されたかを確認したりすることができます。

## プロジェクトを変更する方法

ダイアログが完了すると、Visual Studio は参照を追加し、特定の構成ファイルを変更します。特定の変更はプロジェクトの種類によって異なります。

 - ASP.NET プロジェクトの場合は、[変更内容 – ASP.NET プロジェクト](http://go.microsoft.com/fwlink/p/?LinkId=513126)を参照してください。 
 - ASP.NET 5 プロジェクトの場合は、[変更内容 – ASP.NET 5 プロジェクト](http://go.microsoft.com/fwlink/p/?LinkId=513124)を参照してください。 
 - クラウド サービス プロジェクト (Web ロールおよび worker ロール) の場合は、[変更内容 – クラウド サービス プロジェクト](http://go.microsoft.com/fwlink/p/?LinkId=516965)を参照してください。 
 - WebJob プロジェクトの場合は、[変更内容 - WebJob プロジェクト](./storage/vs-storage-webjobs-what-happened.md)を参照してください。

## 次のステップ

1. 作業の開始のコード サンプルを使用して、必要なストレージの種類を作成し、ストレージ アカウントにアクセスするためのコードを記述し始めます。

1. 質問してヘルプを表示します。
     - [MSDN フォーラム: Azure Storage](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)

     - [Azure Storage チーム ブログ](http://blogs.msdn.com/b/windowsazurestorage/)

     - [Storage (azure.microsoft.com)](https://azure.microsoft.com/services/storage/)

     - [Storage のドキュメント (azure.microsoft.com)](https://azure.microsoft.com/documentation/services/storage/)

<!---HONumber=AcomDC_0608_2016-->