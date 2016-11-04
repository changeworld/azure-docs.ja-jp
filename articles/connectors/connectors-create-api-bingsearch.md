---
title: ロジック アプリに Bing Search コネクタを追加する | Microsoft Docs
description: Bing Search コネクタと REST API パラメーターの概要
services: ''
suite: ''
documentationcenter: ''
author: MandiOhlinger
manager: erikre
editor: ''
tags: connectors

ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: mandia

---
# Bing Search コネクタの概要
Bing Search に接続すると、ニュース、動画など検索できます。Bing Search では、次の操作を実行できます。

* 検索から取得したデータに基づいてビジネス フローを構築することができます。
* 画像、ニュースなどを検索するアクションを使用できます。また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。たとえば、動画を検索し、Twitter を使用して Twitter フィードに動画を投稿することができます。

ロジック アプリに操作を追加する方法については、[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)に関するページを参照してください。

## トリガーとアクション
Bing Search には、次のアクションがあります。トリガーはありません。

| トリガー | アクション |
| --- | --- |
| なし |<ul><li>Web を検索する</li><li>動画を検索する</li><li>画像を検索する</li><li>ニュースを検索する</li><li>関連項目を検索する</li><li>候補を検索する</li><li>すべてを検索する</li></ul> |

すべてのコネクタは、JSON および XML 形式のデータに対応します。

## Swagger REST API リファレンス
適用されるバージョン: 1.0。

### Web を検索する
Bing Search から Web サイトを取得します。```GET: /Web```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| query |string |○ |query |なし |検索するテキスト (例: 'xbox') |
| maxResult |integer |× |query |なし |返される結果の最大数 |
| startOffset |integer |× |query |なし |スキップする結果の数 |
| adultContent |string |× |query |なし |成人向けコンテンツ フィルター。有効な値は次のとおりです。<ul><li>Off</li><li>Moderate</li><li>Strict</li></ul> |
| market |string |× |query |なし |検索を絞り込む市場または地域 (例: ja-JP) |
| longitude |number |× |query |なし |検索を絞り込む経度 (東西の座標、例: 47.603450) |
| latitude |number |× |query |なし |検索を絞り込む緯度 (南北の座標、例: -122.329696) |
| webFileType |string |× |query |なし |検索を絞り込むファイルの種類 (例: 'DOC') |

#### 応答
| Name | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### 動画を検索する
Bing Search から動画を取得します。```GET: /Video```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| query |string |○ |query |なし |検索するテキスト (例: 'xbox') |
| maxResult |integer |× |query |なし |返される結果の最大数 |
| startOffset |integer |× |query |なし |スキップする結果の数 |
| adultContent |string |× |query |なし |成人向けコンテンツ フィルター。有効な値は次のとおりです。<ul><li>Off</li><li>Moderate</li><li>Strict</li></ul> |
| market |string |× |query |なし |検索を絞り込む市場または地域 (例: ja-JP) |
| longitude |number |× |query |なし |検索を絞り込む経度 (東西の座標、例: 47.603450) |
| latitude |number |× |query |なし |検索を絞り込む緯度 (南北の座標、例: -122.329696) |
| videoFilters |string |× |query |なし |サイズ、縦横比、イベント ログ、スタイル、向き、またはそれらの組み合わせに基づいて検索をフィルター処理します。有効な値は次のとおりです。<ul><li>Duration:Short</li><li>Duration:Medium</li><li>Duration:Long</li><li>Aspect:Standard</li><li>Aspect:Widescreen</li><li>Resolution:Low</li><li>Resolution:Medium</li><li>Resolution:High</li></ul> <br/>例: 'Duration:Short+Resolution:High'<br/> |
| videoSortBy |string |× |query |なし |結果の並べ替え順。有効な値は次のとおりです。<ul><li>Date</li><li>Relevance</li></ul> <p>日付の並べ替え順序は降順です。</p> |

