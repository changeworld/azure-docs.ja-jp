---
title: ローカライズのサポート | Microsoft Azure Maps
description: この記事では、Microsoft Azure Maps のサービス向けにサポートされている言語について説明します。
author: walsehgal
ms.author: v-musehg
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4926cb2bb4cb1aa15b212cc7130e0db995a24ed9
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910428"
---
# <a name="localization-support-in-azure-maps"></a>Azure Maps でのローカライズのサポート

Azure Maps は、さまざまな言語および国/地域に基づくビューをサポートします。 このアーティクルでは、サポートされる言語と、Azure Maps の実装に役立つビューを提供します。


## <a name="azure-maps-supported-languages"></a>Azure Maps でサポートされている言語

Azure Maps は、サービス全体にわたってさまざまな言語にローカライズされています。 次の表は、各サービスでサポートされている言語コードを示しています。  
  

| id         | Name                   |  マップ | 検索 | ルーティング | Weather | トラフィック インシデント | JS マップ コントロール |
|------------|------------------------|:-----:|:------:|:-------:|:--------:|:-----------------:|:--------------:|
| af-ZA      | アフリカーンス語              |       |    ✓   |    ✓    |         |                   |                |
| ar-SA      | アラビア語                 |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| bn-BD      | ベンガル語 (バングラデシュ)    |       |       |         |     ✓    |                   |                |
| bn-IN      | ベンガル語 (インド)         |       |       |         |     ✓    |                   |                |
| bs-BA      | ボスニア語                 |       |       |         |     ✓    |                   |                |
| eu-ES      | バスク語                 |       |    ✓   |         |         |                   |                |
| bg-BG      | Bulgarian              |   ✓   |    ✓   |    ✓    |     ✓     |                   |        ✓       |
| ca-ES      | カタロニア語                |       |    ✓   |         |    ✓      |                   |                |
| zh-HanS    | 簡体中国語   |       |  zh-CN |         |     zh-CN   |                   |                |
| zh-HanT    | 中国語 (香港特別行政区)  |  |   |    |    zh-HK   |                   |           |
| zh-HanT    | 中国語 (台湾)  | zh-TW |  zh-TW |  zh-TW  |    zh-TW   |                   |      zh-TW     |
| hr-HR      | Croatian               |       |    ✓   |         |    ✓      |                   |                |
| cs-CZ      | Czech                  |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| da-DK      | Danish                 |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| nl-BE      | オランダ語 (ベルギー)        |       |    ✓   |         |      ✓    |                   |                |
| nl-NL      | オランダ語 (オランダ)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-AU      | 英語 (オーストラリア)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-NZ      | 英語 (ニュージーランド)  |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-GB      | 英語 (英国) |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| ja-JP      | 英語 (米国)          |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| et-EE      | Estonian               |       |    ✓   |         |      ✓    |         ✓         |                |
| fil-PH     | フィリピン語               |       |       |         |     ✓    |                   |                |
| fi-FI      | Finnish                |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-FR      | French                 |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-CA      | フランス語 (カナダ)      |       |    ✓   |         |     ✓     |                   |                |
| gl-ES      | ガリシア語               |       |    ✓   |         |         |                   |                |
| de-DE      | German                 |   ✓   |    ✓   |    ✓    |   ✓      |         ✓         |        ✓       |
| el-GR      | Greek                  |   ✓   |    ✓   |    ✓    |    ✓     |         ✓         |        ✓       |
| gu-IN      | グジャラート語                |       |       |         |     ✓    |                   |                |
| he-IL      | ヘブライ語                 |       |    ✓   |         |     ✓    |         ✓         |                |
| hi-IN      | ヒンディー語                  |       |        |         |     ✓    |                   |                |
| hu-HU      | Hungarian              |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| is-IS      | アイスランド語              |       |       |         |     ✓    |                   |                |
| id-ID      | インドネシア語             |   ✓   |    ✓    |    ✓    |     ✓    |         ✓         |        ✓       |
| it-IT      | Italian                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| ja-JP      | Japanese               |       |        |         |     ✓    |                   |                |
| kn-IN      | カンナダ語                |       |       |         |     ✓    |                   |                |
| kk-KZ      | カザフ語                 |       |    ✓   |         |     ✓    |                   |                |
| ko-KR      | Korean                 |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| es-419     | スペイン語 (ラテンアメリカ) |       |    ✓   |         |         |                   |                |
| lv-LV      | Latvian                |       |    ✓   |         |     ✓    |         ✓         |                |
| lt-LT      | Lithuanian             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| mk-MK      | マケドニア語             |       |       |         |     ✓    |                   |                |
| ms-MY      | マレー語 (ラテン)          |   ✓   |    ✓   |    ✓    |    ✓   |                   |        ✓       |
| mr-IN      | マラーティー語                 |       |       |         |     ✓    |                   |                |
| nb-NO      | ノルウェー語 (ブークモール)       |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| NGT        | Neutral Ground Truth - 地域の書記体系 (利用可能な場合) でのすべてのリージョンに対する公用語 |   ✓     |        |         |       |        |      ✓          |
| NGT-Latn   | Neutral Ground Truth - ラテンの異名。 可能な場合はラテン文字が使用されます |   ✓     |        |         |         |                |        ✓         |
| pl-PL      | Polish                 |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| pt-BR      | ポルトガル語 (ブラジル)    |   ✓   |    ✓   |    ✓    |      ✓   |                   |        ✓       |
| pt-PT      | ポルトガル語 (ポルトガル)  |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| pa-IN      | パンジャーブ語                 |       |       |         |     ✓    |                   |                |
| ro-RO      | Romanian               |       |    ✓    |         |     ✓    |         ✓         |                |
| ru-RU      | Russian                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| sr-Cyrl-RS | セルビア語 (キリル)     |       |   sr-RS  |         |    sr-RS     |                   |                |
| sr-Latn-RS | セルビア語 (ラテン)        |       |       |         |     sr-latn    |                   |                |
| sk-SK      | Slovak             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| sl-SL      | Slovenian              |   ✓   |    ✓   |    ✓    |     ✓    |                   |        ✓       |
| es-ES      | Spanish                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| es-MX      | スペイン語 (メキシコ)       |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| sv-SE      | Swedish                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| ta-IN      | タミール語 (インド)                 |       |       |         |     ✓    |                   |                |
| te-IN      | テルグ語 (インド)                 |       |       |         |     ✓    |                   |                |
| th-TH      | Thai                   |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| tr-TR      | Turkish                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| uk-UA      | ウクライナ語               |       |    ✓   |         |     ✓    |                   |                |
| ur-PK      | ウルドゥ語                 |       |       |         |     ✓    |                   |                |
| uz-Latn-UZ | ウズベク語                 |       |       |         |     ✓    |                   |                |
| vi-VN      | ベトナム語             |       |    ✓   |         |      ✓    |                  |                |


