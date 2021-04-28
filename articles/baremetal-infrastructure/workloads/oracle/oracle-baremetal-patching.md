---
title: Oracle 用 BareMetal の修正プログラムに関する考慮事項
description: Oracle 用 BareMetal インフラストラクチャのオペレーティング システム/カーネル修正プログラムに関する考慮事項について説明します。
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 5af713c776def8af558531c84013b221a8d30087
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2021
ms.locfileid: "107558677"
---
# <a name="patching-considerations-for-baremetal-for-oracle"></a>Oracle 用 BareMetal の修正プログラムに関する考慮事項

この記事では、Oracle 用 BareMetal インフラストラクチャのオペレーティング システム/カーネル修正プログラムに関する重要な考慮事項について説明します。

適切なネットワーク パフォーマンスとシステムの安定性のために、次の互換性の表に示されているように、eNIC ドライバーおよび fNIC ドライバーの OS 固有のバージョンをインストールしてください。 

サーバーは、互換性のあるバージョンの顧客に配信されます。 ただし、オペレーティング システム (OS)/カーネル修正プログラムの適用時に、ドライバーを既定のドライバー バージョンにロールバックできます。 そのため、OS/カーネル修正プログラムの適用操作の後、適切なドライバー バージョンが実行されていることを確認してください。

| OS ベンダー | OS パッケージ バージョン | Firmware Version | eNIC ドライバー | fNIC ドライバー |
| --- | --- | --- | --- | --- |
| Red Hat | RHEL 7.6 | 3.2.3i | 2.3.0.53 | 1.6.0.34 |
| Red Hat | RHEL 7.6 | 4.1.1b | 2.3.0.53 | 1.6.0.34 |

## <a name="next-steps"></a>次のステップ

Oracle 用 BareMetal のイーサネット構成について確認します。

> [!div class="nextstepaction"]
> [Oracle 用 BareMetal のイーサネット構成](oracle-baremetal-ethernet.md)