#### 応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### 画像を検索する
Bing Search から画像を取得します。```GET: /Image```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| query |string |○ |query |なし |検索するテキスト (例: 'xbox') |
| maxResult |integer |× |query |なし |返される結果の最大数 |
| startOffset |integer |× |query |なし |スキップする結果の数 |
| adultContent |string |× |query |なし |成人向けコンテンツ フィルター。有効な値は次のとおりです。<ul><li>Off</li><li>Moderate</li><li>Strict</li></ul> |
| market |string |× |query |なし |検索を絞り込む市場または地域 (例: ja-JP) |
| longitude |number |× |query |なし |検索を絞り込む経度 (東西の座標、例: 47.603450) |
| latitude |number |× |query |なし |検索を絞り込む緯度 (南北の座標、例: -122.329696) |
| imageFilters |string |× |query |なし |サイズ、縦横比、イベント ログ、スタイル、向き、またはそれらの組み合わせに基づいて検索をフィルター処理します。有効な値は次のとおりです。<ul><li>Size:Small</li><li>Size:Medium</li><li>Size:Large</li><li>Size:Width:[Width]</li><li>Size:Height:[Height]</li><li>Aspect:Square</li><li>Aspect:Wide</li><li>Aspect:Tall</li><li>Color:Color</li><li>Color:Monochrome</li><li>Style:Photo</li><li>Style:Graphics</li><li>Face:Face</li><li>Face:Portrait</li><li>Face:Other</li></ul><br/>例: 'Size:Small+Aspect:Square'<br/> |

#### 応答
| Name | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### ニュースを検索する
Bing Search からニュースの検索結果を取得します。```GET: /News```

| Name | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| query |string |○ |query |なし |検索するテキスト (例: 'xbox') |
| maxResult |integer |× |query |なし |返される結果の最大数 |
| startOffset |integer |× |query |なし |スキップする結果の数 |
| adultContent |string |× |query |なし |成人向けコンテンツ フィルター。有効な値は次のとおりです。<ul><li>Off</li><li>Moderate</li><li>Strict</li></ul> |
| market |string |× |query |なし |検索を絞り込む市場または地域 (例: ja-JP) |
| longitude |number |× |query |なし |検索を絞り込む経度 (東西の座標、例: 47.603450) |
| latitude |number |× |query |なし |検索を絞り込む緯度 (南北の座標、例: -122.329696) |
| newsSortBy |string |× |query |なし |結果の並べ替え順。有効な値は次のとおりです。<ul><li>Date</li><li>Relevance</li></ul> <p>日付の並べ替え順序は降順です。</p> |
| newsCategory |string |× |query | |検索を絞り込むニュースのカテゴリ (例: 'rt\_Business') |
| newsLocationOverride |string |× |query |なし |Bing の場所検出を上書きします。このパラメーターは、ja-JP 市場でのみ使用できます。入力形式は、US./<州/> (例: 'US.WA') です。 |

#### 応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### 候補を検索する
検索候補を取得します。```GET: /SpellingSuggestions```

| Name | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| query |string |○ |query |なし |検索するテキスト (例: 'xbox') |
| maxResult |integer |× |query |なし |返される結果の最大数 |
| startOffset |integer |× |query |なし |スキップする結果の数 |
| adultContent |string |× |query |なし |成人向けコンテンツ フィルター。有効な値は次のとおりです。<ul><li>Off</li><li>Moderate</li><li>Strict</li></ul> |
| market |string |× |query |なし |検索を絞り込む市場または地域 (例: ja-JP) |
| longitude |number |× |query |なし |検索を絞り込む経度 (東西の座標、例: 47.603450) |
| latitude |number |× |query |なし |検索を絞り込む緯度 (南北の座標、例: -122.329696) |

#### 応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### 関連項目を検索する
Bing Search から関連する検索結果を取得します。```GET: /RelatedSearch```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| query |string |○ |query |なし |検索するテキスト (例: 'xbox') |
| maxResult |integer |× |query |なし |返される結果の最大数 |
| startOffset |integer |× |query |なし |スキップする結果の数 |
| adultContent |string |× |query |なし |成人向けコンテンツ フィルター。有効な値は次のとおりです。<ul><li>Off</li><li>Moderate</li><li>Strict</li></ul> |
| market |string |× |query |なし |検索を絞り込む市場または地域 (例: ja-JP) |
| longitude |number |× |query |なし |検索を絞り込む経度 (東西の座標、例: 47.603450) |
| latitude |number |× |query |なし |検索を絞り込む緯度 (南北の座標、例: -122.329696) |

