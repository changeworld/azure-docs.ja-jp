---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 09/09/2021
ms.author: victorh
ms.openlocfilehash: f106ca51487e1c4c306d51161110062ad3582fcd
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2021
ms.locfileid: "129440005"
---
| リソース | 制限 | Note |
| --- | --- | --- |
| Azure Application Gateway |サブスクリプションあたり 1,000 | |
| フロントエンド IP 構成 |2 |パブリック 1、プライベート 1 |
| フロントエンド ポート |100<sup>1</sup> | |
| バックエンド アドレス プール |100<sup>1</sup> | |
| プールあたりのバックエンド サーバーの数 |1,200 | |
| HTTP リスナー |200<sup>1</sup> |トラフィックをルーティングするアクティブなリスナーは 100 個に制限されます。 アクティブリスナー数は、リスナーの総数から非アクティブなリスナー数を差し引いた数です。<br>ルーティング規則内の既定の構成がトラフィックをルーティングするように設定されている (たとえば、リスナー、バックエンド プール、HTTP 設定がある) 場合は、それがリスナーとしてカウントされます。 詳細については、「[Application Gateway に関してよく寄せられる質問](../articles/application-gateway/application-gateway-faq.yml#what-is-considered-an-active-listener-versus-inactive-listener)」を参照してください。|
| HTTP の負荷分散規則 |400<sup>1</sup> | |
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
| HTTP/2 向けヘッダーの最大サイズ |16 KB| |
| ファイル アップロードの最大サイズ (Standard SKU) |V2 - 4 GB<br>V1 - 2 GB | |
| ファイル アップロードの最大サイズ (WAF SKU) |V1 中規模 - 100 MB<br>V1 大規模 - 500 MB<br>V2 - 750 MB<br>V2 (CRS 3.2 以降を使用) - 4 GB| |
| WAF の本文サイズの制限 (ファイルがない場合)|V1 または V2 (CRS 3.1 以前を使用) - 128 KB<br>V2 (CRS 3.2 以降を使用) - 2 MB| |
| WAF カスタム規則の最大数|100||
| Application Gateway あたりの最大 WAF 除外数|40||

<sup>1</sup> WAF 対応の SKU の場合、リソース数を 40 に制限する必要があります。

<sup>2</sup> Application Gateway リソースごとではなく、Application Gateway インスタンスごとの制限です。
