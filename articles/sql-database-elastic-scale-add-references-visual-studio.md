<properties title="Add Azure SQL DB Elastic Scale References to a Visual Studio Project" pageTitle="Visual Studio プロジェクトに Azure SQL DB の柔軟な拡張の参照を追加する" description="Nuget を使用して Elastic Scale API に対する .NET 参照を Visual Studio プロジェクトに追加する方法。" metaKeywords="Azure SQL Database, elastic scale, Nuget references" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#方法: Visual Studio プロジェクトに Azure SQL DB の柔軟な拡張の参照を追加する

###前提条件: 

- Visual Studio 向けの [NuGet Visual Studio 拡張機能ギャラリー](http://docs.nuget.org/docs/start-here/installing-nuget)をインストールします。

###Visual Studio に柔軟な拡張の参照を追加するには 

1. **[ファイル]**、**[新規]**、**[プロジェクト]** の順に選択し、[新しいプロジェクト] ダイアログ ボックスで新しいプロジェクトを作成します。
2. ソリューション エクスプ ローラーで、**[参照]** を右クリックして、**[NuGet パッケージの管理]** を選択します。
3. [NuGet パッケージの管理] ウィンドウの左側にあるメニューで、**[オンライン]**、次に **nuget.org** または "All" を選択します。
4. **[オンラインで検索]** ダイアログ ボックスで、「**柔軟な拡張**」と入力し、**[柔軟な拡張のクライアント ライブラリ]** を選択して、**[インストール]** をクリックします。
4. ライセンスを確認し、**[同意する]** をクリックします。 

これで、Azure SQL Database の柔軟な拡張の参照がプロジェクトに追加されました。 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]
