<properties 
	pageTitle="DocumentDB のプログラミング:ストアド プロシージャ、トリガー、UDF | Azure" 
	description="Microsoft Azure DocumentDB を使用して、ストアド プロシージャ、トリガー、ユーザー定義関数 (UDF) を JavaScript でネイティブに記述する方法について説明します。" 
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="mimig"/>

# DocumentDB のプログラミング:ストアド プロシージャ、トリガー、UDF

DocumentDB では、統合された JavaScript 言語によるトランザクション実行が可能なため、開発者は、**ストアド プロシージャ**、**トリガー**、および**ユーザー定義関数 (UDF)** を JavaScript でネイティブに記述できます。これにより、データベース ストレージ パーティションに直接配置して実行できるアプリケーション ロジックを記述できます。 

この記事を読むと、次の質問の答えを見つけることができます。

- どのようにしてストアド プロシージャ、トリガー、または UDF を JavaScript を使用して記述するか。
- DocumentDB では ACID がどのように保証されるか。
- DocumentDB ではトランザクションがどのように処理されるか。
- プリトリガーとポストトリガーとは何か。どのように記述するか。
- HTTP を使用して REST に対応する方法でストアド プロシージャ、トリガー、または UDF を登録し、実行するにはどのようにするか。
- ストアド プロシージャ、トリガー、および UDF を作成して実行するために使用できる DocumentDB はどれか。

##はじめに

この "今日の T-SQL としての JavaScript (JavaScript as a modern day T-SQL)" という手法により、アプリケーション開発者は、型システムのミスマッチとオブジェクト/リレーショナル マッピング テクノロジの複雑さから解放されます。この手法には、リッチなアプリケーションを作成する際に有用な本質的な長所もあります。  

-	**手続き型ロジック:** JavaScript は、高水準プログラミング言語として、ビジネス ロジックを表現するためのよく知られた優れたインターフェイスを提供します。

-	**アトミック トランザクション:** DocumentDB では、単一のストアド プロシージャまたはトリガー内で実行されるデータベース操作がアトミックであることが保証されます。これにより、アプリケーションは、関連する操作を 1 つのバッチに結合できます。その結果は、すべてが成功するか、または成功しないかのどちらかになります。

-	**パフォーマンス:** JSON は、Javascript 言語の型システムに本質的にマップされることに加え、DocumentDB のストレージの基本的な単位であるため、バッファー プール内の JSON ドキュメントの遅延実体化のようないくつかの最適化を行い、それらを必要に応じて実行コードで利用できるようになります。ビジネス ロジックをデータベースに配置することには、より大きなパフォーマンス上のメリットがあります。
	-	バッチ処理 - 開発者は、挿入などの操作をグループ化してそれらを一括送信できます。ネットワーク トラフィックの待機時間コストと、別個のトランザクションの作成に伴う格納オーバーヘッドが大幅に削減されます。 
	-	プリコンパイル - DocumentDB では、ストアド プロシージャ、トリガー、およびユーザー定義関数 (UDF) をプリコンパイルして、JavaScript の呼び出しごとのコンパイル コストを回避しています。手続き型のロジックのバイト コードのビルドに伴うオーバーヘッドは、最小値に平均化されます。
	-	シーケンス処理 - 多くの操作では、1 つまたは複数のセカンダリ格納操作の実行を潜在的に伴う副作用 ("トリガー") が必要です。アトミック性は別として、この操作では、サーバーに移行されたときにより高いパフォーマンスが実現されます。 
-	**カプセル化:** ストアド プロシージャを使用して、ビジネス ロジックを 1 か所にグループ化できます。
	-	生データの上に抽象化レイヤーが追加されるため、データ アーキテクトは、データとは独立してアプリケーションを進化させることができます。これは、データがスキーマを持たない場合に特に有益です。たとえば、アプリケーションがデータを直接処理する必要があり、アプリケーションに不確実な想定を組み込むことが必要になるような場合です。  
	-	この抽象化により、企業は、スクリプトからのアクセスを合理化してデータのセキュリティを保つことができます。  

