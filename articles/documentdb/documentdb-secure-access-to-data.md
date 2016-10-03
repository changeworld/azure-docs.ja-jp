<properties 
	pageTitle="DocumentDB のデータへのアクセスをセキュリティで保護する方法 | Microsoft Azure" 
	description="マスター キー、読み取り専用キー、ユーザー、アクセス許可など、DocumentDB のアクセス制御の概念について説明します。" 
	services="documentdb" 
	authors="kiratp" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/19/2016" 
	ms.author="kipandya"/>

# DocumentDB のデータへのアクセスのセキュリティ保護

この記事では、[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) に格納されたデータへのアクセスをセキュリティ保護する方法の概要を説明します。

この概要を読み終えると、次の質問に答えられるようになります。

-	DocumentDB マスター キーとは何か。
-	DocumentDB 読み取り専用キーとは何か。
-	DocumentDB リソース トークンとは何か。
-	DocumentDB ユーザーとアクセス許可を使用して DocumentDB データへのアクセスをセキュリティ保護するにはどうすればよいか。

## DocumentDB のアクセス制御の概念

DocumentDB には、DocumentDB のリソースへのアクセスを制御するための優れた概念が用意されています。このトピックでは、DocumentDB のリソースを次の 2 つのカテゴリに分類しています。

- 管理リソース
	- Account
	- データベース
	- User
	- アクセス許可
- アプリケーション リソース
	- コレクション
	- プラン
	- ドキュメント
	- 添付ファイル
	- ストアド プロシージャ
	- トリガー
	- ユーザー定義関数

DocumentDB では、この 2 つのカテゴリにおいて、アカウント管理者、読み取り専用の管理者、データベース ユーザーの 3 種類のアクセス制御のペルソナをサポートしています。各アクセス制御のペルソナの権限は次のとおりです。
 
- アカウント管理者: 指定した DocumentDB アカウント内のすべてのリソース (管理リソースおよびアプリケーション リソース) にフル アクセスします。
- 読み取り専用の管理者: 指定した DocumentDB アカウント内のすべてのリソース (管理リソースおよびアプリケーション リソース) に読み取り専用アクセスします。
- データベース ユーザー: DocumentDB データベースの一連のリソース (コレクション、ドキュメント、スクリプトなど) に関連付けられた DocumentDB ユーザー リソースです。特定のデータベースに関連付けられたユーザー リソースが 1 つ以上ある場合があり、各ユーザー リソースはそのデータベースに関連付けられたアクセス許可を 1 つ以上持つことができます。

前のカテゴリとリソースを念頭において、DocumentDB アクセス制御モデルではアクセス許可の構成要素を 3 種類定義します。

- マスター キー: DocumentDB アカウントの作成時に、2 つのマスター キー (プライマリおよびセカンダリ) が作成されます。これらのキーにより、DocumentDB アカウント内のすべてのリソースへの完全な管理アクセスが可能になります。

![図: DocumentDB マスター キー](./media/documentdb-secure-access-to-data/masterkeys.png)

- 読み取り専用キー: DocumentDB アカウントの作成時に、2 つの読み取り専用キー (プライマリおよびセカンダリ) が作成されます。これらのキーにより、DocumentDB アカウント内のすべてのリソースへの読み取り専用アクセスが可能になります。

![図: DocumentDB 読み取り専用キー](./media/documentdb-secure-access-to-data/readonlykeys.png)

- リソース トークン: リソース トークンは、DocumentDB アクセス許可のリソースに関連付けられていて、データベースのユーザーと、ユーザーが持つ特定の DocumentDB アプリケーションのリソース (コレクション、ドキュメントなど) へのアクセス許可の間のリレーションシップをキャプチャします。

![図: DocumentDB リソース トークン](./media/documentdb-secure-access-to-data/resourcekeys.png)

## DocumentDB のマスター キーおよび読み取り専用キーの使用

前に説明したように、DocumentDB マスター キーは、DocumentDB アカウント内のすべてのリソースへの完全な管理アクセス権を提供し、読み取り専用キーは、アカウント内のすべてのリソースへの読み取りアクセスを可能にします。次のコード スニペットは、DocumentDB アカウントのエンドポイントとマスター キーを使用して、DocumentClient をインスタンス化し、新しいデータベースを作成する方法を示します。

    //Read the DocumentDB endpointUrl and authorization keys from config.
    //These values are available from the Azure Classic Portal on the DocumentDB Account Blade under "Keys".
    //NB > Keep these values in a safe and secure location. Together they provide Administrative access to your DocDB account.
    
    private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
    private static readonly SecureString authorizationKey = ToSecureString(ConfigurationManager.AppSettings["AuthorizationKey"]);
        
    client = new DocumentClient(new Uri(endpointUrl), authorizationKey);
    
    // Create Database
    Database database = await client.CreateDatabaseAsync(
        new Database
        {
            Id = databaseName
        });


## DocumentDB リソース トークンの概要

リソース トークンは、自分のマスター キーを知らせたくないクライアントに、自分の DocumentDB アカウント内のリソースへのアクセスを許可する場合に (DocumentDB ユーザーとアクセス許可を作成することによって) 使用できます。DocumentDB マスター キーには、プライマリ キーとセカンダリ キーの両方が含まれており、これらによって、アカウントとそのすべてのリソースへの管理アクセスが許可されます。これらのマスター キーのいずれかを知らせると、悪意で、または誤ってアカウントが使用される可能性が生じます。

