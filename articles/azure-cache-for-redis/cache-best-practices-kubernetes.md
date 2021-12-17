---
title: Kubernetes クライアント アプリケーションをホストするためのベスト プラクティス
titleSuffix: Azure Cache for Redis
description: Azure Cache for Redis を使用する Kubernetes クライアント アプリケーションをホストする方法について説明します。
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 10/11/2021
ms.author: shpathak
ms.openlocfilehash: 33628fc16dfc2693cad0f8cd13f555cc10e55f25
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2021
ms.locfileid: "129808739"
---
# <a name="kubernetes-hosted-client-application"></a>Kubernetes でホストされるクライアント アプリケーション

## <a name="client-connections-from-multiple-pods"></a>複数のポッドからのクライアント接続

Redis サーバーに複数のポッドを接続している場合は、ポッドからの新しい接続が段階的に作成されていることを確認します。 複数のポッドが短時間で時間差処理なく開始されると、作成されるクライアント接続数が急激に増加します。 接続数が多いと、Redis サーバーの負荷が高くなり、タイムアウトが発生する可能性があります。

同時に複数のポッドをシャットダウンする場合は、同じシナリオを避けてください。 時間差のシャットダウができないと、CPU 負荷につながる接続数が急に低下する可能性があります。

## <a name="sufficient-pod-resources"></a>十分なポッド リソース

クライアント アプリケーションを実行しているポッドに十分な CPU リソースとメモリ リソースが割り当てられていることを確認してください。 クライアント アプリケーションがリソースの限界に近い状態で動作していると、タイムアウトが発生する可能性があります。

## <a name="sufficient-node-resources"></a>十分なノード リソース

クライアント アプリケーションを実行しているポッドは、同じノードで実行されている他のポッドの影響を受け、Redis 接続や IO 操作を抑えます。 そのため、クライアント アプリケーショのポッドが動作するノードに十分なメモリ、CPU、ネットワーク帯域幅が確保されている必要があります。 これらのリソースのいずれかが不足すると、接続の問題が発生する可能性があります。

## <a name="linux-hosted-client-applications-and-tcp-settings"></a>Linux でホストされるクライアント アプリケーションと TCP 設定

Azure Cache for Redis クライアント アプリケーションが Linux ベースのコンテナーで実行されている場合、[Linux でホストされるクライアント アプリケーションの TCP 設定](cache-best-practices-connection.md#tcp-settings-for-linux-hosted-client-applications)に関するセクションで詳しく説明しているように、いくつかの TCP 設定を更新することをお勧めします。

## <a name="next-steps"></a>次の手順

- [開発](cache-best-practices-development.md)
- [Azure Cache for Redis 開発に関してよくあるご質問](cache-development-faq.yml)
