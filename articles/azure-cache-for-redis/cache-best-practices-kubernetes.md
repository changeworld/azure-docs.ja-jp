---
title: Kubernetes クライアント アプリケーションをホストするためのベスト プラクティス
titleSuffix: Azure Cache for Redis
description: Azure Cache for Redis を使用する Kubernetes クライアント アプリケーションをホストする方法について説明します。
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: 5d61b7047262c86471dba988bd0f80cb4fdac8ba
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128663632"
---
# <a name="kubernetes-hosted-client-application"></a>Kubernetes でホストされるクライアント アプリケーション

## <a name="multiple-pods"></a>複数のポッド

Redis サーバーに複数のポッドを接続している場合は、ポッドからの新しい接続が段階的に作成されていることを確認します。 複数のポッドが短時間で時間差処理なく開始されると、作成されるクライアント接続数が急激に増加します。 接続数が多いと、Redis サーバーの負荷が高くなり、タイムアウトが発生する可能性があります。

同時に複数のポッドをシャットダウンする場合は、同じシナリオを避けてください。 時間差のシャットダウができないと、CPU 負荷につながる接続数が急に低下する可能性があります。

## <a name="sufficient-resources"></a>十分なリソース

Redis サーバーに接続しているポッドをホストする Kubernetes ノードに、十分なメモリ、CPU、およびネットワーク帯域幅があることを確認します。  

## <a name="noisy-neighbor-problem"></a>ノイズのある近隣の問題

*ノイズのある近隣* の問題に注意してください。 クライアントを実行しているポッドは、同じノードで実行されている他のポッドの影響を受け、Redis 接続や IO 操作を抑えます。

## <a name="next-steps"></a>次のステップ

- [開発](cache-best-practices-development.md)
- [Azure Cache for Redis 開発に関してよくあるご質問](cache-development-faq.yml)
