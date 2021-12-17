---
title: Oracle 用 BareMetal インフラストラクチャのアーキテクチャ
description: Oracle 用 BareMetal インフラストラクチャのいくつかの構成のアーキテクチャについて説明します。
ms.topic: reference
ms.subservice: baremetal-oracle
ms.date: 04/14/2021
ms.openlocfilehash: 1e4a00f53018647ca3b3528a9881ec36af067e28
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2021
ms.locfileid: "110578639"
---
# <a name="architecture-of-baremetal-infrastructure-for-oracle"></a>Oracle 用 BareMetal インフラストラクチャのアーキテクチャ

この記事では、Oracle 用 BareMetal インフラストラクチャのアーキテクチャ オプションと、それぞれがサポートする機能について説明します。

## <a name="single-instance"></a>単一インスタンス

このトポロジでは、BareMetal インフラストラクチャへの移行のために、Oracle Data Guard を備えた Oracle Database の単一インスタンスがサポートされます。 高可用性とメンテナンス作業のために、スタンバイ ノードの使用がサポートされます。

[![Oracle Data Guard を備えた Oracle Database の単一インスタンスのアーキテクチャを示す図。](media/oracle-baremetal-architecture/single-instance-architecture.png)](media/oracle-baremetal-architecture/single-instance-architecture.png#lightbox)

## <a name="oracle-real-application-clusters-rac-one-node"></a>Oracle Real Application Clusters (RAC) One Node

このトポロジでは、共有ストレージとグリッド クラスターを使用した RAC 構成がサポートされます。 データベース インスタンスは 1 つのノードでのみ実行されます (アクティブ/パッシブ構成)。

次のような機能が含まれます。

- Oracle RAC One Node によるアクティブ/パッシブ

    - 自動フェールオーバー

    - 2 番目のノードでの高速再起動

- Oracle RAC によるリアルタイム フェールオーバーとスケーラビリティ

- ダウンタイムなしのローリング メンテナンス

[![Oracle RAC One Node のアクティブ/パッシブ構成のアーキテクチャを示す図。](media/oracle-baremetal-architecture/one-node-rac-architecture.png)](media/oracle-baremetal-architecture/one-node-rac-architecture.png#lightbox)

## <a name="rac"></a>RAC

このトポロジでは、共有ストレージとグリッド クラスターを使用した Oracle RAC 構成がサポートされ、その一方でデータベースごとに複数のインスタンスが同時に実行されます (アクティブ/アクティブ構成)。

- 追加されたサーバーのオンライン プロビジョニングによって、パフォーマンスを簡単にスケーリングできます。 
-  ユーザーはすべてのサーバーでアクティブとなり、すべてのサーバーでは同じ Oracle Database へのアクセスが共有されます。 
-  あらゆる種類のデータベース メンテナンスを、最小限のダウンタイムまたはダウンタイムなしで、オンラインまたはローリング方式で実行できます。 
- Oracle Active Data Guard (ADG) スタンバイ システムは、テスト システムとして 2 つの目的を簡単に果たすことができます。 

この構成を使用すると、変更を運用環境に適用する前に、運用データベースの正確なコピーでそれらのすべての変更をテストできます。

> [!NOTE]
> Active Data Guard Far Sync (同期モード) を使用する場合、この機能がサポートされているリージョン ゾーンを考慮する必要があります。 地理的に分散したリージョンでのみ、非同期モードで Data Guard を使用することをお勧めします。

[![Oracle RAC のアクティブ/アクティブ構成のアーキテクチャを示す図。](media/oracle-baremetal-architecture/rac-architecture.png)](media/oracle-baremetal-architecture/rac-architecture.png#lightbox)

## <a name="next-steps"></a>次のステップ

Oracle ワークロード用 BareMetal インスタンスのプロビジョニングについて確認します。

> [!div class="nextstepaction"]
> [Oracle 用 BareMetal インフラストラクチャのプロビジョニング](oracle-baremetal-provision.md)

