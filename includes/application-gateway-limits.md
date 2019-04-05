---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: victorh
ms.openlocfilehash: 77a391cc661ed33f5888d2b18cb9c5db16498cd6
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554239"
---
| リソース | 既定の制限 | Note |
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
| 認証証明書 |100 | |
| 信頼されたルート証明書 |100 | |
| 最小要求タイムアウト |1 秒 | |
| 最大要求タイムアウト |24 時間 | |
| サイトの数 |100<sup>1</sup> |HTTP リスナーあたり 1 |
| 各リスナーあたりの URL のマップの数 |1 | |
| URL マップあたりのパスベース ルールの最大数|100||
| リダイレクトの構成 |100<sup>1</sup>| |
| コンカレント WebSocket 接続 |5,000| |
| URL の最大長|8,000||
| 最大ファイル アップロード サイズ (標準) |2 GB | |
| 最大ファイル アップロード サイズ (WAF) |中規模の WAF ゲートウェイ - 100 MB<br>大規模の WAF ゲートウェイ - 500 MB| |
| WAF の本文サイズの制限 (ファイルがない場合)|128 KB||

<sup>1</sup> WAF 対応 SKU の場合は、最適なパフォーマンスを確保するためにリソース数を 40 に制限することをお勧めします。
