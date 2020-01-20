---
title: Google マップから Web サービスを移行する | Microsoft Docs
description: Web サービスを Google マップから Microsoft Azure Maps に移行する方法に関するチュートリアルです。
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 8fc3270d15f77576ff353a110d8a3a9a17c7144d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75474997"
---
# <a name="migrate-web-service-from-google-maps"></a>Google マップから Web サービスを移行する

Azure Maps と Google マップでは、どちらの場合も REST Web サービスを介して空間 API にアクセスすることができます。 これらのプラットフォームの API インターフェイスは同様の機能を実行しますが、使用される名前付け規則と応答オブジェクトが異なります。

次の表に、一覧に示される Google マップ サービス API と同様の機能を提供する Azure Maps サービス API を示します。

| Google マップ サービス API | Azure Maps サービス API                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| 道順              | [Route](https://docs.microsoft.com/rest/api/maps/route)                               |
| 距離行列         | [ルート マトリックス](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) |
| ジオコーディング               | [検索](https://docs.microsoft.com/rest/api/maps/search)                             |
| 場所の検索           | [検索](https://docs.microsoft.com/rest/api/maps/search)                             |
| 場所のオートコンプリート      | [検索](https://docs.microsoft.com/rest/api/maps/search)                             |
| 静的マップ              | [Render](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                 |
| タイム ゾーン               | [タイム ゾーン](https://docs.microsoft.com/rest/api/maps/timezone)                        |

次のサービス API は、Azure Maps では現在使用できません。

- Elevation
- 地理的位置情報
- 場所の詳細と写真。 Azure Maps 検索 API で使用可能な電話番号と Web サイトの URL。
- マップの URL
- 道路 - 速度制限のデータは、Azure Maps のルート API と逆ジオコーディング API を介して利用することができます。
- 静的ストリート ビュー

Azure Maps には、必要になる可能性のある追加の REST Web サービスがいくつか用意されています。

- [空間演算](https://docs.microsoft.com/rest/api/maps/spatial): ジオフェンシングなどの複雑な空間計算や空間演算をサービスにオフロードします。
- [交通情報](https://docs.microsoft.com/rest/api/maps/traffic): 交通流量や事故に関するリアルタイム データにアクセスできます。

## <a name="geocoding-addresses"></a>住所のジオコーディング

ジオコーディングは、住所 (たとえば、"1 Microsoft way, Redmond, WA") を座標 (たとえば、経度: -122.1298、緯度: 47.64005) に変換するプロセスです。 多くの場合、この後、座標を使用して、マップにマーカーを位置付けたり、座標の場所をマップの中央に表示したりします。

Azure Maps により、住所をジオコーディングするための複数の方法が提供されます。

- [**自由形式の住所のジオコーディング**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): 1 行の住所文字列 ("1 Microsoft way, Redmond, WA" など) を指定します。要求はただちに処理されます。 これは、個々の住所を迅速にジオコーディングする必要がある場合に推奨されます。
- [**構造化された住所のジオコーディング**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): 単一の住所の一部 (番地、市区町村、国、郵便番号など) を指定します。要求はただちに処理されます。 これは、個々の住所を迅速にジオコーディングする必要があり、データが既に個別の住所の一部として解析されている場合に推奨されます。
- [**住所のバッチ ジオコーディング**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview): 最大 10,000 個の住所を含む要求を作成します。これらの住所は一定期間内に処理されます。 すべての住所はサーバーで並行してジオコーディングされ、完了すると、完全な結果セットをダウンロードできます。 これは、大きなデータ セットをジオコーディングする場合に推奨されます。
- [**あいまい検索**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): この API は、住所のジオコーディングと目的地検索を組み合わせたものです。 住所、場所、ランドマーク、目的地、または目的地のカテゴリを自由形式の文字列として指定することができます。要求はただちに処理されます。 この API は、ユーザーが同じテキスト ボックスから住所や目的地を検索できるアプリケーションに適しています。
- [**あいまいバッチ検索**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): 最大 10,000 個の住所、場所、ランドマーク、または目的地を含む要求を作成します。これらは一定期間内に処理されます。 すべてのデータはサーバーで並行して処理され、完了すると、完全な結果セットをダウンロードすることができます。

次の表では、Google マップ API パラメーターと、それに相当する Azure Maps 内の API パラメーターを相互参照で示しています。

| Google マップ API パラメーター | 相当する Azure Maps API パラメーター  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` および `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality` - 市区町村<br/>`municipalitySubdivision` - 地域、地区<br/>`countrySubdivision` - 都道府県<br/>`countrySecondarySubdivision` - 郡<br/>`countryTertiarySubdivision` - 区<br/>`countryCode` - 2 文字の国番号 |
| `key`                       | `subscription-key` - ドキュメント「[Azure Maps による認証](azure-maps-authentication.md)」も参照してください。 |
| `language`                  | `language` - [サポートされている言語](supported-languages.md)に関するドキュメントも参照してください。  |
| `region`                    | `countrySet`                       |

検索サービスの使用方法の例については、[こちら](how-to-search-for-address.md)を参照してください。 [検索のベスト プラクティス](how-to-use-best-practices-for-search.md)に関するドキュメントを、必ずご覧ください。

> [!TIP]
> 自由形式のアドレスのジオコーディング API とあいまい検索 API は、`&amp;typeahead=true` を要求 URL に追加することにより、オートコンプリート モードで使用できます。 これにより、入力テキストが部分的なものである可能性が高く、予測モードが開始されることがサーバーに通知されます。

## <a name="reverse-geocode-a-coordinate"></a>座標の逆ジオコーディング

逆ジオコーディングは、地理座標 (たとえば、経度: -122.1298、緯度: 47.64005) をおよその住所 (たとえば、"1 Microsoft way, Redmond, WA") に変換するプロセスです。

Azure Maps では、複数の逆ジオコーディング メソッドが提供されています。

- [**住所の逆ジオコーダー**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse): 1 つの地理座標を指定すると、そのおよその住所が取得されます。要求はただちに処理されます。
- [**交差点の逆ジオコーダー**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet): 1 つの地理座標を指定すると周辺の交差点情報 (たとえば、最初の交差点や主要交差点など) が取得されます。要求はただちに処理されます。
- [**住所のバッチ逆ジオコーダー**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview): 最大 10,000 個の座標を含む要求が作成されます。これらの座標は一定期間内に処理されます。 すべてのデータはサーバーで並行して処理され、完了すると、完全な結果セットをダウンロードすることができます。

次の表では、Google マップ API パラメーターと、それに相当する Azure Maps 内の API パラメーターを相互参照で示しています。

| Google マップ API パラメーター   | 相当する Azure Maps API パラメーター   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key` - ドキュメント「[Azure Maps による認証](azure-maps-authentication.md)」も参照してください。 |
| `language`                  | `language` - [サポートされている言語](supported-languages.md)に関するドキュメントも参照してください。  |
| `latlng`                    | `query`  |
| `location_type`             | *N/A*     |
| `result_type`               | `entityType`    |

[検索のベスト プラクティス](how-to-use-best-practices-for-search.md)に関するドキュメントを、必ずご覧ください。

Azure Maps の逆ジオコーディング API には、Google マップでは利用できない追加機能がいくつかあります。これらは、アプリを移行する際に統合するのに役立つ場合があります。

- 速度制限のデータの取得。
- 道路使用情報の取得: 地方の道路、幹線道路、自動車専用道路、ランプ。
- 座標が位置する通りの側。

## <a name="search-for-points-of-interest"></a>目的地を検索する

Google マップで目的地データを検索するには、場所検索 API を使用します。 この API により、目的地を検索するための 3 つの異なる方法が提供されます。

- **テキストから場所を検索する:** 目的地を名前、住所、または電話番号に基づいて検索します。
- **近隣検索**: ある場所から特定の距離内にある目的地を検索します。
- **テキスト検索:** 目的地や場所の情報を含む自由形式のテキストを使用して場所を検索します。 たとえば、"ニューヨークのピザ" や "メイン ストリート付近のレストラン" などです。

Azure Maps には、目的地を検索するための複数の検索 API が用意されています。

- [**POI 検索**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi): 目的地を名前で検索します。 たとえば、"スターバックス" などです。
- [**POI カテゴリ検索**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory): 目的地をカテゴリで検索します。 たとえば、"レストラン" などです。
- [**近隣検索**](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby): ある場所から特定の距離内にある目的地を検索します。
- [**あいまい検索**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): この API は、住所のジオコーディングと目的地検索を組み合わせたものです。 住所、場所、ランドマーク、目的地、または目的地のカテゴリを自由形式の文字列として指定することができます。要求はただちに処理されます。 この API は、ユーザーが同じテキスト ボックスから住所や目的地を検索できるアプリケーションに適しています。
- [**ジオメトリ内検索**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry): 指定されたジオメトリ (ポリゴン) 内にある目的地を検索します。
- [**ルートに沿った検索**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute): 指定されたルートに沿って目的地を検索します。
- [**あいまいバッチ検索**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): 最大 10,000 個の住所、場所、ランドマーク、または目的地を含む要求を作成します。これらは一定期間内に処理されます。 すべてのデータはサーバーで並行して処理され、完了すると、完全な結果セットをダウンロードすることができます。

現在、Azure Maps には、Google マップのテキスト検索 API に相当する API はありません。

> [!TIP]
> POI 検索、POI カテゴリ検索、あいまい検索の各 API は、`&amp;typeahead=true` を要求 URL に追加することにより、オートコンプリート モードで使用できます。 これにより、入力テキストが部分的なものである可能性が高く、予測モードが開始されることがサーバーに通知されます。

[検索のベスト プラクティス](how-to-use-best-practices-for-search.md)に関するドキュメントを、必ずご覧ください。

### <a name="find-place-from-text"></a>テキストから場所を検索する

Azure Maps で目的地を名前または住所で検索するには、[POI 検索](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) API および[あいまい検索](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) API を使用します。

次の表では、Google マップ API パラメーターと、それに相当する Azure Maps 内の API パラメーターを相互参照で示しています。

| Google マップ API パラメーター | 相当する Azure Maps API パラメーター |
|---------------------------|-------------------------------------|
| `fields`                  | *N/A*                               |
| `input`                   | `query`                             |
| `inputtype`               | *N/A*                               |
| `key`                     | `subscription-key` - ドキュメント「[Azure Maps による認証](azure-maps-authentication.md)」も参照してください。 |
| `language`                | `language` - [サポートされている言語](supported-languages.md)に関するドキュメントも参照してください。  |
| `locationbias`            | `lat`、`lon`、`radius`<br/>`topLeft` および `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>近隣検索

Azure Maps で付近の目的地を取得するには、[近隣検索](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) API を使用します。

次の表では、Google マップ API パラメーターと、それに相当する Azure Maps 内の API パラメーターを相互参照で示しています。

| Google マップ API パラメーター | 相当する Azure Maps API パラメーター  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key` - ドキュメント「[Azure Maps による認証](azure-maps-authentication.md)」も参照してください。 |
| `keyword`                   | `categorySet` および `brandSet`        |
| `language`                  | `language` - [サポートされている言語](supported-languages.md)に関するドキュメントも参照してください。  |
| `location`                  | `lat` および `lon`                     |
| `maxprice`                  | *N/A*                               |
| `minprice`                  | *N/A*                               |
| `name`                      | `categorySet` および `brandSet`        |
| `opennow`                   | *N/A*                               |
| `pagetoken`                 | `ofs` および `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *N/A*                               |
| `type`                      | `categorySet –`[サポートされている検索カテゴリ](supported-search-categories.md)に関するドキュメントを参照してください。   |

## <a name="calculate-routes-and-directions"></a>ルートと道順を計算する

Azure Maps を使用してルートと道順を計算することができます。 Azure Maps には、Google マップの経路探索サービスと同じ機能が数多く用意されています。たとえば、次のようなものがあります。

- 到着および出発の時刻。
- リアルタイムと予測ベースの交通路。
- さまざまな移動手段: 乗用車、徒歩、自転車。

> [!NOTE]
> Azure Maps では、すべてのウェイポイントを座標にする必要があります。 住所は、最初にジオコーディングする必要があります。

Azure Maps のルート指定サービスでは、ルート指定の計算用として次の API が提供されます。

- [**ルート計算**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections): ルートが計算されます。要求はただちに処理されます。 この API では、GET 要求と POST 要求の両方がサポートされます。 大量のウェイポイントを指定する場合、または多くのルート オプションを使用する場合は、URL 要求が長くなりすぎて問題が発生することがないように POST 要求が推奨されます。
- [**ルートのバッチ処理**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview): 最大 1,000 個のルート要求を含む要求が作成されます。これらの座標は一定期間内に処理されます。 すべてのデータはサーバーで並行して処理され、完了すると、完全な結果セットをダウンロードすることができます。
- [**モビリティ サービス**](https://docs.microsoft.com/rest/api/maps/mobility): 公共輸送を使用するルートと道順が計算されます。

次の表では、Google マップ API パラメーターと、それに相当する Azure Maps 内の API パラメーターを相互参照で示しています。

| Google マップ API パラメーター    | 相当する Azure Maps API パラメーター  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query` - `"lat0,lon0:lat1,lon1…."` 形式の座標  |
| `key`                          | `subscription-key` - ドキュメント「[Azure Maps による認証](azure-maps-authentication.md)」も参照してください。 |
| `language`                     | `language` - [サポートされている言語](supported-languages.md)に関するドキュメントも参照してください。   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | *N/A* - これはジオコーディング関連の機能です。 Azure Maps ジオコーディング API を使用する場合は、*countrySet* パラメーターを使用します。  |
| `traffic_model`               | *N/A* - 交通情報データを *traffic* パラメーターで使用する必要がある場合のみ指定できます。 |
| `transit_mode`                | [モビリティ サービスのドキュメント](https://docs.microsoft.com/rest/api/maps/mobility)を参照してください。 |
| `transit_routing_preference` | [モビリティ サービスのドキュメント](https://docs.microsoft.com/rest/api/maps/mobility)を参照してください。 |
| `units`                        | *N/A* - Azure Maps ではメートル法のみが使用されます。  |
| `waypoints`                    | `query`                            |

> [!TIP]
> 既定では、Azure Maps ルート API により、概要 (距離と時間) とルートの座標のみが返されます。 道案内の指示を取得するには、`instructionsType` パラメーターを使用します。 `routeRepresentation` パラメーターを使用すると、概要とルートをフィルター処理することができます。

Azure Maps のルート指定 API には、Google マップでは利用できない追加機能が多数用意されています。これらは、アプリを移行する際に統合するのに役立つ場合があります。

- ルートの種類のサポート: 最短、最速、スリリング、最も燃費が良い。
- 追加の移動モードのサポート: バス、バイク、タクシー、トラック、ライトバン。
- 150 個のウェイポイントのサポート。
- 1 つの要求で複数の移動時間を計算: 過去の交通量、現在の交通量、交通量なし。
- 追加の道路の種類を回避: 自動車の相乗り用車線、未舗装道路、既に使用したことがある道路。
- 回避するカスタム領域を指定。
- ルートが上る標高を制限。
- エンジン仕様に基づくルート指定。 燃料または充電の残量とエンジン仕様に基づいて内燃自動車または電気自動車のルートを計算。
- 商用車両ルート パラメーターのサポート: 車両の寸法、重量、アクセル回数、積み荷の種類。
- 車両の最大速度を指定。

これらの機能に加えて、Azure Maps ではさらに、[ルート可能な範囲の計算](https://docs.microsoft.com/rest/api/maps/route/getrouterange)もサポートされます。これは等時間マップとも呼ばれるもので、指定された時間内または燃料や充電の残量内で出発地点から任意の方向に移動できる領域をカバーするポリゴンが生成されます。

## <a name="retrieve-a-map-image"></a>マップ イメージを取得する

Azure Maps には、静的マップ イメージにデータを重ねてレンダリングするための API が用意されています。 Azure Maps の[マップ イメージのレンダリング](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) API は、Google マップの静的マップ API に相当します。

> [!NOTE]
> Azure Maps では、中央、すべてのマーカー、経路の位置を "緯度,経度" 形式の座標にする必要がありますが、Google マップでは、"緯度,経度" 形式が使用されます。 住所は、最初にジオコーディングする必要があります。

次の表では、Google マップ API パラメーターと、それに相当する Azure Maps 内の API パラメーターを相互参照で示しています。

| Google マップ API パラメーター | 相当する Azure Maps API パラメーター  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format` - URL パスの一部として指定されます。 現時点では、PNG のみがサポートされています。 |
| `key`                       | `subscription-key` - ドキュメント「[Azure Maps による認証](azure-maps-authentication.md)」も参照してください。 |
| `language`                  | `language` - [サポートされている言語](supported-languages.md)に関するドキュメントも参照してください。  |
| `maptype`                   | `layer` および `style` - [サポートされているマップ スタイル](supported-map-styles.md)に関するドキュメントを参照してください。 |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | *N/A* - これはジオコーディング関連の機能です。 Azure Maps のジオコーディング API を使用する場合は、`countrySet` パラメーターを使用します。  |
| `scale`                     | *N/A*                              |
| `size`                      | `width` および `height` - 最大 8192 x 8192 のサイズを指定できます。 |
| `style`                     | *N/A*                              |
| `visible`                   | *N/A*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> Azure Maps では、Google マップで使用されるマップ タイルの 2 倍のサイズのタイルを使用するタイル システムが使用されます。 このため、Azure Maps のズーム レベル値では、Google マップと比べて 1 つ上のズーム レベルが表示されます。 移行する要求では、ズーム レベルを 1 つ下げて、これを補正してください。

詳細については、[マップ イメージのレンダリング API に関する攻略ガイド](how-to-render-custom-data.md)を参照してください。

Azure Maps のレンダリング サービスでは、静的マップ イメージを生成できるだけでなく、ラスター (PNG) およびベクター形式のマップ タイルに直接アクセスすることもできます。

- [**マップ タイル**](https://docs.microsoft.com/rest/api/maps/render/getmaptile): ベース マップ (道路、境界、背景) のラスター (PNG) タイルとベクター タイルを取得します。
- [**マップ イメージ タイル**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile): 航空写真と衛星画像のタイルを取得します。

> [!TIP]
> 多くの Google マップ アプリケーションでは、数年前にコスト削減方法として対話型のマップ エクスペリエンスから静的マップ イメージに切り替えられました。 Web SDK の対話型マップ コントロールは、マップ タイルの読み込みに基づいて課金されるため、Azure Maps では、多くの場合、このコントロールを使用する方がはるかにコスト効率が高くなります。 Azure Maps のマップ タイルはサイズが大きいため、多くの場合、数個のタイルだけで静的マップと同じマップ ビューを作成することができます。また、マップ タイルはブラウザーによって自動的にキャッシュされます。 このため、対話型のマップ コントロールでは、静的マップ ビューを再作成する際、トランザクションの一部のみを生成するだけで済むことがほとんどです。 パンおよびズームでは読み込まれるタイルは増えますが、マップ コントロール内に、必要に応じてこの動作を無効にするオプションがあります。 対話型マップ コントロールには、静的マップ サービスで提供される以上の視覚化オプションも用意されています。

### <a name="marker-url-parameter-format-comparison"></a>マーカー URL パラメーターの形式の比較

**移行前:Google マップ**

Google マップでは、URL で `markers` パラメーターを使用することにより、マーカーを静的マップ イメージに追加できます。 `markers` パラメーターには、次に示すように、スタイルと、そのスタイルを使用してマップにレンダリングされる場所の一覧が含まれます。

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

追加のスタイルを使用するには、異なるスタイルや場所のセットを指定した別の `markers` パラメーターを URL に追加します。

マーカーの場所は、"緯度,経度" の形式で指定します。

Google マップでマーカーのスタイルを追加するには、形式 `optionName:value` を使用します。複数のスタイルを指定する場合は、"optionName1:value1\|optionName2:value2" のようにパイプ (\|) 文字で区切ります。 オプションの名前と値はコロン (:) で区切ることに注意してください。 Google マップでマーカーのスタイルを設定するには、次のスタイル オプション名を使用します。

- `color` - 既定のマーカー アイコンの色。 24 ビットの 16 進カラー値 (`0xrrggbb`)、または値 `black`、`brown`、`green`、`purple`、`yellow`、`blue`、`gray`、`orange`、`red`、`white` のいずれかを指定できます。
- `label` - アイコンの上に表示する 1 文字の大文字の英数字。
- `size` - マーカーのサイズ。 `tiny`、`mid`、または `small` を指定できます。

Google マップでは、次のスタイル オプション名を使用してカスタム アイコンを使用することもできます。

- `anchor` - アイコン イメージを座標に合わせて配置する方法を指定します。 ピクセル (x,y) 値、または値 `top`、`bottom`、`left`、`right`、`center`、`topleft`、`topright`、`bottomleft`、`bottomright` のいずれかを指定できます。
- `icon` - アイコン イメージを指す URL。

たとえば、Google マップで、マップの座標 (経度: -110、緯度: 45) に赤色で中サイズのマーカーを追加するには、次の URL パラメーターを使用します。

```
&markers=color:red|size:mid|45,-110
```

<center>

![Google マップのマーカー](media/migrate-google-maps-web-services/google-maps-marker.png)</center>

**移行後: Azure Maps**

Azure Maps では、URL で `pins` パラメーターを指定することにより、マーカーを静的マップ イメージに追加できます。 Google マップと同様、このパラメーターでスタイルと場所の一覧を指定できます。また、異なるスタイルのマーカーをサポートするために、`pins` パラメーターを複数回指定することができます。

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

追加のスタイルを使用するには、異なるスタイルや場所のセットを指定した別の `pins` パラメーターを URL に追加します。

場所のピン留めについては、Azure Maps の場合、座標を "緯度 経度" 形式にする必要がありますが、Google マップでは、"緯度,経度" 形式が使用されます。 さらに、Azure Maps では、経度と緯度をコンマではなくスペースで区切ることにも注意してください。

`iconType` 値は、作成するピンの種類を指定します。次の値を指定することができます。

- `default` - 既定のピン アイコン。
- `none` - アイコンは表示されず、ラベルのみがレンダリングされます。
- `custom` - カスタム アイコンを使用することを指定します。 アイコン イメージを指す URL は、ピンの位置情報の後、`pins` パラメーターの末尾に追加できます。
- `{udid}` - Azure Maps データ ストレージ プラットフォームに格納されるアイコンの固有データ ID (UDID)。

Azure Maps でピンのスタイルを追加するには、形式 `optionNameValue` を使用します。複数のスタイルを指定する場合は、`iconType|optionName1Value1|optionName2Value2` のようにパイプ (\|) 文字で区切ります。 オプションの名前と値は区切らないことに注意してください。 Azure Maps でマーカーのスタイルを設定するには、次のスタイル オプション名を使用します。

- `al` - マーカーの不透明度 (アルファ) を指定します。 0 から 1 までの範囲の数値を指定できます。
- `an` - ピンのアンカーを指定します。 x と y のピクセル値を形式 "x y" で指定します。
- `co` - ピンの色。 24 ビットの 16 進カラー値 (`000000` から `FFFFFF`) を指定する必要があります。
- `la` - ラベルのアンカーを指定します。 x と y のピクセル値を形式 "x y" で指定します。
- `lc` - ラベルの色。 24 ビットの 16 進カラー値 (`000000` から `FFFFFF`) を指定する必要があります。
- `ls` - ラベルのサイズ (ピクセル単位)。 0 より大きい数値を指定できます。
- `ro` - アイコンを回転させる角度の値。 -360 から 360 までの範囲の数値を指定できます。
- `sc` - ピン アイコンのスケール値。 0 より大きい数値を指定できます。

場所の一覧内にあるすべてのマーカーに単一のラベル値を適用するのではなく、ピンの位置ごとにラベル値を指定します。 ラベル値には、複数の文字で構成される文字列を指定することができます。また、スタイルや場所の値として誤って解釈されないようにするため、一重引用符で囲むこともできます。

たとえば、Azure Maps で、赤色 (`FF0000`) の既定のアイコンと、座標 (経度: -122.349300、緯度: 47.620180) にあるアイコンの下 (15 50) に位置付けられたラベル "Space Needle" を追加するには、次の URL パラメーターを使用します。

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

<center>

![Azure Maps のマーカー](media/migrate-google-maps-web-services/azure-maps-marker.png)</center>

次の例では、ラベル値が 1'、'2'、'3' の 3 つのピンを追加します。

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

<center>

![Azure Maps の複数のマーカー](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)</center>

### <a name="path-url-parameter-format-comparison"></a>経路 URL パラメーターの形式の比較

**移行前:Google マップ**

Google マップでは、URL で `path` パラメーターを使用することにより、線とポリゴンを静的マップ イメージに追加できます。 `path` パラメーターには、次に示すように、スタイルと、そのスタイルを使用してマップにレンダリングされる場所の一覧が含まれます。

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

追加のスタイルを使用するには、異なるスタイルや場所のセットを指定した別の `path` パラメーターを URL に追加します。

Google マップで経路の位置を指定するには、形式 `latitude1,longitude1|latitude2,longitude2|…` を使用します。 経路は、エンコードすることも、地点の住所を含めることもできます。

Google マップで経路のスタイルを追加するには、形式 `optionName:value` を使用します。複数のスタイルを指定する場合は、`optionName1:value1|optionName2:value2` のようにパイプ (\|) 文字で区切ります。 オプションの名前と値はコロン (:) で区切ることに注意してください。 Google マップで経路のスタイルを設定するには、次のスタイル オプション名を使用します。

- `color` - 経路またはポリゴンの枠線の色。 24 ビットの 16 進カラー値 (`0xrrggbb`)、32 ビットの 16 進カラー値 (`0xrrggbbbaa`)、または値 black、brown、green、purple、yellow、blue、gray、orange、red、white のいずれかを指定できます。
- `fillColor` - 経路領域 (ポリゴン) を塗りつぶす色。 24 ビットの 16 進カラー値 (`0xrrggbb`)、32 ビットの 16 進カラー値 (`0xrrggbbbaa`)、または値 black、brown、green、purple、yellow、blue、gray、orange、red、white のいずれかを指定できます。
- `geodesic` - 経路を地球の曲線に沿った線にする必要があるかどうかを示します。
- `weight` - 経路の線の太さ (ピクセル単位)。

たとえば、Google マップで、不透明度が 50%、太さが 4 ピクセルの赤色の線をマップの座標 (経度: -110、緯度: 45) と座標 (経度: -100、緯度: 50) の間に追加するには、次の URL を使用します。

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

<center>

![Google マップのポリライン](media/migrate-google-maps-web-services/google-maps-polyline.png)</center>

**移行後: Azure Maps**

Azure Maps では、URL で `path` パラメーターを指定することにより、線とポリゴンを静的マップ イメージに追加できます。 Google マップと同様、このパラメーターでスタイルと場所の一覧を指定できます。また、異なるスタイルの複数の円、線、ポリゴンをレンダリングするために、`path` パラメーターを複数回指定することができます。

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

経路の位置については、Azure Maps の場合、座標を "緯度 経度" 形式にする必要がありますが、Google マップでは、"緯度,経度" 形式が使用されます。 さらに、Azure Maps では、経度と緯度をコンマではなくスペースで区切ることにも注意してください。 Azure Maps では、エンコードされた経路または地点の住所はサポートされません。 大きなデータ セットを GeoJSON 塗りつぶしとして Azure Maps データ ストレージ API に読み込むことができます。[こちら](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage)を参照してください。

Azure Maps で経路のスタイルを追加するには、形式 `optionNameValue` を使用します。複数のスタイルを指定する場合は、`optionName1Value1|optionName2Value2` のようにパイプ (\|) 文字で区切ります。 オプションの名前と値は区切らないことに注意してください。 Azure Maps で経路のスタイルを設定するには、次のスタイル オプション名を使用します。

- `fa` - ポリゴンをレンダリングする際に使用する塗りつぶしの色の不透明度 (アルファ)。 0 から 1 までの範囲の数値を指定できます。
- `fc` - ポリゴンの領域をレンダリングするために使用される塗りつぶしの色。
- `la` - 線およびポリゴンの枠線をレンダリングする際に使用される線の色の不透明度 (アルファ)。 0 から 1 までの範囲の数値を指定できます。
- `lc` - 線およびポリゴンの枠線をレンダリングするために使用される線の色。
- `lw` - 線の幅 (ピクセル単位)。
- `ra` - 円の半径をメートル単位で指定します。

たとえば、Azure Maps で、不透明度が 50%、太さが 4 ピクセルの赤色の線をマップの座標 (経度: -110、緯度: 45) と座標 (経度: -100、緯度: 50) の間に追加するには、次の URL を使用します。

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

<center>

![Azure Maps のポリライン](media/migrate-google-maps-web-services/azure-maps-polyline.png)</center>

## <a name="calculate-a-distance-matrix"></a>距離行列を計算する

Azure Maps には、場所のセット間の移動時間と移動距離を距離行列として計算するための API が用意されています。 Azure Maps の距離行列 API は、Google マップの距離行列 API に相当します。

- [**ルート マトリックス**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview): 出発地と目的地のセットについて移動時間と移動距離が非同期で計算されます。 要求あたり最大 700 個のセルがサポートされます (出発地の数 × 目的地の数)。 この制限を念頭に置いて考えられるマトリックス ディメンションの例としては、700x1、50x10、10x10、28x25、10x70 などがあります。

> [!NOTE]
> 距離行列 API に対する要求は、要求の本文で出発地と目的地の情報を指定した POST 要求を使用してのみ作成できます。 さらに、Azure Maps では、すべての出発地および目的地を座標にする必要があります。 住所は、最初にジオコーディングする必要があります。

次の表では、Google マップ API パラメーターと、それに相当する Azure Maps 内の API パラメーターを相互参照で示しています。

| Google マップ API パラメーター      | 相当する Azure Maps API パラメーター  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination` - POST 要求の本文で GeoJSON として指定します。 |
| `key`                          | `subscription-key` - ドキュメント「[Azure Maps による認証](azure-maps-authentication.md)」も参照してください。 |
| `language`                     | `language` - [サポートされている言語](supported-languages.md)に関するドキュメントも参照してください。  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins` - POST 要求の本文で GeoJSON として指定します。  |
| `region`                       | *N/A* - これはジオコーディング関連の機能です。 Azure Maps のジオコーディング API を使用する場合は、`countrySet` パラメーターを使用します。 |
| `traffic_model`                | *N/A* - 交通情報データを `traffic` パラメーターで使用する必要がある場合のみ指定できます。 |
| `transit_mode`                 | *N/A* - 輸送ベースの距離マトリックスは現在サポートされていません。  |
| `transit_routing_preference`   | *N/A* - 輸送ベースの距離マトリックスは現在サポートされていません。  |
| `units`                        | *N/A* - Azure Maps ではメートル法のみが使用されます。 |

> [!TIP]
> Azure Maps のルート指定 API で使用できる高度なルート指定オプション (トラック ルート指定、エンジン仕様、回避など) はすべて、Azure Maps 距離行列 API でサポートされます。

## <a name="get-a-time-zone"></a>タイム ゾーンを取得する

Azure Maps には、座標が位置する場所のタイム ゾーンを取得するための API が用意されています。 Azure Maps のタイム ゾーン API は、Google マップのタイム ゾーン API に相当します。

- [**座標によるタイム ゾーン**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates): 座標を指定して、その座標が位置する場所のタイム ゾーンの詳細を取得します。

次の表では、Google マップ API パラメーターと、それに相当する Azure Maps 内の API パラメーターを相互参照で示しています。

| Google マップ API パラメーター | 相当する Azure Maps API パラメーター   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key` - ドキュメント「[Azure Maps による認証](azure-maps-authentication.md)」も参照してください。       |
| `language`                  | `language` - [サポートされている言語](supported-languages.md)に関するドキュメントも参照してください。    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

この Azure Maps プラットフォームでは、この API に加えて、タイム ゾーン名と ID を使用する他のタイム ゾーン API も多数用意されており、これらは変換に役立ちます。

- [**ID によるタイム ゾーン**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid): 指定された IANA タイム ゾーン ID について現在、履歴、将来のタイム ゾーンに関する情報が返されます。
- [**タイム ゾーン列挙型 IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana): IANA タイム ゾーン ID の完全な一覧が返されます。 IANA サービスの更新は、1 日以内にシステムに反映されます。
- [**タイム ゾーン列挙型 Windows**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows): Windows タイム ゾーン ID の完全な一覧が返されます。
- [**タイム ゾーン IANA バージョン**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion): Azure Maps で現在使用されている IANA のバージョン番号が返されます。
- [**タイム ゾーン Windows から IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana): 有効な Windows タイム ゾーン ID が指定された場合、対応する IANA ID が返されます。 1 つの Windows ID に対して複数の IANA ID が返される場合があります。

## <a name="client-libraries"></a>クライアント ライブラリ

Azure Maps には、次のプログラミング言語用のクライアント ライブラリが用意されています。

- JavaScript、TypeScript、Node.js - [ドキュメント](how-to-use-services-module.md) \| [NPM パッケージ](https://www.npmjs.com/package/azure-maps-rest)

その他のプログラミング言語用のオープンソース クライアント ライブラリ:

- .NET Standard 2.0 - [GitHub プロジェクト](https://github.com/perfahlen/AzureMapsRestServices) \| [NuGet パッケージ](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="additional-resources"></a>その他のリソース

Azure Maps REST サービスに関するその他のドキュメントとリソースの一部を次に示します。

- [検索のベスト プラクティス](how-to-use-best-practices-for-search.md)
- [住所の検索](how-to-search-for-address.md)
- [Azure Maps REST サービス API のリファレンス ドキュメント](https://docs.microsoft.com/rest/api/maps/)

## <a name="next-steps"></a>次のステップ

Azure Maps REST サービスの詳細について学習します。

> [!div class="nextstepaction"]
> [検索サービスを使用するためのベスト プラクティス](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [サービス モジュール (Web SDK) の使用方法](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [コード サンプル](https://docs.microsoft.com/samples/browse/?products=azure-maps)