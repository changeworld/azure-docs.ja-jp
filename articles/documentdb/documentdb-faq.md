---
title: "Azure Cosmos DB データベース質問 - よく寄せられる質問 | Microsoft Docs"
description: "グローバル分散型のマルチモデル データベース サービスである Azure Cosmos DB についてよく寄せられる質問の回答が得られます。 容量、パフォーマンス レベル、およびスケーリングに関するデータベース質問に回答します。"
keywords: "データベース質問, よく寄せられる質問, documentdb, azure, Microsoft azure"
services: cosmosdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: b68d1831-35f9-443d-a0ac-dad0c89f245b
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 687690fc3b99d52d7c822b7b7ff71c282541be6d
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---
# <a name="frequently-asked-questions-about-azure-cosmos-db"></a>Azure Cosmos DB についてよく寄せられる質問
## <a name="database-questions-about-microsoft-azure-cosmos-db-fundamentals"></a>Microsoft Azure Cosmos DB の基礎についてのデータベース質問
### <a name="what-is-microsoft-azure-cosmos-db"></a>Microsoft Azure Cosmos DB とは何ですか?
Microsoft Azure Cosmos DB は、グローバルにレプリケートされたマルチモデル データベース サービスです。Microsoft Azure の強力な機能と守備範囲に支えられた管理プラットフォームを通じて、スキーマフリーのデータに対する豊富なクエリを提供し、構成可能で信頼性の高いパフォーマンスと迅速な開発を実現します。 Azure Cosmos DB は、予測可能なスループット、高可用性、低待機時間、スキーマフリー データ モデルが重要な要件となる、Web、モバイル、ゲーム、IoT の各アプリケーションに最適なソリューションです。 Azure Cosmos DB は、スキーマの柔軟性と豊富なインデックス作成機能を備え、統合 JavaScript によるマルチドキュメント トランザクションをサポートします。  

Azure Cosmos DB は、Microsoft 社内の大規模なアプリケーションで開発者が直面する問題に対処するために、2010 年後期に運用が開始されました。 その後、グローバル分散型のアプリケーションを構築する作業が問題となるのは Microsoft だけではないことから、Azure DocumentDB というサービスの形で、Azure の開発者を対象に外部にも提供されるようになりました。 このたび提供を開始した Azure Cosmos DB は、DocumentDB がさらに大きな進歩を遂げたものです。 今回 Azure Cosmos DB がリリースされたことを受けて、DocumentDB を使用しているお客様は、(そのデータも含め) Azure Cosmos DB に自動的に移行します。 この切り替えはシームレスに行われるため、Azure Cosmos DB が提供する幅広い新機能にすぐにアクセスできます。 

