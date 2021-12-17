---
title: Azure Blob Storage での NFS 3.0 のパフォーマンスに関する考慮事項 | Microsoft Docs
description: この記事の推奨事項を使用して、ネットワーク ファイル システム (NFS) 3.0 のストレージ要求のパフォーマンスを最適化します。
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: normesta
ms.reviewer: yzheng
ms.openlocfilehash: 1268df1aaf095fd6a965b447d48a9ef4978325d7
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130046888"
---
# <a name="network-file-system-nfs-30-performance-considerations-in-azure-blob-storage"></a>Azure Blob Storage でのネットワーク ファイル システム (NFS) 3.0 のパフォーマンスに関する考慮事項

Blob Storage では、ネットワーク ファイル システム (NFS) 3.0 プロトコルがサポートされるようになりました。 この記事には、ストレージ要求のパフォーマンスを最適化するのに役立つ推奨事項が含まれています。 Azure Blob Storage での NFS 3.0 のサポートの詳細については、「[Azure Blob Storage でのネットワーク ファイル システム (NFS) 3.0 プロトコルのサポート](network-file-system-protocol-support.md)」を参照してください。

## <a name="add-clients-to-increase-throughput"></a>スループット向上のためにクライアントを追加する

Azure Blob Storage は、ストレージ アカウントのエグレスとイングレスの上限に達するまで、直線的にスケーリングします。 したがって、アプリケーションで使用するクライアントが多いほど、より高いスループットを実現できます。 ストレージ アカウントのエグレスとイングレスの制限については、「[Standard Storage アカウントのスケーラビリティとパフォーマンスのターゲット](../common/scalability-targets-standard-account.md)」を参照してください。

次のグラフは、クライアントを追加すると帯域幅がどのように増加するかを示したものです。 このグラフでは、クライアントは仮想マシン (VM) であり、Standard 汎用 v2 ストレージ アカウントを使用しています。

> [!div class="mx-imgBorder"]
> ![Standard パフォーマンス](./media/network-file-system-protocol-support-performance/standard-performance-tier.png)

次のグラフは、Premium ブロック BLOB ストレージ アカウントに同じ効果を適用した場合を示しています。

> [!div class="mx-imgBorder"]
> ![Premium パフォーマンス](./media/network-file-system-protocol-support-performance/premium-performance-tier.png)

## <a name="use-premium-block-blob-storage-accounts-for-small-scale-applications"></a>小規模なアプリケーションに Premium ブロック BLOB ストレージ アカウントを使用する

クライアントを追加することにより、すべてのアプリケーションをスケールアップできるわけではありません。 そのようなアプリケーションの場合は、[Azure Premium ブロック BLOB ストレージ アカウント](../common/storage-account-create.md)を使用することで、低遅延と高トランザクション レートが一貫して提供されます。 Premium ブロック BLOB ストレージ アカウントは、より少ないスレッドとクライアントで最大帯域幅に到達できます。 たとえば、クライアントが 1 つの場合、Standard パフォーマンスの汎用 v2 ストレージ アカウントで使用される同じセットアップと比較して、Premium ブロック BLOB ストレージ アカウントでは **2.3 倍** の帯域幅を実現できます。

次のグラフの各棒は、Premium と Standard のパフォーマンス ストレージ アカウントで実現される帯域幅の違いを示しています。 クライアントの数が増えるにつれて、その違いは小さくなります。

> [!div class="mx-imgBorder"]
> ![相対パフォーマンス](./media/network-file-system-protocol-support-performance/relative-performance.png)

## <a name="improve-read-ahead-size-to-increase-large-file-read-throughput"></a>先行読み取りサイズを改善して、大きなファイル読み取りスループットを向上させる

read_ahead_kb カーネル パラメーターは、特定の読み取り要求を処理した後に読み取る必要がある追加データの量を表します。 このパラメーターを 16 MB に増やして、大きなファイル読み取りスループットを向上させることができます。

```
export AZMNT=/your/container/mountpoint

echo 15728640 > /sys/class/bdi/0:$(stat -c "%d" $AZMNT)/read_ahead_kb
```

## <a name="avoid-frequent-overwrites-on-data"></a>データが頻繁に上書きされないようにする

新規書き込み操作より、上書き操作の方が完了するまでに時間がかかります。 これは、NFS の上書き操作 (特に、部分的なインプレース ファイル編集) が、読み取り、変更、書き込み操作といういくつかの基になる BLOB 操作を組み合わせたものであるためです。 そのため、頻繁にインプレース編集が必要なアプリケーションは、NFS が有効になっている BLOB ストレージ アカウントには適していません。

## <a name="deploy-azure-hpc-cache-for-latency-sensitive-applications"></a>待機時間に厳密なアプリケーションのための Azure HPC Cache をデプロイする

アプリケーションによっては、高スループットに加えて待機時間の短縮が必要になることがあります。 [Azure HPC Cache](../../hpc-cache/nfs-blob-considerations.md) をデプロイすると、待機時間を大幅に向上させることができます。 [Blob Storage での待ち時間](storage-blobs-latency.md) の詳細を確認してください。

## <a name="other-best-practice-recommendations"></a>その他のベスト プラクティスの推奨事項

- 十分なネットワーク帯域幅を備えた VM を使用します。

- ワークロードで許容される場合は、複数のマウント ポイントを使用します。

- できるだけ多くのスレッドを使用します。

- 大きなブロック サイズを使用します。

- ストレージ アカウントと同じリージョンにあるクライアントからストレージ要求を行います。 これにより、ネットワークの待機時間が短縮されます。

## <a name="next-steps"></a>次のステップ

- Azure Blob Storage での NFS 3.0 のサポートの詳細については、「[Azure Blob Storage でのネットワーク ファイル システム (NFS) 3.0 プロトコルのサポート](network-file-system-protocol-support.md)」を参照してください。

- 開始するには、「[ネットワーク ファイル システム (NFS) 3.0 プロトコルを使用して Blob Storage をマウントする](network-file-system-protocol-support-how-to.md)」を参照してください。
