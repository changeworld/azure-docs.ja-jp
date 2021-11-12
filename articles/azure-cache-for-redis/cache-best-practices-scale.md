---
title: スケーリングに関するベスト プラクティス
titleSuffix: Azure Cache for Redis
description: Azure Cache for Redis の拡張方法を確認します。
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: b0d43e062db591600d1cbcc0cdb18b7ce6b8ebac
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132323507"
---
# <a name="scaling"></a>Scaling

## <a name="scaling-under-load"></a>負荷時のスケーリング

負荷時にキャッシュをスケーリングする場合は、システムの応答性を向上させるため にmaxmemory-reserved の設定を行ってください。 詳細は、「[maxmemory 設定の構成](cache-best-practices-memory-management.md#configure-your-maxmemory-reserved-setting)」を参照してください。

## <a name="scaling-clusters"></a>クラスターのスケーリング

クラスタリングされたキャッシュをスケーリングする前に、キャッシュ内のデータをできる限り削減してください。データを削減することで、移動するデータ量が少なくなり、拡張操作に必要な時間が短縮されます。 拡張の時期については、「[拡張の時期](cache-how-to-scale.md#when-to-scale)」を参照してください。

## <a name="scale-before-load-is-too-high"></a>負荷が高くなる前に拡張する

サーバーの負荷またはメモリの使用量が増大する前に、スケーリングを開始します。 高すぎる場合は、Redis サーバーがビジー状態であることを意味します。 ビジー状態の Redis サーバーには、データの拡張と再配布に必要なリソースが不足しています。

## <a name="cache-sizes"></a>キャッシュ サイズ

TLS を使用し、接続数が多い場合は、負荷をより多くのコアに分散できるようにスケール アウトすることを検討してください。 一部のキャッシュ サイズは、4 つ以上のコアを持つ VM でホストされます。

TLS の暗号化と解読および TLS の接続と切断のワークロードを複数のコアに分散させて、キャッシュ VM の全体的な CPU 使用率を下げます。 詳細は、「[VM のサイズとコアの詳細](./cache-planning-faq.yml#azure-cache-for-redis-performance)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [[maxmemory-reserved] の設定を構成する](cache-best-practices-memory-management.md#configure-your-maxmemory-reserved-setting)
- [Azure Cache for Redis インスタンスのスケーリング](cache-how-to-scale.md)
