---
title: クイック スタート:Python と REST APIs - Azure Search
description: Python、Jupyter Notebooks、および Azure Search REST API を使用してインデックスの作成、読み込み、クエリを実行する。
ms.date: 05/15/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: a79a5fe1632eeabee670274ebbb19c4c34bd84d2
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/23/2019
ms.locfileid: "66117335"
---
# <a name="quickstart-create-an-azure-search-index-using-jupyter-python-notebooks"></a>クイック スタート:Jupyter Python Notebooks を使用して Azure Search インデックスを作成する
> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-fiddler.md)
> * [ポータル](search-create-index-portal.md)
> 

Python と [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/) を使用して Azure Search インデックスの作成、読み込み、およびクエリを実行する Jupyter ノートブックを作成します。 この記事では、ノートブックを一から作成する方法を順を追って説明します。 あるいは、完成したノートブックを実行することもできます。 コピーをダウンロードするには、[Azure Search Python サンプル リポジトリ](https://github.com/Azure-Samples/azure-search-python-samples)に関するページを参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは、次のサービスとツールを使用します。 

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section)。これによって、Python 3.x と Jupyter Notebooks が提供されます。

+ [Azure Search サービスを作成](search-create-service-portal.md)するか、現在のサブスクリプションから[既存のサービスを見つけます](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 このクイック スタート用には、無料のサービスを使用できます。 

## <a name="get-a-key-and-url"></a>キーと URL を入手する

REST 呼び出しには、要求ごとにサービス URL とアクセス キーが必要です。 両方を使用して検索サービスが作成されるので、Azure Search をサブスクリプションに追加した場合は、次の手順に従って必要な情報を入手してください。

1. [Azure portal にサインイン](https://portal.azure.com/)し、ご使用の検索サービスの **[概要]** ページで、URL を入手します。 たとえば、エンドポイントは `https://mydemo.search.windows.net` のようになります。

1. **[設定]**  >  **[キー]** で、サービスに対する完全な権限の管理者キーを取得します。 管理キーをロールオーバーする必要がある場合に備えて、2 つの交換可能な管理キーがビジネス継続性のために提供されています。 オブジェクトの追加、変更、および削除の要求には、主キーまたはセカンダリ キーのどちらかを使用できます。

![HTTP エンドポイントとアクセス キーを取得する](media/search-fiddler/get-url-key.png "HTTP エンドポイントとアクセス キーを取得する")

すべての要求では、サービスに送信されるすべての要求に API キーが必要です。 有効なキーがあれば、要求を送信するアプリケーションとそれを処理するサービスの間で、要求ごとに信頼を確立できます。

## <a name="connect-to-azure-search"></a>Azure Search への接続

Jupyter ノートブックを開き、ご利用のローカル ワークステーションからの接続を確認します。それには、ご利用のサービスに対してインデックスのリストを要求します。 Windows と Anaconda 3 の組み合わせでは、Anaconda Navigator を使用してノートブックを起動できます。

1. 新しい Python3 ノートブックを作成します。

1. 最初のセルでは、JSON を操作して HTTP 要求を作成するのに使用するライブラリを読み込みます。

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. 2 番目のセルでは、どの要求でも定数となる要求要素を入力します。 検索サービス名 (YOUR-SEARCH-SERVICE-NAME) と管理者 API キー (YOUR-ADMIN-API-KEY) を有効な値に置き換えます。 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2019-05-06'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

1. 3 番目のセルで、要求を作成します。 この GET 要求では、ご利用の検索サービスのインデックス コレクションがターゲットとされ、name プロパティが選択されます。

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. 各手順を行います。 インデックスが存在する場合、応答にインデックスのリストが含まれます。 次のスクリーン ショットでは、サービスに、azureblob-index と realestate-us-sample インデックスが含まれます。

   ![Azure Search への HTTP 要求が含まれる Jupyter ノートブック内の Python スクリプト](media/search-get-started-python/connect-azure-search.png "Azure Search への HTTP 要求が含まれる Jupyter notebook の Python スクリプト")

   空のインデックス コレクションから次の応答が返されます: `{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

> [!Tip]
> 無料サービスでは、3 つのインデックス、インデクサー、およびデータソースに制限されます。 このクイック スタートでは、それぞれについて、1 つずつ作成します。 先に進む前に、新しいオブジェクトを作成するための領域があることを確認してください。

## <a name="1---create-an-index"></a>1 - インデックスの作成

ポータルを使用している場合を除き、データを読み込むには、サービスにインデックスが存在する必要があります。 このステップでは、[インデックス REST API の作成](https://docs.microsoft.com/rest/api/searchservice/create-index)に関するページを参照して、インデックス スキーマをサービスにプッシュします。

フィールド コレクションは*ドキュメント*の構造を定義します。 インデックスの必要な要素には、名前とフィールド コレクションが含まれます。 各フィールドには、名前、型、およびその使用方法を決定する属性 (たとえば、フルテキスト検索可能、フィルター可能、または検索結果で取得可能) があります。 インデックス内には、`Edm.String` 型のフィールドのいずれかをドキュメント ID の*キー*として指定する必要があります。

このインデックスには "hotels-py" という名前が付けられ、次に示すフィールド定義が含まれています。 これは、他のチュートリアルで使用されている場合より大きい [Hotels インデックス](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON)のサブセットです。 簡潔にするために、このクイックスタートではそれをトリミングしています。

1. 次のセルでは、スキーマを指定するセルに次の例を貼り付けます。 

    ```python
    index_schema = {
       "name": "hotels-py",  
       "fields": [
         {"name": "HotelId", "type": "Edm.String", "key": "true", "filterable": "true"},
         {"name": "HotelName", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "true", "facetable": "false"},
         {"name": "Description", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "en.lucene"},
         {"name": "Description_fr", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "fr.lucene"},
         {"name": "Category", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Tags", "type": "Collection(Edm.String)", "searchable": "true", "filterable": "true", "sortable": "false", "facetable": "true"},
         {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Rating", "type": "Edm.Double", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Address", "type": "Edm.ComplexType", 
         "fields": [
         {"name": "StreetAddress", "type": "Edm.String", "filterable": "false", "sortable": "false", "facetable": "false", "searchable": "true"},
         {"name": "City", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "StateProvince", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "PostalCode", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Country", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"}
        ]
       }
      ]
    }
    ```

2. 別のセルで、要求を作成します。 この PUT 要求では、ご利用の検索サービスのインデックス コレクションがターゲットとされ、前の手順で指定したインデックス スキーマに基づいてインデックスが作成されます。

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. 各手順を行います。

   応答には、スキーマの JSON 表現が含まれます。 次のスクリーンショットでは、応答をより多く確認できるように、インデックス スキーマの一部をトリミングしています。

    ![インデックスを作成するための要求](media/search-get-started-python/create-index.png "インデックスを作成するための要求")

> [!Tip]
> 検証のために、ポータル内のインデックス一覧を確認することも、サービス接続要求を再実行して、インデックス コレクションに一覧表示される *hotels-py* インデックスを確認することもできます。

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - ドキュメントを読み込む

ドキュメントをプッシュするには、インデックスの URL エンドポイントに対する HTTP POST 要求を使用します。 REST API は、[ドキュメントの追加、更新、または削除](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)です。 ドキュメントは GitHub の [HotelsData ](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) から作成されています。

1. 新しいセルでは、インデックス スキーマに準拠する 4 つのドキュメントを指定します。 ドキュメントごとにアップロード アクションを指定します。

    ```python
    documents = {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
        },
        {
        "@search.action": "upload",
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
       }
      }
     ]
    }
    ```

2. 別のセルで、要求を作成します。 この POST 要求では、hotels-py インデックスのドキュメント コレクションがターゲットとされ、前の手順で指定したドキュメントがプッシュされます。

   ```python
   url = endpoint + "indexes/hotels-py/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. 各手順を行って、ご利用の検索サービス内のインデックスにドキュメントをプッシュします。 結果は次の例のようになります。 

   ```
   {'@odata.context': "https://mydemo.search.windows.net/indexes('hotels-py')/$metadata#Collection(Microsoft.Azure.Search.V2019_05_06.IndexResult)",
    'value': [{'errorMessage': None,
            'key': '1',
            'status': True,
            'statusCode': 201},
           {'errorMessage': None,
            'key': '2',
            'status': True,
            'statusCode': 201},
           {'errorMessage': None,
            'key': '3',
            'status': True,
            'statusCode': 201}]},
           {'errorMessage': None,
            'key': '4',
            'status': True,
            'statusCode': 201}]}
     ```


