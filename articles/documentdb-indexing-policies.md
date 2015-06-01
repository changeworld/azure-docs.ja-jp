<properties 
	pageTitle="DocumentDB インデックス作成ポリシー | Azure" 
	description="DocumentDB でのインデックス作成のしくみと、インデックス作成ポリシーを構成する方法について説明します。" 
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="03/13/2015" 
	ms.author="mimig"/>


DocumentDB インデックス作成ポリシー
============================

DocumentDB は完全にスキーマフリーのデータベース システムです。インデックスを作成する JSON ドキュメントには、前提となるスキーマはなく、どのようなスキーマも不要です。このため、アプリケーション データ モデルをすばやく定義し、反復処理できます。コレクションにドキュメントを追加するたびに、DocumentDB がすべてのドキュメント プロパティにインデックスを自動作成してクエリを利用可能にします。自動インデックス作成により、異なる種類のドキュメントも格納できます。

ドキュメントの自動インデックス作成は、書き込み最適化、ロックフリー、ログ構造化メンテナンス技法により実現されます。DocumentDB は一貫性のあるクエリを提供する一方で、高速なボリューム書き込みもサポートします。

DocumentDB インデックス作成サブシステムは以下をサポートするように設計されています。

·         スキーマまたはインデックスの定義を必要とせず、効率的で高度に階層化したリレーショナル クエリ。

·         一定量かつ持続的に実行される書き込みに対する、一貫性のあるクエリ結果。一貫性のあるクエリで書き込みスループットの負荷が高くなる場合は、一定量の書き込みを持続的に処理する間、インデックスは効率的かつ段階的にオンラインで更新されます。

·         ストレージの効率性。コスト効率性を実現するため、インデックスのディスク上のストレージ オーバーヘッドは、有限かつ予測可能なものにします。

·         マルチテナント。インデックスの更新は DocumentDB コレクションごとに割り当てられたシステム リソース量の範囲内で実行されます。

ほとんどのアプリケーションで既定の自動インデックス作成ポリシーを使用できます。これによって柔軟性が保たれ、パフォーマンスとストレージ効率性が適切にトレードオフされます。一方で、カスタムのインデックス作成ポリシーを指定すると、クエリや書き込みのパフォーマンスとインデックスのストレージ オーバーヘッドとの間できめ細かいトレードオフを実現できます。

たとえば、特定のドキュメントまたはドキュメント内のパスをインデックス作成から除外することによって、インデックス作成に使用されるストレージ領域のほか、インデックスのメンテナンスの挿入時間コストを削減できます。インデックスの種類を変更して範囲クエリに対する適合性を向上させたり、インデックスの有効桁数をバイト単位で上げてクエリのパフォーマンスを向上させたりできます。この記事では、DocumentDB で使用可能なインデックス作成のさまざまな構成オプションとワークロードに合わせたインデックス作成ポリシーのカスタマイズ方法について説明します。

<a id="HowWorks"></a>DocumentDB インデックス作成のしくみ
-----------------------------

DocumentDB のインデックス作成は、JSON 文法によりドキュメントの**ツリー表現**が可能になるという事実を活用しています。JSON ドキュメントをツリーとして表現するには、ダミーのルート ノードを作成し、その下のドキュメントに含まれる実際のノードに対する親とする必要があります。JSON ドキュメント内の配列インデックスを含む各ラベルはツリーのノードになります。次の図には、JSON ドキュメントと対応するツリー表現の例を示しています。

![Indexing Policies](media/documentdb-indexing-policies/image001.png)


