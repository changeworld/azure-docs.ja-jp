<properties 
	pageTitle="DocumentDB データベース質問 - よく寄せられる質問 | Microsoft Azure" 
	description="JSON 用の NoSQL ドキュメント データベース サービスである Azure DocumentDB に関してよく寄せられる質問に対する回答を示します。容量、パフォーマンス レベル、およびスケーリングに関するデータベース質問に回答します。" 
	keywords="データベース質問, よく寄せられる質問, documentdb, azure, Microsoft azure"
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/23/2016" 
	ms.author="mimig"/>


#DocumentDB に関してよく寄せられる質問

## Microsoft Azure DocumentDB の基礎に関するデータベース質問

### Microsoft Azure DocumentDB とは 
Microsoft Azure DocumentDB は、スケーラブルな NoSQL ドキュメントのサービスとしてのデータベースです。Microsoft Azure の機能と能力に支えられた管理プラットフォームによって、スキーマフリーのデータに高度なクエリを実行することができ、構成可能な信頼性の高いパフォーマンスの提供、および迅速な開発が可能となります。DocumentDB は、予測可能なスループット、遅延時間の短縮、およびスキーマフリー データ モデルが重要な要件となる、Web アプリケーションやモバイル アプリケーションに適したソリューションです。DocumentDB では、ネイティブ JSON データ モデルでスキーマの柔軟性と豊富なインデックス作成機能が提供され、統合 JavaScript でマルチドキュメント トランザクションがサポートされます。
  
