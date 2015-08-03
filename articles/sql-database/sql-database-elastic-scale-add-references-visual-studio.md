<properties 
	pageTitle="Visual Studio プロジェクトに Azure SQL DB の柔軟な拡張の参照を追加する" 
	description="Nuget を使用して Elastic Scale API に対する .NET 参照を Visual Studio プロジェクトに追加する方法。" 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/16/2015" 
	ms.author="sidneyh"/>

# 方法: エラスティック データベース クライアント ライブラリの参照を Visual Studio プロジェクトに追加する 

### 前提条件: 

- Visual Studio 向けの [NuGet Visual Studio 拡張機能ギャラリー](http://docs.nuget.org/docs/start-here/installing-nuget)をインストールします。 

### エラスティック データベース クライアント ライブラリの参照を Visual Studio プロジェクトに追加するには 

1. 既存のプロジェクトを開くか、**[ファイル]**、**[新規]**、**[プロジェクト]** の順に選択し、[新しいプロジェクト] ダイアログ ボックスで新しいプロジェクトを作成します。 
2. ソリューション エクスプ ローラーで、**[参照]** を右クリックして、**[NuGet パッケージの管理]** を選択します。
3. [NuGet パッケージの管理] ウィンドウの左側にあるメニューで、**[オンライン]**、次に **nuget.org** または "All" を選択します。 
4. **[オンラインで検索]** ダイアログ ボックスで、「**Elastic Database**」と入力し、**[Elastic Database クライアント ライブラリ]** を選択して、**[インストール]** をクリックします。

	![オンラインで検索する][1]
4. ライセンスを確認し、**[同意する]** をクリックします。 

これでクライアント ライブラリの参照がプロジェクトに追加されました。

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-add-references-visual-studio/search-online.png
<!--anchors-->

<!---HONumber=July15_HO4-->