## <a name="3---search-an-index"></a>3 - インデックスの検索

この手順では、[Search Documents REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) を使用してインデックスのクエリを実行する方法を示します。


1. 新しいセルでは、クエリ式を指定します。 次の例では、"hotels" と "wifii" という用語を検索します。 また、一致するドキュメントの*数*を返し、検索結果に含めるフィールドを "*選択*" します。

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'
   ```

2. 要求を作成する この GET リクエストでは hotels-py インデックスの docs コレクションがターゲットとされ、前の手順で指定したクエリが添付されます。

   ```python
   url = endpoint + "indexes/hotels-py/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

   結果は次の出力のようになります。 照合する基準を提供していないため、結果はランク付けられません (search.score = 1.0)。

   ```
   {'@odata.context': "https://mydemo.search.windows.net/indexes('hotels-py')/$metadata#docs(*)",
    '@odata.count': 3,
    'value': [{'@search.score': 1.0,
               'HotelId': '1',
               'HotelName': 'Secret Point Motel'},
              {'@search.score': 1.0,
               'HotelId': '2',
               'HotelName': 'Twin Dome Motel'},
              {'@search.score': 1.0,
               'HotelId': '3',
               'HotelName': 'Triple Landscape Hotel'},
              {'@search.score': 1.0,
               'HotelId': '4',
               'HotelName': 'Sublime Cliff Hotel'}]}
   ```

