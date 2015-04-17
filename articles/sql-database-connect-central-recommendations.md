<properties 
	pageTitle="Azure SQL Database との接続:中心となる推奨事項" 
	description="Azure SQL Database に接続するための ADO.NET や PHP などのさまざまなドライバーに関する、さらに多くの固有のトピックへのリンクが提供されている中心トピッ	ク。" 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/19/2015" 
	ms.author="genemi"/>


#SQL Database への接続:中心となる推奨事項


<!--
GeneMi , 2015-March-19 Thursday 15:41pm
sql-database-connect-central-recommendations.md
sql-database-connect-*.md

Re SqlException, not .HResult, rather .Number.
-->


このトピックでは、Azure SQL Database に接続して対話的作業を実行するために使用できるさまざまなテクノロジに関するコード サンプルへのリンクが提供されています。それらのテクノロジには、Enterprise Library、JDBC、PHP などがあります。特定の接続テクノロジに依存することなく一般に適用される推奨事項が示されています。


##テクノロジに依存しない推奨事項


このセクションの情報は、SQL Database に接続するために使用する特定のテクノロジとは関係なく適用されます。


- 「[プログラムによる Azure SQL Database への接続のガイドライン](http://msdn.microsoft.com/library/azure/ee336282.aspx)」 - 以下の話題が含まれています。
 - ポート
 - ファイアウォール
 - 接続文字列
- [Azure SQL Database のリソース管理](https://msdn.microsoft.com/library/azure/dn338083.aspx) - 以下の話題が含まれています。
 - リソース ガバナンス
 - 制限の適用
 - 調整


どの接続テクノロジ、さらには個々のデータベースを使用するとしても、SQL Database サーバーのファイアウォールの特定の設定値が問題になります。


- [Azure SQL Database ファイアウォール](https://msdn.microsoft.com/library/azure/ee621782.aspx)


###クイック推奨事項


####接続


- クライアント接続ロジックの中で、タイムアウトが 30 秒になるように既定値をオーバーライドします。
 - 既定では 15 秒ですが、インターネットに依存する接続の場合、それでは短すぎます。
- [接続プール](http://msdn.microsoft.com/library/8xx3tyca.aspx)を使用している場合、プログラムで接続をアクティブに使用しておらず、再使用の準備をしている時間は、接続を閉じてください。
 - プログラムにおいて別の操作を休止することなく即座に実行するために接続を再使用するというのでない限り、以下のパターンが推奨されています。
<br/><br/>接続を開きます。
<br/>接続を通じて操作を 1 つ実行します。
<br/>接続を閉じます。<br/><br/>
- 接続ロジックでは、一時エラーの場合にのみ、再試行ロジックを使用します。SQL Database を使用する場合、接続を開いたりクエリを発行したりする試行は、さまざまな理由で失敗する可能性があります。
 - 失敗が持続する場合、理由として考えられるのは、接続文字列の形式が間違っていることです。
 - 失敗の 1 つの一時的理由として考えられるのは、Azure SQL Database システムで負荷全体のバランス調整が必要であるということです。一時的理由はやがて解消されるため、プログラムでは再試行をする必要があります。
 - クエリを再試行する場合、まず接続を閉じた後、別の接続を開きます。
- [Azure SQL Database ファイアウォール](http://msdn.microsoft.com/library/ee621782.aspx)で、ポート 1433 の TCP 発信が許可されていることを確認してください。
 - [ファィアウォール](http://msdn.microsoft.com/library/azure/ee621782.aspx)の設定値は、SQL Database サーバーについて、あるいは個々のデータベースについて構成することができます。


####認証


- Windows の認証ではなく、SQL Database 認証を使用してください。
- 指定なしで既定値の  *master* データベースとするのではなく、特定のデータベースを明示的に指定してください。
- *@yourservername* の接尾辞としてユーザー名を指定することが必要な場合と、接尾辞を付けてはならない場合とがあります。それは、ツールや API の作成方法に応じて異なります。
 - 個々のテクノロジのそれぞれの詳細を確認してください。
- [包含データベース](http://msdn.microsoft.com/library/ff929071.aspx)でのユーザーを指定することにより接続します。
 - このアプローチでは、マスター データベースにログインする必要がなく、パフォーマンスと拡張性が向上します。
 - Transact-SQL の **USE myDatabaseName;** ステートメントを SQL Database に対して使用することはできません。


###一時エラー


SQL Database 接続エラーの中には持続的なものがあります。その場合、接続を即座に再試行しても意味がありません。一方、一時的なエラーの場合、プログラムで少なめの回数の再試行を自動的に実行することが推奨されます。次に例を示します。


- *持続的:*接続試行で SQL Database サーバー名のスペルを間違えた場合、再試行しても意味がありません。
- *一時的:*SQL Database との間に有効な接続があり、それが後に Azure スロットリングにより、あるいはロード バランス調整システムによって強制終了された場合は、再接続を試行することが推奨されます。


SQL Database の呼び出しによってスローされた [SqlException](https://msdn.microsoft.com/library/system.data.sqlclient.sqlexception.aspx) には、その **Number** プロパティに数値エラー コードが含まれています。そのエラー コードが一時エラーのものである場合、プログラムで呼び出しを再試行してください。


- [エラー メッセージ (Azure SQL Database)](http://msdn.microsoft.com/library/azure/ff394106.aspx) -  **Connection-Loss Errors** セクションには、再試行の正当な根拠となる一時エラーのリストが示されています。
 - たとえば、エラー番号 40613 が発生した場合は、再試行してください。その場合、次のようなメッセージが表示されます。<br/>*サーバー  'theserver' 上のデータベース  'mydatabase' は現在使用できません。*


接続エラー (持続的または一時的) が発生してヘルプが必要な場合、次のものを参照してください。


- [Azure SQL Database との接続の問題のトラブルシューティング](http://support.microsoft.com/ja-jp/kb/2980233/ja-jp)


##テクノロジ


以下のトピックには、いくつかの接続テクノロジのためのコード サンプルへのリンクが含まれています。


- [Azure SQL Database 開発:操作方法に関するトピック](http://msdn.microsoft.com/library/azure/ee621787.aspx)


ADO.NET と Enterprise Library および一時障害処理クラスについて詳しくは、以下のものを参照してください。


- [方法:Azure SQL Database への信頼性の高い接続](http://msdn.microsoft.com/library/azure/dn864744.aspx)


Elastic Scale については、以下のものを参照してください。


- [Azure SQL Database Elastic Scale プレビューの使用](sql-database-elastic-scale-get-started.md)
- [データ依存ルーティング](sql-database-elastic-scale-data-dependent-routing.md)


##未完または古い投稿


このセクションに含まれるリンクは、ブログ投稿などのソースに対するものなので、未完の状態であったり古いものであったりする可能性があります。それでも、背景情報として一定の価値があるかもしれません。


- [Microsoft Azure SQL Database における一時障害の再試行ロジック](http://social.technet.microsoft.com/wiki/contents/articles/4235.retry-logic-for-transient-failures-in-windows-azure-sql-database.aspx)

<!-- -->


<!--HONumber=49-->