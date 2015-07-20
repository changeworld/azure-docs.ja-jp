<properties
	pageTitle="チュートリアル: 最新の SQL Database 更新プログラム V12 をアクティブ化する"
	description="新しい Microsoft Azure ポータルの UI を使用して、Azure SQL Database V12 を使用する手順を説明します。"
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management" 
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2015"
	ms.author="genemi"/>


# チュートリアル: 最新の SQL Database 更新プログラム V12 をアクティブ化する

このトピックでは、2014 年 12 月に Microsoft が最初のリリースを公開した Azure SQL Database V12 のオプションをアクティブ化するための手順を説明します。

最新の V12 を試すには、Microsoft Azure のサブスクリプションか、少なくとも[無料試用版](http://azure.microsoft.com/pricing/free-trial/)のサブスクリプションが必要です。

[以前の Azure ポータル](http://manage.windowsazure.com/)ではなく [http://portal.azure.com/](http://portal.azure.com/) の新しい Azure プレビュー ポータルを使用して、V12 をアクティブ化できます。サブスクリプションに対して V12 をアクティブ化すると、Azure プレビュー ポータルで V12 の作成とアップグレードのオプションがロック解除されます。これでユーザーは、サーバー ブレードやデータベース ブレードから[作成](sql-database-create.md)のワークフローを開始できます。

> [AZURE.NOTE]プレビューへのアップグレードには、テスト データベース、データベースのコピー、新しいデータベースを使用すると便利です。ビジネスで利用している本番データベースの場合は、プレビュー期間が終了するまで待つことをお勧めします。

V12 へのアップグレードの詳細については、「[Azure SQL Database V12 へのアップグレードの計画と準備をする](sql-database-v12-plan-prepare-upgrade.md)」を参照してください。


## A.セキュリティの承認

最初のステップとして、サブスクリプションに対して V12 をアクティブ化するために必要な承認を得ていることを確認します。V12 オプションをアクティブ化しようとすると、承認チェックが実行され、サブスクリプション内で必要な権限があることが確認されます。

 V12 を試すには、次のいずれかの承認を得ている必要があります。

- サブスクリプションの所有者
- サブスクリプションの共同管理者

Azure アカウントの詳細については、「[アカウント、サブスクリプション、管理ロールの管理](http://msdn.microsoft.com/library/hh531793.aspx)」を参照してください。

## B.Azure プレビュー ポータルの UI における手順

このセクションでは、V12 オプションをアクティブ化するために Azure プレビュー ポータルの UI で実行できるワンクリックの手順を説明します。オプションを一度有効にすると、以降は使用可能な状態に維持されます。

すべての有効化シナリオで基本的な考え方は同じです。初めて[新しい SQL Database サーバーを作成する](sql-database-create.md)とき、**[最終更新 (プレビュー)]** というラベルの付いたブレードが表示されます。そのブレードにあるチェック ボックスをオンにすると、V12 バージョンを使用する特権をアクティブ化できます。特権を有効にすると、以降このチェック ボックスは表示されません。代わりに **[はい|いいえ]** を切り替えるコントロールが表示され、それを使って新しいサーバーで V12 を使用するかどうかを指定できます。**[いいえ]** を選択すると、V11 サーバーが作成されます (SELECT @@VERSION; で確認できます)。

### B.1 V12 バージョンに関する [はい|いいえ] のコントロール

V12 オプションの特権をアクティブ化した後は、次に示す Azure プレビュー ポータルのスクリーンショットで丸く囲んだ、**[はい|いいえ]** のコントロールが表示されます。

![YesNoOptionForTheV12Preview][Image1]


## C.次の手順

この後の手順として、次のトピックで SQL Database V12 の使用方法について説明します。

- [SQL Database の更新プログラム V12 でデータベースを作成する](sql-database-create.md)


<!-- References, Images. -->
[Image1]: ./media/sql-database-v12-sign-up/V12Preview-YesNo-Option-New-SQLDatabase-Server-Newserver-Screenshot-e23.png

 

<!---HONumber=July15_HO2-->