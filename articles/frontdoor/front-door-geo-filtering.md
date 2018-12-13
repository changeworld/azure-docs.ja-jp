---
title: Azure Front Door Service のドメインに対する geo フィルタリング | Microsoft Docs
description: この記事では、Azure Front Door Service の geo フィルタリング ポリシーについて説明します。
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/09/2018
ms.author: sharadag
ms.openlocfilehash: a2ba0fb34dd34129a134aa2639d06816f3523408
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52865507"
---
# <a name="geo-filtering-geographic-based-access-control-to-azure-front-door-service-frontends"></a>geo フィルタリング: Azure Front Door Service フロントエンドに対する地理ベースのアクセスの制御

既定では、Azure Front Door Service は、要求を行っているユーザーの場所に関係なくユーザーの要求に応答します。 ただし、場合によっては、国ごとに Web アプリケーションへのアクセスを制限しなければならないことも考えられます。 Azure Front Door のアプリケーション レイヤー セキュリティでは、エンドポイントの特定のパスに対するカスタム保護ルールを使用してポリシーを定義することにより、指定した国からのアクセスを許可したりブロックしたりすることができます。 

アプリケーション セキュリティ ポリシーには通常、一連のカスタム ルールが含まれています。 ルールは、一致条件、アクション、優先順位で構成されます。 一致条件には、一致の変数、演算子、一致の値を定義します。  geo フィルタリング ルールの場合、一致変数は REMOTE_ADDR で、演算子は GeoMatch、値は対象となる 2 文字の国別コードです。 GeoMatch 条件と REQUEST_URI 文字列の一致条件を組み合わせて、パス ベースの geo フィルタリング ルールを作成することができます。

Front Door の geo フィルタリング ポリシーは、[Azure PowerShell](front-door-tutorial-geo-filtering.md) を使用するか、または Microsoft から提供されている[クイック スタート テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering)を使用して構成できます。

## <a name="country-code-reference"></a>国別コード リファレンス

|国番号 | 国名 |
| ----- | ----- |
| AD | アンドラ |
| AE | アラブ首長国連邦|
| AF | アフガニスタン|
| AG | アンティグア・バーブーダ|
| AL | アルバニア|
| AM | アルメニア|
| AO | アンゴラ|
| AR | アルゼンチン|
| AS | アメリカ領サモア|
| AT | オーストリア|
| AU | オーストラリア|
| AZ | アゼルバイジャン|
| BA | ボスニア・ヘルツェゴビナ|
| BB | バルバドス|
| BD | バングラデシュ|
| BE | ベルギー|
| BF | ブルキナファソ|
| BG | ブルガリア|
| BH | バーレーン|
| BI | ブルンジ|
| BJ | ベナン|
| BL | サンバルテルミー|
| BN | ブルネイ・ダルサラーム|
| BO | ボリビア|
| BR | ブラジル|
| BS | バハマ|
| BT | ブータン|
| BW | ボツワナ|
| BY | ベラルーシ|
| BZ | ベリーズ|
| CA | カナダ|
| CD | コンゴ民主共和国|
| CF | 中央アフリカ共和国|
| CH | スイス|
| CI | コートジボワール|
| CL | チリ|
| CM | カメルーン|
| CN | 中国|
| CO | コロンビア|
| CR | コスタリカ|
| CU | キューバ|
| CV | カーボベルデ|
| CY | キプロス|
| CZ | チェコ共和国|
| DE | ドイツ|
| DK | デンマーク|
| DO | ドミニカ共和国|
| DZ | アルジェリア|
| EC | エクアドル|
| EE | エストニア|
| EG | エジプト|
| ES | スペイン|
| ET | エチオピア|
| FI | フィンランド|
| FJ | フィジー|
| FM | ミクロネシア連邦|
| FR | フランス|
| GB | イギリス|
| GE | ジョージア|
| GF | 仏領ギアナ|
| GH | ガーナ|
| GN | ギニア|
| GP | グアドループ|
| GR | ギリシャ|
| GT | グアテマラ|
| GY | ガイアナ|
| HK | 香港特別行政区|
| HN | ホンジュラス|
| HR | クロアチア|
| HT | ハイチ|
| HU | ハンガリー|
| ID | インドネシア|
| IE | アイルランド|
| IL | イスラエル|
| IN | インド|
| IQ | イラク|
| IR | イラン・イスラム共和国|
| IS | アイスランド|
| IT | イタリア|
| JM | ジャマイカ|
| JO | ヨルダン|
| JP | 日本|
| KE | ケニア|
| KG | キルギス|
| KH | カンボジア|
| KI | キリバス|
| KN | セントクリストファー・ネイビス|
| KP | 朝鮮民主主義人民共和国|
| KR | 韓国|
| KW | クウェート|
| KY | ケイマン諸島|
| KZ | カザフスタン|
| LA | ラオス人民民主共和国|
| LB | レバノン|
| LI | リヒテンシュタイン|
| LK | スリランカ|
| LR | リベリア|
| LS | レソト|
| LT | リトアニア|
| LU | ルクセンブルク|
| LV | ラトビア|
| LY | リビア|
| MA | モロッコ|
| MD | モルドバ|
| MG | マダガスカル|
| MK | マケドニア|
| ML | マリ|
| MM | ミャンマー|
| MN | モンゴル|
| MO | マカオ|
| MQ | マルティニーク|
| MR | モーリタニア|
| MT | マルタ|
| MV | モルディブ|
| MW | マラウイ|
| MX | メキシコ|
| MY | マレーシア|
| MZ | モザンビーク|
| NA | ナミビア|
| NE | ニジェール|
| NG | ナイジェリア|
| NI | ニカラグア|
| NL | オランダ|
| NO | ノルウェー|
| NP | ネパール|
| NR | ナウル|
| NZ | ニュージーランド|
| OM | オマーン|
| PA | パナマ|
| PE | ペルー|
| PH | フィリピン|
| PK | パキスタン|
| PL | ポーランド|
| PR | プエルトリコ|
| PT | ポルトガル|
| PW | パラオ|
| PY | パラグアイ|
| QA | カタール|
| RE | レユニオン|
| RO | ルーマニア|
| RS | セルビア|
| RU | ロシア|
| RW | ルワンダ|
| SA | サウジアラビア|
| SD | スーダン|
| SE | スウェーデン|
| SG | シンガポール|
| SI | スロベニア|
| SK | スロバキア|
| SN | セネガル|
| SO | ソマリア|
| SR | スリナム|
| SS | 南スーダン|
| SV | エルサルバドル|
| SY | シリア・アラブ共和国|
| SZ | スワジランド|
| TC | タークス・カイコス諸島|
| TG | トーゴ|
| TH | タイ|
| TN | チュニジア|
| TR | トルコ|
| TT | トリニダード・トバゴ|
| TW | 台湾|
| TZ | タンザニア|
| UA | ウクライナ|
| UG | ウガンダ|
| US | 米国|
| UY | ウルグアイ|
| UZ | ウズベキスタン|
| VC | セントビンセント及びグレナディーン諸島|
| VE | ベネズエラ|
| VG | イギリス領ヴァージン諸島|
| VI | アメリカ領ヴァージン諸島|
| VN | ベトナム|
| ZA | 南アフリカ|
| ZM | ザンビア|
| ZW | ジンバブエ|

## <a name="next-steps"></a>次の手順

- [Front Door によるアプリケーション層セキュリティ](front-door-application-security.md)について学習します。
- [フロント ドアの作成](quickstart-create-front-door.md)方法について学習します。
