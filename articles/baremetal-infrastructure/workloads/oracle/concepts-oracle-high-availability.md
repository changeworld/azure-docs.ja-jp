---
title: BareMetal の Oracle の高可用性とディザスター リカバリー
description: Azure BareMetal インフラストラクチャでの Oracle の高可用性とディザスター リカバリーについて説明します。
ms.topic: how-to
ms.subservice: baremetal-oracle
ms.date: 04/15/2021
ms.openlocfilehash: 3b23774604b5126321d85892647e73118e08260f
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2021
ms.locfileid: "110575617"
---
# <a name="high-availability-and-disaster-recovery-for-oracle-on-baremetal"></a>BareMetal の Oracle の高可用性とディザスター リカバリー

この記事では、高可用性とディザスター リカバリーの基本について説明します。 次に、BareMetal インフラストラクチャ上の Oracle 環境で高可用性とディザスター リカバリーを実現する方法について説明します。

## <a name="high-availability-vs-disaster-recovery"></a>高可用性とディザスター リカバリーの比較

高可用性とディザスター リカバリーはどちらもカバレッジを提供しますが、対象となる障害の種類が異なります。 使用する Oracle Database の機能とオプションが異なります。

高可用性により、システムは、アプリケーションのユーザー エクスペリエンスに影響を与えることなく、複数の障害を乗り越えるとができます。 高可用性システムの一般的な特性は次のとおりです。

- 単一障害点のない冗長なハードウェア。
- ディスク ドライブの障害やネットワーク ケーブルの問題など、重大でないエラーからの自動復旧。
- 処理に大きな影響を与えずに、ハードウェアとソフトウェアの変更を適用する機能。
- 目標復旧時間 (RTO) と目標復旧時点 (RPO) の目標を満たす、または上回る。

高可用性のために使用される Oracle の最も一般的な機能は、[Oracle Real Application Clusters (RAC)](https://docs.oracle.com/en/database/oracle/oracle-database/19/racad/introduction-to-oracle-rac.html#GUID-5A1B02A2-A327-42DD-A1AD-20610B2A9D92) です。

ディザスター リカバリーは、プライマリの高可用性戦略に悪影響を与える、回復不可能なローカルの障害から保護します。 Oracle エコシステムでは、これはデータベース レプリケーション ([Oracle Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/19/sbydb/preface.html#GUID-B6209E95-9DA8-4D37-9BAD-3F000C7E3590) とも呼ばれます) によって提供されます。

## <a name="next-steps"></a>次のステップ

Oracle の高可用性機能の詳細について、次を参照してください。

> [!div class="nextstepaction"]
> [BareMetal インフラストラクチャでの Oracle の高可用性機能](high-availability-features.md)
