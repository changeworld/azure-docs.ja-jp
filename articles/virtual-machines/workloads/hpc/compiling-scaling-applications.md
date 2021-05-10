---
title: HPC アプリケーションのスケール - Azure Virtual Machines | Microsoft Docs
description: Azure VM 上で HPC アプリケーションをスケールする方法について説明します。
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/25/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 4ab2c599bea4b2e3e682755a80a2ee348e4de7ef
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606778"
---
# <a name="scaling-hpc-applications"></a>HPC アプリケーションのスケール

Azure 上の HPC アプリケーションの最適なスケールアップとスケールアウトのパフォーマンスには、特定のワークロードに対するパフォーマンスの調整と最適化の実験が必要です。 このセクションと VM シリーズ固有のページでは、アプリケーションをスケールするための一般的なガイダンスについて説明します。

## <a name="application-setup"></a>アプリケーションのセットアップ
[azurehpc リポジトリ](https://github.com/Azure/azurehpc)には、次のような多くの例が含まれています。
- [アプリケーション](https://github.com/Azure/azurehpc/tree/master/apps)の最適な設定と実行。
- [ファイル システムとクラスター](https://github.com/Azure/azurehpc/tree/master/examples)の構成。
- いくつかの一般的なアプリケーション ワークフローを使用して簡単に開始する方法に関する[チュートリアル](https://github.com/Azure/azurehpc/tree/master/tutorials)。

## <a name="optimally-scaling-mpi"></a>MPI を最適にスケールする 

次の提案は、最適なアプリケーションのスケーリング効率、パフォーマンス、および整合性に適用されます。

- 小さいスケールのジョブ (つまり 256K 接続未満) の場合は、次のオプションを使用します。
   ```bash
   UCX_TLS=rc,sm
   ```

- 大規模なスケールのジョブ (つまり 256K 接続以上) の場合は、次のオプションを使用します。
   ```bash
   UCX_TLS=dc,sm
   ```

- 上記で MPI ジョブの接続数を計算するには、以下を使用します。
   ```bash
   Max Connections = (processes per node) x (number of nodes per job) x (number of nodes per job) 
   ```

## <a name="adaptive-routing"></a>アダプティブ ルーティング
アダプティブ ルーティング (AR) を使用すると、EDR と HDR InfiniBand を実行している Azure Virtual Machines (VM) で、より最適なネットワーク パスを動的に選択することで、ネットワークの輻輳を自動的に検出して回避することができます。 その結果、AR によって InfiniBand ネットワーク上の待機時間と帯域幅が改善され、パフォーマンスとスケーリングの効率が向上します。 詳細については、[TechCommunity に関する記事](https://techcommunity.microsoft.com/t5/azure-compute/adaptive-routing-on-azure-hpc/ba-p/1205217)を参照してください。

## <a name="process-pinning"></a>プロセスの固定

- (自動バランス アプローチとは対照的に) シーケンシャルなピン留めアプローチを使用して、コアにプロセスをピン留めします。 
- Numa/Core/HwThread によるバインディングは、既定のバインディングよりも優れています。
- ハイブリッドの並列アプリケーション (OpenMP + MPI) の場合は、HB および HBv2 の VM サイズで、CCX あたり 4 スレッドと 1 つの MPI ランクを使用します。
- 純粋な MPI アプリケーションの場合は、HB および HBv2 の VM サイズで最適なパフォーマンスを得るために、CCX ごとに 1 ～ 4 の MPI ランクで実験します。
- メモリ帯域幅の影響を非常に受けやすいアプリケーションでは、CCX あたりのコア数を減らしてメリットが得られる場合があります。 これらのアプリケーションでは、CCX あたり 3 コアまたは 2 コアを使用してメモリ帯域幅の競合を減らし、実際の環境でより高いパフォーマンスや、より一貫したスケーラビリティを実現できます。 特に、MPI Allreduce はこのアプローチからメリットが得られる可能性があります。
- 非常に大規模な実行の場合、UD またはハイブリッド RC + UD トランスポートを使用することをお勧めします。 多くの MPI ライブラリ/ランタイム ライブラリでは、これが内部的に実行されます (UCX や MVAPICH2 など)。 大規模な実行の場合は、トランスポート構成を確認します。

## <a name="compiling-applications"></a>アプリケーションのコンパイル
<br>
<details>
<summary>展開するにはクリック</summary>

必須ではありませんが、適切な最適化フラグを使用してアプリケーションをコンパイルすると、HB および HC シリーズの VM で最高のスケールアップ パフォーマンスを達成できます。

### <a name="amd-optimizing-cc-compiler"></a>AMD Optimizing C/C++ Compiler

AMD Optimizing C/C++ Compiler (AOCC) コンパイラ システムは、グローバルな最適化、ベクトル化、プロシージャ間の分析、ループ変換、コード生成を含む高度な最適化、マルチスレッド、およびプロセッサのサポートを提供しています。 AOCC コンパイラ バイナリは、GNU C ライブラリ (glibc) バージョン 2.17 以降がインストールされた Linux システムに適しています。 コンパイラ スイートは、C/C++ コンパイラ (clang)、Fortran コンパイラ (FLANG)、および Clang への Fortran フロント エンド (Dragon Egg) で構成されています。

### <a name="clang"></a>Clang

Clang は、前処理、解析、最適化、コード生成、アセンブリ、およびリンクを処理する C、C++、および Objective-C のコンパイラです。 Clang は、AMD の Zen ベースの x86 アーキテクチャに最適なコード生成と調整を行うことができる `-march=znver1` フラグをサポートしています。

### <a name="flang"></a>FLANG

FLANG コンパイラは、AOCC スイートに最近追加された (2018 年 4 月に追加された) ものであり、現在は開発者がダウンロードしてテストするためのプレリリース段階です。 AMD では Fortran 2008 に基づいて GitHub バージョンの FLANG (https://github.com/flang-compiler/flang)) が拡張されます。 FLANG コンパイラは、すべての Clang コンパイラ オプションとその他の FLANG 固有のコンパイラ オプションをサポートしています。

### <a name="dragonegg"></a>DragonEgg

DragonEgg は GCC のオプティマイザーとコード ジェネレーターを LLVM プロジェクトのものと置き換える gcc プラグインです。 AOCC に付属する DragonEgg は gcc-4.8.x で動作し、x86-32 および x86-64 ターゲット向けにテストされており、さまざまな Linux プラットフォームで正常に使用されています。

GFortran は、GCC GIMPLE 中間表現 (IR) を生成する前処理、解析、およびセマンティック分析を担当する Fortran プログラムの実際のフロントエンドです。 DragonEgg は、GFortran コンパイル フローにプラグインする GNU プラグインです。 これには GNU プラグイン API が実装されています。 このプラグイン アーキテクチャでは、DragonEgg がコンパイラ ドライバーになり、コンパイルのさまざまな段階が推進されます。  ダウンロードとインストールの手順を実行した後は、次を使用して Dragon Egg を呼び出すことができます。 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>PGI コンパイラ
PGI Community Edition バージョン 17 は AMD EPYC で動作することが確認されています。 PGI コンパイル バージョンの STREAM は、プラットフォームの全メモリ帯域幅を提供します。 新しい Community Edition 18.10 (2018 年 11 月) も同様に機能するはずです。 以下は Intel コンパイラで最適にコンパイルすることができるサンプル CLI です。

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Intel コンパイラ
Intel コンパイラ バージョン 18 は AMD EPYC で動作することが確認されています。 以下は Intel コンパイラで最適にコンパイルすることができるサンプル CLI です。

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>GCC コンパイラ 
HPC の場合、AMD は GCC コンパイラ 7.3 以降を推奨します。 RHEL/CentOS 7.4 に含まれる 4.8.5 などの古いバージョンは推奨されません。 GCC 7.3 以降では、HPL、HPCG、および DGEMM テストで非常に高いパフォーマンスを達成できます。

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```
</details>

## <a name="next-steps"></a>次のステップ

Azure での [HPC](/azure/architecture/topics/high-performance-computing/) の詳細を確認する。
