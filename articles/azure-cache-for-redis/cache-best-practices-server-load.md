---
title: サーバー負荷の使用と監視に関するベスト プラクティス
titleSuffix: Azure Cache for Redis
description: Azure Cache for Redis のサーバー負荷を使用して監視する方法について説明します。
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: 51a0a5ede1c9d978fcc7eea98c7519c70bd9126e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128626135"
---
# <a name="manage-server-load-for-azure-cache-for-redis"></a>Azure Cache for Redis のサーバー負荷を管理する

## <a name="value-sizes"></a>値のサイズ

クライアント アプリケーションの設計によって、多数の小さな値を格納したほうがよいか、少数のより大きな値を格納したほうがよいかが決まります。 Redis サーバーの観点からは、値が小さいほど、パフォーマンスは向上します。 値のサイズは 100 kB 未満にしておくことをお勧めします。

設計上、Azure Cache for Redis に大きな値を格納する必要がある場合は、サーバーの負荷が高くなります。 このケースでは、CPU の使用によってスループットが制限されないように、より高いキャッシュ層を使用する必要がある場合があります。

キャッシュに十分な CPU 容量がある場合でも、より大きな値を指定すると待ち時間が長くなってしまうため、「[適切なタイムアウトを設定する](cache-best-practices-connection.md#configure-appropriate-timeouts)」のガイダンスに従ってください。

また、値を大きくすると、メモリが断片化される可能性も高くなるため、「[[maxmemory-reserved] の設定を構成する](cache-best-practices-memory-management.md#configure-your-maxmemory-reserved-setting)」のガイダンスに従ってください。

## <a name="avoid-client-connection-spikes"></a>クライアント接続のスパイクを回避する

接続を作成することと閉じることは、Redis サーバーにとってコストのかかる操作です。 クライアント アプリケーションで短時間のうちに作成または閉じられる接続の数が多すぎると、Redis サーバーの負担になる可能性があります。

多数のクライアント インスタンスをインスタンス化して、一度に Redis に接続する場合は、接続されたクライアントの数が急増するのを防ぐために、新しい接続の作成をずらすことを検討してください。

## <a name="memory-pressure"></a>メモリ不足

サーバーのメモリ使用率が高いと、システムでデータをディスクにページングする必要が生じ、ページ フォールトによってシステムの処理速度が大幅に低下するおそれがあります。

## <a name="avoid-long-running-commands"></a>実行時間の長いコマンドを回避する

Redis サーバーはシングル スレッド システムです。 実行時間の長いコマンドは、クライアント側で待ち時間やタイムアウトが発生する原因となります。これは、サーバーで実行時間の長いコマンドを処理している間に他の要求に応答できないためです。 詳細については、「[Azure Cache for Redis のサーバー側の問題に関するトラブルシューティング](cache-troubleshoot-server.md)」を参照してください。  

## <a name="monitor-server-load"></a>サーバーの負荷を監視する

サーバーの負荷を監視する機能を追加して、サーバーの負荷が高いときに通知を受け取ることができるようにします。 監視することは、アプリケーションの制約を理解するのに役立ちます。 その後、問題を軽減するために事前に対処することができます。 パフォーマンスに悪影響が生じるのを防ぐために、サーバーの負荷を 80% 未満にしておくことをお勧めします。

## <a name="plan-for-server-maintenance"></a>サーバー メンテナンスを計画する

キャッシュ サーバーのメンテナンス中は、ピーク時の負荷を処理するのに十分なサーバー容量があることを確認します。 ピーク時の負荷の下でノードを再起動して、システムをテストします。 パッチのデプロイをシミュレートする方法の詳細については、「[再起動](cache-administration.md#reboot)」を参照してください。

## <a name="test-for-increased-server-load-after-failover"></a>フェールオーバー後のサーバー負荷の増加をテストする

Standard と Premium の SKU では、各キャッシュは 2 つのノードでホストされます。 ロード バランサーによってクライアント接続はそれらの 2 つのノードに分散されます。 計画または計画外のメンテナンスがプライマリ ノードで行われると、そのノードですべてのクライアント接続が終了されます。 このような状況では、すべてのクライアント接続が 1 つのノードに対して行われ、残っている 1 つのノードでサーバー負荷が増加する場合があります。 このシナリオをテストするため、プライマリ ノードを再起動し、1 つのノードでもサーバー負荷が高くなりすぎることなく、すべてのクライアント接続を処理できることを確認するようお勧めします。

## <a name="next-steps"></a>次のステップ

- [Azure Cache for Redis のサーバー側の問題に関するトラブルシューティング](cache-troubleshoot-server.md)
- [接続の回復力](cache-best-practices-connection.md)
  - [適切なタイムアウトを構成する](cache-best-practices-connection.md#configure-appropriate-timeouts)。
- [メモリ管理](cache-best-practices-memory-management.md)
  - [[maxmemory-reserved] の設定を構成する](cache-best-practices-memory-management.md#configure-your-maxmemory-reserved-setting)