#### 応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### すべてを検索する
Bing Search から Web サイト、動画、画像など、すべてを取得します。```GET: /CompositeSearch```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| query |string |○ |query |なし |検索するテキスト (例: 'xbox') |
| maxResult |integer |× |query |なし |返される結果の最大数 |
| startOffset |integer |× |query |なし |スキップする結果の数 |
| adultContent |string |× |query |なし |成人向けコンテンツ フィルター。有効な値は次のとおりです。<ul><li>Off</li><li>Moderate</li><li>Strict</li></ul> |
| market |string |× |query |なし |検索を絞り込む市場または地域 (例: ja-JP) |
| longitude |number |× |query |なし |検索を絞り込む経度 (東西の座標、例: 47.603450) |
| latitude |number |× |query |なし |検索を絞り込む緯度 (南北の座標、例: -122.329696) |
| webFileType |string |× |query |なし |検索を絞り込むファイルの種類 (例: 'DOC') |
| videoFilters |string |× |query |なし |サイズ、縦横比、イベント ログ、スタイル、向き、またはそれらの組み合わせに基づいて検索をフィルター処理します。有効な値は次のとおりです。<ul><li>Duration:Short</li><li>Duration:Medium</li><li>Duration:Long</li><li>Aspect:Standard</li><li>Aspect:Widescreen</li><li>Resolution:Low</li><li>Resolution:Medium</li><li>Resolution:High</li></ul> <br/>例: 'Duration:Short+Resolution:High'<br/> |
| videoSortBy |string |× |query |なし |結果の並べ替え順。有効な値は次のとおりです。<ul><li>Date</li><li>Relevance</li></ul> <p>日付の並べ替え順序は降順です。</p> |
| imageFilters |string |× |query |なし |サイズ、縦横比、イベント ログ、スタイル、向き、またはそれらの組み合わせに基づいて検索をフィルター処理します。有効な値は次のとおりです。<ul><li>Size:Small</li><li>Size:Medium</li><li>Size:Large</li><li>Size:Width:[Width]</li><li>Size:Height:[Height]</li><li>Aspect:Square</li><li>Aspect:Wide</li><li>Aspect:Tall</li><li>Color:Color</li><li>Color:Monochrome</li><li>Style:Photo</li><li>Style:Graphics</li><li>Face:Face</li><li>Face:Portrait</li><li>Face:Other</li></ul><br/>例: 'Size:Small+Aspect:Square'<br/> |
| newsSortBy |string |× |query |なし |結果の並べ替え順。有効な値は次のとおりです。<ul><li>Date</li><li>Relevance</li></ul> <p>日付の並べ替え順序は降順です。</p> |
| newsCategory |string |× |query |なし |検索を絞り込むニュースのカテゴリ (例: 'rt\_Business') |
| newsLocationOverride |string |× |query |なし |Bing の場所検出を上書きします。このパラメーターは、ja-JP 市場でのみ使用できます。入力形式は、US./<州/> (例: 'US.WA') です。 |

#### 応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## オブジェクト定義
#### WebResultModel: Bing の Web 検索結果
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| タイトル |string |× |
| Description |string |× |
| DisplayUrl |string |× |
| ID |string |× |
| FullUrl |string |× |

#### VideoResultModel: Bing の動画検索結果
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| タイトル |string |× |
| DisplayUrl |string |× |
| ID |string |× |
| MediaUrl |string |× |
| ランタイム |integer |× |
| サムネイル |未定義 |× |

#### ThumbnailModel: マルチメディア要素のサムネイルのプロパティ
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| MediaUrl |string |× |
| ContentType |string |× |
| 幅 |integer |× |
| 高さ |integer |× |
| FileSize |integer |× |

#### ImageResultModel: Bing の画像検索結果
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| タイトル |string |× |
| DisplayUrl |string |× |
| ID |string |× |
| MediaUrl |string |× |
| SourceUrl |string |× |
| サムネイル |未定義 |× |

#### NewsResultModel: Bing のニュース検索結果
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| タイトル |string |× |
| Description |string |× |
| DisplayUrl |string |× |
| ID |string |× |
| から |string |× |
| Date |string |× |

#### SpellResultModel: Bing の候補の検索結果
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| ID |string |× |
| 値 |string |× |

#### RelatedSearchResultModel: Bing の関連項目の検索結果
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| タイトル |string |× |
| ID |string |× |
| BingUrl |string |× |

#### CompositeSearchResultModel: Bing の複合検索結果
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| WebResultsTotal |integer |× |
| ImageResultsTotal |integer |× |
| VideoResultsTotal |integer |× |
| NewsResultsTotal |integer |× |
| SpellSuggestionsTotal |integer |× |
| WebResults |array |× |
| ImageResults |array |× |
| VideoResults |array |× |
| NewsResults |array |× |
| SpellSuggestionResults |array |× |
| RelatedSearchResults |array |× |

## 次のステップ
[ロジック アプリを作成する](../app-service-logic/app-service-logic-create-a-logic-app.md)

[API リスト](apis-list.md)に戻ります。

<!---HONumber=AcomDC_0824_2016-->