たとえば、上の例の JSON プロパティである {"headquarters": "Belgium"} プロパティはパス /"headquarters"/"Belgium" に対応します。JSON 配列 {"exports": [{"city": "Moscow"}, {"city": Athens"}]} はパス /"exports"/0/"city"/"Moscow" と /"exports"/1/"city"/"Athens" に対応します。

**注:** パス表現は構造やスキーマとドキュメント内のインスタンス値の境界をぼやかし、DocumentDB を完全にスキーマフリーにします。

DocumentDB において、ドキュメントは、SQL を使用して照会したり、単一のトランザクション スコープ内で処理したりできる、コレクションに編成されます。各コレクションは、パス単位の独自のインデックス作成ポリシーにより構成できます。次のセクションでは、DocumentDB コレクションのインデックス処理の動作を構成する方法を説明します。

<a id="ConfigPolicy"></a>コレクションのインデックス作成ポリシーの構成
-------------------------------------------

次の例は、DocumentDB REST API を使用して、コレクションの作成中に カスタム インデックス作成ポリシーを設定する方法を示しています。この例では、 パス、インデックスの種類、有効桁数で表現されるインデックス作成ポリシーを表します。


	POST https://<REST URI>/colls HTTP/1.1                                                  
 	...                                                             
 	Accept: application/json 
                                                                                                                         
 	{                                                                     
	 "id":"customIndexCollection",                                     
	 "indexingPolicy":{                                                 
     "automatic":true,                                            
	 "indexingMode":"Consistent",                                     
	 "IncludedPaths":[                                       
	           {                                                             
	              "IndexType":"Hash",                                        
	              "Path":"/"                                                 
	           }                                                  
	        ],                                                               
	        "ExcludedPaths":[                                                
	           "/"nonIndexedContent"/*"                                 
	        ]                                                               
	     }                                                                 
	 }                                                                                                                                                
 	...                                                                      
                  
                                                        
	 HTTP/1.1 201 Created                                                     


**注:** コレクションのインデックス作成ポリシーは作成時に指定する必要があります。コレクションの作成後にインデックス作成ポリシーを変更することは許可されていませんが、DocumentDB の今後のリリースではサポートされる予定です。

**注:** 既定では、DocumentDB はドキュメント内のすべてのパスについて、ハッシュ インデックスと合わせてインデックスを作成します。内部のタイムスタンプ (_ts) のパスは、範囲インデックスと共に格納されます。

### 自動インデックス作成

コレクションがすべてのドキュメントのインデックスを自動的に作成するか選択できます。既定では、すべてのドキュメントのインデックスが自動的に作成されますが、これを無効にすることができます。自動インデックス作成が無効になっている場合、自己リンクまたは ID を使用したクエリでのみドキュメントにアクセスできます。

自動インデックス作成が無効になっている場合でも、特定のドキュメントだけを選択してインデックスに追加できます。反対に、自動インデックス作成を有効にしたまま、特定のドキュメントだけを選択して除外できます。インデックス作成の有効または無効の構成は、ドキュメントのサブセットだけにクエリを実行する必要がある場合に役立ちます。

自動のプロパティの値を true または false に指定することで、既定のポリシーを構成できます。ドキュメントの挿入または置換の際に x-ms-indexingdirective 要求ヘッダーを設定して1 つのドキュメントをオーバーライドできます。

次の例は、[DocumentDB .NET SDK](https://github.com/Azure/azure-documentdb-java) と [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx) プロパティを使用して、明示的にドキュメントを含める方法を示しています。

	// If you want to override the default collection behavior to either     
	// exclude (or include) a Document from indexing,                                                                                           
	// use the RequestOptions.IndexingDirective property.                                  
	                                                                         
	client.CreateDocumentAsync(defaultCollection.SelfLink,  
	    new { Id = "AndersenFamily", isRegistered = true },                            
		new RequestOptions                               
		    {                                                                    
			    IndexingDirective = IndexingDirective.Include                                                                                      
			}
		);                                                                  


### インデックス作成モード

同期 (**一貫した**) または非同期 (**遅延**) のインデックス更新を選択できます。既定では、コレクションに対するドキュメントの挿入、置換、削除のたびに、インデックスが同期的に更新されます。これにより、クエリには、インデックスが「追いつく」必要がある遅延が発生せず、ドキュメントの読み取りと同じ一貫性レベルが保証されます。

DocumentDB は、書き込みを最適化し、同期インデックス メンテナンスによってドキュメントの持続的書き込みをサポートしていますが、特定のコレクションのインデックスが遅れて更新されるように構成することもできます。インデックスの遅延作成はデータが一括で書き込まれ、コンテンツのインデックス作成に必要な処理が比較的長い時間に渡って定期的に分割される場合に適しています。これにより、プロビジョニングされるスループットを効果的に使用し、ピーク時の書き込み要求を最小の待機時間で処理できます。インデックスが遅延作成される場合でも、クエリの結果は、データベースのアカウント用に構成された整合性レベルに関係なく、最終的に一貫性を持ちます。

次に示すサンプルは、すべてのドキュメントの挿入に一貫性のある自動インデックス作成と .NET SDK を使用して DocumentDB コレクションを作成する方法を示します。


	 // Default collection creates a hash index for all string and numeric    
	 // fields. Hash indexes are compact and offer efficient                                                                                           
	 // performance for equality queries.                                     
	                                                                          
	 var defaultCollection = new DocumentCollection { Id ="defaultCollection" };                                                   
	                                                                          
	 // Optional. Override Automatic to false for opt-in indexing of documents.                                                                
	                                                                          
	 defaultCollection.IndexingPolicy.Automatic = true;                       
	                                                                          
	 // Optional. Set IndexingMode to Lazy for bulk import/read heavy        
	 // collections. Queries might return stale results with Lazy indexing.       
	                                                                          
	 defaultCollection.IndexingPolicy.IndexingMode = IndexingMode.Consistent; 
	                                                                          
	 defaultCollection = await client.CreateDocumentCollectionAsync(database.SelfLink,defaultCollection);                                                      


### インデックスの種類と有効桁数

インデックス エントリに使用される種類またはスキームは、インデックスのストレージとパフォーマンスに直接の影響を及ぼします。通常、使用する有効桁数が高いスキームほど、クエリの速度は高くなります。ただし、インデックスのストレージのオーバーヘッドも高くなります。低い有効桁数を選択することは、クエリの実行時により多くのドキュメントを処理する必要があることを意味しますが、ストレージのオーバーヘッドは低くなります。

任意のパスの値に対するインデックスの有効桁数は 3 ～ 7 バイトの間にすることができます。
ドキュメントが異なると同じパスが数値であったり文字列の値である場合があるため、それぞれ別に制御できます。.NET SDK では、これらの値は [NumericPrecision](http://msdn.microsoft.com/library/microsoft.azure.documents.indexingpath.numericprecision.aspx) プロパティおよび [StringPrecision](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.indexingpath.stringprecision.aspx) プロパティに対応します。

サポートされるインデックスの種類にはハッシュと範囲の 2 つがあります。インデックスの種類として**ハッシュ**を選択すると、効率的な等値クエリが可能になります。ほとんどのケースでは、ハッシュ インデックスには既定値 3 バイトより高い有効桁数は不要です。

インデックスの種類として**範囲**を選択すると、範囲クエリ (>、<、>=、<=、!= を使用) が可能になります。値の範囲が大きいパスの場合、6 バイトなど高い有効桁数の使用が推奨されます。より高い有効桁数の範囲インデックスが必要なケースで一般的なものは、エポック時間として格納されるタイムスタンプです。

効率的な範囲クエリを必要としない場合、ハッシュ インデックスの既定値でストレージとパフォーマンスの最適なバランスが提供されます。範囲クエリをサポートするには、カスタム インデックス ポリシーを指定する必要があることに注意してください。

> [AZURE.NOTE] 範囲インデックスは数値についてのみサポートされます。
  
次の例では、.NET SDK を使用して、コレクション内の範囲インデックスの有効桁数を増やす方法を示します。特定のパス "/" を使用することに注意してください。これについては、次のセクションで説明します。


	 // If your collection has numeric fields that need to be queried    
	 // against ranges (>,>=,<=,<), then you can configure the collection to 
	 // use range queries for all numeric values.                                                                                                      
 
	var rangeDefault = new DocumentCollection { Id = "rangeCollection" };                                                              
	rangeDefault.IndexingPolicy.IncludedPaths.Add(                                                             
												 new IndexingPath {   
													IndexType = IndexType.Range, Path = "/", 
													NumericPrecision = 7 }
												 ); 
	 rangeDefault = await client.CreateDocumentCollectionAsync(database.SelfLink, rangeDefault);   


### インデックスのパス

ドキュメントでインデックス作成に含めたり除外するパスを選択できます。これにより、クエリのパターンが事前にわかっている場合に、書き込みパフォーマンスの向上とインデックス ストレージの削減が可能になります。

インデックスのパスはルート (/) で始まり、通常、? ワイルドカード演算子で終わります。これは、プレフィックスに複数の可能な値があることを示します。たとえば、SELECT * FROM Families F WHERE F.familyName = "Andersen" を処理するには、コレクションのインデックス ポリシーに /"familyName"/? のインデックスのパスを含める必要があります。

インデックスのパスは * ワイルドカード演算子を使用して、プレフィックスの下のパスの動作を再帰的に指定することもできます。たとえば、/"payload"/* を使用して、payload プロパティの下のすべてをインデックス作成から除外できます。

インデックスのパスを指定するための一般的なパターンは次のとおりです。

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top">
                <p>
                    <strong>パス</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>説明またはユース ケース</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /
                </p>
            </td>
            <td valign="top">
                <p>
                    コレクションの既定のパス。再帰的で、ドキュメント ツリー全体に適用されます。
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /"prop"/?
                </p>
            </td>
            <td valign="top">
                <p>
                    次のようなクエリを処理するために必要なインデックスのパス (ハッシュまたは範囲の各種類)。
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop &gt; 5
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /"prop"/*
                </p>
            </td>
            <td valign="top">
                <p>
                    指定されたラベルの下のすべてのパスのインデックスのパス。インデックス作成からの除外のみを指定します。
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /"prop"/"subprop"/
                </p>
            </td>
            <td valign="top">
                <p>
                    クエリ実行中に、指定されたパスがないドキュメントを除外するために使用されるインデックスのパス。
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /"prop"/"subprop"/?
                </p>
            </td>
            <td valign="top">
                <p>
                    次のクエリを処理するために必要なインデックスのパス (ハッシュまたは範囲の各種類)。
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop &gt; 5
                </p>
            </td>
        </tr>
    </tbody>
</table>

> [AZURE.NOTE] カスタム インデックスのパスを設定するときに、特定のパス "/" で表されるドキュメント ツリー全体の既定のインデックス作成ルールを指定する必要があります。

次の例では、範囲インデックスと 7 バイトのカスタムの有効桁数を使用して、特定のパスを構成します。


 	// If you only want to specify range queries against a specific field,   
 	// then use a range index against that field. Doing so reduces the   
	// amount of storage required for indexes for the collection. In this    
 	// case, the query creates an index against the JSON path                   
 	// /"CreatedTimestamp"/?    
 	// allowing queries of the form WHERE CreatedTimestamp [>] X            
	
	var pathRange = new DocumentCollection { Id = "rangeSinglePathCollection" };    
	
	pathRange.IndexingPolicy.IncludedPaths.Add(
								new IndexingPath { 
										IndexType = IndexType.Range, 
										Path = "/"CreatedTimestamp"/?",   
										NumericPrecision = 7   
							 			}
									);   
	
	pathRange.IndexingPolicy.IncludedPaths.Add(   
											 new IndexingPath {  
											  	 Path = "/"  
											 });                                                                      
                                                   
	 pathRange = await client.CreateDocumentCollectionAsync(database.SelfLink, pathRange);      


DocumentDB では、クエリが範囲演算子を使用し、照会されたパスに対して範囲インデックスがなく、インデックスから処理できるフィルターが他にない場合、エラーが返されます。ただし、これらのクエリは範囲インデックスがなくても、.NET SDK を使用することで、REST API 内の x-ms-documentdb-allow-scans ヘッダーまたは AllowScanInQueryrequest オプションを使用して実行できます。

次の例では、"*" ワイルドカードを使用して、パスのサブツリーをインデックス作成から
除外します。

	var excluded = new DocumentCollection { Id = "excludedPathCollection" };                                                                       
  	excluded.IndexingPolicy.IncludedPaths.Add(
	newIndexingPath {  Path = "/" });  

	excluded.IndexingPolicy.ExcludedPaths.Add("/" nonIndexedContent"/*");    
	excluded = await client.CreateDocumentCollectionAsync(database.SelfLink,excluded);                                                               


パフォーマンスのチューニング
------------------

別のインデックス作成ポリシーの構成を評価する場合は、DocumentDB API からポリシーの記憶域とスループットの影響を測定する必要があります。

コレクションの記憶域のクォータと使用状況を確認するには、HEAD 要求または GET 要求をコレクションのリソースに対して実行し、x-ms-request-quota と x-ms-request-usage のヘッダーを調べます。.NET SDK では、[ResourceResponse<T>](http://msdn.microsoft.com/library/dn799209.aspx) の [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) プロパティと [ DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) プロパティに、これらの対応する値が含まれています。


 	// Measure the document size usage (which includes the index size) against   
 	// different policies.        
	 ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(collectionSelfLink);  
	 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentSizeQuota, collectionInfo.DocumentSizeUsage);                                       


書き込み操作 (作成、更新、削除) のたびにインデックス作成のオーバーヘッドを測定するには、x-ms-request-charge ヘッダー (または、.NET SDK の [ResourceResponse<T>](http://msdn.microsoft.com/library/dn799209.aspx) の同等の [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) プロパティ) を調査して、これらの操作で使用される要求単位の数を測定します。


 	// Measure the performance (request units) of writes.     
 	ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);              
                                                                    
 	Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);                                                 
                                                                          
 	// Measure the performance (request units) of queries.    
	 IDocumentQuery<dynamic> queryable =  client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();                                          
                                                                          
 	while (queryable.HasMoreResults)                                                                            
 	{                                                                         
 	   FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
	   Console.WriteLine("Query batch consumed {0} request units",queryResponse.RequestCharge);
	}                                                                        




<!--HONumber=49-->