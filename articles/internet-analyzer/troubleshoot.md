---
title: Azure Internet Analyzer のトラブルシューティング | Microsoft Docs
description: Azure Internet Analyzer のトラブルシューティング リファレンス。
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: 74bf0422bbe6c2c1c84365c1e8f9329a01ff9fdd
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2020
ms.locfileid: "75909034"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Azure Internet Analyzer のトラブルシューティング

この記事では、Internet Analyzer に関する一般的な問題のトラブルシューティング手順について説明します。

## <a name="azure-portal"></a>Azure portal
**スコアカード セクションでの「スコア カードに対して十分な測定が収集されていない」**

以下の点に注意してください。
- クライアント スクリプトは、**HTTPS** Web サイトに埋め込む必要があります。 スクリプトがプレーンテキスト (**http://** ) またはローカル (**file://** ) の Web サイトで実行されている場合、測定値は収集されません。
- 測定データは、インターネットアナライザー プロファイルのクライアント　スクリプトが、実際のユーザー トラフィックを受信するアプリケーションに埋め込まれている場合にのみ収集されます。 合成トラフィック (たとえば、Azure WebApp パフォーマンス テスト) では、通常、埋め込みの Javascript コードは実行されないため、この種類のトラフィックによって測定は生成されません。
- 時系列は、1時間に1回生成されるため、新しい測定データを表示するには、少なくともその時間を待つ必要があります。
- スコアカードは、毎日 (毎日、UTC 時間の最後に) 生成されます。
- スコアカードは選択したフィルターの組み合わせ (テスト、期間、国など) に対して100を超える測定値が収集された場合にのみ生成されます。

## <a name="next-steps"></a>次のステップ
[Internet Analyzer の FAQ](internet-analyzer-faq.md) を読む
