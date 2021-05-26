---
title: 言語サポート - Form Recognizer
titleSuffix: Azure Applied AI Services
description: Form Recognizer で利用できる人間言語について説明します。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 05/10/2021
ms.author: lajanuar
ms.openlocfilehash: c1a8303b0534cfe0398a7e40aa476d320dd61bc5
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110462872"
---
# <a name="language-support-for-form-recognizer"></a>Form Recognizer の言語サポート

 このテーブルでは、Form Recognizer サービスでサポートされている書き言葉の一覧を掲載しています。

<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->

## <a name="layout-and-custom-model"></a>レイアウトとカスタム モデル

### <a name="v21"></a>[v2.1](#tab/v2-1)

|Language| 言語コード |
|:-----|:----:|
|アフリカーンス語|`af`|
|アルバニア語 |`sq`|
|アストリア語 |`ast`|
|バスク語  |`eu`|
|ビスラマ語   |`bi`|
|ブルトン語    |`br`|
|カタロニア語    |`ca`|
|セブアノ語    |`ceb`|
|チャモロ語  |`ch`|
|簡体中国語 | `zh-Hans`|
|繁体中国語 | `zh-Hant`|
|コーンウォール語     |`kw`|
|コルシカ語      |`co`|
|クリミア タタール語 (ラテン)  |`crh`|
|チェコ語 | `cs` |
|デンマーク語 | `da` |
|オランダ語 | `nl` |
|英語 (印刷および手書き) | `en` |
|エストニア語  |`et`|
|フィジー語 |`fj`|
|フィリピン語  |`fil`|
|フィンランド語 | `fi` |
|フランス語 | `fr` |
|フリウリ語  | `fur` |
|ガリシア語   | `gl` |
|ドイツ語 | `de` |
|キリバス語    | `gil` |
|グリーンランド語   | `kl` |
|ハイチ・クレオール語  | `ht` |
|ハニ語  | `hni` |
|ミャオ語 (ラテン) | `mww` |
|ハンガリー語 | `hu` |
|インドネシア語   | `id` |
|インターリングア  | `ia` |
|イヌクティトット語 (ラテン)  | `iu`  |
|アイルランド語    | `ga` |
|イタリア語 | `it` |
|日本語 | `ja` |
|ジャワ文字 | `jv` |
|キチェ語  | `quc` |
|カーボベルデ・クレオール語 | `kea` |
|カチン語 (ラテン) | `kac` |
|カラ・カルパク語 | `kaa` |
|カシュビア語 | `csb` |
|カシ語  | `kha` |
|韓国語 | `ko` |
|クルド語 (ラテン) | `kur` |
|ルクセンブルク語  | `lb` |
|マレー語 (ラテン)  | `ms` |
|マン語  | `gv` |
|ナポリ語   | `nap` |
|ノルウェー語 | `no` |
|オクシタン語 | `oc` |
|ポーランド語 | `pl` |
|ポルトガル語 | `pt` |
|ロマンシュ語  | `rm` |
|スコットランド語  | `sco` |
|スコットランド ゲール語  | `gd` |
|スロベニア語  | `slv` |
|スペイン語 | `es` |
|スワヒリ語 (ラテン)  | `sw` |
|スウェーデン語 | `sv` | 
|タタール語 (ラテン)  | `tat` |
|テトゥン語    | `tet` |
|トルコ語 | `tr` |
|高地ソルブ語  | `hsb` |
|ウズベク語 (ラテン)     | `uz` |
|ヴォラピュク   | `vo` |
|ヴァリス語    | `wae` |
|西フリジア語 | `fy` |
|ユカテコ語 | `yua` |
|チワン語 | `za` |
|ズールー語  | `zu` |

### <a name="v20"></a>[v2.0](#tab/v2-0)

|Language| 言語コード |
|:-----|:----:|
簡体中国語 | `zh-Hans`|
|オランダ語 | `nl` |
|英語 (印刷および手書き) | `en` |
|フランス語 | `fr` |
|ドイツ語 | `de` |
|イタリア語 | `it` |
|日本語 | `ja` |
|Portuguese | `pt` |
|スペイン語 | `es` |

-----

## <a name="prebuilt-receipt-and-business-card"></a>事前構築された領収書と名刺

>[!NOTE]
 > ロケールを指定する必要はありません。 これは省略可能なパラメーターであり、Form Recognizer のディープラーニング技術によって、画像内のテキストの言語が自動的に検出されます。

### <a name="v21"></a>[v2.1](#tab/v2-1)

事前に構築された領収書と名刺は、次のロケールですべての英語の領収書と名刺をサポートしています。

|言語| ロケール コード |
|:-----|:----:|
|英語 (オーストラリア)|`en-au`|
|英語 (カナダ)|`en-ca`|
|英語 (イギリス)|`en-gb`|
|英語 (インド)|`en-in`|
|英語 (米国)| `en-us`|

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!IMPORTANT]
> この機能は、選択した API バージョンでは使用できません。

-----

## <a name="prebuilt-invoice"></a>事前構築済みの請求書

### <a name="v21"></a>[v2.1](#tab/v2-1)

言語| ロケール コード |
|:-----|:----:|
|英語 (米国)|ja-JP|

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!IMPORTANT]
> この機能は、選択した API バージョンでは使用できません。

-----

## <a name="prebuilt-identity-documents"></a>事前構築済みの ID ドキュメント

### <a name="v21"></a>[v2.1](#tab/v2-1)

> [!NOTE]
> このテクノロジは現在、米国の運転免許証と国際パスポートの経歴ページ (VISA などの旅行ドキュメントを除く) で利用できます。

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!IMPORTANT]
> この機能は、選択した API バージョンでは使用できません。

-----

> [!div class="nextstepaction"]
> [Form Recognizer を試す](https://aka.ms/fott-2.1-ga)