トリガー、ストアド プロシージャ、およびカスタム クエリ演算子の作成と実行は、[REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) と多くのプラットフォーム (.NET、Node.js、JavaScript など) の[クライアント SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) でサポートされます。**このチュートリアルでは、[Node.js SDK](http://dl.windowsazure.com/documentDB/nodedocs/)** を使用して、ストアド プロシージャ、トリガー、UDF の構文と使用法を示します。   

##例:単純なストアド プロシージャを記述する 
最初に紹介するのは、"Hello World" 応答を返す単純なストアド プロシージャです。

	var helloWorldStoredProc = {
	    id: "helloWorld",
	    body: function () {
	        var context = getContext();
	        var response = context.getResponse();
	
	        response.setBody("Hello, World");
	    }
	}


ストアド プロシージャは、コレクションごとに登録され、そのコレクションに存在するあらゆるドキュメントと添付ファイルに作用します。次のスニペットは、helloWorld ストアド プロシージャをコレクションに登録する方法を示しています。 

	// register the stored procedure
	var createdStoredProcedure;
	client.createStoredProcedureAsync(collection._self, helloWorldStoredProc)
		.then(function (response) {
		    createdStoredProcedure = response.resource;
		    console.log("Successfully created stored procedure");
		}, function (error) {
		    console.log("Error", error);
		});


ストアド プロシージャを登録した後、コレクションに対して実行して、その結果をクライアントで読み取ることができます。 

	// execute the stored procedure
	client.executeStoredProcedureAsync(createdStoredProcedure._self)
		.then(function (response) {
		    console.log(response.result); // "Hello, World"
		}, function (err) {
		    console.log("Error", error);
		});


コンテキスト オブジェクトは、要求オブジェクトと応答オブジェクトへのアクセスに加えて、DocumentDB ストレージに対して実行できるすべての操作へのアクセスを提供します。ここでは、応答オブジェクトを使用して、クライアントに送り返される応答の本文を設定しています。詳細については、[DocumentDB JavaScript サーバー SDK のドキュメント](http://dl.windowsazure.com/documentDB/jsserverdocs/)を参照してください。  

この例をさらに拡張して、データベースに関連するいくつかの機能をこのストアド プロシージャに追加していきます。ストアド プロシージャを使用すると、コレクション内のドキュメントと添付ファイルの作成、更新、読み取り、照会、および削除を行うことができます。    

##例:ドキュメントを作成するストアド プロシージャを記述する 
コンテキスト オブジェクトを使用して DocumentDB リソースとやり取りする方法を次のスニペットに示します。

	var createDocumentStoredProc = {
	    id: "createMyDocument",
	    body: function createMyDocument(documentToCreate) {
	        var context = getContext();
	        var collection = context.getCollection();
	
	        var accepted = collection.createDocument(collection.getSelfLink(),
	              documentToCreate,
				function (err, documentCreated) {
				    if (err) throw new Error('Error' + err.message);
				    context.getResponse().setBody(documentCreated.id)
				});
	        if (!accepted) return;
	    }
	}


このストアド プロシージャは、入力として documentToCreate を受け取ります。これは、現在のコレクション内に作成するドキュメントの本文を示します。このような操作はすべて非同期に実行され、JavaScript 関数コールバックに依存します。コールバック関数には、操作が失敗した場合のエラー オブジェクト用と作成されたオブジェクト用の 2 つのパラメーターがあります。コールバック内では、例外を処理することも、エラーをスローすることもできます。コールバックが提供されていない場合にエラーが発生すると、DocumentDB ランタイムはエラーをスローします。   

上の例で操作が失敗した場合、コールバックはエラーをスローします。それ以外の場合、コールバックは、作成されたドキュメントの ID をクライアントへの応答の本文として設定します。入力パラメーターによってこのストアド プロシージャがどのように実行されるかを次に示します。

	// register the stored procedure
	client.createStoredProcedureAsync(collection._self, createDocumentStoredProc)
		.then(function (response) {
		    var createdStoredProcedure = response.resource;
	
		    // run stored procedure to create a document
		    var docToCreate = {
		        id: "DocFromSproc",
		        book: "The Hitchhiker's Guide to the Galaxy",
		        author: "Douglas Adams"
		    };
	
		    return client.executeStoredProcedureAsync(createdStoredProcedure._self,
	              docToCreate);
		}, function (error) {
		    console.log("Error", error);
		})
	.then(function (response) {
	    console.log(response); // "DocFromSproc"
	}, function (error) {
	    console.log("Error", error);
	});

	
このストアド プロシージャは、複数のドキュメントを複数のネットワーク要求を使って個別に作成する代わりに、ドキュメント本文の配列を入力として受け取り、すべて同じストアド プロシージャの実行で作成するように変更できます。この方法を使用して、DocumentDB の効率的な一括インポーターを実装できます (このチュートリアルの後半で説明します)。   

上の例ではストアド プロシージャの使用法について説明しました。トリガーとユーザー定義関数 (UDF) については、このチュートリアルの後半で説明します。最初に、DocumentDB でのスクリプトのサポートの一般的な特徴について説明します。  

##ランタイム サポート
[DocumentDB JavaScript サーバー側 SDK](http://dl.windowsazure.com/documentDB/jsserverdocs/) では、[ECMA-262 によって標準化されたメインストリーム JavaScript 言語機能のほとんどをサポートしています](../documentdb-interactions-with-resources.md)。
 
##トランザクション
一般的なデータベースにおけるトランザクションは、作業の単一の論理単位として実行される一連の操作として定義されます。各トランザクションは、**ACID の保証**を提供します。ACID とは、Atomicity (アトミック性)、Consistency (一貫性)、Isolation (分離性)、Durability (持続性) の 4 つの特性のよく知られた頭字語です。  

簡単に説明すると、Atomicity (アトミック性) は、トランザクション内で実行されるすべての操作が単一の単位として扱われることを保証します。その結果は、そのすべてがコミットされるか、またはまったくコミットされないかのどちらかになります。Consistency (一貫性) は、トランザクションにまたがってデータが常に適切な内部状態にあることを保証します。Isolation (分離性) は、2 つのトランザクションが互いに干渉しないことを保証します。通常、ほとんどの商用システムは、アプリケーション ニーズに基づいて使用できる複数の分離性レベルを提供します。Durability (持続性) は、データベース内でコミットされたすべての変更が常に保持されることを保証します。   

DocumentDB では、JavaScript はデータベースと同じメモリ空間でホストされます。したがって、ストアド プロシージャおよびトリガー内で発生した要求は、データベース セッションと同じスコープで実行されます。このため、DocumentDB では、単一のストアド プロシージャ/トリガーに属するすべての操作の ACID が保証されます。次のストアド プロシージャ定義を見てみましょう。

	// JavaScript source code
	var exchangeItemsSproc = {
	    name: "exchangeItems",
	    body: function (playerId1, playerId2) {
	        var context = getContext();
	        var collection = context.getCollection();
	        var response = context.getResponse();
	
	        var player1Document, player2Document;
	
	        // query for players
	        var filterQuery = 'SELECT * FROM Players p where p.id  = "' + playerId1 + '"';
	        var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery, {},
	            function (err, documents, responseOptions) {
	                if (err) throw new Error("Error" + err.message);
	
	                if (documents.length != 1) throw "Unable to find both names";
	                player1Document = documents[0];
	
	                var filterQuery2 = 'SELECT * FROM Players p where p.id = "' + playerId2 + '"';
	                var accept2 = collection.queryDocuments(collection.getSelfLink(), filterQuery2, {},
	                    function (err2, documents2, responseOptions2) {
	                        if (err2) throw new Error("Error" + err2.message);
	                        if (documents2.length != 1) throw "Unable to find both names";
	                        player2Document = documents2[0];
	                        swapItems(player1Document, player2Document);
	                        return;
	                    });
	                if (!accept2) throw "Unable to read player details, abort ";
	            });
	
	        if (!accept) throw "Unable to read player details, abort ";
	
	        // swap the two players' items
	        function swapItems(player1, player2) {
	            var player1ItemSave = player1.item;
	            player1.item = player2.item;
	            player2.item = player1ItemSave;
	
	            var accept = collection.replaceDocument(player1._self, player1,
	                function (err, docReplaced) {
					    if (err) throw "Unable to update player 1, abort ";
	
					    var accept2 = collection.replaceDocument(player2._self, player2,
	                        function (err2, docReplaced2) {
							    if (err) throw "Unable to update player 2, abort"
							});
	
					    if (!accept2) throw "Unable to update player 2, abort";
					});
	
	            if (!accept) throw "Unable to update player 1, abort";
	        }
	    }
	}
	
	// register the stored procedure in Node.js client
	client.createStoredProcedureAsync(collection._self, exchangeItemsSproc)
		.then(function (response) {
		    var createdStoredProcedure = response.resource;
		}
	);

このストアド プロシージャでは、ゲーム アプリ内のトランザクションを使用して、2 人のプレイヤーの間でアイテムを交換する操作を 1 つの処理で実現しています。このストアド プロシージャは、引数として渡されたプレイヤー ID にそれぞれ対応する 2 つのドキュメントの読み取りを試行します。2 つのプレイヤー ドキュメントが見つかると、アイテムを交換してドキュメントを更新します。処理の途中でエラーが発生した場合は、JavaScript 例外がスローされ、トランザクションが暗黙的に中止されます。
	

##コミットとロールバック
トランザクションは、DocumentDB の JavaScript プログラミング モデルに深くネイティブに統合されています。JavaScript 関数内では、1 つのトランザクションの下ですべての操作が自動的にラップされます。例外が発生することなく JavaScript が完了すると、データベースに対する操作がコミットされます。DocumentDB では、実質的に、リレーショナル データベースの BEGIN TRANSACTION ステートメントと COMMIT TRANSACTION ステートメントは暗黙的です。  
 
スクリプトから反映された例外がある場合、DocumentDB の JavaScript ランタイムにより、トランザクション全体がロール バックされます。前の例に示されているように、DocumentDB で例外をスローすることは、"ROLLBACK TRANSACTION" と事実上同じです。 

###データの一貫性
ストアド プロシージャとトリガーは、常に DocumentDB コレクションのプライマリ レプリカ上で実行されます。これにより、ストアド プロシージャ内からの読み取りで強固な一貫性が保証されます。ユーザー定義関数を使用したクエリはプライマリ レプリカまたは任意のセカンダリ レプリカ上で実行できますが、ここでは適切なレプリカを選択することで、要求された一貫性レベルが満たされるようにしています。

##セキュリティ
JavaScript のストアド プロシージャとトリガーはサンドボックス化されているため、データベース レベルのスナップショット トランザクション分離性が適用されなくても 1 つのスクリプトの効果が他のスクリプトに作用しません。ランタイム環境はプーリングされますが、実行ごとにコンテキストがクリーンアップされます。このため、互いの意図していない副作用に対する安全性が保証されています。

##プリコンパイル
ストアド プロシージャ、トリガー、および UDF は、それぞれのスクリプトの呼び出し時のコンパイル コストを回避するために、暗黙的にバイト コード形式にプリコンパイルされます。これにより、高速なストアド プロシージャの呼び出しと小さなフットプリントが保証されます。

##<a id="trigger"></a>トリガー
###プリトリガー
DocumentDB には、ドキュメントの操作によって実行またはトリガーされるトリガーが用意されています。たとえば、ドキュメントを作成するときにプリトリガーを指定できます。このプリトリガーは、ドキュメントが作成される前に実行されます。次の例に、プリトリガーを使用して、作成するドキュメントのプロパティを検証する方法を示します。

	var validateDocumentContentsTrigger = {
	    name: "validateDocumentContents",
	    body: function validate() {
	        var context = getContext();
	        var request = context.getRequest();
	
	        // document to be created in the current operation
	        var documentToCreate = request.getBody();
	
	        // validate properties
	        if (!("timestamp" in documentToCreate)) {
	            var ts = new Date();
	            documentToCreate["my timestamp"] = ts.getTime();
	        }
	
	        // update the document that will be created
	        request.setBody(documentToCreate);
	    },
	    triggerType: TriggerType.Pre,
	    triggerOperation: TriggerOperation.Create
	}


このトリガーの対応する Node.js クライアント側登録コードは次のとおりです。

	// register pre-trigger
	client.createTriggerAsync(collection.self, validateDocumentContentsTrigger)
		.then(function (response) {
		    console.log("Created", response.resource);
		    var docToCreate = {
		        id: "DocWithTrigger",
		        event: "Error",
		        source: "Network outage"
		    };
	
		    // run trigger while creating above document 
		    var options = { preTriggerInclude: "validateDocumentContents" };
	
		    return client.createDocumentAsync(collection.self,
	              docToCreate, options);
		}, function (error) {
		    console.log("Error", error);
		})
	.then(function (response) {
	    console.log(response.resource); // document with timestamp property added
	}, function (error) {
	    console.log("Error", error);
	});


プリトリガーは入力パラメーターを持つことができません。要求オブジェクトを使用して、操作に関連付けられた要求メッセージを操作できます。ここでは、ドキュメントが作成されるときにプリトリガーが実行されます。要求メッセージの本文には、作成するドキュメントが JSON 形式で格納されます。   

トリガーが登録されたら、ユーザーは実行できる操作を指定できます。このトリガーは TriggerOperation.Create によって作成されています。つまり、次の操作は許可されません。

	var options = { preTriggerInclude: "validateDocumentContents" };
	
	client.replaceDocumentAsync(docToReplace.self,
	              newDocBody, options)
	.then(function (response) {
	    console.log(response.resource);
	}, function (error) {
	    console.log("Error", error);
	});
	
	// Fails, can't use a create trigger in a replace operation

###ポストトリガー
ポストトリガーは、プリトリガーと同様に、ドキュメントの操作に関連付けられ、入力パラメーターを受け取りません。ポストトリガーは、操作が完了した**後に**実行され、クライアントに送信される応答メッセージにアクセスします。   

次の例にポストトリガーの使い方を示します。

	var updateMetadataTrigger = {
	    name: "updateMetadata",
	    body: function updateMetadata() {
	        var context = getContext();
	        var collection = context.getCollection();
	        var response = context.getResponse();
	
	        // document that was created
	        var createdDocument = response.getBody();
	
	        // query for metadata document
	        var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
	        var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery,
	            updateMetadataCallback);
	        if(!accept) throw "Unable to update metadata, abort";
	 
	        function updateMetadataCallback(err, documents, responseOptions) {
	            if(err) throw new Error("Error" + err.message);
	                     if(documents.length != 1) throw 'Unable to find metadata document';
	                     
	                     var metadataDocument = documents[0];
	                     
	                     // update metadata
	                     metadataDocument.createdDocuments += 1;
	                     metadataDocument.createdNames += " " + createdDocument.id;
	                     var accept = collection.replaceDocument(metadataDocument._self,
	                           metadataDocument, function(err, docReplaced) {
	                                  if(err) throw "Unable to update metadata, abort";
	                           });
	                     if(!accept) throw "Unable to update metadata, abort";
	                     return;                    
	        }																							
	    },
	    triggerType: TriggerType.Post,
	    triggerOperation: TriggerOperation.All
	}


トリガーの登録方法を次に示します。

	// register post-trigger
	client.createTriggerAsync(collection.self, updateMetadataTrigger)
		.then(function(createdTrigger) { 
		    var docToCreate = { 
		        name: "artist_profile_1023",
		        artist: "The Band",
		        albums: ["Hellujah", "Rotators", "Spinning Top"]
		    };
	
		    // run trigger while creating above document 
		    var options = { postTriggerInclude: "updateMetadata" };
		
		    return client.createDocumentAsync(collection.self,
	              docToCreate, options);
		}, function(error) {
		    console.log("Error" , error);
		})
	.then(function(response) {
	    console.log(response.resource); 
	}, function(error) {
	    console.log("Error" , error);
	});


このトリガーは、メタデータ ドキュメントを照会し、新しく作成されたドキュメントに関する詳細情報に基づいてこれを更新します。  

ここで重要なのは、DocumentDB でのトリガーの**トランザクション**実行です。このポストトリガーは、元のドキュメントの作成に使用されたのと同じトランザクションの一部として実行されます。したがって、(たとえば、メタデータ ドキュメントを更新できないという理由で) ポストトリガーから例外をスローすると、トランザクション全体が失敗し、ロール バックされます。その結果、ドキュメントは作成されず、例外が返されます。  

##<a id="udf"></a>ユーザー定義関数
ユーザー定義関数 (UDF) は、DocumentDB SQL クエリ言語の文法を拡張してカスタム ビジネス ロジックを実装するために使用します。ユーザー定義関数は、クエリ内からのみ呼び出すことができます。ユーザー定義関数は、コンテキスト オブジェクトにアクセスできず、計算のみの JavaScript として使用する必要があります。したがって、UDF は、DocumentDB サービスのセカンダリ レプリカで実行できます。  
 
次のサンプルでは、さまざまな所得階層の税率に基づいて所得税を計算する UDF を作成し、クエリ内でこの UDF を使用して、支払った税金が $20,000 を超える人々を検索しています。

	var taxUdf = {
	    name: "tax",
	    body: function tax(income) {
	
	        if(income == undefined) 
	            throw 'no input';
	
	        if (income < 1000) 
	            return income * 0.1;
	        else if (income < 10000) 
	            return income * 0.2;
	        else
	            return income * 0.4;
	    }
	}


この UDF は、次のサンプルに示すように、後でクエリ内で使用できます。

	// register UDF
	client.createUserDefinedFunctionAsync(collection.self, taxUdf)
		.then(function(response) { 
		    console.log("Created", response.resource);
	
		    var query = 'SELECT * FROM TaxPayers t WHERE tax(t.income) > 20000'; 
		    return client.queryDocuments(collection.self,
	               query).toArrayAsync();
		}, function(error) {
		    console.log("Error" , error);
		})
	.then(function(response) {
	    var documents = response.feed;
	    console.log(response.resource); 
	}, function(error) {
	    console.log("Error" , error);
	});

##制限された実行
すべての DocumentDB 操作は、サーバーによって指定された要求タイムアウト期間内に完了する必要があります。この制約は、JavaScript 関数 (ストアド プロシージャ、トリガー、およびユーザー定義関数) にも適用されます。この制限時間内に操作が完了しなかった場合、トランザクションはロール バックされます。JavaScript 関数は、制限時間内に完了するか、実行をバッチ処理または再開するための継続ベースのモデルを実装する必要があります。  

時間制限を処理するストアド プロシージャとトリガーの開発を容易にするために、コレクション オブジェクトの (ドキュメントおよび添付ファイルの作成、読み取り、置換、削除を行う) すべての関数は、操作が完了するかどうかを表すブール値を返します。値 false は、制限時間に近づいているためプロシージャが実行を終了する必要があることを示します。最初の受け付けられていない格納操作の前にキューに入れられた操作は、ストアド プロシージャが時間内に完了し追加の要求がキューに入れられない限り、完了することが保証されます。  

さらに、JavaScript 関数は、リソースの消費に関しても制限されます。DocumentDB は、データベース アカウントのプロビジョニングされたサイズに基づいて、コレクションあたりのスループットを予約します。スループットは、要求単位 (RU) と呼ばれる、CPU、メモリ、および IO の消費の正規化された単位として表現されます。JavaScript 関数は潜在的に短い時間内に大量の RU を消費する可能性があり、コレクションの制限に達した場合はレートが制限されます。また、プリミティブなデータベース操作の可用性を保証するために、リソースを大量に使用するストアド プロシージャは隔離される可能性があります。  

##データの一括インポート
コレクションへのドキュメントの一括インポートを行うストアド プロシージャの例を次に示します。このストアド プロシージャでは、createDocument からのブール型の戻り値を調べて制限された実行を処理し、ストアド プロシージャの各呼び出しで挿入されたドキュメントの数を使用してバッチの進行状況を追跡および再開しています。

	function bulkImport(docs) {
	    var collection = getContext().getCollection();
	    var collectionLink = collection.getSelfLink();
	
	    // The count of imported docs, also used as current doc index.
	    var count = 0;
	
	    // Validate input.
	    if (!docs) throw new Error("The array is undefined or null.");
	
	    var docsLength = docs.length;
	    if (docsLength == 0) {
	        getContext().getResponse().setBody(0);
	    }
	
	    // Call the create API to create a document.
	    tryCreate(docs[count], callback);
	
	    // Note that there are 2 exit conditions:
	    // 1) The createDocument request was not accepted. 
	    //    In this case the callback will not be called, we just call setBody and we are done.
	    // 2) The callback was called docs.length times.
	    //    In this case all documents were created and we don't need to call tryCreate anymore. Just call setBody and we are done.
	    function tryCreate(doc, callback) {
	        var isAccepted = collection.createDocument(collectionLink, doc, callback);
	
	        // If the request was accepted, callback will be called.
	        // Otherwise report current count back to the client, 
	        // which will call the script again with remaining set of docs.
	        if (!isAccepted) getContext().getResponse().setBody(count);
	    }
	
	    // This is called when collection.createDocument is done in order to process the result.
	    function callback(err, doc, options) {
	        if (err) throw err;
	
	        // One more document has been inserted, increment the count.
	        count++;
	
	        if (count >= docsLength) {
	            // If we created all documents, we are done. Just set the response.
	            getContext().getResponse().setBody(count);
	        } else {
	            // Create next document.
	            tryCreate(docs[count], callback);
	        }
	    }
	}


##REST に対応する方法での実行
すべての DocumentDB 操作は、REST に対応する方法で実行できます。ストアド プロシージャ、トリガー、およびユーザー定義関数は、HTTP POST を使用してコレクションに登録できます。ストアド プロシージャを登録する方法を次の例に示します。

	POST https://<url>/sprocs/ HTTP/1.1
	authorization: <<auth>>
	x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
	
	
	var x = {
	  "name": "createAndAddProperty",
	  "body": function (docToCreate, addedPropertyName, addedPropertyValue) {
	            var collectionManager = getContext().getCollection();
	            collectionManager.createDocument(
	                collectionManager.getSelfLink(),
	                docToCreate,
	                function(err, docCreated) {
	                  if(err) throw new Error('Error:  ' + err.message);
	                  docCreated[addedPropertyName] = addedPropertyValue;
	                  getContext().getResponse().setBody(docCreated);
	                });
	        }
	}


ストアド プロシージャは、作成するストアド プロシージャを本文に含む POST 要求を URI dbs/sehcAA==/colls/sehcAIE2Qy4=/sprocs に対して実行することで登録されます。トリガーと UDF は、それぞれ POST を /triggers と /udfs に発行することで登録できます。
このストアド プロシージャは、リソース リンクに対して POST 要求を発行することで実行できます。

	POST https://<url>/sprocs/<sproc> HTTP/1.1
	authorization: <<auth>>
	x-ms-date: Thu, 07 Aug 2014 03:43:20 GMT
	
	
	[ { "name": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]


ここで、ストアド プロシージャへの入力は、要求の本文に渡されます。入力は入力パラメーターの JSON 配列として渡されることに注意してください。ストアド プロシージャは、最初の入力をドキュメント (応答の本文) として受け取ります。受け取る応答は次のようになります。

	HTTP/1.1 200 OK
	 
	{ 
	  name: 'TestDocument',
	  book: 'Autumn of the Patriarch',
	  id: 'V7tQANV3rAkDAAAAAAAAAA==',
	  ts: 1407830727,
	  self: 'dbs/V7tQAA==/colls/V7tQANV3rAk=/docs/V7tQANV3rAkDAAAAAAAAAA==/',
	  etag: '6c006596-0000-0000-0000-53e9cac70000',
	  attachments: 'attachments/',
	  Price: 200
	}


トリガーは、ストアド プロシージャと異なり、直接実行することはできません。トリガーは、ドキュメントに対する操作の一部として実行されます。実行するトリガーを指定するには、HTTP ヘッダーを使用する要求を使用します。次のコードに、ドキュメントを作成するための要求を示します。

	POST https://<url>/docs/ HTTP/1.1
	authorization: <<auth>>
	x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
	x-ms-documentdb-pre-trigger-include: validateDocumentContents 
	x-ms-documentdb-post-trigger-include: bookCreationPostTrigger
	
	
	{
	   "name": "newDocument",
	   "title": "The Wizard of Oz",
	   "author": "Frank Baum",
	   "pages": 92
	}


ここでは、要求と共に実行されるプリトリガーが x-ms-documentdb-pre-trigger-include ヘッダーに指定されています。同様に、x-ms-documentdb-post-trigger-include ヘッダーにはポストトリガーが指定されています。プリトリガーとポストトリガーはどちらも任意の要求に指定できます。

##SDK のサポート
[Node.js](http://dl.windowsazure.com/documentDB/nodedocs/) クライアントに加え、DocumentDB では、[.NET](https://msdn.microsoft.com/library/azure/dn783362.aspx)、[Java](http://dl.windowsazure.com/documentdb/javadoc/)、[JavaScript](http://dl.windowsazure.com/documentDB/jsclientdocs/)、および [Python の SDK](http://dl.windowsazure.com/documentDB/pythondocs/) をサポートしています。ストアド プロシージャ、トリガー、および UDF は、これらの SDK を使用して作成および実行することもできます。次の例に、.NET クライアントを使用してストアド プロシージャを作成および実行する方法を示します。.NET の型がどのように JSON としてストアド プロシージャに渡され、読み取られるかに注目してください。

	var markAntiquesSproc = new StoredProcedure
	{
	    Id = "ValidateDocumentAge",
	    Body = @"
	            function(docToCreate, antiqueYear) {
	                var collection = getContext().getCollection();    
	                var response = getContext().getResponse();    
	
			        if(docToCreate.Year != undefined && docToCreate.Year < antiqueYear){
				        docToCreate.antique = true;
			        }
	
	                collection.createDocument(collection.getSelfLink(), docToCreate, {}, 
	                    function(err, docCreated, options) { 
	                        if(err) throw new Error('Error while creating document: ' + err.message);                              
	                        if(options.maxCollectionSizeInMb == 0) throw 'max collection size not found'; 
	                        response.setBody(docCreated);
	                });
	 	    }"
	};
	
	// register stored procedure
	StoredProcedure createdStoredProcedure = await client.CreateStoredProcedureAsync(collection.SelfLink, markAntiquesSproc);
	dynamic document = new Document() { Id = "Borges_112" };
	document.Title = "Aleph";
	document.Year = 1949;
	
	// execute stored procedure
	Document createdDocument = await client.ExecuteStoredProcedureAsync<Document>(createdStoredProcedure.SelfLink, document, 1920);


このサンプルは、[.NET SDK](https://msdn.microsoft.com/library/azure/dn783362.aspx) を使用してプリトリガーを作成し、このトリガーが有効なドキュメントを作成する方法を示しています。 

	Trigger preTrigger = new Trigger()
	{
	    Id = "CapitalizeName",
	    Body = @"function() {
	        var item = getContext().getRequest().getBody();
	        item.id = item.id.toUpperCase();
	        getContext().getRequest().setBody(item);
	    }",
	    TriggerOperation = TriggerOperation.Create,
	    TriggerType = TriggerType.Pre
	};
	
	Document createdItem = await client.CreateDocumentAsync(collection.SelfLink, new Document { Id = "documentdb" },
	    new RequestOptions
	    {
	        PreTriggerInclude = new List<string> { "CapitalizeName" },
	    });


次の例では、ユーザー定義関数 (UDF) を作成し、これを [DocumentDB SQL クエリで使用しています](../documentdb-sql-query.md)。

	UserDefinedFunction function = new UserDefinedFunction()
	{
	    Id = "LOWER",
	    Body = @"function(input) 
		{
	        return input.toLowerCase();
	    }"
	};
	
	foreach (Book book in client.CreateDocumentQuery(collection.SelfLink,
	    "SELECT * FROM Books b WHERE LOWER(b.Title) = 'war and peace'"))
	{
	    Console.WriteLine("Read {0} from query", book);
	}
##次のステップ

[Azure DocumentDB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) を利用して、さらにストアド プロシージャ、トリガー、および UDF を作成してみてください。


##参照

- JSON [http://www.json.org/](http://www.json.org/) 
- JavaScript ECMA-262 [http://www.ecma-international.org/publications/standards/Ecma-262.htm ](http://www.ecma-international.org/publications/standards/Ecma-262.htm )
-	JavaScript - JSON の型システム [http://www.json.org/js.html](http://www.json.org/js.html) 
-	安全でポータブルなデータベース拡張性 - [http://dl.acm.org/citation.cfm?id=276339](http://dl.acm.org/citation.cfm?id=276339) 
-	サービス指向のデータベース アーキテクチャ - [http://dl.acm.org/citation.cfm?id=1066267&coll=Portal&dl=GUIDE](http://dl.acm.org/citation.cfm?id=1066267&coll=Portal&dl=GUIDE) 
-	Microsoft SQL Server での .NET ランタイムのホスト - [http://dl.acm.org/citation.cfm?id=1007669](http://dl.acm.org/citation.cfm?id=1007669) 

<!--HONumber=47-->
