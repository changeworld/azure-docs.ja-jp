---
title: Azure での GPU コンピューティング ワークロードの移行ガイド
description: NC、ND、NCv2 シリーズの移行ガイド。
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 08/15/2020
ms.openlocfilehash: 1f148ad07137f6384a9752d2321836601a6bd996
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458777"
---
# <a name="migration-guide-for-gpu-compute-workloads-in-azure"></a>Azure での GPU コンピューティング ワークロードの移行ガイド

より強力な GPU がマーケットプレースおよび Microsoft Azure データセンターで使用できる状態になったら、ワークロードのパフォーマンスを再評価し、新しい GPU への移行を検討することをお勧めします。

同じ理由から、また高品質で信頼性の高いサービス オファリングを維持するために、Azure では古い VM サイズを稼働するハードウェアを定期的に廃止しています。 Azure で廃止される GPU 製品の最初のグループは、NVIDIA Tesla K80、P100、P40 のデータセンター GPU アクセラレータをそれぞれ利用した、当初の NC、NC v2、ND シリーズの VM です。 これらの製品は 2022 年 8 月 31 日に廃止される予定で、このシリーズで最も古い VM は 2016 年に導入されました。

それ以降、GPU はディープ ラーニングと HPC 業界全体と共に大きな進歩を遂げ、通常は世代間のパフォーマンスが 2 倍を超えています。 NVIDIA K80、P40、P100 GPU の発表以降、Azure では新しい世代とカテゴリの VM 製品を複数出荷してきました。これらの製品は、NVIDIA の T4、V100、A100 GPU に基づいて GPU で高速化されたコンピューティングと AI を実行し、InfiniBand ベースの相互接続ファブリックなどのオプション機能によって差別化されています。 これらはすべて、お客様に移行パスとして検討することを推奨するオプションです。

ほとんどの場合、新しい世代の GPU によってパフォーマンスが大幅に向上することで、GPU 使用時間あたりのコストが変化することがあっても、バースト可能なジョブについてジョブの実行時間が短縮されたり、コンピューティング リソースについての固定サイズの需要に応えるのに必要な GPU 対応 VM の全体量が削減されたりすることによって、TCO が減少します。 これらの利点に加えて、お客様はパフォーマンスの高い VM によって解決までの時間を短縮でき、新しいソフトウェア、CUDA ランタイム、およびドライバー バージョンを採用することによってソリューションの正常性とサポート性を向上させることができます。

## <a name="migration-vs-optimization"></a>移行と最適化の比較

Azure では、お客様には特定の GPU VM 製品の選択に影響する複数の要件があることを認識しています。これには GPU アーキテクチャの考慮事項、相互接続、TCO、解決までの時間、地域的なコンプライアンスや待ち時間の要件に基づくリージョン別の利用可能性などがあり、これらのいくつかは、時間の経過につれて変化することもあります。

また、GPU アクセラレーションは急速に進化している新しい領域でもあります。

したがって、この製品領域には真に汎用的な指針がないため、クラスター化されたデプロイ モデルから単一の大規模 8 GPU VM への (あるいはその逆への) 移動、精度の低いデータ型の活用、マルチインスタンス GPU などの機能の採用など、移行のタイミングが、ワークロードへの潜在的な劇的な変化を再評価する上で最も適しています。

世代ごとの GPU パフォーマンスが既に劇的に増加しているのはどのような状況か、TensorCores の追加などによりパフォーマンスが桁違いに増加できる機能はどれかといった種類の検討は、極めてワークロードに固有のものです。

アプリケーションのアーキテクチャ見直しと移行を組み合わせることで、大きな価値を生み出し、コストと解決までの時間を改善することができます。

ただし、これらの種類の改善は、このドキュメントの範囲を超えています。ここでは、現在お客様が実行できる一般化されたワークロードに対する直接的な同等のクラスに焦点を当て、GPU あたりの価格 "*および*" パフォーマンスの両方で、廃止される既存の VM ファミリに最も近い VM オプションを特定することです。

したがって、このドキュメントでは、必要な VM インスタンス、GPU、相互接続の数など、ワークロード固有のプロパティに対する分析情報や制御をユーザーが持っていない場合があることを想定しています。

## <a name="recommended-upgrade-paths"></a>推奨されるアップグレード パス

### <a name="nc-series-vms-featuring-nvidia-k80-gpus"></a>NVIDIA K80 GPU を搭載した NC シリーズの VM

