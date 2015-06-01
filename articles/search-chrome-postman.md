<properties 
	pageTitle="Azure Search で Chrome Postman を使用する方法" 
	description="Azure Search で Chrome Postman を使用する方法" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""
    tags="azure-portal"/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="04/27/2015" 
	ms.author="heidist"/>

# Azure Search で Chrome Postman を使用する方法 #

[Postman](https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm "Chrome Postman") は、開発者が Azure Search などの REST ベースの API サービスで効率的に作業できるように、Google Chrome の一部として提供されるツールです。コードを記述しなくても、Postman を介して API 呼び出しを送信することで、Postman を使用して検索インデックスを簡単に作成およびクエリすることができます。この方法では、API について効率的に学習し、新しい機能を試すことができます。

![][1]
 
## 必要条件 ##

Azure Search サービスを備えている必要があります。Azure Search を使用するすべてのカスタム アプリケーションと同様に、サービスへの URL のほか、インデックスを作成するために管理者 `api-key` が必要です。Search サービスの価値を引き出す方法の詳細については、[ポータルでのサービスの作成](search-create-service-portal.md)に関するページを参照してください。

## Postman のインストール ##
Postman をダウンロードするには、[Google Chrome ストア](https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm)にアクセスします。このページのリンクを使用して、Postman の REST クライアントをダウンロードしてインストールすることができます。インストールすると、Chrome アプリ ランチャーから Postman を起動できるようになります。

![][2]
 
## Azure Search のクエリ用の Postman の構成 ##
Postman を構成するには、次の手順に従います。

1. "Enter request URL here (要求 URL をここに入力)" と表示されている場所に、Azure Search サービスの URL を入力します。  
2. URL に `?api-version=2015-02-28` を付加します。別の API バージョンを指定することもできます。詳細については、「[Azure Search サービスのバージョン](https://msdn.microsoft.com/library/azure/dn864560.aspx)」を参照してください。
3. `GET` が選択されていることを確認します。
4. [**ヘッダー**] ボタンをクリックします。
5. 次の値を入力します。
	- `api-key`: [管理者キー]
	- `Content-Type`: `application/json; charset=utf-8`
6. [**送信**] をクリックして、Azure Search に REST 呼び出しを発行し、JSON 応答を表示します。

![][3]

## Postman での Azure Search インデックスの作成 ##

次に、REST 呼び出しを発行して新しい Azure Search インデックスを作成することで、前の手順で実行した内容を発展させます。前の呼び出しとは異なり、インデックスの作成には HTTP PUT のほか、インデックス スキーマの定義を含む JSON ドキュメントが必要です。このサンプルでは、ハイキング トレイルの一覧を格納するインデックスを作成します。これを行うには、次の手順を実行します。

1. 検索サービス名を使用して、URL を `https://[SEARCH SERVICE].search.windows.net/indexes/trails?api-version=2015-02-28` に変更します。
2. 要求の種類を `GET` から `PUT` に変更します。
3. RAW 本文のコンテンツに、次の JSON を入力します。

	    {
	    "name": "trails", 
	    "fields": [
	    {"name": "id", "type": "Edm.String", "key": true, "searchable": false}, 
	    {"name": "name", "type": "Edm.String"}, 
	    {"name": "county", "type": "Edm.String"}, 
	    {"name": "elevation", "type": "Edm.Int32"}, 
	    {"name": "location", "type": "Edm.GeographyPoint"} ]
	    }

4. [**送信**] をクリックします。

![][4]
 
## Postman での Azure Search インデックスへのドキュメントのポスト ##
インデックスが作成されたので、ドキュメントを読み込むことができます。そのために、米国地質調査所 (USG) のデータセットにある 5 つのトレイルのデータを使用して、ドキュメントのグループをバッチでポストします。

1. 検索サービス名を使用して、URL を `https://[SEARCH SERVICE].windows.net/indexes/trails/docs/index?api-version=2015-02-28` に変更します。作成したインデックスへのパスが URL に含まれることに注意してください。
2. HTTP の種類を `POST` に変更します。
3. RAW 本文のコンテンツに、次の JSON を入力します。

	    {
	      "value": [
		    {"@search.action": "upload", "id": "233358", "name": "Pacific Crest National Scenic Trail", "county": "San Diego", "elevation":1294, "location": { "type": "Point", "coordinates": [-120.802102,49.00021] }},
		    {"@search.action": "upload", "id": "801970", "name": "Lewis and Clark National Historic Trail", "county": "Richland", "elevation":584, "location": { "type": "Point", "coordinates": [-104.8546903,48.1264084] }},
		    {"@search.action": "upload", "id": "1144102", "name": "Intake Trail", "county": "Umatilla", "elevation":1076, "location": { "type": "Point", "coordinates": [-118.0468873,45.9981939] }},
		    {"@search.action": "upload", "id": "1509897", "name": "Burke Gilman Trail", "county": "King", "elevation":10, "location": { "type": "Point", "coordinates": [-122.2754036,47.7059315] }},
		    {"@search.action": "upload", "id": "1517508", "name": "Cavanaugh-Oso Truck Trail", "county": "Skagit", "elevation":339, "location": { "type": "Point", "coordinates": [-121.9470829,48.2981608] }}
	      ]
	    }
    
4. [**送信**] をクリックします。

![][5]

## Postman でのインデックスのクエリ ##
最後の手順では、インデックスをクエリして、語 *trail* に対する簡単なフルテキストの検索要求を発行します。

1. 検索サービス名を使用して、URL に `https://[SEARCH SERVICE].search.windows.net/indexes/trails/docs?api-version=2015-02-28&search=trail` を入力します。URL には、`search` のクエリ パラメーターおよび検索語句 *trail* が含まれています。
2. HTTP 要求の種類を `GET` に変更します。
3. [**送信**] をクリックします。
 
応答には、Azure Search から返された JSON 検索結果が表示されます。

![][6]

## 次のステップ ##
これで、Postman での Azure Search の使用に関するすべての基本事項について説明しました。次の手順で役立ついくつかの事項を次に示します。

1. Postman では、共通して発行された要求を保存するのに便利な `Collections` がサポートされています。コレクションを他のユーザーと共有して、Postman の独自のコピーで発行することができます。
2. Azure Search のドキュメントで、Postman での該当する各呼び出しおよび変更に関連付けられている HTTP 要求の種類 (`GET`、`PUT` など) を必ず書き留めておいてください。

REST API のドキュメントは、[MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx) にあります。

また、[ビデオとチュートリアルの一覧](https://msdn.microsoft.com/library/azure/dn818681.aspx)にもアクセスできます。

<!-- Image References -->
[1]: ./media/search-chrome-postman/full_postman_client.png
[2]: ./media/search-chrome-postman/postman.png
[3]: ./media/search-chrome-postman/configure.png
[4]: ./media/search-chrome-postman/create_index.png
[5]: ./media/search-chrome-postman/upload_documents.png
[6]: ./media/search-chrome-postman/query.png

<!--HONumber=54-->