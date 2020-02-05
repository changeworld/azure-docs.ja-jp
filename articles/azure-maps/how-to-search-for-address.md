---
title: Azure Maps Search Service を使用して場所を検索する | Microsoft Azure Maps
description: この記事では、Microsoft Azure Maps Search Service を使用して場所を検索する方法について説明します。
author: walsehgal
ms.author: v-musehg
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 20a2c18875096680cd1eba7601e88965fcbcc568
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715357"
---
# <a name="using-azure-maps-search-services-for-geocoding-and-reverse-geocoding"></a>ジオコーディングと逆ジオコーディングに Azure Maps Search サービスを使用する

Azure Maps [Search Service](https://docs.microsoft.com/rest/api/maps/search) は、開発者が住所、場所、名前またはカテゴリ別の事業の一覧、およびその他の地理情報を検索できるように設計された RESTful API のセットです。 サービスでは、従来のジオコーディングをサポートするだけでなく、緯度と経度に基づいて住所や交差点の逆ジオコーディングを行うこともできます。 検索で返された緯度と経度の値は、[Route](https://docs.microsoft.com/rest/api/maps/route) サービスや [Weather](https://docs.microsoft.com/rest/api/maps/weather) サービスなどの他の Azure Maps サービスでパラメーターとして使用できます。

それでは次の方法を学習しましょう。

* [住所検索 API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) を使用して、住所 (ジオコード アドレスの場所) の緯度と経度の座標を要求する
* [あいまい検索 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) を使用して、住所または目的地 (POI) を検索する
* 住所をプロパティや座標と一緒に検索
* 座標位置を住所に変換するために [住所の逆引き検索](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)を行う
* [交差点住所の逆引き検索 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet) を使用した交差点の検索

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、最初に Azure Maps アカウントを作成し、Map アカウントのサブスクリプション キーを取得する必要があります。 [アカウントの作成](quick-demo-map-app.md#create-an-account-with-azure-maps)に関するページの手順に従って、Azure Maps アカウントのサブスクリプションを作成します。さらに、[主キーの取得](quick-demo-map-app.md#get-the-primary-key-for-your-account)に関するページの手順に従って、お使いのアカウントの主キーを取得します。 Azure Maps での認証の詳細については、「[Azure Maps での認証の管理](./how-to-manage-authentication.md)」を参照してください。

この記事では、[Postman アプリ](https://www.getpostman.com/apps)を使用して REST 呼び出しを構築します。 選択した任意の API 開発環境を使用できます。

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>住所の緯度と経度を要求する (ジオコーディング)

この例では、Azure Maps の [Get Search Address API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) を使用して、住所を緯度および経度の座標に変換します。 住所のすべてまたは一部を API に渡すと、番地、郵便番号、国または地域などの詳細な住所プロパティと、緯度および経度による位置の値を含む応答を受け取ることができます。

ジオコーディングする住所のセットがある場合は、[Post Search Address Batch API](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatch) を使用して、クエリのバッチを単一の API 呼び出しで送信できます。

1. Postman で、 **[新しい要求]**  |  **[GET request\(GET 要求\)]** をクリックして、「**Address Search**」 (住所検索) という名前を付けます。

2. [Builder]\(ビルダー\) タブで、**GET** HTTP メソッドを選択し、API エンドポイントの要求 URL を入力して、承認プロトコルを選択します (存在する場合)。

![住所検索](./media/how-to-search-for-address/address_search_url.png)

| パラメーター | 推奨値 |
|---------------|------------------------------------------------| 
| HTTP メソッド | GET |
| 要求 URL | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) | 
| 承認 | No Auth |

3. **[パラメーター]** をクリックして、要求 URL のクエリまたはパスのパラメーターとして使用する次のキーと値のペアを入力します。 

![住所検索](./media/how-to-search-for-address/address_search_params.png) 

| Key | Value | 
|------------------|-------------------------| 
| api-version | 1.0 | 
| subscription-key | \<Azure Maps キー\> | 
| query | 400 Broad St, Seattle, WA 98109 | 

4. **[送信]** をクリックして、応答の本体を確認します。 

ここでは、完全な住所のクエリを指定したので、応答の本体には単一の結果を受信します。 

5. [パラメーター] では、クエリ文字列を次の値に編集します。 

    ```plaintext 
        400 Broad, Seattle 
    ``` 

6. 次のキー/値のペアを **Params** セクションに追加し、 **[送信]** をクリックします。 

| Key | Value | 
|-----|------------| 
| typeahead | true | 

**typeahead** フラグは、部分的な入力値としてクエリを処理して、予測値の配列を返すよう、住所検索の API に指示します。

## <a name="search-for-an-address-using-fuzzy-search-api"></a>あいまい検索 API を使用して住所を検索する

検索クエリでユーザーの入力内容がわからない場合は、Azure Maps の[あいまい検索 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) を使用することをお勧めします。 この API は、目的地 (POI) 検索とジオコーディングを正規の "一行検索" で組み合わせています。 たとえば、この API では、任意のアドレスまたは POI のトークンの組み合わせの入力を処理できます。 また、コンテキストの位置 (緯度/経度 のペア) で重み付けされたり、座標と半径によって完全に制約を受けたりする場合があります。あるいは、地理的偏向のアンカー ポイントなしで、より汎用的に API を実行することもできます。

ほとんどの検索クエリは、パフォーマンスを得るためと想定外の結果を減らすために、既定で `maxFuzzyLevel=1` に設定されます。 この既定値は、クエリ パラメーターに `maxFuzzyLevel=2` または `3` を渡すことで、必要に応じて要求ごとにオーバーライドできます。

### <a name="search-for-an-address-using-fuzzy-search"></a>あいまい検索を使用した住所の検索

1. Postman アプリを開き、[新規]、[新規作成] の順にクリックして、 **[GET request(\GET 要求\)]** を選択します。 **あいまい検索**の要求名を入力して、これを保存するコレクションまたはフォルダーを選択し、 **[保存]** をクリックします。

2. [Builder\(ビルダー\)] タブで、**GET** HTTP メソッドを選択し、API エンドポイントの要求 URL を入力します。

    ![あいまい検索](./media/how-to-search-for-address/fuzzy_search_url.png)

    | パラメーター | 推奨値 |
    |---------------|------------------------------------------------|
    | HTTP メソッド | GET |
    | 要求 URL | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | 承認 | No Auth |

    URL パスの **json** 属性で、応答形式が決定まります。 この記事では、使いやすく、読みやすい json を使用します。 使用可能な応答形式については、[Maps Functional API のリファレンス](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)の「**Get Search Fuzzy**」(あいまいな検索の取得) の定義で確認できます。

3. **[パラメーター]** をクリックして、要求 URL のクエリまたはパスのパラメーターとして使用する次のキーと値のペアを入力します。

    ![あいまい検索](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Key | Value |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<Azure Maps キー\> |
    | query | pizza |

4. **[送信]** をクリックして、応答の本体を確認します。

    あいまいなクエリ文字列 "pizza" からは結果として、"pizza" と "restaurant" のカテゴリに含まれる 10 か所の[目的地](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) (POI) が返されました。 各結果からは、該当の場所の所在地住所、緯度値と経度値、ビュー ポート、エントリ ポイントが返されます。
  
    特定の参照場所に結び付けられておらず、このクエリの結果は変化します。 **countrySet** パラメーターを使用すると、アプリケーションの対象にする必要がある国または地域のみを指定できます。 既定の動作では全世界が検索されるので、必要のない結果が返される可能性があります。

5. 次のキー/値のペアを **Params** セクションに追加し、 **[送信]** をクリックします。

    | Key | Value |
    |------------------|-------------------------|
    | countrySet | US |
  
    これで、結果が国コードによってバインドされ、クエリでは米国のピザ レストランが返されます。
  
    ある場所の結果を得るには、関心地点にクエリを実行して、返された緯度と経度の値をあいまい検索サービスの呼び出しの中で使用します。 ここでは、Search サービスを使用してシアトル スペース ニードルの場所が返され、緯度 /経度を使用して 検索に指向が付加されました。
  
6. [パラメーター] に次のキーと値のペアを入力して、 **[送信]** をクリックします。

    ![あいまい検索](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | Key | Value |
    |-----|------------|
    | lat | 47.620525 |
    | lon | -122.349274 |

## <a name="search-for-address-properties-and-coordinates"></a>住所のプロパティと座標の検索

検索アドレス API には、完全な番地を渡すか、部分的な番地を渡すことができます。 いずれにせよ、詳しい住所プロパティが含まれる応答が返されます。 詳しい住所プロパティとは、高度、緯度、地方自治体、下位区分の位置値のような値です。

1. Postman で、 **[新しい要求]**  |  **[GET request\(GET 要求\)]** をクリックして、「**Address Search**」 (住所検索) という名前を付けます。
2. [Builder]\(ビルダー\) タブで、**GET** HTTP メソッドを選択し、API エンドポイントの要求 URL を入力して、承認プロトコルを選択します (存在する場合)。

    ![住所検索](./media/how-to-search-for-address/address_search_url.png)
  
    | パラメーター | 推奨値 |
    |---------------|------------------------------------------------|
    | HTTP メソッド | GET |
    | 要求 URL | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) |
    | 承認 | No Auth |

3. **[パラメーター]** をクリックして、要求 URL のクエリまたはパスのパラメーターとして使用する次のキーと値のペアを入力します。
  
    ![住所検索](./media/how-to-search-for-address/address_search_params.png)
  
    | Key | Value |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<Azure Maps キー\> |
    | query | 400 Broad St, Seattle, WA 98109 |
  
4. **[送信]** をクリックして、応答の本体を確認します。
  
    ここでは、完全な住所のクエリを指定したので、応答の本体には単一の結果を受信します。
  
5. [パラメーター] では、クエリ文字列を次の値に編集します。
    ```plaintext
        400 Broad, Seattle
    ```

6. 次のキー/値のペアを **Params** セクションに追加し、 **[送信]** をクリックします。

    | Key | Value |
    |-----|------------|
    | typeahead | true |

    **typeahead** フラグは、部分的な入力値としてクエリを処理して、予測値の配列を返すよう、住所検索の API に指示します。

## <a name="make-a-reverse-address-search"></a>住所の逆引き検索を行う

1. Postman で、 **[新しい要求]**  |  **[GET request\(GET 要求\)]** をクリックして、「**Reverse Address Search**」 (住所の逆引き検索) という名前を付けます。

2. [Builder\(ビルダー\)] タブで、**GET** HTTP メソッドを選択し、API エンドポイントの要求 URL を入力します。
  
    ![住所の逆引き検索の URL](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | パラメーター | 推奨値 |
    |---------------|------------------------------------------------|
    | HTTP メソッド | GET |
    | 要求 URL | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | 承認 | No Auth |
  
3. **[パラメーター]** をクリックして、要求 URL のクエリまたはパスのパラメーターとして使用する次のキーと値のペアを入力します。
  
    ![住所の逆引き検索のパラメーター](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | Key | Value |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<Azure Maps キー\> |
    | query | 47.591180,-122.332700 |
  
4. **[送信]** をクリックして、応答の本体を確認します。

    応答には、セーフコ フィールドのキー アドレス情報が含まれます。
  
5. 次のキー/値のペアを **Params** セクションに追加し、 **[送信]** をクリックします。

    | Key | Value |
    |-----|------------|
    | number | true |

    [number](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) クエリ パラメーターが要求と共に送信された場合、応答には、通りの側 (左または右) とその数値からのオフセット位置が含まれることがあります。
  
6. 次のキー/値のペアを **Params** セクションに追加し、 **[送信]** をクリックします。

    | Key | Value |
    |-----|------------|
    | returnSpeedLimit | true |
  
    [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) クエリ パラメーターが設定されているとき、応答からは公示速度制限が返されます。

7. 次のキー/値のペアを **Params** セクションに追加し、 **[送信]** をクリックします。

    | Key | Value |
    |-----|------------|
    | returnRoadUse | true |

    [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) クエリ パラメーターが設定された場合、応答には番地レベルで逆引き地理コードの道路用途の配列が返されます。

8. 次のキー/値のペアを **Params** セクションに追加し、 **[送信]** をクリックします。

    | Key | Value |
    |-----|------------|
    | roadUse | true |

    [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) クエリ パラメーターを使用して、逆ジオコード クエリを特定の種類の道路に制限できます。
  
## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>交差点住所の逆引き検索を使用して交差点を検索する

1. Postman で、 **[新しい要求]**  |  **[GET request\(GET 要求\)]** をクリックして、「**Reverse Address Cross Street Search**」 (交差点住所の逆引き検索) という名前を付けます。

2. [Builder\(ビルダー\)] タブで、**GET** HTTP メソッドを選択し、API エンドポイントの要求 URL を入力します。
  
    ![交差点住所の逆引き検索](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | パラメーター | 推奨値 |
    |---------------|------------------------------------------------|
    | HTTP メソッド | GET |
    | 要求 URL | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | 承認 | No Auth |
  
3. **[パラメーター]** をクリックして、要求 URL のクエリまたはパスのパラメーターとして使用する次のキーと値のペアを入力します。
  
    | Key | Value |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<Azure Maps キー\> |
    | query | 47.591180,-122.332700 |
  
4. **[送信]** をクリックして、応答の本体を確認します。

## <a name="next-steps"></a>次のステップ

- [Azure Maps Search サービス](https://docs.microsoft.com/rest/api/maps/search) API ドキュメントを確認する。