## <a name="azure-maps-supported-views"></a>Azure Maps でサポートされているビュー

> [!Note]
> Azure Maps は、2019 年 8 月 1 日に次の国/リージョンでリリースされました。
>  * アルゼンチン
>  * インド
>  * モロッコ
>  * パキスタン
>
> 2019 年 8 月 1 日以降、**View** パラメーターの設定で、上記の新しい地域/国に対して返されるマップ コンテンツが定義されます。 必要に応じて、ご自分のサービスに使用されている REST API および SDK に View パラメーターを設定することをお勧めします。
>  
>
>  **Rest API:**
>  
>  必要に応じて View パラメーターを設定します。 View パラメーターには、Azure Maps サービスを介して返される地政学的な紛争のあるコンテンツのセットを指定します。 
>
>  影響を受ける Azure Maps REST サービス:
>    
>    * Get Map Tile
>    * Get Map Image 
>    * Get Search Fuzzy
>    * Get Search POI
>    * Get Search POI Category
>    * Get Search Nearby
>    * Get Search Address
>    * Get Search Address Structured
>    * Get Search Address Reverse
>    * Get Search Address Reverse Cross Street
>    * Post Search Inside Geometry
>    * Post Search Address Batch Preview
>    * Post Search Address Reverse Batch Preview
>    * Post Search Along Route
>    * Post Search Fuzzy Batch Preview
>
>    
>  **SDK:**
>
>  必要に応じて View パラメーターを設定し、Web SDK と Android SDK の最新バージョンを用意します。 影響を受ける SDK:
>
>    * Azure Maps Web SDK
>    * Azure Maps Android SDK


Azure Maps の **View** パラメーター ("ユーザーの地域のパラメーター" とも呼ばれます) は 2 文字の ISO-3166 の国番号であり、マップ上に表示される国境やラベルなど、その国/地域にとって正しいマップが Azure Maps サービスを介して表示されます。 

要求で定義していない場合でも、既定で View パラメーターは **Unified** に設定されています。 ユーザーの場所を特定し、その場所を正しく View パラメーターを設定するのはユーザーの責任です。 または、要求の IP アドレスに基づいてマップ データを返す 'View = Auto' を設定することもできます。  Azure Maps の View パラメーターは、Azure Maps へのアクセスを承認されたマップ、イメージ、およびその他のデータとサード パーティ コンテンツを表示できる国のマッピングに関する法律など、該当する法律に準拠していなければなりません。


次の表に、サポートされるビューを示します。

| 表示         | [説明]                            |  マップ | 検索 | JS マップ コントロール |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | アラブ首長国連邦、AE    |   ✓   |        |     ✓          |
| AR           | アルゼンチン (アルゼンチン ビュー)           |   ✓   |    ✓   |     ✓          |
| BH           | バーレーン (アラビアのビュー)                 |   ✓   |        |     ✓          |
| IN           | インド (インド ビュー)                    |   ✓   |   ✓     |     ✓          |
| IQ           | イラク (アラビアのビュー)                    |   ✓   |        |     ✓          |
| JO           | ヨルダン (アラビアのビュー)                  |   ✓   |        |     ✓          |
| KW           | クウェート (アラビアのビュー)                  |   ✓   |        |     ✓          |
| LB           | レバノン (アラビアのビュー)                 |   ✓   |        |     ✓          |
| MA           | モロッコ (モロッコのビュー)                |   ✓   |   ✓     |     ✓          |
| OM           | オマーン (アラビアのビュー)                    |   ✓   |        |     ✓          |
| PK           | パキスタン (パキスタンのビュー)              |   ✓   |    ✓    |     ✓          |
| PS           | パレスチナ自治政府 (アラビアのビュー)    |   ✓   |        |     ✓          |
| QA           | カタール (アラビアのビュー)                   |   ✓   |        |     ✓          |
| SA           | サウジアラビア (アラビアのビュー)            |   ✓   |        |     ✓          |
| SY           | シリア (アラビアのビュー)                   |   ✓   |        |     ✓          |
| YE           | イエメン (アラビアのビュー)                   |   ✓   |        |     ✓          |
| Auto         | 要求の IP アドレスに基づいてマップ データを返します。|   ✓   |    ✓   |     ✓          |
| 統一      | 統一されたビュー (その他)                  |   ✓   |   ✓     |     ✓          |
