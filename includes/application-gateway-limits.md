---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: 0ba21fe3789fba03cd4814d19fb103c3a2559c13
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2020
ms.locfileid: "75662906"
---
| リソース | 既定/上限 | Note |
| --- | --- | --- |
| Azure Application Gateway |サブスクリプションあたり 1,000 | |
| フロントエンド IP 構成 |2 |パブリック 1、プライベート 1 |
| フロントエンド ポート |100<sup>1</sup> | |
| バックエンド アドレス プール |100<sup>1</sup> | |
| プールあたりのバックエンド サーバーの数 |1,200 | |
| HTTP リスナー |100<sup>1</sup> | |
| HTTP の負荷分散規則 |100<sup>1</sup> | |
| バックエンドの HTTP 設定 |100<sup>1</sup> | |
| ゲートウェイあたりのインスタンスの数 |32 | |
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
| コンカレント WebSocket 接続 |中規模のゲートウェイ 20k<br> 大規模のゲートウェイ 50k| |
| URL の最大長|32KB| |
| HTTP/2 向けヘッダーの最大サイズ |4KB| |
| 最大ファイル アップロード サイズ (標準) |2 GB | |
| 最大ファイル アップロード サイズ (WAF) |中規模の v1 WAF ゲートウェイ、100 MB<br>大規模の v1 WAF ゲートウェイ、500 MB<br>v2 WAF、750 MB| |
| WAF の本文サイズの制限 (ファイルがない場合)|128 KB||
| WAF カスタム規則の最大数|100||
| WAF 除外の最大数|100||

<sup>1</sup> WAF 対応 SKU の場合は、最適なパフォーマンスを確保するためにリソース数を 40 に制限することをお勧めします。