同様に、DocumentDB 読み取り専用キーは、アクセス許可のリソースを除く、DocumentDB アカウント内のすべてのリソースへの読み取りアクセス権を提供しますが、特定の DocumentDB のリソースへのより細やかなアクセス権を提供する目的で使用することはできません。

DocumentDB リソース トークンにより、付与されたアクセス許可に従って、マスター キーや読み取り専用キーなしでクライアントによる DocumentDB アカウント内のリソースの読み取り、書き込み、および削除を許可する安全な代替手段が提供されます。

リソース トークンの要求、生成、およびクライアントへの配信に使用されることがある一般的な設計パターンを次に示します。

1. 中間層サービスは、ユーザーの写真を共有するモバイル アプリケーションを提供するために設定します。
2. 中間層サービスは、DocumentDB アカウントのマスター キーを持ちます。
3. 写真アプリは、エンド ユーザーのモバイル デバイスにインストールされます。
4. ログイン時に、写真アプリは、中間層サービスを使用してユーザー ID を確立します。この ID 確立のしくみは完全にアプリケーションに依存します。
5. いったん ID が確立されると、中間層サービスはその ID に基づいてアクセス許可を要求します。
6. 中間層サービスから、リソース トークンが電話アプリに返されます。
7. 電話アプリはリソース トークンを引き続き使用し、リソース トークンによって定義されたアクセス許可を使用して、リソース トークンによって許可された間隔で DocumentDB リソースに直接アクセスすることができます。
8. リソース トークンの期限が切れると、それ以降の要求で、401 の承認されていない例外が表示されます。この時点で、電話アプリは ID を再度確立し、新しいリソース トークンを要求します。

![DocumentDB リソース トークンのワークフロー](./media/documentdb-secure-access-to-data/resourcekeyworkflow.png)

## DocumentDB ユーザーおよびアクセス許可の操作
DocumentDB ユーザー リソースは DocumentDB データベースに関連付けられます。各データベースには、0 人以上の DocumentDB ユーザーを含めることができます。次のコード スニペットは、DocumentDB ユーザー リソースを作成する方法を示します。

    //Create a user.
    User docUser = new User
    {
        Id = "mobileuser"
    };

    docUser = await client.CreateUserAsync(UriFactory.CreateDatabaseUri("db"), docUser);

> [AZURE.NOTE] DocumentDB の各ユーザーは PermissionsLink プロパティを持ち、このプロパティを使用して、そのユーザーに関連付けられた権限の一覧を取得することができます。

DocumentDB アクセス許可リソースは DocumentDB ユーザーに関連付けられています。各ユーザーは、0 個以上の DocumentDB アクセス許可を持つ可能性があります。アクセス許可リソースは、特定のアプリケーション リソースにアクセスするときに必要なセキュリティ トークンへのアクセスを提供します。アクセス許可リソースによって提供されることがある次の 2 つのアクセス レベルが使用できます。

- All: ユーザーはそのリソースに完全なアクセス許可を持つ
- Read: ユーザーは、リソースの内容を読み取りのみができますが、リソースへの書き込み、更新、または削除の操作を実行することはできません。


> [AZURE.NOTE] DocumentDB ストアド プロシージャを実行するには、ストアド プロシージャを実行するコレクションの All 権限を持つ必要があります。


次のコード スニペットは、アクセス許可リソースを作成し、アクセス許可リソースのリソース トークンを読み取って、先ほど作成したユーザーにアクセス許可を関連付ける方法を示します。

    // Create a permission.
    Permission docPermission = new Permission
    {
        PermissionMode = PermissionMode.Read,
        ResourceLink = documentCollection.SelfLink,
        Id = "readperm"
    };
            
  docPermission = await client.CreatePermissionAsync(UriFactory.CreateUserUri("db", "user"), docPermission); Console.WriteLine(docPermission.Id + " has token of: " + docPermission.Token);
  
コレクションに対してパーティション キーを指定した場合は、コレクション、ドキュメント、添付ファイル リソースのアクセス許可にも、ResourceLink に加えて ResourcePartitionKey が含まれる必要があります。

すべてのアクセス許可リソースを特定のユーザーに簡単に関連付けるために、DocumentDB により、各ユーザー オブジェクトのアクセス許可フィードが利用できるようになります。次のコード スニペットは、上記で作成したユーザーに関連付けられている権限を取得し、権限の一覧を作成して、ユーザーの代わりに新しい DocumentClient をインスタンス化する方法を示します。

    //Read a permission feed.
    FeedResponse<Permission> permFeed = await client.ReadPermissionFeedAsync(
      UriFactory.CreateUserUri("db", "myUser"));

    List<Permission> permList = new List<Permission>();
      
    foreach (Permission perm in permFeed)
    {
        permList.Add(perm);
    }
            
    DocumentClient userClient = new DocumentClient(new Uri(endpointUrl), permList);

> [AZURE.TIP] リソース トークンの既定の有効期間は 1 時間です。ただし、トークンの有効期間が明示的に指定される可能性があります (最大 5 時間)。

## 次のステップ

- DocumentDB の詳細については、[ここ](http://azure.com/docdb)をクリックしてください。
- マスター キーと読み取り専用のキーの管理の詳細については、[ここ](documentdb-manage-account.md)をクリックしてください。
- DocumentDB 認証トークンを作成する方法については、[ここ](https://msdn.microsoft.com/library/azure/dn783368.aspx)をクリックしてください。
 

<!---HONumber=AcomDC_0921_2016-->