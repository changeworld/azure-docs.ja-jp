---
title: Azure Disk Storage 上のアプリケーションのベンチマーク
description: Azure 上でアプリケーションのベンチマークを実行するプロセスについて説明します。
author: roygara
ms.author: rogarana
ms.date: 06/29/2021
ms.topic: how-to
ms.service: storage
ms.subservice: disks
ms.openlocfilehash: c57047cdaf848bc27805d8819be515636d492676
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122687553"
---
# <a name="benchmark-a-disk"></a>ディスクのベンチマークの実行

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

ベンチマークは、アプリケーションのさまざまなワークロードをシミュレートし、ワークロードごとにアプリケーションのパフォーマンスを測定するプロセスです。 [高パフォーマンス用の設計に関する記事](premium-storage-performance.md)に記載されている手順を使用して、アプリケーションのパフォーマンス要件を収集しました。 アプリケーションをホストする VM でベンチマーク ツールを実行することで、アプリケーションが Premium SSD を使用して実現できるパフォーマンス レベルを確認できます。 この記事では、Azure Premium SSD ディスクと共にプロビジョニングされた Standard_D8ds_v4 VM のベンチマークの例を示します。

一般的なベンチマーク ツールである DiskSpd (Windows) と FIO (Linux) を使用しました。 これらのツールは、ワークロードのような実稼働をシミュレートする複数のスレッドを起動し、システム パフォーマンスを測定します。 これらのツールを使用して、通常はアプリケーションに合わせて変更できないブロック サイズやキューの深さなどのパラメーターを構成することもできます。 これにより、さまざま種類のアプリケーション ワークロードに対応するために、Premium SSD と共にプロビジョニングされた高スケール VM でパフォーマンスを最大限に引き上げる柔軟性が得られます。 各ベンチマーク ツールの詳細については、「[DiskSpd](https://github.com/Microsoft/diskspd/wiki/)」と「[fio](http://freecode.com/projects/fio)」をご覧ください。

次の例を実行するには、Standard_D8ds_v4 を作成し、4 つの Premium SSD を VM に接続します。 4 個のディスクのうち、3 個のディスクのホスト キャッシュを "None" に構成し、これらのディスクを NoCacheWrites というボリュームにストライピングします。 残りのディスクでは、ホストキャッシュを "ReadOnly" に構成し、このディスクを使用して CacheReads というボリュームを作成します。 この設定を使用して、Standard_D8ds_v4 VM から最大の読み取り/書き込みパフォーマンスが得られます。 Premium SSD を使用して Standard_D8ds_v4 を作成する詳細な手順については、「[高パフォーマンス用の設計](premium-storage-performance.md)」を参照してください。

[!INCLUDE [virtual-machines-disks-benchmarking](../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>次のステップ

[ハイ パフォーマンスのための設計](premium-storage-performance.md)に関する記事に進んでください。

この記事では、プロトタイプについて、既存のアプリケーションと同様のチェックリストを作成します。 ベンチマーク ツールを使用して、ワークロードをシミュレートし、プロトタイプ アプリケーションでパフォーマンスを測定します。 こうすることで、どのディスク オファリングがアプリケーションのパフォーマンス要件に合っているか、上回っているかを判断できます。 その後、実稼働アプリケーションにも同じガイドラインを実装できます。
