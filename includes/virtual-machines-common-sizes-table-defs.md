---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 63c53a9b95e27486d7d6944c28f8fb085b1bc6ca
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279210"
---
<!-- Not used for Ls-series -->

## <a name="size-table-definitions"></a>サイズ表の定義

- ストレージ容量は GiB (1024^3 バイト) 単位で示されています。 GB (1000^3 バイト) 単位のディスクと GiB (1024^3 バイト) 単位のディスクを比較する場合は、GiB 単位の方が容量の数値が小さく見えることに注意してください。 たとえば、1023 GiB = 1098.4 GB です。
- ディスク スループットの測定単位は、1 秒あたりの入力/出力操作数 (IOPS) および MBps です (MBps = 10^6 バイト/秒)。
- データ ディスクは、キャッシュを有効にしたモードでも無効化したモードでも動作します。 キャッシュを有効にしたデータ ディスクの操作では、ホスト キャッシュ モードは **ReadOnly** または **ReadWrite** に設定されています。  キャッシュを無効にしたデータ ディスクの操作では、ホスト キャッシュ モードは **None**に設定されています。
- VM の最高のパフォーマンスを得るには、データ ディスクの数を vCPU あたり 2 ディスクに制限する必要があります。
- **想定ネットワーク帯域幅**は、すべての宛先について、すべての NIC で VM の種類ごとに割り当てられた最大集約帯域幅です。 詳しくは、「[仮想マシンのネットワーク帯域幅](../articles/virtual-network/virtual-machine-network-throughput.md)」を参照してください。

  上限は保証されていません。 制限は、目的のアプリケーションに適した VM の種類を選択するためのガイダンスを示しています。 実際のネットワークのパフォーマンスは、ネットワークの輻輳、アプリケーションの負荷、ネットワーク設定などのさまざまな要因に左右されます。 ネットワーク スループットの最適化については、「[Azure 仮想マシンのネットワーク スループットの最適化](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md)」を参照してください。 Linux または Windows で想定ネットワーク パフォーマンスを実現するには、特定のバージョンの選択または VM の最適化が必要になる場合があります。 詳細については、「[帯域幅/スループットのテスト (NTTTCP)](../articles/virtual-network/virtual-network-bandwidth-testing.md)」を参照してください。