3. 構文について大まかに把握するため、その他のクエリ例をいくつか試してください。 フィルタを適用したり、上位 2 つの結果を取得したり、特定のフィールドで並べ替えたりできます。

   + `searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description'`

   + `searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description'`

   + `searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince'`

## <a name="clean-up"></a>クリーンアップ 

不要になった場合は、インデックスを削除する必要があります。 無料のサービスは、3 つのインデックスに制限されています。 他のチュートリアル用の領域を確保するために、積極的に使用していないインデックスがあれば削除することをお勧めします。

   ```python
  url = endpoint + "indexes/hotels-py" + api_version
  response  = requests.delete(url, headers=headers)
   ```

既存のインデックスのリストを返すことでインデックスが削除されているか確認できます。 hotels-py がなくなっていれば、ご自分の要求が成功したことがとわかります。

```python
url = endpoint + "indexes" + api_version + "&$select=name"

response  = requests.get(url, headers=headers)
index_list = response.json()
pprint(index_list)
```

## <a name="next-steps"></a>次の手順

簡単にするために、このクイック スタートでは Hotels インデックスの短縮バージョンが使用されています。 完全なバージョンを作成して、より興味深いクエリを試してみることもできます。 完全バージョンと全部で 50 のドキュメントを取得するには、 **[データのインポート]** ウィザードを実行して、組み込みのサンプル データ ソースから *hotels-sample* を選択します。

> [!div class="nextstepaction"]
> [クイック スタート:Azure portal でインデックスを作成する](search-get-started-portal.md)
