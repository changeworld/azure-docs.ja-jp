---
title: Oracle ワークロード用の BareMetal のストレージ
description: Oracle ワークロード向け BareMetal インフラストラクチャによって提供されるストレージについて学びます。
ms.topic: reference
ms.subservice: baremetal-oracle
ms.date: 04/14/2021
ms.openlocfilehash: 374971412e3866b9bf563e1badc266da382237ff
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2021
ms.locfileid: "110578519"
---
# <a name="storage-on-baremetal-for-oracle-workloads"></a>Oracle ワークロード用の BareMetal のストレージ

この記事では、Oracle ワークロード用の BareMetal インフラストラクチャが提供するストレージの概要を説明します。

Oracle 用の BareMetal インフラストラクチャでは、NetApp ネットワーク ファイルシステム (NFS) ストレージが提供されます。 NFS ストレージには、Oracle Real Application Clusters (RAC) 認定は必要ありません。 詳細については、Oracle の「[RAC Technologies Matrix for Linux Clusters](https://www.oracle.com/database/technologies/tech-generic-linux-new.html)」を参照してください。

このストレージ製品には、A700s または A800s ストレージ コントローラーを使用する、OEM パートナーによる Tier 3 サポートが含まれています。

BareMetal インフラストラクチャ ストレージは、次の Premium Storage 機能を提供します。

- DNFS プロトコルを介して提供されるデータ/ログ/クォーラム/FSA 用ストレージ ボリューム。
- ディスクの冗長性 (*2 件までのディスク障害からの保護*)。
- ボリュームあたり 100 TB に制限された複数のボリュームへのデータのスケールアウト。
- 最大 12 台のコントローラーへのスケールアウト。
- インフラストラクチャにより自動的に処理されるため、ディスク レベルの管理 (ディスクの *追加/削除*) は不要。
- ファイルの内容を別のボリュームに再配布する際のダウンタイムなし。
- ボリュームを拡大/縮小する機能。
- 複製とスナップ コンテナーを使用したバックアップのための SnapCenter 統合。
- 保存時のデータ暗号化、FIPS のサポート (140-2)。

## <a name="next-steps"></a>次のステップ

BareMetal インフラストラクチャの修正プログラムに関する考慮事項について説明します。

> [!div class="nextstepaction"]
> [Oracle 用 BareMetal の修正プログラムに関する考慮事項](oracle-baremetal-patching.md)

