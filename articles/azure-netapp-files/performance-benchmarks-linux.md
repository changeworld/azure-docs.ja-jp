---
title: Azure NetApp Files の Linux 用パフォーマンス ベンチマーク | Microsoft Docs
description: Azure NetApp Files で Linux 用に提供されるパフォーマンス ベンチマークについて説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/29/2021
ms.author: b-juche
ms.openlocfilehash: 814474418386041d2b20759c4fb2accc534e8b9d
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355864"
---
# <a name="azure-netapp-files-performance-benchmarks-for-linux"></a>Azure NetApp Files の Linux 用パフォーマンス ベンチマーク

この記事では、Azure NetApp Files で Linux 用に提供されるパフォーマンス ベンチマークについて説明します。

## <a name="linux-scale-out"></a>Linux のスケールアウト

このセクションでは、Linux ワークロードのスループットとワークロードの IOPS のパフォーマンス ベンチマークについて説明します。

### <a name="linux-workload-throughput"></a>Linux ワークロードのスループット  

次のグラフは、64 キビバイト (KiB) のシーケンシャル ワークロードと 1 TiB のワーキング セットを表しています。 これは、約 1,600 MiB/秒の純粋なシーケンシャル書き込みから約 4,500 MiB/秒の純粋なシーケンシャル読み取りまでの範囲で単一の Azure NetApp Files ボリュームが処理できることを示しています。  

このグラフは、純粋な読み取りから純粋な書き込みまで 10% ずつ低下することを示しています。 これは、さまざまな読み取りと書き込みの比率 (100%:0%、90%:10%、80%:20% など) を使用するときに想定できる値を示しています。

![Linux ワークロードのスループット](../media/azure-netapp-files/performance-benchmarks-linux-workload-throughput.png)  

### <a name="linux-workload-iops"></a>Linux ワークロードの IOPS  

次のグラフは、4 キビバイト (KiB) のランダム ワークロードと 1 TiB のワーキング セットを表しています。 このグラフは、約 130,000 回の純粋なランダムな書き込みから約 460,000 回の純粋なランダム読み取りまでの範囲で単一の Azure NetApp Files ボリュームが処理できることを示しています。  

このグラフは、純粋な読み取りから純粋な書き込みまで 10% ずつ低下することを示しています。 これは、さまざまな読み取りと書き込みの比率 (100%:0%、90%:10%、80%:20% など) を使用するときに想定できる値を示しています。

![Linux ワークロードの IOPS](../media/azure-netapp-files/performance-benchmarks-linux-workload-iops.png)  

## <a name="linux-scale-up"></a>Linux のスケールアップ  

このセクションのグラフは、NFSv3 でのクライアント側マウント オプションの検証テストの結果を示しています。 詳細については、[Linux マウント オプションの `nconnect` のセクション](performance-linux-mount-options.md#nconnect)を参照してください。

これらのグラフでは、`connected` なしのマウントされたボリュームに対する `nconnect` の利点を比較しています。 これらのグラフで、FIO は 64 KiB シーケンシャル ワークロードを使用して、米国西部 2 Azure リージョン内の 1 つの D32s_v4 インスタンスからワークロードを生成しました。これは、ここに表されたテストの時点で Azure NetApp Files でサポートされる最大の I/O サイズでした。 Azure NetApp Files は現在、より大きな I/O サイズをサポートしています。 詳細については、[Linux マウント オプションの `rsize` と `wsize` のセクション](performance-linux-mount-options.md#rsize-and-wsize)を参照してください。

### <a name="linux-read-throughput"></a>Linux の読み取りスループット  

次のグラフは、`nconnect` による約 3,500 MiB/秒の読み取りの 64 KiB シーケンシャル読み取りを示しています。これは、`nconnect` なしの場合の約 2.3 倍です。

![Linux の読み取りスループット](../media/azure-netapp-files/performance-benchmarks-linux-read-throughput.png)  

### <a name="linux-write-throughput"></a>Linux の書き込みスループット  

次のグラフは、シーケンシャル書き込みを示しています。 ここから、シーケンシャル書き込みに関しては `nconnect` に大きなメリットがないことがわかります。 1,500 MiB/秒は、大まかに言って、シーケンシャル書き込みボリュームの上限と D32s_v4 インスタンスのエグレス制限の両方です。

![Linux の書き込みスループット](../media/azure-netapp-files/performance-benchmarks-linux-write-throughput.png)  

### <a name="linux-read-iops"></a>Linux の読み取り IOPS  

次のグラフは、`nconnect` による約 200,000 の読み取り IOPS の 4 KiB ランダム読み取りを示しています。これは、`nconnect` なしの場合の約 3 倍です。

![Linux の読み取り IOPS](../media/azure-netapp-files/performance-benchmarks-linux-read-iops.png)  

### <a name="linux-write-iops"></a>Linux の書き込み IOPS  

次のグラフは、`nconnect` による約 135,000 の書き込み IOPS の 4 KiB ランダム書き込みを示しています。これは、`nconnect` なしの場合の約 3 倍です。

![Linux の書き込み IOPS](../media/azure-netapp-files/performance-benchmarks-linux-write-iops.png)  

## <a name="next-steps"></a>次のステップ

- [Azure NetApp Files: クラウド ストレージを最大限に活用する](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf?hsCtaTracking=f2f560e9-9d13-4814-852d-cfc9bf736c6a%7C764e9d9c-9e6b-4549-97ec-af930247f22f)
