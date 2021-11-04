---
title: リージョン別の Azure CDN の POP の場所 | Microsoft Docs
description: この記事では、Azure CDN 製品に対する Azure CDN の POP の場所をリージョン別に示します。
services: cdn
documentationcenter: ''
author: sohamnchatterjee
manager: danielgi
editor: sohamnchatterjee
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2021
ms.author: duau
ms.custom: references_regions
ms.openlocfilehash: c7d1c51bfe56f6fc5b9109f38019f902deff8b27
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461458"
---
# <a name="azure-cdn-coverage-by-metro"></a>都市別の Azure CDN のカバレッジ 
> [!div class="op_single_selector"]
> * <bpt id="p1">[</bpt>リージョン別の POP の場所<ept id="p1">](cdn-pop-locations.md)</ept>
> * <bpt id="p1">[</bpt>略称別の Verizon POP の場所<ept id="p1">](cdn-pop-abbreviations.md)</ept>
> * <bpt id="p1">[</bpt>略称別の Microsoft POP の場所<ept id="p1">](microsoft-pop-abbreviations.md)</ept>
>


この記事では、Azure Content Delivery Network (CDN) 製品に対するポイント オブ プレゼンス (POP) の場所を含む現在の都市をリージョン別に示します。 各都市には複数の POP が含まれる場合があります。 たとえば、Microsoft の Azure CDN には 100 の都市に 118 の POP があります。 

> [!IMPORTANT]
> 各 Azure CDN 製品がそれぞれの異なる方法で CDN インフラストラクチャを構築するため、どの Azure CDN 製品を使うかを決めるときは、POP の場所を使用しないことをお勧めします。 代わりに、その機能とエンドユーザー パフォーマンスを考慮する必要があります。 各 Azure CDN 製品でパフォーマンスをテストして、ユーザーに適した製品を選択してください。 
> 

## <a name="microsoft"></a>Microsoft

[!INCLUDE [front-door-edge-location](../../includes/front-door-edge-locations.md)]

## <a name="partners"></a>パートナー

> [!IMPORTANT]
> <bpt id="p1">**</bpt>Azure CDN from Akamai<ept id="p1">**</ept> の POP の都市の場所は、個別に開示されていません。  
> 

| Region | Verizon | Akamai |
|--|--|--|
| 北米 | グアダラハラ (メキシコ)<br />メキシコシティ (メキシコ)<br />プエブラ (メキシコ)<br />ケレタロ (メキシコ)<br />アトランタ (米国ジョージア州)<br />ボストン (米国マサチューセッツ州)<br />シカゴ (米国イリノイ州)<br />ダラス (米国テキサス州)<br />デンバー (米国コロラド州)<br />デトロイト (米国ミシガン州)<br />ロサンゼルス (米国カリフォルニア州)<br />マイアミ (米国フロリダ州)<br />ニューヨーク (米国ニューヨーク州)<br />フィラデルフィア (米国ペンシルバニア州)<br />サンホセ (米国カリフォルニア州)<br />シアトル (米国ワシントン州)<br />ワシントン DC (米国) <br /> アッシュバーン (米国バージニア州) <br /> フェニックス (米国アリゾナ州) | Canada<br />メキシコ<br />米国 |
| 南アメリカ | ブエノスアイレス (アルゼンチン)<br />リオデジャネイロ (ブラジル)<br />サンパウロ (ブラジル)<br />バルパライソ (チリ)<br />ボゴタ (コロンビア)<br />バランキヤ (コロンビア)<br />メデジン (コロンビア)<br />キト (エクアドル)<br />リマ (ペルー) | アルゼンチン<br />ブラジル<br />チリ<br />コロンビア<br />エクアドル<br />ペルー<br />ウルグアイ |
| ヨーロッパ | ウィーン (オーストリア)<br />コペンハーゲン (デンマーク)<br />ヘルシンキ (フィンランド)<br />マルセイユ (フランス)<br />パリ (フランス)<br />フランクフルト (ドイツ)<br />ミラノ (イタリア)<br />リガ (ラトビア)<br />アムステルダム (オランダ)<br />ワルシャワ (ポーランド)<br />マドリード (スペイン)<br />スウェーデン、ストックホルム<br />ロンドン (英国) <br /> マンチェスター (英国) | オーストリア<br />ブルガリア<br />デンマーク<br />フィンランド<br />フランス<br />ドイツ<br />ギリシャ<br />アイルランド<br />イタリア<br />オランダ<br />ポーランド<br />ロシア<br />スペイン<br />スウェーデン<br />スイス<br />イギリス |
| アフリカ | ヨハネスバーグ (南アフリカ) <br/> ナイロビ (ケニア) | 南アフリカ |
| 中東 | マスカット (オマーン)<br />フジャイラ (アラブ首長国連邦) | カタール<br />アラブ首長国連邦 |
| インド | ベンガルール (バンガロール) (インド)<br />チェンナイ (インド)<br />ムンバイ (インド)<br />ニュー デリー (インド)<br /> | インド |
| アジア | 香港特別行政区<br />ジャカルタ (インドネシア)<br />大阪 (日本)<br />東京 (日本)<br />シンガポール<br />高雄 (台湾)<br />台北 (台湾) <br />マニラ ( フィリピン) | 香港特別行政区<br />インドネシア<br />イスラエル<br />日本<br />マカオ<br />マレーシア<br />フィリピン<br />シンガポール<br />韓国<br />台湾<br />タイ<br />トルコ<br />ベトナム |
| オーストラリアとニュージーランド | メルボルン (オーストラリア)<br />シドニー (オーストラリア)<br />オークランド (ニュージーランド) | オーストラリア<br />ニュージーランド |

## <a name="next-steps"></a>次のステップ

* 許可リスト用の最新 IP アドレスを取得するには、<bpt id="p1">[</bpt>Azure CDN エッジ ノード API<ept id="p1">](/rest/api/cdn/edge-nodes/list)</ept> に関するページをご覧ください。
