---
title: Azure Maps Search サービスを使用して住所を検索する方法 | Microsoft Docs
description: Azure Maps Search サービスを使用して住所を検索する方法について説明します。
author: dsk-2015
ms.author: dkshir
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: fe3bb3a778a42696cd15f9e4265448479bf043a1
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2018
ms.locfileid: "42145790"
---
# <a name="how-to-find-an-address-using-the-azure-maps-search-service"></a>Azure Maps Search サービスを使用して住所を検索する方法

Maps Search サービスは、開発者が住所、場所、関心地点、事業所一覧、およびその他の地理情報を検索するために設計した RESTful API のセットです。 このサービスでは、特定の住所、交差点、地理的特徴、関心地点に対して緯度/経度を割り当てます。 検索で返された緯度と経度の値は、ルートや交通の流れなどの他の Maps サービスのパラメーターとして使用できます。

## <a name="prerequisites"></a>前提条件

Maps サービス API を呼び出すには、Maps アカウントとキーが必要です。 アカウントを作成し、キーを取得する方法については、「[How to manage your Azure Maps account and keys](how-to-manage-account-keys.md)」(Azure Maps アカウントとキーを管理する方法) を参照してください。

この記事では、[Postman アプリ](https://www.getpostman.com/apps)を使用して REST 呼び出しを構築します。 選択した任意の API 開発環境を使用できます。 


## <a name="using-fuzzy-search"></a>あいまい検索の使用

Search サービスの既定の API は[あいまい検索](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)であり、住所または POI トークンの任意の組み合わせの入力を処理します。 この検索 API は正規の "一行検索" であり、ユーザーが検索クエリとして入力した内容を把握していない場合に便利です。 あいまい検索 API は、POI 検索およびジオコーディングの組み合わせです。 また、API はコンテキストの位置 (緯度/経度 のペア) で重み付けされたり、座標と半径によって完全に制約を受けたりする場合があります。あるいは、地理的偏向のアンカー ポイントなしで、より汎用的に API を実行することもできます。

ほとんどの検索クエリは、より優れたパフォーマンスを得て想定外の結果を減らすために、既定で 'maxFuzzyLevel=1' に設定されます。 この既定値は、クエリ パラメーターに 'maxFuzzyLevel=2' または '3' を渡すことで、必要に応じて要求ごとにオーバーライドできます。

### <a name="search-for-an-address-using-fuzzy-search"></a>あいまい検索を使用した住所の検索

1. Postman アプリを開き、[新規]、[新規作成] の順にクリックして、**[GET request(\GET 要求\)]** を選択します。 **あいまい検索**の要求名を入力して、これを保存するコレクションまたはフォルダーを選択し、**[保存]** をクリックします。

2. [Builder\(ビルダー\)] タブで、**GET** HTTP メソッドを選択し、API エンドポイントの要求 URL を入力します。

    ![あいまい検索 ](./media/how-to-search-for-address/fuzzy_search_url.png)

    | パラメーター | 推奨値 |
    |---------------|------------------------------------------------|
    | HTTP メソッド | GET |
    | 要求 URL | https://atlas.microsoft.com/search/fuzzy/json? |
    | Authorization | No Auth |

    URL パスの **json** 属性で、応答形式が決定まります。 使いやすく、かつ読みやすいように、この記事では json を使用しています。 使用可能な応答形式については、[Maps Functional API のリファレンス](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)の「**Get Search Fuzzy**」(あいまいな検索の取得) の定義で確認できます。

3. **[パラメーター]** をクリックして、要求 URL のクエリまたはパスのパラメーターとして使用する次のキーと値のペアを入力します。

    ![あいまい検索 ](./media/how-to-search-for-address/fuzzy_search_params.png)

    | キー | 値 |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<Azure Maps キー\> |
    | query | pizza |

4. **[送信]** をクリックして、応答の本体を確認します。 

    あいまいなクエリ文字列 "pizza" によって、"pizza" と "restaurant" のカテゴリに含まれる 10 か所の関心地点 (POI) の結果が返されました。 各結果は、該当の場所の所在地住所、緯度/経度の値、ビュー ポート、およびエントリ ポイントを返します。
    
    特定の参照場所に結び付けられておらず、このクエリの結果は変化します。 **countrySet** パラメーターを使用して、お使いのアプリケーションで対応する必要がある国のみを指定することができます。既定の動作では全世界を検索するため、潜在的に不要な結果が返されます。

5. 次のキー/値のペアを **Params** セクションに追加し、**[送信]** をクリックします。

    | キー | 値 |
    |------------------|-------------------------|
    | countrySet | US |
    
    これで、結果が国コードによってバインドされ、クエリでは米国のピザ レストランが返されます。
    
    特定の場所を指向して結果を提供するには、関心地点にクエリを実行して、返された緯度と経度の値をあいまい検索サービスの呼び出しの中で使用します。 ここでは、Search サービスを使用してシアトル スペース ニードルの場所が返され、緯度 /経度を使用して 検索に指向が付加されました。
    
4. [パラメーター] に次のキーと値のペアを入力して、**[送信]** をクリックします。

    ![あいまい検索 ](./media/how-to-search-for-address/fuzzy_search_latlon.png)
    
    | キー | 値 |
    |-----|------------|
    | 緯度 | 47.62039 |
    | 経度 | -122.34928 |

## <a name="search-for-address-properties-and-coordinates"></a>住所のプロパティと座標の検索 

住所検索の API に所在地住所のすべてまたは一部を渡すと、緯度と経度による位置の値と、市町村や区などの詳細な住所プロパティを含む応答を受け取ることができます。

1. Postman で、**[新しい要求]** | **[GET request\(GET 要求\)]** をクリックして、「**Address Search**」 (住所検索) という名前を付けます。
2. [Builder]\(ビルダー\) タブで、**GET** HTTP メソッドを選択し、API エンドポイントの要求 URL を入力して、承認プロトコルを選択します (存在する場合)。

    ![住所検索 ](./media/how-to-search-for-address/address_search_url.png)
    
    | パラメーター | 推奨値 |
    |---------------|------------------------------------------------|
    | HTTP メソッド | GET |
    | 要求 URL | https://atlas.microsoft.com/search/address/json? |
    | Authorization | No Auth |

2. **[パラメーター]** をクリックして、要求 URL のクエリまたはパスのパラメーターとして使用する次のキーと値のペアを入力します。
    
    ![住所検索 ](./media/how-to-search-for-address/address_search_params.png)
    
    | キー | 値 |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<Azure Maps キー\> |
    | query | 400 Broad St, Seattle, WA 98109 |
    
3. **[送信]** をクリックして、応答の本体を確認します。 
    
    ここでは、完全な住所のクエリを指定したので、応答の本体には単一の結果を受信します。 
    
4. [パラメーター] では、クエリ文字列を次の値に編集します。
    ```
        400 Broad, Seattle
    ```

5. 次のキー/値のペアを **Params** セクションに追加し、**[送信]** をクリックします。

    | キー | 値 |
    |-----|------------|
    | typeahead | true |

    **typeahead** フラグは、部分的な入力値としてクエリを処理して、予測値の配列を返すよう、住所検索の API に指示します。

## <a name="search-for-a-street-address-using-reverse-address-search"></a>住所の逆引き検索を使用した所在地住所の検索
1. Postman で、**[新しい要求]** | **[GET request\(GET 要求\)]** をクリックして、「**Reverse Address Search**」 (住所の逆引き検索) という名前を付けます。

2. [Builder\(ビルダー\)] タブで、**GET** HTTP メソッドを選択し、API エンドポイントの要求 URL を入力します。
    
    ![住所の逆引き検索の URL ](./media/how-to-search-for-address/reverse_address_search_url.png)
    
    | パラメーター | 推奨値 |
    |---------------|------------------------------------------------|
    | HTTP メソッド | GET |
    | 要求 URL | https://atlas.microsoft.com/search/address/reverse/json? |
    | Authorization | No Auth |
    
2. **[パラメーター]** をクリックして、要求 URL のクエリまたはパスのパラメーターとして使用する次のキーと値のペアを入力します。
    
    ![住所の逆引き検索のパラメーター ](./media/how-to-search-for-address/reverse_address_search_params.png)
    
    | キー | 値 |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<Azure Maps キー\> |
    | query | 47.59093,-122.33263 |
    
3. **[送信]** をクリックして、応答の本体を確認します。 
    
    応答には、"stadium" という POI カテゴリと共に セーフコ フィールドの POI エントリが含まれています。 
    
4. 次のキー/値のペアを **Params** セクションに追加し、**[送信]** をクリックします。

    | キー | 値 |
    |-----|------------|
    | number | true |

    [number](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) クエリ パラメーターが要求と共に送信された場合、通りの側 (左/右) とその数値からのオフセット位置を応答に含めることができます。
    
5. 次のキー/値のペアを **Params** セクションに追加し、**[送信]** をクリックします。

    | キー | 値 |
    |-----|------------|
    | returnSpeedLimit | true |
    
    [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) クエリ パラメーターが設定された場合、応答には公示されている速度制限が返されます。

6. 次のキー/値のペアを **Params** セクションに追加し、**[送信]** をクリックします。

    | キー | 値 |
    |-----|------------|
    | returnRoadUse | true |

    [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) クエリ パラメーターが設定された場合、応答には番地レベルで逆引き地理コードの道路用途の配列が返されます。

7. 次のキー/値のペアを **Params** セクションに追加し、**[送信]** をクリックします。

    | キー | 値 |
    |-----|------------|
    | roadUse | true |

    [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) クエリ パラメーターを使用して、逆引き地理コードのクエリを特定の種類の道路用途に制限できます。
    
## <a name="search-for-the-cross-street-using-reverse-address-cross-street-search"></a>交差点住所の逆引き検索を使用して交差点を検索する

1. Postman で、**[新しい要求]** | **[GET request\(GET 要求\)]** をクリックして、「**Reverse Address Cross Street Search**」 (交差点住所の逆引き検索) という名前を付けます。

2. [Builder\(ビルダー\)] タブで、**GET** HTTP メソッドを選択し、API エンドポイントの要求 URL を入力します。
    
    ![交差点住所の逆引き検索 ](./media/how-to-search-for-address/reverse_address_search_url.png)
    
    | パラメーター | 推奨値 |
    |---------------|------------------------------------------------|
    | HTTP メソッド | GET |
    | 要求 URL | https://atlas.microsoft.com/search/address/reverse/crossstreet/json? |
    | Authorization | No Auth |
    
3. **[パラメーター]** をクリックして、要求 URL のクエリまたはパスのパラメーターとして使用する次のキーと値のペアを入力します。
    
    | キー | 値 |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<Azure Maps キー\> |
    | query | 47.59093,-122.33263 |
    
4. **[送信]** をクリックして、応答の本体を確認します。 

## <a name="next-steps"></a>次の手順
- [Azure Maps Search サービス](https://docs.microsoft.com/rest/api/maps/search) API ドキュメントを確認する 
