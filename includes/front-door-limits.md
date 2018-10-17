---
title: インクルード ファイル
description: インクルード ファイル
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 9/17/2018
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: f0c2d1501b9aa19dec8c4ad157e004a57e0e5070
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47006516"
---
| リソース | 既定の制限 |
| --- | --- |
| サブスクリプションあたりの Front Door リソース数 | 100 |
| リソースあたりのカスタム ドメインを含むフロントエンド ホスト数 | 100 |
| リソースあたりのルーティング規則数 | 100 |
| リソースあたりのバックエンド プール数 | 50 |
| バックエンド プールあたりのバックエンド数 | 100 |
| ルーティング規則に関して一致するパス パターン数 | 25 |
| ポリシーあたりのカスタム Web アプリケーション ファイアウォール規則数 | 10 |
| リソースあたりの Web アプリケーション ファイアウォール ポリシー数 | 100 |

### <a name="timeout-values"></a>タイムアウト値
#### <a name="client-to-front-door"></a>クライアントから Front Door
- Front Door のアイドル TCP 接続タイムアウトは 61 秒です。
#### <a name="front-door-to-application-backend"></a>Front Door からアプリケーション バックエンド
- 応答がチャンク応答の場合、先頭のチャンクを受信したときに 200 が返されます。
- HTTP 要求がバックエンドに転送された後、Front Door はバックエンドからの最初のパケットを 30 秒間待ってから、503 エラーをクライアントに返します。
- バックエンドからの最初のパケットを受信した後、Front Door は 30 秒間 (アイドル タイムアウト) 待ってから、503 エラーをクライアントに返します。
- Front Door からバックエンド TCP セッションへのタイムアウトは、30 分です。

### <a name="upload--download-data-limit"></a>アップロード/ダウンロード データ制限

|  | チャンク転送エンコーディング (CTE) あり | HTTP チャンクなし |
| ---- | ------- | ------- |
| **ダウンロード** | ダウンロード サイズに制限はありません | ダウンロード サイズに制限はありません |
| **アップロード** |  各 CTE アップロードが 28.6 MB 未満である限り、制限はありません | サイズは、28.6 MB 以下である必要があります。 |
