---
title: 言語サポート - Form Recognizer
titleSuffix: Azure Applied AI Services
description: Form Recognizer で利用できる人間言語について説明します。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 10/07/2021
ms.author: lajanuar
ms.openlocfilehash: b9cd899516188dbac4671addd11d9c6cd05fc843
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130216658"
---
# <a name="language-support-for-form-recognizer"></a>Form Recognizer の言語サポート

 このテーブルでは、Form Recognizer サービスでサポートされている書き言葉の一覧を掲載しています。

<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->

## <a name="layout-and-custom-model"></a>レイアウトとカスタム モデル

次の一覧では、2.1 バージョンの現在の GA 言語と、Form Recognizer の 3.0 プレビュー バージョンの新しいプレビューについて説明します。 これらの言語は、レイアウトとカスタム モデルでサポートされています。 プレビュー リリースには、現在の GA 言語に対する機能強化が含まれています。

> [!NOTE]
> **言語コード (省略可能)**
>
> Form Recognizer のディープ ラーニング ベースのユニバーサル モデルでは、言語が混在するテキスト行を含め、ドキュメント内のすべての多言語テキストが抽出されます。言語コードを指定する必要はありません。 言語について確認し、関連するモデルのみサービスを適用するように強制する場合を除き、言語コードをパラメーターとして指定しないでください。 そうしないと、サービスが不完全で不正確なテキストを返すことがあります。

