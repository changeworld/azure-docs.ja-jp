---
title: Oracle 用 BareMetal の修正プログラムに関する考慮事項
description: Oracle 用 BareMetal インフラストラクチャのオペレーティング システム/カーネル修正プログラムに関する考慮事項について説明します。
ms.topic: reference
ms.subservice: baremetal-oracle
ms.date: 10/06/2021
ms.openlocfilehash: 145601a4725b257be283840eed1e31f68ed720d1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130215917"
---
# <a name="patching-considerations-for-baremetal-for-oracle"></a>Oracle 用 BareMetal の修正プログラムに関する考慮事項

この記事では、Oracle 用 BareMetal インフラストラクチャのオペレーティング システム (OS) とカーネル修正プログラムに関する重要な考慮事項について説明します。

適切なネットワーク パフォーマンスとシステムの安定性のために、eNIC ドライバーと fNIC ドライバーの OS 固有バージョンをインストールしてください。 次の互換性の表を参照してください。 

サーバーは、互換性のあるバージョンの顧客に配信されます。 ただし、OS とカーネルの修正プログラムの適用中に、ドライバーを既定のドライバー バージョンにロールバックできます。 そのため、OS とカーネル修正プログラムの適用後、正しいバージョンのドライバーが実行されていることをご確認ください。

| OS ベンダー | OS パッケージ バージョン | Firmware Version | eNIC ドライバー | fNIC ドライバー |
| --- | --- | --- | --- | --- |
| Red Hat | RHEL 7.6 | 3.2.3i | 2.3.0.53 | 1.6.0.34 |
| Red Hat | RHEL 7.6 | 4.1.1b | 2.3.0.53 | 1.6.0.34 |
| Red Hat | RHEL 7.6 | 4.1.1b | 4.0.0.8  | 2.0.0.60 |

## <a name="next-steps"></a>次のステップ

Oracle 用 BareMetal のイーサネット構成について確認します。

> [!div class="nextstepaction"]
> [Oracle 用 BareMetal のイーサネット構成](oracle-baremetal-ethernet.md)

