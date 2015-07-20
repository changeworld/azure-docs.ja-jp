<properties 
	pageTitle="SQL Database への接続: リンク、ベスト プラクティスと設計のガイドライン" 
	description="ADO.NET および PHP などのテクノロジから Azure SQL Database に接続するクライアント プログラムのリンクおよび推奨事項のまとめが示されている開始ポイントのトピックです。" 
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
	ms.date="05/01/2015" 
	ms.author="genemi"/>


# SQL Database への接続: リンク、ベスト プラクティスと設計のガイドライン


このトピックは、Azure SQL Database にクライアント接続を開始するときにお勧めします。ここには、SQL Database に接続して対話的に作業するためのさまざまなテクノロジのコード サンプルへのリンクがあります。これらのテクノロジは、Enterprise Library、JDBC、PHP を含め、その他にもいくつかあります。接続テクノロジやプログラミング言語に関係なく、一般的に該当する推奨事項が示されています。


## テクノロジに依存しない推奨事項


このセクションの情報は、SQL Database に接続するために使用する特定のテクノロジとは関係なく適用されます。


- 「[プログラムによる Azure SQL Database への接続のガイドライン](http://msdn.microsoft.com/library/azure/ee336282.aspx)」 - 以下の話題が含まれています。
 - ポート
 - ファイアウォール
 - Connection strings
- 「[Azure SQL Database のリソース管理](https://msdn.microsoft.com/library/azure/dn338083.aspx)」 - 以下の話題が含まれています。
 - リソース ガバナンス
 - 制限の適用
 - 調整


どの接続テクノロジ、さらには個々のデータベースを使用するとしても、SQL Database サーバーのファイアウォールの特定の設定値が問題になります。


- [Azure SQL Database ファイアウォール](https://msdn.microsoft.com/library/azure/ee621782.aspx)


## 推奨事項: 接続


- クライアント接続ロジックの中で、タイムアウトが 30 秒になるように既定値をオーバーライドします。
 - 既定では 15 秒ですが、インターネットに依存する接続の場合、それでは短すぎます。
- [接続プール](http://msdn.microsoft.com/library/8xx3tyca.aspx)を使用している場合、プログラムで接続をアクティブに使用しておらず、再使用の準備をしている時間は、接続を閉じてください。
 - プログラムがその接続を別の操作のために休止せずに即座に再使用するというのでない限り、以下のパターンが推奨されています。<br/><br/>接続を開きます。<br/>その接続を使用して操作を 1 つ実行します。<br/>接続を閉じます。<br/><br/>
- 接続ロジックでは、一時エラーの場合にのみ、再試行ロジックを使用します。SQL Database を使用する場合、接続を開いたりクエリを発行したりする試行は、さまざまな理由で失敗する可能性があります。
 - 失敗が持続する場合、理由として考えられるのは、接続文字列の形式が間違っていることです。
 - 失敗の 1 つの一時的理由として考えられるのは、Azure SQL Database システムで負荷全体のバランス調整が必要であるということです。一時的理由はやがて解消されるため、プログラムでは再試行をする必要があります。
 - クエリを再試行する場合、まず接続を閉じた後、別の接続を開きます。
- [Azure SQL Database ファイアウォール](http://msdn.microsoft.com/library/ee621782.aspx)で、ポート 1433 の TCP 発信が許可されていることを確認してください。
 - [ファイアウォール](http://msdn.microsoft.com/library/azure/ee621782.aspx)の設定値は、SQL Database サーバーについて、あるいは個々のデータベースについて構成することができます。


## 推奨事項: 認証


- Windows の認証ではなく、SQL Database 認証を使用してください。
- 既定の*マスター* データベースではなく、特定のデータベースを明示的に指定してください。
- *@yourservername* の接尾辞としてユーザー名を指定することが必要な場合と、接尾辞を付けてはならない場合とがあります。それは、ツールや API の作成方法に応じて異なります。
 - 個々のテクノロジのそれぞれの詳細を確認してください。
- [包含データベース](http://msdn.microsoft.com/library/ff929071.aspx)でユーザーを指定して接続します。
 - このアプローチでは、マスター データベースにログインする必要がなく、パフォーマンスと拡張性が向上します。
 - Transact-SQL の** USE myDatabaseName; **ステートメントを SQL Database に対して使用することはできません。


## 一時エラー


Azure とその SQL Database サービスなどのクラウド サービスには、ワークロードの分散とリソースの管理の課題が無限にあります。同じコンピューターからサービスを提供している 2 つのデータベースの処理量が非常に多く、同時に発生している場合、管理システムが能力が余っているリソースに片方のデータベースのワークロードを移行する必要があることを検出する場合があります。


移行時には、データベースが一時的に使用できなくなることがあります。これによって新しい接続がブロックされたり、クライアント プログラムが接続を失ったりする場合があります。しかしリソースの移行は一時的なものなので、数分または数秒で自動的に解決される場合があります。移行が完了したら、クライアント プログラムでその接続を再確立して、作業を再開することができます。クライアント プログラムの回避できないエラーよりは、処理が一時停止する方が望ましい状態です。


SQL Database でエラーが発生した場合、[SqlException](https://msdn.microsoft.com/library/system.data.sqlclient.sqlexception.aspx) がスローされます。SqlException の **Number** プロパティには、数値のエラー コードが含まれています。一時的なエラーのエラー コードである場合、プログラムで呼び出しを再試行してください。


- [エラー メッセージ (Azure SQL Database)](http://msdn.microsoft.com/library/azure/ff394106.aspx) - **Connection-Loss Errors** セクションには、再試行が自動的に保証されている一時エラーのリストが示されています。
 - たとえば、「<br/>*サーバー 'theserver' 上のデータベース 'mydatabase' は現在使用できません。*」などのような番号 40613 のエラーが発生した場合、再試行されます。


一時的な *エラー*は、一時的な *フォールト*とも呼ばれています。このトピックでは、これらの 2 つの語を同義で扱っています。


一時的または一時的でない接続エラーが発生した場合、次を参照してください。


- [Azure SQL Database との接続の問題のトラブルシューティング](http://support.microsoft.com/kb/2980233/)


## テクノロジ


次のトピックには、クライアント プログラムから Azure SQL Database への接続に使用できる、いくつかの言語のコード サンプルと、ドライバー テクノロジへのリンクが含まれています。


Windows と Linux の両方で実行可能なさまざまなコード サンプルがあります。


**一般的なサンプル:** PHP、Python、Node.js、および .NET CSharp などのさまざまなプログラミング言語のコード サンプルがあります。また、Windows または Linux 上で実行されているクライアント用のサンプルもあります。


- [SQL Database のクライアント開発とクイック スタート コード サンプル](sql-database-develop-quick-start-client-code-samples.md)
- [Azure SQL Database 開発作業の方法に関するトピック](http://msdn.microsoft.com/library/azure/ee621787.aspx)


**再試行ロジック:** 一時エラーの処理に必要な自動再試行ロジックを持つ高度な設計のコード サンプルがあります。


- [Azure SQL Database への信頼性の高い接続方法](http://msdn.microsoft.com/library/azure/dn864744.aspx)
- [Enterprise Library で ADO.NET を使用して Azure SQL Database に接続する方法](http://msdn.microsoft.com/library/azure/dn961167.aspx)
- [ADO.NET を使用して Azure SQL Database に接続する方法](http://msdn.microsoft.com/library/azure/ee336243.aspx)


**Elastic Scale:** Elastic Scale データベースへの接続に関する詳細については、次を参照してください。


- [Azure SQL データベース Elastic Scale プレビューの概要](sql-database-elastic-scale-get-started.md)
- [データ依存ルーティング](sql-database-elastic-scale-data-dependent-routing.md)


**ドライバー ライブラリ:** 推奨バージョンを含む接続ドライバー ライブラリの詳細については、次を参照してください。


- [SQL Database と SQL Server の接続ライブラリ](sql-database-libraries.md)


## 関連項目


- [Create your first Azure SQL Database (最初の Azure SQL Database を作成する)](sql-database-get-started.md)

 

<!---HONumber=July15_HO2-->