データベース質問および回答の詳細と、このサービスのデプロイおよび使用の手順については、[DocumentDB ドキュメント ページ](https://azure.microsoft.com/documentation/services/documentdb/)を参照してください。

### DocumentDB はどのような種類のデータベースですか?
DocumentDB は、JSON 形式でデータを格納する NoSQL ドキュメント指向のデータベースです。DocumentDB では、高度な DocumentDB [SQL クエリ文法](documentdb-sql-query.md)で照会できる入れ子になった自立的データ構造がサポートされます。DocumentDB では、[ストアド プロシージャ、トリガー、およびユーザー定義関数](documentdb-programming.md)を使用して、サーバー側 JavaScript のトランザクション処理を高いパフォーマンスで実行できます。また、開発者によって調整可能な一貫性レベルを、関連付けられた[パフォーマンス レベル](documentdb-performance-levels.md)と共にサポートします。
 
### DocumentDB データベースはリレーショナル データベース (RDBMS) のようなテーブルを保持しますか?
いいえ、DocumentDB は JSON ドキュメントのコレクション内にデータを格納します。DocumentDB のリソースの詳細については、「[DocumentDB のリソース モデルと概念](documentdb-resources.md)」を参照してください。

### DocumentDB データベースはスキーマフリー データをサポートしますか?
はい、DocumentDB ではスキーマ定義またはヒントなしで、アプリケーションが任意の JSON ドキュメントを格納できます。DocumentDB SQL クエリ インターフェイスを使用したクエリに、データをすぐに利用できます。

### DocumentDB は ACID トランザクションをサポートしますか?
はい、DocumentDB では、JavaScript ストアド プロシージャおよびトリガーとして表現されるクロスドキュメント トランザクションをサポートします。トランザクションは、単一のコレクションを対象とし、他の同時実行されるコードおよびユーザー要求から完全に分離された ACID セマンティクスにより実行されます。JavaScript アプリケーション コードのサーバー側実行により例外がスローされた場合は、トランザクション全体がロールバックされます。

### DocumentDB の一般的な使用事例にはどのようなものがありますか?  
DocumentDB は、拡張性、パフォーマンス、およびスキーマフリー データでのクエリを実行できることが重要である新しい Web アプリケーションやモバイル アプリケーションに適しています。DocumentDB は、迅速な開発に役立ち、アプリケーション データ モデルの継続的な反復をサポートします。ユーザーが生成したコンテンツとデータを管理するアプリケーションは、[DocumentDB の一般的な使用事例](documentdb-use-cases.md)です。

### DocumentDB HIPAA は準拠していますか?
はい、DocumentDB は HIPAA は準拠しています。HIPAA は、個別に識別できる医療情報の使用、開示、および保護するための要件を確立しています。詳細については、[Microsoft セキュリティ センター](https://www.microsoft.com/ja-JP/TrustCenter/Compliance/HIPAA)をご覧ください。

### DocumentDB のスケール制限とは何ですか?
ストレージおよびスループットの観点では、DocumentDB アカウントはコレクションを追加してスケールすることができます。コレクションの数に対するサービスのクォータについては、「[DocumentDB の制限](documentdb-limits.md)」を参照してください。追加のコレクションが必要な場合は、[サポートに連絡して](documentdb-increase-limits.md)、アカウント クォータの増加を依頼してください。

### Microsoft Azure DocumentDB の料金はいくらですか?
詳細については、「[DocumentDB 料金](http://go.microsoft.com/fwlink/p/?LinkID=402317)」ページを参照してください。DocumentDB の利用料金は、使用されたコレクションの数、コレクションがオンラインであった時間数、および各コレクションの[パフォーマンス レベル](documentdb-performance-levels.md)によって決まります。

### 無料アカウントを使用できますか?
Azure を初めて使用する場合は、[Azure 無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。これは 30 日間使用でき、すべての Azure サービスを試すには 200 ドルかかります。Visual Studio サブスクリプションをお持ちの場合は、[1 か月あたり 150 ドルの無料 Azure クレジット](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)ですべての Azure サービスを使用できます。

### DocumentDB に関するその他のヘルプはどのようにして得られますか?
ヘルプが必要な場合は、[Stack Overflow](http://stackoverflow.com/questions/tagged/azure-documentdb) または [Azure DocumentDB MSDN デベロッパー フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureDocumentDB)を利用するか、または [DocumentDB エンジニアリング チームとの 1 対 1 のチャット](http://www.askdocdb.com/)をスケジュールしてください。DocumentDB の最新のニュースと機能については、[Twitter](https://twitter.com/DocumentDB) をフォローしてください。

## Microsoft Azure DocumentDB のセットアップ

### どのようにして Microsoft Azure DocumentDB にサインアップしますか?
Microsoft Azure DocumentDB は、[Azure ポータル][azure-portal]で利用可能です。最初に、Microsoft Azure サブスクリプションにサインアップする必要があります。Microsoft Azure サブスクリプションにサインアップした後、DocumentDB アカウントを Azure サブスクリプションに追加できます。DocumentDB アカウントの追加方法については、「[DocumentDB データベース アカウントの作成](documentdb-create-account.md)」を参照してください。

### マスター キーとは何ですか?
マスター キーは、アカウントのすべてのリソースにアクセスするためのセキュリティ トークンです。キーを保持する個人には、データベース アカウント内のすべてのリソースへの読み取り/書き込みアクセスが許可されます。マスター キーを配布するときには十分な注意が必要となります。プライマリ マスター キーとセカンダリ マスター キーは、[Azure ポータル][azure-portal]の **[キー] **ブレードで入手できます。リソース キーの詳細については、「[アクセス キーを表示、コピー、および再生成する](documentdb-manage-account.md#keys)」を参照してください。

### どのようにしてデータベースを作成しますか?
「[DocumentDB データベースの作成](documentdb-create-database.md)」で説明されているように [Azure ポータル]()を使用するか、あるいは [DocumentDB SDK](documentdb-sdk-dotnet.md) のいずれかまたは [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) を使用してデータベースを作成できます。

### コレクションとは何ですか?
コレクションには、JSON ドキュメントのほか、関連する JavaScript アプリケーション ロジックが格納されます。クエリおよびトランザクションの範囲としてコレクションが指定されます。多種多様な JSON ドキュメントのセットを 1 つのコレクション内に格納し、自動的にインデックスを作成することができます。

コレクションも DocumentDB の課金エンティティです。毎月の DocumentDB の利用料金は、使用されたコレクションの数、コレクションがオンラインであった時間数、および各コレクションの[パフォーマンス レベル](documentdb-performance-levels.md)によって決まります。詳細については、「[DocumentDB 価格](https://azure.microsoft.com/pricing/details/documentdb/)」を参照してください。

### データベースとコレクションに制限はありますか?
各コレクションには、いずれかのサポートされる[パフォーマンス レベル](documentdb-performance-levels.md)でデータベース ストレージとプロビジョニング済みスループットが割り当てられています。サービスで管理される、各リソースに対するクォータも用意されています。すべての制限については、「[DocumentDB の制限](documentdb-limits.md)」を参照してください。アカウント制限の変更を要求する方法については、「[DocumentDB アカウント制限の引き上げを要求する](documentdb-increase-limits.md)」を参照してください。

### どのようにしてユーザーおよびアクセス許可を設定しますか?
[DocumentDB SDK](documentdb-sdk-dotnet.md) または [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) を使用して、ユーザーおよびアクセス許可を作成できます。

## Microsoft Azure DocumentDB に対する開発についてのデータベース質問

### どのようにして DocumentDB に対する開発を開始しますか?
.NET、Python、Node.js、JavaScript、および Java では、[SDK](documentdb-sdk-dotnet.md) が利用可能です。開発者は、[RESTful HTTP API](https://msdn.microsoft.com/library/azure/dn781481.aspx) を利用して、さまざまなプラットフォームおよび言語から DocumentDB リソースとやり取りすることもできます。

GitHub では、[.NET](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples)、[Java](https://github.com/Azure/azure-documentdb-java)、[Node.js](https://github.com/Azure/azure-documentdb-node/tree/master/samples)、[Python](https://github.com/Azure/azure-documentdb-python) 用の DocumentDB SDK のサンプルを入手できます。

### DocumentDB は SQL をサポートしますか?
DocumentDB SQL クエリ言語は、SQL でサポートされるクエリ機能の強化版サブセットです。DocumentDB の SQL クエリ言語は、JavaScript ベースのユーザー定義関数 (UDF) により高度な階層型の関係演算子と機能拡張を提供します。JSON 文法では、JSON ドキュメントをツリー ノードとしてラベルが付けられたツリーとしてモデル化することができます。これは、DocumentDB の自動的なインデックス作成手法と DocumentDB の SQL クエリ方言の両方で利用されます。SQL 文法の使用方法の詳細については、「[DocumentDB のクエリ][query]」を参照してください。

### DocumentDB でサポートされるデータ型は何ですか?
DocumentDB でサポートされるプリミティブ データ型は JSON と同じです。JSON には、文字列、数値 (IEEE754 倍精度)、ブール値 (true、false、Null) で構成されるシンプルな型システムがあります。{ } 演算子で入れ子になったオブジェクトを作成し、[ ] 演算子で配列を作成することにより、さらに複雑なデータ型 (DateTime、Guid、Int64、Geometry など) を JSON と DocumentDB の両方で表現できます。

### DocumentDB はどのようにして同時実行を提供しますか?
DocumentDB では、HTTP エンティティ タグ (または ETag) でオプティミスティック同時実行制御 (OCC) をサポートします。すべての DocumentDB リソースには ETag があり、DocumentDB クライアントでは最新の読み取りバージョンが書き込み要求内に含まれます。ETag が最新である場合は、変更がコミットされます。値が外部で変更されている場合、サーバーは "HTTP 412 Precondition failure" 応答コードにより書き込みを拒否します。クライアントは最新バージョンのリソースを読み取り、要求を再試行する必要があります。

### どのようにして DocumentDB のトランザクションを実行しますか?
DocumentDB では、JavaScript ストアド プロシージャおよびトリガーを介して、統合された言語のトランザクションがサポートされます。スクリプト内のすべてのデータベース操作は、コレクションを対象としたスナップショット分離により実行されます。ドキュメント バージョン (ETag) のスナップショットは、トランザクションの開始時に取得され、スクリプトが成功された場合のみコミットされます。JavaScript がエラーをスローした場合、トランザクションはロールバックされます。詳細については、「[DocumentDB server-side programming](documentdb-programming.md)」(DocumentDB のサーバー側プログラミング) を参照してください。

### どのようにして DocumentDB にドキュメントを一括挿入しますか? 
DocumentDB にドキュメントを一括挿入するには 3 つの方法があります。

- データ移行ツール。「[DocumentDB にデータをインポートする](documentdb-import-data.md)」を参照してください。
- Azure ポータルの Document Explorer。「[Document Explorer を使用したドキュメントの一括追加](documentdb-view-json-document-explorer.md#BulkAdd)」を参照してください。
- ストアド プロシージャ。「[DocumentDB のサーバー側プログラミング](documentdb-programming.md)」を参照してください。

### DocumentDB はリソース リンク キャッシュをサポートしますか?
はい、DocumentDB は RESTful サービスであるため、リソース リンクは不変であり、キャッシュできます。DocumentDB クライアントは、ドキュメントやコレクションなどリソースの読み取りに対して "If-None-Match" ヘッダーを指定でき、サーバー バージョンが変更された場合のみローカル コピーを更新できます。




[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md
 

<!---HONumber=AcomDC_0323_2016-->