レイアウトとカスタム モデルのプレビュー言語を使用するには、[v3.0 REST API 移行ガイド](/rest/api/media/#changes-to-the-rest-api-endpoints)を参照して v2.1 GA API との違いを理解し、[v3.0 プレビュー SDK のクイックスタート](quickstarts/try-v3-python-sdk.md)と[プレビュー REST API のクイックスタート](quickstarts/try-v3-rest-api.md)を確認してください。

### <a name="handwritten-languages"></a>手書きの言語
次の表は、Form Recognizer のレイアウトとカスタム モデル機能によりサポートされる手書き言語の一覧です。

|言語| 言語コード (省略可能) | プレビュー?  |
|:-----|:----:|:----:|
|英語|`en`||
|簡体中国語 |`zh-Hans`| preview
|フランス語 |`fr`| preview
|ドイツ語 |`de`| preview
|イタリア語|`it`| preview
|ポルトガル語 |`pt`| preview
|スペイン語 |`es`| preview

### <a name="print-languages"></a>印刷言語
次の表は、Form Recognizer のレイアウトとカスタム モデル機能によりサポートされる印刷言語の一覧です。

|言語| 言語コード (省略可能) | プレビュー? |
|:-----|:----:|:----:|
|アフリカーンス語|`af`||
|アルバニア語 |`sq`||
|アストリア語 |`ast`| |
|アゼルバイジャン語 (ラテン) | `az` | preview |
|バスク語  |`eu`| |
|ベラルーシ語 (キリル) | `be` | preview |
|ベラルーシ語 (ラテン) | `be` | preview |
|ビスラマ語   |`bi`| |
|ボスニア語 (ラテン)   |`bs`| preview |
|ブルトン語    |`br`| |
|ブルガリア語 |`bg`| preview |
|ブリヤート語 (キリル)|`bua`| preview |
|カタロニア語    |`ca`| |
|セブアノ語    |`ceb`| |
|チャモロ語  |`ch`| |
|簡体中国語 | `zh-Hans`| |
|中国語 (繁体字) | `zh-Hant`| |
|コーンウォール語     |`kw`| |
|コルシカ語      |`co`| |
|クリミア タタール語 (ラテン)|`crh`| |
|クロアチア語 |`hr`| preview |
|チェコ語 | `cs` | |
|デンマーク語 | `da` | |
|オランダ語 | `nl` | |
|英語 | `en` | |
|エルジャ語 (キリル) |`myv`| preview |
|エストニア語  |`et`|  |
|フェロー語 |`fo`| preview |
|フィジー語 |`fj`| |
|フィリピン語  |`fil`| |
|フィンランド語 | `fi` | |
|フランス語 | `fr` | |
|フリウリ語  | `fur` | |
|ガガウズ語 (ラテン) |`gag`| preview |
|ガリシア語   | `gl` | |
|ドイツ語 | `de` | |
|キリバス語    | `gil` | |
|グリーンランド語   | `kl` | |
|ハイチ・クレオール語  | `ht` | |
|ハニ語  | `hni` | |
|ハワイ語 |`haw`| preview |
|ミャオ語 (ラテン)| `mww` | |
|ハンガリー語 | `hu` | |
|アイスランド語 |`is`| preview |
|イナリ サーミ語 |`smn`| preview |
|インドネシア語   | `id` | |
|インターリングア  | `ia` | |
|イヌクティトット語 (ラテン) | `iu` | |
|アイルランド語    | `ga` | |
|イタリア語 | `it` | |
|日本語 | `ja` | |
|ジャワ文字 | `jv` | |
|キチェ語  | `quc` | |
|カーボベルデ・クレオール語 | `kea` | |
|カチン語 (ラテン) | `kac` | |
|カラチャイ バルカル語 |`krc`| preview |
|カラ カルパク語 (ラテン) | `kaa` | |
|カラ カルパク語 (キリル) | `kaa-cyrl` | preview |
|カシュビア語 | `csb` | |
|カザフ語 (キリル) |`kk-cyrl`| preview |
|カザフ語 (ラテン) |`kk-latn`| preview |
|カシ語  | `kha` |  |
|韓国語 | `ko` | |
|コリャーク語 |`kpy`| preview |
|コスラエ語 |`kos`| preview |
|クムク語 (キリル) |`kum`| preview |
|クルド語 (ラテン文字)| `ku` | |
|キルギス語 (キリル) |`ky`| preview |
|ラコタ語 |`lkt`| preview |
|ラテン語|`la`| preview |
|リトアニア語|`lt`| preview |
|低地ソルブ語|`dsb`| preview |
|ルレ サーミ語|`smj`| preview |
|ルクセンブルク語  | `lb` |  |
|マレー語 (ラテン) | `ms` |  |
|マルタ語|`mt`| preview |
|マン語  | `gv` |  |
|マオリ語|`mi`| preview |
|モンゴル語 (キリル)|`mn`| preview |
|モンテネグロ語 (キリル)|`cnr-cyrl`| preview |
|モンテネグロ語 (ラテン)|`cnr-latn`| preview |
|ナポリ語   | `nap` | |
|ニウエ語|`niu`| preview |
|ノガイ語|`nog`| preview |
|北部サーミ語 (ラテン)|`sme`| preview |
|ノルウェー語 | `no` |  |
|オクシタン語 | `oc` | |
|オセット語|`os`| preview |
|ポーランド語 | `pl` | |
|ポルトガル語 | `pt` | |
|リプアーリ語|`ksh`| preview |
|ルーマニア語 | `ro` | preview |
|ロマンシュ語  | `rm` | |
|ロシア語 | `ru` | preview |
|サモア語 (ラテン)|`sm`| preview |
|スコットランド語  | `sco` | |
|スコットランド ゲール語  | `gd` | |
|セルビア語 (ラテン) | `sr-latn` | preview |
|スコルト サーミ語|`sms`| preview |
|スロバキア語 | `sk` | preview |
|スロベニア語  | `sl` | |
|南部サーミ語|`sma`| preview |
|スペイン語 | `es` | |
|スワヒリ語 (ラテン)  | `sw` | |
|スウェーデン語 | `sv` | |
|タジク語 (キリル)|`tg`| preview |
|タタール語 (ラテン)  | `tt` | |
|テトゥン語    | `tet` |  |
|トンガ語|`to`|(プレビュー) |
|トルコ語 | `tr` | |
|トルクメン語 (ラテン)|`tk`| preview |
|トゥヴァ語|`tyv`| preview |
|高地ソルブ語  | `hsb` | |
|ウズベク語 (キリル)  | `uz-cyrl` |  |
|ウズベク語 (ラテン)     | `uz` |  |
|ヴォラピュク   | `vo` | |
|ヴァリス語    | `wae` |  |
|ウェールズ語     | `cy` | preview |
|西フリジア語 | `fy` |  |
|ユカテコ語 | `yua` |  |
|チワン語 | `za` | |
|ズールー語  | `zu` |  |

## <a name="receipt-and-business-card-models"></a>レシートと名刺のモデル

>[!NOTE]
 > ロケールを指定する必要はありません。 これは省略可能なパラメーターです。 Form Recognizer のディープラーニング技術によって、画像内のテキストの言語が自動的に検出されます。

事前に構築された領収書と名刺は、次のロケールですべての英語の領収書と名刺をサポートしています。

|言語| ロケール コード |
|:-----|:----:|
|英語 (オーストラリア)|`en-au`|
|英語 (カナダ)|`en-ca`|
|英語 (イギリス)|`en-gb`|
|英語 (インド)|`en-in`|
|英語 (米国)| `en-us`|

## <a name="invoice-model"></a>請求書モデル

言語| ロケール コード |
|:-----|:----:|
|英語 (米国)|ja-JP|

## <a name="id-documents"></a>身分証明書

このテクノロジは現在、米国の運転免許証と国際パスポートの経歴ページ (VISA などの旅行ドキュメントを除く) で利用できます。

> [!div class="nextstepaction"]
> [Form Recognizer を試す](https://aka.ms/fott-2.1-ga)

## <a name="general-document"></a>一般的なドキュメント

言語| ロケール コード |
|:-----|:----:|
|英語 (米国)|ja-JP|