[NC (v1) シリーズ](./nc-series.md)の VM は、Azure の最も古い GPU 高速コンピューティングの VM タイプで、Intel Xeon E5-2690 v3 (Haswell) プロセッサとペアになった 1 から 4 個の NVIDIA Tesla K80 データセンター GPU アクセラレーターによって稼働されます。 かつては、要求の厳しい AI、ML、HPC の用途向けの主力 VM のタイプでした。1 ドルあたりのスループットが高い GPU よりも、GPU 使用時間あたりの絶対コストが非常に低いことを評価するユーザーによって、製品ライフサイクルの後半も (特に NC シリーズのキャンペーン価格を通じて)　好んで選ばれました。

今日では、新しい GPU を搭載した VM シリーズに比べて旧型の NVIDIA K80 GPU プラットフォームは、コンピューティング パフォーマンスが比較的低いことから、NC シリーズの一般的なユース ケースは、リアルタイムの推論と分析ワークロードです。この場合、アプリケーションからの要求が到着したときに要求に対応するために、高速 VM が安定状態で利用できる必要があります。 これらのケースでは、要求の量またはバッチ サイズが不十分なため、パフォーマンスの高い GPU のメリットが得られない場合もあります。 NC VM は、GPU アクセラレーションについて学習し、開発し、または実験する開発者や学生にも人気があります。これらのユーザーは、実稼働レベルで実行する必要がない、反復処理を行う低コストのクラウドベースの CUDA デプロイ ターゲットを必要としています。

一般に、NC シリーズをご利用のお客様は、NC サイズから、NVIDIA Tesla T4 GPU で稼働する軽量ワークロード用の Azure の新しい GPU 高速プラットフォームである [NC T4 v3](./nct4-v3-series.md) サイズに直接移行することを検討する必要があります。ただし、InfiniBand 対応の NC シリーズ サイズで実行中のワークロードについては、他の VM SKU を検討する必要があります。

| 現在の VM サイズ | 目標とする VM サイズ | 仕様の違い | 
|---|---|---|
Standard_NC6 <br> Standard_NC6_Promo | Standard_NC4as_T4_v3 <br>or<br>Standard_NC8as_T4 | CPU: Intel Haswell と AMD Rome<br>GPU 数: 1 (同じ)<br>GPU 世代: NVIDIA Keppler と Turing (+2 世代、~2x FP32 FLOPs)<br>GPU メモリ (GPU あたり GiB): 16 (+4)<br>vCPU: 4 (-2) または 8 (+2)<br>メモリ GiB: 16 (-40) または 56 (同じ)<br>一時ストレージ (SSD) GiB: 180 (-160) または 360 (+20)<br>最大データ ディスク数: 8 (-4) または 16 (+4)<br>高速ネットワーク: あり (+)<br>Premium Storage: あり (+)| 
| Standard_NC24<br>Standard_NC24_Promo | Standard_NC64as_T4_v3* | CPU: Intel Haswell と AMD Rome<br>GPU 数: 4 (同じ)<br>GPU 世代: NVIDIA Keppler と Turing (+2 世代、~2x FP32 FLOPs)<br>GPU メモリ (GPU あたり GiB): 16 (+4)<br>vCPU: 64 (+40)<br>メモリ GiB: 440 (+216)<br>一時ストレージ (SSD) GiB: 2880 (+1440)<br>最大データ ディスク数: 32 (-32)<br>高速ネットワーク: あり (+)<br>Premium Storage: あり (+) | 
|Standard_NC24r<br>Standard_NC24r_Promo<br><br>(InfiniBand クラスタリング対応サイズ) | Standard_NC24rs_v3* | CPU: Intel Haswell と Intel Broadwell<br>GPU 数: 4 (同じ)<br>GPU 世代: NVIDIA Keppler と Volta (+2 世代)<br>GPU メモリ (GPU あたり GiB): 16 (+4)<br>vCPU: 24 (+0)<br>メモリ GiB: 448 (+224)<br>一時ストレージ (SSD) GiB: 2948 (+1440)<br>最大データ ディスク数: 32 (同じ)<br>高速ネットワーク設定: なし (同じ)<br>Premium Storage: あり (+)<br>InfiniBand 相互接続: あり | 


### <a name="nd-series-vms-featuring-nvidia-tesla-p40-gpus"></a>NVIDIA Tesla P40 GPU を搭載した ND シリーズの VM

ND シリーズの仮想マシンは、AI やディープ ラーニングのワークロード向けに元々設計されたミッドレンジ プラットフォームです。 これらは旧型に比べて単精度浮動小数点演算が改善されたことによりバッチ推論に対して優れたパフォーマンスを提供し、NVIDIA Tesla P40 GPU と Intel Xeon E5-2690 v4 (Broadwell) CPU で稼働しています。 NC および NC v2 シリーズと同様に、ND シリーズでは RDMA を利用した低遅延で高スループットなセカンダリ ネットワーク、InfiniBand との接続性などを備えた構成が利用できます。これにより、多数の GPU を利用した大規模なトレーニング ジョブを実行できます。

