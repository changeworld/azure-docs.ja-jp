<properties 
	pageTitle="MongoDB のプロトコル対応の DocumentDB アカウントで MongoChef を使用する | Microsoft Azure" 
	description="現在プレビューとして提供されている MongoDB のプロトコル対応の DocumentDB アカウントで MongoChef を使用する方法について説明します。" 
	keywords="mongochef"
	services="documentdb" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/25/2016" 
	ms.author="stbaro"/>

# MongoDB のプロトコル対応の DocumentDB アカウントで MongoChef を使用する

MongoChef を使用して MongoDB のプロトコル対応の Azure DocumentDB アカウントに接続するには、次の要件を満たす必要があります。

- [MongoChef](http://3t.io/mongochef) をダウンロードしてインストールする
- MongoDB のプロトコル対応の DocumentDB アカウントに使用する[接続文字列](documentdb-connect-mongodb-account.md)情報を取得する

## MongoChef で接続を作成する  

MongoChef 接続マネージャーに MongoDB のプロトコル対応の DocumentDB アカウントを追加するには、次の手順を実行します。

1. [ここ](documentdb-connect-mongodb-account.md)に記載されている手順に従って、MongoDB のプロトコル対応の DocumentDB に使用する接続情報を取得します。

	![Screen shot of the connection string blade](./media/documentdb-mongodb-mongochef/ConnectionStringBlade.png)

2. **[Connect (接続)]** をクリックして接続マネージャーを開き、**[New Connection (新しい接続)]** をクリックします。

	![Screen shot of the MongoChef connection manager](./media/documentdb-mongodb-mongochef/ConnectionManager.png)
	
2. **[New Connection (新しい接続)]** ウィンドウの **[Server (サーバー)]** タブで、MongoDB のプロトコル対応の DocumentDB アカウントのホスト (FQDN) とポートを入力します。
	
	![Screen shot of the MongoChef connection manager server tab](./media/documentdb-mongodb-mongochef/ConnectionManagerServerTab.png)

3. **[New Connection (新しい接続)]** ウィンドウの **[Authentication (認証)]** タブで、認証モードとして **[Standard (MONGODB-CR or SCARM-SHA-1) (標準 (MONGODB-CR または SCARM-SHA-1))]** を選択し、ユーザー名とパスワードを入力します。既定の認証データベース (admin) をそのまま使用するか、または独自の値を指定します。

	![Screen shot of the MongoChef connection manager authentication tab](./media/documentdb-mongodb-mongochef/ConnectionManagerAuthenticationTab.png)

4. **[New Connection (新しい接続)]** ウィンドウの **[SSL]** タブで、**[Use SSL protocol to connect (SSL プロトコルを使用して接続する)]** チェック ボックスをオンにし、**[Accept self-signed SSL certificates (自己署名 SSL 証明書を受け付ける)]** オプション ボタンをオンにします。

	![Screen shot of the MongoChef connection manager SSL tab](./media/documentdb-mongodb-mongochef/ConnectionManagerSSLTab.png)

5. **[Test Connection (接続のテスト)]** をクリックして接続情報を検証し、**[OK]** をクリックして [New Connection (新しい接続)] ウィンドウに戻り、**[Save (保存)]** をクリックします。

	![Screen shot of the MongoChef test connection window](./media/documentdb-mongodb-mongochef/TestConnectionResults.png)

## MongoChef を使用してデータベース、コレクション、ドキュメントを作成する  

MongoChef を使用してデータベース、コレクション、ドキュメントを作成するには、次の手順を実行します。

1. **接続マネージャー**で目的の接続を強調表示し、**[Connect (接続)]** をクリックします。

	![Screen shot of the MongoChef connection manager](./media/documentdb-mongodb-mongochef/ConnectToAccount.png)

2. ホストを右クリックし、**[Add Database (データベースの追加)]** を選択します。データベースの名前を指定し、**[OK]** をクリックします。
	
	![Screen shot of the MongoChef Add Database option](./media/documentdb-mongodb-mongochef/AddDatabase1.png)

3. データベースを右クリックして **[Add Collection (コレクションの追加)]** を選択します。コレクションの名前を指定し、**[Create (作成)]** をクリックします。

	![Screen shot of the MongoChef Add Collection option](./media/documentdb-mongodb-mongochef/AddCollection.png)

4. **[Collection (コレクション)]** メニュー項目をクリックし、**[Add Document (ドキュメントの追加)]** をクリックします。

	![Screen shot of the MongoChef Add Document menu item](./media/documentdb-mongodb-mongochef/AddDocument1.png)

5. [Add Document (ドキュメントの追加)] ダイアログに次の情報を貼り付けて **[Add Document (ドキュメントの追加)]** をクリックします。

		{
    	"_id": "AndersenFamily",
    	"lastName": "Andersen",
    	"parents": [
       		{ "firstName": "Thomas" },
       		{ "firstName": "Mary Kay"}
    	],
    	"children": [
       	{
           	"firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
           	"pets": [{ "givenName": "Fluffy" }]
       	}
    	],
    	"address": { "state": "WA", "county": "King", "city": "seattle" },
    	"isRegistered": true
		}

	
6. もう 1 つドキュメントを追加します。今度は次の内容を使用します。

		{
	    "_id": "WakefieldFamily",
	    "parents": [
    	    { "familyName": "Wakefield", "givenName": "Robin" },
        	{ "familyName": "Miller", "givenName": "Ben" }
    	],
    	"children": [
	        {
            	"familyName": "Merriam", 
             	"givenName": "Jesse", 
            	"gender": "female", "grade": 1,
            	"pets": [
	                { "givenName": "Goofy" },
                	{ "givenName": "Shadow" }
            	]
        	},
        	{ 
	            "familyName": "Miller", 
             	"givenName": "Lisa", 
             	"gender": "female", 
             	"grade": 8 }
    	],
    	"address": { "state": "NY", "county": "Manhattan", "city": "NY" },
    	"isRegistered": false
		}

7. サンプル クエリを実行します。たとえば姓が "Andersen" である世帯を検索し、parents フィールドと state フィールドを取得します。

	![Screen shot of Mongo Chef query results](./media/documentdb-mongodb-mongochef/QueryDocument1.png)
	

## 次のステップ

- MongoDB のプロトコル対応 DocumentDB の[サンプル](documentdb-mongodb-samples.md)を体験します。
- MongoDB のプロトコル対応 DocumentDB に関する[プレビュー開発ガイドライン](documentdb-mongodb-guidelines.md)を読みます。

 

<!---HONumber=AcomDC_0831_2016-->