---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: victorh
ms.openlocfilehash: 44f9400a1ecebd3c204ec0b891c5d7fe01bdfd25
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2018
ms.locfileid: "51628164"
---
| リソース | 既定の制限 | Note |
| --- | --- | --- |
| Application Gateway |サブスクリプションあたり 1,000 | |
| フロントエンド IP の構成の数 |2 |パブリック 1、プライベート 1 |
| フロントエンド ポートの数 |40 | |
| バックエンド アドレス プールの数 |40 | |
| プールあたりのバックエンド サーバーの数 |1200 | |
| HTTP リスナーの数 |40 | |
| HTTP の負荷分散規則 |400 |HTTP リスナー数 * n |
| バックエンドの HTTP 設定の数 |40 | |
| ゲートウェイあたりのインスタンスの数 |75 | |
| SSL 証明書の数 |40 |HTTP リスナーあたり 1 |
| 認証証明書 |40 | |
| 要求のタイムアウト (最小) |1 秒 | |
| 要求のタイムアウト (最大) |24 時間 | |
| サイトの数 |20 |HTTP リスナーあたり 1 |
| 各リスナーあたりの URL のマップの数 |1 | |
| URL マップあたりのパスベース ルールの最大数|100|
| リダイレクトの構成 |40| |
| 同時実行の WebSocket 接続 |5000| |
|URL の最大長|8000|
| 最大ファイル アップロード サイズ (標準) |2 GB | |
| 最大ファイル アップロード サイズ (WAF) |中規模の WAF ゲートウェイ - 100 MB<br>大規模の WAF ゲートウェイ - 500 MB| |
|WAF の本文サイズの制限 (ファイルがない場合)|128 KB|
