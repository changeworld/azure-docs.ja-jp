---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: c98d88d66e89df3efe186212d8c004ec2a65158d
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2021
ms.locfileid: "106450362"
---
| リソース | 制限 | Note |
| --- | --- | --- |
| Azure Application Gateway |サブスクリプションあたり 1,000 | |
| フロントエンド IP 構成 |2 |パブリック 1、プライベート 1 |
| フロントエンド ポート |100<sup>1</sup> | |
| バックエンド アドレス プール |100<sup>1</sup> | |
| プールあたりのバックエンド サーバーの数 |1,200 | |
| HTTP リスナー |200<sup>1</sup> |トラフィックをルーティングするアクティブなリスナーは 100 個に制限されます。 アクティブリスナー数は、リスナーの総数から非アクティブなリスナー数を差し引いた数です。<br>ルーティング規則内の既定の構成がトラフィックをルーティングするように設定されている (たとえば、リスナー、バックエンド プール、HTTP 設定がある) 場合は、それがリスナーとしてカウントされます。|
| HTTP の負荷分散規則 |100<sup>1</sup> | |
| バックエンドの HTTP 設定 |100<sup>1</sup> | |
| ゲートウェイあたりのインスタンスの数 |V1 SKU - 32<br>V2 SKU - 125 | |
| SSL 証明書の数 |100<sup>1</sup> |HTTP リスナーあたり 1 |
| 最大 SSL 証明書サイズ |V1 SKU - 10 KB<br>V2 SKU - 16 KB| |
| 認証証明書 |100 | |
| 信頼されたルート証明書 |100 | |
| 最小要求タイムアウト |1 秒 | |
| 最大要求タイムアウト |24 時間 | |
| サイトの数 |100<sup>1</sup> |HTTP リスナーあたり 1 |
| 各リスナーあたりの URL のマップの数 |1 | |
| URL マップあたりのパスベース ルールの最大数|100||
| リダイレクトの構成 |100<sup>1</sup>| |
| 書き換えルール セットの数 |400| |
| 書き換えルール セットごとのヘッダーまたは URL 構成の数|40| |
| 書き換えルール セットごとの条件の数|40| |
| コンカレント WebSocket 接続 |中規模のゲートウェイ 20k<sup>2</sup><br> 大規模のゲートウェイ 50k<sup>2</sup>| |
| URL の最大長|32KB| |
| HTTP/2 向けヘッダーの最大サイズ |4KB| |
| 最大ファイル アップロード サイズ (標準) |2 GB | |
| 最大ファイル アップロード サイズ (WAF) |中規模の V1 WAF ゲートウェイ、100 MB<br>大規模の V1 WAF ゲートウェイ、500 MB<br>V2 WAF、750 MB| |
| WAF の本文サイズの制限 (ファイルがない場合)|128 KB||
| WAF カスタム規則の最大数|100||
| Application Gateway あたりの最大 WAF 除外数|40||

<sup>1</sup> WAF 対応の SKU の場合、リソース数を 40 に制限する必要があります。

<sup>2</sup> Application Gateway リソースごとではなく、Application Gateway インスタンスごとの制限です。