データベース質問および回答の詳細と、このサービスのデプロイおよび使用の手順については、[Azure Cosmos DB ドキュメント ページ](https://azure.microsoft.com/documentation/services/documentdb/)をご覧ください。

### <a name="what-happened-to-documentdb"></a>DocumentDB はどうなりましたか?
DocumentDB API は、Azure Cosmos DB でサポートされている API の 1 つであり、データ モデルの 1 つです。 さらに、Azure Cosmos DB は、Graph API (プレビュー)、Table API (プレビュー)、MongoDB API もサポートしています。 

### <a name="how-do-i-get-to-my-documentdb-account-in-the-azure-portal"></a>Azure Portal で DocumentDB アカウントにアクセスするにはどうすればよいですか?
Azure Portal の左側のメニューで Azure Cosmos DB アイコンをクリックするだけです。 DocumentDB アカウントを既に持っていた場合は、そのアカウントが Azure Cosmos DB アカウントになります。課金の変更はありません。

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Azure Cosmos DB の一般的なユース ケースを教えてください。
Azure Cosmos DB は、自動スケール、予測可能なパフォーマンス、ミリ秒レベルの高速応答時間、スキーマフリー データに対してクエリを実行できることが重要である、新しい Web、モバイル、ゲーム、IoT の各アプリケーションに適しています。 Azure Cosmos DB は迅速な開発に役立ち、アプリケーション データ モデルの継続的な反復をサポートします。  ユーザーが生成したコンテンツとデータを管理するアプリケーションは、[Azure Cosmos DB の一般的なユース ケース](documentdb-use-cases.md)です。  

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Azure Cosmos DB では、予測可能なパフォーマンスをどのようにして実現していますか?
[要求ユニット](documentdb-request-units.md)は、Azure Cosmos DB におけるスループットの測定単位です。 1 RU は、1KB のドキュメントを取得するスループットに相当します。 Azure Cosmos DB におけるすべての操作 (読み取り、書き込み、SQL クエリ、ストアド プロシージャの実行など) には、操作を完了するために必要なスループットに基づいて明確な RU 値が設定されています。 CPU、IO、メモリや、これらがアプリケーションのスループットに及ぼす影響を考慮する代わりに、RU という 1 つの単位を基にして考えることができます。

各 Azure Cosmos DB コンテナーは、1 秒あたりのスループットを表す RU を単位として、プロビジョニング スループットを使用して予約できます。 あらゆる規模のアプリケーションで、個々の要求のベンチマークを実行して RU 値を測定し、すべての要求の要求ユニットの合計に対処できるようにコンテナーをプロビジョニングできます。 アプリケーションのニーズの進化に合わせて、コンテナーのスループットをスケールアップまたはスケールダウンすることもできます。 要求ユニットの詳細とコンテナーのニーズを判断する方法については、「[スループットのニーズの推定](documentdb-request-units.md#estimating-throughput-needs)」を参照し、[スループット計算ツール](https://www.documentdb.com/capacityplanner)をお試しください。 ここで言う "コンテナー" とは、DocumentDB API のコレクション、Graph API のグラフ、MongoDB API のコレクション、Table API のテーブルを指します。  


### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Azure Cosmos DB は HIPAA 準拠ですか?
はい。Azure Cosmos DB は HIPAA 準拠です。 HIPAA は、個別に識別できる医療情報の使用、開示、および保護するための要件を確立しています。 詳細については、[Microsoft セキュリティ センター](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA)を参照してください。

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Azure Cosmos DB のストレージの制限を教えてください。
Azure Cosmos DB でコンテナーが格納できるデータの合計量に制限はありません。

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Azure Cosmos DB のスループットの制限を教えてください。
Azure Cosmos DB でコンテナーがサポートできるスループットの合計量に制限はありません。ただし、十分な数のパーティション キーにワークロードをほぼ均等に分散する必要があります。

### <a name="how-much-does-microsoft-azure-cosmos-db-cost"></a>Microsoft Azure Cosmos DB の料金はいくらですか?
詳細については、[Azure Cosmos DB の価格の詳細](https://azure.microsoft.com/pricing/details/documentdb/)に関するページをご覧ください。 Azure Cosmos DB の利用料金は、プロビジョニング済みコンテナーの数、コンテナーがオンラインであった時間数、各コンテナーのプロビジョニング スループットによって決まります。 ここで言う "コンテナー" とは、DocumentDB API のコレクション、Graph API のグラフ、MongoDB API のコレクション、Table API のテーブルを指します。 

### <a name="is-there-a-free-account-available"></a>無料アカウントを使用できますか?
Azure を初めて使用する場合は、30 日間使用できる [Azure 無料アカウント](https://azure.microsoft.com/free/)にサインアップできます。すべての Azure サービスを試すには 200 ドルかかります。 Visual Studio サブスクリプションをお持ちの場合は、[1 か月あたり 150 ドルの無料 Azure クレジット](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)ですべての Azure サービスを使用できます。  

また、[Azure Cosmos DB Emulator](documentdb-nosql-local-emulator.md) を使用すると、Azure サブスクリプションを作成しなくても、ローカルでのアプリケーションの開発とテストを無料で行うことができます。 Azure Cosmos DB Emulator でのアプリケーションの動作に満足できたら、クラウドでの Azure Cosmos DB アカウントの使用に切り替えることができます。

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Azure Cosmos DB に関するその他の支援を得るにはどうすればよいですか?
支援が必要な場合は、[Stack Overflow](http://stackoverflow.com/questions/tagged/azure-documentdb) または [MSDN フォーラム](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB)を利用するか、[askdocdb@microsoft.com](mailto:askdocdb@microsoft.com) にメールを送って、Azure Cosmos DB エンジニアリング チームとの 1 対 1 のチャットをスケジュールしてください。 

## <a name="set-up-microsoft-azure-cosmos-db"></a>Microsoft Azure Cosmos DB のセットアップ
### <a name="how-do-i-sign-up-for-microsoft-azure-cosmos-db"></a>Microsoft Azure Cosmos DB にサインアップするにはどうすればよいですか?
Microsoft Azure Cosmos DB は Azure Portal で利用できます。 最初に、Microsoft Azure サブスクリプションにサインアップする必要があります。 Microsoft Azure サブスクリプションにサインアップしたら、DocumentDB API、Graph API (プレビュー)、Table API (プレビュー)、または MongoDB API のアカウントを Azure サブスクリプションに追加できます。

### <a name="what-is-a-master-key"></a>マスター キーとは何ですか?
マスター キーは、アカウントのすべてのリソースにアクセスするためのセキュリティ トークンです。 キーを保持する個人には、データベース アカウント内のすべてのリソースへの読み取り/書き込みアクセスが許可されます。 マスター キーを配布するときには十分な注意が必要となります。 プライマリ マスター キーとセカンダリ マスター キーは、[Azure Portal][azure-portal] の **[キー]** ブレードで入手できます。 リソース キーの詳細については、「 [アクセス キーを表示、コピー、および再生成する](documentdb-manage-account.md#keys)」を参照してください。

### <a name="what-are-the-regions-to-which-preferredlocations-be-set-to"></a>PreferredLocations として設定できるリージョンを教えてください。 
PrefferredLocations の現在有効なリージョンは、"West US (米国西部)"、"West Central US (米国中西部)"、"West US 2 (米国西部 2)"、"East US (米国東部)"、"East US 2 (米国東部 2)"、"Central US (米国中部)"、"South Central US (米国中南部)"、"North Central US (米国中北部)"、"West Europe (西ヨーロッパ)"、"North Europe (北ヨーロッパ)"、"East Asia (東アジア)"、"Southeast Asia (東南アジア)"、"Japan West (西日本)"、"Japan East (東日本)"、"Australia Southeast (オーストラリア南東部)"、"Australia East (オーストラリア東部)"、"Central India (インド中部)"、"South India (インド南部)"、"West India (インド西部)"、"Canada East (カナダ東部)"、"Canada Central (カナダ中部)"、"Germany Central (ドイツ中部)"、"Germany Northeast (ドイツ北東部)"、"China North (中国北部)"、"China East (中国東部)"、"Korea South (韓国南部)"、"Korea Central (韓国中部)"、"UK West (英国西部)"、"UK South (英国南部)"、"Brazil South (ブラジル南部)"、"USGov Arizona (米国政府アリゾナ)"、"USGov Texas (米国政府テキサス)" です。

### <a name="is-there-something-i-should-be-aware-of-when-distributing-data-across-the-world-via-azures-data-centers"></a>Azure のデータ センター経由で世界中にデータを配布するときに注意すべきことはありますか? 
Cosmos DB はすべてのリージョンにわたって存在します。 Cosmos DB はコア サービスであるため、すべての新しいデータ センターに Cosmos DB が存在することになります。 現時点でのリージョンの一覧は上記のとおりです。 リージョンを設定するときは、Cosmos DB では主権のあるクラウドと政府機関のクラウドが重視されることに注意してください。 これらの場所にアカウントを作成し、外部にレプリケートしようとしても、これは許可されません。同様に、外部のアカウントからこれらの場所にレプリケートしようとしても、その場所に到達することはできません。 



### <a name="do-you-plan-to-provide-more-price-options-in-the-future"></a>今後、さらに多くの価格オプションを提供する予定はありますか?
はい。現在の Cosmos DB は、スループットに最適化されたモデルを提供しています。 近い将来、さらに最適な価格モデルを提供する予定です。 

 
# <a name="documentdb-api"></a>DocumentDB API 
## <a name="database-questions-about-developing-against-documentdb-api"></a>DocumentDB API に対する開発についてのデータベース質問

### <a name="how-to-do-i-start-developing-against-documentdb-api"></a>DocumentDB API に対する開発を開始するにはどうすればよいですか?
Microsoft DocumentDB API は、[Azure Portal][azure-portal] で利用できます。  最初に、Microsoft Azure サブスクリプションにサインアップする必要があります。  Microsoft Azure サブスクリプションにサインアップしたら、DocumentDB API コンテナーを Azure サブスクリプションに追加できます。 Azure Cosmos DB アカウントを追加する手順については、[Azure Cosmos DB データベース アカウントの作成](documentdb-create-account.md)に関するセクションをご覧ください。 DocumentDB アカウントを既に持っていた場合は、そのアカウントが Azure Cosmos DB アカウントになります。  

[SDK](documentdb-sdk-dotnet.md) が利用可能です。  開発者は、[RESTful HTTP API](https://msdn.microsoft.com/library/azure/dn781481.aspx) を使用して、さまざまなプラットフォームや言語で Azure Cosmos DB リソースを操作することもできます。

### <a name="can-i-access-some-ready-made-samples-for-getting-headstart"></a>開発作業を速やかに開始するために、既製のサンプルを利用できますか?
DocumentDB API の [.NET](documentdb-dotnet-samples.md)、[Java](https://github.com/Azure/azure-documentdb-java)、[Node.js](documentdb-nodejs-samples.md)、[Python](documentdb-python-samples.md) の各 SDK のサンプルを GitHub で入手できます。


### <a name="does-documentdb-api--database-support-schema-free-data"></a>DocumentDB API データベースはスキーマフリー データをサポートしていますか?
はい。DocumentDB API では、スキーマ定義やヒントを必要とせずに、アプリケーションが任意の JSON ドキュメントを格納できます。 データは、Azure Cosmos DB SQL クエリ インターフェイスを使用してクエリにすぐに使用できます。   

### <a name="does-documentdb-api-support-acid-transactions"></a>DocumentDB API は ACID トランザクションをサポートしていますか?
はい。DocumentDB API では、JavaScript のストアド プロシージャとトリガーとして表現されるクロス ドキュメント トランザクションをサポートしています。 トランザクションは、各コレクション内の単一のパーティションを対象とし、他の同時実行されるコードおよびユーザー要求から完全に分離された ACID セマンティクスで実行されます。  JavaScript アプリケーション コードのサーバー側実行により例外がスローされた場合は、トランザクション全体がロールバックされます。 トランザクションの詳細については、「 [プログラム データベース トランザクション](documentdb-programming.md#database-program-transactions)」を参照してください。

### <a name="what-is-a-collection"></a>コレクションとは何ですか?
コレクションとは、ドキュメントと、関連する JavaScript アプリケーション ロジックのグループです。 コレクションは課金対象のエンティティであり、その[コスト](documentdb-performance-levels.md)は、使用しているスループットとストレージによって決まります。 コレクションは、1 つまたは複数のパーティション/サーバーにまたがって存在でき、拡張性があるので、扱うことのできるストレージのボリュームやスループットには実質的に制限がありません。

コレクションは、Azure Cosmos DB の課金エンティティでもあります。 各コレクションは、プロビジョニング済みスループットと使用されたストレージ領域に基づいて時間単位で課金されます。 詳細については、[DocumentDB API の価格](https://azure.microsoft.com/pricing/details/documentdb/)に関するページをご覧ください。  

### <a name="how-do-i-create-a-database"></a>どのようにしてデータベースを作成しますか?
データベースは、[Azure Portal]() ([Azure Cosmos DB のコレクションとデータベースの作成](documentdb-create-collection.md)に関する記事を参照)、[Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) のいずれか、または [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) を使用して作成できます。  

### <a name="how-do-i-set-up-users-and-permissions"></a>どのようにしてユーザーおよびアクセス許可を設定しますか?
ユーザーとアクセス許可は、[DocumentDB API SDK](documentdb-sdk-dotnet.md) のいずれかまたは [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) を使用して作成できます。   

### <a name="does-documentdb-api-support-sql"></a>DocumentDB API は SQL をサポートしていますか?
SQL クエリ言語は、SQL でサポートされるクエリ機能の強化版サブセットです。 Azure Cosmos DB の SQL クエリ言語は、高度な階層型の関係演算子と、JavaScript ベースのユーザー定義関数 (UDF) による機能拡張を提供します。 JSON 文法では、ツリー ノードとしてラベルが付けられたツリーとして JSON ドキュメントをモデル化できます。これは、Azure Cosmos DB の自動インデックス作成手法と Azure Cosmos DB の SQL クエリ言語の両方で使用されます。  SQL 文法の使用方法の詳細については、[QueryDocumentDB][query] に関する記事をご覧ください。

### <a name="does-documentdb-api--support-sql-aggregation-functions"></a>DocumentDB API は SQL 集計関数をサポートしていますか?
DocumentDB API は、SQL 文法の `COUNT`、`MIN`、`MAX`、`AVG`、`SUM` の各集計関数を使用した、あらゆるスケールでの低待機時間の集計をサポートしています。 詳細については、「[集計関数](documentdb-sql-query.md#Aggregates)」をご覧ください。

### <a name="how-does-documentdb-api--provide-concurrency"></a>DocumentDB API はどのようにして同時実行を提供しますか?
DocumentDB API は、HTTP エンティティ タグ (ETag) によるオプティミスティック同時実行制御 (OCC) をサポートしています。 すべての DocumentDB API リソースに ETag があり、ドキュメントが更新されるたびにサーバーで ETag が設定されます。 すべての応答メッセージに ETag ヘッダーと現在の値が含まれます。 Etag を If-Match ヘッダーと共に使用すると、サーバーでリソースを更新するかどうかを判定できるようになります。 If-Match 値は、チェック対象の ETag 値です。 対象の ETag 値がサーバーの ETag 値と一致する場合に、リソースが更新されます。 ETag が現在の値ではない場合、サーバーは操作を拒否して "HTTP 412 Precondition failure" 応答コードを返します。 この場合、クライアントでは、リソースを再フェッチしてリソースの現在の ETag 値を取得する必要があります。 また、ETag を If-None-Match ヘッダーと共に使用すると、リソースの再フェッチが必要かどうかを判定することができます。

.NET でオプティミスティック同時実行制御を使用するには、 [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) クラスを使用します。 .NET サンプルについては、GitHub にある DocumentManagement サンプルの [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) を参照してください。

### <a name="how-do-i-perform-transactions-in-documentdb-api"></a>DocumentDB API でトランザクションを実行するにはどうすればよいですか?
DocumentDB API は、JavaScript のストアド プロシージャとトリガーによる、統合された言語のトランザクションをサポートしています。 スクリプト内のすべてのデータベース操作は、単一パーティション コレクションの場合はコレクションを対象として、スナップショット分離下で実行されます。コレクションがパーティション分割されている場合は、コレクション内の同じパーティション キー値を持つドキュメントを対象として実行されます。 ドキュメント バージョン (ETag) のスナップショットは、トランザクションの開始時に取得され、スクリプトが成功された場合のみコミットされます。 JavaScript がエラーをスローした場合、トランザクションはロールバックされます。 詳細については、[DocumentDB API のサーバー側プログラミング](documentdb-programming.md)に関する記事をご覧ください。

### <a name="how-can-i-bulk-insert-documents-into-documentdb-api"></a>DocumentDB API にドキュメントを一括挿入するにはどうすればよいですか?
Azure Cosmos DB にドキュメントを一括挿入するには、次の 3 つの方法があります。

* データ移行ツール。[DocumentDB API へのデータのインポート](documentdb-import-data.md)に関する記事をご覧ください。
* Azure Portal の Document Explorer。[ドキュメント エクスプローラーを使用したドキュメントの一括追加](documentdb-view-json-document-explorer.md#bulk-add-documents)に関するページを参照してください。
* ストアド プロシージャ。[DocumentDB API のサーバー側プログラミング](documentdb-programming.md)に関する記事をご覧ください。

### <a name="does-documentdb-api-support-resource-link-caching"></a>DocumentDB API はリソース リンク キャッシュをサポートしていますか?
はい。Cosmos DB は RESTful サービスであるため、リソース リンクは不変であり、キャッシュできます。 DocumentDB API クライアントは、ドキュメントやコレクションなどのリソースに対する読み取りに "If-None-Match" ヘッダーを指定し、サーバー バージョンが変更された場合にのみローカル コピーを更新できます。

### <a name="is-a-local-instance-of-documentdb-api-available"></a>DocumentDB API のローカル インスタンスは使用できますか?
はい。 [Azure Cosmos DB Emulator](documentdb-nosql-local-emulator.md) では、DocumentDB API サービスの高忠実度エミュレーションを実行できます。 JSON ドキュメントの作成とクエリ、コレクションのプロビジョニングとスケーリング、ストアド プロシージャとトリガーの実行のサポートなど、Azure Cosmos DB と同じ機能がサポートされています。 Azure Cosmos DB Emulator を使用してアプリケーションの開発とテストを行い、Azure Cosmos DB の接続エンドポイントの構成を 1 つ変更するだけで、世界規模で Azure にデプロイすることができます。

## <a name="database-questions-about-developing-against-api-for-mongodb"></a>MongoDB 用 API に対する開発についてのデータベース質問
### <a name="what-is-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API とは何ですか?
Microsoft Azure Cosmos DB の MongoDB 用 API は、コミュニティでサポートされる既存の Apache MongoDB API とドライバーを使用して、アプリケーションがネイティブの Azure Cosmos DB データベース エンジンと簡単かつ透過的に通信できるようにする互換性レイヤーです。 開発者は、既存の MongoDB ツール チェーンとスキルを使って、Azure Cosmos DB を活用するアプリケーションを構築できるようになりました。これにより、自動インデックス作成、バックアップ メンテナンス、利用料金に基づくサービス レベル アグリーメント (SLA) など、Azure Cosmos DB の独自の機能によるメリットが得られます。

### <a name="how-to-do-i-connect-to-my-api-for-mongodb-database"></a>MongoDB 用 API データベースへの接続方法を教えてください。
Azure Cosmos DB の MongoDB 用 API に接続する最も簡単な方法は、[Azure Portal](https://portal.azure.com) を使用することです。 自身のアカウントに移動します。 アカウントの*左側のナビゲーション*で、*[クイック スタート]* をクリックします。 *クイック スタート*は、コード スニペットを取得してデータベースに接続するための最善の方法です。 

Azure Cosmos DB では、厳密なセキュリティ要件と基準が適用されます。 Azure Cosmos DB アカウントでは、*SSL* による認証とセキュリティで保護された通信が要求されるので、必ず TLSv1.2 を使用してください。

詳しくは、[MongoDB 用 API データベースへの接続](documentdb-connect-mongodb-account.md)に関する記事をご覧ください。

### <a name="are-there-additional-error-codes-for-an-api-for-mongodb-database"></a>MongoDB 用 API にはその他のエラー コードはありますか?
MongoDB 用 API には、一般的な MongoDB エラー コードのほかに、独自の特定のエラー コードがあります。


| エラー               | コード  | 説明  | 解決策  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | 使用された要求ユニットの合計数が、コレクションのプロビジョニング済み要求ユニット レートを超えたため調整されました。 | Azure Portal でのコレクションのスループットのスケーリングか、再試行を検討してください。 |
| ExceededMemoryLimit | 16501 | マルチ テナント サービスとして、操作がクライアントのメモリ配分を超えました。 | より制限の厳しいクエリ条件により操作のスコープを減らすか、[Azure ポータル](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)からサポートに問い合わせます。 <br><br>*例:  &nbsp;&nbsp;&nbsp;&nbsp;db.getCollection('users').aggregate([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {name: "Andy"}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {age: -1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])*) |

## <a name="database-questions-about-developing-with-azure-cosmos-db-table-api-preview"></a>Azure Cosmos DB: Table API (プレビュー) を使用した開発についてのデータベース質問

### <a name="terms"></a>用語 
Azure Cosmos DB: Table API (プレビュー) は、テーブルをサポートするためにビルド 2017 で発表された、Azure Cosmos DB が提供する Premium サービスを指します。 

Standard Table SDK は、従来の Azure Storage Table SDK です。 

### <a name="how-can-i-use-the-new-table-api-preview-offering"></a>新しい Table API (プレビュー) を使用するにはどうすればよいですか? 
Microsoft Table API は、[Azure Portal][azure-portal] で利用できます。  最初に、Microsoft Azure サブスクリプションにサインアップする必要があります。  Microsoft Azure サブスクリプションにサインアップしたら、Table API コンテナーを Azure サブスクリプションに追加できます。  
プレビュー期間は、.NET で [SDK](../cosmos-db/table-sdk-dotnet.md) を使用できます。まず、[Table API](../cosmos-db/create-table-dotnet.md) のクイックスタートを完了してください。

### <a name="do-i-need-a-new-sdk-to-use-the-table-api-preview"></a>Table API (プレビュー) を使用するには新しい SDK が必要ですか? 
はい。新しい SDK は、こちらの [Table API](../cosmos-db/table-sdk-dotnet.md) に関するページから Nuget パッケージの形で入手できます。 これは、SDK for Windows Azure Storage 8.1.2 with Premium Table API (プレビュー) と呼ばれます。  

### <a name="how-do-i-provide-the-feedback-about-the-sdk-bugs"></a>SDK のバグに関するフィードバックを提供するにはどうすればよいですか?
次のいずれかの方法でフィードバックをお寄せください。

* [UserVoice](https://feedback.azure.com/forums/263030-documentdb)
* [MSDN フォーラム](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB)
* [StackOverflow](http://stackoverflow.com/questions/tagged/azure-documentdb)

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api-preview"></a>Table API (プレビュー) に接続する際に使用する必要がある接続文字列を教えてください。
接続文字列は、`DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountNameFromDocumentDB>.documents.azure.com` です。 接続文字列は、Azure Portal の [キー] ページから取得できます。 

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-new-table-apipreview"></a>新しい Table API (プレビュー) で、要求オプションの構成設定を上書きするにはどうすればよいですか?
これらの設定は、「[Azure Cosmos DB の機能](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)」に記載されています。 設定を変更するには、クライアント アプリケーションの app.config の appsettings セクションに設定を追加します。
<appSettings>
    <add key="TableConsistencyLevel" value="Eventual|Strong|Session|BoundedStaleness|ConsistentPrefix"/>
    <add key="TableThroughput" value="<PositiveIntegerValue"/>
    <add key="TableIndexingPolicy" value="<jsonindexdefn>"/>
    <add key="TableUseGatewayMode" value="True|False"/>
    <add key="TablePreferredLocations" value="Location1|Location2|Location3|Location4>"/>....
</appSettings>


### <a name="is-there-any-change-to-existing-customers-that-use-the-existing-standard-table-sdk"></a>既存の Standard Table SDK を使用する既存の顧客を対象とする変更はありますか?
なし。 既存の Standard Table SDK を使用する既存または新規のお客様を対象とする変更はありません。 

### <a name="how-do-i-view-table-data-that-is-stored-in-azure-cosmos-db-for-use-with-the-table-api-review"></a>Table API (プレビュー) で使用するために、Azure Cosmos DB に格納されているテーブル データを表示するにはどうすればよいですか? 
Azure Portal を使用してデータを参照できます。 また、Table API (プレビュー) コードまたは下記のツールを使用することもできます。 

### <a name="which-tools-will-work-with-table-api-preview"></a>Table API (プレビュー) で動作するのはどのツールですか? 
以前のバージョンの Azure Explorer (0.8.9) です。

以前に指定した形式の接続文字列を取得する柔軟性を備えたツールは、新しい Table API (プレビュー) に対応します。 テーブル ツールの一覧については、「[Azure Storage クライアント ツール](../storage/storage-explorers.md)」をご覧ください。 

### <a name="does-powershellcli-work-with-the-new-table-api-preview-"></a>PowerShell/CLI は新しい Table API (プレビュー) で動作しますか?
Table API (プレビュー) 用の PowerShell/CLI のサポートを追加する予定です。 

### <a name="is-the-concurrency-on-operations-controlled"></a>操作の同時実行は制御されますか?
はい。オプティミスティック同時実行制御は、Standard Table API での想定どおりに ETag メカニズムを使用して提供されます。 

### <a name="is-odata-query-model-supported-for-entities"></a>エンティティの OData クエリ モデルはサポートされていますか? 
はい。Table API (プレビュー) では、OData クエリと Linq クエリをサポートしています。 

### <a name="can-i-connect-to-standard-azure-table-and-the-new-premium-table-api-preview-at-side-by-side-in-same-application-"></a>同じアプリケーションで Standard Azure Table と新しい Premium Table API (プレビュー) に同時に接続できますか? 
はい。これを実現するには、接続文字列を使用して独自の URI を参照する、CloudTableClient の 2 つのインスタンスを作成します。

### <a name="how-do-i-migrate-existing-table-storage-application-to-this-new-offering"></a>既存の Table Storage アプリケーションをこの新しいサービスに移行するにはどうすればよいですか?
既存の Table Storage データで新しい Table API を利用する場合は、askdocdb@microsoft.com までご連絡ください。 

### <a name="what-is-the-roadmap-for-this-service-when-will-other-functionality-of-standard-table-api-be-offered"></a>このサービスのロードマップを教えてください。Standard Table API の他の機能はいつ提供されますか?
GA に向けて、SAS トークン、ServiceContext、統計、暗号化、分析などの機能のサポートを追加する予定です。  [UserVoice](https://feedback.azure.com/forums/263030-documentdb) でフィードバックをお寄せください。 

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-say-i-start-with-n-gbs-of-data-and-my-data-will-grow-to-1-tb-overtime"></a>最初は N GB のデータが時間の経過と共に 1 TB に増加した場合、このサービスではストレージ サイズはどのように拡張されるのですか?  
Cosmos DB は、水平スケーリングを使用して無制限のストレージを提供するように設計されています。 サービスがストレージを監視し、ストレージを効率的に増やします。 

### <a name="how-do-i-monitor-the-table-api-preview-offering"></a>Table API (プレビュー) を監視するにはどうすればよいですか?
Table API (プレビュー) のメトリック ウィンドウを使用して、要求とストレージ使用量を監視できます。 

### <a name="how-do-i-calculate-throughput-i-require"></a>必要なスループットを計算するにはどうすればよいですか?
「[Estimate Request Units and Data Storage (要求ユニットとデータ ストレージの見積もり)](https://www.documentdb.com/capacityplanner)」に記載されているように、容量見積もりツールを使用して操作に必要な TableThroughput を計算できます。 一般に、エンティティを JSON として表し、操作数を指定します。 

### <a name="can-i-use-the-new-table-api-preview-sdk-locally-with-the-emulator"></a>エミュレーターで新しい Table API (プレビュー) SDK をローカルで使用できますか?
新しい SDK を使用すると、ローカル エミュレーターで Table API (プレビュー) を使用できます。 [こちら](documentdb-nosql-local-emulator.md)から新しいエミュレーターをダウンロードしてください。 app.config の StorageConnectionString 値が、"DefaultEndpointsProtocol=https;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=https://localhost:8081" である必要があります。 

### <a name="can-my-existing-application-work-with-the-table-api-preview"></a>既存のアプリケーションを Table API (プレビュー) で動作させることはできますか? 
新しい Table API (プレビュー) の表面領域は、.NET API での作成、削除、更新、クエリの各操作で既存の Azure Standard Table SDK と互換性があります。 Table API (プレビュー) では、パーティション キーと行キーの両方が必要であるため、行キーがあることを確認する必要があります。このサービスの GA に向けて、他の SDK のサポートも追加する予定です。

### <a name="do-i-need-to-migrate-existing-azure-table-based-application-to-new-sdk-if-i-do-not-want-to-use-table-api-preview-features"></a>Table API (プレビュー) の機能を使用しない場合、Azure テーブル ベースの既存のアプリケーションを新しい SDK に移行する必要はありますか?
いいえ。既存のお客様は、中断することなく Standard Table 資産を作成したり、現在の資産を使用したりできます。 ただし、新しい Table API (プレビュー) を使用しない場合、自動インデックス、追加の整合性オプション、またはグローバル分散によるメリットは得られません。 

### <a name="how-do-i-add-replication-for-the-data-in-premium-table-api-preview-across-multiple-regions-of-azure"></a>Premium Table API (プレビュー) で、Azure の複数のリージョン間でのデータのレプリケーションを追加するにはどうすればよいですか?
Cosmos DB ポータルの[グローバル レプリケーション設定](documentdb-portal-global-replication.md)を使用して、アプリケーションに適したリージョンを追加できます。 グローバル分散型のアプリケーションを開発するには、低待機時間の読み取りを実現するために、PreferredLocation 情報をローカル リージョンに設定したアプリケーションを追加する必要があります。 

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in--premium-table-apipreview"></a>Premium Table API (プレビュー) でアカウントのプライマリ書き込みリージョンを変更するにはどうすればよいですか?
Cosmos DB のグローバル レプリケーション ポータル ウィンドウを使用してリージョンを追加し、必要なリージョンにフェールオーバーできます。 手順については、[複数リージョンの Azure Cosmos DB アカウントを使用した開発](documentdb-regional-failovers.md)に関する記事をご覧ください。 

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>データを分散するときに低待機時間を実現するために、優先読み取りリージョンを構成するにはどうすればよいですか? 
ローカルの場所から読み取りを実行できるように、app.config の PreferredLocation キーを利用する必要があります。 既存のアプリケーションの場合、LocationMode が設定されていると、Table API (プレビュー) はエラーをスローします。Premium Table API(プレビュー) は app.config ファイルからこの情報を取得するので、該当のコードを削除してください。  これらの設定は、「[Azure Cosmos DB の機能](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)」に記載されています。


### <a name="how-do-i-think-of-consistency-in-table-api-preview"></a>Table API (プレビュー) では整合性についてどのように考えればよいですか? 
Cosmos DB では、整合性、可用性、待機時間の最適なトレードオフを提供します。 このリリース時点では、5 種類の整合性レベルの中から選択できます。 Table API (プレビュー) の開発者はこれらの整合性レベルを使用できます。 これにより、開発者はデータを照会するときにテーブル レベルで個々の要求に最適な整合性モデルを選択できます。  クライアントは、接続時に整合性レベルを指定できます。これは、app.config で TableConsistencyLevel キーの値を設定することで変更できます。 Table API (プレビュー) では、既定でセッションの整合性が適用され、独自の書き込みの読み取りで低待機時間の読み取りを実現します。 詳細については、[整合性レベル](documentdb-consistency-levels.md)に関する記事をご覧ください。 既定では、現在の Standard Table はリージョン内では厳密な整合性を提供し、セカンダリの場所では最終的な整合性を提供します。  

### <a name="does-this-mean-compared-to-eventual--strong-consistency-that-is-possible-with-standard-table---we-now-have-more-choices"></a>つまり、Standard Table では最終的な整合性と厳密な整合性を使用できるが、ほかにも選択肢があるということですか?
はい。各選択肢については、[整合性レベル](documentdb-consistency-levels.md)に関する記事をご覧ください。整合性レベルは、アプリケーション開発者が Cosmos DB の分散特性を活用する際に役立ちます。 整合性も保証の対象となるため、整合性レベルを安心してすぐに活用できます。 これらの設定は、「[Azure Cosmos DB の機能](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)」に記載されています。

### <a name="when-global-distribution-is-enabled--how-long-does-it-take-to-replicate-the-data"></a>グローバル分散を有効にした場合、データのレプリケーションにどのくらいの時間がかかりますか?
データはローカル リージョンで永続的にコミットされ、ほんの数ミリ秒ですぐに他のリージョンにプッシュされます。このレプリケーションは、データ センターの RTT にのみ依存します。 Cosmos DB のグローバル分散機能については、[Azure Cosmos DB: Azure のグローバル分散データベース サービス](documentdb-distribute-data-globally.md)に関する記事をご覧ください。

### <a name="can-the-read-request-consistency-be-changed"></a>読み取り要求の整合性は変更できますか?
Cosmos DB では、コンテナー (テーブル) レベルで整合性を設定できます。 SDK では、app.config ファイルで TableConsistencyLevel キーの値を指定することでレベルを変更することもできます。 指定できる値は、Strong、Bounded Staleness、Session、ConsistentPrefix、Eventual です。 詳細については、[整合性レベル](documentdb-consistency-levels.md)に関する記事をご覧ください。 要求の整合性は、テーブルに設定されているものより高いレベルにはできないことに注意してください。 たとえば、テーブルに最終的な整合性を設定し、要求の整合性レベルを厳密な整合性に設定した場合、これは機能しません。 

### <a name="how-does-premium-table-api-preview-account-take-care-of-failover-in-case-a-region-goes-down"></a>リージョンがダウンした場合、Premium Table API (プレビュー) アカウントではフェールオーバーにどのように対処するのでしょうか? 
Premium Table API (プレビュー) では、Cosmos DB のグローバル分散型プラットフォームを利用します。 アプリケーションがデータ センターのダウンタイムを許容できるようにするには、Cosmos DB ポータルでアカウントのリージョンを少なくとももう 1 つ有効にする必要があります ([複数リージョンの Azure Cosmos DB アカウントを使用した開発](documentdb-regional-failovers.md)に関する記事を参照)。 ポータルを使用してリージョンの優先順位を設定できます ([複数リージョンの Azure Cosmos DB アカウントを使用した開発](documentdb-regional-failovers.md)に関する記事を参照)。 アカウントの多数のリージョンを追加し、優先順位を指定することでフェールオーバー先を制御できます。 データベースを利用するには、そのリージョンでもアプリケーションを提供する必要があることは言うまでもありません。 このようにすることで、ダウンタイムが発生しなくなります。 クライアント SDK は自動回帰します。つまり、リージョンがダウンしていることを検出すると、新しいリージョンに自動的にフェールオーバーできます。

### <a name="is-premium-table-api-preview-enabled-for-backups"></a>Premium Table API (プレビュー) ではバックアップは有効になっていますか?
はい。Premium Table API (プレビュー) では、バックアップに Cosmos DB のプラットフォームを利用します。 バックアップは自動的に作成されます。 Cosmos DB のバックアップについては、[Azure Cosmos DB でのオンライン バックアップと復元](C:\Users\govindk\azure-docs-pr\articles\documentdb\documentdb-online-backup-and-restore.md)に関する記事をご覧ください。

 
### <a name="does-the-table-api-preview-index-all-attributes-of-entities-by-default"></a>Table API (プレビュー) では、既定でエンティティのすべての属性のインデックスが作成されるのですか?
はい。既定では、エンティティのすべての属性のインデックスが作成されます。 インデックス作成の詳細については、[Azure Cosmos DB のインデックス作成ポリシー](documentdb-indexing-policies.md)に関する記事をご覧ください。 

### <a name="does-this-mean-i-do-not-have-to-create-different-indexes-to-satisfy-the-queries"></a>つまり、クエリを満たすためにさまざまなインデックスを作成する必要はないということですか? 
はい。Cosmos DB はすべての属性の自動インデックス作成機能を備えています。スキーマ定義は不要です。 これにより、開発者はインデックスの作成と管理を気にするのではなく、アプリケーションに注力できます。 インデックス作成の詳細については、[Azure Cosmos DB のインデックス作成ポリシー](documentdb-indexing-policies.md)に関する記事をご覧ください。

### <a name="can-the-indexing-policy-be-changed"></a>インデックス作成ポリシーは変更できますか?
はい。インデックス定義を提供することでインデックスを変更できます。 各設定の意味については、「[Azure Cosmos DB の機能](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)」をご覧ください。 これらの設定を適切にエンコードし、エスケープする必要があります。  

app.config ファイルに JSON 形式の文字列で指定します。
{ "indexingMode": "consistent", "automatic": true, "includedPaths": [ { "path": "/somepath", "indexes": [ { "kind": "Range", "dataType": "Number", "precision": -1 }, { "kind": "Range", "dataType": "String", "precision": -1 } ] } ], "excludedPaths": [ { "path": "/anotherpath" } ] }

### <a name="cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-like-sorting-aggregates-hierarchy-and-other-functionality---is-this-planned-to-be-added-to-the-table-api-"></a>プラットフォームとしての Cosmos DB は、並べ替え、集計、階層などの多数の機能を備えているようですが、これは Table API に追加される予定ですか? 
プレビューでは、Azure Cosmos DB は、Table API について Azure Table Storage と同じクエリ機能をサポートしています。 また、並べ替え、集計、地理空間クエリ、階層、さまざまな組み込み関数もサポートしています。 将来のサービス更新プログラムで Table API に追加機能が提供される予定です。 これらの機能の概要については、[Azure Cosmos DB のクエリ](../documentdb/documentdb-sql-query.md)に関する記事をご覧ください。
 
### <a name="when-should-i-change-tablethroughput-for-the-table-api-preview"></a>Table API (プレビュー) の TableThroughput は、どのようなときに変更する必要がありますか?
データの ETL を実行するときや、大量のデータを短時間でアップロードする必要があるときに、TableThroughput を変更する必要があります。 

または

メトリックで使用されたスループットがプロビジョニング スループットを超えると、バックエンドでコンテナーのスループットがさらに必要になるので、調整が行われます。 詳細については、[スループットの設定](documentdb-set-throughput.md)に関する記事をご覧ください。

### <a name="can-i-scale-up-or-down-the-throughput-of-my-table-api-preview-table"></a>Table API (プレビュー) のテーブルのスループットはスケールアップまたはスケールダウンできますか? 
はい。スケールアップとスケールダウンは、Cosmos DB ポータルのスケール ウィンドウを使用して実行できます。 詳細については、[スループットの設定](documentdb-set-throughput.md)に関するトピックをご覧ください。

### <a name="can-premium-table-api-preview-leverage-ru-per-minute-offering"></a>Premium Table API (プレビュー) では 1 分あたりの RU を利用できますか? 
はい。Premium Table (プレビュー) では、Cosmos DB の機能を利用して、パフォーマンス、待機時間、可用性、整合性の SLA を提供します。 これにより、[要求ユニット](documentdb-request-units.md)に関する記事に記載されているように、1 分あたりの RU を利用できます。 この機能により、ピーク時にプロビジョニングしなくて済むようになり、ワークロードのスパイクを平滑化できます。

### <a name="is-there-a-default-tablethroughput-which-is-set-for-newly-provisioned-tables"></a>新しくプロビジョニングされたテーブルに設定される既定の TableThroughput はありますか?
はい。app.config で TableThroughput を上書きしておらず、Cosmos DB であらかじめ作成されているコンテナーを使用していない場合、スループットが 400 に設定されたテーブルが作成されます。
 
### <a name="is-there-any-change-of-pricing-for-existing-customers-of-standard-table-api"></a>Standard Table API の既存の顧客を対象とする価格の変更はありますか?
なし。 Standard Table API の既存のお客様を対象とする価格の変更はありません。 

### <a name="how-is-the-price-calculated-for-the-table-apipreview"></a>Table API (プレビュー) の料金はどのように計算されますか? 
割り当てられた TableThroughput によって異なります。 

### <a name="how-do-i-handle-any-throttling-on-the-tables-in-table-api-preview-offering"></a>Table API (プレビュー) では、テーブルの調整にどのように対処すればよいですか? 
要求レートが基になるコンテナーのプロビジョニング スループットの容量を超えると、エラーが発生し、SDK は再試行ポリシーを使用して呼び出しを再試行します。

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-leverage-premium-table-preview-offering-of-cosmos-db"></a>Cosmos DB の Premium Table (プレビュー) を利用する際に、PartitionKey と RowKey 以外にスループットを選択する必要があるのはなぜですか?
Cosmos DB では、app.config にコンテナーのスループットが指定されていない場合、既定のスループットが設定されます。 

Cosmos DB では、操作に上限を設定してパフォーマンスと待機時間を保証します。 これは、エンジンがテナントの操作にガバナンスを適用できる場合に可能になります。 TableThroughput を設定すると、プラットフォームでこの容量が予約され、操作が正常に完了することが保証されるので、基本的に保証されたスループットと待機時間が確保されます。  
また、スループットの仕様により、スループットを弾力的に変更して、アプリケーションの季節性を活用し、スループットのニーズを満たして、コストを削減できます。

### <a name="azure-storage-sdk-has-been-very-cheap-for-me-as-i-only-pay-to-store-the-data-and-i-rarely-query-cosmos-dbs-new-offering-seems-to-be-charging-me-even-though-i-have-not-performed-single-transaction-or-stored-anything-whats-going-on-here"></a>データの保存にしか料金を支払っておらず、クエリを実行することはほとんどないため、Azure Storage SDK は非常に安価でした。 Cosmos DB の新しいサービスでは、トランザクションを 1 つも実行していない場合や何も保存していない場合でも課金されているようです。 どうなっているのでしょうか?

Cosmos DB は、可用性、待機時間、スループットが保証された、グローバル分散型の SLA ベースのシステムとして設計されています。 Cosmos DB でスループットを予約すると、他のシステムとは異なり、そのスループットが保証されます。 また、Cosmos DB は、お客様が長い間求めてきた追加機能 (セカンダリ インデックス、グローバル分散など) も備えています。プレビュー期間中はスループットに最適化されたモデルを提供していますが、長期的には、お客様のニーズを満たすためにストレージに最適化されたモデルを提供する予定です。  

### <a name="i-never-get-quota-full-indicating-a-partition-is-full-when-i-keep-ingesting-data-into-azure-table-storage-with-the-table-apipreview--i-can-get-this-error-is-this-offering-limiting-me-and-forcing-me-to-change-my-present-application"></a>Azure Table Storage にデータを取り込み続けても、"クォータ" が上限に達する (パーティションが満杯であることを示す) ことは一度もありませんでした。 Table API (プレビュー) では、このエラーが発生する可能性があります。 このサービスには制限があり、現在のアプリケーションを変更しなければならないのでしょうか?

Cosmos DB は、待機時間、スループット、可用性、整合性を保証し、無制限のスケールを提供する SLA ベースのシステムです。 保証された Premium パフォーマンスを確保するには、データ サイズとインデックスが管理可能であり、スケーラブルであることを確認する必要があります。 パーティション キーごとのエンティティ数/項目数に 10 GB の制限を設けているのは、ルックアップ クエリの優れたパフォーマンスを確実に提供するためです。 すべての情報を 1 つのパーティションに格納し、そのパーティションに対してクエリを実行すると、ホット パーティションになります。Azure Storage でもアプリケーションのスケールを適切に確保するために、ホット パーティションにならないようにしてください。  

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-new-table-api-preview"></a>新しい Table API (プレビュー) で PartitionKey と RowKey が必要なのはそのためですか? 
はい。 Table API (プレビュー) の表面領域は Azure Table Storage SDK とほぼ同じであるため、パーティション キーはデータを分散する優れた方法となります。 行キーはそのパーティション内で一意です。 行キーが存在する必要があり、Standard SDK の場合と同様に、行キーを null にすることはできません。 ビルド プレビュー時点で、RowKey の長さは 255 バイト、PartitionKey の長さは 100 バイトです (間もなく 1 KB に増えます)。 

### <a name="what-will-be-the-error-messages-of-table-api-preview-"></a>Table API (プレビュー) のエラー メッセージはどのようなものになりますか?
このプレビューは Standard Table と互換性があるため、エラーの大半は Standard Table のエラーにマップされます。 

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another--in-tables-api-preview"></a>Table API (プレビュー) で多数のテーブルを次々に作成しようとすると調整が行われるのはなぜですか?
Cosmos DB は、待機時間、スループット、可用性、整合性を保証する SLA ベースのシステムです。 Cosmos DB はプロビジョニングされるシステムであるため、これらの要件を保証するためにリソースが予約されています。 テーブルを次々に作成すると、その作成ペースが検出され、調整が行われます。 テーブルの作成ペースを確認し、1 分あたり 5 つ以下に抑えることをお勧めします。 Table API (プレビュー) はプロビジョニングされるシステムであるため、プロビジョニングした時点で課金されることに注意してください。  

# <a name="graph-apipreview"></a>Graph API (プレビュー)
## <a name="database-questions-about-developing-against-graph-apipreview"></a>Graph API (プレビュー) に対する開発についてのデータベース質問
### <a name="how-can-i-leverage-graph-api-preview-with-cosmos-db"></a>Cosmos DB で Graph API (プレビュー) を利用するにはどうすればよいですか?
Graph API (プレビュー) の機能は、拡張ライブラリから利用できます。 これは Microsoft Azure Graphs と呼ばれ、NuGet で提供されます。 

### <a name="it-looks-like-you-support-gremlin-as-traversal-language-do-you-plan-to-add-some-more-forms-of-query-"></a>トラバーサル言語として Gremlin がサポートされているようですが、クエリの他の形式を追加する予定はありますか?
はい。将来、クエリの他のメカニズムを追加する予定です。 

### <a name="how-can-i-use-the-new-graph-apipreview-offering"></a>新しい Graph API (プレビュー) を使用するにはどうすればよいですか? 
まず、[Graph API](../cosmos-db/create-graph-dotnet.md) のクイックスタートを完了してください。




[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md

