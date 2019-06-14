---
title: Azure NetApp Files のパフォーマンス ベンチマーク | Microsoft Docs
description: ボリューム レベルでの Azure NetApp Files のパフォーマンス ベンチマーク テストの結果について説明します。
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
ms.date: 04/22/2019
ms.author: b-juche
ms.openlocfilehash: aca0668fc364518fe45d9fe94d089ee366b25676
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "64871224"
---
# <a name="performance-benchmarks-for-azure-netapp-files"></a>Azure NetApp Files のパフォーマンス ベンチマーク

この記事では、ボリューム レベルでの Azure NetApp Files のパフォーマンス ベンチマーク テストの結果について説明します。 

## <a name="sample-application-used-for-the-tests"></a>テストに使用するサンプル アプリケーション

パフォーマンス テストは、Azure NetApp Files を使用してサンプル アプリケーションで実行しました。 このアプリケーションには次のような特徴があります。 

* クラウド用に構築された Linux ベースのアプリケーションです。
* 必要に応じてコンピューティング能力を向上させるために、仮想マシン (VM) を追加して直線的にスケーリングすることができます。
* データ レイクへの迅速なアクセスが必要です。
* その I/O パターンはランダムの場合もあればシーケンシャルな場合もあります。 
    * ランダム パターンでは、大量の I/O に対して低待機時間が必要です。 
    * シーケンシャル パターンでは、大量の帯域幅が必要です。 

## <a name="about-the-workload-generator"></a>ワークロード ジェネレーターについて

結果は Vdbench 概要ファイルから得られます。 [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) は、ストレージ パフォーマンス検証用のディスク I/O ワークロードを生成するコマンドライン ユーティリティです。 使用するクライアント サーバー構成はスケーラブルです。  これには、単一の混合マスター/クライアントと 14 の専用のクライアント VM が含まれます。

## <a name="about-the-tests"></a>テストについて

テストは、サンプル アプリケーションが持つ可能性のある制限と、その制限に達するまでの応答時間を特定するように設計されました。  

次のテストが実行されました。 

* 100% 8 KiB ランダム読み取り
* 100% 8 KiB ランダム書き込み
* 100% 64 KiB シーケンシャル読み取り
* 100% 64 KiB シーケンシャル書き込み
* 50% 64 KiB シーケンシャル読み取り、50% 64 KiB シーケンシャル書き込み
* 50% 8 KiB ランダム読み取り、50% 8 KiB ランダム書き込み

## <a name="bandwidth"></a>帯域幅

Azure NetApp Files では、複数の[サービス レベル](azure-netapp-files-service-levels.md)が用意されています。 各サービス レベルでは、プロビジョニングされた容量 (ボリューム クォータ) の TiB ごとの帯域幅の量が異なります。 ボリュームの帯域幅の制限は、サービス レベルとボリューム クォータの組み合わせに基づいてプロビジョニングされます。 帯域幅の制限は、実現する実際のスループット量を決定する際の唯一の要因であることに注意してください。  

現在、テストの際にワークロードが単一ボリュームに対して達成した最高のスループットは 4,500 MiB です。  Premium サービス レベルの場合、70.31 TiB のボリューム クォータで、このスループットを実現するのに十分な帯域幅が次の計算に従ってプロビジョニングされます。 

![帯域幅の数式](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![クォータとサービス レベル](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>スループット集中型のワークロード

スループット テストでは、Vdbench と 12xD32s V3 ストレージ VM の組み合わせが使用されました。 テストのサンプル ボリュームでは、次のスループットの数値が達成されました。

![スループットのテスト](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>I/O 集中型のワークロード

I/O テストでは、Vdbench と 12xD32s V3 ストレージ VM の組み合わせが使用されました。 テストのサンプル ボリュームでは、次の I/O の数値が達成されました。

![I/O テスト](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Latency

テスト VM と Azure NetApp Files ボリュームとの距離は、I/O パフォーマンスに影響を与えます。  次のグラフは、2 つの異なる VM セットについて、IOPS の曲線と応答の待機時間の曲線と比較したものです。  VM の 1 つのセットは Azure NetApp Files の近くに置かれており、もう 1 つのセットはそれよりさらに離れた場所にあります。  より遠くにある VM のセットの待機時間の増大は、所与のレベルの並列処理で達成される IOPS の量に影響を与えていることに注目してください。  それにもかかわらず、以下に示すように、ボリュームに対する読み取りは 300,000 IOPS を超える可能性があります。 

![待機時間の検討](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>まとめ

待機時間の影響を受けやすいワークロード (データベース) の応答時間は、1 ミリ秒を示しています。 トランザクション パフォーマンスは、単一ボリュームの場合、300k IOPS を超えています。

スループットの影響を受けやすいアプリケーション (ストリーミングおよびイメージング用) のスループットは、4.5 GiB/s を示しています。
