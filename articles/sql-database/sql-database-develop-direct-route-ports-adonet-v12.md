<properties 
	pageTitle="SQL Database における 1433 以外のポート | Microsoft Azure"
	description="ADO.NET から Azure SQL Database V12 へのクライアント接続では、プロキシを使用せずに、データベースと直接やり取りする場合があります。1433 以外のポートが重要になります。"
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor="" />


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/04/2015" 
	ms.author="genemi"/>


# Ports beyond 1433 for ADO.NET 4.5, and SQL Database V12 (ADO.NET 4.5、SQL Database V12 における 1433 以外のポート)


このトピックでは、Azure SQL Database V12 によってもたらされる ADO.NET 4.5 以降のバージョンを使用するクライアントの接続動作の変化について説明します。


## SQL Database V11: ポート 1433


クライアント プログラムで ADO.NET 4.5 を使用して、SQL Database V11 に接続し、クエリを実行する場合、内部での実行順序は次のようになります。


1. ADO.NET が SQL Database への接続を試行します。

2. ADO.NET は、ポート 1433 を使用して、ミドルウェア モジュールを呼び出し、ミドルウェアが SQL Database に接続します。

3. SQL Database は、ミドルウェアへ応答を送信し、ミドルウェアが ADO.NET のポート 1433 へ応答を転送します。


**用語:** 前述の実行順序の説明では、ADO.NET は*プロキシ ルート*を使用して SQL Database とやり取りしていることを表現しています。ミドルウェアが関与していない場合は、*ダイレクト ルート*を使用するというように表現します。


## SQL Database V12: 外部と内部


V12 への接続については、クライアント プログラムが Azure クラウド境界の*内部*で実行されているか、または*外部*で実行されているかを確認する必要があります。サブセクションでは、次の 2 つの一般的なシナリオについて説明します。


#### *外部:* クライアントをデスクトップ コンピューターで実行


ポート 1433 が、SQL Database クライアント アプリケーションをホストするデスクトップ コンピューターで開く必要がある唯一のポートです。


#### *内部:* クライアントを Azure で実行


Azure クラウド境界内でクライアントを実行している場合、クライアントは、いわゆる*ダイレクト ルート* を使用して SQL Database とやり取りします。接続が確立した後に、クライアントとデータベース間のやり取りにミドルウェア プロキシが関与することはありません。


順序は次のとおりです。


1. ADO.NET 4.5 (またはそれ以降) は、Azure クラウドと簡単なやり取りを開始し、動的に指定されたポート番号を受信します。
 - 動的に特定されるポート番号は、11000 から 11999 または 14000 から 14999 の範囲になります。

2. ADO.NET は次に、ミドルウェアによって仲介することなく、SQL Database サーバーと直接接続します。

3. クエリは、データベースに直接送信され、その結果もクライアントに直接返されます。


11000 から 11999 および 14000 から 14999 のポート範囲が Azure クライアント コンピューター上で使用可能なまま残され、SQL Database V12 と ADO.NET 4.5 クライアントのやり取りに使用できることを確認します。

- 具体的には、対象の範囲のポートが他のすべての送信ブロッカーの影響を受けないようにします。
- Azure VM 上の Windows ファイアウォールがポート設定を制御します。


## バージョンの明確化


このセクションでは、製品バージョンを参照するモニカーを明らかにします。また、製品間でのバージョンのいくつかの組み合わせも一覧表示します。


#### ADO.NET


- ADO.NET 4.0 は TDS 7.3 プロトコルをサポートしますが、7.4 はサポートされません。
- ADO.NET 4.5 以降は、TDS 7.4 プロトコルをサポートします。


#### SQL Database V11 と V12


このトピックでは、SQL Database V11 と V12 でのクライアント接続の差異について説明します。


*注:* Transact-SQL ステートメント `SELECT @@version;` は、'11.' または '12.' から始まる値を返します。この数値は、SQL Database のバージョン名 (V11 と V12) と一致します。


## 関連リンク


- ADO.NET 4.6 は、2015 年 7 月 20 日にリリースされました。.NET チームのブログのお知らせは[こちら](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx)からご利用になれます。


- ADO.NET 4.5 は、2012 年 8 月 15 日にリリースされました。.NET チームのブログのお知らせは[こちら](http://blogs.msdn.com/b/dotnet/archive/2012/08/15/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx)からご利用になれます。
 - ADO.NET 4.5.1 についてのブログの投稿は、[こちら](http://blogs.msdn.com/b/dotnet/archive/2013/06/26/announcing-the-net-framework-4-5-1-preview.aspx)からご利用になれます。


- [TDS プロトコルのバージョンの一覧](http://www.freetds.org/userguide/tdshistory.htm)


- [SQL Database への接続: リンク、ベスト プラクティスと設計のガイドライン](sql-database-connect-central-recommendations.md)

<!---HONumber=Sept15_HO2-->