| 現在の VM サイズ | 目標とする VM サイズ | 仕様の違い | 
|---|---|---|
|Standard_ND6 | Standard_NC4as_T4_v3<br>or<br>Standard_NC8as_T4 | CPU: Intel Broadwell と AMD Rome<br>GPU 数: 1 (同じ)<br>GPU 世代: NVIDIA Pascal と Turing (+1 世代)<br>GPU メモリ (GPU あたり GiB): 16 (-8)<br>vCPU: 4 (-2) または 8 (+2)<br>メモリ GiB: 16 (-40) または 56 (-56)<br>一時ストレージ (SSD) GiB: 180 (-552) または 360 (-372)<br>最大データ ディスク数: 8 (-4) または 16 (+4)<br>高速ネットワーク: あり (+)<br>Premium Storage: あり (+) | 
| Standard_ND12 | Standard_NC16as_T4_v3 | CPU: Intel Broadwell と AMD Rome<br>GPU 数: 1 (-1)<br>GPU 世代: NVIDIA Pascal と Turing (+1 世代)<br>GPU メモリ (GPU あたり GiB): 16 (-8)<br>vCPU: 16 (+4)<br>メモリ GiB: 110 (-114)<br>一時ストレージ (SSD) GiB: 360 (-1,114)<br>最大データ ディスク数: 48 (+16)<br>高速ネットワーク: あり (+)<br>Premium Storage: あり (+) | 
| Standard_ND24 | Standard_NC64as_T4_v3* | CPU: Intel Broadwell と AMD Rome<br>GPU 数: 4 (同じ)<br>GPU 世代: NVIDIA Pascal と Turing (+1 世代)<br>GPU メモリ (GPU あたり GiB): 16 (-8)<br>vCPU: 64 (+40)<br>メモリ GiB: 440 (同じ)<br>一時ストレージ (SSD) GiB: 2880 (同じ)<br>最大データ ディスク数: 32 (同じ)<br>高速ネットワーク: あり (+)<br>Premium Storage: あり (+) | 
| Standard_ND24r | Standard_NC24rs_v3* | CPU: Intel Broadwell (同じ)<br>GPU 数: 4 (同じ)<br>GPU 世代: NVIDIA Pascal と Volta (+1 世代)<br>GPU メモリ (GPU あたり GiB): 16 (-8)<br>vCPU: 24 (+0)<br>メモリ GiB: 448 (同じ)<br>一時ストレージ (SSD) GiB: 2948 (同じ)<br>最大データ ディスク数: 32 (同じ)<br>高速ネットワーク設定: なし (同じ)<br>Premium Storage: あり (+)<br>InfiniBand 相互接続: あり (同じ) | 

### <a name="nc-v2-series-vms-featuring-nvidia-tesla-p100-gpus"></a>NVIDIA Tesla P100 GPU を搭載した NC v2 シリーズの VM

NC v2 シリーズの仮想マシンは、AI やディープ ラーニングのワークロード向けに元々設計された主力プラットフォームです。 これらは GPU あたりのパフォーマンスが元の NC シリーズの約 2 倍で、NVIDIA Tesla P100 GPU および Intel Xeon E5-2690 v4 (Broadwell) CPU で稼働し、ディープ ラーニングのトレーニングに対して優れたパフォーマンスを提供します。 NC および ND シリーズと同様に、NC v2 シリーズでは RDMA を利用した低遅延で高スループットなセカンダリ ネットワーク、InfiniBand との接続性などを備えた構成が利用できます。これにより、多数の GPU を利用した大規模なトレーニング ジョブを実行できます。

