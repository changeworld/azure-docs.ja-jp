---
title: Azure Front Door Service - メトリックとログ | Microsoft Docs
description: この記事では、Azure Front Door Service がサポートするさまざまなメトリックとアクセス ログについて説明します
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sharadag
ms.openlocfilehash: 643ae03a9350868b172d99b2af7ce23e34fedf47
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46998000"
---
# <a name="monitoring-metrics-for-front-door"></a>Front Door のメトリックの監視

Azure Front Door Service を使用すると、主要なメトリックを監視し、Front Door の構成と共に、使用状況、正常性、およびパフォーマンス、または Front Door を検証できます。

## <a name="metrics"></a>メトリック

メトリックは特定の Azure リソース用の機能で、ポータルでパフォーマンス カウンターを表示できます。 Front Door について、次のメトリックを使用できます。

| メトリック | メトリックの表示名 | 単位 | ディメンション | 説明 |
| --- | --- | --- | --- | --- |
| RequestCount | 要求数 | Count | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door によって処理されるクライアント要求の数。  |
| RequestSize | 要求サイズ | Bytes | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door にクライアントからの要求として送信されたバイト数。 |
| ResponseSize | 応答サイズ | Bytes | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | クライアントに Front Door からの応答として送信されたバイト数。 |
| TotalLatency | 合計待機時間 | ミリ秒 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door によってクライアント要求が受信されてから、Front Door からの最後の応答バイトがクライアントで確認されるまでの算出時間。 |
| BackendRequestCount | バックエンド要求数 | Count | HttpStatus</br>HttpStatusGroup</br>バックエンド | バックエンドに Front Door から送信された要求の数。 |
| BackendRequestLatency | バックエンド要求の待機時間 | ミリ秒 | バックエンド | Front Door によってバックエンドに要求が送信されてから、Front Door でバックエンドから最後の応答バイトが受信されるまでの算出時間。 |
| BackendHealthPercentage | バックエンドの正常性の割合 | Percent | バックエンド</br>BackendPool | Front Door からバックエンドへの成功した正常性プローブの割合。 |
| WebApplicationFirewallRequestCount | Web アプリケーション ファイアウォール要求の数 | Count | PolicyName</br>RuleName</br>アクションを表示します。 | Front Door のアプリケーション レイヤー セキュリティによって処理されたクライアント要求の数。 |


## <a name="next-steps"></a>次の手順

- [Front Door の作成](quickstart-create-front-door.md)方法について説明します。
- [Front Door の機能](front-door-routing-architecture.md)について説明します。
