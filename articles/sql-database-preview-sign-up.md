<properties 
	pageTitle="チュートリアル:最新の SQL Database 更新プログラム V12 (プレビュー) を有効化する" 
	description="新しい Azure ポータル UI を使用して Azure SQL Database V12 のプレビュー リリースを有効化する手順を説明します。" 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jhubbard, jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/29/2015" 
	ms.author="genemi"/>


# チュートリアル:最新の SQL Database 更新プログラム V12 (プレビュー) を有効化する

このトピックでは、2014 年 12 月に Microsoft が最初のリリースを公開した Azure SQL Database 更新プログラム V12 (プレビュー) を有効化するための手順を説明します。

最新の V12 プレビューを試すには、Microsoft Azure のサブスクリプションか、少なくとも[無料評価版](http://azure.microsoft.com/pricing/free-trial/)のサブスクリプションが必要です。

V12 プレビューは、新しいプレビュー版の Microsoft Azure 管理ポータル ([http://portal.azure.com/](http://portal.azure.com/)) を使用して有効化できます。サブスクリプションに対して V12 プレビューを有効化すると、Azure ポータルで V12 プレビューの作成と更新オプションがロック解除されます。これでユーザーは、サーバー ブレードやデータベース ブレードから[作成](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/)や[アップグレード](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/)のワークフローを開始できます。

> [AZURE.NOTE]
> プレビューへのアップグレードには、テスト データベース、データベースのコピー、新しいデータベースを使用すると便利です。ビジネスで利用している本番データベースの場合は、プレビュー期間が終了するまで待つことをお勧めします。

プレビューの詳細については、「[Azure SQL Database 更新プログラム V12 (プレビュー) へのアップグレードの計画と準備をする](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/)」をご覧ください。


## セキュリティの承認

最初のステップとして、サブスクリプションに対して V12 プレビューを有効化するために必要な承認を得ていることを確認します。V12 プレビュー オプションを有効化しようとすると、承認チェックが実行されて、サブスクリプション内で必要な権限があることが確認されます。

 プレビューを試すには、次のいずれかの承認を得ている必要があります。

- サブスクリプションの所有者
- サブスクリプションの共同管理者

Azure アカウントの詳細については、「[アカウント、サブスクリプション、管理ロールを管理する](http://msdn.microsoft.com/library/hh531793.aspx)」をご覧ください。

## B. ポータル UI での手順

このセクションでは、V12 プレビュー オプションを有効化するために Azure ポータル UI で 1 回だけ実行できる手順を説明します。オプションを一度有効にすると、以降は使用可能な状態に維持されます。

すべての有効化シナリオで基本的な考え方は同じです。初めて[新しい SQL Database サーバーを作成](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/)するとき、**[最終更新 (プレビュー)]** というラベルの付いたブレードが表示されます。そのブレードにあるチェック ボックスをオンにすると、V12 プレビュー バージョンを使用する特権を有効化できます。特権を有効にすると、以降このチェック ボックスは表示されません。代わりに **[はい|いいえ]** を切り替えるコントロールが表示され、それを使って新しいサーバーで V12 プレビューを使用するかどうかを指定できます。**[いいえ]** を選択すると、V11 サーバーが作成されます (SELECT @@VERSION; で確認できます)。

### B.1 V12 プレビュー バージョンに関する [はい|いいえ] のコントロール

V12 プレビュー特権を有効化した後は、次に示すポータルのスクリーンショットで丸く囲んだ、**[はい|いいえ]** のコントロールが表示されます。

![YesNoOptionForTheV12Preview][Image1]


## C. 次の手順

この後の手順として、次に示すトピックでは、V12 プレビューの使用方法をご覧ください。

- [最新の SQL データベースの更新プログラムの V12 (プレビュー) を作成する](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/)
- [最新の SQL データベースの更新プログラムの V12 (プレビュー) にアップグレードする](http://azure.microsoft.com/documentation/articles/sql-database-preview-upgrade/)

> [AZURE.NOTE]
> プレビューへのアップグレードには、テスト データベース、データベースのコピー、新しいデータベースを使用すると便利です。ビジネスで利用している本番データベースの場合は、プレビュー期間が終了するまで待つことをお勧めします。


<!-- References, Images. -->
[Image1]: ./media/sql-database-preview-sign-up/V12Preview-YesNo-Option-New-SQLDatabase-Server-Newserver-Screenshot-e23.png


<!-- EOF -->

<!--HONumber=47-->