| 現在の VM サイズ | 目標とする VM サイズ | 仕様の違い | 
|---|---|---|
| Standard_NC6s_v2 | Standard_NC6s_v3 | CPU: Intel Broadwell (同じ)<br>GPU 数: 1 (同じ)<br>GPU 世代: NVIDIA Pascal と Volta (+1 世代)<br>GPU メモリ (GPU あたり GiB): 16 (同じ)<br>vCPU: 6 (同じ)<br>メモリ GiB: 112 (同じ)<br>一時ストレージ (SSD) GiB: 736 (同じ)<br>最大データ ディスク数: 12 (同じ)<br>高速ネットワーク設定: なし (同じ)<br>Premium Storage: あり (+) | 
| Standard_NC12s_v2 | Standard_NC12s_v3 | CPU: Intel Broadwell (同じ)<br>GPU 数: 2 (同じ)<br>GPU 世代: NVIDIA Pascal と Volta (+1 世代)<br>GPU メモリ (GPU あたり GiB): 16 (同じ)<br>vCPU: 12 (同じ)<br>メモリ GiB: 112 (同じ)<br>一時ストレージ (SSD) GiB: 1474 (同じ)<br>最大データ ディスク数: 24 (同じ)<br>高速ネットワーク設定: なし (同じ)<br>Premium Storage: あり (+) | 
| Standard_NC24s_v2 | Standard_NC24s_v3 | CPU: Intel Broadwell (同じ)<br>GPU 数: 4 (同じ)<br>GPU 世代: NVIDIA Pascal と Volta (+1 世代)<br>GPU メモリ (GPU あたり GiB): 16 (同じ)<br>vCPU: 24 (同じ)<br>メモリ GiB: 448 (同じ)<br>一時ストレージ (SSD) GiB: 2948 (同じ)<br>最大データ ディスク数: 32 (同じ)<br>高速ネットワーク設定: なし (同じ)<br>Premium Storage: あり (+) | 
| Standard_NC24rs_v2 | Standard_NC24rs_v3* | CPU: Intel Broadwell (同じ)<br>GPU 数: 4 (同じ)<br>GPU 世代: NVIDIA Pascal と Volta (+1 世代)<br>GPU メモリ (GPU あたり GiB): 16 (同じ)<br>vCPU: 24 (同じ)<br>メモリ GiB: 448 (同じ)<br>一時ストレージ (SSD) GiB: 2948 (同じ)<br>最大データ ディスク数: 32 (同じ)<br>高速ネットワーク設定: なし (同じ)<br>Premium Storage: あり (+)<br>InfiniBand 相互接続: あり (同じ)| 


## <a name="migration-steps"></a>移行の手順

### <a name="general-changes"></a>一般的な変更

1.  移行用のシリーズとサイズを選択します。 詳細な分析情報については、[料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を利用してください。

2.  ターゲット VM シリーズのクォータを取得します

3.  現在の N\* シリーズの VM サイズをターゲット サイズに変更します。 ここで、仮想マシン イメージによって使用されるオペレーティング システムを更新したり、開始点としてドライバーが事前にインストールされている HPC イメージの 1 つを採用したりするとよい場合もあります。

    > [!IMPORTANT]
    > お使いの VM イメージは、新しい GPU VM シリーズで必要とされるものよりも古いバージョンの CUDA ランタイム、NVIDIA ドライバー、および (RDMA 対応のサイズのみで該当する場合は) Mellanox OFED ドライバーで作成されている可能性があります。これらは [Azure ドキュメントの手順に従って](./sizes-gpu.md)更新できます。

### <a name="breaking-changes"></a>重大な変更

#### <a name="select-target-size-for-migration"></a>移行用のターゲット サイズを選択する

現在の使用状況を評価したら、必要な GPU VM の種類を決定します。 ワークロードの要件に応じて、さまざまな選択肢があります。

> [!NOTE]
> ベスト プラクティスとして、コストとパフォーマンスの両方に基づいて VM サイズを選択することをお勧めします。 このガイドの推奨は、パフォーマンス メトリックを、別の VM シリーズの最も合致するものと 1 対 1 で比較した汎用的な結果に基づいています。 適切なサイズを決定する前に、Azure 料金計算ツールを使用してコストを比較してください。

> [!IMPORTANT]
> すべてのレガシ NC、NC v2、ND シリーズのサイズは、マルチ GPU サイズで使用できます。これには、単一の 4 GPU VM または単一の K80、P40、または P100 GPU がそれぞれ提供できるよりも多くのコンピューティング能力を必要とする、スケールアウトされた密結合のワークロードのための InfiniBand 相互接続を持つ (または持たない) 4 GPU サイズなどが含まれます。 上記の推奨では単純な移行パスを示していますが、これらのサイズのユーザーは、[NC v3 シリーズ](./ncv3-series.md)や [ND v2 シリーズ](./ndv2-series.md) などのさらに強力な NVIDIA V100 GPU ベースの VM シリーズを使用してパフォーマンス目標を達成することを検討してください。これらは通常、マルチ GPU およびマルチノード構成が必要になる前に、GPU あたり VM あたりのパフォーマンスを大幅に上昇させることによって、同水準のワークロード パフォーマンスをより低いコストおよび高い管理性で実現できます。
<br>

#### <a name="get-quota-for-the-target-vm-family"></a>ターゲット VM ファミリのクォータを取得する

[VM ファミリによる vCPU クォータの増加を要求する](../azure-portal/supportability/per-vm-quota-requests.md)ためのガイドに従います。 移行用に選択したターゲット VM サイズを選択します。

#### <a name="resize-the-current-virtual-machine"></a>現在の仮想マシンをサイズ変更する

[仮想マシンのサイズを変更](resize-vm.md)できます。 

## <a name="next-steps"></a>次のステップ

GPU 対応の仮想マシンのサイズの完全な一覧については、[GPU - 高速コンピューティングの概要](sizes-gpu.md)に関する記事を参照してください。